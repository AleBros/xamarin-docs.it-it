---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 24. Page navigation''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 09622adc269027b589a7345a7d4411c3dcecbf0c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136643"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Riepilogo del capitolo 24. Navigazione tra le pagine

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

Molte applicazioni sono costituite da più pagine tra cui l'utente si sposta. L'applicazione ha sempre una pagina *principale* o una *Home* page e da qui l'utente passa ad altre pagine, che vengono mantenute in uno stack per lo spostamento indietro. Le opzioni di navigazione aggiuntive sono descritte nel [**capitolo 25. Tipi di pagine**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Pagine modali e pagine non modali

`VisualElement`definisce una [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà di tipo [`INavigation`](xref:Xamarin.Forms.INavigation) , che include i due metodi seguenti per passare a una nuova pagina:

- [ `PushAsync` ] (xrif: Xamarin.Forms . INavigation. PushAsync ( Xamarin.Forms . Pagina)
- [ `PushModalAsync` ] (xrif: Xamarin.Forms . INavigation. PushModalAsync ( Xamarin.Forms . Pagina)

Entrambi i metodi accettano un' `Page` istanza come argomento e restituiscono un `Task` oggetto. I due metodi seguenti consentono di tornare alla pagina precedente:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Se l'interfaccia utente ha il proprio pulsante **indietro** (come i telefoni Android e Windows), non è necessario che l'applicazione chiami questi metodi.

Sebbene questi metodi siano disponibili da qualsiasi `VisualElement` , in genere vengono chiamati dalla `Navigation` proprietà dell' `Page` istanza corrente.

Le applicazioni utilizzano in genere pagine modali quando l'utente deve fornire alcune informazioni nella pagina prima di tornare alla pagina precedente. Le pagine che non sono modali sono talvolta denominate non modali o *gerarchiche*. Nessun elemento della pagina lo distingue come modale o non modale; viene invece governato dal metodo usato per passare a tale oggetto. Per lavorare su tutte le piattaforme, una pagina modale deve fornire la propria interfaccia utente per tornare alla pagina precedente.

L'esempio [**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) consente di esplorare la differenza tra le pagine modali e non modali. Qualsiasi applicazione che usa la navigazione tra le pagine deve passare il home page al [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) costruttore, in genere nella `App` classe del programma. Un vantaggio è che non è più necessario impostare un `Padding` nella pagina per iOS.

Si noterà che, per le pagine non modali, [`Title`](xref:Xamarin.Forms.Page.Title) viene visualizzata la proprietà della pagina. IOS, Android e le piattaforme Tablet PC e desktop di Windows forniscono un elemento dell'interfaccia utente per tornare alla pagina precedente. Naturalmente, i dispositivi Android e Windows Phone hanno un pulsante **indietro** standard per tornare indietro.

Per le pagine modali, la pagina `Title` non viene visualizzata e non viene fornito alcun elemento dell'interfaccia utente per tornare alla pagina precedente. Sebbene sia possibile usare il pulsante **indietro** per Android e Windows Phone standard per tornare alla pagina precedente, la pagina modale sulle altre piattaforme deve fornire un meccanismo personalizzato per tornare indietro.

### <a name="animated-page-transitions"></a>Transizioni di pagine animate

Le versioni alternative dei diversi metodi di navigazione sono fornite con un secondo argomento booleano impostato su `true` se si desidera che la transizione della pagina includa un'animazione:

- PushAsync (xrif: Xamarin.Forms . INavigation. PushAsync ( Xamarin.Forms . Pagina, System. Boolean)
- [PushModalAsync] (xrif: Xamarin.Forms . INavigation. PushModalAsync ( Xamarin.Forms . Pagina, System. Boolean)
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

Tuttavia, i metodi di navigazione della pagina standard includono l'animazione per impostazione predefinita, quindi sono utili solo per passare a una pagina specifica all'avvio (come illustrato alla fine di questo capitolo) o quando si fornisce un'animazione di ingresso personalizzata, come descritto in [**Chapter22. Animazione**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variazioni visive e funzionali

`NavigationPage`include due proprietà che è possibile impostare quando si crea un'istanza della classe nel `App` Metodo:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage`include anche quattro proprietà associabili associate che interessano la pagina specifica in cui sono impostate:

- [ `SetHasBackButton` ] (xrif: Xamarin.Forms . NavigationPage. SetHasBackButton ( Xamarin.Forms . Page, System. Boolean) e [ `GetHasBackButton` ] (xrif: Xamarin.Forms . NavigationPage. GetHasBackButton ( Xamarin.Forms . Pagina)
- [ `SetHasNavigationBar` ] (xrif: Xamarin.Forms . NavigationPage. SetHasNavigationBar ( Xamarin.Forms . BindableObject, System. Boolean) e [ `GetHasNavigationBar` ] (xrif: Xamarin.Forms . NavigationPage. GetHasNavigationBar ( Xamarin.Forms . BindableObject))
- [ `SetBackButtonTitle` ] (xrif: Xamarin.Forms . NavigationPage. SetBackButtonTitle ( Xamarin.Forms . BindableObject, System. String) e [ `GetBackButtonTitle` ] (xrif: Xamarin.Forms . NavigationPage. GetBackButtonTitle ( Xamarin.Forms . BindableObject)) lavorare solo su iOS
- [ `SetTitleIcon` ] (xrif: Xamarin.Forms . NavigationPage. SetTitleIcon ( Xamarin.Forms . BindableObject, Xamarin.Forms . Fileimagesource)) e [ `GetTitleIcon` ] (xrif: Xamarin.Forms . NavigationPage. GetTitleIcon ( Xamarin.Forms . BindableObject)) funzionano solo su iOS e Android

### <a name="exploring-the-mechanics"></a>Esplorazione dei meccanismi

I metodi di navigazione della pagina sono tutti asincroni e devono essere usati con `await` . Il completamento non indica che la navigazione delle pagine è stata completata, ma solo che è sicuro esaminare lo stack di navigazione della pagina.

Quando una pagina passa a un'altra, la prima pagina in genere riceve una chiamata al [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) metodo e la seconda pagina riceve una chiamata al relativo [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) metodo. Analogamente, quando una pagina viene restituita a un'altra, la prima pagina riceve una chiamata al `OnDisappearing` metodo e la seconda pagina in genere riceve una chiamata al relativo `OnAppearing` metodo. L'ordine di queste chiamate (e il completamento dei metodi asincroni che richiama lo spostamento) è dipendente dalla piattaforma. L'uso della parola "in genere" nelle due istruzioni precedenti è dovuto all'esplorazione delle pagine modali di Android, in cui queste chiamate al metodo non vengono eseguite.

Inoltre, le chiamate ai `OnAppearing` `OnDisappearing` metodi e non indicano necessariamente la navigazione delle pagine.

L' `INavigation` interfaccia include due proprietà della raccolta che consentono di esaminare lo stack di navigazione:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack)di tipo `IReadOnlyList<Page>` per lo stack non modale
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack)di tipo `IReadOnlyList<Page>` per lo stack modale

È più sicuro accedere a questi stack dalla `Navigation` proprietà di `NavigationPage` (che deve essere la `App` proprietà della classe [`MainPage`](xref:Xamarin.Forms.Application.MainPage) ). È possibile esaminare questi stack solo in modo sicuro dopo il completamento dei metodi di navigazione di pagina asincroni. La [`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage) proprietà di `NavigationPage` non indica la pagina corrente se la pagina corrente è una pagina modale, ma indica invece l'ultima pagina non modale.

L'esempio [**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) consente di esplorare la navigazione tra le pagine e gli stack e i tipi validi di navigazione tra le pagine:

- Una pagina non modale può passare a un'altra pagina non modale o a una pagina modale
- Una pagina modale può spostarsi solo in un'altra pagina modale

### <a name="enforcing-modality"></a>Applicazione della modalità

Un'applicazione usa una pagina modale quando è necessario ottenere alcune informazioni dall'utente. L'utente non deve essere autorizzato a tornare alla pagina precedente finché non vengono fornite le informazioni. In iOS è facile fornire un pulsante **indietro** e abilitarlo solo quando l'utente ha terminato la pagina. Tuttavia, per i dispositivi Android e Windows Phone, l'applicazione deve eseguire l'override del [`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) metodo e restituire `true` se il programma ha gestito il pulsante **indietro** , come illustrato nell'esempio [**ModalEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) .

Nell'esempio [**MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) viene illustrato come funziona in uno scenario MVVM.

## <a name="navigation-variations"></a>Variazioni di navigazione

Se una particolare pagina modale può essere spostata più volte, deve conservare le informazioni in modo che l'utente possa modificare le informazioni anziché digitarle nuovamente. Per gestire questo problema, è possibile mantenere l'istanza specifica della pagina modale, ma un approccio migliore (in particolare in iOS) è preservare le informazioni in un modello di visualizzazione.

### <a name="making-a-navigation-menu"></a>Creazione di un menu di navigazione

Nell'esempio [**ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) viene illustrato l'utilizzo di un oggetto `TableView` per elencare le voci di menu. Ogni elemento è associato a un `Type` oggetto per una pagina specifica. Quando tale elemento è selezionato, il programma crea un'istanza della pagina e vi passa.

[![Schermata tripla del tipo di raccolta viste](images/ch24fg21-small.png "Voci di menu elenco TableView")](images/ch24fg21-large.png#lightbox "Voci di menu elenco TableView")

L'esempio [**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) è leggermente diverso perché il menu contiene istanze di ogni pagina anziché di tipi. Questo consente di conservare le informazioni da ogni pagina, ma è necessario creare un'istanza di tutte le pagine all'avvio del programma.

### <a name="manipulating-the-navigation-stack"></a>Modifica dello stack di navigazione

[**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) illustra diverse funzioni definite da `INavigation` che consentono di modificare lo stack di navigazione in modo strutturato:

- [ `RemovePage` ] (xrif: Xamarin.Forms . INavigation. RemovePage ( Xamarin.Forms . Pagina)
- [ `InsertPageBefore` ] (xrif: Xamarin.Forms . INavigation. InsertPageBefore ( Xamarin.Forms . Xamarin.Forms, Pagina)
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync)e [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) con animazione facoltativa

### <a name="dynamic-page-generation"></a>Generazione dinamica di pagine

L'esempio [**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) illustra la creazione di una pagina in fase di esecuzione in base all'input dell'utente.

## <a name="patterns-of-data-transfer"></a>Modelli di trasferimento dei dati

Spesso è necessario condividere i dati tra le pagine &mdash; per trasferire i dati a una pagina navigata e per una pagina per restituire dati alla pagina che lo ha richiamato. Per eseguire questa operazione, sono disponibili diverse tecniche.

### <a name="constructor-arguments"></a>Argomenti del costruttore

Quando si passa a una nuova pagina, è possibile creare un'istanza della classe di pagina con un argomento del costruttore che consente l'inizializzazione della pagina. Questa operazione è illustrata nell'esempio [**SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) . È anche possibile che la pagina navigata sia `BindingContext` impostata dalla pagina che lo sposta.

### <a name="properties-and-method-calls"></a>Proprietà e chiamate al metodo

Gli esempi di trasferimento dei dati rimanenti esplorano il problema del passaggio di informazioni tra le pagine quando una pagina passa a un'altra pagina e viceversa. In queste discussioni la *Home* Page passa alla pagina *info* e deve trasferire le informazioni inizializzate nella pagina *info* . La pagina *informazioni* ottiene informazioni aggiuntive dall'utente e trasferisce le informazioni alla *Home* page.

La *Home* page può accedere facilmente a proprietà e metodi pubblici nella pagina *informazioni* non appena viene creata un'istanza di tale pagina. La pagina di *informazioni* può accedere anche a metodi e proprietà pubblici nella *Home* page, ma la scelta di un periodo di tempo ideale può risultare complessa. L'esempio [**DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) esegue questa operazione nella relativa `OnDisappearing` sostituzione. Uno svantaggio è che la pagina di *informazioni* deve essere in grado di individuare il tipo della *Home* page.

### <a name="messagingcenter"></a>MessagingCenter

La Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe fornisce un altro modo per due pagine per comunicare tra loro. I messaggi sono identificati da una stringa di testo e possono essere accompagnati da qualsiasi oggetto.

Un programma che desidera ricevere messaggi da un particolare tipo deve sottoscriverli usando [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) e specificare una funzione di callback. Successivamente, è possibile annullare la sottoscrizione chiamando [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) . La funzione di callback riceve tutti i messaggi inviati dal tipo specificato con il nome specificato inviato tramite il [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) metodo.

Il programma [**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) illustra come trasferire i dati tramite il centro di messaggistica, ma anche in questo caso è necessario che la pagina *info* conosca il tipo della *Home* page.

### <a name="events"></a>Eventi

L'evento è un approccio basato sul tempo per l'invio di informazioni a un'altra classe da parte di una classe senza conoscere il tipo di tale classe. Nell'esempio [**DateTransfer3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) la classe *info* definisce un evento che viene attivato quando le informazioni sono pronte. Tuttavia, non esiste una posizione comoda per la rimozione del gestore eventi da parte della *Home* page.

### <a name="the-app-class-intermediary"></a>Intermediario della classe App

Nell'esempio [**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) viene illustrato come accedere alle proprietà definite nella `App` classe sia dalla *Home* page che dalla pagina *info* . Si tratta di una soluzione ottimale, ma nella sezione successiva viene descritto qualcosa di meglio.

### <a name="switching-to-a-viewmodel"></a>Passare a un ViewModel

L'uso di un ViewModel per le informazioni consente alla *Home* page e alla pagina *info* di condividere l'istanza della classe Information. Questa operazione è illustrata nell'esempio [**DateTransfer5**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) .

### <a name="saving-and-restoring-page-state"></a>Salvataggio e ripristino dello stato della pagina

L' `App` intermediario della classe o l'approccio ViewModel è ideale quando l'applicazione deve salvare le informazioni se il programma passa alla modalità di sospensione mentre la pagina *informazioni* è attiva. Questa operazione è illustrata nell'esempio [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) .

## <a name="saving-and-restoring-the-navigation-stack"></a>Salvataggio e ripristino dello stack di navigazione

Nel caso generale, un programma a più pagine che passa alla modalità di sospensione deve passare alla stessa pagina quando viene ripristinato. Questo significa che un programma di questo tipo deve salvare il contenuto dello stack di navigazione. In questa sezione viene illustrato come automatizzare questo processo in una classe progettata a questo scopo. Questa classe chiama anche le singole pagine per consentire il salvataggio e il ripristino dello stato della relativa pagina.

La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) definisce un'interfaccia denominata [`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) che le classi possono implementare per salvare e ripristinare gli elementi nel `Properties` dizionario.

La [`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) classe nella libreria **Novell. FormsBook. Toolkit** deriva da `Application` . È quindi possibile derivare la `App` classe da `MultiPageRestorableApp` ed eseguire alcune operazioni di manutenzione.

[**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) illustra l'uso di `MultiPageRestorableApp` .

### <a name="something-like-a-real-life-app"></a>Qualcosa di simile a un'app reale

L'esempio [**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) utilizza inoltre `MultiPageRestorableApp` e consente l'immissione e la modifica di note salvate nel `Properties` dizionario.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 24 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Capitolo 24 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Pagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md)
