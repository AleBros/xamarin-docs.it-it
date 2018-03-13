---
title: Riepilogo del capitolo 24. Navigazione tra le pagine
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 3390a298cd8d9967f0aea2bd9fb5a90830714ba5
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-24-page-navigation"></a>Riepilogo del capitolo 24. Navigazione tra le pagine

Molte applicazioni sono costituiti da più pagine, tra cui l'utente si sposta. Sempre l'applicazione ha un *principale* pagina o *home* pagina e da qui l'utente passa ad altre pagine, che vengono gestite in uno stack per lo spostamento all'indietro. Opzioni di navigazione aggiuntivi vengono trattate nelle [ **capitolo 25. Pagina varietà**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Pagine di modale e non modali

`VisualElement` definisce un [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) proprietà di tipo [ `INavigation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INavigation/), che include i due metodi seguenti per passare a una nuova pagina:

- [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/)
- [`PushModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync/p/Xamarin.Forms.Page/)

Entrambi i metodi accettano un `Page` istanza come argomento e restituiscono un `Task` oggetto. I due metodi seguenti tornare alla pagina precedente:

- [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopAsync()/)
- [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/)

Se l'interfaccia utente ha il proprio **nuovamente** pulsante (come i telefoni Android e Windows), quindi non è necessario per l'applicazione di chiamare questi metodi.

Anche se questi metodi sono disponibili da qualsiasi `VisualElement`, in genere vengono chiamati i `Navigation` proprietà dell'oggetto corrente `Page` istanza.

Le applicazioni utilizzano in genere pagine modale quando l'utente è necessario fornire alcune informazioni nella pagina prima di tornare alla pagina precedente. Le pagine che non sono modali sono talvolta denominate non modale o *gerarchica*. Nulla nella pagina stessa lo distingue come modale o non modale. è governato invece dal metodo utilizzato per spostarsi su di esso. Per utilizzare tutte le piattaforme, una pagina modale è necessario fornire la propria interfaccia utente per tornare alla pagina precedente.

Il [ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) esempio consente di esplorare la differenza tra le pagine non modale e modale. Qualsiasi applicazione che utilizza la navigazione è necessario passare la relativa home page per il [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) costruttore, in genere all'interno del programma `App` classe. Un premio di produzione è che non è più necessario impostare un `Padding` della pagina per iOS.

Si noterà che per non modale pagine, la pagina [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) proprietà viene visualizzata. IOS, Android e le piattaforme di Tablet PC e desktop Windows forniscono un elemento dell'interfaccia utente di tornare alla pagina precedente. Naturalmente, Android e Windows phone presentano standard **nuovamente** pulsante per tornare alla schermata precedente.

Per le pagine modale, la pagina `Title` non viene visualizzata e viene fornito alcun elemento di interfaccia utente per tornare alla pagina precedente. Sebbene sia possibile utilizzare lo standard di Android e Windows phone **nuovamente** per tornare alla pagina precedente, la pagina modale nelle altre piattaforme è necessario fornire un proprio meccanismo di tornare indietro.

### <a name="animated-page-transitions"></a>Transizioni di pagina animato

Sono disponibili versioni alternative dei vari metodi di navigazione con un secondo argomento booleano che è impostato su `true` se si desidera che la transizione di pagina per includere un'animazione:

- [PushAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/System.Boolean/)
- [PushModalAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync/p/Xamarin.Forms.Page/System.Boolean/)
- [PopAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopAsync/p/System.Boolean/)
- [PopModalAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync/p/System.Boolean/)

Tuttavia, i metodi di navigazione a pagina standard includono l'animazione per impostazione predefinita, pertanto questi sono utili solo per la navigazione a una determinata pagina all'avvio (come descritto verso la fine di questo capitolo) o quando si specifica un'animazione di entrata (come descritto in [ **Chapter22. Animazione**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Varianti visivi e funzionali

`NavigationPage` include due proprietà che è possibile impostare quando si crea un'istanza della classe nel `App` metodo:

- [`BarBackgroundColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/)
- [`BarTextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarTextColor/)

`NavigationPage` Inoltre, include quattro proprietà associabile associate che interessano la pagina particolare in cui siano impostate:

- [`SetHasBackButton`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetHasBackButton/p/Xamarin.Forms.Page/System.Boolean/) e [`GetHasBackButton`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetHasBackButton/p/Xamarin.Forms.Page/)
- [`SetHasNavigationBar`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetHasNavigationBar/p/Xamarin.Forms.BindableObject/System.Boolean/) e [`GetHasNavigationBar`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetHasNavigationBar/p/Xamarin.Forms.BindableObject/)
- [`SetBackButtonTitle`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetBackButtonTitle/p/Xamarin.Forms.BindableObject/System.String/) e [ `GetBackButtonTitle` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetBackButtonTitle/p/Xamarin.Forms.BindableObject/) utilizzare solo per iOS
- [`SetTitleIcon`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetTitleIcon/p/Xamarin.Forms.BindableObject/Xamarin.Forms.FileImageSource/) e [ `GetTitleIcon` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetTitleIcon/p/Xamarin.Forms.BindableObject/) aziendale in iOS e Android solo

### <a name="exploring-the-mechanics"></a>Il meccanismo di esplorazione

I metodi di navigazione della pagina sono tutti asincroni e deve essere utilizzati con `await`. Il completamento non indica che la navigazione è stata completata, ma solo che è possibile esaminare lo stack di navigazione della pagina.

Quando una pagina accede a un altro, la prima pagina ottiene in genere una chiamata al relativo [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing()/) metodo e la seconda pagina Ottiene una chiamata al relativo [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) metodo. Analogamente, quando una pagina viene restituito a un altro, la prima pagina Ottiene una chiamata a relativo `OnDisappearing` metodo e la seconda pagina ottiene in genere una chiamata a relativo `OnAppearing` metodo. L'ordine di queste chiamate (e il completamento dei metodi asincroni che richiama la navigazione) è dipendente dalla piattaforma. L'utilizzo della parola "in genere" nelle due istruzioni precedente è a causa di navigazione a pagina modale Android, in cui non si verificano queste chiamate al metodo.

Inoltre, le chiamate al `OnAppearing` e `OnDisappearing` metodi non indicano necessariamente la navigazione.

Il `INavigation` interfaccia include due proprietà di raccolta che consentono di esaminare lo stack di navigazione:

- [`NavigationStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.NavigationStack/) di tipo `IReadOnlyList<Page>` per lo stack non modale
- [`ModalStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) di tipo `IReadOnlyList<Page>` per lo stack modale

È consigliabile accedere a questi stack dal `Navigation` proprietà del `NavigationPage` (che deve essere il `App` della classe [ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) proprietà). Solo quando è possibile esaminare questi stack dopo aver completato i metodi di navigazione a pagina asincrona. Il [ `CurrentPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.CurrentPage/) proprietà del `NavigationPage` non indica la pagina corrente se la pagina corrente è una pagina modale, ma indica invece l'ultima pagina non modale.

Il [ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) esempio consente di esplorare la navigazione e gli stack di e i tipi validi di spostamenti di pagina:

- Una pagina è possibile passare a un'altra pagina non modale o modale
- Una pagina modale è possibile passare solo a un'altra pagina modale

### <a name="enforcing-modality"></a>Modalità di imposizione

Un'applicazione utilizza una pagina modale quando è necessario ottenere alcune informazioni da parte dell'utente. L'utente dovrebbe essere proibito tornare alla pagina precedente fino a quando non viene fornite tali informazioni. In iOS, è facile fornire un **nuovamente** pulsante e abilitare questa funzionalità solo quando l'utente ha terminato con la pagina. Ma per i dispositivi Android e Windows phone, l'applicazione deve eseguire l'override di [ `OnBackButtonPressed` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnBackButtonPressed()/) metodo e restituire `true` se è gestita dal programma il **nuovamente** pulsante stesso, come illustrato in il [ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) esempio.

Il [ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) esempio viene illustrato il funzionamento in uno scenario MVVM.

## <a name="navigation-variations"></a>Variazioni di navigazione

Se una particolare pagina modale possa essere passata a più volte, è necessario conservare le informazioni in modo che l'utente può modificare le informazioni anziché digitarlo tutto nuovo. È possibile gestire questo mantenendo l'istanza specifica della pagina modale, ma un approccio migliore (in particolare in iOS) mantiene le informazioni contenute in un modello di visualizzazione.

### <a name="making-a-navigation-menu"></a>Effettua un menu di navigazione

Il [ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) esempio viene illustrato come utilizzare un `TableView` per elencare gli elementi di menu. Ogni elemento è associato un `Type` oggetto per una pagina particolare. Quando tale elemento è selezionato, il programma crea un'istanza della pagina e passa a esso.

[![La schermata di raccolta del tipo di vista](images/ch24fg21-small.png "TableView elenco di voci di Menu")](images/ch24fg21-large.png#lightbox "TableView elenco di voci di Menu")

Il [ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) esempio è leggermente diversa in quanto il menu contiene istanze di ogni pagina anziché di tipi. Ciò consente di conservare le informazioni da ogni pagina, ma devono essere creata un'istanza di tutte le pagine all'avvio del programma.

### <a name="manipulating-the-navigation-stack"></a>Modifica lo stack di navigazione

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) illustra diverse funzioni definite dal `INavigation` che consentono di modificare lo stack di navigazione in un modo strutturato:

- [`RemovePage`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage/p/Xamarin.Forms.Page/)
- [`InsertPageBefore`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore/p/Xamarin.Forms.Page/Xamarin.Forms.Page/)
- [`PopToRootAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopToRootAsync()/) e [ `PopToRootAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopToRootAsync/p/System.Boolean/) con animazione facoltativo

### <a name="dynamic-page-generation"></a>Generazione della pagina dinamica

Il [ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) illustra la creazione di una pagina in fase di esecuzione in base all'input utente.

## <a name="patterns-of-data-transfer"></a>Modelli di trasferimento dei dati

È spesso necessario condividere dati tra pagine & #x 2014; per trasferire i dati a una pagina di navigazione e per una pagina restituire i dati per la pagina che lo ha richiamato. Sono disponibili diverse tecniche per raggiungere questo obiettivo.

### <a name="constructor-arguments"></a>Argomenti del costruttore

Durante la navigazione in una nuova pagina, è possibile creare un'istanza della classe di pagina con un argomento del costruttore che consente la pagina per l'inizializzazione. Il [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) esempio viene illustrata questa. È inoltre possibile per la pagina di navigazione relativa `BindingContext` impostato utilizzando la pagina che consente di passare a esso.

### <a name="properties-and-method-calls"></a>Le proprietà e chiamate al metodo

Negli esempi di trasferimento di dati restanti esplorare il problema del passaggio di informazioni tra pagine, quando si sposta una pagina a un'altra pagina e viceversa. Questi discussioni, il *home* consente di passare alla pagina di *info* pagina e necessario trasferire informazioni inizializzate per la *info* pagina. Il *info* pagina Ottiene informazioni aggiuntive da parte dell'utente e trasferisce le informazioni per il *home* pagina.

Il *home* pagina può accedere facilmente ai metodi pubblici e le proprietà di *info* pagina non appena viene creata un'istanza di tale pagina. Il *info* pagina possa inoltre accedere a metodi pubblici e le proprietà di *home* pagina, ma la scelta di un'ora valida per questa operazione può risultare difficile. Il [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) esempio esegue l'operazione nel relativo `OnDisappearing` eseguire l'override. Uno svantaggio è che il *info* deve conoscere il tipo di pagina il *home* pagina.

### <a name="messagingcenter"></a>MessagingCenter

Di xamarin. Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe fornisce un altro modo per due pagine comunicare tra loro. I messaggi sono identificati da una stringa di testo e possono essere accompagnati da qualsiasi oggetto.

Un programma che si desidera ricevere messaggi da un particolare tipo necessario sottoscriverli utilizzando [ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender,TArgs%7D/p/System.Object/System.String/System.Action%7BTSender,TArgs%7D/TSender/) e specificare una funzione di callback. In un secondo momento è possibile annullare la sottoscrizione chiamando [ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/). La funzione di callback riceve i messaggi inviati dal tipo specificato con il nome specificato inviato tramite il [ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) metodo.

Il [ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) programma viene illustrato come trasferire i dati utilizzando il centro di messaggistica, ma è più necessario che il *info* pagina conosce il tipo di *home* pagina.

### <a name="events"></a>Eventi

L'evento è un approccio famoso per una classe inviare informazioni a un'altra classe senza conoscerne il tipo di tale classe. Nel [ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) esempio il *info* classe definisce un evento generato quando le informazioni sono pronte. Tuttavia, non è pratico per il *home* pagina per scollegare il gestore dell'evento.

### <a name="the-app-class-intermediary"></a>L'intermediario della classe App

Il [ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) esempio viene illustrato come accedere alle proprietà definite nel `App` classe da entrambi i *home* pagina e *info*pagina. Si tratta di una buona soluzione, ma la prossima sezione descrive qualcosa di più.

### <a name="switching-to-a-viewmodel"></a>Il passaggio a un elemento ViewModel

Utilizzando un ViewModel per le informazioni consentono di *home* pagina e *info* pagina per condividere l'istanza della classe di informazioni. Questa funzionalità viene illustrata la [ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) esempio.

### <a name="saving-and-restoring-page-state"></a>Salvataggio e il ripristino dello stato della pagina

Il `App` intermediario di classe o l'approccio ViewModel è ideale quando l'applicazione deve salvare informazioni se il programma passa alla modalità sospensione mentre il *info* pagina è attiva. Il [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) esempio viene illustrata questa.

## <a name="saving-and-restoring-the-navigation-stack"></a>Salvataggio e ripristino di stack di navigazione

Nel caso generale, un programma con più pagine che passa alla modalità sospensione deve passare alla stessa pagina quando viene ripristinato. Ciò significa che tale programma deve salvare il contenuto dello stack di navigazione. In questa sezione viene illustrato come automatizzare il processo in una classe progettata a tale scopo. Questa classe chiama anche le singole pagine e consentire loro di salvare e ripristinare lo stato della pagina.

Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria definisce un'interfaccia denominata [ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) che possono implementare le classi per salvare e ripristinare gli elementi di `Properties`dizionario.

Il [ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) classe il **Xamarin.FormsBook.Toolkit** libreria deriva da `Application`. È quindi possibile derivare i `App` classe `MultiPageRestorableApp` ed eseguire alcune le attività di manutenzione.

Il [ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) illustra l'uso di `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>Ad esempio un'applicazione reale

Il [ **NoteTaker** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) si avvale anche di esempio `MultiPageRestorableApp` e consente l'immissione e modifica di note in cui vengono salvate nel `Properties` dizionario.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 24 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Esempi di capitolo 24](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Pagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md)
