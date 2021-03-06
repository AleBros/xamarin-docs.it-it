---
title: Aggiunta di un sistema di riconoscimento del movimento di panoramica
description: Questo articolo illustra come usare un movimento di panoramica per eseguire una panoramica in orizzontale e in verticale di un'immagine, in modo da poter vedere tutto il contenuto dell'immagine quando è visualizzata in un viewport più piccolo rispetto alle dimensioni dell'immagine.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 73e312a1af56091a7e579d3fcbcea810ee0efb1e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "68820975"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento del movimento di panoramica

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_Il gesto di panoramica viene utilizzato per rilevare il movimento delle dita `PanGestureRecognizer` intorno allo schermo e applicare tale movimento al contenuto e viene implementato con la classe . Uno scenario comune per il movimento di panoramica consiste nel eseguire la panoramica orizzontale e verticale di un'immagine, in modo che tutto il contenuto dell'immagine possa essere visualizzato quando viene visualizzato in una finestra più piccola delle dimensioni dell'immagine. Questa operazione viene eseguita spostando l'immagine all'interno del riquadro di visualizzazione e viene illustrata in questo articolo._

Per rendere un elemento dell'interfaccia utente spostabile con [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) il movimento di panoramica, [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) creare un'istanza, [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) gestire l'evento e aggiungere il nuovo riconoscitore di movimento alla raccolta nell'elemento dell'interfaccia utente. Nell'esempio di `PanGestureRecognizer` codice riportato [`Image`](xref:Xamarin.Forms.Image) di seguito viene illustrato un elemento associato a un elemento:The following code example shows a attached to an element:

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

È possibile eseguire il wrapping di questa classe per un elemento dell'interfaccia utente, in modo che il movimento esegua la panoramica dell'elemento dell'interfaccia utente sottoposto a wrapping. Nell'esempio di codice `PanContainer` XAML [`Image`](xref:Xamarin.Forms.Image) seguente viene illustrato il wrapping di un elemento:The following XAML code example shows the wrapping an element:

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

Nell'esempio di codice `PanContainer` seguente [`Image`](xref:Xamarin.Forms.Image) viene illustrato come eseguire il wrapping di un elemento in una pagina di C :The following code example shows how the wraps an element in a C ' page:

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

In entrambi gli [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) esempi, le proprietà e sono impostate sui valori di larghezza e altezza dell'immagine visualizzata.

Quando [`Image`](xref:Xamarin.Forms.Image) l'elemento riceve un gesto di panoramica, l'immagine visualizzata verrà sottoposta a panoramica. La panoramica viene eseguita dal metodo `PanContainer.OnPanUpdated`, visualizzato nell'esempio di codice seguente:

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

Questo metodo aggiorna il contenuto visualizzabile dell'elemento dell'interfaccia utente sottoposto a wrapping in base al movimento di panoramica dell'utente. Ciò si ottiene utilizzando [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) i [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) valori [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) delle proprietà e dell'istanza per calcolare la direzione e la distanza della pan. Le proprietà `App.ScreenWidth` e `App.ScreenHeight` specificano l'altezza e la larghezza del viewport e vengono impostate sui valori di larghezza e altezza dello schermo del dispositivo dai rispettivi progetti specifici della piattaforma. L'elemento utente di cui è [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) stato eseguito il wrapping viene quindi eseguito l'impostazione impostandone le proprietà e sui valori calcolati.

Quando si esegue la panoramica del contenuto in un elemento che non occupa l'intero [`Height`](xref:Xamarin.Forms.VisualElement.Height) schermo, l'altezza e la larghezza del riquadro di visualizzazione possono essere ottenute dalle proprietà e [`Width`](xref:Xamarin.Forms.VisualElement.Width) dell'elemento.

> [!NOTE]
> La visualizzazione di immagini ad alta risoluzione può aumentare notevolmente il footprint della memoria dell'app. Di conseguenza, tali risorse devono essere create solo se necessario e rilasciate non appena l'app non le richiede più. Per altre informazioni, vedere [Optimize Image Resources](~/xamarin-forms/deploy-test/performance.md#optimize-image-resources) (Ottimizzare le risorse immagine).

## <a name="related-links"></a>Collegamenti correlati

- [PanGesture (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture) (Esempio di PanGesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
