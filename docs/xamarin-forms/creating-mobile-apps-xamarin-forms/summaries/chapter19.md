---
title: Riepilogo del capitolo 19. Viste di raccolta
description: 'Creazione di app per dispositivi mobili con Novell. Forms: riepilogo del capitolo 19. Viste di raccolta'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bffbd2dec4a8494723597ba6e0f0af69e57f3718
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032858"
---
# <a name="summary-of-chapter-19-collection-views"></a>Riepilogo del capitolo 19. Viste di raccolta

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui Novell. Forms è divergente rispetto al materiale presentato nel libro.

Novell. Forms definisce tre visualizzazioni che gestiscono le raccolte e visualizzano i relativi elementi:

- [`Picker`](xref:Xamarin.Forms.Picker) è un elenco relativamente breve di elementi stringa che consente all'utente di sceglierne uno
- [`ListView`](xref:Xamarin.Forms.ListView) è spesso un lungo elenco di elementi in genere dello stesso tipo e della stessa formattazione, consentendo inoltre all'utente di scegliere uno
- [`TableView`](xref:Xamarin.Forms.TableView) è una raccolta di *celle* (in genere di diversi tipi e aspetto) per visualizzare i dati o gestire l'input dell'utente

Per le applicazioni MVVM è comune utilizzare il `ListView` per visualizzare una raccolta selezionabile di oggetti.

## <a name="program-options-with-picker"></a>Opzioni di programma con selezione

Il [`Picker`](xref:Xamarin.Forms.Picker) è una scelta ottimale quando è necessario consentire all'utente di scegliere un'opzione tra un elenco relativamente breve di elementi di `string`.

### <a name="the-picker-and-event-handling"></a>Selezione e gestione degli eventi

Nell'esempio [**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) viene illustrato come utilizzare XAML per impostare la proprietà `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) e aggiungere elementi `string` alla raccolta [`Items`](xref:Xamarin.Forms.Picker.Items) . Quando l'utente seleziona il `Picker`, Visualizza gli elementi nella raccolta `Items` in modo dipendente dalla piattaforma.

L'evento [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) indica quando l'utente ha selezionato un elemento. La proprietà [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) in base zero indica quindi l'elemento selezionato. Se non è selezionato alcun elemento, `SelectedIndex` è uguale a &ndash;1.

È anche possibile usare `SelectedIndex` per inizializzare l'elemento selezionato, ma deve essere impostato dopo il riempimento della raccolta di `Items`. In XAML questo significa che probabilmente verrà usato un elemento Property per impostare `SelectedIndex`.

### <a name="data-binding-the-picker"></a>Associazione dati selezione

La proprietà `SelectedIndex` è supportata da una proprietà associabile, ma `Items` non lo è, pertanto l'utilizzo di data binding con `Picker` è difficile. Una soluzione consiste nell'usare il `Picker` in combinazione con un [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) , ad esempio quello nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . L'oggetto [**Pickering**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) ne illustra il funzionamento.

