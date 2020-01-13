---
title: Personalizzazione dell'aspetto di cella ListView
description: Questo articolo esamina le opzioni per la presentazione dei dati nelle applicazioni Xamarin.Forms, sfruttando la praticità del controllo ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2019
ms.openlocfilehash: ab54b54c9f2f7d6d7748137ea079439b7c3ddfca
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998159"
---
# <a name="customizing-listview-cell-appearance"></a>Personalizzazione dell'aspetto di cella ListView

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

La classe Xamarin.[`ListView`](xref:Xamarin.Forms.ListView) Forms viene usata per presentare elenchi scorrevoli, che possono essere personalizzati tramite l'uso `ViewCell` di elementi. Un `ViewCell` elemento può visualizzare testo e immagini, indicare uno stato true/false e ricevere l'input dell'utente.

## <a name="built-in-cells"></a>Compilato in celle
Xamarin.Forms è dotato di celle predefinite che funzionano per molte applicazioni:

- [`TextCell`](#textcell)i controlli vengono utilizzati per visualizzare il testo con una seconda riga facoltativa per il testo del dettaglio.
- [`ImageCell`](#imagecell)i controlli sono simili `TextCell`a, ma includono un'immagine a sinistra del testo.
- `SwitchCell`i controlli vengono usati per presentare e acquisire gli stati on/off o true/false.
- `EntryCell`i controlli vengono usati per presentare i dati di testo che l'utente può modificare.

I [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell) controlli [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) e sono più comunemente utilizzati nel contesto di un oggetto [`TableView`](~/xamarin-forms/user-interface/tableview.md).

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) è una cella per la visualizzazione di testo, facoltativamente con una seconda riga come testo di dettaglio. La schermata seguente mostra `TextCell` gli elementi in iOS e Android:

![](customizing-cell-appearance-images/text-cell-default.png "Nell'esempio TextCell predefinito")

TextCells vengono visualizzate come controlli nativi in fase di esecuzione, in modo che le prestazioni sono molto buona rispetto a un oggetto personalizzato `ViewCell`. TextCells sono personalizzabili, consentendo di impostare le proprietà seguenti:

- `Text` &ndash; il testo che viene visualizzato nella prima riga, in caratteri grandi.
- `Detail` &ndash; testo che viene visualizzato sotto la prima riga, in un carattere più piccolo.
- `TextColor` &ndash; colore del testo.
- `DetailColor` &ndash; il colore del testo dettagliato

La schermata seguente mostra `TextCell` gli elementi con proprietà colore personalizzate:

![](customizing-cell-appearance-images/text-cell-custom.png "Esempio di TextCell personalizzato")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), ad esempio `TextCell`, può essere usata per visualizzare testo e il testo secondario dettaglio e offre prestazioni eccezionali utilizzando i controlli nativi di ciascuna piattaforma. `ImageCell` è diverso da `TextCell` in cui viene visualizzata un'immagine a sinistra del testo.

La schermata seguente mostra `ImageCell` gli elementi in iOS e Android: !["Esempio di ImageCell predefinito"](customizing-cell-appearance-images/image-cell-default.png "Esempio di ImageCell predefinito")

`ImageCell` è utile quando è necessario visualizzare un elenco di dati con un aspetto visivo, ad esempio un elenco di contatti o filmati. `ImageCell`i sono personalizzabili e consentono di impostare:

- `Text` &ndash; il testo che viene visualizzato nella prima riga, in caratteri grandi
- `Detail` &ndash; il testo che viene visualizzato sotto la prima riga, in un carattere più piccolo
- `TextColor` &ndash; il colore del testo
- `DetailColor` &ndash; il colore del testo dettagliato
- `ImageSource` &ndash; l'immagine da visualizzare accanto al testo

La schermata seguente mostra `ImageCell` gli elementi con proprietà colore personalizzate: !["Esempio di ImageCell personalizzato"](customizing-cell-appearance-images/image-cell-custom.png "Esempio di ImageCell personalizzato")

