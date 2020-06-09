---
title: " Xamarin.Forms TableView" Description: "questo articolo spiega come usare la Xamarin.Forms classe Tableview per presentare i menu di scorrimento, le impostazioni e i moduli di input nelle applicazioni".
ms. prod: Novell MS. AssetID: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 09/25/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-tableview"></a>Xamarin.FormsTableView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView)visualizzazione per la visualizzazione di elenchi scorrevoli di dati o opzioni in cui sono presenti righe che non condividono lo stesso modello. A differenza di [ListView](~/xamarin-forms/user-interface/listview/index.md), non `TableView` ha il concetto di `ItemsSource` , pertanto gli elementi devono essere aggiunti manualmente come elementi figlio.

![Esempio di TableView](tableview-images/tableview-all-sml.png)

## <a name="use-cases"></a>Casi d'uso

[`TableView`](xref:Xamarin.Forms.TableView)è utile nei casi seguenti:

- presentazione di un elenco di impostazioni
- raccolta di dati in un form o
- Visualizzazione dei dati presentati in modo diverso da riga a riga (ad esempio numeri, percentuali e immagini).

[`TableView`](xref:Xamarin.Forms.TableView)gestisce lo scorrimento e il layout delle righe nelle sezioni attraenti, una necessità comune per gli scenari precedenti. Il `TableView` controllo Usa la visualizzazione equivalente sottostante di ogni piattaforma, se disponibile, creando un aspetto nativo per ogni piattaforma.

## <a name="structure"></a>Struttura

Gli elementi in un oggetto [`TableView`](xref:Xamarin.Forms.TableView) sono organizzati in sezioni. Alla radice di `TableView` è [`TableRoot`](xref:Xamarin.Forms.TableRoot) , che è padre di una o più [`TableSection`](xref:Xamarin.Forms.TableSection) istanze di. Ogni [`TableSection`](xref:Xamarin.Forms.TableSection) è costituito da un'intestazione e da una o più [`ViewCell`](xref:Xamarin.Forms.ViewCell) istanze:

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

Il codice C# equivalente è il seguente:

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

## <a name="appearance"></a>Aspetto

[`TableView`](xref:Xamarin.Forms.TableView)espone la [`Intent`](xref:Xamarin.Forms.TableView.Intent) proprietà, che può essere impostata su uno dei [`TableIntent`](xref:Xamarin.Forms.TableIntent) membri dell'enumerazione:

- `Data`: da utilizzare per la visualizzazione di voci di dati. Si noti che [ListView](~/xamarin-forms/user-interface/listview/index.md) può essere un'opzione migliore per lo scorrimento degli elenchi di dati.
- `Form`: da usare quando TableView funge da form.
- `Menu`: da usare quando si presenta un menu di selezioni.
- `Settings`: da usare quando si visualizza un elenco di impostazioni di configurazione.

Il [`TableIntent`](xref:Xamarin.Forms.TableIntent) valore scelto può influisca sul modo [`TableView`](xref:Xamarin.Forms.TableView) in cui viene visualizzato in ogni piattaforma. Anche se non sono presenti differenze chiare, è consigliabile selezionare l'oggetto `TableIntent` che corrisponde maggiormente al modo in cui si intende usare la tabella.

Inoltre, il colore del testo visualizzato per ogni oggetto [`TableSection`](xref:Xamarin.Forms.TableSection) può essere modificato impostando la `TextColor` proprietà su un oggetto [`Color`](xref:Xamarin.Forms.Color) .

## <a name="built-in-cells"></a>Celle predefinite

Xamarin.Formsviene fornita con celle predefinite per la raccolta e la visualizzazione di informazioni. Sebbene [`ListView`](xref:Xamarin.Forms.ListView) e [`TableView`](xref:Xamarin.Forms.TableView) possano utilizzare tutte le stesse celle, [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) e [`EntryCell`](xref:Xamarin.Forms.EntryCell) sono le più rilevanti per uno `TableView` scenario.

Per una descrizione dettagliata di [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) e [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell), vedere [aspetto della cella ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) .

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)è il controllo da utilizzare per la presentazione e l'acquisizione di uno stato on/off o `true` / `false` . Questo file definisce le proprietà seguenti:

- `Text`: testo da visualizzare accanto all'opzione.
- `On`: indica se l'opzione viene visualizzata come attivata o disattivata.
- `OnColor`: oggetto [`Color`](xref:Xamarin.Forms.Color) dell'opzione quando si trova in in posizione.

Tutte queste proprietà sono associabili.

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)espone anche l' `OnChanged` evento, consentendo di rispondere alle modifiche nello stato della cella.

![Esempio di SwitchCell](tableview-images/switch-cell.png)

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell)è utile quando è necessario visualizzare dati di testo che l'utente può modificare. Questo file definisce le proprietà seguenti:

