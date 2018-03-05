---
title: Aggiunta di un riconoscimento di movimento movimenti tocco
description: I movimenti tocco viene utilizzato per il rilevamento di tap e viene implementato con la classe TapGestureRecognizer.
ms.topic: article
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: d767b50d98b88e6b97a07caffcc103c70cfda428
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="adding-a-tap-gesture-gesture-recognizer"></a>Aggiunta di un riconoscimento di movimento movimenti tocco

_I movimenti tocco viene utilizzato per il rilevamento di tap e viene implementato con la classe TapGestureRecognizer._

## <a name="overview"></a>Panoramica

Per rendere selezionabile con i movimenti tocco un elemento dell'interfaccia utente, creare un [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) dell'istanza, gestire il [ `Tapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.TapGestureRecognizer.Tapped/) eventi e aggiungere il nuovo riconoscitore di movimento per il [ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/) raccolta nell'elemento dell'interfaccia utente. Nell'esempio di codice riportato di seguito viene illustrato un `TapGestureRecognizer` collegato a un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Per impostazione predefinita, l'immagine risponderà alle scelte singole. Impostare il [ `NumberOfTapsRequired` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired/) proprietà attendere un doppio tocco (o altre scelte, se necessario).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Quando [ `NumberOfTapsRequired` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired/) è impostato in precedenza, il gestore dell'evento verrà eseguito soltanto se si verificano le scelte all'interno di un determinato periodo di tempo (in questo periodo non è configurabile). Se entro tale termine non si verificano le scelte di seconda (o versione successive) vengono ignorati e viene riavviato il conteggio di tap' '.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Utilizzo di Xaml

Riconoscitore di movimento può essere aggiunti a un controllo in Xaml tramite le proprietà associate. La sintassi per aggiungere un [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) a un'immagine è illustrato di seguito (in questo caso la definizione di un *doppio tocco* evento):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

Il codice per il gestore dell'evento (nell'esempio) incrementa un contatore e passa l'immagine da colore nero &amp; bianco.

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

## <a name="using-icommand"></a>Utilizzando ICommand

Le applicazioni che utilizzano il modello Mvvm in genere utilizzano `ICommand` anziché il collegamento dei gestori eventi direttamente. Il [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) possibile supportare facilmente `ICommand` impostando l'associazione nel codice:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

o l'utilizzo di Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

Il codice completo per questo modello di visualizzazione è reperibile nell'esempio. Pertinente `Command` seguito sono riportati i dettagli di implementazione:

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

I movimenti tocco viene utilizzato per il rilevamento di tap e viene implementato con la [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) classe. Il numero di elementi di ritardo può essere specificato per riconoscere un doppio tocco (o triplo tocco o più tocca) comportamento.


## <a name="related-links"></a>Collegamenti correlati

- [TapGesture (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [TapGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)
