---
title: Aggiunta di un sistema di riconoscimento del gesto tocco
description: Questo articolo illustra come usare il gesto tocco per il rilevamento del tocco in un'applicazione Xamarin.Forms. Il rilevamento del tocco viene implementato con la classe TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 95f25dbce55e2b960f604b6e304ffb6e8ed775e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771342"
---
# <a name="adding-a-tap-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento del gesto tocco

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_Il gesto tocco viene usato per il rilevamento del tocco e viene implementato con la classe TapGestureRecognizer._

Per rendere selezionabile un elemento dell'interfaccia [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) utente con [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) il gesto tocco, creare [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) un'istanza, gestire l'evento e aggiungere il nuovo riconoscitore di movimento alla raccolta nell'elemento dell'interfaccia utente. Nell'esempio di `TapGestureRecognizer` codice riportato [`Image`](xref:Xamarin.Forms.Image) di seguito viene illustrato un elemento associato a un elemento:The following code example shows a attached to an element:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Per impostazione predefinita, l'immagine risponde ai singoli tocchi. Imposta [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) la proprietà in modo che attenda un doppio tocco (o più tocchi, se necessario).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Quando [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) è impostato sopra di uno, il gestore eventi verrà eseguito solo se i tocchi si verificano entro un determinato periodo di tempo (questo periodo non è configurabile). Se non vengono effettuati entro il periodo di tempo, il secondo tocco o i tocchi successivi vengono ignorati e il 'conteggio dei tocchi' viene azzerato.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Uso di Xaml

È possibile aggiungere un sistema di riconoscimento del gesto tocco a un controllo in Xaml usando le proprietà associate. La sintassi [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) per aggiungere un a a un'immagine è illustrata di seguito (in questo caso definendo un evento *doppio tocco):*

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

Le applicazioni che usano il criterio Model-View-ViewModel (MVVM) usano in genere `ICommand` anziché collegare direttamente i gestori degli eventi. Il [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) può `ICommand` facilmente supportare impostando l'associazione nel codice:The can easily support either by setting the binding in code:

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

- [TapGesture (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
