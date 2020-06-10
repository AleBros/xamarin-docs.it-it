---
title: "Riepilogo del capitolo 19. Viste raccolta "Descrizione:" creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 19. Viste della raccolta "ms. prod: Novell MS. Technology: Novell-Forms ms. AssetID: 0AEC3A5C-586E-4D0F-9895-67E99A053A79 Author: davidbritch ms. Author: dabritch ms. Date: 07/18/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-19-collection-views"></a>Riepilogo del capitolo 19. Viste di raccolta

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> Le note in questa pagina indicano Xamarin.Forms le aree in cui è stato divergente rispetto al materiale presentato nel libro.

Xamarin.Formsdefinisce tre visualizzazioni che gestiscono le raccolte e visualizzano gli elementi:

- [`Picker`](xref:Xamarin.Forms.Picker)è un elenco relativamente breve di elementi stringa che consente all'utente di sceglierne uno
- [`ListView`](xref:Xamarin.Forms.ListView)è spesso un lungo elenco di elementi in genere dello stesso tipo e della stessa formattazione, consentendo inoltre all'utente di sceglierne uno
- [`TableView`](xref:Xamarin.Forms.TableView)è una raccolta di *celle* (in genere di diversi tipi e aspetto) per la visualizzazione dei dati o la gestione dell'input dell'utente

Per le applicazioni MVVM è comune utilizzare per `ListView` visualizzare una raccolta selezionabile di oggetti.

## <a name="program-options-with-picker"></a>Opzioni di programma con selezione

[`Picker`](xref:Xamarin.Forms.Picker)È una scelta ottimale quando è necessario consentire all'utente di scegliere un'opzione tra un elenco di elementi relativamente breve `string` .

### <a name="the-picker-and-event-handling"></a>Selezione e gestione degli eventi

Nell'esempio [**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) viene illustrato come utilizzare XAML per impostare la `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) proprietà e aggiungere `string` elementi alla [`Items`](xref:Xamarin.Forms.Picker.Items) raccolta. Quando l'utente seleziona `Picker` , Visualizza gli elementi nella `Items` raccolta in modo dipendente dalla piattaforma.

L' [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento indica quando l'utente ha selezionato un elemento. La proprietà in base zero [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) indica quindi l'elemento selezionato. Se non è selezionato alcun elemento, è `SelectedIndex` uguale a &ndash; 1.

È anche possibile usare `SelectedIndex` per inizializzare l'elemento selezionato, ma deve essere impostato dopo il `Items` riempimento della raccolta. In XAML questo significa che probabilmente verrà usato un elemento Property da impostare `SelectedIndex` .

### <a name="data-binding-the-picker"></a>Associazione dati selezione

La `SelectedIndex` proprietà è supportata da una proprietà associabile `Items` , ma non è, pertanto l'uso di data binding con un `Picker` è difficile. Una soluzione consiste nell'usare `Picker` in combinazione con un [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) , ad esempio quello nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . L'oggetto [**Pickering**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) ne illustra il funzionamento.

