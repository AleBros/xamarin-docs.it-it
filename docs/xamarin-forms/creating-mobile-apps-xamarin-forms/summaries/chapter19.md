---
title: Riepilogo del capitolo 19. Visualizzazioni di raccolta
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 19. Visualizzazioni di raccolta'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: charlespetzold
ms.author: chape
ms.date: 07/18/2018
ms.openlocfilehash: 84d21738298360eb8829a569b152a131e6543709
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526780"
---
# <a name="summary-of-chapter-19-collection-views"></a>Riepilogo del capitolo 19. Visualizzazioni di raccolta

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

Xamarin. Forms definisce tre visualizzazioni che gestiscono le raccolte e visualizzano i relativi elementi:

- [`Picker`](xref:Xamarin.Forms.Picker) è un elenco di elementi di tipo stringa relativamente breve che consente all'utente di sceglierne uno
- [`ListView`](xref:Xamarin.Forms.ListView) è spesso un lungo elenco di elementi in genere dello stesso tipo e la formattazione, anche di consentire all'utente di scegliere uno
- [`TableView`](xref:Xamarin.Forms.TableView) è una raccolta di *celle* (in genere di vari tipi e gli aspetti) per visualizzare i dati o gestire l'input dell'utente

È comune nelle applicazioni MVVM da utilizzare per il `ListView` per visualizzare una raccolta di oggetti selezionabile.

## <a name="program-options-with-picker"></a>Opzioni di analisi con selezione

Il [ `Picker` ](xref:Xamarin.Forms.Picker) è una scelta ottimale quando è necessario consentire all'utente di scegliere un'opzione tra quelle per un elenco di relativamente breve `string` elementi.

### <a name="the-picker-and-event-handling"></a>La selezione e la gestione degli eventi

Il [ **PickerDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) esempio viene illustrato come utilizzare XAML per impostare il `Picker` [ `Title` ](xref:Xamarin.Forms.Picker.Title) proprietà e aggiungere `string` elementi per il [ `Items` ](xref:Xamarin.Forms.Picker.Items) raccolta. Quando l'utente seleziona il `Picker`, Visualizza gli elementi nel `Items` raccolta in modo dipendente dalla piattaforma.

Il [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) eventi indica quando l'utente ha selezionato un elemento. In base zero [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) proprietà indica quindi l'elemento selezionato. Se è selezionato alcun elemento, `SelectedIndex` è uguale a &ndash;1.

È anche possibile usare `SelectedIndex` per inizializzare l'elemento selezionato, ma deve essere impostato dopo la `Items` insieme viene compilato. In XAML, ciò significa che è probabile che userà un elemento di proprietà per impostare `SelectedIndex`.

### <a name="data-binding-the-picker"></a>La selezione di associazione dati

Il `SelectedIndex` proprietà è supportata da una proprietà associabile, ma `Items` non lo è, quindi l'uso del data binding con una `Picker` è difficile. Una soluzione consiste nell'usare la `Picker` in combinazione con un [ `ObjectToIndexConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) come quello usato nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria. Il [ **PickerBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) dimostra il funzionamento.

> [!NOTE] 
> Xamarin. Forms `Picker` ora include `ItemsSource` e `SelectedItem` proprietà che supportano il data binding. Visualizzare [selezione](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Il rendering dei dati con ListView

Il [ `ListView` ](xref:Xamarin.Forms.ListView) è l'unica classe che deriva da [ `ItemsView<TVisual>` ](xref:Xamarin.Forms.ItemsView`1) da cui eredita il [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) e [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) proprietà.

`ItemsSource` JE typu `IEnumerable` ma è `null` per impostazione predefinita e deve essere inizializzato in modo esplicito o impostare (in genere) a una raccolta tramite l'associazione dati. Gli elementi in questa raccolta possono essere di qualsiasi tipo.

