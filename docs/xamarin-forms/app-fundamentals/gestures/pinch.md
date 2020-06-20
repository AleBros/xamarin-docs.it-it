---
title: Aggiunta di un sistema di riconoscimento del movimento di avvicinamento delle dita
description: Questo articolo illustra come usare il movimento di avvicinamento delle dita per eseguire lo zoom interattivo di un'immagine in corrispondenza della posizione di avvicinamento delle dita.
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: da4a8bc66a7986efd3683de6dce1f6af618b85cc
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137852"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento del movimento di avvicinamento delle dita

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)

_Il gesto del pizzico viene usato per eseguire lo zoom interattivo e viene implementato con la classe PinchGestureRecognizer. Uno scenario comune per il gesto del pizzico consiste nell'eseguire lo zoom interattivo di un'immagine nella posizione del pizzico. Questa operazione viene eseguita scalando il contenuto del viewport ed è illustrato in questo articolo._

Per rendere ingrandibile un elemento dell'interfaccia utente con il gesto del pizzico, creare un' [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) istanza, gestire l' [`PinchUpdated`](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) evento e aggiungere il nuovo riconoscimento di movimento alla [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) raccolta nell'elemento dell'interfaccia utente. Nell'esempio di codice riportato di seguito viene illustrato un oggetto `PinchGestureRecognizer` associato a un [`Image`](xref:Xamarin.Forms.Image) elemento:

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

Questo risultato può essere ottenuto anche in XAML, come illustrato nell'esempio di codice seguente:

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

Il codice del gestore eventi `OnPinchUpdated` viene quindi aggiunto al file code-behind:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>Creazione di un contenitore PinchToZoom

La gestione del movimento di avvicinamento delle dita per eseguire un'operazione di zoom richiede alcuni calcoli matematici per la trasformazione dell'interfaccia utente. Questa sezione contiene una classe helper generica per l'esecuzione dei calcoli, che può essere usata per lo zoom avanti interattivo su qualsiasi elemento dell'interfaccia utente. L'esempio di codice seguente visualizza la classe `PinchToZoomContainer`:

```csharp
public class PinchToZoomContainer : ContentView
{
  ...

  public PinchToZoomContainer ()
  {
    var pinchGesture = new PinchGestureRecognizer ();
    pinchGesture.PinchUpdated += OnPinchUpdated;
    GestureRecognizers.Add (pinchGesture);
  }

  void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
  {
    ...
  }
}
```

È possibile eseguire il wrapping di questa classe intorno a un elemento dell'interfaccia utente, in modo che il movimento di avvicinamento delle dita esegua lo zoom sull'elemento dell'interfaccia utente sottoposto a wrapping. Nell'esempio di codice XAML seguente viene illustrato il `PinchToZoomContainer` wrapping di un [`Image`](xref:Xamarin.Forms.Image) elemento:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PinchGesture;assembly=PinchGesture"
             x:Class="PinchGesture.HomePage">
    <ContentPage.Content>
        <Grid Padding="20">
            <local:PinchToZoomContainer>
                <local:PinchToZoomContainer.Content>
                    <Image Source="waterfront.jpg" />
                </local:PinchToZoomContainer.Content>
            </local:PinchToZoomContainer>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

Nell'esempio di codice seguente viene illustrato come l'oggetto `PinchToZoomContainer` esegue il wrapping [`Image`](xref:Xamarin.Forms.Image) di un elemento in una pagina C#:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new Grid {
      Padding = new Thickness (20),
      Children = {
        new PinchToZoomContainer {
          Content = new Image { Source = ImageSource.FromFile ("waterfront.jpg") }
        }
      }
    };
  }
}
```

Quando l' [`Image`](xref:Xamarin.Forms.Image) elemento riceve un movimento di tocco, l'immagine visualizzata verrà ingrandita o disattivata. Lo zoom viene eseguito dal `PinchZoomContainer.OnPinchUpdated` metodo, illustrato nell'esempio di codice seguente:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  if (e.Status == GestureStatus.Started) {
    // Store the current scale factor applied to the wrapped user interface element,
    // and zero the components for the center point of the translate transform.
    startScale = Content.Scale;
    Content.AnchorX = 0;
    Content.AnchorY = 0;
  }
  if (e.Status == GestureStatus.Running) {
    // Calculate the scale factor to be applied.
    currentScale += (e.Scale - 1) * startScale;
    currentScale = Math.Max (1, currentScale);

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the X pixel coordinate.
    double renderedX = Content.X + xOffset;
    double deltaX = renderedX / Width;
    double deltaWidth = Width / (Content.Width * startScale);
    double originX = (e.ScaleOrigin.X - deltaX) * deltaWidth;

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the Y pixel coordinate.
    double renderedY = Content.Y + yOffset;
    double deltaY = renderedY / Height;
    double deltaHeight = Height / (Content.Height * startScale);
    double originY = (e.ScaleOrigin.Y - deltaY) * deltaHeight;

    // Calculate the transformed element pixel coordinates.
    double targetX = xOffset - (originX * Content.Width) * (currentScale - startScale);
    double targetY = yOffset - (originY * Content.Height) * (currentScale - startScale);

    // Apply translation based on the change in origin.
    Content.TranslationX = targetX.Clamp (-Content.Width * (currentScale - 1), 0);
    Content.TranslationY = targetY.Clamp (-Content.Height * (currentScale - 1), 0);

    // Apply scale factor.
    Content.Scale = currentScale;
  }
  if (e.Status == GestureStatus.Completed) {
    // Store the translation delta's of the wrapped user interface element.
    xOffset = Content.TranslationX;
    yOffset = Content.TranslationY;
  }
}
```

Questo metodo aggiorna il livello di zoom dell'elemento dell'interfaccia utente sottoposto a wrapping in base al movimento di avvicinamento delle dita dell'utente. Questa operazione viene eseguita utilizzando i valori delle [`Scale`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale) [`ScaleOrigin`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) [`Status`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) proprietà e dell' [`PinchGestureUpdatedEventArgs`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) istanza di per calcolare il fattore di scala da applicare all'origine del movimento del pizzico. L'elemento utente di cui è stato eseguito il wrapped viene quindi ingrandito in corrispondenza dell'origine del movimento del pizzico impostando le relative [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) proprietà, e sui [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) valori calcolati.

## <a name="related-links"></a>Collegamenti correlati

- [PinchGesture (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
