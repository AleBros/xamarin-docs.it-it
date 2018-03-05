---
title: Aggiunta di dati alla raccolta di elementi di un controllo di selezione
description: "La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. In questo articolo viene illustrato come compilare un controllo di selezione con i dati, aggiungerlo alla raccolta di elementi e come rispondere a una selezione di elementi dall'utente."
ms.topic: article
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 3468e38d8ef46dfef870a05bf72d93c28195dae7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="adding-data-to-a-pickers-items-collection"></a>Aggiunta di dati alla raccolta di elementi di un controllo di selezione

_La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. In questo articolo viene illustrato come compilare un controllo di selezione con i dati, aggiungerlo alla raccolta di elementi e come rispondere a una selezione di elementi dall'utente._

## <a name="populating-a-picker-with-data"></a>Popolamento di un controllo di selezione con dati

Prima di xamarin. Forms 2.3.4, il processo per la compilazione di un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) con dati di stato per aggiungere i dati da visualizzare in sola lettura [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) raccolta, è di tipo `IList<string>`. Ogni elemento nella raccolta deve essere di tipo `string`. È possibile aggiungere elementi in XAML l'inizializzazione di `Items` proprietà con un elenco di `x:String` elementi:

```xaml
<Picker Title="Select a monkey">
  <Picker.Items>
    <x:String>Baboon</x:String>
    <x:String>Capuchin Monkey</x:String>
    <x:String>Blue Monkey</x:String>
    <x:String>Squirrel Monkey</x:String>
    <x:String>Golden Lion Tamarin</x:String>
    <x:String>Howler Monkey</x:String>
    <x:String>Japanese Macaque</x:String>
  </Picker.Items>
</Picker>
```

Il codice c# equivalente è illustrato di seguito:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

Oltre ad aggiungere dati tramite il `Items.Add` (metodo), dati inoltre possono essere inseriti nella raccolta utilizzando la `Items.Insert` metodo.

## <a name="responding-to-item-selection"></a>Risposta alla selezione di elementi

Oggetto [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) supporta la selezione di un elemento alla volta. Quando un utente seleziona un elemento, il [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) viene generato l'evento e [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) proprietà viene aggiornata in un intero che rappresenta l'indice dell'elemento selezionato nell'elenco. Il `SelectedIndex` proprietà è un numero in base zero che indica l'elemento selezionato dall'utente. Se è selezionato alcun elemento, che si verifica quando il `Picker` viene prima creato e inizializzato, `SelectedIndex` sarà -1.

> [!NOTE]
> Elemento comportamento di selezione in un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) possono essere personalizzati in iOS con un specifico della piattaforma. Per ulteriori informazioni, vedere [controllo selezione elemento](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

Nell'esempio di codice riportato di seguito viene illustrato il `OnPickerSelectedIndexChanged` metodo del gestore eventi, ovvero eseguita quando il [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) viene generato l'evento:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = picker.Items[selectedIndex];
  }
}
```

Questo metodo ottiene il [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) valore della proprietà, il valore viene utilizzato per recuperare l'elemento selezionato dal [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) insieme. Poiché ogni elemento nel `Items` raccolta è un `string`, possono essere visualizzati da un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) senza un cast.

> [!NOTE]
> Oggetto [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) possono essere inizializzati per visualizzare un elemento specifico impostando il [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) proprietà. Tuttavia, il `SelectedIndex` deve essere impostata dopo l'inizializzazione di [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) insieme.

## <a name="summary"></a>Riepilogo

Il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) Vista è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo ha descritto come popolare un `Picker` con i dati aggiungendo l'oggetto per il [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) insieme e come rispondere a una selezione di elementi dall'utente. Questo è il processo per l'utilizzo di un `Picker` prima di xamarin. Forms 2.3.4.


## <a name="related-links"></a>Collegamenti correlati

- [Demo di selezione (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Selezione](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