`ListView` definisce un [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) proprietà che può essere impostata su uno degli elementi nel `ItemsSource` raccolta o `null` se è selezionato alcun elemento. `ListView` viene attivato il [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento quando viene selezionato un nuovo elemento.

### <a name="collections-and-selections"></a>Le raccolte e le selezioni

Il [ **ListViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) esempio riempimenti una `ListView` con 17 `Color` valori in un `List<Color>` raccolta. Gli elementi sono selezionabili, ma per impostazione predefinita vengono visualizzati con loro graficamente meno elegante `ToString` rappresentazioni. Per alcuni esempi in questo capitolo viene illustrato come correzione per la visualizzazione e renderlo altrettanto soddisfacenti in base alle esigenze.

### <a name="the-row-separator"></a>Il separatore di riga

In iOS e Android consente di visualizzare, una linea sottile separa le righe. È possibile controllare questo valore con il [ `SeparatorVisibility` ](xref:Xamarin.Forms.ListView.SeparatorVisibility) e [ `SeparatorColor` ](xref:Xamarin.Forms.ListView.SeparatorColor) proprietà. `SeparatorVisibility` proprietà JE typu [ `SeparatorVisibility` ](xref:Xamarin.Forms.SeparatorVisibility), un'enumerazione con due membri:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), l'impostazione predefinita
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>Data binding l'elemento selezionato

Il `SelectedItem` proprietà è supportata da una proprietà associabile, pertanto può essere l'origine o la destinazione di un data binding. Valore predefinito `BindingMode` è `OneWayToSource`, ma in genere si tratta della destinazione di un'associazione dati bidirezionale, in particolare negli scenari MVVM. Il [ **ListViewArray** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) esempio viene illustrato questo tipo di binding.

### <a name="the-observablecollection-difference"></a>La differenza di classe ObservableCollection

Il [ **ListViewLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) set di esempio il `ItemsSource` proprietà di un `ListView` per un `List<DateTime>` raccolta e progressivamente aggiunge quindi un nuovo `DateTime` oggetto alla raccolta ogni secondo usando un timer.

Tuttavia, il `ListView` non viene aggiornato automaticamente se stesso in quanto il `List<T>` raccolta non ha un meccanismo di notifica per indicare quando gli elementi vengono aggiunti o rimossi dalla raccolta.

È una classe molto migliore da usare in questi scenari [ `ObservableCollection<T>` ](xref:System.Collections.ObjectModel.ObservableCollection`1) definito nel `System.Collections.ObjectModel` dello spazio dei nomi. Questa classe implementa il [ `INotifyCollectionChanged` ](xref:System.Collections.Specialized.INotifyCollectionChanged) interfaccia e, di conseguenza, viene attivato un [ `CollectionChanged` ](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) eventi quando gli elementi vengono aggiunti o rimossi dalla raccolta, o quando vengono sostituiti o spostati all'interno di la raccolta. Quando la `ListView` internamente rileva che una classe che implementa `INotifyCollectionChanged` è stata impostata su relativo `ItemsSource` proprietà, collega un gestore per il `CollectionChanged` eventi e aggiorna i dati visualizzati quando la raccolta cambia.

Il [ **ObservableLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) esempio dimostra l'uso di `ObservableCollection`.

### <a name="templates-and-cells"></a>Modelli e delle celle

Per impostazione predefinita, un `ListView` Visualizza gli elementi nella raccolta corrispondente con ogni elemento `ToString` (metodo). Un approccio migliore implica la definizione di un modello per visualizzare gli elementi.

Per provare questa funzionalità, è possibile usare la [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria. Questa classe definisce un valore statico `All` vlastnosti typu `IList<NamedColor>` contenente 141 `NamedColor` corrispondente per i campi pubblici di oggetti di `Color` struttura.

Il [ **NaiveNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) set di esempio il `ItemsSource` di un `ListView` a questo `NamedColor.All` proprietà, ma solo i nomi di classe completo del `NamedColor` gli oggetti sono visualizzato.

`ListView` è necessario un modello per visualizzare questi elementi. Nel codice, è possibile impostare il [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) proprietà definita dal `ItemsView<TVisual>` a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) dell'oggetto usando la [ `DataTemplate` costruttore](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) che fa riferimento a un derivato del [ `Cell` ](xref:Xamarin.Forms.Cell) classe. `Cell` ha cinque derivati:

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; contiene due `Label` viste (concettuale)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; Aggiunge un `Image` visualizzare a `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; contiene un `Entry` consente di visualizzare una `Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; contiene un `Switch` con un `Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; può essere qualsiasi `View` (probabilmente con elementi figlio)

Quindi chiamare [ `SetValue` ](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) e [ `SetBinding` ](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) nel `DataTemplate` oggetto per associare i valori con il `Cell` proprietà, oppure per impostare le associazioni dati sul `Cell` riferimento alle proprietà degli elementi nella proprietà di `ItemsSource` raccolta. Questa funzionalità viene illustrata la [ **TextCellListCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) esempio.

Ogni elemento viene visualizzato dal `ListView`, un struttura ad albero visuale piccolo viene costruito dal modello e i data binding vengono stabiliti tra l'elemento e le proprietà degli elementi in questa struttura ad albero visuale. È possibile ottenere un'idea di questo processo installando i gestori per il [ `ItemAppearing` ](xref:Xamarin.Forms.ListView.ItemAppearing) e [ `ItemDisappearing` ](xref:Xamarin.Forms.ListView.ItemDisappearing) gli eventi del `ListView`, o con un'alternativa [ `DataTemplate`costruttore](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) che usa una funzione che viene chiamata ogni volta che è necessario creare struttura ad albero visuale di un elemento.

Il [ **TextCellListXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) Mostra un programma funzionalmente identico interamente in XAML. Oggetto `DataTemplate` tag è impostato sul `ItemTemplate` proprietà del `ListView`e quindi il `TextCell` è impostato sul `DataTemplate`. Le associazioni alle proprietà degli elementi nella raccolta vengono impostate direttamente nel [ `Text` ](xref:Xamarin.Forms.TextCell.Text) e [ `Detail` ](xref:Xamarin.Forms.TextCell.Detail) le proprietà del `TextCell`.

### <a name="custom-cells"></a>Celle personalizzate

In XAML è possibile impostare una [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) per il `DataTemplate` e quindi definire un struttura ad albero visuale personalizzato come il [ `View` ](xref:Xamarin.Forms.ViewCell.View) proprietà di `ViewCell`. (`View` è la proprietà content del `ViewCell` in modo che il `ViewCell.View` tag non sono necessari.) Il [ **CustomNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) questa tecnica è dimostrata esempio:

[![Tripla screenshot dell'elenco dei colori personalizzata denominata](images/ch19fg11-small.png "personalizzata denominata elenco dei colori")](images/ch19fg11-large.png#lightbox "personalizzata denominata elenco dei colori")

Ottenere il ridimensionamento a destra per tutte le piattaforme può essere difficile. Il [ `RowHeight` ](xref:Xamarin.Forms.ListView.RowHeight) proprietà è utile, ma in alcuni casi è opportuno ricorrere al [ `HasUnevenRows` ](xref:Xamarin.Forms.ListView.HasUnevenRows) proprietà, che è meno efficiente, ma impone il `ListView` per ridimensionare le righe. Per iOS e Android, è necessario utilizzare una di queste due proprietà per ottenere il ridimensionamento delle righe appropriata.

### <a name="grouping-the-listview-items"></a>Il raggruppamento degli articoli di ListView

`ListView` supporta il raggruppamento degli elementi e lo spostamento tra tali gruppi. Il `ItemsSource` proprietà deve essere impostata su una raccolta di raccolte: l'oggetto che `ItemsSource` è impostata su devono implementare `IEnumerable`, e ogni elemento della raccolta deve implementare anche `IEnumerable`. Ogni gruppo deve includere due proprietà: una descrizione del gruppo e un'abbreviazione di tre lettere.

Il [ `NamedColorGroup` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) creato dalla libreria sette gruppi di `NamedColor` oggetti. Il [ **ColorGroupList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) esempio viene illustrato come usare questi gruppi con il [ `IsGroupingEnabled` ](xref:Xamarin.Forms.ListView.IsGroupingEnabled) proprietà del `ListView` impostato su `true`e la [ `GroupDisplayBinding` ](xref:Xamarin.Forms.ListView.GroupDisplayBinding) e [ `GroupShortNameBinding` ](xref:Xamarin.Forms.ListView.GroupShortNameBinding) proprietà associate alle proprietà in ogni gruppo.

### <a name="custom-group-headers"></a>Intestazioni di gruppo personalizzato

È possibile creare intestazioni personalizzate per il `ListView` gruppi sostituendo il `GroupDisplayBinding` proprietà con il [ `GroupHeaderTemplate` ](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) che definisce un modello per le intestazioni.

### <a name="listview-and-interactivity"></a>ListView e interattività

In genere un'applicazione ottiene l'interazione dell'utente con un `ListView` associando un gestore per il `ItemSelected` o [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) evento, oppure tramite l'impostazione di un data binding nel `SelectedItem` proprietà. Ma alcuni tipi di cella (`EntryCell` e `SwitchCell`) consentono l'interazione dell'utente ed è anche possibile creare le celle personalizzate che interagiscono con l'utente. Il [ **InteractiveListView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crea 100 istanze di [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) e consente all'utente di modificare ogni colore usando una combinazione di tre `Slider` elementi. Il programma Usa anche il [ `ColorToContrastColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) nel [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView e MVVM

`ListView` svolge un ruolo importante negli scenari di MVVM. Ogni volta che un `IEnumerable` raccolta esiste in un elemento ViewModel, è spesso associato a un `ListView`. Inoltre, gli elementi nella raccolta spesso implementano `INotifyPropertyChanged` per associare le proprietà in un modello.

### <a name="a-collection-of-viewmodels"></a>Una raccolta di elementi ViewModel

Per esplorare, il [ **SchoolOfFineArts** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) diverse classi di base creato dalla libreria un [file di dati XML](http://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e immagini di fittizi studenti della scuola fittizia.

Il [ `Student` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) deriva dalla classe [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). Il [ `StudentBody` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) classe è una raccolta di `Student` degli oggetti e deriva anche da `ViewModelBase`. Il [ `SchoolViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) Scarica il file XML e li assembla tutti gli oggetti.

Il [ **StudentList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) programma utilizza un' `ImageCell` per visualizzare gli studenti e le relative immagini in un `ListView`:

[![Tripla screenshot dell'elenco degli studenti](images/ch19fg18-small.png "elenco di studenti")](images/ch19fg18-large.png#lightbox "elenco degli studenti")

Il [ **ListViewHeader** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) esempio aggiunge un [ `Header` ](xref:Xamarin.Forms.ListView.Header) proprietà, ma viene visualizzata solo in Android.

### <a name="selection-and-the-binding-context"></a>Selezione e il contesto di associazione

Il [ **SelectedStudentDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) programmare associa il `BindingContext` di un `StackLayout` per il `SelectedItem` proprietà del `ListView`. In questo modo il programma per visualizzare informazioni dettagliate su dello studente selezionato.

### <a name="context-menus"></a>Menu di scelta rapida

Una cella può definire un menu di scelta rapida che viene implementato in modo specifico della piattaforma. Per creare questo menu, aggiungere [ `MenuItem` ](xref:Xamarin.Forms.MenuItem) oggetti per il [ `ContextActions` ](xref:Xamarin.Forms.Cell.ContextActions) proprietà del `Cell`.

`MenuItem` definisce cinque proprietà:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) di tipo `string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) di tipo `FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) di tipo `bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command) di tipo `ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) di tipo `object`

Il `Command` e `CommandParameter` proprietà implicano che l'elemento ViewModel per ogni elemento contiene i metodi per eseguire i comandi di menu desiderato. In scenari non MVVM `MenuItem` definisce anche una [ `Clicked` ](xref:Xamarin.Forms.MenuItem.Clicked) evento.

Il [ **CellContextMenu** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) questa tecnica è dimostrata. Il `Command` proprietà della ognuno `MenuItem` è associato a una proprietà di tipo `ICommand` nel `Student` classe. Impostare il `IsDestructive` proprietà `true` per un `MenuItem` che rimuove o Elimina l'oggetto selezionato.

### <a name="varying-the-visuals"></a>Variare gli oggetti visivi

In alcuni casi è opportuno leggere variazioni negli oggetti visivi degli elementi di `ListView` basata su una proprietà. Ad esempio, quando grade-Media uno studente scende di sotto 2.0, il [ **ColorCodedStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) nell'esempio vengono visualizzati il nome dello studente in rosso.
Questa operazione viene eseguita tramite l'uso di un convertitore dell'associazione [ `ThresholdToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs), nella [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria.

### <a name="refreshing-the-content"></a>L'aggiornamento del contenuto

Il `ListView` supporta un movimento di menu a discesa per aggiornarne i dati. Il programma è necessario impostare il [ `IsPullToRefresh` ](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) proprietà `true` per abilitare questa opzione. Il `ListView` risponde ai movimenti di discesa impostando relativo [ `IsRefreshing` ](xref:Xamarin.Forms.ListView.IsRefreshing) proprietà `true`e generando i [ `Refreshing` ](xref:Xamarin.Forms.ListView.Refreshing) eventi e (per gli scenari MVVM) chiamata il `Execute` metodo del relativo [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand) proprietà.

La gestione del codice il `Refresh` evento o la `RefreshCommand` quindi probabilmente Aggiorna i dati visualizzati per il `ListView` e imposta `IsRefreshing` al `false`.

Il [ **RssFeed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) esempio viene illustrato come utilizzare un' [ `RssFeedViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) che implementa `RefreshCommand` e `IsRefreshing` proprietà per il data binding.

## <a name="the-tableview-and-its-intents"></a>Il TableView e relative finalità

Mentre il `ListView` in genere vengono visualizzate più istanze dello stesso tipo, il [ `TableView` ](xref:Xamarin.Forms.TableView) in genere è incentrata sulla capacità di fornire un'interfaccia utente per più proprietà di vario tipo. Ogni elemento è associato un proprio [ `Cell` ](xref:Xamarin.Forms.Cell) derivativo per la visualizzazione della proprietà o che forniscono un'interfaccia utente a esso.

### <a name="properties-and-hierarchies"></a>Le proprietà e gerarchie

`TableView` definisce solo quattro proprietà:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent) typu [ `TableIntent` ](xref:Xamarin.Forms.TableIntent), un'enumerazione
- [`Root`](xref:Xamarin.Forms.TableView.Root) typu [ `TableRoot` ](xref:Xamarin.Forms.TableRoot), la proprietà di contenuto di `TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) di tipo `int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) di tipo `bool`

Il `TableIntent` enumerazione indica come si prevede di usare il `TableView`:

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Questi membri suggeriscono anche alcuni usi del `TableView`.

Molte altre classi, sono coinvolti nella definizione di una tabella:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase) è una classe astratta da cui deriva `BindableObject` e definisce un [ `Title` ](xref:Xamarin.Forms.TableSectionBase.Title) proprietà

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1) è una classe astratta da cui deriva `TableSectionBase` e implementa `IList<T>` e `INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection) deriva da `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot) deriva da `TableSectionBase<TableSection>`

