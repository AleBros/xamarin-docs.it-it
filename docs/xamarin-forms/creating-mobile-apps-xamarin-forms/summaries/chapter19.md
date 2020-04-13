---
title: Riassunto del capitolo 19. Visualizzazioni raccolta
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 19. Visualizzazioni raccolta'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bffbd2dec4a8494723597ba6e0f0af69e57f3718
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032858"
---
# <a name="summary-of-chapter-19-collection-views"></a>Riassunto del capitolo 19. Visualizzazioni raccolta

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui Xamarin.Forms si è discostata dal materiale presentato nel libro.

Xamarin.Forms definisce tre visualizzazioni che gestiscono le raccolte e ne visualizzano gli elementi:

- [`Picker`](xref:Xamarin.Forms.Picker)è un elenco relativamente breve di elementi stringa che consente all'utente di scegliere
- [`ListView`](xref:Xamarin.Forms.ListView)è spesso un lungo elenco di elementi di solito dello stesso tipo e la stessa formattazione, consentendo anche all'utente di scegliere uno
- [`TableView`](xref:Xamarin.Forms.TableView)è una raccolta di *celle* (di solito di vari tipi e aspetti) per visualizzare i dati o gestire l'input dell'utente

È comune per le applicazioni MVVM utilizzare il `ListView` per visualizzare una raccolta selezionabile di oggetti.

## <a name="program-options-with-picker"></a>Opzioni del programma con Selezione

Il [`Picker`](xref:Xamarin.Forms.Picker) è una buona scelta quando è necessario consentire all'utente di `string` scegliere un'opzione tra un elenco relativamente breve di elementi.

### <a name="the-picker-and-event-handling"></a>Selezione e gestione degli eventi

Nell'esempio [**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) viene illustrato come `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) utilizzare `string` XAML per [`Items`](xref:Xamarin.Forms.Picker.Items) impostare la proprietà e aggiungere elementi alla raccolta. Quando l'utente `Picker`seleziona l'oggetto , `Items` visualizza gli elementi dell'insieme in modo dipendente dalla piattaforma.

L'evento [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) indica quando l'utente ha selezionato un elemento. La proprietà [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) in base zero indica quindi l'elemento selezionato. Se non è `SelectedIndex` selezionato &ndash;alcun elemento, è uguale a 1.

È anche `SelectedIndex` possibile utilizzare per inizializzare l'elemento `Items` selezionato, ma deve essere impostato dopo il riempimento della raccolta. In XAML, questo significa che probabilmente userai un elemento proprietà per impostare `SelectedIndex`.

### <a name="data-binding-the-picker"></a>Associazione dati della selezioneData binding the Picker

La `SelectedIndex` proprietà è supportata da `Items` una proprietà associabile ma `Picker` non lo è, pertanto è difficile usare l'associazione dati con un oggetto. Una soluzione consiste `Picker` nell'utilizzare [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) il in in in combinazione con uno come quello nella libreria [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) Il [**PickerBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) viene illustrato come funziona.

> [!NOTE] 
> Xamarin.Forms `Picker` ora `ItemsSource` include `SelectedItem` e le proprietà che supportano l'associazione dati. Consultate [Selezione.](~/xamarin-forms/user-interface/picker/index.md)

## <a name="rendering-data-with-listview"></a>Rendering data with ListView

L'unica [`ListView`](xref:Xamarin.Forms.ListView) classe che deriva [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) da cui eredita [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) le proprietà e .

`ItemsSource`è di `IEnumerable` tipo, `null` ma è per impostazione predefinita e deve essere inizializzato in modo esplicito o (più comunemente) impostato su una raccolta tramite un'associazione dati. Gli elementi in questa raccolta possono essere di qualsiasi tipo.

`ListView`definisce [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) una proprietà impostata su uno degli `ItemsSource` elementi `null` nella raccolta o se non è selezionato alcun elemento. `ListView`genera l'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) quando viene selezionato un nuovo elemento.

