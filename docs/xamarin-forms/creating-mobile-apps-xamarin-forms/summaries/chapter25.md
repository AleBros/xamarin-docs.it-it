---
title: Riepilogo del capitolo 25. Tipi di pagina
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 951ae41763d8338d5adf73fb46ebc6defa64f8f8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-25-page-varieties"></a>Riepilogo del capitolo 25. Tipi di pagina

Finora si sono visto due classi che derivano da `Page`: `ContentPage` e `NavigationPage`. In questo capitolo vengono altri due:

- [`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) gestisce due pagine, uno schema e un dettaglio
- [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) Consente di gestire più pagine figlio accede mediante le schede

Questi tipi di pagina forniscono le opzioni di navigazione più sofisticate rispetto di `NavagationPage` descritti in [capitolo 24. Pagina spostamento](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Master e dettaglio

Il [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) definisce due proprietà di tipo `Page`: [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) e [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/). In genere si imposta ognuna di queste proprietà per un `ContentPage`. Il `MasterDetailPage` Visualizza e passa tra queste due pagine.

Esistono due modi per passare tra queste due pagine:

- *dividere* in cui lo schema e i dettagli sono side-by-side
- *popover* in cui la pagina dettagli copre o parzialmente copre il master per la pagina

Esistono diverse varianti del *popover* approccio (*diapositiva*, *si sovrappongono*, e *scambio*), ma questi sono in genere di piattaforma dipendenti. È possibile impostare il [ `MasterDetailBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) proprietà di `MasterDetailPage` a un membro del [ `MasterBehavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterBehavior/) enumerazione:

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Default/)
- [`Split`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Split/)
- [`SplitOnLandscape`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.SplitOnLandscape/)
- [`SplitOnPortrait`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.SplitOnPortrait/)
- [`Popover`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Popover/)

Tuttavia, questa proprietà ha effetto nei telefoni. Telefoni hanno sempre un comportamento popover. Solo i Tablet e windows desktop può avere un comportamento di divisione.

### <a name="exploring-the-behaviors"></a>I comportamenti di esplorazione

Il [ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) esempio consente di sperimentare il comportamento predefinito in dispositivi diversi. Il programma contiene due `ContentPage` derivati per lo schema e i dettagli (con un `Title` proprietà è impostata su entrambi) e un'altra classe che deriva da `MasterDetailPage` che li combina. La pagina dei dettagli è incluso in un `NavigationPage` perché il programma di piattaforma UWP non funzionerebbe senza di esso.

Le piattaforme Windows 8.1 e Windows Phone 8.1 richiedono che una mappa di bit deve essere impostata sul `Icon` proprietà della pagina master.

### <a name="back-to-school"></a>Ritorno a scuola

Il [ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) esempio adotta un approccio leggermente diverso per il programma per visualizzare gli studenti dalla costruzione di [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) libreria.

Il `Master` e `Detail` proprietà sono definite con strutture ad albero visuali nel [SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) file, che deriva da `MasterDetailPage`. In questo modo le associazioni di dati da impostare tra le pagine master e di dettaglio.

Che file XAML imposta inoltre il [ `IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/) proprietà di `MasterDetailPage` a `True`. In questo modo la pagina master che verrà visualizzato all'avvio. Per impostazione predefinita viene visualizzata la pagina di dettaglio. Il [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) file imposta `IsPresented` a `false` quando è selezionato un elemento dal `ListView` nella pagina master. Quindi verrà visualizzata la pagina di dettaglio:

[![Tripla schermata dei dettagli e dell'istituto di istruzione](images/ch25fg09-small.png "pagina dei dettagli da un MasterDetailPage")](images/ch25fg09-large.png#lightbox "pagina dei dettagli da un MasterDetailPage")

### <a name="your-own-user-interface"></a>Interfaccia utente personalizzata

Sebbene xamarin. Forms fornisce un'interfaccia utente per il passaggio tra le visualizzazioni master e di dettaglio, è possibile fornire la propria. A tale scopo:

- Impostare il [ `IsGestureEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsGestureEnabled/) proprietà `false` per disabilitare il passaggio di
- Eseguire l'override di [ `ShouldShowToolbarButton` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton()/) metodo e restituire `false` per nascondere i pulsanti della barra degli strumenti di Windows 8.1 e Windows Phone 8.1.

È necessario fornire un modo per passare tra le pagine master e di dettaglio, ad esempio illustrati il [ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) esempio.

Il [ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) esempio viene illustrato l'utilizzo di un altro approccio un `TapGestureRecognizer` nelle pagine master e di dettaglio.

## <a name="tabbedpage"></a>TabbedPage

Il [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) è una raccolta di pagine che è possibile passare tra l'utilizzo delle schede. Deriva da `MultiPage<Page>` e non definisce le proprietà pubbliche o metodi propri. [`MultiPage<T>`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/), tuttavia, definire una proprietà:

- [`Children`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/) proprietà di tipo `IList<T>`

Si compila questo `Children` insieme con oggetti pagina.

Un altro approccio consente di definire il `TabbedPage` figli in modo analogo a un `ListView` tramite queste due proprietà che genera automaticamente le pagine a schede:

- [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/) di tipo `IEnumerable`
- [`ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/) di tipo `DataTemplate`

Tuttavia, questo approccio non funziona bene in iOS quando la raccolta contiene più di alcuni elementi.

`MultiPage<T>` definisce le proprietà di altre due che consentono di tenere traccia di pagina è attualmente visualizzata:

- [`CurrentPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.CurrentPage/) di tipo `T`, che fa riferimento alla pagina
- [`SelectedItem`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.SelectedItem/) di tipo `Object`, che fa riferimento all'oggetto di `ItemsSource` raccolta

`MultiPage<T>` definisce inoltre due eventi:

- [`PagesChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.MultiPage%3CT%3E.PagesChanged/) Quando il `ItemsSource` modifiche di raccolte
- [`CurrentPageChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.MultiPage%3CT%3E.CurrentPageChanged/) Quando viene modificata la pagina visualizzata

### <a name="discrete-tab-pages"></a>Le schede discreti

Il [ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) esempio è costituito da tre pagine a schede che consentono di visualizzare i colori in tre modi diversi. Ogni scheda è un `ContentPage` derivato e quindi la `TabbedPage` derivato [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combina tre pagine.

Per ogni pagina visualizzata in un `TabbedPage`, `Title` proprietà è necessaria per specificare il testo della scheda e Store di Apple è necessario utilizzare un'icona, pertanto la `Icon` proprietà è impostata per iOS:

[![Schermata triplo dei colori schede discreti](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

Il [ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) esempio ha una home page che elenca tutti gli studenti. Quando verrà scelti uno studente, passa a un `TabbedPage` derivato, [ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), che include tre `ContentPage` oggetti nella relativa struttura ad albero, una delle quali consente di immettere alcune note per tale studente.

### <a name="using-an-itemtemplate"></a>Utilizzo di un elemento ItemTemplate

Il [ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) esempio viene utilizzata la [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria. Il [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) file imposta il `DataTemplate` proprietà di `TabbedPage` fino all'inizio struttura ad albero visuale con `ContentPage` che contiene le associazioni alle proprietà di `NamedColor` (tra cui un'associazione per il `Title` proprietà).

Tuttavia, questo rappresenta un problema in iOS. Possono essere visualizzati solo alcuni degli elementi e non è valido per consentire loro di icone.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 25 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Esempi di capitolo 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Pagina master-details](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Pagina a schede](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
