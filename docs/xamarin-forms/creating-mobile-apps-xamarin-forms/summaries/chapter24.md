---
title: Riepilogo del capitolo 24. Navigazione tra le pagine
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 24. Navigazione tra le pagine'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 35130baac4025fe69dbc7aa9b6928f824b35c573
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156704"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Riepilogo del capitolo 24. Navigazione tra le pagine

Molte applicazioni è costituita da più pagine, tra cui l'utente si sposta. Sempre l'applicazione ha un *principale* pagina oppure *home* pagina e da lì l'utente si sposta ad altre pagine, che vengono gestite in uno stack per tornare indietro. Le opzioni di navigazione aggiuntivi sono illustrate nella [ **capitolo 25. Pagina varietà**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Le pagine modali e non modali

`VisualElement` definisce un [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) vlastnosti typu [ `INavigation` ](xref:Xamarin.Forms.INavigation), che include i due metodi seguenti per passare a una nuova pagina:

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Entrambi i metodi accettano un `Page` istanza di un argomento e restituiscono un `Task` oggetto. I due metodi seguenti passare nuovamente alla pagina precedente:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Se l'interfaccia utente ha il proprio **nuovamente** pulsante (come i telefoni Android e Windows), quindi non è necessario per l'applicazione chiamare questi metodi.

Anche se questi metodi sono disponibili da qualsiasi `VisualElement`, in genere vengono chiamati dal `Navigation` proprietà dell'oggetto corrente `Page` istanza.

Le applicazioni utilizzano pagine modali in genere quando l'utente deve fornire alcune informazioni prima di tornare alla pagina precedente della pagina. Pagine non modale sono talvolta denominate non modale o *gerarchici*. Nulla nella pagina stessa lo distingue come modale o non modale. è governato invece dal metodo utilizzato per spostarsi su di esso. Per usare tutte le piattaforme, una pagina modale è necessario fornire la propria interfaccia utente per lo spostamento all'indietro alla pagina precedente.

Il [ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) esempio consente di esplorare la differenza tra le pagine modali e non modali. Qualsiasi applicazione che usa la navigazione deve superare la relativa home page per il [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) costruttore, in genere all'interno del programma `App` classe. Un vantaggio è che non è più necessario impostare un `Padding` nella pagina per iOS.

Si noterà che, per non modale le pagine, la pagina [ `Title` ](xref:Xamarin.Forms.Page.Title) proprietà viene visualizzata. IOS, Android e tutte le piattaforme Windows, tablet e desktop forniscono un elemento dell'interfaccia utente di tornare alla pagina precedente. Naturalmente, Android e Windows phone dispositivi hanno uno standard **nuovamente** pulsante per tornare indietro.

Per le pagine modali, la pagina `Title` non viene visualizzata e non viene fornito alcun elemento dell'interfaccia utente per tornare alla pagina precedente. Sebbene sia possibile utilizzare lo standard di Android e Windows phone **nuovamente** per tornare alla pagina precedente, la pagina modale nelle altre piattaforme deve fornire un proprio meccanismo di tornare indietro.

### <a name="animated-page-transitions"></a>Transizioni di pagina animata

Versioni alternative dei vari metodi di navigazione vengono fornite con un secondo argomento booleano che è impostato su `true` se si desidera che la transizione di pagina per includere un'animazione:

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

Tuttavia, i metodi di navigazione a pagina standard includono l'animazione per impostazione predefinita, in modo che questi sono utili solo per la navigazione in una pagina specifica all'avvio (come descritto verso la fine di questo capitolo) o quando si fornisce il proprio animazione di ingresso (come descritto in [ **Chapter22. Animazione**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variazioni visivi e funzionali

`NavigationPage` include due proprietà che è possibile impostare quando si crea un'istanza della classe nel `App` metodo:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` inoltre include quattro proprietà associabili associate che influiscono su quella pagina in cui sono impostati:

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) e [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) e [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String)) e [ `GetBackButtonTitle` ](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) funzionano solo in iOS
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource)) e [ `GetTitleIcon` ](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) operano con solo iOS e Android

### <a name="exploring-the-mechanics"></a>I meccanismi di esplorazione

I metodi di navigazione pagina sono tutti asincroni e devono essere usati con `await`. Il completamento non indica che la navigazione è stata completata, ma solo che è possibile esaminare lo stack di navigazione a pagina.

