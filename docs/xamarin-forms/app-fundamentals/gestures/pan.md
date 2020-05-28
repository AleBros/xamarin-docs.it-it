---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 53122991811c06360e8d015a753096cb35c1cca0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137631"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento del movimento di panoramica

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_Il gesto Pan viene usato per rilevare lo spostamento delle dita intorno allo schermo e l'applicazione del movimento al contenuto e viene implementato con la `PanGestureRecognizer` classe. Uno scenario comune per il movimento Pan consiste nell'eseguire il panning orizzontale e verticale di un'immagine, in modo che tutto il contenuto dell'immagine possa essere visualizzato quando viene visualizzato in un viewport più piccolo delle dimensioni dell'immagine. Questa operazione viene eseguita spostando l'immagine all'interno del viewport e viene illustrata in questo articolo._

Per rendere un elemento dell'interfaccia utente mobile con il gesto di panoramica, creare un' [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) istanza, gestire l' [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) evento e aggiungere il nuovo riconoscimento di movimento alla [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) raccolta nell'elemento dell'interfaccia utente. Nell'esempio di codice riportato di seguito viene illustrato un oggetto `PanGestureRecognizer` associato a un [`Image`](xref:Xamarin.Forms.Image) elemento:

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

È possibile eseguire il wrapping di questa classe per un elemento dell'interfaccia utente, in modo che il movimento esegua la panoramica dell'elemento dell'interfaccia utente sottoposto a wrapping. Nell'esempio di codice XAML seguente viene illustrato il `PanContainer` wrapping di un [`Image`](xref:Xamarin.Forms.Image) elemento:

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

Nell'esempio di codice seguente viene illustrato come l'oggetto `PanContainer` esegue il wrapping [`Image`](xref:Xamarin.Forms.Image) di un elemento in una pagina C#:

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

In entrambi gli esempi, [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) le [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) proprietà e vengono impostate sui valori di larghezza e altezza dell'immagine da visualizzare.

Quando l' [`Image`](xref:Xamarin.Forms.Image) elemento riceve un movimento di panoramica, l'immagine visualizzata verrà stroncata. La panoramica viene eseguita dal metodo `PanContainer.OnPanUpdated`, visualizzato nell'esempio di codice seguente:

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

Questo metodo aggiorna il contenuto visualizzabile dell'elemento dell'interfaccia utente sottoposto a wrapping in base al movimento di panoramica dell'utente. Questa operazione viene eseguita utilizzando i valori delle [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) proprietà e [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) dell' [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) istanza di per calcolare la direzione e la distanza del Pan. Le proprietà `App.ScreenWidth` e `App.ScreenHeight` specificano l'altezza e la larghezza del viewport e vengono impostate sui valori di larghezza e altezza dello schermo del dispositivo dai rispettivi progetti specifici della piattaforma. L'elemento utente di cui è stato eseguito il wrapped viene quindi stroncato impostando le relative [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) proprietà e sui [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) valori calcolati.

Quando si esegue il panning del contenuto in un elemento che non occupa lo schermo intero, è possibile ottenere l'altezza e la larghezza del viewport dalle [`Height`](xref:Xamarin.Forms.VisualElement.Height) proprietà e dell'elemento [`Width`](xref:Xamarin.Forms.VisualElement.Width) .

> [!NOTE]
> La visualizzazione di immagini ad alta risoluzione può aumentare notevolmente il footprint della memoria dell'app. Di conseguenza, tali risorse devono essere create solo se necessario e rilasciate non appena l'app non le richiede più. Per altre informazioni, vedere [Optimize Image Resources](~/xamarin-forms/deploy-test/performance.md#optimize-image-resources) (Ottimizzare le risorse immagine).

## <a name="related-links"></a>Collegamenti correlati

- [PanGesture (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture) (Esempio di PanGesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
