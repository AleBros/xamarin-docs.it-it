---
title: Riepilogo del capitolo 19. Viste di raccolta
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 37afa3a54fd20745a65312fb5a24d958c8ec405f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-19-collection-views"></a>Riepilogo del capitolo 19. Viste di raccolta

Xamarin. Forms definisce tre visualizzazioni che gestiscono le raccolte e visualizzare i relativi elementi:

- [`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) è un relativamente breve elenco di elementi di tipo stringa che consente all'utente di scegliere uno
- [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) è spesso un lungo elenco di elementi in genere dello stesso tipo e la formattazione, anche consentire all'utente di scegliere uno
- [`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) è una raccolta di *celle* (in genere di diversi tipi e gli aspetti) per visualizzare i dati o gestire l'input dell'utente

È comune nelle applicazioni MVVM da utilizzare per il `ListView` per visualizzare una raccolta di oggetti selezionabile.

## <a name="program-options-with-picker"></a>Opzioni del programma con pulsante di selezione

Il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) è una scelta ottimale quando è necessario consentire all'utente di scegliere un'opzione tra quelle per un elenco di relativamente breve `string` elementi.

### <a name="the-picker-and-event-handling"></a>La selezione e la gestione degli eventi

Il [ **PickerDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) esempio viene illustrato come utilizzare XAML per impostare il `Picker` [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/) proprietà e aggiungere `string` elementi per il [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) insieme. Quando l'utente seleziona il `Picker`, Visualizza gli elementi nel `Items` insieme in modo dipendente dalla piattaforma.

Il [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) evento indica quando l'utente ha selezionato un elemento. In base zero [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) proprietà indicherà l'elemento selezionato. Se è selezionato alcun elemento, `SelectedIndex` uguale a & #x 2013; 1.

È inoltre possibile utilizzare `SelectedIndex` per inizializzare l'elemento selezionato, ma deve essere impostato dopo la `Items` insieme viene compilato. In XAML, ciò significa che è probabilmente userà un elemento di proprietà per impostare `SelectedIndex`.

### <a name="data-binding-the-picker"></a>Il selettore di associazione dati

Il `SelectedIndex` proprietà è supportata da una proprietà associabile ma `Items` non lo è, pertanto usando l'associazione dati con un `Picker` è difficile. Una soluzione consiste nell'usare il `Picker` in combinazione con un [ `ObjectToIndexConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) come quello usato nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria. Il [ **PickerBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) viene illustrato il funzionamento.

## <a name="rendering-data-with-listview"></a>Il rendering dei dati con ListView

Il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) è l'unica classe che deriva da [ `ItemsView<TVisual>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) da cui eredita il [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) e [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) proprietà.

`ItemsSource` è di tipo `IEnumerable` ma è `null` per impostazione predefinita e deve essere inizializzato in modo esplicito o impostare (di solito) a una raccolta tramite l'associazione dati. Gli elementi nella raccolta possono essere di qualsiasi tipo.

`ListView` definisce un [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/) proprietà che sia impostata su uno degli elementi di `ItemsSource` insieme o `null` se è selezionato alcun elemento. `ListView` viene attivato il [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) evento quando viene selezionato un nuovo elemento.

### <a name="collections-and-selections"></a>Raccolte e le selezioni

Il [ **ListViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) esempio compila un `ListView` con 17 `Color` i valori in un `List<Color>` insieme. Gli elementi sono selezionabili, ma per impostazione predefinita vengono visualizzati con i relativi renderne `ToString` rappresentazioni. Alcuni esempi in questo capitolo viene illustrato come risolvere che visualizzano e renderlo altrettanto soddisfacenti in base alle esigenze.

### <a name="the-row-separator"></a>Il separatore di riga

