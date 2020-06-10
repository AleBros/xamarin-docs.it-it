---
title: "Riepilogo del capitolo 25. Varietà di pagine "Descrizione:" creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 25. Varietà di pagine "ms. prod: Novell MS. Technology: Novell-Forms ms. AssetID: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89 Author: davidbritch ms. Author: dabritch ms. Date: 11/07/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-25-page-varieties"></a>Riepilogo del capitolo 25. Tipi di pagina

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

Finora sono state visualizzate due classi che derivano da `Page` : `ContentPage` e `NavigationPage` . Questo capitolo presenta altri due elementi:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)gestisce due pagine, un master e un dettaglio
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)gestisce più pagine figlio a cui si accede tramite schede

Questi tipi di pagina offrono opzioni di navigazione più sofisticate rispetto `NavagationPage` a quanto descritto nel [capitolo 24. Navigazione tra pagine](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Master e dettagli

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)Definisce due proprietà di tipo `Page` : [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) e [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) . In genere, queste proprietà vengono impostate su `ContentPage` . `MasterDetailPage`Consente di visualizzare e alternare le due pagine.

Esistono due modi fondamentali per spostarsi tra le due pagine seguenti:

- *dividere* i punti in cui il master e i dettagli sono affiancati
- *popover* in cui la pagina dei dettagli copre o parzialmente la pagina master

Esistono diverse varianti dell'approccio di *popopov* (*scorrimento*, *sovrapposizione*e *scambio*), ma sono generalmente dipendenti dalla piattaforma. È possibile impostare la [`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) proprietà di `MasterDetailPage` su un membro dell' [`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior) enumerazione:

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

Tuttavia, questa proprietà non ha alcun effetto sui telefoni. I telefoni hanno sempre un comportamento di popover. Solo i tablet e le finestre desktop possono avere un comportamento di suddivisione.

### <a name="exploring-the-behaviors"></a>Esplorazione dei comportamenti

L'esempio [**MasterDetailBehaviors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) consente di sperimentare il comportamento predefinito in dispositivi diversi. Il programma contiene due `ContentPage` derivati distinti per il master e i dettagli (con una `Title` proprietà impostata su entrambi) e un'altra classe che deriva da `MasterDetailPage` che li combina. La pagina dei dettagli è racchiusa in un oggetto `NavigationPage` perché il programma UWP non funzionerà senza di esso.

Per le piattaforme Windows 8.1 e Windows Phone 8,1 è necessario impostare una bitmap sulla `Icon` proprietà della pagina master.

### <a name="back-to-school"></a>Di nuovo a scuola

L'esempio [**SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) adotta un approccio leggermente diverso per costruire il programma per visualizzare gli studenti dalla libreria [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) .

Le `Master` `Detail` proprietà e sono definite con alberi visivi nel file [SchoolAndDetailPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) , che deriva da `MasterDetailPage` . Questa disposizione consente di impostare i data binding tra le pagine master e i dettagli.

Il file XAML imposta anche la [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) proprietà di `MasterDetailPage` su `True` . Questa operazione determina la visualizzazione della pagina master all'avvio; per impostazione predefinita, viene visualizzata la pagina dei dettagli. Il file [SchoolAndDetailPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) imposta `IsPresented` su `false` quando viene selezionato un elemento dalla `ListView` nella pagina master. Viene quindi visualizzata la pagina dei dettagli:

[![Schermata tripla della scuola e dettagli](images/ch25fg09-small.png "Pagina dei dettagli da un MasterDetailPage")](images/ch25fg09-large.png#lightbox "Pagina dei dettagli da un MasterDetailPage")

### <a name="your-own-user-interface"></a>Interfaccia utente personalizzata

Sebbene Xamarin.Forms fornisca un'interfaccia utente per lo spostamento tra le visualizzazioni master e dettagli, è possibile fornire i propri. A tale scopo, procedere nel seguente modo:

- Impostare la [`IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) proprietà su `false` per disabilitare il scorrimento
- Eseguire l'override del [`ShouldShowToolbarButton`](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) metodo e restituire `false` per nascondere i pulsanti della barra degli strumenti in Windows 8.1 e Windows Phone 8,1.

È quindi necessario fornire un mezzo per passare tra le pagine master e i dettagli, come illustrato nell'esempio [**ColorsDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) .

Nell'esempio [**MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) viene illustrato un altro approccio utilizzando un `TapGestureRecognizer` nelle pagine master e di dettaglio.

## <a name="tabbedpage"></a>TabbedPage

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)È una raccolta di pagine che è possibile passare tra l'utilizzo delle schede. Deriva da `MultiPage<Page>` e non definisce proprietà o metodi pubblici. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1)Tuttavia, definisce una proprietà:

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children)Proprietà di tipo`IList<T>`

Questa raccolta viene compilata `Children` con gli oggetti Page.

Un altro approccio consente di definire gli `TabbedPage` elementi figlio in modo molto simile a `ListView` utilizzando queste due proprietà che generano automaticamente le pagine a schede:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource)di tipo`IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)di tipo`DataTemplate`

Tuttavia, questo approccio non funziona bene in iOS quando la raccolta contiene più di alcuni elementi.

`MultiPage<T>`definisce altre due proprietà che consentono di tenere traccia della pagina attualmente visualizzata:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage)di tipo `T` , che fa riferimento alla pagina
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem)di tipo `Object` , che fa riferimento all'oggetto nella `ItemsSource` raccolta

`MultiPage<T>`definisce anche due eventi:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged)Quando la `ItemsSource` raccolta viene modificata
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged)Quando la pagina visualizzata cambia

### <a name="discrete-tab-pages"></a>Pagine di schede discrete

L'esempio [**DiscreteTabbedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) è costituito da tre pagine a schede che visualizzano i colori in tre modi diversi. Ogni scheda è un `ContentPage` derivato, quindi il `TabbedPage` [DiscreteTabbedColorsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) derivato combina le tre pagine.

Per ogni pagina visualizzata in un oggetto `TabbedPage` , la `Title` proprietà è necessaria per specificare il testo nella scheda e l'Apple Store richiede anche che venga usata un'icona, quindi la `Icon` proprietà è impostata per iOS:

[![Schermata tripla dei colori a schede discrete](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

L'esempio [**StudentNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) include un Home page che elenca tutti gli studenti. Quando viene toccato uno studente, questo passa a un `TabbedPage` derivato, [`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml) , che incorpora tre `ContentPage` oggetti nella relativa struttura ad albero visuale, uno dei quali consente di immettere alcune note per lo studente.

### <a name="using-an-itemtemplate"></a>Uso di un ItemTemplate

Nell'esempio [**MultiTabbedColor**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) viene utilizzata la [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Il file [MultiTabbedColorsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) imposta la `DataTemplate` proprietà di `TabbedPage` su una struttura ad albero visuale che inizia con `ContentPage` che contiene le associazioni alle proprietà di `NamedColor` (inclusa un'associazione alla `Title` proprietà).

Tuttavia, ciò è problematico in iOS. È possibile visualizzare solo alcuni degli elementi e non esiste un modo efficace per assegnare loro icone.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 25 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Esempi del capitolo 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Pagina master-dettagli](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Pagina a schede](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
