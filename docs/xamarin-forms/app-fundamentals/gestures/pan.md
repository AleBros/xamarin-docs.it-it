---
title: Aggiunta di un sistema di riconoscimento del movimento di panoramica
description: Questo articolo illustra come usare un movimento di panoramica per eseguire una panoramica in orizzontale e in verticale di un'immagine, in modo da poter vedere tutto il contenuto dell'immagine quando è visualizzata in un viewport più piccolo rispetto alle dimensioni dell'immagine.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 03b9c5f903cea67032a9560754a32ac552635e92
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653868"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento del movimento di panoramica

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_Il movimento di panoramica viene usato per rilevare il movimento delle dita sullo schermo e applicare tale movimento al contenuto e viene implementato con la classe `PanGestureRecognizer`. Uno scenario comune per questo movimento è l'esecuzione di una panoramica in orizzontale e in verticale di un'immagine, in modo da poter vedere tutto il contenuto dell'immagine quando è visualizzata in un viewport più piccolo rispetto alle dimensioni dell'immagine. Questa operazione viene eseguita spostando l'immagine all'interno del viewport ed è illustrata in questo articolo._

Per supportare lo spostamento di un elemento dell'interfaccia utente tramite il movimento di panoramica, creare un'istanza di [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer), gestire l'evento [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) e aggiungere il nuovo sistema di riconoscimento dei movimenti alla raccolta [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) nell'elemento dell'interfaccia utente. L'esempio di codice seguente visualizza un `PanGestureRecognizer` associato a un elemento [`Image`](xref:Xamarin.Forms.Image):

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

Questo risultato può essere ottenuto anche in XAML, come illustrato nell'esempio di codice seguente:

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

Il codice del gestore eventi `OnPanUpdated` viene quindi aggiunto al file code-behind:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

> [!NOTE]
> Per eseguire correttamente la panoramica in Android è richiesto almeno il [pacchetto NuGet di Xamarin.Forms 2.1.0-pre1](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1).

## <a name="creating-a-pan-container"></a>Creazione di un contenitore di panoramica

Questa sezione contiene una classe helper generalizzata che consente di eseguire la panoramica a mano libera, adatta in genere agli spostamenti all'interno di immagini o mappe. La gestione del movimento di panoramica per eseguire questa operazione richiede alcuni calcoli matematici per la trasformazione dell'interfaccia utente. Questi calcoli matematici vengono usati per eseguire la panoramica solo entro i limiti dell'elemento di interfaccia utente sottoposto a wrapping. L'esempio di codice seguente visualizza la classe `PanContainer`:

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

È possibile eseguire il wrapping di questa classe per un elemento dell'interfaccia utente, in modo che il movimento esegua la panoramica dell'elemento dell'interfaccia utente sottoposto a wrapping. L'esempio di codice XAML seguente mostra il wrapping `PanContainer` di un elemento [`Image`](xref:Xamarin.Forms.Image):

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

L'esempio di codice seguente illustra come `PanContainer` esegue il wrapping di un elemento [`Image`](xref:Xamarin.Forms.Image) in una pagina C#:

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

In entrambi gli esempi, le proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) vengono impostate sui valori di larghezza e altezza dell'immagine visualizzata.

Quando l'elemento [`Image`](xref:Xamarin.Forms.Image) riceve un movimento di panoramica, verrà eseguita la panoramica dell'immagine visualizzata. La panoramica viene eseguita dal metodo `PanContainer.OnPanUpdated`, visualizzato nell'esempio di codice seguente:

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

Questo metodo aggiorna il contenuto visualizzabile dell'elemento dell'interfaccia utente sottoposto a wrapping in base al movimento di panoramica dell'utente. Questo avviene usando i valori delle proprietà [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) e [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) dell'istanza di [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) per calcolare la direzione e la distanza della panoramica. Le proprietà `App.ScreenWidth` e `App.ScreenHeight` specificano l'altezza e la larghezza del viewport e vengono impostate sui valori di larghezza e altezza dello schermo del dispositivo dai rispettivi progetti specifici della piattaforma. Viene quindi eseguita la panoramica dell'elemento dell'interfaccia utente sottoposto a wrapping impostandone le proprietà [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) sui valori calcolati.

Per la panoramica del contenuto in un elemento che non occupa l'intero schermo, l'altezza e la larghezza del viewport possono essere ottenuti dalle proprietà [`Height`](xref:Xamarin.Forms.VisualElement.Height) e [`Width`](xref:Xamarin.Forms.VisualElement.Width) dell'elemento.

> [!NOTE]
> La visualizzazione di immagini ad alta risoluzione può aumentare notevolmente il footprint della memoria dell'app. Di conseguenza, tali risorse devono essere create solo se necessario e rilasciate non appena l'app non le richiede più. Per altre informazioni, vedere [Optimize Image Resources](~/xamarin-forms/deploy-test/performance.md#optimizeimages) (Ottimizzare le risorse immagine).

## <a name="related-links"></a>Collegamenti correlati

- [PanGesture (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture) (Esempio di PanGesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
