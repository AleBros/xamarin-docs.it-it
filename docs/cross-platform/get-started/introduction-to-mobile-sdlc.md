---
title: Introduzione al ciclo di vita di sviluppo del software per dispositivi mobili
description: Questo documento descrive il ciclo di vita di sviluppo del software per dispositivi mobili, prendendo in esame la progettazione dell'esperienza utente, la progettazione dell'interfaccia utente, lo sviluppo, la stabilizzazione, la distribuzione e altro ancora.
ms.prod: xamarin
ms.assetid: 420c5fdf-4610-4e71-9db5-fe894c961924
author: asb3993
ms.author: amburns
ms.date: 11/22/2016
ms.openlocfilehash: 74a9fc59b395d68db9b116106cc9fa63d3969c3b
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58855237"
---
# <a name="introduction-to-the-mobile-software-development-lifecycle"></a>Introduzione al ciclo di vita di sviluppo del software per dispositivi mobili

La compilazione di applicazioni per dispositivi mobili può essere un'operazione semplice e rapida come aprire l'IDE, assemblare una serie di componenti, eseguire un breve test e caricare il tutto in App Store. Oppure può essere un processo estremamente complesso che richiede un rigoroso design up-front, un test di usabilità, il controllo qualità su migliaia di dispositivi, un ciclo di vita completo della versione beta e quindi la distribuzione in numerosi modi diversi.

In questo documento vengono fornite informazioni introduttive dettagliate sulla creazione di applicazioni per dispositivi mobili che includono:

1.   **Processo**: il processo di sviluppo del software è chiamato ciclo di vita di sviluppo del software (SDLC, Software Development Lifecycle). Verranno esaminate tutte le fasi del ciclo di vita di sviluppo del software relative allo sviluppo di applicazioni per dispositivi mobili, tra cui ispirazione, progettazione, sviluppo, stabilizzazione, distribuzione e manutenzione.
1.   **Considerazioni**: sono numerose le considerazioni da tenere presente durante la creazione di applicazioni per dispositivi mobili, che si distingue in particolare da quella di applicazioni Web o desktop tradizionali. Queste considerazioni verranno prese in esame e ne verrà descritto l'effetto sullo sviluppo per applicazioni mobili.

Questo documento si propone di rispondere alle domande fondamentali sullo sviluppo di app per dispositivi mobili degli sviluppatori di ogni livello. La maggior parte dei concetti relativi all'intero ciclo di vita di sviluppo del software (SDLC) sono descritti con un approccio sostanzialmente completo. È possibile tuttavia che questo documento non sia adatto a tutti. Coloro che sono impazienti di iniziare possono passare direttamente alla guida [Introduzione allo sviluppo per dispositivi mobili](~/cross-platform/get-started/introduction-to-mobile-development.md) e tornare più tardi a questo documento.

## <a name="mobile-development-sdlc"></a>Ciclo di vita di sviluppo del software per dispositivi mobili

Il ciclo di vita di sviluppo del software per dispositivi mobili non è totalmente diverso dal ciclo di vita di sviluppo del software per applicazioni Web o desktop. In entrambi si distinguono 5 fasi principali del processo:

1.   **Concepimento**: tutte le app nascono da un'idea. L'idea viene solitamente perfezionata in una solida base per l'applicazione.
1.   **Progettazione**: la fase di progettazione consiste nel definire l'esperienza utente dell'app, ad esempio il layout generale e il funzionamento, e nel trasformarla nella progettazione di un'interfaccia utente appropriata, in genere con l'aiuto di un Graphic Designer.
1.   **Sviluppo**: solitamente la fase più dispendiosa in termini di risorse, si tratta dell'effettiva creazione dell'applicazione.
1.   **Stabilizzazione**: quando lo sviluppo è a buon punto, gli addetti al controllo qualità iniziano a testare l'applicazione e a correggerne i bug. Spesso l'applicazione entra in una fase beta in cui viene data la possibilità a un pubblico di utenti più ampio di usare l'applicazione e inviare commenti o suggerire modifiche.
1.  **Distribuzione**