> [!NOTE] 
> Xamarin.Forms `Picker` Include ora le `ItemsSource` `SelectedItem` proprietà e che supportano data binding. Vedere [selezione](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Rendering dei dati con ListView

[`ListView`](xref:Xamarin.Forms.ListView)È l'unica classe che deriva da [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) dalla quale eredita le [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) proprietà e.

`ItemsSource`è di tipo, `IEnumerable` ma è `null` per impostazione predefinita e deve essere inizializzato in modo esplicito o (più comunemente) impostato su una raccolta tramite un data binding. Gli elementi in questa raccolta possono essere di qualsiasi tipo.

`ListView`definisce una [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) proprietà impostata su uno degli elementi della `ItemsSource` raccolta o `null` se non è selezionato alcun elemento. `ListView`genera l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento quando viene selezionato un nuovo elemento.

### <a name="collections-and-selections"></a>Raccolte e selezioni

L'esempio [**ListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) compila un oggetto `ListView` con 17 `Color` valori in una `List<Color>` raccolta. Gli elementi sono selezionabili, ma per impostazione predefinita vengono visualizzati con le relative `ToString` rappresentazioni non attraenti. Molti esempi in questo capitolo illustrano come correggere la visualizzazione e renderla più interessante a seconda delle esigenze.

### <a name="the-row-separator"></a>Separatore di riga

Nelle visualizzazioni iOS e Android, una linea sottile separa le righe. È possibile controllare questo oggetto con [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) le [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor) proprietà e. `SeparatorVisibility`Property è di tipo [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility) , un'enumerazione con due membri:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), impostazione predefinita
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>Associazione dati dell'elemento selezionato

La `SelectedItem` proprietà è supportata da una proprietà associabile, quindi può essere l'origine o la destinazione di un data binding. Il valore predefinito `BindingMode` è `OneWayToSource` , ma in genere è la destinazione di un data binding bidirezionale, in particolare negli scenari MVVM. Nell'esempio [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) viene illustrato questo tipo di associazione.

### <a name="the-observablecollection-difference"></a>Differenza ObservableCollection

L'esempio [**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) imposta la `ItemsSource` proprietà di un `ListView` oggetto su una `List<DateTime>` raccolta e quindi aggiunge progressivamente un nuovo `DateTime` oggetto alla raccolta ogni secondo usando un timer.

Tuttavia, `ListView` non si aggiorna automaticamente perché la `List<T>` raccolta non dispone di un meccanismo di notifica per indicare quando gli elementi vengono aggiunti o rimossi dalla raccolta.

Una classe molto migliore da usare in questi scenari è [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) definita nello `System.Collections.ObjectModel` spazio dei nomi. Questa classe implementa l' [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) interfaccia e genera quindi un [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) evento quando gli elementi vengono aggiunti o rimossi dalla raccolta oppure quando vengono sostituiti o spostati all'interno della raccolta. Quando l'oggetto `ListView` rileva internamente che una classe che implementa `INotifyCollectionChanged` è stata impostata sulla relativa `ItemsSource` proprietà, collega un gestore all' `CollectionChanged` evento e ne aggiorna la visualizzazione quando la raccolta viene modificata.

Nell'esempio [**ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) viene illustrato l'utilizzo di `ObservableCollection` .

### <a name="templates-and-cells"></a>Modelli e celle

Per impostazione predefinita, un oggetto `ListView` Visualizza gli elementi nella raccolta usando il metodo di ogni elemento `ToString` . Un approccio migliore prevede la definizione di un modello per la visualizzazione degli elementi.

Per provare questa funzionalità, è possibile usare la [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Questa classe definisce una `All` proprietà statica di tipo `IList<NamedColor>` che contiene `NamedColor` gli oggetti 141 corrispondenti ai campi pubblici della `Color` struttura.

L'esempio [**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) imposta la `ItemsSource` proprietà di un oggetto `ListView` su questa `NamedColor.All` proprietà, ma vengono visualizzati solo i nomi di classe completi degli `NamedColor` oggetti.

`ListView`richiede un modello per visualizzare questi elementi. Nel codice è possibile impostare la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) proprietà definita da `ItemsView<TVisual>` su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto usando il [ `DataTemplate` Costruttore](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) che fa riferimento a un derivato della [`Cell`](xref:Xamarin.Forms.Cell) classe. `Cell`ha cinque derivati:

- [`TextCell`](xref:Xamarin.Forms.TextCell)&mdash;contiene due `Label` visualizzazioni (in teoria)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)&mdash;aggiunge una `Image` vista a`TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)&mdash;contiene una `Entry` visualizzazione con un`Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)&mdash;contiene un oggetto `Switch` con un oggetto`Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell)&mdash;può essere qualsiasi `View` (probabilmente con elementi figlio)

Chiamare quindi [ `SetValue` ] (xrif: Xamarin.Forms . DataTemplate. SetValue ( Xamarin.Forms . BindableProperty, System. Object) e [ `SetBinding` ] (xrif: Xamarin.Forms . DataTemplate. SetBinding ( Xamarin.Forms . BindableProperty, Xamarin.Forms . BindingBase)) nell' `DataTemplate` oggetto per associare i valori alle `Cell` proprietà o per impostare associazioni dati nelle `Cell` proprietà che fanno riferimento alle proprietà degli elementi nella `ItemsSource` raccolta. Questa operazione è illustrata nell'esempio [**TextCellListCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) .

Poiché ogni elemento viene visualizzato da `ListView` , viene creata una piccola struttura ad albero visuale dal modello e vengono stabilite associazioni dati tra l'elemento e le proprietà degli elementi in questa struttura ad albero visuale. È possibile ottenere un'idea di questo processo installando gestori per gli [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) eventi e [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) di `ListView` oppure usando un [ `DataTemplate` Costruttore](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) alternativo che usa una funzione chiamata ogni volta che è necessario creare la struttura ad albero visuale di un elemento.

[**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) Mostra un programma identico dal punto di vista funzionale, interamente in XAML. Un `DataTemplate` tag viene impostato sulla `ItemTemplate` proprietà dell'oggetto `ListView` , quindi la proprietà `TextCell` viene impostata su `DataTemplate` . Le associazioni alle proprietà degli elementi della raccolta sono impostate direttamente sulle [`Text`](xref:Xamarin.Forms.TextCell.Text) [`Detail`](xref:Xamarin.Forms.TextCell.Detail) proprietà e di `TextCell` .

### <a name="custom-cells"></a>Celle personalizzate

In XAML è possibile impostare un oggetto [`ViewCell`](xref:Xamarin.Forms.ViewCell) su `DataTemplate` e quindi definire una struttura ad albero visuale personalizzata come [`View`](xref:Xamarin.Forms.ViewCell.View) proprietà di `ViewCell` . ( `View` è la proprietà di contenuto di `ViewCell` , quindi i `ViewCell.View` tag non sono necessari). Questa tecnica è illustrata nell'esempio [**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) :

[![Schermata tripla dell'elenco colori con nome personalizzato](images/ch19fg11-small.png "Elenco colori con nome personalizzato")](images/ch19fg11-large.png#lightbox "Elenco colori con nome personalizzato")

Ottenere il diritto di ridimensionamento per tutte le piattaforme può risultare complesso. La [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight) proprietà è utile, ma in alcuni casi è opportuno ricorrere alla [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) proprietà, che è meno efficiente ma impone `ListView` a di ridimensionare le righe. Per iOS e Android, è necessario usare una di queste due proprietà per ottenere il dimensionamento corretto delle righe.

### <a name="grouping-the-listview-items"></a>Raggruppamento degli elementi di ListView

`ListView`supporta il raggruppamento di elementi e lo spostamento tra i gruppi. La `ItemsSource` proprietà deve essere impostata su una raccolta di raccolte: l'oggetto `ItemsSource` impostato su deve implementare `IEnumerable` e ogni elemento nella raccolta deve implementare anche `IEnumerable` . Ogni gruppo deve includere due proprietà: una descrizione di testo del gruppo e un'abbreviazione di tre lettere.

La [`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) crea sette gruppi di `NamedColor` oggetti. Nell'esempio [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) viene illustrato come utilizzare questi gruppi con la [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) proprietà `ListView` impostata su `true` e le [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) proprietà e [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) associate alle proprietà in ogni gruppo.