### <a name="collections-and-selections"></a>Raccolte e selezioni

Il [**ListViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) esempio `ListView` riempie `Color` un con `List<Color>` 17 valori in una raccolta. Gli elementi sono selezionabili, ma per impostazione `ToString` predefinita vengono visualizzati con le relative rappresentazioni poco attraenti. Diversi esempi in questo capitolo mostrano come risolvere quel display e renderlo attraente come desiderato.

### <a name="the-row-separator"></a>Separatore di riga

Sui display iOS e Android, una linea sottile separa le righe. È possibile controllare [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) questo [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor) con il e proprietà. `SeparatorVisibility`proprietà è [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility)di tipo , un'enumerazione con due membri :

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), l'impostazione predefinita
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>Associazione dati dell'elemento selezionato

La `SelectedItem` proprietà è supportata da una proprietà associabile, pertanto può essere l'origine o la destinazione di un'associazione dati. Il `BindingMode` valore `OneWayToSource`predefinito è , ma in genere è la destinazione di un'associazione dati bidirezionale, in particolare negli scenari MVVM. Nell'esempio [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) viene illustrato questo tipo di associazione.

### <a name="the-observablecollection-difference"></a>La differenza ObservableCollectionThe ObservableCollection difference

Il [**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) esempio `ItemsSource` imposta `ListView` la `List<DateTime>` proprietà di un a `DateTime` a una raccolta e quindi aggiunge progressivamente un nuovo oggetto alla raccolta ogni secondo utilizzando un timer.

Tuttavia, `ListView` il non si aggiorna `List<T>` automaticamente perché la raccolta non dispone di un meccanismo di notifica per indicare quando gli elementi vengono aggiunti o rimossi dalla raccolta.

Una classe molto migliore da [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) utilizzare in `System.Collections.ObjectModel` tali scenari è definita nello spazio dei nomi. Questa classe [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) implementa l'interfaccia e [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) di conseguenza genera un evento quando gli elementi vengono aggiunti o rimossi dalla raccolta o quando vengono sostituiti o spostati all'interno della raccolta. Quando `ListView` l'oggetto rileva internamente `INotifyCollectionChanged` che una `ItemsSource` classe che implementa è stata `CollectionChanged` impostata sulla relativa proprietà, associa un gestore all'evento e ne aggiorna la visualizzazione quando la raccolta viene modificata.