In iOS e Android consente di visualizzare, una riga sottile separa le righe. È possibile controllare con il [ `SeparatorVisibiliy` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SeparatorVisibility/) e [ `SeparatorColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SeparatorColor/) proprietà. `SeparatorVisibility` la proprietà è di tipo [ `SeparatorVisbility` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SeparatorVisibility/), un'enumerazione con due membri:

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.SeparatorVisibility.Default/), l'impostazione predefinita
- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.SeparatorVisibility.None/)

### <a name="data-binding-the-selected-item"></a>L'elemento selezionato di associazione dati

Il `SelectedItem` proprietà è supportata da una proprietà associabile, pertanto può essere l'origine o la destinazione di un'associazione dati. Il valore predefinito `BindingMode` è `OneWayToSource`, ma in genere è la destinazione di un'associazione bidirezionale dei dati, in particolare in scenari MVVM. Il [ **ListViewArray** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) esempio viene illustrato questo tipo di associazione.

### <a name="the-observablecollection-difference"></a>La differenza ObservableCollection

Il [ **ListViewLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) set di esempio di `ItemsSource` proprietà di un `ListView` per un `List<DateTime>` insieme e progressivamente aggiunge quindi un nuovo `DateTime` oggetto alla raccolta ogni secondo tramite un timer.

Tuttavia, il `ListView` non viene aggiornato automaticamente in quanto il `List<T>` raccolta non dispone di un meccanismo di notifica per indicare quando gli elementi vengono aggiunti o rimossi dalla raccolta.

Una classe molto migliore da usare in scenari di questo tipo è [ `ObservableCollection<T>` ](https://developer.xamarin.com/api/type/System.Collections.ObjectModel.ObservableCollection%3CT%3E/) definito nel `System.Collections.ObjectModel` dello spazio dei nomi. Questa classe implementa il [ `INotifyCollectionChanged` ](https://developer.xamarin.com/api/type/System.Collections.Specialized.INotifyCollectionChanged/) interfaccia e conseguenza viene attivato un [ `CollectionChanged` ](https://developer.xamarin.com/api/event/System.Collections.ObjectModel.ObservableCollection%3CT%3E.CollectionChanged/) evento quando gli elementi vengono aggiunti o rimossi dalla raccolta o quando vengono sostituiti o spostati all'interno la raccolta. Quando il `ListView` internamente rileva che una classe che implementa `INotifyCollectionChanged` è stata impostata su relativo `ItemsSource` proprietà, associa un gestore per il `CollectionChanged` evento e aggiorna la visualizzazione quando la raccolta cambia.

Il [ **ObservableLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) esempio viene illustrato l'utilizzo di `ObservableCollection`.

### <a name="templates-and-cells"></a>Modelli e le celle

Per impostazione predefinita, un `ListView` Visualizza gli elementi nella raccolta corrispondente con ogni elemento `ToString` metodo. Un approccio migliore include la definizione di un modello per visualizzare gli elementi.

Per provare questa funzionalità, è possibile utilizzare il [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria. Questa classe definisce un valore statico `All` proprietà di tipo `IList<NamedColor>` contenente 141 `NamedColor` corrispondente per i campi pubblici di oggetti di `Color` struttura.

Il [ **NaiveNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) set di esempio di `ItemsSource` di un `ListView` a questo `NamedColor.All` proprietà, ma solo i nomi di classe completo del `NamedColor` gli oggetti sono visualizzato.

`ListView` è necessario un modello per visualizzare questi elementi. Nel codice, è possibile impostare il [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) definita dalla proprietà `ItemsView<TVisual>` per un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) utilizzando il [ `DataTemplate` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/) che fa riferimento a un derivato del [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) classe. `Cell` include cinque derivati:

- [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) & #x 2014; contiene due `Label` viste (concettuale)
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) & #x 2014; Aggiunge un `Image` visualizzazione `TextCell`
- [`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/) & #x 2014; contiene un `Entry` visualizzare con un `Label`
- [`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/) & #x 2014; contiene un `Switch` con un `Label`
- [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) & #x 2014; può essere qualsiasi `View` (probabilmente con elementi figlio)

Quindi chiamare [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DataTemplate.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) e [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DataTemplate.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) nel `DataTemplate` oggetto per associare i valori con il `Cell` proprietà o impostare le associazioni dati per il `Cell` Fare riferimento alle proprietà degli elementi nella proprietà di `ItemsSource` insieme. Questa funzionalità viene illustrata la [ **TextCellListCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) esempio.

Ogni elemento viene visualizzato nel `ListView`, un struttura ad albero visuale piccolo viene costruito dal modello e vengono stabilite le associazioni di dati tra l'elemento e le proprietà degli elementi in questa struttura ad albero visuale. È possibile farsi un'idea di questo processo installando i gestori per il [ `ItemAppearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemAppearing/) e [ `ItemDisappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemDisappearing/) gli eventi del `ListView`, o con un'alternativa [ `DataTemplate`costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Func%7BSystem.Object%7D/) che utilizza una funzione che viene chiamata ogni volta che è necessario creare struttura ad albero visuale di un elemento.

Il [ **TextCellListXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) Mostra un programma funzionalmente identico interamente in XAML. A `DataTemplate` tag è impostato sul `ItemTemplate` proprietà del `ListView`e quindi la `TextCell` è impostato sul `DataTemplate`. Le associazioni alle proprietà degli elementi nella raccolta vengono impostate direttamente nel [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Text/) e [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Detail/) le proprietà del `TextCell`.

### <a name="custom-cells"></a>Celle personalizzate

In XAML è possibile impostare un [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) per il `DataTemplate` e quindi definire un struttura ad albero visuale personalizzato come il [ `View` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ViewCell.View/) proprietà di `ViewCell`. (`View` è la proprietà content del `ViewCell` pertanto `ViewCell.View` tag non sono necessari.) Il [ **CustomNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) illustra questa tecnica:

[![Schermata tripla dell'elenco dei colori personalizzata denominata](images/ch19fg11-small.png "personalizzato denominato elenco di colore")](images/ch19fg11-large.png "personalizzato denominato elenco di colori")

Ottenere il ridimensionamento di destra per tutte le piattaforme, può essere complessa. Il [ `RowHeight` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RowHeight/) proprietà è utile, ma in alcuni casi sarà necessario ricorrere al [ `HasUnevenRows` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.HasUnevenRows/) proprietà, è meno efficiente, ma impone il `ListView` di ridimensionare le righe. Per iOS e Android, è necessario utilizzare una di queste due proprietà per ottenere il ridimensionamento delle righe appropriato.

### <a name="grouping-the-listview-items"></a>Raggruppamento di elementi di ListView

`ListView` supporta il raggruppamento degli elementi e la navigazione tra tali gruppi. Il `ItemsSource` proprietà deve essere impostata su una raccolta di raccolte: l'oggetto che `ItemsSource` è impostata su deve implementare `IEnumerable`, e ogni elemento nella raccolta deve implementare anche `IEnumerable`. Ogni gruppo deve includere due proprietà: una descrizione del gruppo e l'abbreviazione di tre lettere.

Il [ `NamedColorGroup` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria crea sette gruppi di `NamedColor` oggetti. Il [ **ColorGroupList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) esempio viene illustrato come utilizzare questi gruppi con il [ `IsGroupingEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsGroupingEnabled/) proprietà di `ListView` impostato su `true`e il [ `GroupDisplayBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupDisplayBinding/) e [ `GroupShortNameBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupShortNameBinding/) le proprietà associate alle proprietà in ogni gruppo.

