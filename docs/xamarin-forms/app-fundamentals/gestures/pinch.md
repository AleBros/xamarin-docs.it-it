---
title: Aggiunta di un riconoscimento di movimento zoom indietro
description: Il movimento zoom indietro viene utilizzato per eseguire lo zoom interattivo e viene implementato con la classe PinchGestureRecognizer. Uno scenario comune per i movimenti zoom indietro consiste nell'eseguire lo zoom interattivo di un'immagine in corrispondenza della posizione di zoom indietro. Questa operazione viene eseguita modificando il contenuto del riquadro di visualizzazione e viene illustrata in questo articolo.
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: b2348a1f0dfacc4a7a0e37f5c9041a07217ff802
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846113"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>Aggiunta di un riconoscimento di movimento zoom indietro

_Il movimento zoom indietro viene utilizzato per eseguire lo zoom interattivo e viene implementato con la classe PinchGestureRecognizer. Uno scenario comune per i movimenti zoom indietro consiste nell'eseguire lo zoom interattivo di un'immagine in corrispondenza della posizione di zoom indietro. Questa operazione viene eseguita modificando il contenuto del riquadro di visualizzazione e viene illustrata in questo articolo._

## <a name="overview"></a>Panoramica

Per rendere un elemento dell'interfaccia utente sul quale con del movimento zoom indietro, creare un [ `PinchGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/) dell'istanza, gestire il [ `PinchUpdated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.PinchGestureRecognizer.PinchUpdated/) evento, e aggiungere il nuovo riconoscitore di movimento per il [ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/) raccolta nell'elemento dell'interfaccia utente. Nell'esempio di codice riportato di seguito viene illustrato un `PinchGestureRecognizer` collegato a un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

Questo può anche essere ottenuto in XAML, come illustrato nell'esempio di codice seguente:

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

La gestione del movimento zoom indietro per eseguire un'operazione di zoom richiede alcuni calcoli matematici per trasformare l'interfaccia utente. In questa sezione contiene una classe helper generalizzato per eseguire le operazioni matematiche, che può essere usato per eseguire lo zoom in modo interattivo qualsiasi elemento dell'interfaccia utente. L'esempio di codice seguente visualizza la classe `PinchToZoomContainer`:

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

Questa classe può essere eseguito il wrapping intorno a un elemento dell'interfaccia utente in modo che l'elemento dell'interfaccia utente sottoposta a wrapping comporterà l'ingrandimento del movimento zoom indietro. Nell'esempio di codice XAML seguente viene il `PinchToZoomContainer` il wrapping di un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

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

Nell'esempio di codice riportato di seguito viene illustrato come la `PinchToZoomContainer` esegue il wrapping di un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento in una pagina in c#:

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

Quando il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento riceve un movimento zoom indietro, l'immagine visualizzata sarà essere ingrandita-in o out. Il valore di zoom viene eseguita mediante il `PinchZoomContainer.OnPinchUpdated` (metodo), come illustrato nell'esempio di codice seguente:

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

Questo metodo aggiorna il livello di zoom di wrapping elemento dell'interfaccia utente in movimento zoom indietro dell'utente. Questo risultato viene ottenuto utilizzando i valori del [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale/), [ `ScaleOrigin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin/) e [ `Status` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.Status/) le proprietà del [ `PinchGestureUpdatedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureUpdatedEventArgs/) istanza per calcolare il fattore di scala da applicare all'origine del movimento zoom indietro. L'elemento sottoposto a wrapping utente viene quindi applicato lo zoom in corrispondenza dell'origine del movimento zoom indietro impostando il relativo [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/), [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/), e [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) proprietà per i valori calcolati.

## <a name="summary"></a>Riepilogo

Il movimento zoom indietro viene utilizzato per eseguire lo zoom interattivo e viene implementato con la [ `PinchGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/) classe.


## <a name="related-links"></a>Collegamenti correlati

- [PinchGesture (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PinchGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [PinchGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/)
