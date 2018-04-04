---
title: Creazione di servizi di Android
description: Questa guida illustra xamarin services, ovvero i componenti di Android che consentono di lavorare a essere eseguita senza un'interfaccia utente attivo. I servizi vengono comunemente utilizzati per le attività vengono eseguite in background, ad esempio calcoli molto tempo, il download dei file, riprodurre musica e così via. Vengono illustrati i diversi scenari che i servizi sono adatti per e viene illustrato come implementarle per l'esecuzione di attività in background con esecuzione prolungata, nonché per fornire un'interfaccia per le chiamate di procedura remota.
ms.prod: xamarin
ms.assetid: BA371A59-6F7A-F62A-02FC-28253504ACC9
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/19/2018
ms.openlocfilehash: 2e942d1085822fee935ae0f23f2253f23d49a43d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="creating-android-services"></a>Creazione di servizi di Android

_Questa guida illustra xamarin services, ovvero i componenti di Android che consentono di lavorare a essere eseguita senza un'interfaccia utente attivo. I servizi vengono comunemente utilizzati per le attività vengono eseguite in background, ad esempio calcoli molto tempo, il download dei file, riprodurre musica e così via. Vengono illustrati i diversi scenari che i servizi sono adatti per e viene illustrato come implementarle per l'esecuzione di attività in background con esecuzione prolungata, nonché per fornire un'interfaccia per le chiamate di procedura remota._

## <a name="android-services-overview"></a>Panoramica di servizi di Android

App per dispositivi mobili non sono presenti come App desktop. Desktop sono abbondanti quantità elevate di risorse, ad esempio l'area dello schermo, memoria, spazio di archiviazione e un alimentatore connesso, non i dispositivi mobili. Questi vincoli forzata delle App per dispositivi mobili si comportano in modo diverso. Schermo di piccole dimensioni in un dispositivo mobile significa, ad esempio, in genere che un solo app (ad esempio attività) è visibile alla volta. Altre attività sono spostata in background e inseriti in uno stato sospeso in cui è possibile eseguire alcuna operazione. Tuttavia, solo perché è un'applicazione Android in background non significa che è Impossibile per l'app continuare a lavorare. 

Le applicazioni Android sono costituite da almeno uno seguenti quattro componenti principali: _attività_, _Broadcast ricevitori_, _i provider di contenuti_e _Servizi_. Le attività rappresentano le fondamenta di molte applicazioni Android ottimale in quanto forniscono l'interfaccia utente che consente agli utenti di interagire con l'applicazione. Tuttavia, quando si tratta di esecuzione simultanee o operazioni in background, le attività non sono sempre la scelta migliore.
 
È il meccanismo principale per operazioni in background in Android le _servizio_. Un servizio Android è un componente che è progettato per eseguire alcune operazioni senza un'interfaccia utente. Un servizio può scaricare un file, riprodurre musica o applicare un filtro a un'immagine. Servizi possono essere utilizzati anche per la comunicazione interprocesso (_IPC_) tra le applicazioni Android. Ad esempio un'app Android potrebbe utilizzare il servizio Windows Media player musica da un'altra applicazione o un'app potrebbe esporre i dati (ad esempio informazioni di contatto di un utente) ad altre applicazioni tramite un servizio. 

Servizi e la capacità di eseguire operazioni in background, sono fondamentali per l'interfaccia utente semplice e uniforme. Tutte le applicazioni Android un _thread principale_ (noto anche come un _thread dell'interfaccia utente_) in cui vengono eseguite le attività. Per mantenere reattiva il dispositivo, Android deve essere in grado di aggiornare l'interfaccia utente con la frequenza di 60 fotogrammi al secondo. Se un'app Android esegue alla quantità di lavoro nel thread principale, quindi Android verrà eliminato il frame, che a sua volta comporta l'interfaccia utente appaia scatti (anche noto come _janky_). Ciò significa che qualsiasi lavoro eseguito sul thread dell'interfaccia utente devono essere completate nell'intervallo di tempo tra due frame, circa 16 millisecondi (1 secondo ogni 60 frame). 

Per risolvere questo problema, uno sviluppatore può utilizzare i thread in un'attività per eseguire alcune operazioni che bloccano l'interfaccia utente. Tuttavia, ciò potrebbe causare problemi. È molto probabile che Android verrà eliminare e ricreare le istanze multiple dell'attività. Tuttavia, Android non comporta automaticamente la distruzione di thread, che potrebbe causare perdite di memoria. Un esempio è quando il [dispositivo viene ruotato](~/android/app-fundamentals/handling-rotation.md) &ndash; Android tenterà di eliminare l'istanza dell'attività e quindi ricreare uno nuovo:

