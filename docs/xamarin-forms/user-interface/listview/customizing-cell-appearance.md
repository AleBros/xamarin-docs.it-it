---
title: "personalizzazione dell'aspetto della cella ListView" Descrizione: "questo articolo illustra le opzioni per la presentazione dei dati nelle Xamarin.Forms applicazioni, sfruttando al contempo il controllo ListView".
ms. prod: Novell MS. AssetID: FD45CB91-1A8F-46FB-B432-6BC20492E456 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 09/12/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="customizing-listview-cell-appearance"></a>Personalizzazione dell'aspetto della cella ListView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

La Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) classe viene utilizzata per presentare elenchi scorrevoli, che possono essere personalizzati tramite l'utilizzo di `ViewCell` elementi. Un `ViewCell` elemento può visualizzare testo e immagini, indicare uno stato true/false e ricevere l'input dell'utente.

## <a name="built-in-cells"></a>Celle predefinite
Xamarin.Formsviene fornita con celle predefinite che funzionano per molte applicazioni:

- [`TextCell`](#textcell)i controlli vengono utilizzati per visualizzare il testo con una seconda riga facoltativa per il testo del dettaglio.
- [`ImageCell`](#imagecell)i controlli sono simili a `TextCell` , ma includono un'immagine a sinistra del testo.
- `SwitchCell`i controlli vengono usati per presentare e acquisire gli stati on/off o true/false.
- `EntryCell`i controlli vengono usati per presentare i dati di testo che l'utente può modificare.

I [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell) [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) controlli e sono più comunemente utilizzati nel contesto di un oggetto [`TableView`](~/xamarin-forms/user-interface/tableview.md) .

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell)è una cella per la visualizzazione di testo, facoltativamente con una seconda riga come testo di dettaglio. La schermata seguente mostra `TextCell` gli elementi in iOS e Android:

![](customizing-cell-appearance-images/text-cell-default.png "Default TextCell Example")

TextCells viene eseguito il rendering come controlli nativi in fase di esecuzione, pertanto le prestazioni sono molto valide rispetto a un oggetto personalizzato `ViewCell` . TextCells sono personalizzabili, consentendo di impostare le proprietà seguenti:

- `Text`&ndash;testo visualizzato sulla prima riga, in un tipo di carattere grande.
- `Detail`&ndash;testo visualizzato sotto la prima riga, in un tipo di carattere minore.
- `TextColor`&ndash;colore del testo.
- `DetailColor`&ndash;colore del testo del dettaglio

La schermata seguente mostra `TextCell` gli elementi con proprietà colore personalizzate:

![](customizing-cell-appearance-images/text-cell-custom.png "Custom TextCell Example")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), `TextCell` ad esempio, può essere utilizzato per visualizzare testo e testo dettaglio secondario e offre prestazioni ottimali utilizzando i controlli nativi di ogni piattaforma. `ImageCell`è diverso da perché `TextCell` Visualizza un'immagine a sinistra del testo.

La schermata seguente mostra `ImageCell` gli elementi in iOS e Android: !["esempio di ImageCell predefinito"](customizing-cell-appearance-images/image-cell-default.png "Esempio di ImageCell predefinito")

`ImageCell`è utile quando è necessario visualizzare un elenco di dati con un aspetto visivo, ad esempio un elenco di contatti o filmati. `ImageCell`i sono personalizzabili e consentono di impostare:

- `Text`&ndash;testo visualizzato sulla prima riga, in carattere grande
- `Detail`&ndash;testo visualizzato sotto la prima riga, in un tipo di carattere inferiore
- `TextColor`&ndash;colore del testo
- `DetailColor`&ndash;colore del testo del dettaglio
- `ImageSource`&ndash;immagine da visualizzare accanto al testo

La schermata seguente mostra `ImageCell` gli elementi con proprietà colore personalizzate: !["esempio di ImageCell personalizzato"](customizing-cell-appearance-images/image-cell-custom.png "Esempio di ImageCell personalizzato")

