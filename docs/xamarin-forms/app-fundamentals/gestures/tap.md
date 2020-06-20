---
title: Aggiunta di un sistema di riconoscimento del gesto tocco
description: Questo articolo illustra come usare il gesto Tap per il rilevamento tap in un' Xamarin.Forms applicazione. Il rilevamento del tocco viene implementato con la classe TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1f5b476ac83f801b4ccded3e18bb601c06e0d0ef
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84570609"
---
# <a name="adding-a-tap-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento del gesto tocco

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_Il gesto tocco viene usato per il rilevamento del tocco e viene implementato con la classe TapGestureRecognizer._

Per rendere un elemento dell'interfaccia utente selezionabile con il gesto Tap, creare un' [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) istanza, gestire l' [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) evento e aggiungere il nuovo riconoscimento di movimento alla [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) raccolta nell'elemento dell'interfaccia utente. Nell'esempio di codice riportato di seguito viene illustrato un oggetto `TapGestureRecognizer` associato a un [`Image`](xref:Xamarin.Forms.Image) elemento:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Per impostazione predefinita, l'immagine risponde ai singoli tocchi. Impostare la [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) Proprietà in modo che attenda un doppio tocco o più rubinetti, se necessario.

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Quando [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) viene impostato sopra uno, il gestore eventi viene eseguito solo se i colpetti si verificano entro un determinato periodo di tempo (questo periodo non è configurabile). Se non vengono effettuati entro il periodo di tempo, il secondo tocco o i tocchi successivi vengono ignorati e il 'conteggio dei tocchi' viene azzerato.

## <a name="using-xaml"></a>Uso di Xaml

È possibile aggiungere un sistema di riconoscimento del gesto tocco a un controllo in Xaml usando le proprietà associate. Di seguito è illustrata la sintassi per aggiungere un oggetto [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a un'immagine, in questo caso la definizione di un evento di *doppio tocco* :

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

Le applicazioni che usano il criterio Model-View-ViewModel (MVVM) usano in genere `ICommand` anziché collegare direttamente i gestori degli eventi. Il [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) può supportare facilmente impostando `ICommand` l'associazione nel codice:

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
