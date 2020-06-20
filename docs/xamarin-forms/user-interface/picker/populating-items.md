---
title: Aggiunta di dati alla raccolta di elementi per una selezione
description: La visualizzazione selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo illustra come popolare una selezione con i dati aggiungendola alla raccolta Items e come rispondere alla selezione dell'elemento da parte dell'utente.
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8872c6748ba778a2622d82803d580c781bd282cd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139633"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>Aggiunta di dati alla raccolta di elementi per una selezione

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)

_La visualizzazione selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo illustra come popolare una selezione con i dati aggiungendola alla raccolta Items e come rispondere alla selezione dell'elemento da parte dell'utente._

## <a name="populating-a-picker-with-data"></a>Popolamento di un selettore con i dati

Prima di Xamarin.Forms 2.3.4, il processo per popolare un oggetto [`Picker`](xref:Xamarin.Forms.Picker) con dati consisteva nell'aggiungere i dati da visualizzare alla raccolta di sola lettura [`Items`](xref:Xamarin.Forms.Picker.Items) , che è di tipo `IList<string>` . Ogni elemento nella raccolta deve essere di tipo `string` . È possibile aggiungere elementi in XAML inizializzando la `Items` proprietà con un elenco di `x:String` elementi:

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red">
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

Il codice C# equivalente è illustrato di seguito:

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

Oltre ad aggiungere dati tramite il `Items.Add` metodo, è possibile inserire i dati nella raccolta anche usando il `Items.Insert` metodo.

## <a name="responding-to-item-selection"></a>Risposta alla selezione di un elemento

[`Picker`](xref:Xamarin.Forms.Picker)Supporta la selezione di un elemento alla volta. Quando un utente seleziona un elemento, [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) viene generato l'evento e la [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) proprietà viene aggiornata a un Integer che rappresenta l'indice dell'elemento selezionato nell'elenco. La `SelectedIndex` proprietà è un numero in base zero che indica l'elemento selezionato dall'utente. Se non è selezionato alcun elemento, ad esempio quando `Picker` viene creato e inizializzato per la prima volta, `SelectedIndex` sarà-1.

> [!NOTE]
> Il comportamento di selezione degli elementi in un [`Picker`](xref:Xamarin.Forms.Picker) può essere personalizzato in iOS con una specifica della piattaforma. Per altre informazioni, vedere [controllo selezione elementi selezione](~/xamarin-forms/platform/ios/picker-selection.md).

Nell'esempio di codice seguente viene illustrato il `OnPickerSelectedIndexChanged` metodo del gestore eventi, che viene eseguito quando viene [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) generato l'evento:

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

Questo metodo ottiene il [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) valore della proprietà e usa il valore per recuperare l'elemento selezionato dalla [`Items`](xref:Xamarin.Forms.Picker.Items) raccolta. Poiché ogni elemento nella `Items` raccolta è un `string` , può essere visualizzato da un [`Label`](xref:Xamarin.Forms.Label) senza richiedere un cast.

> [!NOTE]
> Un [`Picker`](xref:Xamarin.Forms.Picker) oggetto può essere inizializzato per visualizzare un elemento specifico impostando la [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) Proprietà. Tuttavia, la `SelectedIndex` proprietà deve essere impostata dopo l'inizializzazione della [`Items`](xref:Xamarin.Forms.Picker.Items) raccolta.

## <a name="related-links"></a>Collegamenti correlati

- [Demo di selezione (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Selezione](xref:Xamarin.Forms.Picker)
