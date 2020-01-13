---
title: Creazione di servizi Android
description: Questa guida illustra i servizi di Xamarin.Android, che sono componenti Android che consentono di eseguire il lavoro senza un'interfaccia utente attiva. I servizi sono comunemente usati per le attività eseguite in background, ad esempio i calcoli che richiedono molto tempo, il download di file, la riproduzione di musica e così via. Vengono illustrati i diversi scenari per cui sono adatti i servizi e viene illustrato come implementarli sia per eseguire attività in background con esecuzione prolungata che per fornire un'interfaccia per le chiamate di procedure remote.
ms.prod: xamarin
ms.assetid: BA371A59-6F7A-F62A-02FC-28253504ACC9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: b427bbadc72ca557a37c652e853674fdf849c2c8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024563"
---
# <a name="creating-android-services"></a>Creazione di servizi Android

_Questa guida illustra i servizi di Xamarin.Android, che sono componenti Android che consentono di eseguire il lavoro senza un'interfaccia utente attiva. I servizi sono comunemente usati per le attività eseguite in background, ad esempio i calcoli che richiedono molto tempo, il download di file, la riproduzione di musica e così via. Vengono illustrati i diversi scenari per cui sono adatti i servizi e viene illustrato come implementarli sia per eseguire attività in background con esecuzione prolungata che per fornire un'interfaccia per le chiamate di procedure remote._

## <a name="android-services-overview"></a>Panoramica dei servizi Android

Le app per dispositivi mobili non sono come le app desktop. I desktop hanno quantità di risorse coesistenti, ad esempio la proprietà dello schermo, la memoria, lo spazio di archiviazione e un alimentatore connesso, ma non i dispositivi mobili. Questi vincoli forzano il comportamento delle app per dispositivi mobili in modo diverso. Ad esempio, la piccola schermata in un dispositivo mobile in genere significa che è visibile una sola app, ad esempio attività, alla volta. Altre attività vengono spostate in background e inserite nello stato Suspended, in cui non è possibile eseguire alcuna operazione. Tuttavia, solo perché un'applicazione Android è in background non significa che non è possibile che l'app continui a funzionare. 

Le applicazioni Android sono costituite da almeno uno dei quattro componenti principali seguenti: _attività_, _ricevitori broadcast_, _provider di contenuti_e _Servizi_. Le attività sono l'elemento fondamentale di molte ottime applicazioni Android, perché forniscono l'interfaccia utente che consente a un utente di interagire con l'applicazione. Tuttavia, quando si tratta di eseguire operazioni simultanee o in background, le attività non sono sempre la scelta migliore.

Il meccanismo principale per il lavoro in background in Android è il _servizio_. Un servizio Android è un componente progettato per eseguire alcune operazioni senza un'interfaccia utente. Un servizio può scaricare un file, riprodurre musica o applicare un filtro a un'immagine. I servizi possono essere usati anche per la comunicazione interprocesso (_IPC_) tra le applicazioni Android. Ad esempio, un'app Android può usare il servizio Music Player che si trova in un'altra app o un'app potrebbe esporre i dati, ad esempio le informazioni di contatto di un utente, ad altre app tramite un servizio. 

I servizi e la loro capacità di eseguire operazioni in background sono fondamentali per fornire un'interfaccia utente uniforme e fluida. Tutte le applicazioni Android hanno un _thread principale_ (noto anche come _thread dell'interfaccia utente_) in cui vengono eseguite le attività. Per tenere il dispositivo reattivo, Android deve essere in grado di aggiornare l'interfaccia utente alla velocità di 60 fotogrammi al secondo. Se un'app Android esegue una quantità eccessiva di lavoro sul thread principale, Android eliminerà i frame, che a sua volta fa sì che l'interfaccia utente sembri Jerky (anche a volte definita _Janky_). Ciò significa che tutte le operazioni eseguite sul thread dell'interfaccia utente devono essere completate nell'intervallo di tempo tra due frame, circa 16 millisecondi (1 secondo ogni 60 di frame). 

Per risolvere questo problema, uno sviluppatore può utilizzare i thread in un'attività per eseguire alcune operazioni che bloccano l'interfaccia utente. Tuttavia, ciò potrebbe causare problemi. È molto probabile che Android elimini e ricrei più istanze dell'attività. Tuttavia, Android non eliminerà automaticamente i thread, che potrebbero causare perdite di memoria. Un esempio principale è quando il [dispositivo viene ruotato](~/android/app-fundamentals/handling-rotation.md) &ndash; Android tenterà di eliminare l'istanza dell'attività e quindi di ricrearne una nuova:

![Quando il dispositivo viene ruotato, l'istanza 1 viene distrutta e viene creata l'istanza 2](images/image-01.png)

Si tratta di una potenziale perdita di memoria &ndash; il thread creato dalla prima istanza dell'attività sarà ancora in esecuzione. Se il thread ha un riferimento alla prima istanza dell'attività, questo impedirà a Android di eseguire il Garbage Collection dell'oggetto. Tuttavia, la seconda istanza dell'attività viene ancora creata, che a sua volta potrebbe creare un nuovo thread. La rotazione del dispositivo più volte in rapida successione può esaurire tutta la RAM e forzare Android a terminare l'intera applicazione per recuperare memoria.

Come regola generale, se il lavoro da eseguire dovrebbe essere in esecuzione in un'attività, è necessario creare un servizio per eseguire tale operazione. Tuttavia, se il lavoro è applicabile solo nel contesto di un'attività, la creazione di un thread per eseguire il lavoro potrebbe essere più appropriata. Ad esempio, la creazione di un'anteprima per una foto appena aggiunta a un'app della raccolta foto dovrebbe probabilmente verificarsi in un servizio. Tuttavia, un thread potrebbe essere più appropriato per riprodurre musica da ascoltare solo quando un'attività è in primo piano.

Il lavoro in background può essere suddiviso in due classificazioni generali:

1. **Attività a esecuzione Prolungata** &ndash; questa operazione è in corso fino a quando non viene arrestata in modo esplicito. Un esempio di _attività a esecuzione prolungata_ è un'app che trasmette musica o che deve monitorare i dati raccolti da un sensore. Queste attività devono essere eseguite anche se l'applicazione non dispone di un'interfaccia utente visibile.
2. Le **attività periodiche** &ndash; (talvolta definita _processo_) un'attività periodica è un periodo di tempo relativamente breve (alcuni secondi) e viene eseguito in base a una pianificazione (ad esempio una volta al giorno per una settimana o una sola volta nei prossimi 60 secondi). Un esempio è il download di un file da Internet o la generazione di un'anteprima per un'immagine.

Sono disponibili quattro tipi diversi di servizi Android:

* Il **servizio associato** &ndash; un _servizio associato_ è un servizio a cui è associato un altro componente (in genere un'attività). Un servizio associato fornisce un'interfaccia che consente al componente associato e al servizio di interagire tra loro. Quando non ci sono altri client associati al servizio, Android arresterà il servizio. 

* **`IntentService`** &ndash; un _`IntentService`_ è una sottoclasse specializzata della classe `Service` che semplifica la creazione e l'utilizzo dei servizi. Un `IntentService` è progettato per gestire singole chiamate autonome. A differenza di un servizio, che può gestire contemporaneamente più chiamate, una `IntentService` è più simile a un _processore della coda di lavoro_ &ndash; il lavoro viene accodato e un `IntentService` elabora ogni processo uno alla volta in un singolo thread di lavoro. In genere, un`IntentService` non è associato a un'attività o a un frammento. 

* Il **servizio avviato** &ndash; un servizio _avviato_ è un servizio che è stato avviato da un altro componente Android, ad esempio un'attività, e viene eseguito in modo continuo in background fino a quando un elemento non indica esplicitamente al servizio di arrestarsi. A differenza di un servizio associato, a un servizio avviato non è associato alcun client direttamente. Per questo motivo, è importante progettare i servizi avviati in modo che possano essere riavviati correttamente in base alle esigenze.

* Il servizio **ibrido** &ndash; un servizio _ibrido_ è un servizio con le caratteristiche di un servizio _avviato_ e di un _servizio associato_. Un servizio ibrido può essere avviato da quando un componente viene associato ad esso oppure può essere avviato da un determinato evento. Un componente client può essere associato o meno al servizio ibrido. Un servizio ibrido continuerà a funzionare fino a quando non viene indicato in modo esplicito di arrestarsi o fino a quando non ci sono altri client associati.

Il tipo di servizio da usare dipende molto dai requisiti dell'applicazione. Come regola generale, una `IntentService` o un servizio associato è sufficiente per la maggior parte delle attività che devono essere eseguite da un'applicazione Android, quindi è necessario assegnare una preferenza a uno di questi due tipi di servizi. Un `IntentService` è una scelta ottimale per le attività "monouso", ad esempio il download di un file, mentre un servizio associato è adatto quando sono necessarie interazioni frequenti con un'attività o un frammento. 

Mentre la maggior parte dei servizi viene eseguita in background, esiste una sottocategoria speciale nota come _servizio in primo piano_. Si tratta di un servizio a cui viene assegnata una priorità più alta (rispetto a un servizio normale) per eseguire alcune operazioni per l'utente (ad esempio la riproduzione di musica). 

È anche possibile eseguire un servizio nel proprio processo sullo stesso dispositivo, a volte definito _servizio remoto_ o come _servizio out-of-process (out-of-process_). Questa operazione richiede più impegno per la creazione, ma può essere utile quando un'applicazione deve condividere funzionalità con altre applicazioni e può, in alcuni casi, migliorare l'esperienza utente di un'applicazione. 

### <a name="background-execution-limits-in-android-80"></a>Limiti di esecuzione in background in Android 8,0

A partire da Android 8,0 (API level 26), un'applicazione Android non è più in grado di eseguire liberamente in background. In primo piano, un'app può avviare ed eseguire servizi senza restrizioni. Quando un'applicazione viene spostata in background, Android concede all'app una certa quantità di tempo per avviare e usare i servizi. Una volta trascorso tale periodo di tempo, l'app non può più avviare alcun servizio e tutti i servizi avviati verranno interrotti. A questo punto, l'app non può eseguire alcuna operazione. Android considera che un'applicazione sia in primo piano se viene soddisfatta una delle condizioni seguenti:

* È presente un'attività visibile (avviata o sospesa).
* L'app ha avviato un servizio in primo piano.
* Un'altra app è in primo piano e usa i componenti di un'app che altrimenti sarebbero in background. Un esempio è se l'applicazione A, che è in primo piano, è associata a un servizio fornito dall'applicazione B. l'applicazione B verrebbe quindi considerata in primo piano e non terminata da Android per essere in background.

In alcuni casi, anche se un'app è in background, Android riattiverà l'app e rilasserà le restrizioni per alcuni minuti, consentendo all'app di eseguire alcune operazioni:

* L'app riceve un messaggio di Firebase cloud a priorità alta.
* L'app riceve una trasmissione. 
* L'applicazione riceve ed esegue un `PendingIntent` in risposta a una notifica.

Le applicazioni Xamarin.Android esistenti potrebbero dover modificare il modo in cui eseguono il lavoro in background per evitare eventuali problemi che possono verificarsi in Android 8,0. Di seguito sono riportate alcune alternative pratiche a un servizio Android:

* **Pianificare il lavoro da eseguire in background usando l'utilità di pianificazione dei processi Android o il [dispatcher di processi Firebase](~/android/platform/firebase-job-dispatcher.md)**  &ndash; queste due librerie forniscono un Framework per le applicazioni per separare il lavoro in background nei _processi_, un'unità di lavoro discreta. Le app possono quindi pianificare il processo con il sistema operativo insieme ad alcuni criteri relativi al momento in cui il processo può essere eseguito.
* **Avviare il servizio in primo piano** &ndash; un servizio in primo piano è utile quando l'app deve eseguire un'attività in background e l'utente potrebbe dover interagire periodicamente con tale attività. Il servizio in primo piano visualizzerà una notifica persistente, in modo che l'utente sappia che l'app sta eseguendo un'attività in background e fornisce anche un modo per monitorare o interagire con l'attività. Un esempio è costituito da un'app di podcasting che riproduce un podcast per l'utente o forse Scarica un episodio podcast in modo che possa essere goduto in un secondo momento. 
* **Usare un messaggio di Firebase cloud Message (FCM) con priorità alta** &ndash; quando Android riceve un FCM con priorità alta per un'app, consente all'app di eseguire i servizi in background per un breve periodo di tempo. Si tratta di una valida alternativa alla presenza di un servizio in background che esegue il polling di un'applicazione in background. 
* **Rinviare il lavoro quando l'app entra in primo piano** &ndash; se nessuna delle soluzioni precedenti è realizzabile, le app devono sviluppare il proprio modo per sospendere e riprendere il lavoro quando l'app viene visualizzata in primo piano.

## <a name="related-links"></a>Collegamenti correlati

* [Limiti di esecuzione in background di Android Oreo](https://www.youtube.com/watch?v=Pumf_4yjTMc)
