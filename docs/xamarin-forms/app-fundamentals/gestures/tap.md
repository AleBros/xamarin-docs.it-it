---
title: Aggiunta di un sistema di riconoscimento del gesto tocco
description: Questo articolo illustra come usare il gesto tocco per il rilevamento del tocco in un'applicazione Xamarin.Forms. Il rilevamento del tocco viene implementato con la classe TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: e2e5a71d7cddee66612c12bc6091c57426b3ed47
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059960"
---
# <a name="adding-a-tap-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento del gesto tocco

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)

_Il gesto tocco viene usato per il rilevamento del tocco e viene implementato con la classe TapGestureRecognizer._

Per consentire il clic tramite il gesto tocco su un elemento dell'interfaccia utente, creare un'istanza di [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer), gestire l'evento [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) e aggiungere il nuovo sistema di riconoscimento del gesto tocco alla raccolta [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) nell'elemento dell'interfaccia utente. L'esempio di codice seguente visualizza un `TapGestureRecognizer` associato a un elemento [`Image`](xref:Xamarin.Forms.Image):

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Per impostazione predefinita, l'immagine risponde ai singoli tocchi. Impostare la proprietà [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) per attendere un doppio tocco (o più tocchi se richiesti).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Quando [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) è impostato su un valore maggiore di uno, il gestore dell'evento viene eseguito solo se i tocchi vengono effettuati entro un determinato periodo di tempo (il periodo di tempo non è configurabile). Se non vengono effettuati entro il periodo di tempo, il secondo tocco o i tocchi successivi vengono ignorati e il 'conteggio dei tocchi' viene azzerato.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Uso di Xaml

È possibile aggiungere un sistema di riconoscimento del gesto tocco a un controllo in Xaml usando le proprietà associate. La sintassi per aggiungere [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a un'immagine è indicata di seguito (in questo caso la definizione di un evento *doppio tocco*):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

Il codice per il gestore dell'evento (nell'esempio) incrementa un contatore e modifica l'immagine da immagine a colori a immagine in bianco e nero.

```csharp
void OnTapGestureRecognizerTapped(object sender, EventArgs args)
{
    tapCount++;
    var imageSender = (Image)sender;
    // watch the monkey go from color to black&white!
    if (tapCount % 2 == 0) {
        imageSender.Source = "tapped.jpg";
    } else {
        imageSender.Source = "tapped_bw.jpg";
    }
}
```

## <a name="using-icommand"></a>Uso di ICommand

Le applicazioni che usano il criterio Model-View-ViewModel (MVVM) usano in genere `ICommand` anziché collegare direttamente i gestori degli eventi. [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) può supportare `ICommand` impostando l'associazione nel codice:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

o usando Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

Il codice completo per questo modello di visualizzazione è indicato nell'esempio. I dettagli dell'implementazione di `Command` pertinenti sono illustrati di seguito:

```csharp
public class TapViewModel : INotifyPropertyChanged
{
    int taps = 0;
    ICommand tapCommand;
    public TapViewModel () {
        // configure the TapCommand with a method
        tapCommand = new Command (OnTapped);
    }
    public ICommand TapCommand {
        get { return tapCommand; }
    }
    void OnTapped (object s)  {
        taps++;
        Debug.WriteLine ("parameter: " + s);
    }
    //region INotifyPropertyChanged code omitted
}
```


## <a name="related-links"></a>Collegamenti correlati

- [TapGesture (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