[**Nell'esempio ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) viene `ObservableCollection`illustrato l'utilizzo di .

### <a name="templates-and-cells"></a>Modelli e celle

Per impostazione `ListView` predefinita, un visualizza gli `ToString` elementi nella raccolta utilizzando il metodo di ogni elemento. Un approccio migliore comporta la definizione di un modello per visualizzare gli elementi.

Per sperimentare questa funzionalità, è [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) possibile utilizzare la classe nella libreria [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) Questa classe definisce `All` una `IList<NamedColor>` proprietà statica `NamedColor` di tipo che contiene `Color` 141 oggetti corrispondenti ai campi pubblici della struttura.

Il [**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) esempio `ItemsSource` imposta `ListView` il `NamedColor.All` di a a per questa proprietà, ma vengono visualizzati solo i nomi di classe completi degli `NamedColor` oggetti.

`ListView`richiede un modello per visualizzare questi elementi. Nel codice è possibile [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) impostare `ItemsView<TVisual>` la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà definita da per un [`Cell`](xref:Xamarin.Forms.Cell) oggetto utilizzando il [ `DataTemplate` costruttore](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) che fa riferimento a una derivata della classe . `Cell`ha cinque derivati:

- [`TextCell`](xref:Xamarin.Forms.TextCell)&mdash; contiene `Label` due viste (concettualmente parlando)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)&mdash; aggiunge `Image` una vista a`TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)&mdash; contiene `Entry` una vista con un`Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)&mdash; contiene `Switch` un con un`Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell)&mdash; può essere `View` qualsiasi (probabilmente con i bambini)

Chiamare [`SetValue`](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) quindi [`SetBinding`](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) e `DataTemplate` sull'oggetto per `Cell` associare valori alle proprietà `Cell` o per impostare associazioni dati `ItemsSource` sulle proprietà che fanno riferimento alle proprietà degli elementi nella raccolta. Ciò è illustrato nell'esempio [**TextCellListCode.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) is demonstrated in the TextCellListCode sample.

Quando ogni elemento viene `ListView`visualizzato da , una piccola struttura ad albero visuale viene costruita dal modello e le associazioni dati vengono stabilite tra l'elemento e le proprietà degli elementi in questa struttura ad albero visuale. È possibile avere un'idea di questo processo [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) installando `ListView`gestori per gli eventi e di , o utilizzando un [ `DataTemplate` costruttore](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) alternativo che utilizza una funzione che viene chiamata ogni volta che è necessario creare la struttura ad albero visuale di un elemento.

[**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) mostra un programma funzionalmente identico interamente in XAML. Un `DataTemplate` tag viene `ItemTemplate` impostato sulla `ListView`proprietà di `TextCell` , quindi `DataTemplate`l'oggetto viene impostato su . Le associazioni alle proprietà degli elementi dell'insieme [`Text`](xref:Xamarin.Forms.TextCell.Text) [`Detail`](xref:Xamarin.Forms.TextCell.Detail) vengono impostate direttamente sulle proprietà e dell'oggetto `TextCell`.

### <a name="custom-cells"></a>Celle personalizzate

In XAML è possibile [`ViewCell`](xref:Xamarin.Forms.ViewCell) impostare `DataTemplate` a su e quindi [`View`](xref:Xamarin.Forms.ViewCell.View) definire `ViewCell`una struttura ad albero visuale personalizzata come proprietà di . (è`View` la proprietà `ViewCell` content `ViewCell.View` di in modo che i tag non sono obbligatori.) [**Nell'esempio CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) viene illustrata questa tecnica:The CustomNamedColorList sample demonstrates this technique:

[![Tripla schermata dell'elenco dei colori con nome personalizzato](images/ch19fg11-small.png "Elenco colori con nome personalizzato")](images/ch19fg11-large.png#lightbox "Elenco colori con nome personalizzato")

Ottenere il dimensionamento giusto per tutte le piattaforme può essere difficile. La [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight) proprietà è utile, ma in alcuni casi [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) si vorrà ricorrere `ListView` alla proprietà, che è meno efficiente, ma costringe il per ridimensionare le righe. Per iOS e Android, è necessario utilizzare una di queste due proprietà per ottenere il ridimensionamento corretto delle righe.

### <a name="grouping-the-listview-items"></a>Raggruppamento degli elementi di ListView

`ListView`supporta il raggruppamento di elementi e la navigazione tra tali gruppi. La `ItemsSource` proprietà deve essere impostata su un `ItemsSource` insieme di `IEnumerable`raccolte: l'oggetto impostato su `IEnumerable`deve implementare e ogni elemento dell'insieme deve implementare anche . Ogni gruppo deve includere due proprietà: una descrizione testuale del gruppo e un'abbreviazione di tre lettere.

La [`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) classe nella libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) `NamedColor` crea sette gruppi di oggetti. Nell'esempio [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) viene illustrato come [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) utilizzare `ListView` questi `true`gruppi [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) con [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) la proprietà set to e le proprietà e associate alle proprietà in ogni gruppo.

### <a name="custom-group-headers"></a>Intestazioni di gruppo personalizzate

È possibile creare intestazioni personalizzate `ListView` per i gruppi `GroupDisplayBinding` sostituendo [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) la proprietà con la definizione di un modello per le intestazioni.

### <a name="listview-and-interactivity"></a>ListView e interattività

