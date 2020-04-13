---
title: Riassunto del capitolo 24. Navigazione tra le pagine
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 24. Navigazione tra le pagine'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: fd8e4fc77917fcba9bc61e59ced714ac1cd6fbe9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292010"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Riassunto del capitolo 24. Navigazione tra le pagine

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

Molte applicazioni sono costituite da più pagine tra cui l'utente si sposta. L'applicazione ha sempre una pagina *principale* o *home* page, e da lì l'utente passa ad altre pagine, che vengono mantenute in uno stack per la navigazione indietro. Ulteriori opzioni di navigazione sono trattate nel [**capitolo 25. Varietà di pagine**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Pagine modali e pagine non modali

`VisualElement`definisce [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) una proprietà [`INavigation`](xref:Xamarin.Forms.INavigation)di tipo , che include i due metodi seguenti per passare a una nuova pagina:

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Entrambi i `Page` metodi accettano un'istanza come argomento e restituiscono un `Task` oggetto. I due metodi seguenti consentono di tornare alla pagina precedente:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Se l'interfaccia utente ha il proprio pulsante **Indietro** (come fanno i telefoni Android e Windows) allora non è necessario che l'applicazione chiami questi metodi.

Sebbene questi metodi `VisualElement`siano disponibili da qualsiasi `Navigation` oggetto , `Page` in genere vengono chiamati dalla proprietà dell'istanza corrente.

Le applicazioni in genere utilizzano pagine modali quando all'utente viene richiesto di fornire alcune informazioni nella pagina prima di tornare alla pagina precedente. Le pagine non modali sono talvolta denominate non modali o *gerarchiche.* Nulla nella pagina stessa lo distingue come modale o non modale; è regolato invece dal metodo utilizzato per navigare ad esso. Per funzionare su tutte le piattaforme, una pagina modale deve fornire la propria interfaccia utente per tornare alla pagina precedente.

L'esempio [**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) consente di esplorare la differenza tra pagine non modali e pagine modali. Qualsiasi applicazione che utilizza lo spostamento tra [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) le pagine deve passare `App` la propria home page al costruttore, in genere nella classe del programma. Un bonus è che non è `Padding` più necessario impostare un sulla pagina per iOS.

Si scoprirà che per le pagine [`Title`](xref:Xamarin.Forms.Page.Title) non modali, viene visualizzata la proprietà della pagina. I dispositivi iOS, Android e le piattaforme desktop e tablet Windows forniscono tutti un elemento dell'interfaccia utente per tornare alla pagina precedente. Naturalmente, i dispositivi Android e Windows Phone hanno un pulsante **Indietro** standard per tornare indietro.

Per le pagine `Title` modali, la pagina non viene visualizzata e non viene fornito alcun elemento dell'interfaccia utente per tornare alla pagina precedente. Anche se è possibile utilizzare il pulsante **Indietro** standard di Android e Windows Phone per tornare alla pagina precedente, la pagina modale sulle altre piattaforme deve fornire il proprio meccanismo per tornare indietro.

### <a name="animated-page-transitions"></a>Transizioni di pagina animate

Le versioni alternative dei vari metodi di navigazione vengono `true` fornite con un secondo argomento booleano impostato su se si desidera che la transizione di pagina includa un'animazione:

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsyncPopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

Tuttavia, i metodi standard di spostamento tra le pagine includono l'animazione per impostazione predefinita, pertanto sono validi solo per passare a una determinata pagina all'avvio (come descritto verso la fine di questo capitolo) o quando si fornisce la propria animazione di ingresso (come illustrato nel [**Capitolo22. Animazione**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variazioni visive e funzionali

`NavigationPage`include due proprietà che è possibile impostare `App` quando si crea un'istanza della classe nel metodo:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage`include inoltre quattro proprietà associabili associate che influiscono sulla pagina specifica in cui sono impostate:

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean))E[`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean))E[`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String))e [`GetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) lavorare solo su iOS
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource))e [`GetTitleIcon`](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) lavorare solo su iOS e Android

### <a name="exploring-the-mechanics"></a>Esplorare la meccanica

I metodi di spostamento tra le `await`pagine sono tutti asincroni e devono essere utilizzati con . Il completamento non indica che lo spostamento tra le pagine è stato completato, ma solo che è sicuro esaminare lo stack di spostamento tra le pagine.

Quando una pagina passa a un'altra, la [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) prima pagina riceve in genere una [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) chiamata al relativo metodo e la seconda pagina ottiene una chiamata al relativo metodo. Analogamente, quando una pagina torna a un'altra, la prima pagina ottiene una chiamata al relativo `OnDisappearing` metodo e la seconda pagina riceve in genere una chiamata al relativo `OnAppearing` metodo. L'ordine di queste chiamate (e il completamento dei metodi asincroni che richiama la navigazione) dipende dalla piattaforma. L'uso della parola "generalmente" nelle due istruzioni precedenti è dovuto alla navigazione tra pagine modali Android, in cui queste chiamate al metodo non si verificano.

Inoltre, le `OnAppearing` chiamate `OnDisappearing` ai metodi e non indicano necessariamente lo spostamento tra le pagine.

L'interfaccia `INavigation` include due proprietà della raccolta che consentono di esaminare lo stack di navigazione:The interface includes two collection properties that allow you to examine the navigation stack:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack)di `IReadOnlyList<Page>` tipo per lo stack non modale
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack)di `IReadOnlyList<Page>` tipo per lo stack modale

È più sicuro accedere a questi `Navigation` stack `NavigationPage` dalla proprietà del `App` (che [`MainPage`](xref:Xamarin.Forms.Application.MainPage) dovrebbe essere la proprietà della classe). È sicuro esaminare questi stack solo dopo il completamento dei metodi asincroni di spostamento tra le pagine. La [`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage) proprietà `NavigationPage` di indica la pagina corrente se la pagina corrente è una pagina modale, ma indica invece l'ultima pagina non modale.