- `Keyboard`: La tastiera da visualizzare durante la modifica. Sono disponibili opzioni per elementi come valori numerici, messaggi di posta elettronica, numeri di telefono e così via. [vedere la documentazione API](xref:Xamarin.Forms.Keyboard).
- `Label`: Testo dell'etichetta da visualizzare a sinistra del campo di immissione di testo.
- `LabelColor`: Colore del testo dell'etichetta.
- `Placeholder`: Testo da visualizzare nel campo di immissione quando è null o vuoto. Questo testo scompare quando inizia la voce di testo.
- `Text`: Il testo nel campo di immissione.
- `HorizontalTextAlignment`: Allineamento orizzontale del testo. I valori sono allineati al centro, a sinistra o a destra. [Vedere la documentazione dell'API](xref:Xamarin.Forms.TextAlignment).
- `VerticalTextAlignment`: Allineamento verticale del testo. I valori sono `Start` , `Center` o `End` .

[`EntryCell`](xref:Xamarin.Forms.EntryCell)espone anche l' `Completed` evento, che viene generato quando l'utente preme il pulsante "fine" sulla tastiera durante la modifica del testo.

![Esempio di EntryCell](tableview-images/entry-cell.png)

## <a name="custom-cells"></a>Celle personalizzate

Quando le celle predefinite non sono sufficienti, le celle personalizzate possono essere usate per presentare e acquisire i dati nel modo più appropriato per l'app. È ad esempio possibile che si desideri presentare un dispositivo di scorrimento per consentire a un utente di scegliere l'opacità di un'immagine.

Tutte le celle personalizzate devono derivare da [`ViewCell`](xref:Xamarin.Forms.ViewCell) , la stessa classe di base utilizzata da tutti i tipi di cella incorporati.

Di seguito è riportato un esempio di una cella personalizzata:

![Esempio di cella personalizzata](tableview-images/custom-cell.png)

Nell'esempio seguente viene illustrato il codice XAML usato per creare [`TableView`](xref:Xamarin.Forms.TableView) negli screenshot precedenti:

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

Il codice C# equivalente è il seguente:

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

L'elemento radice sotto [`TableView`](xref:Xamarin.Forms.TableView) è [`TableRoot`](xref:Xamarin.Forms.TableRoot) e c'è un oggetto [`TableSection`](xref:Xamarin.Forms.TableSection) immediatamente sotto `TableRoot` . L'oggetto [`ViewCell`](xref:Xamarin.Forms.ViewCell) viene definito direttamente sotto `TableSection` e [`StackLayout`](xref:Xamarin.Forms.StackLayout) viene utilizzato per gestire il layout della cella personalizzata, sebbene sia possibile utilizzare qualsiasi layout.

> [!NOTE]
> Diversamente da [`ListView`](xref:Xamarin.Forms.ListView) , [`TableView`](xref:Xamarin.Forms.TableView) non richiede che le celle personalizzate (o qualsiasi) siano definite in un oggetto `ItemTemplate` .

## <a name="row-height"></a>Altezza riga

La [`TableView`](xref:Xamarin.Forms.TableView) classe dispone di due proprietà che possono essere utilizzate per modificare l'altezza di riga delle celle:

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight): imposta l'altezza di ogni riga su un oggetto `int` .
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows): le righe presentano altezze variabili se impostate su `true` . Si noti che quando si imposta questa proprietà su `true` , le altezze delle righe verranno calcolate e applicate automaticamente da Xamarin.Forms .

Quando viene modificata l'altezza del contenuto in una cella in un oggetto [`TableView`](xref:Xamarin.Forms.TableView) , l'altezza della riga viene aggiornata in modo implicito in Android e nel piattaforma UWP (Universal Windows Platform) (UWP). Tuttavia, in iOS è necessario forzare l'aggiornamento impostando la [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) proprietà su `true` e chiamando il [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) metodo.

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`TableView`](xref:Xamarin.Forms.TableView) che contiene un oggetto [`ViewCell`](xref:Xamarin.Forms.ViewCell) :

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

Quando [`ViewCell`](xref:Xamarin.Forms.ViewCell) viene toccato, viene eseguito il `OnViewCellTapped` gestore eventi:

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

Il `OnViewCellTapped` gestore eventi Mostra o nasconde il secondo [`Label`](xref:Xamarin.Forms.Label) nell'oggetto [`ViewCell`](xref:Xamarin.Forms.ViewCell) e aggiorna in modo esplicito le dimensioni della cella chiamando il [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) metodo.

Gli screenshot seguenti mostrano la cella prima di essere toccata:

![ViewCell prima di essere ridimensionato](tableview-images/cell-beforeresize.png)

Le schermate seguenti mostrano la cella dopo il tocco:

![ViewCell dopo il ridimensionamento](tableview-images/cell-afterresize.png)

> [!IMPORTANT]
> Se questa funzionalità viene sovrautilizzata, esiste una forte possibilità di riduzione delle prestazioni.

## <a name="related-links"></a>Collegamenti correlati

- [TableView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)
