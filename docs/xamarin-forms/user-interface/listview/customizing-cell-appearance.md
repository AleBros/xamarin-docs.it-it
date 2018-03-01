---
title: Aspetto delle celle
description: "Esplorare le opzioni per la presentazione dei dati sfruttando la praticità di ListView."
ms.topic: article
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 726c65746b1282223c403b08c54c400ea32a6324
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="cell-appearance"></a>Aspetto delle celle

Controllo ListView di vengono visualizzati elenchi scorrevoli, che possono essere personalizzati tramite l'utilizzo di `ViewCell`s. `ViewCells` può essere utilizzato per la visualizzazione di testo e immagini, che indica uno stato true o false e la ricezione dell'input dell'utente.

Esistono due approcci per ottenere il risultato desiderato dal controllo ListView celle:

- **[Personalizzazione di celle predefinite](#Built_in_Cells)**  &ndash; implementazione semplificata e migliorare le prestazioni a discapito della personalizzazione.
- **[Creazione di celle personalizzate](#customcells)**  &ndash; più controllo sul risultato finale, ma sono soggetti a problemi di prestazioni se non è implementato correttamente.

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>Celle predefinite
Xamarin. Forms include le celle predefinite che funzionano per molte applicazioni semplici:

- **TextCell** &ndash; per la visualizzazione di testo
- **ImageCell** &ndash; per visualizzare un'immagine con il testo.

Due altre celle, [ `SwitchCell` ](~/xamarin-forms/user-interface/tableview.md#switchcell) e [ `EntryCell` ](~/xamarin-forms/user-interface/tableview.md#entrycell) sono disponibili, ma non vengono comunemente usati con `ListView`. Vedere [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) per ulteriori informazioni su queste celle.

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](http://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) è una cella per la visualizzazione di testo, facoltativamente con una seconda riga come testo di dettaglio.

TextCells vengono visualizzati come controlli nativi in fase di esecuzione, le prestazioni sono molto efficaci rispetto a un oggetto personalizzato `ViewCell`. TextCells sono personalizzabili, che consente di impostare:

- `Text` &ndash; il testo visualizzato nella prima riga, in caratteri grandi.
- `Detail` &ndash; il testo visualizzato sotto la prima riga, un carattere più piccolo.
- `TextColor` &ndash; il colore del testo.
- `DetailColor` &ndash; il colore del testo dettagliato

![](customizing-cell-appearance-images/text-cell-default.png "Nell'esempio TextCell predefinito")

![](customizing-cell-appearance-images/text-cell-custom.png "Esempio TextCell personalizzato")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](http://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/), ad esempio `TextCell`, può essere utilizzato per visualizzare testo e testo dettagliato secondario e offre prestazioni elevate utilizzando i controlli nativi di ciascuna piattaforma. `ImageCell` è diverso da `TextCell` in cui viene visualizzata un'immagine a sinistra del testo.

`ImageCell` è utile quando è necessario visualizzare un elenco di dati con un aspetto visivo, ad esempio un elenco di contatti o film. ImageCells sono personalizzabili, che consente di impostare:

- `Text` &ndash; il testo visualizzato nella prima riga, in caratteri grandi
- `Detail` &ndash; il testo visualizzato sotto la prima riga, un carattere più piccolo
- `TextColor` &ndash; il colore del testo
- `DetailColor` &ndash; il colore del testo dettagliato
- `ImageSource` &ndash; l'immagine da visualizzare accanto al testo

Si noti che quando la destinazione è Windows Phone 8.1, `ImageCell` non garantiscono le immagini per impostazione predefinita. Inoltre, notare che Windows Phone 8.1 è l'unica piattaforma nella quale dettaglio viene presentato il testo nella stesso colore e tipo di carattere del testo principale per impostazione predefinita. Esegue il rendering di Windows Phone 8.0 `ImageCell` come illustrato di seguito:

![](customizing-cell-appearance-images/image-cell-default.png "Nell'esempio ImageCell predefinito")

![](customizing-cell-appearance-images/image-cell-custom.png "Esempio ImageCell personalizzato")

<a name="customcells" />

## <a name="custom-cells"></a>Celle personalizzate
Quando le celle incorporate non forniscono il layout richiesto, celle personalizzate implementato il layout richiesto. Ad esempio, si desideri presentare una cella con due etichette che hanno lo stesso peso. A `LabelCell` sarebbe sufficiente perché il `LabelCell` ha un'etichetta che è inferiore.

Tutte le celle personalizzate devono derivare da [ `ViewCell` ](http://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), la stessa classe di base che tutti i della cella predefinita tipi di utilizzo.

La maggior parte delle personalizzazioni di cella aggiungono dati di sola lettura aggiuntivi (ad esempio ulteriori etichette, immagini o altre informazioni di visualizzazione). Se vengono aggiunti i pulsanti o altri controlli che possono essere con stato attivo, la cella potrebbe non essere selezionabile in Android. Per un modo aggirare questa limitazione, vedere di seguito.

Xamarin. Forms 2 è stata introdotta una nuova [il comportamento di memorizzazione nella cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy) sul `ListView` controllo che può essere impostata per migliorare le prestazioni di scorrimento per alcuni tipi di celle.

Questo è un esempio di una cella personalizzata:

![](customizing-cell-appearance-images/custom-cell.png "Esempio di cella personalizzato")

### <a name="xaml"></a>XAML
Il codice XAML per creare il layout precedente è inferiore:

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

Esegue il codice XAML precedente molto. Di seguito suddividerla:

- La cella personalizzata è annidata all'interno di un `DataTemplate`, che si trova all'interno `ListView.ItemTemplate`. Questo è lo stesso processo come con qualsiasi altra cella.
- `ViewCell` è il tipo di cella personalizzato. L'elemento figlio del `DataTemplate` elemento deve essere o derivare dal tipo `ViewCell`.
- Si noti che all'interno di `ViewCell`, layout è gestito da un `StackLayout`. Questo layout consente di personalizzare il colore di sfondo. Si noti che qualsiasi proprietà di `StackLayout` ovvero associabili può essere associato all'interno di una cella personalizzata, anche se non illustrata di seguito.

### <a name="cnum"></a>C&num;

Specifica di una cella personalizzata in c# è un po' più dettagliato rispetto l'equivalente XAML. Esaminare il codice seguente:

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

Nel costruttore per la pagina con il `ListView`, impostare il controllo ListView `ItemTemplate` proprietà a un nuovo `DataTemplate`:

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

### <a name="binding-context-changes"></a>Modifiche di contesto di associazione

Durante l'associazione a un tipo di cella personalizzato [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) istanze, i controlli dell'interfaccia utente di visualizzare il `BindableProperty` devono utilizzare valori di [ `OnBindingContextChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.OnBindingContextChanged()/) sottoposto a override per impostare i dati da visualizzare ogni cella, anziché il costruttore di cella, come illustrato nell'esempio di codice seguente:

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

Il [ `OnBindingContextChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.OnBindingContextChanged()/) sostituzione verrà chiamata quando il [ `BindingContextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.BindingContextChanged/) evento viene generato in risposta al valore del [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) la modifica delle proprietà. Pertanto, quando il `BindingContext` viene modificato, i controlli dell'interfaccia utente di visualizzare il [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) valori è necessario impostare i propri dati. Si noti che il `BindingContext` deve essere verificata una `null` valore, questo può essere impostato xamarin. Forms per l'operazione di garbage collection, che a sua volta comporterà la `OnBindingContextChanged` sostituire la chiamata.

In alternativa, cui è possibile associare i controlli dell'interfaccia utente di [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) istanze per visualizzare i relativi valori, che elimina la necessità di eseguire l'override di `OnBindingContextChanged` metodo.

> [!NOTE]
> **Nota**: quando si esegue l'override `OnBindingContextChanged`, assicurarsi che la classe base `OnBindingContextChanged` metodo viene chiamato in modo che i delegati registrati ricevano il `BindingContextChanged` evento.

In XAML, il tipo di cella personalizzato di associazione ai dati può essere ottenuto come illustrato nell'esempio di codice seguente:

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Ciò consente di associare il `Name`, `Age`, e `Location` le proprietà associabili nel `CustomCell` all'istanza di `Name`, `Age`, e `Location` le proprietà di ogni oggetto nella raccolta sottostante.

L'associazione equivalente in c# è illustrata nell'esempio di codice seguente:

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

In iOS e Android, se il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) elementi il riciclo e la cella personalizzata Usa un renderer personalizzato, il renderer personalizzato deve implementare correttamente la notifica di modifiche. Quando le celle vengono riutilizzate i valori di proprietà verranno modificato quando il contesto di associazione viene aggiornato a quello di una cella, disponibile con `PropertyChanged` eventi generati. Per ulteriori informazioni, vedere [personalizzazione un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Per ulteriori informazioni sulla cella riciclo, vedere [strategia di memorizzazione nella cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

### <a name="enabling-row-selection-on-android"></a>Abilitare la selezione della riga in Android

Per consentire la selezione di riga per le celle che includono anche gli elementi di input, ad esempio pulsanti, un semplice [ `custom renderer` ](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) è obbligatorio. Nel codice comune, creare una sottoclasse di `Button` in modo che sia possibile aggiungere un renderer personalizzato nei progetti di piattaforma:

```csharp
public class ListButton : Button { }
```

L'implementazione del renderer per Android imposta semplicemente il `Focusable` proprietà che consente alla riga di essere selezionabile e pulsanti host selezionabile. Questo codice viene aggiunto al progetto di applicazione Android:

```csharp
[assembly: ExportRenderer (typeof (ListButton), typeof (ListButtonRenderer))]
// ...
public class ListButtonRenderer : ButtonRenderer {
    protected override void OnElementChanged (ElementChangedEventArgs<ListButton> e) {
        base.OnElementChanged (e);
        Control.Focusable = false;
    }
}
```

Come descritto in precedenza, solo Android richiede il `ButtonRenderer` da implementare. iOS e le piattaforme Windows Phone consentono il pulsante per essere selezionato senza implementare un renderer personalizzato.


## <a name="related-links"></a>Collegamenti correlati

- [Compilato nelle celle (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [Celle personalizzate (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [Contesto di associazione modificato (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BindingContextChanged)