L'esempio [**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) consente di esplorare lo spostamento tra le pagine e gli stack e i tipi legali di spostamento tra le pagine:

- Una pagina non modale può passare a un'altra pagina non modale o a una pagina modale
- Una pagina modale può passare solo a un'altra pagina modale

### <a name="enforcing-modality"></a>Applicazione della modalità

Un'applicazione utilizza una pagina modale quando è necessario ottenere alcune informazioni dall'utente. L'utente dovrebbe essere vietato di tornare alla pagina precedente fino a quando non vengono fornite tali informazioni. In iOS, è facile fornire un pulsante **Indietro** e abilitarlo solo quando l'utente ha terminato con la pagina. Ma per i dispositivi Android e Windows [`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) Phone, `true` l'applicazione deve eseguire l'override del metodo e restituire se il programma ha gestito il pulsante Indietro stesso, come illustrato nell'esempio [**ModalEnforcement.But**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) for Android and Windows phone devices, the application should override the method and return if program has handled the **Back** button itself, as demonstrated in the ModalEnforcement sample.

[**Nell'esempio MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) viene illustrato il funzionamento in uno scenario MVVM.

## <a name="navigation-variations"></a>Variazioni di navigazione

Se è possibile passare a più volte a una determinata pagina modale, è necessario conservare le informazioni in modo che l'utente possa modificare le informazioni anziché digitarle di nuovo. È possibile gestire questo mantenendo la particolare istanza della pagina modale, ma un approccio migliore (in particolare su iOS) è mantenere le informazioni in un modello di visualizzazione.

### <a name="making-a-navigation-menu"></a>Creazione di un menu di navigazione

[**Nell'esempio ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) `TableView` viene illustrato l'utilizzo di un oggetto per elencare le voci di menu. Ogni elemento è `Type` associato a un oggetto per una pagina specifica. Quando tale elemento è selezionato, il programma crea un'istanza della pagina e si sposta su di essa.

[![Tripla schermata di Visualizza tipo di raccolta](images/ch24fg21-small.png "TableView Voci del menu Elenco")](images/ch24fg21-large.png#lightbox "TableView Voci del menu Elenco")

Il [**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) esempio è leggermente diverso in quanto il menu contiene istanze di ogni pagina anziché tipi. Ciò consente di mantenere le informazioni da ogni pagina, ma è necessario creare un'istanza di tutte le pagine all'avvio del programma.

### <a name="manipulating-the-navigation-stack"></a>Manipolazione dello stack di navigazione

[**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) illustra diverse `INavigation` funzioni definite da che consentono di modificare lo stack di navigazione in modo strutturato:StackManipulation demonstrates several functions defined by that let you manipulate the navigation stack in a structured manner:

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync)e [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) con animazione opzionale

### <a name="dynamic-page-generation"></a>Generazione dinamica delle pagine

[**Nell'esempio BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) viene illustrata la costruzione di una pagina in fase di esecuzione in base all'input dell'utente.

## <a name="patterns-of-data-transfer"></a>Modelli di trasferimento dei dati

Spesso è necessario condividere i &mdash; dati tra le pagine per trasferire i dati in una pagina visitata e per una pagina restituire i dati alla pagina che li ha richiamati. Ci sono diverse tecniche per fare questo.

### <a name="constructor-arguments"></a>Argomenti del costruttore

Quando si passa a una nuova pagina, è possibile creare un'istanza della classe della pagina con un argomento del costruttore che consente alla pagina di inizializzarsi. Il [**SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) esempio viene illustrato questo. È anche possibile che la pagina esplorata abbia il proprio `BindingContext` set dalla pagina che vi si sposta.

### <a name="properties-and-method-calls"></a>Proprietà e chiamate ai metodi

Gli esempi di trasferimento dati rimanenti esplorano il problema del passaggio di informazioni tra le pagine quando una pagina passa a un'altra pagina e viceversa. In queste discussioni, la *home* page passa alla pagina delle *informazioni* e deve trasferire le informazioni inizializzate alla pagina *delle informazioni.* La pagina *delle informazioni* ottiene informazioni aggiuntive dall'utente e le trasferisce alla *home* page.

La *home* page può accedere facilmente ai metodi e alle proprietà pubbliche nella pagina *delle informazioni* non appena crea un'istanza di tale pagina. La pagina *delle informazioni* può anche accedere a i metodi e le proprietà pubbliche nella *home* page, ma scegliere un buon momento per questo può essere difficile. [**L'esempio DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) esegue `OnDisappearing` questa operazione nell'override. Uno svantaggio è che la pagina *delle informazioni* deve conoscere il tipo di *home* page.

### <a name="messagingcenter"></a>MessagingCenter

La classe Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) fornisce un altro modo per comunicare tra loro due pagine. I messaggi sono identificati da una stringa di testo e possono essere accompagnati da qualsiasi oggetto.

Un programma che desidera ricevere messaggi da un determinato [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) tipo deve sottoscrivere utilizzarli utilizzando e specificare una funzione di callback. In seguito può annullare la sottoscrizione chiamando [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*). La funzione di callback riceve qualsiasi messaggio inviato dal tipo [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) specificato con il nome specificato inviato tramite il metodo .

Il programma [**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) illustra come trasferire i dati utilizzando il centro messaggistica, ma anche in questo caso ciò richiede che la pagina delle *informazioni* conosca il tipo di *home* page.

### <a name="events"></a>Events

L'evento è un approccio rispettato nel tempo per una classe per inviare informazioni a un'altra classe senza conoscere il tipo di tale classe. Nell'esempio [**DateTransfer3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) la classe *info* definisce un evento che viene generato quando le informazioni sono pronte. Tuttavia, non esiste una posizione comoda per la *home* page per scollegare il gestore eventi.

### <a name="the-app-class-intermediary"></a>L'intermediario della classe App

Nell'esempio [**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) viene illustrato come `App` accedere alle proprietà definite nella classe sia dalla *home* page che dalla pagina *delle informazioni.* Questa è una buona soluzione, ma la sezione successiva descrive qualcosa di meglio.

### <a name="switching-to-a-viewmodel"></a>Passaggio a un ViewModel

L'utilizzo di un ViewModel per le informazioni consente alla *home* page e alla pagina *delle informazioni* di condividere l'istanza della classe di informazioni. Ciò è illustrato nell'esempio [**DateTransfer5.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) is demonstrated in the DateTransfer5 sample.

### <a name="saving-and-restoring-page-state"></a>Salvataggio e ripristino dello stato della pagina

L'intermediario `App` di classe o l'approccio ViewModel è ideale quando l'applicazione deve salvare le informazioni se il programma passa alla modalità di sospensione mentre la pagina *delle informazioni* è attiva. Nell'esempio [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) viene illustrato questo.

## <a name="saving-and-restoring-the-navigation-stack"></a>Salvataggio e ripristino dello stack di navigazione

Nel caso generale, un programma a più pagine che va a dormire dovrebbe passare alla stessa pagina quando viene ripristinato. Ciò significa che un programma di questo tipo deve salvare il contenuto dello stack di navigazione. In questa sezione viene illustrato come automatizzare questo processo in una classe progettata per questo scopo. Questa classe chiama anche le singole pagine per consentire loro di salvare e ripristinare lo stato della pagina.

La libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) definisce [`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) un'interfaccia denominata che le `Properties` classi possono implementare per salvare e ripristinare gli elementi nel dizionario.

La [`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) classe nella libreria **Xamarin.FormsBook.Toolkit** deriva da `Application`. È quindi possibile `App` derivare la classe da `MultiPageRestorableApp` ed eseguire alcune pulidi.

[**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) viene illustrato `MultiPageRestorableApp`l'utilizzo di .

### <a name="something-like-a-real-life-app"></a>Qualcosa di simile a un'app reale

L'esempio [**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) utilizza `MultiPageRestorableApp` e consente l'immissione e la `Properties` modifica delle note salvate nel dizionario.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 24 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Esempi del capitolo 24](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Pagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md)