![Quando viene ruotato dispositivo, istanza 1 viene eliminato e viene creata istanza 2](images/image-01.png)

Si tratta di una potenziale perdita di memoria &ndash; thread creato per la prima istanza dell'attività ancora in esecuzione. Se il thread ha un riferimento alla prima istanza dell'attività, ciò impedirà Android garbage raccolta l'oggetto. Tuttavia, la seconda istanza dell'attività viene comunque creata (che a sua volta potrebbe creare un nuovo thread). Il dispositivo più volte in successione rapida di rotazione può esaurire tutta la RAM e forzare Android per terminare l'intera applicazione di recuperare memoria.

Come regola generale, se l'esecuzione di operazioni deve sopravvivono un'attività, quindi un servizio deve essere creato per eseguire tale lavoro. Tuttavia, se il lavoro è disponibile solo nel contesto di un'attività, quindi la creazione di un thread per eseguire il lavoro possibile più appropriata. Ad esempio, la creazione di un'immagine di anteprima per una foto che è stato appena aggiunto a un'app di raccolta foto deve probabilmente si verificano in un servizio. Tuttavia, un thread potrebbe essere più opportuno riprodurre musica che debba solo essere ascoltato mentre un'attività è in primo piano.

Operazioni in background possono essere suddivisi in due ampie classificazioni:

1. **Attività di lunga durata esecuzione** &ndash; si tratta di lavoro che è in corso finché non viene interrotto in modo esplicito. Un esempio di un _attività a esecuzione prolungata_ è un'app che consente di trasmettere musica o che deve monitorare i dati raccolti da un sensore. Anche se l'applicazione non dispone di alcuna interfaccia utente visibile, devono eseguire queste attività.
2. **Attività periodiche** &ndash; (talvolta detto un _processo_) un'attività periodica è uno di relativamente breve durata (secondi) e viene eseguito in base a una pianificazione (ad esempio una volta al giorno per una settimana o forse solo una volta nel Avanti 60 secondi). Un esempio di questo è un file di download da internet o la generazione di un'immagine di anteprima per un'immagine.

Esistono quattro tipi diversi di servizi Android:

* **Servizio associato** &ndash; A _associato servizio_ è un servizio che dispone di un altro componente (in genere un'attività) a esso associato. Un servizio associato fornisce un'interfaccia che consente al servizio di interagire tra loro e il componente associato. Una volta non sono presenti più client associato al servizio, Android verrà arrestato il servizio. 

* **`IntentService`** &ndash; Un _`IntentService`_ è una sottoclasse specializzata della `Service` classe che semplifica la creazione del servizio e l'utilizzo. Un `IntentService` è progettato per gestire singole chiamate autonome. A differenza di un servizio, che può gestire contemporaneamente più chiamate, un `IntentService` è più simile a un _processore della coda di lavoro_ &ndash; lavoro viene messo in coda e un `IntentService` elabora ogni processo uno alla volta in un singolo thread di lavoro. In genere, un`IntentService` non è associato a un'attività o un frammento. 

* **Servizio avviato** &ndash; A _servizio avviato_ è un servizio che è stato avviato da un altro componente Android (ad esempio un'attività) e l'esecuzione continui in background finché non indica un elemento in modo esplicito il arresto del servizio. A differenza di un servizio associato, un servizio avviato non dispone di tutti i client direttamente a esso associati. Per questo motivo, è importante progettare i servizi avviati in modo che può essere riavviati normalmente in base alle esigenze.

* **Servizio ibrido** &ndash; A _servizio ibrido_ è un servizio che ha le caratteristiche di un _servizio avviato_ e _associato servizio_. Un servizio ibrido può essere avviato quando si associa un componente o può essere avviato da un evento. Un componente client può o non può essere associato a questo servizio ibrido. Un servizio ibrido verrà eseguiti fino a quando non è richiesto in modo esplicito per arrestare o fino a quando non sono presenti più client a esso associato.

Il tipo di servizio da usare dipende molto requisiti dell'applicazione. Come regola generale, un `IntentService` o un servizio associato sono sufficienti per la maggior parte delle attività che deve eseguire un'applicazione Android, in modo preferenza dovrebbe essere concessa a uno di questi due tipi di servizi. Un `IntentService` è una buona scelta per l'attività "monofase", ad esempio download di un file, mentre un servizio associato potrebbe essere adatto quando frequenti interazioni con un'attività o un frammento è obbligatorio. 

Mentre la maggior parte dei servizi vengono eseguiti in background, è una sottocategoria speciale nota come un _servizio di primo piano_. Si tratta di un servizio che è la priorità più alta (rispetto a un normale servizio) per eseguire alcune operazioni per l'utente (ad esempio musica). 

È anche possibile eseguire un servizio nel proprio processo sullo stesso dispositivo, questa talvolta detta un _servizio remoto_ o come un _servizio out-of-process_. Questo richiede ulteriori operazioni per creare, ma può essere utile per quando un'applicazione deve condividere funzionalità con altre applicazioni e può, in alcuni casi, migliorare l'esperienza utente di un'applicazione. 

### <a name="background-execution-limits-in-android-80"></a>Limiti di esecuzione in background in Android 8.0

Avvio in Android 8.0 (livello API 26), non è più un'applicazione Android hanno la possibilità di eseguire liberamente in background. In primo piano, un'app può avviare ed eseguire servizi senza alcuna restrizione. Quando si sposta un'applicazione in background, Android concederà l'app un determinato periodo di tempo per avviare e utilizzare i servizi. Una volta trascorso tale tempo, l'app non è più possibile avviare qualsiasi servizio e tutti i servizi che sono stati avviati verranno terminati. In questo punto non è possibile per l'app eseguire le operazioni. Android prende in considerazione un'applicazione può essere in primo piano, se viene soddisfatta una delle condizioni seguenti:

* È un'attività visibile (avviato o sospeso).
* L'app ha avviato un servizio di primo piano.
* Un'altra app in primo piano e utilizza i componenti da un'app che sarebbe altrimenti in background. Un esempio di ciò è se un'applicazione, che è in primo piano, è associata a un servizio fornito da applicazione B. applicazione B quindi anche sarebbe considerato in primo piano e non è stata terminata da Android per in background.

Esistono alcune situazioni in cui, anche se un'app è in background, Android verrà riattivare l'app e ridurre le restrizioni per alcuni minuti, consentendo all'app di eseguire alcune operazioni:
* Una priorità alta Firebase Cloud messaggio viene ricevuta dall'app.
* L'app riceve una trasmissione, ad esempio 
* L'applicazione riceve un viene eseguito un `PendingIntent` in risposta a una notifica.

Le applicazioni di xamarin. Android esistenti potrebbero essere necessario modificare la modalità eseguono operazioni in background per evitare eventuali problemi che potrebbero verificarsi in Android 8.0. Ecco alcuni alterantives pratiche a un servizio Android:

* **Pianificare il lavoro eseguito in background tramite il pianificatore di processi Android o il [Firebase processo Dispatcher](~/android/platform/firebase-job-dispatcher.md)**  &ndash; queste due librerie forniscono un framework per le applicazioni di separare attività in background per _processi_, un'unità di lavoro discreta. Le app possono quindi pianificare il processo con il sistema operativo e alcuni criteri su quando è possibile eseguire i processi.
* **Avviare il servizio in primo piano** &ndash; un servizio di primo piano è utile quando l'app deve eseguire alcune attività in background e l'utente potrebbe essere necessario periodicamente interagire con tale attività. Il servizio di primo piano verrà visualizzato un avviso permanente in modo che l'utente è a conoscenza che l'app è in esecuzione un'attività in background e fornisce inoltre un modo per monitorare o interagire con l'attività. Un esempio di questo oggetto può essere un'app il podcast riproduzione di un podcast all'utente o forse scaricato un episodio di podcast in modo che è in uso in un secondo momento. 
* **Utilizzare una priorità alta Firebase Cloud messaggio (FCM)** &ndash; Android quando riceve una priorità alta FCM per un'app, consentirà di tale app eseguire i servizi in background per un breve periodo di tempo. Potrebbe trattarsi di una valida alternativa alla disponibilità di un servizio in background che esegue il polling di un'applicazione in background. 
* **Rinviare per quando l'app passa in primo piano** &ndash; se nessuna delle soluzioni precedente sia valida, quindi App necessario sviluppare le proprie possibilità di sospendere e riprendere il lavoro quando l'app passa in primo piano.

## <a name="related-links"></a>Collegamenti correlati

* [Consente di limitare l'esecuzione in Background Oreo Android](https://www.youtube.com/watch?v=Pumf_4yjTMc)