---
title: Aggiunta di un riconoscitore di movimento di zoom
description: Questo articolo illustra come usare un movimento di zoom da orizzontalmente e verticalmente trascinare un'immagine, in modo che tutto il contenuto di immagine possono essere visualizzati quando viene visualizzata in un viewport inferiore alle dimensioni immagine.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 45c0a1452916f193236e5ba741f8e8e19b6691aa
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996806"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Aggiunta di un riconoscitore di movimento di zoom

_Il movimento di zoom viene usato per il rilevamento di trascinamento e viene implementato con la classe PanGestureRecognizer. Uno scenario comune per il movimento di zoom è orizzontalmente e verticalmente trascinare un'immagine, in modo che tutto il contenuto di immagine possono essere visualizzati quando viene visualizzata in un viewport inferiore alle dimensioni immagine. Ciò avviene spostando l'immagine all'interno del viewport e viene illustrato in questo articolo._

## <a name="overview"></a>Panoramica

Per rendere un elemento dell'interfaccia utente trascinabile con il movimento di zoom, creare un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) dell'istanza, gestire la [ `PanUpdated` ](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) evento, e aggiungere il nuovo riconoscitore di movimento per il [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) raccolta nell'elemento dell'interfaccia utente. Nell'esempio di codice riportato di seguito viene illustrato un `PanGestureRecognizer` collegato a un [ `Image` ](xref:Xamarin.Forms.Image) elemento:

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

Ciò può anche essere ottenuto in XAML, come illustrato nell'esempio di codice seguente:

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
> La panoramica corretto in Android richiede la [pacchetto NuGet di xamarin. Forms 2.1.0-pre1](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1) minimo.

## <a name="creating-a-pan-container"></a>Creazione di un contenitore di zoom

In questa sezione contiene una classe helper generalizzato che consente di eseguire la panoramica a mano libera, in genere adatto per spostarsi all'interno di immagini o mappe. Gestisce il movimento di zoom per eseguire un'operazione di trascinamento richiede alcuni calcoli matematici per trasformare l'interfaccia utente. La matematica consente di trascinare solo entro i limiti dell'elemento di interfaccia utente sottoposta a wrapping. L'esempio di codice seguente visualizza la classe `PanContainer`:

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

Questa classe può essere eseguito il wrapping intorno a un elemento dell'interfaccia utente in modo che il movimento di zoom verrà trascinato l'elemento dell'interfaccia utente sottoposta a wrapping. L'esempio di codice XAML seguente mostra le `PanContainer` wrapping di un' [ `Image` ](xref:Xamarin.Forms.Image) elemento:

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

Nell'esempio di codice riportato di seguito viene illustrato come la `PanContainer` esegue il wrapping di un' [ `Image` ](xref:Xamarin.Forms.Image) elemento in una pagina in c#:

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

In entrambi gli esempi, il [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) proprietà vengono impostate sui valori di larghezza e altezza dell'immagine viene visualizzata.

Quando la [ `Image` ](xref:Xamarin.Forms.Image) elemento riceve un movimento di zoom, sarà possibile trascinare l'immagine visualizzata. L'operazione di trascinamento viene eseguita mediante il `PanContainer.OnPanUpdated` metodo, che viene visualizzato nell'esempio di codice seguente:

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

Questo metodo aggiorna il contenuto dell'elemento dell'interfaccia utente sottoposta a wrapping, il movimento di zoom dell'utente in base visibile. Questo avviene utilizzando i valori del [ `TotalX` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) e [ `TotalY` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) le proprietà del [ `PanUpdatedEventArgs` ](xref:Xamarin.Forms.PanUpdatedEventArgs) istanza per cui calcolare la direzione e distanza di zoom. Il `App.ScreenWidth` e `App.ScreenHeight` proprietà forniscono l'altezza e la larghezza del viewport e sono configurate per la larghezza dello schermo e i valori di altezza dello schermo del dispositivo per i rispettivi progetti specifici della piattaforma. L'elemento dell'utente con wrapping viene quindi trascinato impostando relativi [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) i valori calcolati delle proprietà.

La panoramica del contenuto in un elemento che non occupa l'intero schermo, l'altezza e la larghezza del riquadro di visualizzazione può essere ottenute dell'elemento [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) e [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) proprietà.

> [!NOTE]
> Visualizzazione di immagini ad alta risoluzione può aumentare notevolmente il footprint di memoria dell'app. Di conseguenza, essi devono essere creati solo quando necessario e devono essere rilasciate non appena l'app non richiede più. Per altre informazioni, vedere [Optimize Image Resources](~/xamarin-forms/deploy-test/performance.md#optimizeimages) (Ottimizzare le risorse immagine).

## <a name="summary"></a>Riepilogo

Il movimento di zoom viene utilizzato per il rilevamento di trascinamento e viene implementato con il [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) classe.



## <a name="related-links"></a>Collegamenti correlati

- [PanGesture (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