In breve, `TableView` ha un `Root` proprietà che è impostato su un `TableRoot` oggetto, ovvero una raccolta di `TableSection` oggetti, ognuno dei quali è una raccolta di `Cell` oggetti. Una tabella ha più sezioni, ognuna composta da più celle. La tabella stessa può avere un titolo e ogni sezione può avere un titolo. Sebbene `TableView` Usa `Cell` derivati, non rende Usa `DataTemplate`.

### <a name="a-prosaic-form"></a>Un form semplice

Il [ **EntryForm** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) esempio definisce un [ `PersonalInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) modello di visualizzazione, un'istanza di cui diventa la `BindingContext` del `TableView`. Ogni `Cell` derivativo nel relativo `TableSection` può quindi avere associazioni alle proprietà del `PersonalInformation` classe.

### <a name="custom-cells"></a>Celle personalizzate

Il [ **ConditionalCells** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) esempio amplia **EntryForm**. Il [ `ProgrammerInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) classe include una proprietà booleana che determina l'applicabilità di due proprietà aggiuntive. Per queste due proprietà aggiuntive, il programma Usa una classe personalizzata `PickerCell` basato su una [PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) e [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) nel [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria.

Anche se il `IsEnabled` delle proprietà dei due `PickerCell` gli elementi vengono associati per la proprietà booleana `ProgrammerInformation`, questa tecnica non sembra funzionare, che richiede l'esempio successivo.

### <a name="conditional-sections"></a>Sezioni condizionali

Il [ **ConditionalSection** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) esempio inserisce i due elementi che sono condizionali per la selezione dell'elemento booleano in un oggetto separato `TableSection`. Il file code-behind rimuove questa sezione dal `TableView` o aggiunge in base nuovamente la proprietà booleana.

### <a name="a-tableview-menu"></a>Un menu TableView

Un altro uso di un `TableView` è un menu. Il [ **confronto tra oggetti MenuCommand** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) esempio viene illustrato un menu che consente di spostare un po' `BoxView` attorno alla schermata.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 19 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Esempi di capitolo 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Selezione](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
