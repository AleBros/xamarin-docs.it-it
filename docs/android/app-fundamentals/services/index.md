---
title: Creazione di servizi di Android
description: Questa guida illustra i servizi di xamarin. Android, che sono componenti di Android che consentono di lavorare essere eseguita senza un'interfaccia utente attivo. I servizi vengono comunemente utilizzati per le attività che vengono eseguite in background, ad esempio calcoli molto tempo, il download dei file, la riproduzione di musica e così via. Illustra i diversi scenari appropriati per i servizi e Mostra come implementarli sia per l'esecuzione di attività in background a esecuzione prolungata che consentono di fornire un'interfaccia per le chiamate di procedura remota.
ms.prod: xamarin
ms.assetid: BA371A59-6F7A-F62A-02FC-28253504ACC9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: dfc0e1cb7239381ef2f495b0f9774d390b0dc82e
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527196"
---
# <a name="creating-android-services"></a>Creazione di servizi di Android

_Questa guida illustra i servizi di xamarin. Android, che sono componenti di Android che consentono di lavorare essere eseguita senza un'interfaccia utente attivo. I servizi vengono comunemente utilizzati per le attività che vengono eseguite in background, ad esempio calcoli molto tempo, il download dei file, la riproduzione di musica e così via. Illustra i diversi scenari appropriati per i servizi e Mostra come implementarli sia per l'esecuzione di attività in background a esecuzione prolungata che consentono di fornire un'interfaccia per le chiamate di procedura remota._

## <a name="android-services-overview"></a>Panoramica di servizi di Android

App per dispositivi mobili non sono ad esempio le app desktop. Desktop sono abbondanti quantità di risorse, ad esempio l'area dello schermo, memoria, spazio di archiviazione e un alimentatore connesso, non i dispositivi mobili. Questi vincoli forzare l'App per dispositivi mobili per ottenere un comportamento diverso. Ad esempio, schermo di piccole dimensioni in un dispositivo mobile significa in genere che un solo app (ad esempio attività) è visibile alla volta. Altre attività sono spostata in background e il push in uno stato sospeso in cui non possono eseguire alcuna operazione. Tuttavia, solo perché un'applicazione Android è in background non significa che è Impossibile per l'app continuare a lavorare. 

Android le applicazioni sono costituite da almeno uno dei quattro componenti principali seguenti: _impegni_, _ricevitori di trasmissione_, _provider di contenuti_e _Servizi_. Le attività sono il fulcro di molte applicazioni Android eccezionali perché forniscono l'interfaccia utente che consente agli utenti di interagire con l'applicazione. Tuttavia, se si desidera eseguire simultanee o operazioni in background, le attività non sono sempre la scelta migliore.
 
Il meccanismo principale per attività in background in Android è il _servizio_. Un servizio di Android è un componente che è progettato per eseguire alcune operazioni senza un'interfaccia utente. Un servizio può scaricare un file, riprodurre musica o applicare un filtro a un'immagine. Servizi nonché per la comunicazione interprocesso (_IPC_) tra le applicazioni Android. Ad esempio un'app per Android può usare il servizio di Windows Media player musica che proviene da un'altra app o un'app potrebbe esporre i dati (ad esempio, le informazioni di contatto della persona) ad altre App tramite un servizio. 

Servizi e la capacità di eseguire attività in background, sono fondamentali per fornire un'interfaccia utente fluida e uniforme. Tutte le applicazioni Android hanno una _thread principale_ (noto anche come una _thread dell'interfaccia utente_) in cui vengono eseguite le attività. Per mantenere reattivo il dispositivo, Android deve essere in grado di aggiornare l'interfaccia utente alla tariffa di 60 fotogrammi al secondo. Se un'app per Android esegue troppe operazioni sul thread principale, quindi Android eliminerà i frame, che a sua volta, l'interfaccia utente appaia scatti (talvolta anche detta _janky_). Ciò significa che qualsiasi lavoro eseguito sul thread dell'interfaccia utente devono essere completate nell'intervallo di tempo tra due frame, circa 16 millisecondi (1 secondo ogni 60 fotogrammi). 

Per risolvere questo problema, uno sviluppatore può utilizzare i thread in un'attività per eseguire alcune operazioni che possono bloccare l'interfaccia utente. Tuttavia, ciò potrebbe causare problemi. È molto probabile che Android verrà eliminare e ricreare le varie istanze dell'attività. Tuttavia, Android non elimina automaticamente i thread, che potrebbe comportare perdite di memoria. Un esempio tipico è quando il [dispositivo viene ruotato](~/android/app-fundamentals/handling-rotation.md) &ndash; Android proverà a eliminare l'istanza dell'attività e quindi ricreare una nuova:

![Quando viene ruotato dispositivo, l'istanza 1 viene eliminato definitivamente e istanza 2 viene creato](images/image-01.png)

Si tratta di una potenziale perdita di memoria &ndash; thread creato per la prima istanza dell'attività ancora in esecuzione. Se il thread ha un riferimento alla prima istanza dell'attività, ciò impedirà Android garbage la raccolta dell'oggetto. Tuttavia, la seconda istanza dell'attività viene comunque creata (che a sua volta può creare un nuovo thread). Ruotare il dispositivo più volte in rapida successione potrebbero esaurire tutta la RAM e forzare Android per terminare l'intera applicazione di recuperare la memoria.

Come regola generale, se l'esecuzione di operazioni deve sopravvivere a un'attività, quindi un servizio deve essere creato per eseguire tale lavoro. Tuttavia, se il lavoro è disponibile solo nel contesto di un'attività, il quindi la creazione di un thread per eseguire questa operazione potrebbe essere più appropriata. Ad esempio, la creazione di un'anteprima per una foto appena aggiunto a un'app della raccolta foto deve probabilmente si verificano in un servizio. Tuttavia, un thread potrebbe essere più appropriato per la riproduzione di musica che deve far valere solo mentre un'attività è in primo piano.

Attività in background possono essere suddivisi in due classificazioni ampie:

1. **Attività di esecuzione di lunga durata** &ndash; si tratta di operazioni che sono in corso fino a quando non viene esplicitamente interrotto. Un esempio di un _attività a esecuzione prolungata_ è un'app che consente di trasmettere musica o che deve monitorare i dati raccolti da un sensore. Queste attività devono eseguire anche l'applicazione non dispone di alcuna interfaccia utente visibile.
2. **Attività periodiche** &ndash; (talvolta detta un _processo_) un'attività periodica è quella di relativamente breve durata (secondi) e viene eseguita in una pianificazione (vale a dire una volta al giorno per una settimana o forse solo una volta nel Avanti 60 secondi). Un esempio di questo download di un file da internet o generazione di un'anteprima per un'immagine.

Esistono quattro tipi diversi di servizi Android:

* **Servizio associato** &ndash; oggetto _associato servizio_ è un servizio che dispone di un altro componente (in genere un'attività) a esso associato. Un servizio associato fornisce un'interfaccia che consente al servizio di interagire tra loro e il componente associato. Una volta che non sono presenti più client associato al servizio, Android si arresta il servizio. 

* **`IntentService`** &ndash; Un' _`IntentService`_ è una sottoclasse specializzata del `Service` classe che semplifica la creazione di un servizio e l'utilizzo. Un `IntentService` è progettato per gestire singole chiamate autonome. A differenza di un servizio, che può gestire simultaneamente più chiamate, un' `IntentService` è più simile a un _processori di coda di lavoro_ &ndash; lavoro è in coda e un `IntentService` elabora ogni processo uno alla volta in un unico thread. In genere, un`IntentService` non è associato a un'attività o un frammento. 

* **Servizio avviato** &ndash; oggetto _avviati servizio_ è un servizio che è stato avviato da un altro componente Android (ad esempio, un'attività) e viene eseguito in modo continuo in background fino a quando non si indica in modo esplicito il arresto del servizio. A differenza di un servizio associato, un servizio avviato non dispone di tutti i client associati direttamente a esso. Per questo motivo, è importante progettare i servizi avviati in modo che potrebbe essere riavviati normalmente in base alle esigenze.

* **Servizio ibrido** &ndash; oggetto _servizio ibrido_ è un servizio che ha le caratteristiche di un _avviati servizio_ e un _associato servizio_. Un servizio ibrido può essere avviato da quando si associa un componente o può essere avviato da un evento. Un componente client può o non può essere associato a questo servizio ibrido. Un servizio ibrido verrà eseguiti fino a quando non viene informato in modo esplicito per arrestare o fino a quando non sono presenti più client a esso associati.

Quale tipo di servizio da usare dipende molto i requisiti dell'applicazione. Come regola generale, un `IntentService` o un servizio associato sono sufficienti per la maggior parte delle attività che è necessario eseguire un'applicazione Android, in modo dovrebbe dare la preferenza a uno di questi due tipi di servizi. Un `IntentService` è una scelta ottimale per le attività "monofase", ad esempio download di un file, mentre un servizio associato potrebbe essere appropriato quando frequenti interazioni con un'attività o frammento è obbligatorio. 

Anche se la maggior parte dei servizi vengono eseguiti in background, è necessario una sottocategoria speciale nota come un _servizio di primo piano_. Questo è un servizio che è la priorità più alta (rispetto a un normale servizio) per eseguire alcune operazioni per l'utente (ad esempio la riproduzione di musica). 

È anche possibile eseguire un servizio nel proprio processo sullo stesso dispositivo, ciò è talvolta detta un _assistenza remota_ o come un _servizio out-of-process_. Ciò richiede maggiori interventi a creare, ma può essere utile per quando è necessario che un'applicazione condividere funzionalità con altre applicazioni e può, in alcuni casi, migliorare l'esperienza utente di un'applicazione. 

### <a name="background-execution-limits-in-android-80"></a>Limiti di esecuzione in background in Android 8.0

Partire da Android 8.0 (livello API 26), non è più un'applicazione Android hanno la possibilità di eseguire liberamente in background. Quando è in primo piano, un'app può avviare ed eseguire i servizi senza alcuna restrizione. Quando si sposta un'applicazione in background, Android comporterà la concessione all'app un determinato periodo di tempo per avviare e usare i servizi. Dopo tale periodo di tempo, l'app non è più possibile avviare eventuali servizi e i servizi che sono stati avviati verranno terminati. A questo punto non è possibile per l'app eseguire qualsiasi attività. Android prende in considerazione un'applicazione può essere in primo piano se viene soddisfatta una delle condizioni seguenti:

* È presente un'attività visibile (avviato o sospeso).
* L'app ha avviato un servizio di primo piano.
* Un'altra app in primo piano e Usa i componenti da un'app che sarebbe altrimenti in background. Un esempio è se un'applicazione, che è in primo piano, è associata a un servizio fornito dall'applicazione B. applicazione B sarebbe stato considerato in primo piano e non è stata terminata da Android scelto in background.

Esistono alcune situazioni in cui, anche se un'app è in background, Android verrà riattivare l'app e ridurre le restrizioni per alcuni minuti, consentendo all'app di eseguire alcune operazioni:
* Una priorità alta Firebase Cloud e il messaggio viene ricevuta dall'app.
* L'app riceve una trasmissione. 
* L'applicazione riceve un viene eseguito un `PendingIntent` in risposta a una notifica.

Le applicazioni xamarin. Android esistente potrebbero essere necessario modificare il modo di operare di attività in background per evitare eventuali problemi che potrebbero verificarsi in Android 8.0. Di seguito sono riportate alcune alternative pratiche per un servizio di Android:

* **Pianificare il lavoro eseguito in background mediante l'utilità di pianificazione di processo Android o il [Firebase processo Dispatcher](~/android/platform/firebase-job-dispatcher.md)**  &ndash; questi due librerie di forniscono un framework per le applicazioni separare le attività in background a _processi_, un'unità di lavoro discreta. Le app possono quindi pianificare il processo con il sistema operativo e alcuni criteri su quando eseguire il processo.
* **Avviare il servizio in primo piano** &ndash; un servizio di primo piano è utile per quando l'app deve eseguire alcune attività in background e l'utente potrebbe essere necessario periodicamente interagire con tale attività. Il servizio di primo piano visualizzerà una notifica persistente in modo che l'utente è a conoscenza che l'app è in esecuzione un'attività in background e fornisce anche un modo per monitorare o interagire con l'attività. Un esempio di questo sarebbe un'app il podcast riproduzione di un podcast all'utente o forse il download di un episodio di podcast, in modo che può essere soddisfacenti in un secondo momento. 
* **Usare una Firebase Cloud messaggio (FCM) con priorità alta** &ndash; Android quando si riceve una priorità alta FCM per un'app, consentirà di tale app eseguire i servizi in background per un breve periodo di tempo. Questa operazione è una valida alternativa alla presenza di un servizio in background che esegue il polling di un'app in background. 
* **Rinviare operazioni quando l'app passa in primo piano** &ndash; se nessuna delle soluzioni precedenti siano attuabili, l'App è necessario sviluppare i propri consente di sospendere e riprendere il lavoro quando l'app passa in primo piano.

## <a name="related-links"></a>Collegamenti correlati

* [Consente di limitare l'esecuzione in Background di Android Oreo](https://www.youtube.com/watch?v=Pumf_4yjTMc)
