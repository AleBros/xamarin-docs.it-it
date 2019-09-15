---
title: Xamarin. Forms TableView
description: Questo articolo illustra come usare la classe TableView xamarin. Forms per presentare i menu a discesa, impostazioni e moduli di input nelle applicazioni.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: 558eb9f476fd6b566f1f161c01fc809498a4c4a8
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998001"
---
# <a name="xamarinforms-tableview"></a>Xamarin. Forms TableView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView)visualizzazione per la visualizzazione di elenchi scorrevoli di dati o opzioni in cui sono presenti righe che non condividono lo stesso modello. A differenza di [ListView](~/xamarin-forms/user-interface/listview/index.md), `TableView` non ha il concetto di `ItemsSource`, pertanto gli elementi devono essere aggiunti manualmente come elementi figlio.

![Esempio di TableView](tableview-images/tableview-all-sml.png)

<a name="Use_Cases" />

## <a name="use-cases"></a>Casi d'uso

[`TableView`](xref:Xamarin.Forms.TableView)è utile nei casi seguenti:

- presentare un elenco di impostazioni,
- raccolta dei dati in un form, o
- che mostra i dati presentati in modo diverso dalla riga per riga (ad esempio, numeri, percentuali e immagini).

[`TableView`](xref:Xamarin.Forms.TableView)gestisce lo scorrimento e il layout delle righe nelle sezioni attraenti, una necessità comune per gli scenari precedenti. Il `TableView` utilizzata dal controllo ogni piattaforma sottostanti della vista equivalente al momento disponibili, creazione di un aspetto nativo per ogni piattaforma.

<a name="TableView_Structure" />

## <a name="structure"></a>Struttura

Gli elementi in [`TableView`](xref:Xamarin.Forms.TableView) un oggetto sono organizzati in sezioni. Alla radice di `TableView` [`TableRoot`](xref:Xamarin.Forms.TableRoot)è, che è padre di una o più [`TableSection`](xref:Xamarin.Forms.TableSection) istanze di. Ogni [`TableSection`](xref:Xamarin.Forms.TableSection) è costituito da un'intestazione e da [`ViewCell`](xref:Xamarin.Forms.ViewCell) una o più istanze:

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

Il codice C# equivalente è:

```csharp
Content = new TableView
{
    Root = new TableRoot
    {
        new TableSection("Ring")
        {
          // TableSection constructor takes title as an optional parameter
          new SwitchCell { Text = "New Voice Mail" },
          new SwitchCell { Text = "New Mail", On = true }
        }
    },
    Intent = TableIntent.Settings
};
```

<a name="TableView_Appearance" />

## <a name="appearance"></a>Aspetto

[`TableView`](xref:Xamarin.Forms.TableView)espone la [`Intent`](xref:Xamarin.Forms.TableView.Intent) proprietà, che può essere impostata su uno [`TableIntent`](xref:Xamarin.Forms.TableIntent) dei membri dell'enumerazione:

- `Data`: da utilizzare per la visualizzazione di voci di dati. Si noti che [ListView](~/xamarin-forms/user-interface/listview/index.md) potrebbe essere un'opzione migliore per lo scorrimento di elenchi di dati.
- `Form`: da usare quando TableView funge da form.
- `Menu`: da usare quando si presenta un menu di selezioni.
- `Settings`: da usare quando si visualizza un elenco di impostazioni di configurazione.

Il [`TableIntent`](xref:Xamarin.Forms.TableIntent) valore scelto può influisca sul [`TableView`](xref:Xamarin.Forms.TableView) modo in cui viene visualizzato in ogni piattaforma. Anche se sono presenti differenze non è chiaro, è consigliabile selezionare il `TableIntent` che rispecchia meglio come si intende usare la tabella.

Inoltre, il colore del testo visualizzato per ogni [`TableSection`](xref:Xamarin.Forms.TableSection) oggetto può essere modificato impostando la `TextColor` proprietà su un oggetto [`Color`](xref:Xamarin.Forms.Color).

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Celle predefinite

Xamarin. Forms viene fornito con celle predefinite per la raccolta e visualizzazione di informazioni. Sebbene [`ListView`](xref:Xamarin.Forms.ListView) [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) e [`TableView`](xref:Xamarin.Forms.TableView) possano utilizzare tutte le stesse celle, e [`EntryCell`](xref:Xamarin.Forms.EntryCell) sono le più rilevanti per uno `TableView` scenario.

Visualizzare [aspetto delle celle ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) per una descrizione dettagliata delle [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) e [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) è il controllo da utilizzare per la presentazione e l'acquisizione un'attivato/disattivato o `true` / `false` dello stato. Definisce le proprietà seguenti:

- `Text`: testo da visualizzare accanto all'opzione.
- `On`: indica se l'opzione viene visualizzata come attivata o disattivata.
- `OnColor`: oggetto [`Color`](xref:Xamarin.Forms.Color) dell'opzione quando si trova in in posizione.

Tutte queste proprietà sono associabili.

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)espone anche l' `OnChanged` evento, consentendo di rispondere alle modifiche nello stato della cella.

![Esempio di SwitchCell](tableview-images/switch-cell.png)

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell) è utile quando è necessario visualizzare i dati di testo che l'utente può modificare. Definisce le proprietà seguenti:

- `Keyboard`: La tastiera da visualizzare durante la modifica. Sono disponibili opzioni per elementi come i valori numerici, messaggio di posta elettronica, i numeri di telefono e così via. [Vedere la documentazione delle API](xref:Xamarin.Forms.Keyboard).
- `Label`: Testo dell'etichetta da visualizzare a sinistra del campo di immissione di testo.
- `LabelColor`: Colore del testo dell'etichetta.
- `Placeholder`: Testo da visualizzare nel campo di immissione quando è null o vuoto. Questo testo scomparirà quando viene avviata l'immissione di testo.
- `Text`: Il testo nel campo di immissione.
- `HorizontalTextAlignment`: Allineamento orizzontale del testo. Può essere allineata centro, a sinistra o destra. [Vedere la documentazione delle API](xref:Xamarin.Forms.TextAlignment).

[`EntryCell`](xref:Xamarin.Forms.EntryCell)espone anche l' `Completed` evento, che viene generato quando l'utente preme il pulsante "fine" sulla tastiera durante la modifica del testo.

![Esempio di EntryCell](tableview-images/entry-cell.png)

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Celle personalizzate

Quando le celle predefinite non sono sufficienti, le celle personalizzate è utilizzabile per presentare e acquisire i dati nel modo appropriato per l'app. Ad esempio, è possibile presentare un dispositivo di scorrimento per consentire all'utente di scegliere l'opacità di un'immagine.

Tutte le celle personalizzate devono derivare da [`ViewCell`](xref:Xamarin.Forms.ViewCell), la stessa classe di base che tutta la cella predefinita dei tipi di utilizzo.

Questo è un esempio di una cella personalizzata:

![Esempio di cella personalizzata](tableview-images/custom-cell.png)

Nell'esempio seguente viene illustrato il codice XAML usato per [`TableView`](xref:Xamarin.Forms.TableView) creare negli screenshot precedenti:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoTableView.TablePage"
             Title="TableView">
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
</ContentPage>
```

Il codice C# equivalente è:

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() { Source = "bulb.png"});
layout.Children.Add (new Label()
{
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label ()
{
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot ()
{
    new TableSection("Getting Started")
    {
        new ViewCell() {View = layout}
    }
};
Content = table;
```

[`TableView`](xref:Xamarin.Forms.TableView) L'elemento radice sotto [`TableRoot`](xref:Xamarin.Forms.TableRoot)è e c' `TableRoot`è un oggetto [`TableSection`](xref:Xamarin.Forms.TableSection) immediatamente sotto. L' [`ViewCell`](xref:Xamarin.Forms.ViewCell) oggetto viene definito direttamente `TableSection`sotto e [`StackLayout`](xref:Xamarin.Forms.StackLayout) viene utilizzato per gestire il layout della cella personalizzata, sebbene sia possibile utilizzare qualsiasi layout.

> [!NOTE]
> Diversamente da [`ListView`](xref:Xamarin.Forms.ListView), [`TableView`](xref:Xamarin.Forms.TableView) non richiede che le celle personalizzate (o qualsiasi) siano definite in un `ItemTemplate`oggetto.

## <a name="row-height"></a>Altezza riga

Il [`TableView`](xref:Xamarin.Forms.TableView) classe ha due proprietà che possono essere utilizzate per modificare l'altezza della riga di celle:

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) : imposta l'altezza di ogni riga a un `int`.
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) -righe hanno altezza diversa se impostato su `true`. Si noti che quando si imposta questa proprietà su `true`, altezze delle righe verrà automaticamente calcolati e applicate da xamarin. Forms.

Quando l'altezza del contenuto in una cella in una [`TableView`](xref:Xamarin.Forms.TableView) viene modificata, la riga in modo implicito l'altezza viene aggiornata in Android e Universal Windows Platform (UWP). Tuttavia, in iOS che deve essere costretto ad aggiornare impostando il [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) proprietà `true` e chiamando i [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) (metodo).

L'esempio XAML seguente viene illustrato un [`TableView`](xref:Xamarin.Forms.TableView) contenente una [`ViewCell`](xref:Xamarin.Forms.ViewCell):

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

Quando la [`ViewCell`](xref:Xamarin.Forms.ViewCell) toccando, il `OnViewCellTapped` viene eseguito il gestore eventi:

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

Il `OnViewCellTapped` gestore dell'evento Mostra o nasconde il secondo [`Label`](xref:Xamarin.Forms.Label) nel [`ViewCell`](xref:Xamarin.Forms.ViewCell)e aggiorna le dimensioni della cella in modo esplicito chiamando la [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) (metodo).

Le schermate seguenti illustrano la cella prima vengano utilizzate al momento:

![ViewCell prima di essere ridimensionato](tableview-images/cell-beforeresize.png)

Le schermate seguenti illustrano la cella dopo vengano utilizzate al momento:

![ViewCell dopo il ridimensionamento](tableview-images/cell-afterresize.png)

> [!IMPORTANT]
> È possibile sicuro di riduzione delle prestazioni se questa funzionalità è eccessivo.

## <a name="related-links"></a>Collegamenti correlati

- [TableView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)
