---
title: "impostazione della proprietà ItemsSource della selezione" Description: "la visualizzazione selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo illustra come popolare una selezione con i dati impostando la proprietà ItemsSource e come rispondere alla selezione dell'elemento da parte dell'utente.
ms. prod: Novell MS. AssetID: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 02/26/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="setting-a-pickers-itemssource-property"></a>Impostazione della proprietà ItemsSource per una selezione

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)

_La visualizzazione selezione è un controllo per la selezione di un elemento di testo da un elenco di dati. Questo articolo illustra come popolare una selezione con i dati impostando la proprietà ItemsSource e come rispondere alla selezione dell'elemento da parte dell'utente._

Xamarin.Forms2.3.4 ha migliorato la [`Picker`](xref:Xamarin.Forms.Picker) visualizzazione aggiungendo la possibilità di popolarla con i dati impostando la relativa [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) proprietà e di recuperare l'elemento selezionato dalla [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) Proprietà. Inoltre, il colore del testo per l'elemento selezionato può essere modificato impostando la [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) proprietà su un oggetto [`Color`](xref:Xamarin.Forms.Color) .

## <a name="populating-a-picker-with-data"></a>Popolamento di un selettore con i dati

Un oggetto [`Picker`](xref:Xamarin.Forms.Picker) può essere popolato con i dati impostando la relativa [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) proprietà su una `IList` raccolta. Ogni elemento nella raccolta deve essere di tipo o derivato da `object` . È possibile aggiungere elementi in XAML inizializzando la `ItemsSource` proprietà da una matrice di elementi:

```xaml
<Picker x:Name="picker"
        Title="Select a monkey"
        TitleColor="Red">
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
> Si noti che l'elemento `x:Array` richiede un attributo `Type` che indica il tipo degli elementi nella matrice.

Il codice C# equivalente è illustrato di seguito:

```csharp
var monkeyList = new List<string>();
monkeyList.Add("Baboon");
monkeyList.Add("Capuchin Monkey");
monkeyList.Add("Blue Monkey");
monkeyList.Add("Squirrel Monkey");
monkeyList.Add("Golden Lion Tamarin");
monkeyList.Add("Howler Monkey");
monkeyList.Add("Japanese Macaque");

var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>Risposta alla selezione di un elemento

[`Picker`](xref:Xamarin.Forms.Picker)Supporta la selezione di un elemento alla volta. Quando un utente seleziona un elemento, l' [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento viene generato, la [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) proprietà viene aggiornata a un Integer che rappresenta l'indice dell'elemento selezionato nell'elenco e la [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) proprietà viene aggiornata a `object` che rappresenta l'elemento selezionato. La [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) proprietà è un numero in base zero che indica l'elemento selezionato dall'utente. Se non è selezionato alcun elemento, ad esempio quando [`Picker`](xref:Xamarin.Forms.Picker) viene creato e inizializzato per la prima volta, `SelectedIndex` sarà-1.

> [!NOTE]
> Il comportamento di selezione degli elementi in un [`Picker`](xref:Xamarin.Forms.Picker) può essere personalizzato in iOS con una specifica della piattaforma. Per altre informazioni, vedere [controllo selezione elementi selezione](~/xamarin-forms/platform/ios/picker-selection.md).

Nell'esempio di codice seguente viene illustrato come recuperare il [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) valore della proprietà da [`Picker`](xref:Xamarin.Forms.Picker) in XAML:

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

Il codice C# equivalente è illustrato di seguito:

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

Inoltre, un gestore eventi può essere eseguito quando viene [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) generato l'evento:

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

Questo metodo ottiene il [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) valore della proprietà e usa il valore per recuperare l'elemento selezionato dalla [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) raccolta. Dal punto di vista funzionale, equivale a recuperare l'elemento selezionato dalla [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) Proprietà. Si noti che ogni elemento della `ItemsSource` raccolta è di tipo `object` ed è quindi necessario eseguirne il cast a un oggetto `string` per la visualizzazione.

> [!NOTE]
> Un [`Picker`](xref:Xamarin.Forms.Picker) oggetto può essere inizializzato per visualizzare un elemento specifico impostando le [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) proprietà o. Tuttavia, queste proprietà devono essere impostate dopo l'inizializzazione della [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) raccolta.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Popolamento di un selettore con i dati usando data binding

Un oggetto [`Picker`](xref:Xamarin.Forms.Picker) può essere popolato anche con i dati usando data binding per associare la relativa [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) proprietà a una `IList` raccolta. In XAML questa operazione viene eseguita con l' [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) estensione di markup:

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}" />
```

Il codice C# equivalente è illustrato di seguito:

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

I [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) dati della proprietà vengono associati alla `Monkeys` proprietà del modello di visualizzazione connesso, che restituisce una `IList<Monkey>` raccolta. Nell'esempio di codice seguente viene illustrata la `Monkey` classe, che contiene quattro proprietà:

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

Quando si esegue il binding a un elenco di oggetti, [`Picker`](xref:Xamarin.Forms.Picker) è necessario indicare la proprietà da visualizzare da ogni oggetto. Questa operazione viene eseguita impostando la [`ItemDisplayBinding`](xref:Xamarin.Forms.Picker.ItemDisplayBinding) proprietà sulla proprietà obbligatoria da ogni oggetto. Negli esempi di codice precedenti, `Picker` è impostato per visualizzare ogni `Monkey.Name` valore della proprietà.

### <a name="responding-to-item-selection"></a>Risposta alla selezione di un elemento

Il Data Binding può essere utilizzato per impostare un oggetto sul [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) valore della proprietà quando viene modificato:

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}"
        SelectedItem="{Binding SelectedMonkey}" />
<Label Text="{Binding SelectedMonkey.Name}" ... />
<Label Text="{Binding SelectedMonkey.Location}" ... />
<Image Source="{Binding SelectedMonkey.ImageUrl}" ... />
<Label Text="{Binding SelectedMonkey.Details}" ... />
```

Il codice C# equivalente è illustrato di seguito:

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
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

I [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) dati della proprietà vengono associati alla `SelectedMonkey` proprietà del modello di visualizzazione connesso, che è di tipo `Monkey` . Pertanto, quando l'utente seleziona un elemento in [`Picker`](xref:Xamarin.Forms.Picker) , la `SelectedMonkey` proprietà verrà impostata sull' `Monkey` oggetto selezionato. I `SelectedMonkey` dati dell'oggetto vengono visualizzati nell'interfaccia utente da [`Label`](xref:Xamarin.Forms.Label) e dalle [`Image`](xref:Xamarin.Forms.Image) Visualizzazioni:

![](populating-itemssource-images/monkeys.png "Picker Item Selection")

> [!NOTE]
> Si noti che [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) le [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) proprietà e supportano entrambe associazioni bidirezionali per impostazione predefinita.

## <a name="related-links"></a>Collegamenti correlati

- [Demo di selezione (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [App Monkey (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)
- [Selezione associabile (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
- [API selezione](xref:Xamarin.Forms.Picker)
