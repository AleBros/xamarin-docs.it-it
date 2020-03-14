---
title: Personalizzazione dell'aspetto di cella ListView
description: Questo articolo esamina le opzioni per la presentazione dei dati nelle applicazioni xamarin. Forms, sfruttando la praticità del controllo ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2019
ms.openlocfilehash: ab54b54c9f2f7d6d7748137ea079439b7c3ddfca
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305451"
---
# <a name="customizing-listview-cell-appearance"></a>Personalizzazione dell'aspetto di cella ListView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

La classe [`ListView`](xref:Xamarin.Forms.ListView) Novell. Forms viene usata per presentare elenchi scorrevoli, che possono essere personalizzati tramite l'uso di `ViewCell` elementi. Un elemento `ViewCell` può visualizzare testo e immagini, indicare uno stato true/false e ricevere l'input dell'utente.

## <a name="built-in-cells"></a>Compilato in celle
Novell. Forms è dotato di celle predefinite che funzionano per molte applicazioni:

- i controlli [`TextCell`](#textcell) vengono utilizzati per visualizzare il testo con una seconda riga facoltativa per il testo del dettaglio.
- i controlli [`ImageCell`](#imagecell) sono simili ai `TextCell`, ma includono un'immagine a sinistra del testo.
- i controlli `SwitchCell` vengono usati per presentare e acquisire gli stati on/off o true/false.
- i controlli `EntryCell` vengono usati per presentare i dati di testo che l'utente può modificare.

I controlli [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell) e [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) sono più comunemente utilizzati nel contesto di un [`TableView`](~/xamarin-forms/user-interface/tableview.md).

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) è una cella per la visualizzazione di testo, facoltativamente con una seconda riga come testo di dettaglio. La schermata seguente Mostra gli elementi `TextCell` in iOS e Android:

![](customizing-cell-appearance-images/text-cell-default.png "Default TextCell Example")

TextCells viene eseguito il rendering come controlli nativi in fase di esecuzione, pertanto le prestazioni sono molto valide rispetto a una `ViewCell`personalizzata. TextCells sono personalizzabili, consentendo di impostare le proprietà seguenti:

- `Text` &ndash; il testo visualizzato sulla prima riga, in un tipo di carattere grande.
- `Detail` &ndash; il testo visualizzato sotto la prima riga, in un tipo di carattere più piccolo.
- `TextColor` &ndash; il colore del testo.
- `DetailColor` &ndash; colore del testo del dettaglio

Lo screenshot seguente mostra `TextCell` elementi con proprietà colore personalizzate:

![](customizing-cell-appearance-images/text-cell-custom.png "Custom TextCell Example")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), ad esempio `TextCell`, può essere utilizzato per la visualizzazione di testo e testo di dettaglio secondario e offre prestazioni ottimali utilizzando i controlli nativi di ogni piattaforma. `ImageCell` differisce da `TextCell` perché Visualizza un'immagine a sinistra del testo.

La schermata seguente Mostra gli elementi `ImageCell` in iOS e Android: !["esempio di ImageCell predefinito"](customizing-cell-appearance-images/image-cell-default.png "Esempio di ImageCell predefinito")

`ImageCell` è utile quando è necessario visualizzare un elenco di dati con un aspetto visivo, ad esempio un elenco di contatti o filmati. `ImageCell`s sono personalizzabili, consentendo di impostare:

- `Text` &ndash; il testo visualizzato sulla prima riga, in un tipo di carattere grande
- `Detail` &ndash; il testo visualizzato sotto la prima riga, in un tipo di carattere inferiore
- `TextColor` &ndash; colore del testo
- `DetailColor` &ndash; colore del testo del dettaglio
- `ImageSource` &ndash; immagine da visualizzare accanto al testo

Lo screenshot seguente Mostra gli elementi di `ImageCell` con proprietà colore personalizzate: !["esempio di ImageCell personalizzato"](customizing-cell-appearance-images/image-cell-custom.png "Esempio di ImageCell personalizzato")

## <a name="custom-cells"></a>Celle personalizzate
Le celle personalizzate consentono di creare layout di celle non supportati dalle celle predefinite. Ad esempio, è possibile presentare una cella con due etichette sulle quali hanno lo stesso peso. Un `TextCell` non sarebbe sufficiente perché il `TextCell` dispone di un'etichetta di dimensioni minori. La maggior parte delle personalizzazioni di cella aggiungono dati di sola lettura aggiuntivi (ad esempio etichette aggiuntive, immagini o altre informazioni di visualizzazione).