### <a name="custom-group-headers"></a>Intestazioni di gruppo personalizzato

È possibile creare intestazioni personalizzate per il `ListView` gruppi sostituendo il `GroupDisplayBinding` proprietà con il [ `GroupHeaderTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupHeaderTemplate/) che definisce un modello per le intestazioni.

### <a name="listview-and-interactivity"></a>ListView e interattività

In genere un'applicazione ottiene l'interazione dell'utente con un `ListView` associando un gestore per il `ItemSelected` o [ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/) evento, oppure tramite l'impostazione di un data binding nel `SelectedItem` proprietà. Ma alcuni tipi di cella (`EntryCell` e `SwitchCell`) consentono l'interazione dell'utente ed è anche possibile creare celle personalizzate che sono interagire con l'utente. Il [ **InteractiveListView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crea 100 istanze di [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) e consente all'utente di modificare ogni colore utilizzando una combinazione di tre `Slider` elementi. Il programma si avvale anche del [ `ColorToContrastColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) nel [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView e MVVM

`ListView` svolge un ruolo importante in scenari MVVM. Ogni volta che un `IEnumerable` raccolta esiste in un elemento ViewModel, spesso è associato a un `ListView`. Inoltre, gli elementi nella raccolta spesso implementano `INotifyPropertyChanged` per associare le proprietà in un modello.

### <a name="a-collection-of-viewmodels"></a>Una raccolta di ViewModel

Per esplorare, il [ **SchoolOfFineArts** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) libreria crea diverse classi in base a un [file di dati XML](http://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e immagini di studenti fittizi fittizio della scuola.

Il [ `Student` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) deriva dalla classe [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). Il [ `StudentBody` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) classe è una raccolta di `Student` degli oggetti e deriva da `ViewModelBase`. Il [ `SchoolViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) Scarica il file XML e l'assemblaggio di tutti gli oggetti.

Il [ **StudentList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) programma utilizza una `ImageCell` per visualizzare gli studenti e le immagini in un `ListView`:

[![La schermata dell'elenco di studenti](images/ch19fg18-small.png "elenco studente")](images/ch19fg18-large.png "studente elenco")

Il [ **ListViewHeader** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) esempio aggiunge un [ `Header` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.Header/) proprietà ma vengono visualizzati solo i in Android.

### <a name="selection-and-the-binding-context"></a>Selezione e il contesto di associazione

Il [ **SelectedStudentDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) programma associa il `BindingContext` di un `StackLayout` per il `SelectedItem` proprietà del `ListView`. Ciò consente al programma di visualizzare informazioni dettagliate sugli studenti selezionato.

### <a name="context-menus"></a>Menu di scelta rapida

Una cella è possibile definire un menu di scelta rapida che viene implementato in modo specifico della piattaforma. Per creare questo menu, aggiungere [ `MenuItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/) oggetti per il [ `ContextActions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Cell.ContextActions/) proprietà del `Cell`.

`MenuItem` definisce le proprietà di cinque:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Text/) di tipo `string`
- [`Icon`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Icon/) di tipo `FileImageSource`
- [`IsDestructive`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.IsDestructive/) di tipo `bool`
- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Command/) di tipo `ICommand`
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.CommandParameter/) di tipo `object`

Il `Command` e `CommandParameter` proprietà implicano che l'elemento ViewModel per ogni elemento contiene i metodi per eseguire i comandi di menu desiderato. In scenari non MVVM `MenuItem` definisce inoltre un [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.MenuItem.Clicked/) evento.

Il [ **CellContextMenu** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) illustra questa tecnica. Il `Command` proprietà di ogni `MenuItem` è associato a una proprietà di tipo `ICommand` nel `Student` classe. Impostare il `IsDestructive` proprietà `true` per un `MenuItem` che rimuove o si elimina l'oggetto selezionato.

### <a name="varying-the-visuals"></a>Gli oggetti visivi diversi

In alcuni casi è opportuno piccole variazioni in oggetti visivi degli elementi di `ListView` basata su una proprietà. Ad esempio, quando Media risultati valutazione uno studente scende di sotto 2.0, il [ **ColorCodedStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) esempio, tale nome viene visualizzato in rosso.
Questa operazione viene eseguita tramite l'utilizzo di un convertitore di valori di associazione, [ `ThresholdToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)nella [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria.

### <a name="refreshing-the-content"></a>Aggiornamento del contenuto

Il `ListView` supporta un movimento di menu a discesa per aggiornarne i dati. Il programma è necessario impostare il [ `IsPullToRefresh` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsPullToRefreshEnabled/) proprietà `true` per abilitare questa opzione. Il `ListView` risponde all'azione di menu a discesa impostando il relativo [ `IsRefreshing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsRefreshing/) proprietà `true`e generando il [ `Refreshing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.Refreshing/) evento e (per gli scenari MVVM) la chiamata il `Execute` metodo del relativo [ `RefreshCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RefreshCommand/) proprietà.

Gestione del codice il `Refresh` evento o `RefreshCommand` eventualmente Aggiorna i dati visualizzati per il `ListView` e imposta `IsRefreshing` al `false`.

Il [ **feed RSS** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) esempio viene illustrato come utilizzare un [ `RssFeedViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) che implementa `RefreshCommand` e `IsRefreshing` proprietà per l'associazione dati.

## <a name="the-tableview-and-its-intents"></a>Il TableView e i relativi tipi

Mentre il `ListView` in genere vengono visualizzate più istanze dello stesso tipo, il [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) in genere mira a offrire un'interfaccia utente per più proprietà di vario tipo. Ogni elemento è associato un proprio [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) derivati per la visualizzazione della proprietà o fornire un'interfaccia utente.

### <a name="properties-and-hierarchies"></a>Proprietà e le gerarchie

`TableView` definisce solo quattro proprietà:

- [`Intent`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Intent/) di tipo [ `TableIntent` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableIntent/), un'enumerazione
- [`Root`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Root/) di tipo [ `TableRoot` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/), la proprietà di contenuto `TableView`
- [`RowHeight`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.RowHeight/) di tipo `int`
- [`HasUnevenRows`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.HasUnevenRows/) di tipo `bool`

Il `TableIntent` enumerazione indica come si prevede di usare il `TableView`:

- [`Data`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Data/)
- [`Form`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Form/)
- [`Settings`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Settings/)
- [`Menu`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Menu/)

Questi membri suggeriscono anche alcuni utilizzi il `TableView`.

Altre classi sono coinvolti nella definizione di una tabella:

- [`TableSectionBase`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSectionBase/) è una classe astratta che deriva da `BindableObject` e definisce un [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TableSectionBase.Title/) proprietà

- [`TableSectionBase<T>`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSectionBase%3CT%3E/) è una classe astratta che deriva da `TableSectionBase` e implementa `IList<T>` e `INotifyCollectionChanged`

- [`TableSection`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSection/) deriva da `TableSectionBase<Cell>`

- [`TableRoot`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/) deriva da `TableSectionBase<TableSection>`

In breve, `TableView` ha un `Root` proprietà che è impostata su un `TableRoot` oggetto, che è una raccolta di `TableSection` oggetti, ognuno dei quali è una raccolta di `Cell` oggetti. Una tabella con più sezioni e ogni sezione contiene più celle. La tabella stessa può avere un titolo e ogni sezione può avere un titolo. Sebbene `TableView` si avvalgono di `Cell` derivati, non risulta utilizzare `DataTemplate`.

### <a name="a-prosaic-form"></a>Un form semplice

Il [ **EntryForm** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) esempio definisce un [ `PersonalInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) modello di visualizzazione, un'istanza di cui diventa il `BindingContext` del `TableView`. Ogni `Cell` derivati nel relativo `TableSection` possono quindi essere binding alle proprietà del `PersonalInformation` classe.

### <a name="custom-cells"></a>Celle personalizzate

Il [ **ConditionalCells** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) esempio espande **EntryForm**. Il [ `ProgrammerInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) classe include una proprietà booleana che determina l'applicabilità di due proprietà aggiuntive. Per queste due proprietà aggiuntive, il programma utilizza una classe personalizzata `PickerCell` in base a un [PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) e [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) nel [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria.

Sebbene il `IsEnabled` le proprietà dei due `PickerCell` gli elementi vengono associati per la proprietà booleana `ProgrammerInformation`, questa tecnica non sembra funzionare, che richiede l'esempio successivo.

### <a name="conditional-sections"></a>Sezioni condizionali

Il [ **ConditionalSection** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) esempio inserisce i due elementi che sono subordinati la selezione dell'elemento in una funzione booleana `TableSection`. Il file code-behind rimuove questa sezione di `TableView` o aggiunto nuovamente in base alla proprietà booleana.

### <a name="a-tableview-menu"></a>Un menu TableView

Un altro uso di un `TableView` è un menu. Il [ **oggetti MenuCommand** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) esempio viene illustrato un menu che consente di spostare un po' `BoxView` sullo schermo.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 19 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Esempi di capitolo 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