> [!NOTE] 
> Il `Picker` Novell. Forms include ora `ItemsSource` e `SelectedItem` proprietà che supportano data binding. Vedere [selezione](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Rendering dei dati con ListView

Il [`ListView`](xref:Xamarin.Forms.ListView) è l'unica classe che deriva da [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) da cui eredita le proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) e [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) .

`ItemsSource` è di tipo `IEnumerable` ma è `null` per impostazione predefinita e deve essere inizializzato in modo esplicito o (più comunemente) impostato su una raccolta tramite un data binding. Gli elementi in questa raccolta possono essere di qualsiasi tipo.

`ListView` definisce una proprietà [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) impostata su uno degli elementi nella raccolta `ItemsSource` o `null` se non è selezionato alcun elemento. `ListView` genera l'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) quando viene selezionato un nuovo elemento.

### <a name="collections-and-selections"></a>Raccolte e selezioni

L'esempio [**ListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) compila un `ListView` con 17 `Color` valori in una raccolta di `List<Color>`. Gli elementi sono selezionabili, ma per impostazione predefinita vengono visualizzati con le relative rappresentazioni di `ToString` non attraenti. Molti esempi in questo capitolo illustrano come correggere la visualizzazione e renderla più interessante a seconda delle esigenze.

### <a name="the-row-separator"></a>Separatore di riga

Nelle visualizzazioni iOS e Android, una linea sottile separa le righe. È possibile controllare questa operazione con le proprietà [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) e [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor) . `SeparatorVisibility` proprietà è di tipo [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility), un'enumerazione con due membri:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), l'impostazione predefinita
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>Associazione dati dell'elemento selezionato

La proprietà `SelectedItem` è supportata da una proprietà associabile, quindi può essere l'origine o la destinazione di un data binding. Il `BindingMode` predefinito è `OneWayToSource`, ma in genere è la destinazione di un data binding bidirezionale, in particolare negli scenari MVVM. Nell'esempio [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) viene illustrato questo tipo di associazione.

### <a name="the-observablecollection-difference"></a>Differenza ObservableCollection

L'esempio [**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) imposta la proprietà `ItemsSource` di un `ListView` su una raccolta di `List<DateTime>` e quindi aggiunge progressivamente un nuovo oggetto `DateTime` alla raccolta ogni secondo usando un timer.

Tuttavia, il `ListView` non viene automaticamente aggiornato perché la raccolta di `List<T>` non dispone di un meccanismo di notifica per indicare quando gli elementi vengono aggiunti o rimossi dalla raccolta.

Una classe molto migliore da usare in questi scenari è [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) definita nello spazio dei nomi `System.Collections.ObjectModel`. Questa classe implementa l'interfaccia [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) e, di conseguenza, genera un evento [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) quando gli elementi vengono aggiunti o rimossi dalla raccolta oppure quando vengono sostituiti o spostati all'interno della raccolta. Quando il `ListView` rileva internamente che una classe che implementa `INotifyCollectionChanged` è stata impostata sulla relativa proprietà `ItemsSource`, collega un gestore all'evento `CollectionChanged` e ne aggiorna la visualizzazione quando la raccolta viene modificata.

Nell'esempio [**ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) viene illustrato l'utilizzo di `ObservableCollection`.

### <a name="templates-and-cells"></a>Modelli e celle

Per impostazione predefinita, un `ListView` Visualizza gli elementi nella raccolta usando il metodo `ToString` di ogni elemento. Un approccio migliore prevede la definizione di un modello per la visualizzazione degli elementi.

Per provare questa funzionalità, è possibile usare la classe [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Questa classe definisce una proprietà `All` statica di tipo `IList<NamedColor>` contenente 141 `NamedColor` oggetti corrispondenti ai campi pubblici della struttura `Color`.

L'esempio [**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) imposta la `ItemsSource` di un `ListView` a questa proprietà `NamedColor.All`, ma vengono visualizzati solo i nomi di classe completi degli oggetti `NamedColor`.

`ListView` necessita di un modello per visualizzare questi elementi. Nel codice è possibile impostare la proprietà [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) definita da `ItemsView<TVisual>` su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) usando il [Costruttore`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) che fa riferimento a un derivato della classe [`Cell`](xref:Xamarin.Forms.Cell) . `Cell` ha cinque derivati:

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; contiene due visualizzazioni `Label` (concettualmente)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; aggiunge una vista `Image` per `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; contiene una visualizzazione `Entry` con `Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; contiene un `Switch` con `Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; può essere qualsiasi `View` (probabilmente con elementi figlio)

Chiamare quindi [`SetValue`](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) e [`SetBinding`](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) sull'oggetto `DataTemplate` per associare i valori alle proprietà del `Cell` o per impostare le associazioni dati nelle proprietà di `Cell` che fanno riferimento alle proprietà degli elementi nella raccolta di `ItemsSource`. Questa operazione è illustrata nell'esempio [**TextCellListCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) .

Poiché ogni elemento viene visualizzato dalla `ListView`, viene costruita una piccola struttura ad albero visuale dal modello e vengono stabilite associazioni dati tra l'elemento e le proprietà degli elementi in questa struttura ad albero visuale. È possibile ottenere un'idea di questo processo installando gestori per gli eventi [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) e [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) del `ListView`oppure usando un [costruttore di`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) alternativo che usa una funzione chiamata ogni volta che la struttura ad albero visuale di un elemento deve da creare.

[**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) Mostra un programma identico dal punto di vista funzionale, interamente in XAML. Un tag `DataTemplate` viene impostato sulla proprietà `ItemTemplate` della `ListView`, quindi il `TextCell` viene impostato sul `DataTemplate`. Le associazioni alle proprietà degli elementi della raccolta sono impostate direttamente sul [`Text`](xref:Xamarin.Forms.TextCell.Text) e [`Detail`](xref:Xamarin.Forms.TextCell.Detail) proprietà del `TextCell`.

### <a name="custom-cells"></a>Celle personalizzate

In XAML è possibile impostare un [`ViewCell`](xref:Xamarin.Forms.ViewCell) sul `DataTemplate` e quindi definire una struttura ad albero visuale personalizzata come proprietà [`View`](xref:Xamarin.Forms.ViewCell.View) di `ViewCell`. (`View` è la proprietà Content di `ViewCell` in modo che i tag di `ViewCell.View` non siano necessari). Questa tecnica è illustrata nell'esempio [**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) :

[![Schermata tripla dell'elenco colori con nome personalizzato](images/ch19fg11-small.png "Elenco colori con nome personalizzato")](images/ch19fg11-large.png#lightbox "Elenco colori con nome personalizzato")

Ottenere il diritto di ridimensionamento per tutte le piattaforme può risultare complesso. La proprietà [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight) è utile, ma in alcuni casi è opportuno ricorrere alla proprietà [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) , che è meno efficiente, ma forza la `ListView` a ridimensionare le righe. Per iOS e Android, è necessario usare una di queste due proprietà per ottenere il dimensionamento corretto delle righe.

### <a name="grouping-the-listview-items"></a>Raggruppamento degli elementi di ListView

`ListView` supporta il raggruppamento di elementi e lo spostamento tra i gruppi. È necessario impostare la proprietà `ItemsSource` su una raccolta di raccolte: l'oggetto che `ItemsSource` è impostato su deve implementare `IEnumerable`e ogni elemento nella raccolta deve implementare anche `IEnumerable`. Ogni gruppo deve includere due proprietà: una descrizione di testo del gruppo e un'abbreviazione di tre lettere.

La classe [`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) crea sette gruppi di oggetti `NamedColor`. Nell'esempio [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) viene illustrato come utilizzare questi gruppi con la proprietà [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) di `ListView` impostata su `true`e le proprietà [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) e [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) associate alle proprietà in ogni gruppo.

### <a name="custom-group-headers"></a>Intestazioni di gruppo personalizzate

È possibile creare intestazioni personalizzate per i gruppi di `ListView` sostituendo la proprietà `GroupDisplayBinding` con la [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) la definizione di un modello per le intestazioni.

### <a name="listview-and-interactivity"></a>ListView e interattività

In genere, un'applicazione ottiene l'interazione dell'utente con un `ListView` associando un gestore all'evento `ItemSelected` o [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) oppure impostando un data binding nella proprietà `SelectedItem`. Alcuni tipi di celle (`EntryCell` e `SwitchCell`), tuttavia, consentono l'interazione dell'utente ed è anche possibile creare celle personalizzate che interagiscono con l'utente. [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crea 100 istanze di [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) e consente all'utente di modificare ogni colore usando un trio di `Slider` elementi. Il programma usa anche il [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) in [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView e MVVM

`ListView` svolge un ruolo importante negli scenari MVVM. Ogni volta che una raccolta di `IEnumerable` è presente in un ViewModel, viene spesso associata a una `ListView`. Inoltre, gli elementi della raccolta spesso implementano `INotifyPropertyChanged` per eseguire il binding con le proprietà in un modello.

### <a name="a-collection-of-viewmodels"></a>Raccolta di ViewModel

Per esplorare questo problema, la libreria [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) crea diverse classi basate su un [file di dati XML](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e immagini di studenti fittizi in questa scuola fittizia.

La classe [`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) deriva da [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). La classe [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) è una raccolta di oggetti `Student` e deriva anche da `ViewModelBase`. Il [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) Scarica il file XML e assembla tutti gli oggetti.

Il programma [**students**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) usa un `ImageCell` per visualizzare gli studenti e le relative immagini in una `ListView`:

[![Schermata tripla dell'elenco degli studenti](images/ch19fg18-small.png "Elenco degli studenti")](images/ch19fg18-large.png#lightbox "Elenco degli studenti")

L'esempio [**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) aggiunge una proprietà [`Header`](xref:Xamarin.Forms.ListView.Header) ma viene visualizzata solo in Android.

### <a name="selection-and-the-binding-context"></a>Selezione e contesto di associazione

Il programma [**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) associa la `BindingContext` di una `StackLayout` alla proprietà `SelectedItem` della `ListView`. Ciò consente al programma di visualizzare informazioni dettagliate sullo studente selezionato.

### <a name="context-menus"></a>Menu di scelta rapida

Una cella può definire un menu di scelta rapida implementato in modo specifico per la piattaforma. Per creare questo menu, aggiungere [`MenuItem`](xref:Xamarin.Forms.MenuItem) oggetti alla proprietà [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) dell'`Cell`.

`MenuItem` definisce cinque proprietà:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) di tipo `string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) di tipo `FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) di tipo `bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command) di tipo `ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) di tipo `object`

Le proprietà `Command` e `CommandParameter` implicano che il ViewModel per ogni elemento contiene metodi per eseguire i comandi di menu desiderati. Negli scenari non MVVM `MenuItem` definisce anche un evento di [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) .

[**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) illustra questa tecnica. La proprietà `Command` di ogni `MenuItem` è associata a una proprietà di tipo `ICommand` nella classe `Student`. Impostare la proprietà `IsDestructive` su `true` per un `MenuItem` che rimuove o Elimina l'oggetto selezionato.

### <a name="varying-the-visuals"></a>Variazione degli oggetti visivi

In alcuni casi si desiderano lievi variazioni negli oggetti visivi degli elementi nell'`ListView` in base a una proprietà. Se, ad esempio, la media dei voti di un studente scende sotto 2,0, nell'esempio [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) viene visualizzato il nome di tale studente in rosso.
Questa operazione viene eseguita tramite l'uso di un convertitore di valori di binding, [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs), nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

### <a name="refreshing-the-content"></a>Aggiornamento del contenuto

Il `ListView` supporta un movimento a discesa per l'aggiornamento dei dati. Il programma deve impostare la proprietà [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) su `true` per abilitarla. Il `ListView` risponde al gesto a discesa impostando la relativa proprietà [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) su `true`e generando l'evento [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) e (per gli scenari MVVM) chiamando il metodo `Execute` della relativa proprietà [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) .

Il codice che gestisce l'evento `Refresh` o l'`RefreshCommand` può quindi aggiornare i dati visualizzati dal `ListView` e imposta di nuovo `IsRefreshing` su `false`.

Nell'esempio [**feed RSS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) viene illustrato l'utilizzo di un [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) che implementa `RefreshCommand` e `IsRefreshing` proprietà per data binding.

## <a name="the-tableview-and-its-intents"></a>TableView e i relativi Intent

Mentre il `ListView` Visualizza in genere più istanze dello stesso tipo, il [`TableView`](xref:Xamarin.Forms.TableView) è in genere incentrato sulla fornitura di un'interfaccia utente per più proprietà di vari tipi. Ogni elemento è associato alla propria [`Cell`](xref:Xamarin.Forms.Cell) derivata per visualizzare la proprietà o fornire un'interfaccia utente.

### <a name="properties-and-hierarchies"></a>Proprietà e gerarchie

`TableView` definisce solo quattro proprietà:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent) di tipo [`TableIntent`](xref:Xamarin.Forms.TableIntent), enumerazione
- [`Root`](xref:Xamarin.Forms.TableView.Root) di tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot), la proprietà Content di `TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) di tipo `int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) di tipo `bool`

L'enumerazione `TableIntent` indica come si intende utilizzare l'`TableView`:

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Questi membri suggeriscono anche alcuni usi per la `TableView`.

Per la definizione di una tabella sono necessarie diverse altre classi:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase) è una classe astratta che deriva da `BindableObject` e definisce una proprietà [`Title`](xref:Xamarin.Forms.TableSectionBase.Title)

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1) è una classe astratta che deriva da `TableSectionBase` e implementa `IList<T>` e `INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection) deriva da `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot) deriva da `TableSectionBase<TableSection>`

In breve, `TableView` dispone di una proprietà `Root` impostata su un oggetto `TableRoot`, ovvero una raccolta di oggetti `TableSection`, ognuno dei quali è una raccolta di oggetti `Cell`. Una tabella include più sezioni e ogni sezione dispone di più celle. La tabella stessa può avere un titolo e ogni sezione può avere un titolo. Sebbene `TableView` usi `Cell` derivati, non usa `DataTemplate`.

### <a name="a-prosaic-form"></a>Formato prosaico

Nell'esempio [**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) viene definito un modello di visualizzazione [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) , un'istanza di che diventa l'`BindingContext` della `TableView`. Ogni `Cell` derivata nel `TableSection` può quindi avere associazioni alle proprietà della classe `PersonalInformation`.

### <a name="custom-cells"></a>Celle personalizzate

L'esempio [**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) si espande in **EntryForm**. La classe [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) include una proprietà booleana che regola l'applicabilità di due proprietà aggiuntive. Per queste due proprietà aggiuntive, il programma usa una `PickerCell` personalizzata basata su [PickerCell. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) e [PickerCell.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

Sebbene le proprietà `IsEnabled` dei due elementi `PickerCell` siano associate alla proprietà booleana in `ProgrammerInformation`, questa tecnica non sembra funzionare, il che richiede l'esempio successivo.

### <a name="conditional-sections"></a>Sezioni condizionali

L'esempio [**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) inserisce i due elementi che sono condizionali sulla selezione dell'elemento booleano in un `TableSection`separato. Il file code-behind rimuove questa sezione dal `TableView` o la aggiunge di nuovo in base alla proprietà booleana.

### <a name="a-tableview-menu"></a>Menu TableView

Un altro uso di un `TableView` è un menu. Nell'esempio [**oggetti MenuCommand**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) viene illustrato un menu che consente di spostare un piccolo `BoxView` sullo schermo.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 19 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Esempi del capitolo 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Selezione](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
