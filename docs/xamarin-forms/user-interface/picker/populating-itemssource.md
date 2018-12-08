---
title: L'impostazione di proprietà ItemsSource del controllo di selezione
description: La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo illustra come popolare un controllo di selezione con i dati impostando la proprietà ItemsSource e come rispondere alla selezione di elementi dall'utente.
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 102ff15ea0e0002b783f993c59eb50d03a5ece63
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051069"
---
# <a name="setting-a-pickers-itemssource-property"></a>L'impostazione di proprietà ItemsSource del controllo di selezione

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)

_La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo illustra come popolare un controllo di selezione con i dati impostando la proprietà ItemsSource e come rispondere alla selezione di elementi dall'utente._

Xamarin. Forms 2.3.4 ha migliorato la [ `Picker` ](xref:Xamarin.Forms.Picker) vista aggiungendo la possibilità di popolarlo con i dati tramite l'impostazione relativa [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) proprietà e per recuperare l'elemento selezionato dal [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) proprietà. Inoltre, il colore del testo per l'elemento selezionato può essere modificato impostando il [ `TextColor` ](xref:Xamarin.Forms.Picker.TextColor) proprietà a un [ `Color` ](xref:Xamarin.Forms.Color).

## <a name="populating-a-picker-with-data"></a>Popolamento di un controllo di selezione con i dati

Oggetto [ `Picker` ](xref:Xamarin.Forms.Picker) possono essere popolati con dati mediante l'impostazione relativa [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) proprietà da un `IList` raccolta. Ogni elemento della raccolta deve essere di, o derivati dal tipo `object`. È possibile aggiungere elementi in XAML inizializzando il `ItemsSource` proprietà da una matrice di elementi:

```xaml
<Picker x:Name="picker" Title="Select a monkey">
  <Picker.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </Picker.ItemsSource>
</Picker>
```

> [!NOTE]
> Si noti che il `x:Array` elemento richiede un `Type` attributo che indica il tipo degli elementi nella matrice.

Seguito è riportato il codice c# equivalente:

```csharp
var monkeyList = new List<string>();
monkeyList.Add("Baboon");
monkeyList.Add("Capuchin Monkey");
monkeyList.Add("Blue Monkey");
monkeyList.Add("Squirrel Monkey");
monkeyList.Add("Golden Lion Tamarin");
monkeyList.Add("Howler Monkey");
monkeyList.Add("Japanese Macaque");

var picker = new Picker { Title = "Select a monkey" };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>Rispondere alla selezione di elementi

Oggetto [ `Picker` ](xref:Xamarin.Forms.Picker) supporta la selezione di un elemento alla volta. Quando un utente seleziona un elemento, il [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) viene generato l'evento, il [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) proprietà viene aggiornata in un intero che rappresenta l'indice dell'elemento selezionato nell'elenco e il [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) proprietà viene aggiornata per il `object` che rappresenta l'elemento selezionato. Il [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) proprietà è un numero in base zero che indica l'elemento selezionato dall'utente. Se è selezionato alcun elemento, che non viene soddisfatta quando la [ `Picker` ](xref:Xamarin.Forms.Picker) prima di tutto viene creato e inizializzato, `SelectedIndex` sarà -1.

> [!NOTE]
> Elemento comportamento di selezione in un [ `Picker` ](xref:Xamarin.Forms.Picker) possono essere personalizzati in iOS con una specifica della piattaforma. Per altre informazioni, vedere [controllo di selezione Item Selection](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

Esempio di codice seguente viene illustrato come recuperare il [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) valore della proprietà dal [ `Picker` ](xref:Xamarin.Forms.Picker) in XAML:

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

Seguito è riportato il codice c# equivalente:

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

Inoltre, un gestore di eventi può essere eseguita quando il [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) viene generato l'evento:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = (string)picker.ItemsSource[selectedIndex];
  }
}
```

Questo metodo consente di ottenere il [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) valore della proprietà, il valore viene utilizzato per recuperare l'elemento selezionato dal [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) raccolta. Questa funzionalità equivale a recuperare l'elemento selezionato dal [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) proprietà. Si noti che ogni elemento il `ItemsSource` raccolta è di tipo `object`e pertanto deve essere eseguito il cast a un `string` per la visualizzazione.

