---
title: "Impostazione di proprietà ItemsSource un controllo di selezione"
description: "La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. In questo articolo viene illustrato come compilare un controllo di selezione con i dati impostando la proprietà ItemsSource e come rispondere a una selezione di elementi dall'utente."
ms.topic: article
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 5e3d20ad213df9fd9331c71c84003c7738bd5a29
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="setting-a-pickers-itemssource-property"></a>Impostazione di proprietà ItemsSource un controllo di selezione

_La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. In questo articolo viene illustrato come compilare un controllo di selezione con i dati impostando la proprietà ItemsSource e come rispondere a una selezione di elementi dall'utente._

Xamarin. Forms 2.3.4 è migliorato il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) visualizzazione aggiungendo la possibilità di popolarla con i dati impostando il relativo [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) proprietà e per recuperare l'elemento selezionato dal [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) proprietà. Inoltre, il colore del testo per l'elemento selezionato può essere modificato impostando la [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.TextColor/) proprietà per un [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/).

## <a name="populating-a-picker-with-data"></a>Popolamento di un controllo di selezione con dati

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) possono essere popolati con dati impostando il relativo [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) proprietà per un `IList` insieme. Ogni elemento nella raccolta deve essere di, o derivato da, digitare `object`. È possibile aggiungere elementi in XAML inizializzando il `ItemsSource` proprietà da una matrice di elementi:

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

Il codice c# equivalente è illustrato di seguito:

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

## <a name="responding-to-item-selection"></a>Risposta alla selezione di elementi

Oggetto [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) supporta la selezione di un elemento alla volta. Quando un utente seleziona un elemento, il [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) viene generato l'evento, il [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) proprietà viene aggiornata in un intero che rappresenta l'indice dell'elemento selezionato nell'elenco e [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) proprietà viene aggiornata per il `object` che rappresenta l'elemento selezionato. Il [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) proprietà è un numero in base zero che indica l'elemento selezionato dall'utente. Se è selezionato alcun elemento, che si verifica quando il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) viene prima creato e inizializzato, `SelectedIndex` sarà -1.

> [!NOTE]
> Elemento comportamento di selezione in un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) possono essere personalizzati in iOS con un specifico della piattaforma. Per ulteriori informazioni, vedere [controllo selezione elemento](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

Esempio di codice seguente viene illustrato come recuperare il [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) dal valore della proprietà di [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) in XAML:

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

Il codice c# equivalente è illustrato di seguito:

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

Inoltre, un gestore eventi è può essere eseguita quando il [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) viene generato l'evento:

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

Questo metodo ottiene il [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) valore della proprietà, il valore viene utilizzato per recuperare l'elemento selezionato dal [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) insieme. Questa funzionalità equivale a recuperare l'elemento selezionato dal [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) proprietà. Si noti che ogni elemento di `ItemsSource` raccolta è di tipo `object`e pertanto è necessario eseguire il cast a un `string` per la visualizzazione.

> [!NOTE]
> Oggetto [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) possono essere inizializzati per visualizzare un elemento specifico impostando il [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) o [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) proprietà. Tuttavia, è necessario impostare queste proprietà dopo l'inizializzazione di [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) insieme.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Popolamento di un controllo di selezione con i dati utilizzando l'associazione dati

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) può essere inoltre popolata con dati utilizzando l'associazione dati per associare il [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) proprietà per un `IList` insieme. In XAML questo risultato viene ottenuto con il [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) estensione di markup:

```xaml
<Picker Title="Select a monkey" ItemsSource="{Binding Monkeys}" ItemDisplayBinding="{Binding Name}" />
```

Il codice c# equivalente è illustrato di seguito:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

Il [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) Associa dati di proprietà per il `Monkeys` proprietà del modello di visualizzazione connessa, che restituisce un `IList<Monkey>` insieme. Nell'esempio di codice riportato di seguito viene illustrato il `Monkey` (classe), che contiene quattro proprietà:

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

Quando si associa a un elenco di oggetti, il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) deve specificare che la proprietà da visualizzare da ogni oggetto. Questo risultato si ottiene impostando il [ `ItemDisplayBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemDisplayBinding/) proprietà per la proprietà richiesta da ogni oggetto. Negli esempi di codice precedenti, il `Picker` è impostato per visualizzare ogni `Monkey.Name` valore della proprietà.

### <a name="responding-to-item-selection"></a>Risposta alla selezione di elementi

Associazione dati consente di impostare un oggetto il [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) valore della proprietà in caso di modifiche:

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

Il codice c# equivalente è illustrato di seguito:

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

Il [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) Associa dati di proprietà per il `SelectedMonkey` proprietà del modello di visualizzazione connessa, è di tipo `Monkey`. Pertanto, quando l'utente seleziona un elemento il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), `SelectedMonkey` verrà impostata al `Monkey` oggetto. Il `SelectedMonkey` dati dell'oggetto viene visualizzati nell'interfaccia utente da [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) e [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) viste:

![](populating-itemssource-images/monkeys.png "Selezione di elementi di selezione")

> [!NOTE]
> Si noti che il [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) e [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) entrambe le proprietà supportano le associazioni bidirezionali per impostazione predefinita.

## <a name="summary"></a>Riepilogo

Il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) Vista è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo ha descritto come popolare un `Picker` con i dati impostando il [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) , proprietà e come rispondere alla selezione di elementi dall'utente. Questo approccio, che è stata introdotta in xamarin. Forms 2.3.4, è l'approccio consigliato per l'interazione con un `Picker`.


## <a name="related-links"></a>Collegamenti correlati

- [Demo di selezione (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Monkey App (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)
- [Selezione associabili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
- [Selezione](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
