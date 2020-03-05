---
title: Riepilogo del capitolo 24. Navigazione tra le pagine
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 24. Navigazione tra le pagine'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: fd8e4fc77917fcba9bc61e59ced714ac1cd6fbe9
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "78292010"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Riepilogo del capitolo 24. Navigazione tra le pagine

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

Molte applicazioni è costituita da più pagine, tra cui l'utente si sposta. L'applicazione ha sempre una pagina *principale* o una *Home* page e da qui l'utente passa ad altre pagine, che vengono mantenute in uno stack per lo spostamento indietro. Le opzioni di navigazione aggiuntive sono descritte nel [**capitolo 25. Tipi di pagine**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Le pagine modali e non modali

`VisualElement` definisce una proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) di tipo [`INavigation`](xref:Xamarin.Forms.INavigation), che include i due metodi seguenti per passare a una nuova pagina:

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Entrambi i metodi accettano un'istanza di `Page` come argomento e restituiscono un oggetto `Task`. I due metodi seguenti passare nuovamente alla pagina precedente:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Se l'interfaccia utente ha il proprio pulsante **indietro** (come i telefoni Android e Windows), non è necessario che l'applicazione chiami questi metodi.

Sebbene questi metodi siano disponibili da qualsiasi `VisualElement`, in genere vengono chiamati dalla proprietà `Navigation` dell'istanza di `Page` corrente.

Le applicazioni utilizzano pagine modali in genere quando l'utente deve fornire alcune informazioni prima di tornare alla pagina precedente della pagina. Le pagine che non sono modali sono talvolta denominate non modali o *gerarchiche*. Nulla nella pagina stessa lo distingue come modale o non modale. è governato invece dal metodo utilizzato per spostarsi su di esso. Per usare tutte le piattaforme, una pagina modale è necessario fornire la propria interfaccia utente per lo spostamento all'indietro alla pagina precedente.

L'esempio [**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) consente di esplorare la differenza tra le pagine modali e non modali. Qualsiasi applicazione che usa la navigazione tra le pagine deve passare il home page al costruttore [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , in genere nella classe `App` del programma. Un vantaggio è che non è più necessario impostare un `Padding` nella pagina per iOS.

Si noterà che, per le pagine non modali, viene visualizzata la proprietà [`Title`](xref:Xamarin.Forms.Page.Title) della pagina. IOS, Android e tutte le piattaforme Windows, tablet e desktop forniscono un elemento dell'interfaccia utente di tornare alla pagina precedente. Naturalmente, i dispositivi Android e Windows Phone hanno un pulsante **indietro** standard per tornare indietro.

Per le pagine modali, la pagina `Title` non viene visualizzata e non viene fornito alcun elemento dell'interfaccia utente per tornare alla pagina precedente. Sebbene sia possibile usare il pulsante **indietro** per Android e Windows Phone standard per tornare alla pagina precedente, la pagina modale sulle altre piattaforme deve fornire un meccanismo personalizzato per tornare indietro.

### <a name="animated-page-transitions"></a>Transizioni di pagina animata

Le versioni alternative dei diversi metodi di navigazione sono fornite con un secondo argomento booleano impostato su `true` se si desidera che la transizione della pagina includa un'animazione:

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

Tuttavia, i metodi di navigazione della pagina standard includono l'animazione per impostazione predefinita, quindi sono utili solo per passare a una pagina specifica all'avvio (come illustrato alla fine di questo capitolo) o quando si fornisce un'animazione di ingresso personalizzata, come descritto in [**Chapter22. Animazione**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variazioni visivi e funzionali