### <a name="custom-group-headers"></a>Intestazioni di gruppo personalizzate

È possibile creare intestazioni personalizzate per i `ListView` gruppi sostituendo la `GroupDisplayBinding` proprietà con la definizione di [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) un modello per le intestazioni.

### <a name="listview-and-interactivity"></a>ListView e interattività

In genere, un'applicazione ottiene l'interazione dell'utente con un `ListView` associando un gestore `ItemSelected` all' [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) evento o oppure impostando un data binding sulla `SelectedItem` Proprietà. Tuttavia, alcuni tipi di cella ( `EntryCell` e `SwitchCell` ) consentono l'interazione dell'utente ed è anche possibile creare celle personalizzate che interagiscono con l'utente. [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crea 100 istanze di [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) e consente all'utente di modificare ogni colore usando un trio di `Slider` elementi. Il programma usa anche [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) in [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView e MVVM

`ListView`svolge un ruolo importante negli scenari MVVM. Ogni volta che una `IEnumerable` raccolta è presente in un ViewModel, viene spesso associata a un oggetto `ListView` . Inoltre, gli elementi della raccolta spesso implementano `INotifyPropertyChanged` per l'associazione alle proprietà in un modello.

### <a name="a-collection-of-viewmodels"></a>Raccolta di ViewModel

Per esplorare questo problema, la libreria [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) crea diverse classi basate su un [file di dati XML](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e immagini di studenti fittizi in questa scuola fittizia.

La [`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) classe deriva da [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs) . La [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) classe è una raccolta di `Student` oggetti e deriva anche da `ViewModelBase` . Il [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) Scarica il file XML e assembla tutti gli oggetti.

Il programma [**students**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) usa un oggetto `ImageCell` per visualizzare gli studenti e le relative immagini in un `ListView` :

[![Schermata tripla dell'elenco degli studenti](images/ch19fg18-small.png "Elenco degli studenti")](images/ch19fg18-large.png#lightbox "Elenco degli studenti")

L'esempio [**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) aggiunge una [`Header`](xref:Xamarin.Forms.ListView.Header) proprietà ma viene visualizzata solo in Android.

### <a name="selection-and-the-binding-context"></a>Selezione e contesto di associazione

Il programma [**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) associa la `BindingContext` proprietà di un oggetto `StackLayout` alla `SelectedItem` proprietà dell'oggetto `ListView` . Ciò consente al programma di visualizzare informazioni dettagliate sullo studente selezionato.

### <a name="context-menus"></a>Menu di scelta rapida

Una cella può definire un menu di scelta rapida implementato in modo specifico per la piattaforma. Per creare questo menu, aggiungere [`MenuItem`](xref:Xamarin.Forms.MenuItem) oggetti alla [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) proprietà di `Cell` .

`MenuItem`definisce cinque proprietà:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)di tipo`string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)di tipo`FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)di tipo`bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command)di tipo`ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)di tipo`object`

Le `Command` `CommandParameter` proprietà e implicano che il ViewModel per ogni elemento contiene metodi per eseguire i comandi di menu desiderati. Negli scenari non MVVM, `MenuItem` definisce anche un [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) evento.

[**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) illustra questa tecnica. La `Command` proprietà di ogni oggetto `MenuItem` è associata a una proprietà di tipo `ICommand` nella `Student` classe. Impostare la `IsDestructive` proprietà su `true` per un `MenuItem` oggetto che rimuove o Elimina l'oggetto selezionato.

### <a name="varying-the-visuals"></a>Variazione degli oggetti visivi

In alcuni casi si desiderano lievi variazioni negli oggetti visivi degli elementi in in `ListView` base a una proprietà. Se, ad esempio, la media dei voti di un studente scende sotto 2,0, nell'esempio [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) viene visualizzato il nome di tale studente in rosso.
Questa operazione viene eseguita tramite l'uso di un convertitore di valori di binding, [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs) , nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

### <a name="refreshing-the-content"></a>Aggiornamento del contenuto

`ListView`Supporta un movimento a discesa per l'aggiornamento dei dati. Il programma deve impostare la [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) proprietà su `true` per abilitarla. `ListView`Risponde al gesto a discesa impostando la relativa [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) proprietà su `true` e generando l' [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) evento e (per gli scenari MVVM) chiamando il `Execute` metodo della relativa [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) Proprietà.

Il codice che gestisce l' `Refresh` evento o `RefreshCommand` , eventualmente, aggiorna i dati visualizzati da `ListView` e imposta `IsRefreshing` di nuovo su `false` .

Nell'esempio [**feed RSS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) viene illustrato l'utilizzo di un oggetto [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) che implementa le `RefreshCommand` `IsRefreshing` proprietà e per data binding.

## <a name="the-tableview-and-its-intents"></a>TableView e i relativi Intent

Mentre `ListView` in vengono in genere visualizzate più istanze dello stesso tipo, [`TableView`](xref:Xamarin.Forms.TableView) è in genere incentrato sulla fornitura di un'interfaccia utente per più proprietà di vari tipi. Ogni elemento è associato al relativo [`Cell`](xref:Xamarin.Forms.Cell) derivato per la visualizzazione della proprietà o fornisce un'interfaccia utente.

### <a name="properties-and-hierarchies"></a>Proprietà e gerarchie

`TableView`definisce solo quattro proprietà:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent)di tipo [`TableIntent`](xref:Xamarin.Forms.TableIntent) , un'enumerazione
- [`Root`](xref:Xamarin.Forms.TableView.Root)di tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot) , la proprietà Content di`TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight)di tipo`int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)di tipo`bool`

L' `TableIntent` enumerazione indica come si intende usare `TableView` :

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Questi membri suggeriscono anche alcuni usi per il `TableView` .

Per la definizione di una tabella sono necessarie diverse altre classi:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase)è una classe astratta che deriva da `BindableObject` e definisce una [`Title`](xref:Xamarin.Forms.TableSectionBase.Title) Proprietà

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1)è una classe astratta che deriva da `TableSectionBase` e implementa `IList<T>` e`INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection)deriva da`TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot)deriva da`TableSectionBase<TableSection>`

In breve, `TableView` ha una `Root` proprietà impostata su un `TableRoot` oggetto, ovvero una raccolta di `TableSection` oggetti, ognuno dei quali è una raccolta di `Cell` oggetti. Una tabella include più sezioni e ogni sezione dispone di più celle. La tabella stessa può avere un titolo e ogni sezione può avere un titolo. Sebbene `TableView` usi i `Cell` derivati, non fa uso di `DataTemplate` .

### <a name="a-prosaic-form"></a>Formato prosaico

Nell'esempio [**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) viene definito un [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) modello di visualizzazione, un'istanza di che diventa il `BindingContext` di `TableView` . Ogni `Cell` derivato in `TableSection` può quindi avere associazioni alle proprietà della `PersonalInformation` classe.

### <a name="custom-cells"></a>Celle personalizzate

L'esempio [**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) si espande in **EntryForm**. La [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) classe include una proprietà booleana che regola l'applicabilità di due proprietà aggiuntive. Per queste due proprietà aggiuntive, il programma usa un oggetto personalizzato `PickerCell` basato su [PickerCell. xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) e [PickerCell.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

Sebbene le `IsEnabled` proprietà dei due `PickerCell` elementi siano associate alla proprietà booleana in `ProgrammerInformation` , questa tecnica non sembra funzionare, il che richiede l'esempio successivo.

### <a name="conditional-sections"></a>Sezioni condizionali

L'esempio [**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) inserisce i due elementi che sono condizionali sulla selezione dell'elemento booleano in un separato `TableSection` . Il file code-behind rimuove questa sezione dalla `TableView` o la aggiunge di nuovo in base alla proprietà booleana.

### <a name="a-tableview-menu"></a>Menu TableView

Un altro uso di un `TableView` è un menu. L'esempio [**oggetti MenuCommand**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) illustra un menu che consente di spostarsi leggermente sullo `BoxView` schermo.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 19 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Esempi del capitolo 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Selezione](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
