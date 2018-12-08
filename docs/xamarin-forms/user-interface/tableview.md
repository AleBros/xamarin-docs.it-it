---
title: Xamarin. Forms TableView
description: Questo articolo illustra come usare la classe TableView xamarin. Forms per presentare i menu a discesa, impostazioni e moduli di input nelle applicazioni.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/04/2018
ms.openlocfilehash: 0561c2a08aa96755d6d6caa9a3c683a5ce83ae10
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053986"
---
# <a name="xamarinforms-tableview"></a>Xamarin. Forms TableView

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)

[TableView](xref:Xamarin.Forms.TableView) è una vista per la visualizzazione di elenchi scorrevoli di dati o le opzioni in cui sono presenti righe che non condividono lo stesso modello. A differenza [ListView](~/xamarin-forms/user-interface/listview/index.md), TableView non prevedono il concetto di un `ItemsSource`, in modo che gli elementi devono essere aggiunti come elementi figlio manualmente.

Questa guida è costituita da sezioni seguenti:

- **[Casi d'uso](#Use_Cases)**  &ndash; quando utilizzare TableView invece ListView o una visualizzazione personalizzata.
- **[Struttura TableView](#TableView_Structure)**  &ndash; la gerarchia delle visualizzazioni che serve all'interno di un TableView.
- **[Aspetto TableView](#TableView_Appearance)**  &ndash; le opzioni di personalizzazione per TableView.
- **[Celle predefinite](#Built-In_Cells)**  &ndash; opzioni di cella predefiniti, tra cui [EntryCell](#EntryCell) e [SwitchCell](#SwitchCell).
- **[Le celle personalizzate](#Custom_Cells)**  &ndash; come rendere il proprio le celle personalizzate.

![](tableview-images/tableview-all-sml.png "Esempio TableView")

<a name="Use_Cases" />

## <a name="use-cases"></a>Casi d'uso

TableView è utile quando:

- presentare un elenco di impostazioni,
- raccolta dei dati in un form, o
- che mostra i dati presentati in modo diverso dalla riga per riga (ad esempio, numeri, percentuali e immagini).

TableView gestisce lo scorrimento e la disposizione di righe nelle sezioni interessanti, una necessità comune per gli scenari precedenti. Il `TableView` utilizzata dal controllo ogni piattaforma sottostanti della vista equivalente al momento disponibili, creazione di un aspetto nativo per ogni piattaforma.

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>Struttura TableView

Gli elementi in un `TableView` sono organizzati in sezioni. Nella radice del `TableView` è il `TableRoot`, che è elemento padre a uno o più `TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

Ogni `TableSection` è costituito da un'intestazione e uno o più ViewCells. Qui possiamo vedere le `TableSection`del `Title` impostata su *"Anello"* nel costruttore:

```csharp
var section = new TableSection ("Ring") { //TableSection constructor takes title as an optional parameter
    new SwitchCell {Text = "New Voice Mail"},
    new SwitchCell {Text = "New Mail", On = true}
};
```

Per eseguire lo stesso layout come indicato in precedenza in XAML:

```xaml
<TableView Intent="Settings">
    <TableRoot>
        <TableSection Title="Ring">
            <SwitchCell Text="New Voice Mail" />
      <SwitchCell Text="New Mail" On="true" />
        </TableSection>
    </TableRoot>
</TableView>
```

<a name="TableView_Appearance" />

## <a name="tableview-appearance"></a>Aspetto TableView

TableView espone il `Intent` proprietà, che è un'enumerazione delle opzioni seguenti:

- **I dati** &ndash; da usare durante la visualizzazione di voci di dati. Si noti che [ListView](~/xamarin-forms/user-interface/listview/index.md) potrebbe essere un'opzione migliore per lo scorrimento di elenchi di dati.
- **Form** &ndash; per l'uso quando il TableView agisce come un Form.
- **Menu di scelta** &ndash; da usare durante la visualizzazione del menu delle selezioni.
- **Le impostazioni** &ndash; per l'uso quando si visualizza un elenco di impostazioni di configurazione.

Il `TableIntent` scelto può influire sul `TableView` viene visualizzato in ogni piattaforma. Anche se sono presenti differenze non è chiaro, è consigliabile selezionare il `TableIntent` che rispecchia meglio come si intende usare la tabella.

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Celle predefinite

Xamarin. Forms viene fornito con celle predefinite per la raccolta e visualizzazione di informazioni. Sebbene ListView e TableView possono usare tutte le celle stesso, sono più rilevanti per uno scenario TableView quanto segue:

- **SwitchCell** &ndash; presentazione e di acquisizione di uno stato true/false, insieme a un'etichetta di testo.
- **EntryCell** &ndash; a presentare e acquisizione del testo.

Visualizzare [aspetto delle celle ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) per una descrizione dettagliata delle [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) e [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) è il controllo da utilizzare per la presentazione e l'acquisizione un'attivato/disattivato o `true` / `false` dello stato.

SwitchCells hanno una sola riga di testo da modificare e una proprietà di attivazione/disattivazione. Entrambe queste proprietà sono associabili.

- `Text` &ndash; testo da visualizzare accanto il commutatore.
- `On` &ndash; Se l'opzione viene visualizzata come on o off.

Si noti che il `SwitchCell` espone il `OnChanged` eventi, consentendo di rispondere alle modifiche dello stato della cella.

![](tableview-images/switch-cell.png "Esempio SwitchCell")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](xref:Xamarin.Forms.EntryCell) è utile quando è necessario visualizzare i dati di testo che l'utente può modificare. `EntryCell`s offrono le proprietà seguenti che possono essere personalizzate:

- `Keyboard` &ndash; La tastiera per visualizzare durante la modifica. Sono disponibili opzioni per elementi come i valori numerici, messaggio di posta elettronica, i numeri di telefono e così via. [Vedere la documentazione delle API](xref:Xamarin.Forms.Keyboard).
- `Label` &ndash; Il testo dell'etichetta da visualizzare a destra del campo di immissione di testo.
- `LabelColor` &ndash; Colore del testo dell'etichetta.
- `Placeholder` &ndash; Testo da visualizzare nel campo di immissione quando è null o vuoto. Questo testo scomparirà quando viene avviata l'immissione di testo.
- `Text` &ndash; Il testo nel campo di immissione.
- `HorizontalTextAlignment` &ndash; L'allineamento orizzontale del testo. Può essere allineata centro, a sinistra o destra. [Vedere la documentazione delle API](xref:Xamarin.Forms.TextAlignment).

Si noti che `EntryCell` espone il `Completed` evento, che viene attivato quando viene premuto il tasto 'done' sulla tastiera durante la modifica del testo.

![](tableview-images/entry-cell.png "Esempio EntryCell")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Celle personalizzate
Quando le celle predefinite non sono sufficienti, le celle personalizzate è utilizzabile per presentare e acquisire i dati nel modo appropriato per l'app. Ad esempio, è possibile presentare un dispositivo di scorrimento per consentire all'utente di scegliere l'opacità di un'immagine.

Tutte le celle personalizzate devono derivare da [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), la stessa classe di base che tutta la cella predefinita dei tipi di utilizzo.

Questo è un esempio di una cella personalizzata:

![](tableview-images/custom-cell.png "Esempio di cella personalizzato")

### <a name="xaml"></a>XAML
Di seguito è riportata il XAML per creare il layout precedente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="DemoTableView.TablePage" Title="TableView">
    <ContentPage.Content>
        <TableView Intent="Settings">
            <TableRoot>
                <TableSection Title="Getting Started">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Image Source="bulb.png" />
                            <Label Text="left"
                              TextColor="#f35e20" />
                            <Label Text="right"
                              HorizontalOptions="EndAndExpand"
                              TextColor="#503026" />
                        </StackLayout>
                    </ViewCell>
                </TableSection>
            </TableRoot>
        </TableView>
    </ContentPage.Content>
</ContentPage>

```

il XAML riportato sopra esegue molto. È consigliabile analizzarne ogni:

- L'elemento di primo livello sotto il `TableView` è il `TableRoot`.
- È presente una `TableSection` immediatamente sotto il `TableRoot`.
- Il `ViewCell` viene definito direttamente nella sezione della tabella. A differenza `ListView`, `TableView` non richiede tale personalizzato (o qualsiasi) le celle vengono definite un `ItemTemplate`.
- Un StackLayout viene usato per gestire il layout della cella personalizzata. Qualsiasi layout può essere usato qui.

### <a name="cnum"></a>C&num;

Poiché `TableView` funziona con dati statici o dati che sono stati modificati manualmente, che non è il concetto di un modello di elemento. Al contrario, le celle personalizzate possono essere manualmente create e inserite nella tabella. Si noti che la cella la tecnica di creazione di una classe personalizzata che eredita da `ViewCell`, quindi aggiungerlo al `TableView` , ad esempio si sarebbe una cella predefinita, è anche supportato.
Ecco il codice c# per eseguire il layout precedente:

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() {Source = "bulb.png"});
layout.Children.Add (new Label() {
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label () {
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot () {
    new TableSection("Getting Started") {
        new ViewCell() {View = layout}
    }
};

Content = table;
```

Il codice c# sopra esegue molto. È consigliabile analizzarne ogni:

- L'elemento di primo livello sotto il `TableView` è il `TableRoot`.
- È presente una `TableSection` immediatamente sotto il `TableRoot`.
- Il `ViewCell` viene definito direttamente nella sezione della tabella. A differenza `ListView`, `TableView` non richiede tale personalizzato (o qualsiasi) le celle vengono definite un `ItemTemplate`.
- Un StackLayout viene usato per gestire il layout della cella personalizzata. Qualsiasi layout può essere usato qui.

Si noti che non viene definita una classe per la cella personalizzata. Al contrario, il `ViewCell`della visualizzazione viene impostata per una particolare istanza di `ViewCell`.

## <a name="row-height"></a>Altezza di riga

Il [ `TableView` ](xref:Xamarin.Forms.TableView) classe ha due proprietà che possono essere utilizzate per modificare l'altezza della riga di celle:

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) : imposta l'altezza di ogni riga a un `int`.
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) -righe hanno altezza diversa se impostato su `true`. Si noti che quando si imposta questa proprietà su `true`, altezze delle righe verrà automaticamente calcolati e applicate da xamarin. Forms.

Quando l'altezza del contenuto in una cella in una [ `TableView` ](xref:Xamarin.Forms.TableView) viene modificata, la riga in modo implicito l'altezza viene aggiornata in Android e Universal Windows Platform (UWP). Tuttavia, in iOS che deve essere costretto ad aggiornare impostando il [ `HasUnevenRows` ](xref:Xamarin.Forms.TableView.HasUnevenRows) proprietà `true` e chiamando i [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) (metodo).

L'esempio XAML seguente viene illustrato un [ `TableView` ](xref:Xamarin.Forms.TableView) contenente una [ `ViewCell` ](xref:Xamarin.Forms.ViewCell):

```xaml
<ContentPage ...>
    <TableView ...
               HasUnevenRows="true">
        <TableRoot>
            ...
            <TableSection ...>
                ...
                <ViewCell x:Name="_viewCell"
                          Tapped="OnViewCellTapped">
                    <Grid Margin="15,0">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Label Text="Tap this cell." />
                        <Label x:Name="_target"
                               Grid.Row="1"
                               Text="The cell has changed size."
                               IsVisible="false" />
                    </Grid>
                </ViewCell>
            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

Quando la [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) toccando, il `OnViewCellTapped` viene eseguito il gestore eventi:

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

Il `OnViewCellTapped` gestore dell'evento Mostra o nasconde il secondo [ `Label` ](xref:Xamarin.Forms.Label) nel [ `ViewCell` ](xref:Xamarin.Forms.ViewCell)e aggiorna le dimensioni della cella in modo esplicito chiamando la [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) (metodo).

Le schermate seguenti illustrano la cella prima vengano utilizzate al momento:

![](tableview-images/cell-beforeresize.png "Elemento ViewCell prima che venga ridimensionata")

Le schermate seguenti illustrano la cella dopo vengano utilizzate al momento:

![](tableview-images/cell-afterresize.png "Elemento ViewCell dopo il ridimensionamento")

> [!IMPORTANT]
> È possibile sicuro di riduzione delle prestazioni se questa funzionalità è eccessivo.

## <a name="related-links"></a>Collegamenti correlati

- [TableView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)
