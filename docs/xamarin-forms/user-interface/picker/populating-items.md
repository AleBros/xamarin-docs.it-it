---
title: Aggiunta di dati alla raccolta di elementi del controllo di selezione
description: La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo illustra come popolare un controllo di selezione con i dati, aggiungerlo alla raccolta di elementi e come rispondere alla selezione di elementi dall'utente.
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 5eb3348df39b8931d8cdd89d8c6f54b8bb3024a0
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057431"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>Aggiunta di dati alla raccolta di elementi del controllo di selezione

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)

_La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo illustra come popolare un controllo di selezione con i dati, aggiungerlo alla raccolta di elementi e come rispondere alla selezione di elementi dall'utente._

## <a name="populating-a-picker-with-data"></a>Popolamento di un controllo di selezione con i dati

Prima di xamarin. Forms 2.3.4, il processo per l'inserimento in una [ `Picker` ](xref:Xamarin.Forms.Picker) con i dati è stata per aggiungere i dati da visualizzare per la sola lettura [ `Items` ](xref:Xamarin.Forms.Picker.Items) raccolta, che è di tipo `IList<string>`. Ogni elemento della raccolta deve essere di tipo `string`. È possibile aggiungere elementi in XAML inizializzando il `Items` con un elenco di proprietà `x:String` elementi:

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

Seguito è riportato il codice c# equivalente:

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

Oltre ad aggiungere dati tramite il `Items.Add` metodo, i dati possono anche essere inseriti nella raccolta utilizzando il `Items.Insert` (metodo).

## <a name="responding-to-item-selection"></a>Rispondere alla selezione di elementi

Oggetto [ `Picker` ](xref:Xamarin.Forms.Picker) supporta la selezione di un elemento alla volta. Quando un utente seleziona un elemento, il [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) attivazione dell'evento e il [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) proprietà viene aggiornata in un intero che rappresenta l'indice dell'elemento selezionato nell'elenco. Il `SelectedIndex` proprietà è un numero in base zero che indica l'elemento selezionato dall'utente. Se è selezionato alcun elemento, che non viene soddisfatta quando la `Picker` prima di tutto viene creato e inizializzato, `SelectedIndex` sarà -1.

> [!NOTE]
> Elemento comportamento di selezione in un [ `Picker` ](xref:Xamarin.Forms.Picker) possono essere personalizzati in iOS con una specifica della piattaforma. Per altre informazioni, vedere [controllo di selezione Item Selection](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

Nell'esempio di codice riportato di seguito viene illustrato il `OnPickerSelectedIndexChanged` metodo del gestore eventi, ovvero eseguita quando il [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) viene generato l'evento:

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

Questo metodo consente di ottenere il [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) valore della proprietà, il valore viene utilizzato per recuperare l'elemento selezionato dal [ `Items` ](xref:Xamarin.Forms.Picker.Items) raccolta. Poiché ogni elemento nella `Items` raccolta è un `string`, possano essere visualizzati da un [ `Label` ](xref:Xamarin.Forms.Label) senza un cast.

> [!NOTE]
> Oggetto [ `Picker` ](xref:Xamarin.Forms.Picker) possono essere inizializzati per visualizzare un elemento specifico impostando le [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) proprietà. Tuttavia, il `SelectedIndex` deve essere impostata dopo l'inizializzazione di [ `Items` ](xref:Xamarin.Forms.Picker.Items) raccolta.

## <a name="summary"></a>Riepilogo

Il [ `Picker` ](xref:Xamarin.Forms.Picker) Vista è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo ha illustrato come popolare una `Picker` con i dati, aggiungerlo al [ `Items` ](xref:Xamarin.Forms.Picker.Items) insieme e su come rispondere alla selezione di elementi dall'utente. Questo è il processo per l'uso di un `Picker` prima di xamarin. Forms 2.3.4.


## <a name="related-links"></a>Collegamenti correlati

- [Demo di selezione (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Selezione](xref:Xamarin.Forms.Picker)
