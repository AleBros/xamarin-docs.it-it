---
title: Riassunto del capitolo 25. Tipi di pagina
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 25. Tipi di pagina'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760539"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Riassunto del capitolo 25. Tipi di pagina

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

Finora avete visto due classi che `Page` `ContentPage` derivano da : e `NavigationPage`. Questo capitolo ne presenta altri due:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)gestisce due pagine, una mastro e un dettaglio
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)gestisce più pagine figlio accessibili tramite schede

Questi tipi di pagina forniscono `NavagationPage` opzioni di navigazione più sofisticate rispetto a quelle descritte nel [capitolo 24. Navigazione tra le pagine](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Master e dettaglio

L'oggetto [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) definisce `Page`due [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)proprietà di tipo : e . In genere ognuna di `ContentPage`queste proprietà viene impostata su un oggetto . Il `MasterDetailPage` display e passa da una pagina all'altra.

Esistono due modi fondamentali per passare da una pagina all'altra:

- *diviso* dove il master e il dettaglio sono fianco a fianco
- *popover* in cui la pagina di dettaglio copre o parzialmente copre la pagina master

Esistono diverse varianti dell'approccio *popover* (*slide*, *overlap*e *swap*), ma queste sono generalmente dipendenti dalla piattaforma. È possibile [`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) impostare `MasterDetailPage` la proprietà [`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior) di su un membro dell'enumerazione:

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

Tuttavia, questa proprietà non ha alcun effetto sui telefoni. I telefoni hanno sempre un comportamento popover. Solo i tablet e le finestre del desktop possono avere un comportamento diviso.

### <a name="exploring-the-behaviors"></a>Esplorazione dei comportamenti

L'esempio [**MasterDetailBehaviors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) consente di sperimentare il comportamento predefinito su dispositivi diversi. Il programma contiene `ContentPage` due derivati separati per `Title` il master e il dettaglio (con una proprietà impostata su entrambi) e un'altra classe che deriva da `MasterDetailPage` che li combina. La pagina dei dettagli `NavigationPage` è racchiusa in un perché il programma UWP non funzionerebbe senza di essa.

Le piattaforme Windows 8.1 e Windows Phone 8.1 `Icon` richiedono che una bitmap sia impostata sulla proprietà della pagina master.

### <a name="back-to-school"></a>Di nuovo a scuola

[**L'esempio SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) adotta un approccio leggermente diverso per la costruzione del programma per visualizzare gli studenti della libreria [**SchoolOfFineArt.The SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) sample takes a some somesome different approach to constructing the program to display students from the SchoolOfFineArt library.

Le `Master` `Detail` proprietà e vengono definite con strutture ad albero visive nel `MasterDetailPage`file [SchoolAndDetailPage.xaml,](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) che deriva da . Questa disposizione consente di impostare associazioni dati tra le pagine master e di dettaglio.

Tale file XAML [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) imposta `MasterDetailPage` anche `True`la proprietà di su . In questo modo la pagina master da visualizzare all'avvio; per impostazione predefinita viene visualizzata la pagina dei dettagli. Il [file](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) `IsPresented` SchoolAndDetailPage.xaml.cs `false` imposta quando un `ListView` elemento viene selezionato dalla nella pagina master. Viene quindi visualizzata la pagina dei dettagli:

[![Triplo screenshot di Scuola e Dettaglio](images/ch25fg09-small.png "Pagina di dettaglio da un MasterDetailPageDetail Page from a MasterDetailPage")](images/ch25fg09-large.png#lightbox "Pagina di dettaglio da un MasterDetailPageDetail Page from a MasterDetailPage")

### <a name="your-own-user-interface"></a>La tua interfaccia utente

Sebbene Xamarin.Forms fornisca un'interfaccia utente per passare tra le visualizzazioni master e di dettaglio, è possibile fornire le proprie. A tale scopo, procedere come indicato di seguito:

- Impostare [`IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) la `false` proprietà su per disabilitare lo scorrimento rapido
- Eseguire [`ShouldShowToolbarButton`](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) l'override `false` del metodo e tornare per nascondere i pulsanti della barra degli strumenti in Windows 8.1 e Windows Phone 8.1.

È quindi necessario fornire un mezzo per passare tra le pagine master e di dettaglio, come illustrato dal [**ColorsDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) esempio.

[**Nell'esempio MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) viene `TapGestureRecognizer` illustrato un altro approccio usando un oggetto nella pagina master e nelle pagine di dettaglio.

## <a name="tabbedpage"></a>TabbedPage

Si [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) tratta di una raccolta di pagine tra cui è possibile passare utilizzando le schede. Deriva da `MultiPage<Page>` e non definisce proprietà o metodi pubblici propri. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1), tuttavia, definisce una proprietà:

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children)proprietà di tipo`IList<T>`

Riempire questa `Children` raccolta con gli oggetti della pagina.

Un altro approccio `TabbedPage` consente di `ListView` definire gli elementi figlio in modo molto simile a utilizzando queste due proprietà che generano automaticamente le pagine a schede:Another approach allows you to define the children much like a using these two properties that generate the Tabbed pages automatically:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource)di tipo`IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)di tipo`DataTemplate`

Tuttavia, questo approccio non funziona bene in iOS quando la raccolta contiene più di alcuni elementi.

`MultiPage<T>`definisce altre due proprietà che consentono di tenere traccia della pagina attualmente visualizzata:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage)di `T`tipo , facendo riferimento alla pagina
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem)di `Object`tipo , riferendosi all'oggetto nell'insieme `ItemsSource`

`MultiPage<T>`definisce anche due eventi:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged)quando `ItemsSource` la raccolta cambia
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged)quando la pagina visualizzata cambia

### <a name="discrete-tab-pages"></a>Schede discrete

Il [**DiscreteTabbedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) esempio è costituito da tre pagine a schede che visualizzano i colori in tre modi diversi. Ogni scheda `ContentPage` è una derivata e quindi la `TabbedPage` deriva [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combina le tre pagine.

Per ogni pagina visualizzata in un `TabbedPage`oggetto , la `Title` proprietà deve specificare il testo nella scheda e `Icon` l'Apple Store richiede che venga utilizzata anche un'icona, pertanto la proprietà viene impostata per iOS:

[![Tripla schermata dei colori a schede discreti](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

L'esempio [**StudentNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) include una home page che elenca tutti gli studenti. Quando uno studente viene toccato, `TabbedPage` questo [`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml)passa a un `ContentPage` derivato, , che incorpora tre oggetti nella sua struttura ad albero visuale, uno dei quali consente di inserire alcune note per quello studente.

### <a name="using-an-itemtemplate"></a>Utilizzo di un ItemTemplateUsing an ItemTemplate

Nell'esempio [**MultiTabbedColor**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) viene utilizzata la [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe nella libreria [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) Il file [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) `DataTemplate` imposta `TabbedPage` la proprietà di `ContentPage` su una struttura `NamedColor` ad albero visuale `Title` che inizia con che contiene associazioni alle proprietà di (inclusa un'associazione alla proprietà).

Tuttavia, questo è problematico su iOS. Solo alcuni degli elementi possono essere visualizzati, e non c'è un buon modo per dare loro icone.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 25 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Capitolo 25 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Pagina Master-Details](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Pagina a schede](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