In genere un'applicazione ottiene `ListView` l'interazione dell'utente con un oggetto associando un gestore all'evento `ItemSelected` o [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) o impostando un'associazione dati sulla `SelectedItem` proprietà. Ma alcuni tipi`EntryCell` `SwitchCell`di cella ( e ) consentono l'interazione dell'utente ed è anche possibile creare celle personalizzate che interagiscono con l'utente. Il [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crea 100 istanze di e consente all'utente di [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) modificare ogni colore utilizzando un trio di `Slider` elementi. Il programma utilizza anche [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) il in [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView e MVVM

`ListView`svolge un ruolo importante negli scenari MVVM. Ogni `IEnumerable` volta che una raccolta esiste in un `ListView`ViewModel, è spesso associata a un oggetto . Inoltre, gli elementi nella `INotifyPropertyChanged` raccolta spesso implementano per l'associazione con le proprietà in un modello.

### <a name="a-collection-of-viewmodels"></a>Raccolta di ViewModels

Per esplorare questo, la libreria [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) crea diverse classi basate su un file di [dati XML](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e immagini di studenti fittizi in questa scuola fittizia.

La [`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) classe deriva [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs)da . La [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) classe è `Student` un insieme di `ViewModelBase`oggetti e deriva anche da . Scarica [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) il file XML e assembla tutti gli oggetti.

Il programma [**StudentList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) utilizza un `ImageCell` per visualizzare `ListView`gli studenti e le loro immagini in un :

[![Tripla schermata dell'elenco degli studenti](images/ch19fg18-small.png "Elenco studenti")](images/ch19fg18-large.png#lightbox "Elenco studenti")

Il [**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) esempio [`Header`](xref:Xamarin.Forms.ListView.Header) aggiunge una proprietà ma viene visualizzato solo in Android.The ListViewHeader sample adds a property but it only shows up on Android.

### <a name="selection-and-the-binding-context"></a>Selezione e contesto di associazione

Il programma [**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) `BindingContext` associa `StackLayout` l'oggetto di a alla `SelectedItem` proprietà dell'oggetto `ListView`. Ciò consente al programma di visualizzare informazioni dettagliate sullo studente selezionato.

### <a name="context-menus"></a>Menu di scelta rapida

Una cella può definire un menu di scelta rapida che viene implementato in modo specifico della piattaforma. Per creare questo [`MenuItem`](xref:Xamarin.Forms.MenuItem) menu, [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) aggiungere oggetti `Cell`alla proprietà dell'oggetto .

`MenuItem`definisce cinque proprietà:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)di tipo`string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)di tipo`FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)di tipo`bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command)di tipo`ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)di tipo`object`

Le `Command` `CommandParameter` proprietà e implicano che il ViewModel per ogni elemento contiene metodi per eseguire i comandi di menu desiderati. Negli scenari non MVVM, `MenuItem` definisce anche un [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) evento.

Il [**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) viene illustrata questa tecnica. La `Command` proprietà `MenuItem` di each è associata a una proprietà di tipo `ICommand` nella `Student` classe. Impostare `IsDestructive` la `true` proprietà `MenuItem` su per un oggetto che rimuove o elimina l'oggetto selezionato.

### <a name="varying-the-visuals"></a>Variazione degli oggetti visivi

A volte è necessario lievi variazioni negli oggetti `ListView` visivi degli elementi in basato su una proprietà. Ad esempio, quando la media dei voti di uno studente scende al di sotto di 2.0, l'esempio [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) visualizza il nome dello studente in rosso.
Questa operazione viene eseguita tramite [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)l'utilizzo di un convertitore di valori di associazione, , nella libreria [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

### <a name="refreshing-the-content"></a>Aggiornamento del contenuto

Il `ListView` supporta un gesto pull-down per l'aggiornamento dei dati. Il programma deve [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) impostare la proprietà su `true` per abilitare questa opzione. Risponde `ListView` al movimento a discesa [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) impostandone `true`la proprietà su [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) , e generando l'evento `Execute` e [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) chiamando (per gli scenari MVVM) chiamando il metodo della relativa proprietà.

Il codice `Refresh` che `RefreshCommand` gestisce l'evento o `ListView` quindi `IsRefreshing` aggiorna `false`eventualmente i dati visualizzati da e viene reimpostato su .

Nell'esempio [**RssFeed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) viene `RefreshCommand` illustrato `IsRefreshing` l'uso di un che implementa e delle proprietà per l'associazione dati.

## <a name="the-tableview-and-its-intents"></a>Il TableView e le relative finalità

Mentre `ListView` generalmente visualizza più istanze dello [`TableView`](xref:Xamarin.Forms.TableView) stesso tipo, l'oggetto è generalmente incentrato sulla fornitura di un'interfaccia utente per più proprietà di vari tipi. Ogni elemento è associato [`Cell`](xref:Xamarin.Forms.Cell) alla propria derivata per la visualizzazione della proprietà o la fornitura di un'interfaccia utente.

### <a name="properties-and-hierarchies"></a>Proprietà e gerarchie

`TableView`definisce solo quattro proprietà:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent)di [`TableIntent`](xref:Xamarin.Forms.TableIntent)tipo , un'enumerazione
- [`Root`](xref:Xamarin.Forms.TableView.Root)di [`TableRoot`](xref:Xamarin.Forms.TableRoot)tipo , la proprietà content di`TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight)di tipo`int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)di tipo`bool`

L'enumerazione `TableIntent` indica come si `TableView`intende utilizzare il :

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Questi membri suggeriscono anche `TableView`alcuni usi per il .

Diverse altre classi sono coinvolte nella definizione di una tabella:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase)è una classe astratta `BindableObject` che deriva [`Title`](xref:Xamarin.Forms.TableSectionBase.Title) da e definisce una proprietà

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1)è una classe astratta `TableSectionBase` che `IList<T>` deriva da e implementa e`INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection)deriva da`TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot)deriva da`TableSectionBase<TableSection>`

In `TableView` breve, `Root` ha una proprietà `TableRoot` che si imposta su `TableSection` un oggetto, che è `Cell` una raccolta di oggetti, ognuno dei quali è una raccolta di oggetti. Una tabella ha più sezioni e ogni sezione ha più celle. La tabella stessa può avere un titolo e ogni sezione può avere un titolo. Anche `TableView` se `Cell` fa uso di derivati, `DataTemplate`non fa uso di .

### <a name="a-prosaic-form"></a>Una forma prosaica

Nell'esempio [**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) viene definito un modello di `BindingContext` visualizzazione, un'istanza di cui diventa l'oggetto `TableView`. Ogni `Cell` derivato `TableSection` nella sua può quindi `PersonalInformation` avere associazioni alle proprietà della classe.

### <a name="custom-cells"></a>Celle personalizzate

L'esempio [**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) si espande in **EntryForm**. La [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) classe include una proprietà booleana che regola l'applicabilità di due proprietà aggiuntive. Per queste due proprietà aggiuntive, `PickerCell` il programma utilizza un oggetto personalizzato basato su un [file PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) e [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) nella libreria [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

Sebbene `IsEnabled` le proprietà `PickerCell` dei due elementi siano `ProgrammerInformation`associate alla proprietà booleana in , questa tecnica non sembra funzionare, il che richiede l'esempio successivo.

### <a name="conditional-sections"></a>Sezioni condizionali

Nell'esempio [**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) vengono inseriti i due elementi condizionati `TableSection`alla selezione dell'elemento booleano in un file . Il file code-behind rimuove `TableView` questa sezione dalla o la aggiunge nuovamente in base alla proprietà booleana.

### <a name="a-tableview-menu"></a>Un menu TableView

Un altro `TableView` uso di un è un menu. Nell'esempio [**MenuCommands**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) viene illustrato un `BoxView` menu che consente di spostarsi un po' sullo schermo.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 19 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Capitolo 19 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Selezione](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [Visualizzazione Tabella](~/xamarin-forms/user-interface/tableview.md)