Tutte le celle personalizzate devono derivare da [`ViewCell`](xref:Xamarin.Forms.ViewCell), la stessa classe di base utilizzata da tutti i tipi di cella incorporati.

Novell. Forms offre un [comportamento di memorizzazione nella cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy) sul controllo `ListView` che può migliorare le prestazioni di scorrimento per alcuni tipi di celle personalizzate.

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

- La cella personalizzata è annidata all'interno di una `DataTemplate`, che si trova all'interno `ListView.ItemTemplate`. Si tratta dello stesso processo utilizzato per qualsiasi cella incorporata.
- `ViewCell` è il tipo della cella personalizzata. Il figlio dell'elemento `DataTemplate` deve essere di o derivare dalla classe `ViewCell`.
- All'interno del `ViewCell`il layout può essere gestito da qualsiasi layout di Novell. Forms. In questo esempio il layout è gestito da un `StackLayout`, che consente di personalizzare il colore di sfondo.

> [!NOTE]
> Qualsiasi proprietà di `StackLayout` associabile può essere associata all'interno di una cella personalizzata. Questa funzionalità, tuttavia, non viene mostrata nell'esempio di XAML.

### <a name="code"></a>Codice

È anche possibile creare una cella personalizzata nel codice. In primo luogo, è necessario creare una classe personalizzata che deriva da `ViewCell`:

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

Nel costruttore della pagina la proprietà `ItemTemplate` di ListView è impostata su un `DataTemplate` con il tipo di `CustomCell` specificato:

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

### <a name="binding-context-changes"></a>Modifica del contesto di associazione

Quando si esegue l'associazione a istanze [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) di un tipo di cella personalizzato, i controlli dell'interfaccia utente che visualizzano i valori di `BindableProperty` devono usare l'override [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) per impostare i dati da visualizzare in ogni cella, anziché il costruttore della cella, come illustrato nell'esempio di codice seguente:

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

L'override del [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) verrà chiamato quando viene generato l'evento [`BindingContextChanged`](xref:Xamarin.Forms.BindableObject.BindingContextChanged) , in risposta al valore della modifica della proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . Pertanto, quando il `BindingContext` cambia, i controlli dell'interfaccia utente che visualizzano i valori di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) devono impostare i relativi dati. Si noti che è necessario verificare la presenza di un valore `null` per il `BindingContext`, in quanto può essere impostato da Novell. Forms per Garbage Collection, che a sua volta comporterà la chiamata del `OnBindingContextChanged` override.

In alternativa, i controlli dell'interfaccia utente possono essere associati alle istanze [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) per visualizzare i relativi valori, evitando così la necessità di eseguire l'override del metodo `OnBindingContextChanged`.

> [!NOTE]
> Quando si esegue l'override di `OnBindingContextChanged`, assicurarsi che venga chiamato il metodo `OnBindingContextChanged` della classe base in modo che i delegati registrati ricevano l'evento `BindingContextChanged`.

In XAML, associazione il tipo di cella personalizzato ai dati può essere ottenuto come illustrato nell'esempio di codice seguente:

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

In questo modo le proprietà `Name`, `Age`e `Location` associabili nell'istanza di `CustomCell` vengono associate alle proprietà `Name`, `Age`e `Location` di ogni oggetto della raccolta sottostante.

L'associazione equivalente in c# è illustrato nell'esempio di codice seguente:

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

In iOS e Android, se il [`ListView`](xref:Xamarin.Forms.ListView) è riciclo di elementi e la cella personalizzata usa un renderer personalizzato, il renderer personalizzato deve implementare correttamente la notifica delle modifiche alle proprietà. Quando le celle vengono riutilizzate, i valori delle proprietà verranno modificati quando il contesto di associazione viene aggiornato a quello di una cella disponibile, con `PropertyChanged` eventi generati. Per altre informazioni, vedere [personalizzazione di un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Per ulteriori informazioni sul riciclo delle celle, vedere la pagina relativa alla [strategia di Caching](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy).

## <a name="related-links"></a>Collegamenti correlati

- [Celle predefinite (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celle personalizzate (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Contesto di associazione modificato (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