> [!NOTE]
> Oggetto [ `Picker` ](xref:Xamarin.Forms.Picker) possono essere inizializzati per visualizzare un elemento specifico impostando le [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) oppure [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) proprietà. Tuttavia, queste proprietà devono essere impostate dopo l'inizializzazione di [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) raccolta.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Popolamento di un controllo di selezione con i dati usando l'associazione dati

Oggetto [ `Picker` ](xref:Xamarin.Forms.Picker) può essere popolato anche con i dati utilizzando un'associazione dati per l'associazione relativa [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) proprietà da un `IList` raccolta. In XAML questo si ottiene con il [ `Binding` ](xref:Xamarin.Forms.Xaml.BindingExtension) estensione di markup:

```xaml
<Picker Title="Select a monkey" ItemsSource="{Binding Monkeys}" ItemDisplayBinding="{Binding Name}" />
```

Seguito è riportato il codice c# equivalente:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

Il [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) esegue l'associazione dati di proprietà per il `Monkeys` proprietà del modello di visualizzazione connessa, che restituisce un `IList<Monkey>` raccolta. Nell'esempio di codice riportato di seguito viene illustrato il `Monkey` (classe), che contiene quattro proprietà:

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

Quando si associa a un elenco di oggetti, il [ `Picker` ](xref:Xamarin.Forms.Picker) deve essere comunicato la proprietà da visualizzare da ciascun oggetto. Questo risultato viene ottenuto impostando il [ `ItemDisplayBinding` ](xref:Xamarin.Forms.Picker.ItemDisplayBinding) proprietà per la proprietà richiesta da ogni oggetto. Negli esempi di codice precedenti, il `Picker` è impostato per visualizzare ogni `Monkey.Name` valore della proprietà.

### <a name="responding-to-item-selection"></a>Rispondere alla selezione di elementi

Associazione dati consente di impostare un oggetto il [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) valore della proprietà in caso di modifiche:

```xaml
<Picker Title="Select a monkey"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}"
        SelectedItem="{Binding SelectedMonkey}" />
<Label Text="{Binding SelectedMonkey.Name}" ... />
<Label Text="{Binding SelectedMonkey.Location}" ... />
<Image Source="{Binding SelectedMonkey.ImageUrl}" ... />
<Label Text="{Binding SelectedMonkey.Details}" ... />
```

Seguito è riportato il codice c# equivalente:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.SetBinding(Picker.SelectedItemProperty, "SelectedMonkey");
picker.ItemDisplayBinding = new Binding("Name");

var nameLabel = new Label { ... };
nameLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Name");

var locationLabel = new Label { ... };
locationLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Location");

var image = new Image { ... };
image.SetBinding(Image.SourceProperty, "SelectedMonkey.ImageUrl");

var detailsLabel = new Label();
detailsLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Details");
```

Il [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) esegue l'associazione dati di proprietà per il `SelectedMonkey` proprietà del modello di visualizzazione connessa, che è di tipo `Monkey`. Pertanto, quando l'utente seleziona un elemento nel [ `Picker` ](xref:Xamarin.Forms.Picker), il `SelectedMonkey` viene impostata al `Monkey` oggetto. Il `SelectedMonkey` dati dell'oggetto viene visualizzati nell'interfaccia utente dal [ `Label` ](xref:Xamarin.Forms.Label) e [ `Image` ](xref:Xamarin.Forms.Image) viste:

![](populating-itemssource-images/monkeys.png "Selezione di elementi di selezione")

> [!NOTE]
> Si noti che il [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) e [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) entrambe le proprietà supportano le associazioni bidirezionali per impostazione predefinita.

## <a name="summary"></a>Riepilogo

Il [ `Picker` ](xref:Xamarin.Forms.Picker) Vista è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo ha illustrato come popolare una `Picker` con i dati impostando i [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) proprietà e su come rispondere alla selezione di elementi dall'utente. Questo approccio, che è stata introdotta in xamarin. Forms 2.3.4, è l'approccio consigliato per l'interazione con un `Picker`.


## <a name="related-links"></a>Collegamenti correlati

- [Demo di selezione (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Monkey App (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)
- [Selezione associabile (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
- [Selezione](xref:Xamarin.Forms.Picker)
