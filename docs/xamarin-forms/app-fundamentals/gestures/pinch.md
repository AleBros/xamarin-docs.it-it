---
title: Aggiunta di un sistema di riconoscimento di movimento zoom indietro
description: Questo articolo illustra come usare il movimento zoom indietro per eseguire lo zoom interattivo di un'immagine in corrispondenza della posizione zoom indietro.
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: f67cbb136c42a4bc476c1715ea6fd15255d71dc7
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "38998702"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento di movimento zoom indietro

_Il movimento zoom indietro viene utilizzato per l'esecuzione interattivo zoom e viene implementato con la classe PinchGestureRecognizer. Uno scenario comune per il movimento zoom indietro consiste nell'eseguire lo zoom interattivo di un'immagine in corrispondenza della posizione zoom indietro. Questa operazione viene eseguita scalando il contenuto del riquadro di visualizzazione e viene illustrata in questo articolo._

Per rendere un elemento dell'interfaccia utente Ingrandibile con il movimento zoom indietro, creare un [ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer) dell'istanza, gestire la [ `PinchUpdated` ](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) evento, e aggiungere il nuovo riconoscitore di movimento per il [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) raccolta nell'elemento dell'interfaccia utente. Nell'esempio di codice riportato di seguito viene illustrato un `PinchGestureRecognizer` collegato a un [ `Image` ](xref:Xamarin.Forms.Image) elemento:

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

Ciò può anche essere ottenuto in XAML, come illustrato nell'esempio di codice seguente:

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

Il codice per il `OnPinchUpdated` gestore eventi viene quindi aggiunto al file code-behind:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>Creazione di un contenitore PinchToZoom

La gestione del movimento zoom indietro per eseguire un'operazione di zoom richiede alcuni calcoli matematici per trasformare l'interfaccia utente. In questa sezione contiene una classe di supporto generalizzato per eseguire le operazioni matematiche, che possono essere utilizzata per qualsiasi elemento dell'interfaccia utente di zoom in modo interattivo. L'esempio di codice seguente visualizza la classe `PinchToZoomContainer`:

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

Questa classe può essere eseguito il wrapping intorno a un elemento dell'interfaccia utente in modo che il movimento zoom indietro eseguirà lo zoom l'elemento dell'interfaccia utente sottoposta a wrapping. L'esempio di codice XAML seguente mostra le `PinchToZoomContainer` wrapping di un' [ `Image` ](xref:Xamarin.Forms.Image) elemento:

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

Nell'esempio di codice riportato di seguito viene illustrato come la `PinchToZoomContainer` esegue il wrapping di un' [ `Image` ](xref:Xamarin.Forms.Image) elemento in una pagina in c#:

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

Quando la [ `Image` ](xref:Xamarin.Forms.Image) elemento riceve un movimento zoom indietro, l'immagine visualizzata sarà essere ingrandito-in o out. Il valore di zoom viene eseguita mediante il `PinchZoomContainer.OnPinchUpdated` metodo, che viene visualizzato nell'esempio di codice seguente:

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

Questo metodo aggiorna il livello di zoom dell'elemento di interfaccia utente sottoposta a wrapping basata movimento zoom indietro dell'utente. Questo avviene utilizzando i valori del [ `Scale` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale), [ `ScaleOrigin` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) e [ `Status` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) proprietà del [ `PinchGestureUpdatedEventArgs` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) istanza per cui calcolare il fattore di scala da applicare all'origine del movimento zoom indietro. L'elemento dell'utente con wrapping viene quindi applicato lo zoom in corrispondenza dell'origine del movimento zoom indietro impostando relativi [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX), [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY), e [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) proprietà per i valori calcolati.

## <a name="related-links"></a>Collegamenti correlati

- [PinchGesture (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PinchGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
