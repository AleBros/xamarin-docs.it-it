---
title: Riepilogo del capitolo 25. Tipi di pagina
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 25. Tipi di pagina'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 148388b80137bd335bbb977ea230726da1f4a32d
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995885"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Riepilogo del capitolo 25. Tipi di pagina

Finora si sono visto due classi che derivano da `Page`: `ContentPage` e `NavigationPage`. Questo capitolo vengono illustrati altri due controlli:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gestisce due pagine, un master e un corpo
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) gestisce più pagine di figlio a cui accede tramite schede visualizzate

Questi tipi di pagina offrono le opzioni di navigazione più sofisticate rispetto al `NavagationPage` descritti in [capitolo 24. Pagina spostamento](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Master e dettaglio

Il [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) definisce due proprietà di tipo `Page`: [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) e [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail). In genere è ognuna di queste proprietà per impostare un `ContentPage`. Il `MasterDetailPage` consente di visualizzare e passare tra queste due pagine.

Esistono due modi fondamentali per passare tra queste due pagine:

- *suddividere* in cui il master e dettaglio sono side-by-side
- *nel popover* pagina in cui la pagina dei dettagli copre o parzialmente coperto master

Esistono diverse varianti del *nel popover* approccio (*diapositiva*, *si sovrappongono*, e *swap*), ma si tratta in genere piattaforma dipendenti. È possibile impostare il [ `MasterDetailBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) proprietà della `MasterDetailPage` a un membro del [ `MasterBehavior` ](xref:Xamarin.Forms.MasterBehavior) enumerazione:

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

Tuttavia, questa proprietà ha effetto sui telefoni. I telefoni hanno sempre un comportamento nel popover. Solo i Tablet e desktop windows può avere un comportamento di divisione.

### <a name="exploring-the-behaviors"></a>Analisi dei comportamenti

Il [ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) esempio consente di sperimentare il comportamento predefinito in dispositivi diversi. Il programma contiene due `ContentPage` derivati per il master e di dettaglio (con un `Title` proprietà è impostata su entrambi) e un'altra classe che deriva da `MasterDetailPage` che li combina. La pagina dei dettagli è racchiuso tra parentesi un `NavigationPage` perché il programma UWP non funzionerebbe senza di essa.

Le piattaforme Windows 8.1 e Windows Phone 8.1 è necessario che una mappa di bit sia impostata il `Icon` proprietà della pagina master.

### <a name="back-to-school"></a>Si torna a scuola

Il [ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) esempio adotta un approccio leggermente diverso per costruire il programma per studenti da visualizzare il [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) libreria.

Il `Master` e `Detail` delle proprietà sono definite con strutture ad albero visuali nel [SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) file, che deriva da `MasterDetailPage`. Questa disposizione consente le associazioni di dati da impostare tra le pagine master e di dettaglio.

Che file XAML imposta anche il [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) proprietà della `MasterDetailPage` a `True`. In questo modo la pagina master da visualizzare all'avvio. Per impostazione predefinita viene visualizzata la pagina di dettaglio. Il [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) file imposta `IsPresented` al `false` quando viene selezionato un elemento dal `ListView` nella pagina master. Verrà quindi visualizzata la pagina di dettaglio:

[![Schermata di tripla di dettaglio e dell'istituto di istruzione](images/ch25fg09-small.png "pagina dei dettagli da un MasterDetailPage")](images/ch25fg09-large.png#lightbox "pagina dei dettagli da un MasterDetailPage")

### <a name="your-own-user-interface"></a>Interfaccia utente personalizzata

Sebbene xamarin. Forms fornisce un'interfaccia utente per il passaggio tra le visualizzazioni master e di dettaglio, è possibile fornire il proprio. A tale scopo:

- Impostare il [ `IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) proprietà `false` disabilitare scorrendo rapidamente
- Eseguire l'override di [ `ShouldShowToolbarButton` ](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) metodo e restituire `false` per nascondere i pulsanti della barra degli strumenti in Windows 8.1 e Windows Phone 8.1.

È quindi necessario fornire un mezzo per spostarsi tra le pagine master e di dettaglio, ad esempio illustrati il [ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) esempio.

Il [ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) esempio viene illustrato un altro approccio tramite un `TapGestureRecognizer` nelle pagine master e di dettaglio.

## <a name="tabbedpage"></a>TabbedPage

Il [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) è una raccolta di pagine in cui è possibile alternare usando le schede. Deriva da `MultiPage<Page>` e non definisce proprietà pubbliche o metodi propri. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1), tuttavia, definire una proprietà:

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) proprietà di tipo `IList<T>`

Ciò riempire `Children` insieme con gli oggetti della pagina.

Un altro approccio è possibile definire le `TabbedPage` figli in modo analogo un `ListView` usando queste due proprietà che generano automaticamente le pagine a schede:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) di tipo `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) di tipo `DataTemplate`

Tuttavia, questo approccio non funziona bene in iOS quando la raccolta contiene più di alcuni elementi.

`MultiPage<T>` definisce altre due proprietà che consentono di tenere traccia di quale pagina è attualmente visualizzata:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) di tipo `T`, che fa riferimento alla pagina
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) typu `Object`, che fa riferimento all'oggetto nel `ItemsSource` raccolta

`MultiPage<T>` definisce inoltre due eventi:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) Quando il `ItemsSource` le modifiche alla raccolta
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) quando cambia la pagina visualizzata

### <a name="discrete-tab-pages"></a>Le schede discreti

Il [ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) esempio è costituito da tre pagine a schede che consentono di visualizzare i colori in tre modi diversi. Ogni scheda è un `ContentPage` derivata da e quindi il `TabbedPage` derivato [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combina tre pagine.

Per ogni pagina che viene visualizzato in una `TabbedPage`, il `Title` è necessario specificare il testo nella scheda proprietà e Store di Apple è necessario utilizzare un'icona, in modo che il `Icon` viene impostata per iOS:

[![Schermata triplo dei colori a schede discreti](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

Il [ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) esempio dispone di una home page in cui sono elencati tutti gli studenti. Quando viene toccato uno studente, in tal modo un `TabbedPage` derivato, [ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), che include tre `ContentPage` oggetti nella relativa struttura ad albero visuale, uno dei quali consente di immettere alcune note per studente.

### <a name="using-an-itemtemplate"></a>Usa un ItemTemplate

Il [ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) nell'esempio il [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria. Il [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) file imposta il `DataTemplate` proprietà della `TabbedPage` a un struttura ad albero visuale a partire `ContentPage` che contiene le associazioni alle proprietà di `NamedColor` (tra cui un'associazione per il `Title` proprietà).

Si tratta tuttavia causare problemi con iOS. Possono essere visualizzati solo alcuni degli elementi e non vi è alcuna tecnica efficace per concedere loro le icone.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 25 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Capitolo 25 campioni](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Pagina master-Detail](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Pagina a schede](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