Le fasi del ciclo di vita spesso si sovrappongono. Capita spesso ad esempio che mentre viene finalizzata l'interfaccia utente lo sviluppo sia ancora in corso e possa addirittura fornire dati per la progettazione dell'interfaccia. È anche possibile che venga iniziata la fase di stabilizzazione di un'applicazione mentre vengono aggiunte nuove funzioni a una nuova versione.

Inoltre, queste fasi possono essere usate in altre metodologie del ciclo di vita di sviluppo del software (SDLC), ad esempio nei modelli agile, a spirale, a cascata e così via.

Le singole fasi sono descritte dettagliatamente nelle sezioni seguenti.

### <a name="inception"></a>Concepimento

La diffusione e il livello di interazione degli utenti con i dispositivi mobili indica che quasi tutti hanno un'idea per un'app per dispositivi mobili. I dispositivi mobili hanno reso disponibile un modo completamente nuovo di interagire con i computer, il Web e persino con l'infrastruttura aziendale.

La fase del concepimento riguarda la definizione e il perfezionamento dell'idea per un'app.
Per creare un'app di successo, è importante porsi alcune domande fondamentali. Di seguito sono indicati alcuni aspetti da tenere presente prima di pubblicare un'app in uno degli App Store pubblici:

-   **Vantaggio competitivo**: esistono già app simili disponibili? In caso affermativo, come si differenzia l'applicazione dalle altre applicazioni?

Per le app che verranno distribuite in un'azienda:

-   **Integrazione dell'infrastruttura**: in quale infrastruttura già esistente dovrà essere integrata o quale infrastruttura dovrà estendere?

Le app dovranno anche essere valutate rispetto al fattore di forma dei dispositivi mobili:

-   **Valore**: qual è il valore offerto agli utenti? Come useranno l'app?
-   **Forma/Mobilità**: come funzionerà l'app nel fattore di forma di un dispositivo mobile? Com'è possibile aggiungere valore usando tecnologie per dispositivi mobili come il riconoscimento della posizione, la videocamera e così via?