`NavigationPage` include due proprietà che è possibile impostare quando si crea un'istanza della classe nel metodo `App`:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` include anche quattro proprietà associabili associate che interessano la pagina specifica in cui sono impostate:

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) e [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) e [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String)) e [`GetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) funzionano solo in iOS
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource)) e [`GetTitleIcon`](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) funzionano solo su iOS e Android

### <a name="exploring-the-mechanics"></a>I meccanismi di esplorazione

I metodi di navigazione della pagina sono tutti asincroni e devono essere usati con `await`. Il completamento non indica che la navigazione è stata completata, ma solo che è possibile esaminare lo stack di navigazione a pagina.

Quando una pagina passa a un'altra, la prima pagina in genere riceve una chiamata al metodo [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) e la seconda pagina riceve una chiamata al relativo metodo di [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) . Analogamente, quando una pagina viene restituita a un'altra, la prima pagina riceve una chiamata al metodo `OnDisappearing` e la seconda pagina in genere riceve una chiamata al relativo metodo di `OnAppearing`. L'ordine di queste chiamate (e il completamento dei metodi asincroni che richiama la navigazione) è dipendente dalla piattaforma. L'uso della parola "in genere" in due istruzioni precedenti è a causa di navigazione a pagina modale Android, in cui non si verificano queste chiamate al metodo.

Inoltre, le chiamate ai metodi `OnAppearing` e `OnDisappearing` non indicano necessariamente la navigazione delle pagine.

L'interfaccia `INavigation` include due proprietà della raccolta che consentono di esaminare lo stack di navigazione:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) di tipo `IReadOnlyList<Page>` per lo stack non modale
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) di tipo `IReadOnlyList<Page>` per lo stack modale

È più sicuro accedere a questi stack dalla proprietà `Navigation` della `NavigationPage` (che deve essere la proprietà [`MainPage`](xref:Xamarin.Forms.Application.MainPage) della classe `App`). Solo quando è possibile esaminare questi stack dopo avere completato i metodi di navigazione a pagina asincrona. La proprietà [`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage) del `NavigationPage` non indica la pagina corrente se la pagina corrente è una pagina modale, ma indica invece l'ultima pagina non modale.

L'esempio [**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) consente di esplorare la navigazione tra le pagine e gli stack e i tipi validi di navigazione tra le pagine:

- Una pagina modale può passare a un'altra pagina non modale o modale
- Una pagina modale può passare solo a un'altra pagina modale

### <a name="enforcing-modality"></a>Modalità di imposizione

