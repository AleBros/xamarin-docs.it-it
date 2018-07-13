---
title: Aggiunta di un riconoscitore di movimento del gesto tocco
description: Questo articolo illustra come usare i movimenti di tocco per il tocco di rilevamento in un'applicazione xamarin. Forms. Rilevamento di tocco viene implementato con la classe TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: e602ae1f140640d9a895b65d78feab3d0a3b7861
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994854"
---
# <a name="adding-a-tap-gesture-gesture-recognizer"></a>Aggiunta di un riconoscitore di movimento del gesto tocco

_Il gesto tocco viene usato per il rilevamento di tocco e viene implementato con la classe TapGestureRecognizer._

## <a name="overview"></a>Panoramica

Per rendere selezionabile con i movimenti tocco di un elemento dell'interfaccia utente, creare un [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) dell'istanza, gestire la [ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) eventi e aggiungere il nuovo riconoscitore di movimento per il [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) raccolta nell'elemento dell'interfaccia utente. Nell'esempio di codice riportato di seguito viene illustrato un `TapGestureRecognizer` collegato a un [ `Image` ](xref:Xamarin.Forms.Image) elemento:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Per impostazione predefinita l'immagine di rispondere alla richiesta singola scelte. Impostare il [ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) proprietà attendere un doppio tocco (o altre scelte se necessario).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Quando [ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) è impostato in precedenza, il gestore dell'evento verrà eseguito solo se si verificano i colpetti all'interno di un determinato periodo di tempo (in questo periodo non è configurabile). Se i colpetti secondo (o successive) non si verifichino entro tale periodo vengono ignorati in modo efficace e riavvia il conteggio di tap' '.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Utilizzo di Xaml

Un riconoscitore di movimento può essere aggiunti a un controllo in Xaml usando le proprietà associate. La sintassi per aggiungere un [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) a un'immagine è illustrato di seguito (in questo caso la definizione di una *doppio tocco* eventi):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

Il codice per il gestore dell'evento (nell'esempio) incrementa un contatore e l'immagine viene modificato dal colore nero &amp; bianco.

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

## <a name="using-icommand"></a>Usa ICommand

Le applicazioni che utilizzano il modello Mvvm in genere usano `ICommand` anziché direttamente il collegamento dei gestori di eventi. Il [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) possibile supportare facilmente `ICommand` impostando l'associazione nel codice:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

In alternativa, con Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

Il codice completo per questo modello di visualizzazione è reperibile nell'esempio. Pertinente `Command` i dettagli di implementazione sono illustrati di seguito:

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

## <a name="summary"></a>Riepilogo

Il gesto tocco viene usato per il rilevamento di tocco e viene implementata con il [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) classe. Il numero di elementi di ritardo può essere specificato per riconoscere un doppio tocco (oppure toccare triple o più tocca) comportamento.


## <a name="related-links"></a>Collegamenti correlati

- [TapGesture (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
