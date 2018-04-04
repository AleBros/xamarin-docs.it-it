---
title: TableView
description: Presentare il menu di scorrimento, impostazioni e i moduli di input.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: dc55f3fe70450c71b639cf33166720fc27d45a10
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="tableview"></a>TableView

[TableView](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) è una vista per la visualizzazione scorrevoli elenchi di dati o scelte in cui sono presenti righe che non condividono lo stesso modello. A differenza di [ListView](~/xamarin-forms/user-interface/listview/index.md), TableView non hanno il concetto di un `ItemsSource`, in modo che gli elementi devono essere aggiunti come elementi figlio manualmente.

Questa guida è costituita da nelle sezioni seguenti:

- **[Casi d'uso](#Use_Cases)**  &ndash; quando utilizzare TableView invece ListView o una vista personalizzata.
- **[Struttura TableView](#TableView_Structure)**  &ndash; la gerarchia di viste che è necessario all'interno di un TableView.
- **[Aspetto TableView](#TableView_Appearance)**  &ndash; le opzioni di personalizzazione per TableView.
- **[Celle predefinite](#Built-In_Cells)**  &ndash; le opzioni di cella predefiniti, inclusi [EntryCell](#EntryCell) e [SwitchCell](#SwitchCell).
- **[Le celle personalizzate](#Custom_Cells)**  &ndash; come effettuare la propria celle personalizzate.

![](tableview-images/tableview-all-sml.png "Esempio TableView")

<a name="Use_Cases" />

## <a name="use-cases"></a>Casi d'uso

TableView risulta utile quando:

- presentare un elenco di impostazioni,
- la raccolta dei dati in un form, o
- verranno visualizzati i dati presentati in modo diverso dalla riga per riga (ad esempio, numeri, le percentuali e immagini).

TableView gestisce lo scorrimento e la disposizione di righe nelle sezioni interessante, una necessità comune per gli scenari precedenti. Il `TableView` controllo utilizza ogni piattaforma sottostante vista equivalente, se disponibile, la creazione di un aspetto nativo per ogni piattaforma.

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>Struttura TableView

Gli elementi in un `TableView` sono organizzati in sezioni. Nella radice del `TableView` è il `TableRoot`, ovvero padre a una o più `TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

Ogni `TableSection` è costituito da un'intestazione e uno o più ViewCells. Ecco il `TableSection`del `Title` proprietà impostata su *"Anello"* nel costruttore:

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

- **Dati** &ndash; da usare durante la visualizzazione di voci di dati. Si noti che [ListView](~/xamarin-forms/user-interface/listview/index.md) potrebbe essere un'opzione migliore per lo scorrimento di elenchi di dati.
- **Modulo** &ndash; da utilizzare quando il TableView agisce come un Form.
- **Menu** &ndash; da usare durante la visualizzazione del menu di selezione.
- **Impostazioni** &ndash; da utilizzare quando viene visualizzato un elenco di impostazioni di configurazione.

Il `TableIntent` prescelto può influire sulle come `TableView` viene visualizzato in ogni piattaforma. Anche se sono presenti differenze non è chiaro, è consigliabile selezionare il `TableIntent` che corrisponde maggiormente a come si intende utilizzare la tabella.

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Celle predefinite

Xamarin. Forms include celle predefinite per la raccolta e visualizzazione di informazioni. Anche se ListView e TableView possono utilizzare tutte le celle stesso, sono più rilevanti per uno scenario TableView le operazioni seguenti:

- **SwitchCell** &ndash; per la presentazione e l'acquisizione di uno stato true/false, insieme a un'etichetta di testo.
- **EntryCell** &ndash; per la presentazione e l'acquisizione del testo.

Vedere [aspetto delle celle ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) per una descrizione dettagliata di [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) e [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/) è il controllo da utilizzare per la presentazione e l'acquisizione di un attivato/disattivato o `true` / `false` stato.

SwitchCells hanno una riga di testo da modificare e una proprietà di attivazione/disattivazione. Entrambe queste proprietà sono associabile.

- `Text` &ndash; testo da visualizzare accanto il commutatore.
- `On` &ndash; indica se l'opzione viene visualizzata come on o off.

Si noti che il `SwitchCell` espone il `OnChanged` evento, che consente di rispondere alle modifiche di stato della cella.

![](tableview-images/switch-cell.png "Esempio SwitchCell")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/) è utile quando è necessario visualizzare i dati di testo che l'utente può modificare. `EntryCell`s offrono le seguenti proprietà che possono essere personalizzate:

- `Keyboard` &ndash; La tastiera per visualizzare durante la modifica. Sono disponibili opzioni per elementi come valori numerici, posta elettronica, i numeri di telefono e così via. [Vedere la documentazione dell'API](http://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/).
- `Label` &ndash; Il testo dell'etichetta da visualizzare a destra del campo di immissione testo.
- `LabelColor` &ndash; Il colore del testo dell'etichetta.
- `Placeholder` &ndash; Testo da visualizzare nel campo di immissione quando è null o vuoto. Questo testo scomparirà quando inizia la voce di testo.
- `Text` &ndash; Il testo nel campo di immissione.
- `HorizontalTextAlignment` &ndash; L'allineamento orizzontale del testo. Può essere allineato centro, a sinistra o destra. [Vedere la documentazione dell'API](http://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/).

Si noti che `EntryCell` espone il `Completed` evento, viene generato quando l'utente preme 'done' sulla tastiera durante la modifica del testo.

![](tableview-images/entry-cell.png "Esempio EntryCell")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Celle personalizzate
Quando le celle predefinite non sono sufficienti, è possono utilizzare celle personalizzate per consentire di acquisire i dati in modo appropriato per l'app. Ad esempio, si desideri presentare un dispositivo di scorrimento per consentire all'utente di scegliere l'opacità di un'immagine.

Tutte le celle personalizzate devono derivare da [ `ViewCell` ](http://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), la stessa classe di base che tutti i della cella predefinita tipi di utilizzo.

Questo è un esempio di una cella personalizzata:

![](tableview-images/custom-cell.png "Esempio di cella personalizzato")

### <a name="xaml"></a>XAML
Il codice XAML per creare il layout precedente è inferiore:

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

Esegue il codice XAML precedente molto. Di seguito suddividerla:

- L'elemento radice nel `TableView` è il `TableRoot`.
- È presente un `TableSection` immediatamente sotto il `TableRoot`.
- Il `ViewCell` è definito direttamente nella sezione della tabella. A differenza di `ListView`, `TableView` non richiede tale personalizzato (o qualsiasi) le celle vengono definite un `ItemTemplate`.
- Un StackLayout viene utilizzato per gestire il layout della cella personalizzato. Impossibile utilizzare qualsiasi layout.

### <a name="cnum"></a>C&num;

Poiché `TableView` funziona con dati statici o dati che sono stati modificati manualmente, non è il concetto di un modello di elemento. Invece, le celle personalizzate possono essere manualmente create e inserite nella tabella. Si noti che la cella la tecnica di creazione di un oggetto personalizzato che eredita da `ViewCell`, quindi aggiungerlo al `TableView` come sarebbe una cella predefinita, è anche supportato.
Di seguito è riportato il codice c# per eseguire il layout precedente:

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

C# sopra sta effettuando una grande quantità. Di seguito suddividerla:

- L'elemento radice nel `TableView` è il `TableRoot`.
- È presente un `TableSection` immediatamente sotto il `TableRoot`.
- Il `ViewCell` è definito direttamente nella sezione della tabella. A differenza di `ListView`, `TableView` non richiede tale personalizzato (o qualsiasi) le celle vengono definite un `ItemTemplate`.
- Un StackLayout viene utilizzato per gestire il layout della cella personalizzato. Impossibile utilizzare qualsiasi layout.

Si noti che non viene mai definita una classe per la cella personalizzata. Invece di `ViewCell`della proprietà di visualizzazione è impostata per una particolare istanza di `ViewCell`.



## <a name="related-links"></a>Collegamenti correlati

- [TableView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)
