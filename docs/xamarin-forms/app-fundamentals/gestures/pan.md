---
title: Aggiunta di un riconoscimento di movimento zoom
description: "Il movimento zoom viene utilizzato per il rilevamento di trascinamento e viene implementato con la classe PanGestureRecognizer. Uno scenario comune per i movimenti zoom è orizzontalmente e verticalmente, trascinare un'immagine, in modo che tutto il contenuto dell'immagine possono essere visualizzati quando viene visualizzato in un viewport inferiore alle dimensioni immagine. Questo avviene spostando l'immagine all'interno del viewport e viene illustrato in questo articolo."
ms.topic: article
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 4da42a92c83dcc1ec0b0ba2528de672e3fcf3be3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="adding-a-pan-gesture-recognizer"></a>Aggiunta di un riconoscimento di movimento zoom

_Il movimento zoom viene utilizzato per il rilevamento di trascinamento e viene implementato con la classe PanGestureRecognizer. Uno scenario comune per i movimenti zoom è orizzontalmente e verticalmente, trascinare un'immagine, in modo che tutto il contenuto dell'immagine possono essere visualizzati quando viene visualizzato in un viewport inferiore alle dimensioni immagine. Questo avviene spostando l'immagine all'interno del viewport e viene illustrato in questo articolo._

## <a name="overview"></a>Panoramica

Per rendere un elemento dell'interfaccia utente draggable con la combinazione di dettaglio, creare un [ `PanGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/) dell'istanza, gestire il [ `PanUpdated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.PanGestureRecognizer.PanUpdated/) evento, e aggiungere il nuovo riconoscitore di movimento per il [ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/) raccolta nell'elemento dell'interfaccia utente. Nell'esempio di codice riportato di seguito viene illustrato un `PanGestureRecognizer` collegato a un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

Questo può anche essere ottenuto in XAML, come illustrato nell'esempio di codice seguente:

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

Il codice per il `OnPanUpdated` gestore eventi viene quindi aggiunto al file code-behind:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

> [!NOTE]
> Panoramica corretto in Android richiede il [pacchetto NuGet di xamarin. Forms 2.1.0-pre1](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1) minimo.

## <a name="creating-a-pan-container"></a>Creazione di un contenitore di dettaglio

In questa sezione contiene una classe helper generalizzato che esegue la panoramica a mano libera, in genere adatto per spostarsi all'interno di immagini o mappe. Gestisce il movimento zoom per eseguire un'operazione di trascinamento richiede alcuni calcoli matematici per trasformare l'interfaccia utente. Questo matematiche è possibile trascinare solo entro i limiti dell'elemento dell'interfaccia utente sottoposta a wrapping. L'esempio di codice seguente visualizza la classe `PanContainer`:

```csharp
public class PanContainer : ContentView
{
  double x, y;

  public PanContainer ()
  {
    // Set PanGestureRecognizer.TouchPoints to control the
    // number of touch points needed to pan
    var panGesture = new PanGestureRecognizer ();
    panGesture.PanUpdated += OnPanUpdated;
    GestureRecognizers.Add (panGesture);
  }

  void OnPanUpdated (object sender, PanUpdatedEventArgs e)
  {
    ...
  }
}
```

Questa classe può essere eseguito il wrapping intorno a un elemento dell'interfaccia utente in modo che il movimento zoom verrà trascinato l'elemento dell'interfaccia utente sottoposta a wrapping. Nell'esempio di codice XAML seguente viene il `PanContainer` il wrapping di un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PanGesture"
             x:Class="PanGesture.HomePage">
    <ContentPage.Content>
        <AbsoluteLayout>
            <local:PanContainer>
                <Image Source="MonoMonkey.jpg" WidthRequest="1024" HeightRequest="768" />
            </local:PanContainer>
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

Nell'esempio di codice riportato di seguito viene illustrato come la `PanContainer` esegue il wrapping di un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento in una pagina in c#:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new AbsoluteLayout {
      Padding = new Thickness (20),
      Children = {
        new PanContainer {
          Content = new Image {
            Source = ImageSource.FromFile ("MonoMonkey.jpg"),
            WidthRequest = 1024,
            HeightRequest = 768
          }
        }
      }
    };
  }
}
```

In entrambi gli esempi, il [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) e [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) proprietà vengono impostate sui valori di larghezza e altezza dell'immagine viene visualizzata.

Quando il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento riceve un movimento zoom, sarà possibile trascinare l'immagine visualizzata. L'operazione di trascinamento viene eseguita mediante il `PanContainer.OnPanUpdated` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  switch (e.StatusType) {
  case GestureStatus.Running:
    // Translate and ensure we don't pan beyond the wrapped user interface element bounds.
    Content.TranslationX =
      Math.Max (Math.Min (0, x + e.TotalX), -Math.Abs (Content.Width - App.ScreenWidth));
    Content.TranslationY =
      Math.Max (Math.Min (0, y + e.TotalY), -Math.Abs (Content.Height - App.ScreenHeight));
    break;

  case GestureStatus.Completed:
    // Store the translation applied during the pan
    x = Content.TranslationX;
    y = Content.TranslationY;
    break;
  }
}
```

Questo metodo aggiorna il contenuto visualizzabile di elemento dell'interfaccia utente sottoposta a wrapping, in base a movimento zoom dell'utente. Questo risultato viene ottenuto utilizzando i valori del [ `TotalX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PanUpdatedEventArgs.TotalX/) e [ `TotalY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PanUpdatedEventArgs.TotalY/) le proprietà del [ `PanUpdatedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanUpdatedEventArgs/) istanza per calcolare la direzione e distanza del dettaglio. Il `App.ScreenWidth` e `App.ScreenHeight` proprietà forniscono l'altezza e la larghezza del viewport e sono impostate per la larghezza dello schermo e i valori di altezza dello schermo del dispositivo tramite i rispettivi progetti specifici della piattaforma. L'elemento utente sottoposto a wrapping viene quindi trascinato impostando il relativo [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) e [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) proprietà per i valori calcolati.

Quando Panoramica contenuto in un elemento che non occupa l'intero schermo, l'altezza e la larghezza del riquadro di visualizzazione può essere ottenuti dell'elemento [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) e [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) proprietà.

> [!NOTE]
> Visualizzazione di immagini ad alta risoluzione può aumentare notevolmente il footprint di memoria di un'app. Pertanto, queste devono solo essere create quando necessario e devono essere rilasciate non appena non è più necessario l'app. Per altre informazioni, vedere [Optimize Image Resources](~/xamarin-forms/deploy-test/performance.md#optimizeimages) (Ottimizzare le risorse immagine).

## <a name="summary"></a>Riepilogo

Il movimento zoom viene utilizzato per il rilevamento di trascinamento e viene implementato con la [ `PanGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/) classe.



## <a name="related-links"></a>Collegamenti correlati

- [PanGesture (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [PanGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/)
