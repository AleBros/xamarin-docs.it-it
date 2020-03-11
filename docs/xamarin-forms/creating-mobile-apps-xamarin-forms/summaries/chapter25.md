---
title: Riepilogo del capitolo 25. Tipi di pagina
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 25. Tipi di pagina'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760539"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Riepilogo del capitolo 25. Tipi di pagina

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

Finora sono state visualizzate due classi che derivano da `Page`: `ContentPage` e `NavigationPage`. Questo capitolo vengono illustrati altri due controlli:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gestisce due pagine, un master e un dettaglio
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) gestisce più pagine figlio a cui si accede tramite schede

Questi tipi di pagina offrono opzioni di navigazione più sofisticate rispetto a `NavagationPage` illustrate nel [capitolo 24. Navigazione tra pagine](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Master e dettaglio

Il [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) definisce due proprietà di tipo `Page`: [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) e [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail). In genere, queste proprietà vengono impostate su un `ContentPage`. Il `MasterDetailPage` Visualizza e passa tra queste due pagine.

Esistono due modi fondamentali per passare tra queste due pagine:

- *dividere* i punti in cui il master e i dettagli sono affiancati
- *popover* in cui la pagina dei dettagli copre o parzialmente la pagina master

Esistono diverse varianti dell'approccio di *popopov* (*scorrimento*, *sovrapposizione*e *scambio*), ma sono generalmente dipendenti dalla piattaforma. È possibile impostare la proprietà [`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) di `MasterDetailPage` su un membro dell'enumerazione [`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior) :

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

Tuttavia, questa proprietà ha effetto sui telefoni. I telefoni hanno sempre un comportamento nel popover. Solo i Tablet e desktop windows può avere un comportamento di divisione.

### <a name="exploring-the-behaviors"></a>Analisi dei comportamenti

L'esempio [**MasterDetailBehaviors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) consente di sperimentare il comportamento predefinito in dispositivi diversi. Il programma contiene due derivati `ContentPage` distinti per il master e i dettagli (con una proprietà `Title` impostata su entrambi) e un'altra classe che deriva da `MasterDetailPage` che li combina. La pagina dei dettagli è racchiusa in un `NavigationPage` perché il programma UWP non funzionerà senza di esso.

Per le piattaforme Windows 8.1 e Windows Phone 8,1 è necessario impostare una bitmap sulla proprietà `Icon` della pagina master.

### <a name="back-to-school"></a>Di nuovo a scuola

L'esempio [**SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) adotta un approccio leggermente diverso per costruire il programma per visualizzare gli studenti dalla libreria [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) .

Le proprietà `Master` e `Detail` sono definite con alberi visivi nel file [SchoolAndDetailPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) , che deriva da `MasterDetailPage`. Questa disposizione consente le associazioni di dati da impostare tra le pagine master e di dettaglio.

Il file XAML imposta anche la proprietà [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) di `MasterDetailPage` su `True`. In questo modo la pagina master da visualizzare all'avvio. Per impostazione predefinita viene visualizzata la pagina di dettaglio. Il file [SchoolAndDetailPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) imposta `IsPresented` su `false` quando viene selezionato un elemento dalla `ListView` nella pagina master. Verrà quindi visualizzata la pagina di dettaglio:

[![Schermata tripla della scuola e dettagli](images/ch25fg09-small.png "Pagina dei dettagli da un MasterDetailPage")](images/ch25fg09-large.png#lightbox "Pagina dei dettagli da un MasterDetailPage")

### <a name="your-own-user-interface"></a>Interfaccia utente personalizzata

Sebbene xamarin. Forms fornisce un'interfaccia utente per il passaggio tra le visualizzazioni master e di dettaglio, è possibile fornire il proprio. A tale scopo, procedere come indicato di seguito:

- Impostare la proprietà [`IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) su `false` per disabilitare il scorrimento
- Eseguire l'override del metodo [`ShouldShowToolbarButton`](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) e restituire `false` per nascondere i pulsanti della barra degli strumenti in Windows 8.1 e Windows Phone 8,1.

È quindi necessario fornire un mezzo per passare tra le pagine master e i dettagli, come illustrato nell'esempio [**ColorsDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) .

Nell'esempio [**MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) viene illustrato un altro approccio utilizzando un `TapGestureRecognizer` nelle pagine master e di dettaglio.

## <a name="tabbedpage"></a>TabbedPage

Il [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) è una raccolta di pagine che è possibile passare tra le schede. Deriva da `MultiPage<Page>` e non definisce proprietà o metodi pubblici. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1), tuttavia, definisce una proprietà:

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) proprietà di tipo `IList<T>`

Questa raccolta di `Children` viene riempita con gli oggetti Page.

Un altro approccio consente di definire gli elementi figlio `TabbedPage` in modo molto simile a una `ListView` usando queste due proprietà che generano automaticamente le pagine a schede:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) di tipo `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) di tipo `DataTemplate`

Tuttavia, questo approccio non funziona bene in iOS quando la raccolta contiene più di alcuni elementi.

`MultiPage<T>` definisce altre due proprietà che consentono di tenere traccia della pagina attualmente visualizzata:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) di tipo `T`, che fa riferimento alla pagina
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) di tipo `Object`, che fa riferimento all'oggetto nella raccolta di `ItemsSource`

`MultiPage<T>` definisce anche due eventi:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) quando viene modificata la raccolta di `ItemsSource`
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) quando cambia la pagina visualizzata

### <a name="discrete-tab-pages"></a>Le schede discreti

L'esempio [**DiscreteTabbedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) è costituito da tre pagine a schede che visualizzano i colori in tre modi diversi. Ogni scheda è un `ContentPage` derivato, quindi il derivato `TabbedPage` [DiscreteTabbedColorsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combina le tre pagine.

Per ogni pagina visualizzata in una `TabbedPage`, la proprietà `Title` è necessaria per specificare il testo nella scheda e l'Apple Store richiede anche che venga usata un'icona, quindi la proprietà `Icon` è impostata per iOS:

[![Schermata tripla dei colori a schede discrete](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

L'esempio [**StudentNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) include un Home page che elenca tutti gli studenti. Quando viene toccato uno studente, questo passa a un `TabbedPage` derivato, [`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), che incorpora tre oggetti `ContentPage` nella relativa struttura ad albero visuale, uno dei quali consente di immettere alcune note per lo studente.

### <a name="using-an-itemtemplate"></a>Usa un ItemTemplate

Nell'esempio [**MultiTabbedColor**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) viene utilizzata la classe [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Il file [MultiTabbedColorsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) imposta la proprietà `DataTemplate` di `TabbedPage` su una struttura ad albero visuale che inizia con `ContentPage` che contiene le associazioni alle proprietà di `NamedColor` (incluso un binding alla proprietà `Title`).

Si tratta tuttavia causare problemi con iOS. Possono essere visualizzati solo alcuni degli elementi e non vi è alcuna tecnica efficace per concedere loro le icone.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 25 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Esempi del capitolo 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Pagina master-dettagli](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Pagina a schede](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