## <a name="custom-cells"></a>Celle personalizzate
Le celle personalizzate consentono di creare layout di celle non supportati dalle celle predefinite. Ad esempio, è possibile presentare una cella con due etichette sulle quali hanno lo stesso peso. Oggetto `TextCell` sarebbe sufficiente perché il `TextCell` ha un'etichetta che è inferiore. La maggior parte delle personalizzazioni di cella aggiungono dati di sola lettura aggiuntivi (ad esempio etichette aggiuntive, immagini o altre informazioni di visualizzazione).

Tutte le celle personalizzate devono derivare da [`ViewCell`](xref:Xamarin.Forms.ViewCell), la stessa classe di base che tutta la cella predefinita dei tipi di utilizzo.

Xamarin.Forms offre un [comportamento di memorizzazione nella cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy) sul controllo che può migliorare le `ListView` prestazioni di scorrimento per alcuni tipi di celle personalizzate.

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

- La cella personalizzata è annidata all'interno di un `DataTemplate`, che si trova all'interno `ListView.ItemTemplate`. Si tratta dello stesso processo utilizzato per qualsiasi cella incorporata.
- `ViewCell` è il tipo di cella personalizzato. Il figlio dell' `DataTemplate` elemento deve essere o derivare dalla `ViewCell` classe.
- All'interno `ViewCell`di il layout può essere gestito da qualsiasi layout di Xamarin.Forms. In questo esempio il layout è gestito da un `StackLayout`, che consente la personalizzazione del colore di sfondo.

> [!NOTE]
> Qualsiasi proprietà di `StackLayout` che è associabile può essere associata all'interno di una cella personalizzata. Questa funzionalità, tuttavia, non viene mostrata nell'esempio di XAML.

### <a name="code"></a>Codice

È anche possibile creare una cella personalizzata nel codice. Prima di tutto, è necessario creare una classe `ViewCell` personalizzata che deriva da:

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

Nel costruttore della pagina, la `ItemTemplate` proprietà del controllo ListView è impostata su un `DataTemplate` oggetto `CustomCell` con il tipo specificato:

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

Quando si associa a un tipo di cella personalizzato [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanze, i controlli dell'interfaccia utente che visualizzano il `BindableProperty` devono utilizzare valori di [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) sottoposto a override per impostare i dati da visualizzare in ogni cella, anziché il costruttore di cella, come illustrato nell'esempio di codice seguente:

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

Il [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) sostituzione verrà chiamata quando il [`BindingContextChanged`](xref:Xamarin.Forms.BindableObject.BindingContextChanged) attivazione dell'evento in risposta al valore della [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) modifica delle proprietà. Pertanto, quando la `BindingContext` viene modificato, i controlli dell'interfaccia utente che visualizzano la [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) valori devono impostare i propri dati. Si noti che il `BindingContext` deve essere verificato per un `null` valore, come può essere impostato da Xamarin.Forms per garbage collection, che a sua volta comporterà il `OnBindingContextChanged` eseguire l'override viene chiamato.

In alternativa, possono associare i controlli dell'interfaccia utente per il [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanze per visualizzare i relativi valori, che elimina la necessità di eseguire l'override di `OnBindingContextChanged` (metodo).

> [!NOTE]
> Quando si esegue l'override `OnBindingContextChanged`, verificare che la classe di base `OnBindingContextChanged` viene chiamato in modo che i delegati registrati ricevano il `BindingContextChanged` evento.

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

Ciò consente di associare il `Name`, `Age`, e `Location` proprietà associabili nel `CustomCell` , a istanza il `Name`, `Age`, e `Location` le proprietà di ogni oggetto nella raccolta sottostante.

L'associazione equivalente in C# è illustrato nell'esempio di codice seguente:

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

In iOS e Android, se il [`ListView`](xref:Xamarin.Forms.ListView) elementi il riciclo e la cella personalizzata Usa un renderer personalizzato, il renderer personalizzato deve implementare correttamente la notifica di modifiche. Quando le celle vengono riutilizzate i relativi valori di proprietà cambia il contesto di associazione viene aggiornato a quella di una cella, disponibile con `PropertyChanged` eventi generati. Per altre informazioni, vedere [personalizzazione di un elemento ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Per altre informazioni sulla cella riciclo, vedere [strategia di memorizzazione nella cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy).

## <a name="related-links"></a>Collegamenti correlati

- [Compilato in celle (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celle personalizzate (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Contesto di associazione modificato (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