## <a name="custom-cells"></a>Celle personalizzate
Le celle personalizzate consentono di creare layout di celle non supportati dalle celle predefinite. È ad esempio possibile che si desideri presentare una cella con due etichette con un peso uguale. Un oggetto non `TextCell` sarebbe sufficiente perché `TextCell` ha un'etichetta di dimensioni minori. La maggior parte delle personalizzazioni delle celle aggiunge ulteriori dati di sola lettura, ad esempio etichette aggiuntive, immagini o altre informazioni di visualizzazione.

Tutte le celle personalizzate devono derivare da [`ViewCell`](xref:Xamarin.Forms.ViewCell) , la stessa classe di base utilizzata da tutti i tipi di cella incorporati.

Xamarin.Formsoffre un [comportamento di memorizzazione nella cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy) sul `ListView` controllo che può migliorare le prestazioni di scorrimento per alcuni tipi di celle personalizzate.

Lo screenshot seguente mostra un esempio di una cella personalizzata:

!["Esempio di cella personalizzata"](customizing-cell-appearance-images/custom-cell.png "Esempio di cella personalizzata")

### <a name="xaml"></a>XAML
La cella personalizzata mostrata nella schermata precedente può essere creata con il codice XAML seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="demoListView.ImageCellPage">
    <ContentPage.Content>
        <ListView  x:Name="listView">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout BackgroundColor="#eee"
                        Orientation="Vertical">
                            <StackLayout Orientation="Horizontal">
                                <Image Source="{Binding image}" />
                                <Label Text="{Binding title}"
                                TextColor="#f35e20" />
                                <Label Text="{Binding subtitle}"
                                HorizontalOptions="EndAndExpand"
                                TextColor="#503026" />
                            </StackLayout>
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

Il codice XAML funziona nel modo seguente:

- La cella personalizzata è annidata all'interno di un oggetto `DataTemplate` , che si trova all'interno di `ListView.ItemTemplate` . Si tratta dello stesso processo utilizzato per qualsiasi cella incorporata.
- `ViewCell`tipo della cella personalizzata. Il figlio dell' `DataTemplate` elemento deve essere o derivare dalla `ViewCell` classe.
- All'interno `ViewCell` di, il layout può essere gestito da qualsiasi Xamarin.Forms layout. In questo esempio il layout è gestito da un `StackLayout` , che consente la personalizzazione del colore di sfondo.

> [!NOTE]
> Qualsiasi proprietà di `StackLayout` che è associabile può essere associata all'interno di una cella personalizzata. Questa funzionalità, tuttavia, non viene mostrata nell'esempio di XAML.

### <a name="code"></a>Codice

È anche possibile creare una cella personalizzata nel codice. Prima di tutto, è necessario creare una classe personalizzata che deriva da `ViewCell` :

```csharp
public class CustomCell : ViewCell
    {
        public CustomCell()
        {
            //instantiate each of our views
            var image = new Image ();
            StackLayout cellWrapper = new StackLayout ();
            StackLayout horizontalLayout = new StackLayout ();
            Label left = new Label ();
            Label right = new Label ();

            //set bindings
            left.SetBinding (Label.TextProperty, "title");
            right.SetBinding (Label.TextProperty, "subtitle");
            image.SetBinding (Image.SourceProperty, "image");

            //Set properties for desired design
            cellWrapper.BackgroundColor = Color.FromHex ("#eee");
            horizontalLayout.Orientation = StackOrientation.Horizontal;
            right.HorizontalOptions = LayoutOptions.EndAndExpand;
            left.TextColor = Color.FromHex ("#f35e20");
            right.TextColor = Color.FromHex ("503026");

            //add views to the view hierarchy
            horizontalLayout.Children.Add (image);
            horizontalLayout.Children.Add (left);
            horizontalLayout.Children.Add (right);
            cellWrapper.Children.Add (horizontalLayout);
            View = cellWrapper;
        }
    }
```