Un'applicazione utilizza una pagina modale quando è necessario ottenere alcune informazioni da parte dell'utente. L'utente dovrebbe essere proibito tornare alla pagina precedente fino a quando non viene fornite queste informazioni. In iOS è facile fornire un pulsante **indietro** e abilitarlo solo quando l'utente ha terminato la pagina. Tuttavia, per i dispositivi Android e Windows Phone, l'applicazione deve eseguire l'override del metodo [`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) e restituire `true` se il programma ha gestito il pulsante **indietro** , come illustrato nell'esempio [**ModalEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) .

Nell'esempio [**MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) viene illustrato come funziona in uno scenario MVVM.

## <a name="navigation-variations"></a>Variazioni di navigazione

Se una particolare pagina modale può essere passata a più volte, consigliabile mantenere le informazioni in modo che l'utente può modificare le informazioni anziché digitarlo tutto nuovamente. È possibile gestire questo aspetto conservando particolare istanza della pagina modale, ma un approccio migliore (in particolare in iOS) mantiene le informazioni contenute in un modello di visualizzazione.

### <a name="making-a-navigation-menu"></a>Effettua un menu di navigazione

Nell'esempio [**ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) viene illustrato l'utilizzo di un `TableView` per elencare le voci di menu. Ogni elemento è associato a un oggetto `Type` per una pagina specifica. Quando tale elemento è selezionato, il programma crea un'istanza di pagina e si passa a esso.

[![Schermata tripla del tipo di raccolta viste](images/ch24fg21-small.png "Voci di menu elenco TableView")](images/ch24fg21-large.png#lightbox "Voci di menu elenco TableView")

L'esempio [**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) è leggermente diverso perché il menu contiene istanze di ogni pagina anziché di tipi. Ciò consente di mantenere le informazioni da ciascuna pagina, ma devono essere creata un'istanza di tutte le pagine all'avvio del programma.

### <a name="manipulating-the-navigation-stack"></a>Modifica lo stack di navigazione

[**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) illustra diverse funzioni definite da `INavigation` che consentono di modificare lo stack di navigazione in modo strutturato:

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) e [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) con animazione facoltativa

### <a name="dynamic-page-generation"></a>Generazione di pagine dinamiche

L'esempio [**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) illustra la creazione di una pagina in fase di esecuzione in base all'input dell'utente.

## <a name="patterns-of-data-transfer"></a>Modelli di trasferimento dei dati

Spesso è necessario condividere i dati tra le pagine &mdash; per trasferire i dati a una pagina navigata e per una pagina per restituire dati alla pagina che lo ha richiamato. Esistono diverse tecniche per eseguire questa operazione.

### <a name="constructor-arguments"></a>Argomenti del costruttore

Quando si passa a una nuova pagina, è possibile creare un'istanza della classe di pagina con un argomento del costruttore che consente alla pagina per l'inizializzazione. Questa operazione è illustrata nell'esempio [**SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) . È anche possibile che la pagina navigata abbia la `BindingContext` impostata dalla pagina che lo sposta.

### <a name="properties-and-method-calls"></a>Le proprietà e chiamate al metodo

Gli esempi di trasferimento dei dati rimanenti esplorare il problema di passare le informazioni tra le pagine quando si sposta una pagina a un'altra pagina e viceversa. In queste discussioni la *Home* Page passa alla pagina *info* e deve trasferire le informazioni inizializzate nella pagina *info* . La pagina *informazioni* ottiene informazioni aggiuntive dall'utente e trasferisce le informazioni alla *Home* page.

La *Home* page può accedere facilmente a proprietà e metodi pubblici nella pagina *informazioni* non appena viene creata un'istanza di tale pagina. La pagina di *informazioni* può accedere anche a metodi e proprietà pubblici nella *Home* page, ma la scelta di un periodo di tempo ideale può risultare complessa. L'esempio [**DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) esegue questa operazione nell'override del `OnDisappearing`. Uno svantaggio è che la pagina di *informazioni* deve essere in grado di individuare il tipo della *Home* page.

### <a name="messagingcenter"></a>MessagingCenter

La classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) Novell. Forms fornisce un altro modo per comunicare tra due pagine. I messaggi sono identificati da una stringa di testo e possono essere accompagnati da qualsiasi oggetto.

Un programma che desidera ricevere messaggi da un particolare tipo deve sottoscriverli usando [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) e specificare una funzione di callback. Successivamente, è possibile annullare la sottoscrizione chiamando [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*). La funzione di callback riceve tutti i messaggi inviati dal tipo specificato con il nome specificato inviato tramite il metodo [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) .

Il programma [**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) illustra come trasferire i dati tramite il centro di messaggistica, ma anche in questo caso è necessario che la pagina *info* conosca il tipo della *Home* page.

### <a name="events"></a>Eventi

L'evento è un approccio antica per una classe inviare informazioni a un'altra classe senza conoscerne il tipo di tale classe. Nell'esempio [**DateTransfer3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) la classe *info* definisce un evento che viene attivato quando le informazioni sono pronte. Tuttavia, non esiste una posizione comoda per la rimozione del gestore eventi da parte della *Home* page.

### <a name="the-app-class-intermediary"></a>L'intermediario di classe di App

Nell'esempio [**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) viene illustrato come accedere alle proprietà definite nella classe `App` sia dalla *Home* page che dalla pagina *info* . Si tratta di una buona soluzione, ma la sezione successiva descrive qualcosa di più.

### <a name="switching-to-a-viewmodel"></a>Il passaggio a un elemento ViewModel

L'uso di un ViewModel per le informazioni consente alla *Home* page e alla pagina *info* di condividere l'istanza della classe Information. Questa operazione è illustrata nell'esempio [**DateTransfer5**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) .

### <a name="saving-and-restoring-page-state"></a>Salvataggio e il ripristino dello stato di pagina

L'intermediario della classe `App` o l'approccio ViewModel è ideale quando l'applicazione deve salvare le informazioni se il programma passa alla modalità di sospensione mentre la pagina *informazioni* è attiva. Questa operazione è illustrata nell'esempio [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) .

## <a name="saving-and-restoring-the-navigation-stack"></a>Salvataggio e ripristino stack di navigazione

In generale, un programma a più pagine che va in sospensione deve passare alla stessa pagina quando viene ripristinato. Ciò significa che tale programma deve salvare il contenuto dello stack di navigazione. Questa sezione illustra come automatizzare questo processo in una classe progettata a tale scopo. Questa classe chiama anche le singole pagine e consentire loro di salvare e ripristinare lo stato della pagina.

La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) definisce un'interfaccia denominata [`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) che le classi possono implementare per salvare e ripristinare gli elementi nel dizionario `Properties`.

La classe [`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) nella libreria **Novell. FormsBook. Toolkit** deriva da `Application`. È quindi possibile derivare la classe `App` da `MultiPageRestorableApp` ed eseguire alcune operazioni di manutenzione.

Il [**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) illustra l'uso di `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>È simile a un'app reale

L'esempio [**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) utilizza anche `MultiPageRestorableApp` e consente l'immissione e la modifica di note salvate nel dizionario `Properties`.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 24 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Capitolo 24 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Pagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md)