Quando si sposta una pagina a altra, la prima pagina ottiene in genere una chiamata al relativo [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) metodo e la seconda pagina Ottiene una chiamata al relativo [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) (metodo). Analogamente, quando una pagina viene restituito a un altro, la prima pagina Ottiene una chiamata al relativo `OnDisappearing` metodo e la seconda pagina in genere Ottiene una chiamata a relativo `OnAppearing` (metodo). L'ordine di queste chiamate (e il completamento dei metodi asincroni che richiama la navigazione) è dipendente dalla piattaforma. L'uso della parola "in genere" in due istruzioni precedenti è a causa di navigazione a pagina modale Android, in cui non si verificano queste chiamate al metodo.

Inoltre, le chiamate al `OnAppearing` e `OnDisappearing` metodi non indicano necessariamente la navigazione.

Il `INavigation` interfaccia include due proprietà di raccolta che consentono di esaminare lo stack di navigazione:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) di tipo `IReadOnlyList<Page>` per lo stack non modale
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) di tipo `IReadOnlyList<Page>` per lo stack modale

È più sicuro accedere a questi stack dal `Navigation` proprietà del `NavigationPage` (che deve essere il `App` della classe [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) proprietà). Solo quando è possibile esaminare questi stack dopo avere completato i metodi di navigazione a pagina asincrona. Il [ `CurrentPage` ](xref:Xamarin.Forms.NavigationPage.CurrentPage) proprietà del `NavigationPage` non indica la pagina corrente se la pagina corrente è una pagina modale, ma indica invece l'ultima pagina non modale.

Il [ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) esempio consente di esplorare la navigazione e gli stack e i tipi validi di esplorazioni di pagina:

- Una pagina modale può passare a un'altra pagina non modale o modale
- Una pagina modale può passare solo a un'altra pagina modale

### <a name="enforcing-modality"></a>Modalità di imposizione

Un'applicazione utilizza una pagina modale quando è necessario ottenere alcune informazioni da parte dell'utente. L'utente dovrebbe essere proibito tornare alla pagina precedente fino a quando non viene fornite queste informazioni. In iOS, è più semplice fornire un' **nuovamente** pulsante e abilitarlo solo quando l'utente ha terminato la pagina. Ma dispositivi Android e Windows phone, l'applicazione deve eseguire l'override la [ `OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed) metodo e restituire `true` se è stato gestito dall'applicazione il **nuovamente** pulsante stesso, come illustrato in il [ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) esempio.

Il [ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) esempio viene illustrato il funzionamento in uno scenario MVVM.

## <a name="navigation-variations"></a>Variazioni di navigazione

Se una particolare pagina modale può essere passata a più volte, consigliabile mantenere le informazioni in modo che l'utente può modificare le informazioni anziché digitarlo tutto nuovamente. È possibile gestire questo aspetto conservando particolare istanza della pagina modale, ma un approccio migliore (in particolare in iOS) mantiene le informazioni contenute in un modello di visualizzazione.

### <a name="making-a-navigation-menu"></a>Effettua un menu di navigazione

Il [ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) esempio viene illustrato come utilizzare un `TableView` alle voci di menu elenco. Ogni elemento è associato un `Type` oggetto per una pagina particolare. Quando tale elemento è selezionato, il programma crea un'istanza di pagina e si passa a esso.

[![Schermata triplo della raccolta del tipo di vista](images/ch24fg21-small.png "voci di Menu elenco TableView")](images/ch24fg21-large.png#lightbox "TableView elenco di voci di Menu")

Il [ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) esempio è leggermente diverso in quanto il menu contiene istanze di ogni pagina anziché di tipi. Ciò consente di mantenere le informazioni da ciascuna pagina, ma devono essere creata un'istanza di tutte le pagine all'avvio del programma.

### <a name="manipulating-the-navigation-stack"></a>Modifica lo stack di navigazione

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) illustra le diverse funzioni definite da `INavigation` che consentono di modificare lo stack di navigazione in modo strutturato:

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) e [ `PopToRootAsync` ](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) con animazione facoltativo

### <a name="dynamic-page-generation"></a>Generazione di pagine dinamiche

Il [ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) viene illustrata la costruzione di una pagina in fase di esecuzione in base all'input utente.

## <a name="patterns-of-data-transfer"></a>Modelli di trasferimento dei dati

È spesso necessario condividere dati tra le pagine &mdash; per trasferire i dati a una pagina di navigazione e per una pagina restituire dati alla pagina che lo ha richiamato. Esistono diverse tecniche per eseguire questa operazione.

### <a name="constructor-arguments"></a>Argomenti del costruttore

