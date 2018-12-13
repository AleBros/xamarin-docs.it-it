---
title: Aggiunta di un sistema di riconoscimento del movimento di avvicinamento delle dita
description: Questo articolo illustra come usare il movimento di avvicinamento delle dita per eseguire lo zoom interattivo di un'immagine in corrispondenza della posizione di avvicinamento delle dita.
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: f67cbb136c42a4bc476c1715ea6fd15255d71dc7
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "38998702"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento del movimento di avvicinamento delle dita

_Il movimento di avvicinamento delle dita viene usato per lo zoom interattivo e viene implementato con la classe PinchGestureRecognizer. Uno scenario comune per il movimento di avvicinamento delle dita è l'esecuzione dello zoom interattivo di un'immagine in corrispondenza della posizione di avvicinamento delle dita. Questa operazione viene eseguita modificando in scala il contenuto del riquadro di visualizzazione ed è illustrata in questo articolo._

Per abilitare per lo zoom di un elemento dell'interfaccia utente con il movimento di avvicinamento delle dita, creare un'istanza [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer), gestire l'evento [`PinchUpdated`](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) e aggiungere il nuovo riconoscitore di movimento alla raccolta [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) nell'elemento dell'interfaccia utente. L'esempio di codice seguente visualizza un `PinchGestureRecognizer` associato a un elemento [`Image`](xref:Xamarin.Forms.Image):

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

È possibile eseguire il wrapping di questa classe intorno a un elemento dell'interfaccia utente, in modo che il movimento di avvicinamento delle dita esegua lo zoom sull'elemento dell'interfaccia utente sottoposto a wrapping. L'esempio di codice XAML seguente visualizza il wrapping `PinchToZoomContainer` di un elemento [`Image`](xref:Xamarin.Forms.Image):

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

L'esempio di codice seguente illustra come `PinchToZoomContainer` esegue il wrapping di un elemento [`Image`](xref:Xamarin.Forms.Image) in una pagina C#:

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

Quando l'elemento [`Image`](xref:Xamarin.Forms.Image) riceve un movimento di avvicinamento delle dita, viene eseguito lo zoom avanti o indietro sull'immagine visualizzata. Lo zoom viene eseguito mediante il metodo `PinchZoomContainer.OnPinchUpdated`, visualizzato nell'esempio di codice seguente:

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

Questo metodo aggiorna il livello di zoom dell'elemento dell'interfaccia utente sottoposto a wrapping in base al movimento di avvicinamento delle dita dell'utente. Questo risultato viene ottenuto usando i valori delle proprietà [`Scale`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale), [`ScaleOrigin`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) e [`Status`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) dell'istanza [`PinchGestureUpdatedEventArgs`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) per calcolare il fattore di scala da applicare all'origine del movimento di avvicinamento delle dita. L'elemento utente con wrapping viene quindi sottoposto a zoom in corrispondenza dell'origine del movimento di avvicinamento delle dita mediante l'impostazione delle proprietà [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX), [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) e [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) dell'elemento sui valori calcolati.

## <a name="related-links"></a>Collegamenti correlati

- [PinchGesture (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PinchGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