Per rendere più semplice la progettazione delle funzionalità di un'app può essere utile definire gli attori e i [casi d'uso](https://en.wikipedia.org/wiki/Use_case). Gli attori sono i ruoli all'interno di un'applicazione e sono spesso utenti. I casi d'uso sono in genere azioni o scopi.

Ad esempio, un'applicazione di verifica delle attività può avere due attori: l'*utente* e un *amico*. L'utente può *creare un'attività* e *condividere un'attività* con un amico. In questo caso, la creazione di un'attività e la condivisione di un'attività sono due casi d'uso distinti che, in associazione agli attori, indicano le schermate che è necessario creare e le entità di business e la logica che è necessario sviluppare.

Dopo aver acquisito un numero appropriato di casi d'uso e attori, è molto più semplice iniziare a progettare un'applicazione. Lo sviluppo può quindi concentrarsi su come creare l'app anziché sulla sua definizione o la sua funzione.

### <a name="designing-mobile-applications"></a>Progettazione di applicazioni per dispositivi mobili

Dopo aver individuato le funzioni e le funzionalità dell'app, il passaggio successivo consiste nell'iniziare a risolvere l'esperienza utente.

#### <a name="ux-design"></a>Progettazione dell'esperienza utente

L'esperienza utente viene in genere creata con wireframe o mockup usando uno dei tanti [toolkit di progettazione](https://docs.microsoft.com/windows/uwp/design/downloads/). I mockup consentono di progettare l'esperienza utente senza doversi preoccupare della progettazione dell'interfaccia utente:

 [![](introduction-to-mobile-sdlc-images/balsamiq.png "L'esperienza utente viene in genere creata con wireframe o mockup usando strumenti come Balsamiq")](introduction-to-mobile-sdlc-images/balsamiq.png#lightbox)

Quando si creano i mockup di esperienza utente, è importante tenere presente le linee guida dell'interfaccia per le varie piattaforme a cui è destinata l'app. L'app deve adattarsi a ogni piattaforma. Le linee guida di progettazione ufficiali per ogni piattaforma sono le seguenti:

1.   **Apple** -  [Human Interface Guidelines](https://developer.apple.com/ios/human-interface-guidelines/overview/themes/)
1.   **Android** -  [Design Guidelines](https://developer.android.com/design/index.html)
1.   **UWP** - [UWP Design basics](https://docs.microsoft.com/windows/uwp/design/basics/) (Nozioni di base sulla progettazione per la piattaforma UWP)

Ad esempio, ogni app ha una metafora per il passaggio da una sezione all'altra in un'applicazione. iOS usa una barra di schede nella parte inferiore della schermata, Android usa una barra di schede nella parte superiore della schermata e la piattaforma UWP usa la visualizzazione con [pivot o schede](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/tabs-pivot).

Anche l'hardware determina le decisioni relative all'esperienza utente. Ad esempio, i dispositivi iOS non hanno un tasto *Indietro* e di conseguenza adottano la metafora del controller di spostamento:

 ![](introduction-to-mobile-sdlc-images/01-navigation-controller.png "I dispositivi iOS non hanno un tasto Indietro e di conseguenza adottano la metafora del controller di spostamento")

Anche il fattore di forma influenza le decisioni relative all'esperienza utente. Un tablet ha più spazio e può visualizzare più informazioni. Spesso gli elementi che richiedono più schermate in un telefono vengono compressi in un'unica schermata in un tablet:

 [![](introduction-to-mobile-sdlc-images/iphone-vs-ipad.png "Spesso gli elementi che richiedono più schermate in un telefono vengono compressi in un'unica schermata in un tablet")](introduction-to-mobile-sdlc-images/iphone-vs-ipad.png#lightbox)

Considerati gli innumerevoli fattori di forma esistenti, è possibile che si desideri adattare l'app anche a fattori di forma di medie dimensioni (compresi tra un telefono e un tablet).

#### <a name="user-interface-ui-design"></a>Progettazione dell'interfaccia utente

Dopo aver determinato l'esperienza utente, il passaggio successivo consiste nella progettazione dell'interfaccia utente. A differenza dell'esperienza utente costituita in genere da mockup bianchi e neri, la fase di progettazione dell'interfaccia utente prevede l'uso e la finalizzazione di colori, immagini e così via. Il tempo dedicato alla progettazione dell'interfaccia utente è importante. Tutte le app più diffuse hanno una progettazione professionale.

Come per l'esperienza utente, è importante tenere presente che ogni piattaforma ha un proprio linguaggio di progettazione: un'applicazione ben progettata può avere un aspetto diverso in ogni piattaforma:

 [![](introduction-to-mobile-sdlc-images/multiplatform-1.png "Un'applicazione ben progettata può avere un aspetto diverso in ogni piattaforma")](introduction-to-mobile-sdlc-images/multiplatform-1.png#lightbox)

### <a name="development"></a>Sviluppo

La fase di sviluppo inizia in genere molto presto. Infatti, dopo aver maturato un'idea nella fase concettuale, viene spesso sviluppato un prototipo funzionante per la convalida delle funzionalità e delle assunzioni e utile per comprendere l'ambito del progetto.

La maggior parte delle esercitazioni che seguono è incentrata sulla fase di sviluppo.

### <a name="stabilization"></a>Stabilizzazione

La stabilizzazione è il processo di risoluzione dei bug presenti nell'applicazione. Non soltanto da un punto di vista funzionale (ad esempio, l'arresto anomalo dell'applicazione quando si fa clic su un pulsante), ma riguarda anche l'usabilità e le prestazioni. È consigliabile iniziare la stabilizzazione in una fase iniziale del processo di sviluppo in modo da eseguire immediatamente le correzioni prima che i problemi diventino un costo . In genere le applicazioni prevedono le fasi di *prototipo*, *alfa*, *beta* e *versione finale candidata*. Sebbene possano essere chiamate in modo diverso, queste fasi seguono in genere il modello seguente:

1.   **Prototipo**: l'app è ancora nella fase di modello di verifica e solo la funzionalità principale e parti specifiche dell'applicazione sono funzionanti. Sono presenti i bug maggiori.
1.   **Alfa**: il codice delle funzionalità principali è stato completato (le funzionalità sono state compilate ma non testate completamente). I bug maggiori sono ancora presenti e le funzionalità periferiche potrebbero non essere ancora presenti.
1.   **Beta**: la maggior parte delle funzionalità è stata completata e sono stati eseguiti almeno un test parziale e la correzione dei bug. I principali problemi noti potrebbero essere ancora presenti.
1.   **Versione finale candidata**: tutte le funzionalità sono state completate e testate. Ad eccezione di alcuni bug, l'app è candidata per il rilascio.

Non è mai troppo presto per iniziare a testare un'applicazione. Ad esempio, se viene rilevato un problema importante nella fase di prototipo, l'esperienza utente dell'app può essere ancora modificata per risolverlo. Se viene rilevato un problema di prestazioni nella fase alfa, è ancora possibile modificare l'architettura prima che venga compilata una grande quantità di codice basato su assunzioni false.

In genere procedendo nelle fasi del ciclo di vita, l'applicazione viene aperta a un maggior numero di persone che la provano, la testano, inviano commenti e così via. Le applicazioni prototipo, ad esempio, possono essere mostrate o rese disponibili solo ai principali stakeholder, mentre la versione finale candidata delle applicazioni può essere distribuita ai clienti che effettuano la registrazione per l'accesso in anteprima.

Per un test preliminare e la distribuzione in un numero relativamente basso di dispositivi, è solitamente sufficiente la distribuzione diretta dal computer di sviluppo. Tuttavia, quando il pubblico diventa più vasto, questa operazione può diventare complessa. Per questa ragione, sono disponibili diverse opzioni di distribuzione dei test che rendono più semplice questo processo consentendo di invitare persone in un pool di test, rilasciare build sul Web e offrire strumenti per l'invio di commenti da parte degli utenti.

Per i test e la distribuzione, è possibile usare [App Center](https://appcenter.ms/) per gestire in modo continuativo le attività di compilazione, test, rilascio e monitoraggio delle app.

### <a name="distribution"></a>Distribuzione

Dopo che l'applicazione è stata stabilizzata, è possibile procedere alla distribuzione. Sono disponibili diverse opzioni di distribuzione, a seconda della piattaforma.

#### <a name="ios"></a>iOS

Le app Xamarin.iOS e Objective-C vengono distribuite esattamente nello stesso modo:

1.   **App Store di Apple**: l'App Store di Apple è un repository di applicazioni online con disponibilità globale creato in Mac OS X tramite iTunes. Si tratta del metodo di distribuzione più diffuso per le applicazioni e consente agli sviluppatori di commercializzare e distribuire le app online in modo molto semplice.
1.   **Distribuzione interna**: distribuzione interna delle applicazioni aziendali non disponibili al pubblico tramite l'App Store.
1.   **Distribuzione ad-hoc**: la distribuzione ad-hoc è destinata principalmente allo sviluppo e al test e consente di eseguire la distribuzione a un numero limitato di dispositivi di cui è stato effettuato correttamente il provisioning. La distribuzione a un dispositivo tramite Xcode o Visual Studio per Mac è considerata una distribuzione ad-hoc.

#### <a name="android"></a>Android

Tutte le applicazioni Android devono essere firmate prima di essere distribuite. Gli sviluppatori firmano le applicazioni usando il proprio certificato protetto da una chiave privata. Questo certificato può fornire una catena di autenticità che collega lo sviluppatore di applicazioni alle applicazioni che ha creato e rilasciato.
È importante notare che sebbene un certificato di sviluppo per Android possa essere firmato da un'autorità di certificazione riconosciuta, la maggior parte degli sviluppatori sceglie di non usare questi servizi e autofirma i propri certificati. Lo scopo principale dei certificati è quello di distinguere i diversi sviluppatori e le diverse applicazioni.
Android usa queste informazioni per consentire di applicare la delega delle autorizzazioni tra applicazioni e componenti in esecuzione nel sistema operativo Android.

A differenza di altre note piattaforme per dispositivi mobili, Android adotta un approccio molto aperto per la distribuzione delle app. I dispositivi non sono limitati a un unico App Store autorizzato.
Al contrario, tutti gli utenti sono liberi di creare un App Store e la maggior parte dei telefoni Android consente l'installazione delle app da Store di terze parti.

Ciò offre agli sviluppatori un canale di distribuzione per le applicazioni più grande ma più complesso. [Google Play](https://play.google.com/store?hl=en) è l'App Store ufficiale di Google, ma sono disponibili numerosi altri Store. Alcuni degli Store più noti sono:

1.  [AppBrain](http://www.appbrain.com/)
1.  [Amazon Appstore per Android](http://www.amazon.com/mobile-apps/b?ie=UTF8&amp;node=2350149011)
1.  [Handango](http://www.handango.com/)
1.  [GetJar](http://www.getjar.com/)

#### <a name="uwp"></a>UWP

Le applicazioni UWP vengono distribuite agli utenti tramite Microsoft Store. Gli sviluppatori inviano le proprie app per l'approvazione. Una volta approvate, le app sono visibili nello Store. Per altre informazioni sulla pubblicazione di app di Windows, vedere la documentazione sulla [pubblicazione](https://docs.microsoft.com/windows/uwp/publish/) della piattaforma UWP (Universal Windows Platform).

## <a name="mobile-development-considerations"></a>Considerazioni sullo sviluppo per dispositivi mobili

Sebbene lo sviluppo di applicazioni per dispositivi mobili non sia fondamentalmente diverso dallo sviluppo di applicazioni Web o desktop tradizionali in termini di processo o architettura, è necessario tenere presente alcune considerazioni.

### <a name="common-considerations"></a>Considerazioni comuni

#### <a name="multitasking"></a>Multitasking

Esistono due sfide significative per il multitasking, ovvero l'esecuzione contemporanea di più applicazioni, in un dispositivo mobile. In primo luogo, a causa dello spazio disponibile limitato, è difficile visualizzare contemporaneamente più applicazioni. Di conseguenza, nei dispositivi mobili è possibile visualizzare in primo piano solo un'app alla volta. In secondo luogo, avere più applicazioni aperte che eseguono attività può esaurire rapidamente la carica della batteria.

Di seguito viene descritto come ogni piattaforma gestisce il multitasking in modo diverso.

#### <a name="form-factor"></a>Fattore di forma

La maggior parte dei dispositivi mobili può essere suddivisa in due categorie, ovvero telefoni e tablet, con poche eccezioni. Sebbene lo sviluppo per questi fattori di forma sia in generale molto simile, la progettazione di applicazioni può variare notevolmente.
I telefoni hanno uno spazio sulla schermata molto limitato e i tablet, sebbene più grandi, rimangono comunque dispositivi mobili con meno spazio sulla schermata anche rispetto alla maggior parte dei laptop. Per questa ragione, i controlli dell'interfaccia utente delle piattaforme per dispositivi mobili sono progettati specificatamente per essere efficaci su fattori di forma più piccoli.

#### <a name="device-and-os-fragmentation"></a>Frammentazione dei dispositivi e del sistema operativo

È importante prendere in considerazione dispositivi diversi durante l'intero ciclo di vita di sviluppo del software:

1.   **Concettualizzazione e pianificazione**: tenere presente che l'hardware e le funzioni varieranno da un dispositivo all'altro. È possibile che un'applicazione basata su determinate funzioni non funzioni correttamente su alcuni dispositivi. Ad esempio, poiché non tutti i dispositivi sono provvisti di videocamera, se si crea un'applicazione di messaggistica video, è possibile che alcuni dispositivi siano in grado di riprodurre i video ma non di registrarli.
1.   **Progettazione**: durante la progettazione di un'esperienza utente, fare attenzione ai diversi formati e dimensioni dello schermo dei dispositivi. Inoltre, durante la progettazione dell'interfaccia utente di un'applicazione, è necessario considerare le diverse risoluzioni dello schermo.
1.   **Sviluppo**: quando si usa una funzione dal codice, è necessario verificare sempre prima la presenza della funzione. Ad esempio, prima di usare una funzione del dispositivo come la videocamera, verificare sempre nel sistema operativo la presenza della funzione. Quindi, durante l'inizializzazione della funzione o del dispositivo, assicurarsi di richiedere gli elementi attualmente supportati dal sistema operativo relativi al dispositivo e quindi usare tali impostazioni di configurazione.
1.   **Test**: è molto importante testare l'applicazione in fase iniziale e spesso sui dispositivi attuali. Anche i dispositivi con le stesse specifiche hardware possono variare notevolmente nel comportamento.

#### <a name="limited-resources"></a>Risorse limitate

Sebbene i dispositivi mobili diventino sempre più potenti, rimangono dispositivi con capacità limitate rispetto ai computer desktop o notebook. Ad esempio, gli sviluppatori desktop in genere non si preoccupano delle capacità di memoria; sono abituati ad avere a disposizione grandi quantità di memoria fisica e virtuale, mentre su un dispositivo mobile è possibile esaurire rapidamente tutta la memoria disponibile solo caricando un gruppo di immagini di qualità elevata.

Inoltre, le applicazioni che richiedono notevoli risorse del processore, ad esempio i giochi o il riconoscimento del testo, possono sovraccaricare la CPU e influire negativamente sulle prestazioni del dispositivo.

Per questi motivi, è importante creare un codice efficace e distribuire con anticipo e frequentemente su dispositivi attuali per verificare la velocità di risposta.

### <a name="ios-considerations"></a>Considerazioni su iOS

#### <a name="multitasking"></a>Multitasking

Il multitasking è controllato attentamente in iOS ed esistono regole e comportamenti che l'applicazione deve rispettare quando un'altra applicazione passa in primo piano, altrimenti l'applicazione verrà terminata da iOS.

#### <a name="device-specific-resources"></a>Risorse specifiche del dispositivo

In un particolare fattore di forma, l'hardware può variare notevolmente da un modello all'altro. Ad esempio, alcuni dispositivi hanno una fotocamera posteriore, altri hanno anche una fotocamera anteriore e alcuni non hanno alcuna fotocamera.

Alcuni dispositivi meno recenti (iPhone 3G e precedenti) non supportano il multitasking.

A causa di queste differenze tra i modelli di dispositivo, è importante verificare la presenza di una funzione prima di tentare di usarla.

#### <a name="os-specific-constraints"></a>Vincoli specifici del sistema operativo

Per assicurarsi che le applicazioni siano reattive e sicure, iOS applica una serie di regole che le applicazioni devono rispettare. Oltre alle regole riguardanti il multitasking, esistono alcuni metodi di evento a cui l'app deve rispondere entro una certa quantità di tempo, altrimenti verrà terminata da iOS.

Tenere presente anche che le app vengono eseguite in un Sandbox, un ambiente che applica vincoli di sicurezza che limitano gli accessi dell'app. Ad esempio, un'app può leggere e scrivere nella propria directory, ma se tenta di scrivere nella directory di un'altra applicazione, verrà terminata.

### <a name="android-considerations"></a>Considerazioni su Android

#### <a name="multitasking"></a>Multitasking

Il multitasking in Android ha due componenti. Il primo componente è il ciclo di vita delle attività. Ogni schermata in un'applicazione Android è rappresentata da un'attività ed esiste uno specifico gruppo di eventi che si verificano quando un'applicazione passa in background o torna in primo piano. Le applicazioni devono essere conformi a questo ciclo di vita per essere reattive e ben progettate. Per altre informazioni, vedere la guida [Activity Lifecycle](~/android/app-fundamentals/activity-lifecycle/index.md) (Ciclo di vita delle attività).

Il secondo componente del multitasking in Android è l'uso dei servizi.
I servizi sono processi a esecuzione prolungata che esistono indipendentemente da un'applicazione e vengono usati per eseguire processi mentre l'applicazione viene eseguita in background. Per altre informazioni, vedere la guida [Creating Services](~/android/app-fundamentals/services/index.md) (Creazione di servizi).

#### <a name="many-devices-and-many-form-factors"></a>Numerosi dispositivi e numerosi fattori di forma

Google non impone alcun limite ai dispositivi che possono eseguire il sistema operativo Android. Questo paradigma aperto crea un ambiente di prodotto popolato da innumerevoli dispositivi con hardware, formati e risoluzioni dello schermo, funzioni dei dispositivi e capacità molto diversi.

A causa di questa frammentazione estrema dei dispositivi Android, nella maggior parte dei casi vengono scelti i 5 o 6 dispositivi più diffusi per la progettazione e i test, a cui viene data priorità.

#### <a name="security-considerations"></a>Considerazioni sulla sicurezza

Tutte le applicazioni nel sistema operativo Android vengono eseguite in un'identità distinta isolata con autorizzazioni limitate. Per impostazione predefinita, le applicazioni possono eseguire un numero molto limitato di operazioni. Ad esempio, senza autorizzazioni speciali, un'applicazione non può inviare un messaggio di testo, determinare lo stato del telefono o accedere a Internet. Per accedere a queste funzioni, è necessario che nel file manifesto dell'applicazione siano specificate le autorizzazioni richieste dall'applicazione al momento dell'installazione; il sistema operativo legge le autorizzazioni, notifica all'utente che l'applicazione richiede le autorizzazioni e consente all'utente di scegliere se continuare o annullare l'installazione.
A causa del modello di Store delle applicazioni aperto, questo è un passaggio essenziale nel modello di distribuzione Android poiché ad esempio le applicazioni non sono controllate come in iOS. Per un elenco delle autorizzazioni delle applicazioni, vedere l'articolo di riferimento [Manifest Permissions](https://developer.android.com/reference/android/Manifest.permission.html) (Autorizzazioni del manifesto) nella documentazione di Android.

### <a name="windows-considerations"></a>Considerazioni per Windows

#### <a name="multitasking"></a>Multitasking

Anche nella piattaforma UWP il multitasking è suddiviso in due parti: il ciclo di vita per le pagine e le applicazioni e i processi in background. Ogni schermata di un'applicazione è un'istanza di una classe Page che include eventi associati all'attivazione o alla disattivazione (con regole speciali per la gestione dello stato inattivo o il contrassegno per la rimozione).

La seconda parte consiste nella specifica di agenti in background per l'elaborazione delle attività anche quando l'applicazione non è in esecuzione in primo piano. 

#### <a name="device-capabilities"></a>Funzionalità del dispositivo

Sebbene l'hardware UWP sia abbastanza omogeneo, esistono ancora componenti facoltativi che è necessario tenere presente durante la creazione del codice. Le funzionalità hardware facoltative includono la fotocamera, la bussola e il giroscopio. Esiste anche una classe speciale a memoria limitata (256 MB) che richiede un'attenzione particolare oppure gli sviluppatori possono escludere il supporto dei dispositivi a memoria limitata.

#### <a name="security-considerations"></a>Considerazioni sulla sicurezza

Per informazioni su importanti considerazioni di sicurezza per la piattaforma UWP, vedere la documentazione sulla [sicurezza](https://docs.microsoft.com/windows/uwp/security/).

## <a name="summary"></a>Riepilogo

Questa guida ha offerto un'introduzione al ciclo di vita di sviluppo del software relativo allo sviluppo per dispositivi mobili. La guida ha fornito considerazioni generali sulla creazione di applicazioni per dispositivi mobili e ha esaminato diverse considerazioni specifiche delle piattaforme relative alla progettazione, al test e alla distribuzione.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione allo sviluppo per dispositivi mobili](~/cross-platform/get-started/introduction-to-mobile-development.md)
- [Introduzione a Xamarin](~/get-started/index.yml)
- [Condivisione del codice tra piattaforme diverse](~/cross-platform/app-fundamentals/index.md)