Nel costruttore della pagina, la proprietà del controllo ListView `ItemTemplate` è impostata su un oggetto `DataTemplate` con il `CustomCell` tipo specificato:

```csharp
public partial class ImageCellPage : ContentPage
    {
        public ImageCellPage ()
        {
            InitializeComponent ();
            listView.ItemTemplate = new DataTemplate (typeof(CustomCell));
        }
    }
```

### <a name="binding-context-changes"></a>Modifiche del contesto di associazione

Quando si esegue l'associazione a istanze di un tipo di cella personalizzato [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , i controlli dell'interfaccia utente che visualizzano i `BindableProperty` valori devono usare l' [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) override per impostare i dati da visualizzare in ogni cella, anziché il costruttore della cella, come illustrato nell'esempio di codice seguente:

```csharp
public class CustomCell : ViewCell
{
    Label nameLabel, ageLabel, locationLabel;

    public static readonly BindableProperty NameProperty =
        BindableProperty.Create ("Name", typeof(string), typeof(CustomCell), "Name");
    public static readonly BindableProperty AgeProperty =
        BindableProperty.Create ("Age", typeof(int), typeof(CustomCell), 0);
    public static readonly BindableProperty LocationProperty =
        BindableProperty.Create ("Location", typeof(string), typeof(CustomCell), "Location");

    public string Name
    {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age
    {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location
    {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null)
        {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

L' [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) override verrà chiamato quando viene [`BindingContextChanged`](xref:Xamarin.Forms.BindableObject.BindingContextChanged) generato l'evento, in risposta al valore della proprietà in fase di [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) modifica. Pertanto, quando `BindingContext` cambia, i controlli dell'interfaccia utente che visualizzano i [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) valori devono impostare i relativi dati. Si noti che `BindingContext` deve essere verificata la `null` presenza di un valore, in quanto può essere impostato da Xamarin.Forms per Garbage Collection, che a sua volta comporterà la `OnBindingContextChanged` chiamata dell'override.

In alternativa, i controlli dell'interfaccia utente possono essere associati alle [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanze per visualizzare i relativi valori, eliminando la necessità di eseguire l'override del `OnBindingContextChanged` metodo.

> [!NOTE]
> Quando si esegue l'override `OnBindingContextChanged` di, assicurarsi che venga chiamato il metodo della classe di base in `OnBindingContextChanged` modo che i delegati registrati ricevano l' `BindingContextChanged` evento.

In XAML, l'associazione del tipo di cella personalizzato ai dati può essere eseguita come illustrato nell'esempio di codice seguente:

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

In questo modo, `Name` le `Age` `Location` proprietà associabili, e nell'istanza di vengono associate `CustomCell` alle `Name` `Age` proprietà, e `Location` di ogni oggetto della raccolta sottostante.

L'associazione equivalente in C# è illustrata nell'esempio di codice seguente:

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView
{
    ItemsSource = people,
    ItemTemplate = customCell
};
```

In iOS e Android, se [`ListView`](xref:Xamarin.Forms.ListView) è in esecuzione il riciclo degli elementi e la cella personalizzata usa un renderer personalizzato, il renderer personalizzato deve implementare correttamente la notifica delle modifiche alle proprietà. Quando le celle vengono riutilizzate, i valori delle proprietà verranno modificati quando il contesto di associazione viene aggiornato a quello di una cella disponibile, con la `PropertyChanged` generazione di eventi. Per altre informazioni, vedere [personalizzazione di un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Per ulteriori informazioni sul riciclo delle celle, vedere la pagina relativa alla [strategia di Caching](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy).

## <a name="related-links"></a>Collegamenti correlati

- [Celle predefinite (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celle personalizzate (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Contesto di associazione modificato (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