Quando si passa a una nuova pagina, è possibile creare un'istanza della classe di pagina con un argomento del costruttore che consente alla pagina per l'inizializzazione. Il [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) esempio viene illustrata questa. È anche possibile per la pagina di navigazione avere relativo `BindingContext` impostata utilizzando la pagina che consente di passare a esso.

### <a name="properties-and-method-calls"></a>Le proprietà e chiamate al metodo

Gli esempi di trasferimento dei dati rimanenti esplorare il problema di passare le informazioni tra le pagine quando si sposta una pagina a un'altra pagina e viceversa. In queste discussioni, il *home* si passa alla pagina il *info* pagina e necessario trasferire informazioni inizializzate per il *info* pagina. Il *info* pagina Ottiene informazioni aggiuntive da parte dell'utente e trasferisce le informazioni per il *home* pagina.

Il *domestica* pagina facilmente accessibili i metodi pubblici e le proprietà nel *info* pagina, non appena crea un'istanza di tale pagina. Il *info* pagina possa inoltre accedere a metodi pubblici e le proprietà di *home* pagina, ma scegliere il momento giusto per questa operazione può risultare difficile. Il [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) esempio esegue questa operazione nel relativo `OnDisappearing` eseguire l'override. Uno svantaggio è che il *info* deve conoscere il tipo di pagina il *home* pagina.

### <a name="messagingcenter"></a>MessagingCenter

Xamarin. Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe offre un altro modo per due pagine comunicare tra loro. I messaggi sono identificati da una stringa di testo e possono essere accompagnati da qualsiasi oggetto.

È necessario sottoscriverli un programma che desidera ricevere messaggi da un determinato tipo usando [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) e specificare una funzione di callback. In un secondo momento è possibile annullare la sottoscrizione chiamando [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*). La funzione di richiamata riceve qualsiasi messaggio inviato dal tipo specificato con il nome specificato inviato tramite il [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) (metodo).

Il [ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) programma illustra come trasferire i dati utilizzando il centro di messaggistica, ma è più necessario che il *info* pagina conoscere il tipo del *domestica* pagina.

### <a name="events"></a>Eventi

L'evento è un approccio antica per una classe inviare informazioni a un'altra classe senza conoscerne il tipo di tale classe. Nel [ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) esempio il *info* classe definisce un evento che viene attivata quando le informazioni sono pronte. Tuttavia, non è possibile utilizzare pratico per la *domestica* pagina per scollegare il gestore dell'evento.

### <a name="the-app-class-intermediary"></a>L'intermediario di classe di App

Il [ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) esempio viene illustrato come accedere alle proprietà definite nel `App` classe da entrambe le *home* pagina e il *info*pagina. Si tratta di una buona soluzione, ma la sezione successiva descrive qualcosa di più.

### <a name="switching-to-a-viewmodel"></a>Il passaggio a un elemento ViewModel

Usando un ViewModel per le informazioni del *domestica* pagina e il *info* pagina per condividere la stessa istanza della classe di informazioni. Questa funzionalità viene illustrata la [ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) esempio.

### <a name="saving-and-restoring-page-state"></a>Salvataggio e il ripristino dello stato di pagina

Il `App` intermediario di classe o l'approccio ViewModel è ideale per l'applicazione deve salvare informazioni se il programma entra in sospensione mentre la *info* pagina è attiva. Il [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) esempio viene illustrata questa.

## <a name="saving-and-restoring-the-navigation-stack"></a>Salvataggio e ripristino stack di navigazione

In generale, un programma a più pagine che va in sospensione deve passare alla stessa pagina quando viene ripristinato. Ciò significa che tale programma deve salvare il contenuto dello stack di navigazione. Questa sezione illustra come automatizzare questo processo in una classe progettata a tale scopo. Questa classe chiama anche le singole pagine e consentire loro di salvare e ripristinare lo stato della pagina.

Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria definisce un'interfaccia denominata [ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) che possono implementare le classi per salvare e ripristinare gli elementi nel `Properties`dizionario.

Il [ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) classe la **Xamarin.FormsBook.Toolkit** libreria deriva da `Application`. È quindi possibile derivare le `App` classe `MultiPageRestorableApp` ed eseguire alcune attività di manutenzione.

Il [ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) illustra l'uso di `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>È simile a un'app reale

Il [ **NoteTaker** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) esempio Usa anche `MultiPageRestorableApp` e consente l'immissione e la modifica delle note che vengono salvate nel `Properties` dizionario.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 24 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Capitolo 24 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Pagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md)
