---
title: Personalizzazione dell'aspetto di cella ListView
description: Questo articolo esamina le opzioni per la presentazione dei dati nelle applicazioni xamarin. Forms, sfruttando la praticità del controllo ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: ecb9e84473778f5185276a854bfbf2fdfcbb6528
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654852"
---
# <a name="customizing-listview-cell-appearance"></a>Personalizzazione dell'aspetto di cella ListView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

[`ListView`](xref:Xamarin.Forms.ListView)presenta elenchi scorrevoli, che possono essere personalizzati tramite l'uso di `ViewCell`s. `ViewCells` può essere utilizzato per la visualizzazione di testo e immagini, che indica uno stato true/false e la ricezione dell'input dell'utente.

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>Compilato in celle
Xamarin. Forms viene fornito con celle predefinite che funzionano per molte applicazioni semplici:

- **TextCell** &ndash; per la visualizzazione di testo
- **ImageCell** &ndash; per visualizzare un'immagine con il testo.

Due altre celle, [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell) e [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) sono disponibili, ma non vengono comunemente utilizzate con `ListView`. Visualizzare [`TableView`](~/xamarin-forms/user-interface/tableview.md) per ulteriori informazioni su queste celle.

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) è una cella per la visualizzazione di testo, facoltativamente con una seconda riga come testo di dettaglio.

TextCells vengono visualizzate come controlli nativi in fase di esecuzione, in modo che le prestazioni sono molto buona rispetto a un oggetto personalizzato `ViewCell`. TextCells sono personalizzabili, consentendo di impostare:

- `Text` &ndash; il testo che viene visualizzato nella prima riga, in caratteri grandi.
- `Detail` &ndash; testo che viene visualizzato sotto la prima riga, in un carattere più piccolo.
- `TextColor` &ndash; colore del testo.
- `DetailColor` &ndash; il colore del testo dettagliato

![](customizing-cell-appearance-images/text-cell-default.png "Nell'esempio TextCell predefinito")

![](customizing-cell-appearance-images/text-cell-custom.png "Esempio TextCell personalizzato")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), ad esempio `TextCell`, può essere usata per visualizzare testo e il testo secondario dettaglio e offre prestazioni eccezionali utilizzando i controlli nativi di ciascuna piattaforma. `ImageCell` è diverso da `TextCell` in cui viene visualizzata un'immagine a sinistra del testo.

`ImageCell` è utile quando è necessario visualizzare un elenco di dati con un aspetto visivo, ad esempio un elenco di contatti o filmati. ImageCells sono personalizzabili, consentendo di impostare:

- `Text` &ndash; il testo che viene visualizzato nella prima riga, in caratteri grandi
- `Detail` &ndash; il testo che viene visualizzato sotto la prima riga, in un carattere più piccolo
- `TextColor` &ndash; il colore del testo
- `DetailColor` &ndash; il colore del testo dettagliato
- `ImageSource` &ndash; l'immagine da visualizzare accanto al testo

![](customizing-cell-appearance-images/image-cell-default.png "Nell'esempio ImageCell predefinito")

![](customizing-cell-appearance-images/image-cell-custom.png "Esempio ImageCell personalizzato")

<a name="customcells" />

## <a name="custom-cells"></a>Celle personalizzate
Quando le celle predefinite non forniscono il layout necessario, le celle personalizzate implementato il layout obbligatorie. Ad esempio, è possibile presentare una cella con due etichette sulle quali hanno lo stesso peso. Oggetto `TextCell` sarebbe sufficiente perché il `TextCell` ha un'etichetta che è inferiore. La maggior parte delle personalizzazioni di cella aggiungono dati di sola lettura aggiuntivi (ad esempio etichette aggiuntive, immagini o altre informazioni di visualizzazione).

Tutte le celle personalizzate devono derivare da [`ViewCell`](xref:Xamarin.Forms.ViewCell), la stessa classe di base che tutta la cella predefinita dei tipi di utilizzo.

Xamarin. Forms 2 viene introdotta una nuova [comportamento della cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy) nel `ListView` controllo che può essere impostata per migliorare le prestazioni di scorrimento per alcuni tipi di celle personalizzate.

Questo è un esempio di una cella personalizzata:

![](customizing-cell-appearance-images/custom-cell.png "Esempio di cella personalizzato")

### <a name="xaml"></a>XAML
Di seguito è riportata il XAML per creare il layout precedente:

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

il XAML riportato sopra esegue molto. È consigliabile analizzarne ogni:

- La cella personalizzata è annidata all'interno di un `DataTemplate`, che si trova all'interno `ListView.ItemTemplate`. Questo è lo stesso processo usando qualsiasi altra cella.
- `ViewCell` è il tipo di cella personalizzato. L'elemento figlio del `DataTemplate` deve essere di o derivare dal tipo di elemento `ViewCell`.
- Si noti che all'interno di `ViewCell`, layout è gestito da un `StackLayout`. Questo layout consente di personalizzare il colore di sfondo. Si noti che tutte le proprietà di `StackLayout` vale a dire associabile possono essere associate all'interno di una cella personalizzata, sebbene non indicata di seguito.
- All'interno `ViewCell`di il layout può essere gestito da qualsiasi layout di Novell. Forms. 

### <a name="cnum"></a>C&num;

Specifica di una cella personalizzata in C# è un po' più dettagliato rispetto l'equivalente XAML. Esaminare il codice seguente:

In primo luogo, definire una classe di cella personalizzato, con `ViewCell` come classe di base:

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

Nel costruttore per la pagina con il `ListView`, impostare il ListView `ItemTemplate` a una nuova proprietà `DataTemplate`:

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

Si noti che il costruttore per `DataTemplate` accetta un tipo. L'operatore typeof Ottiene il tipo CLR per `CustomCell`.

<a name="binding-context-changes" />

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

    public string Name {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null) {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

Il [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) sostituzione verrà chiamata quando il [`BindingContextChanged`](xref:Xamarin.Forms.BindableObject.BindingContextChanged) attivazione dell'evento in risposta al valore della [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) modifica delle proprietà. Pertanto, quando la `BindingContext` viene modificato, i controlli dell'interfaccia utente che visualizzano la [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) valori devono impostare i propri dati. Si noti che il `BindingContext` deve essere verificato per un `null` valore, come può essere impostato da xamarin. Forms per garbage collection, che a sua volta comporterà il `OnBindingContextChanged` eseguire l'override viene chiamato.

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

var listView = new ListView {
    ItemsSource = people,
    ItemTemplate = customCell
};
```

In iOS e Android, se il [`ListView`](xref:Xamarin.Forms.ListView) elementi il riciclo e la cella personalizzata Usa un renderer personalizzato, il renderer personalizzato deve implementare correttamente la notifica di modifiche. Quando le celle vengono riutilizzate i relativi valori di proprietà cambia il contesto di associazione viene aggiornato a quella di una cella, disponibile con `PropertyChanged` eventi generati. Per altre informazioni, vedere [personalizzazione di un elemento ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Per altre informazioni sulla cella riciclo, vedere [strategia di memorizzazione nella cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

## <a name="related-links"></a>Collegamenti correlati

- [Compilato in celle (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celle personalizzate (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Contesto di associazione modificato (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
