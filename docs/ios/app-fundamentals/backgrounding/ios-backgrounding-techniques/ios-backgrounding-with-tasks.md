---
title: iOS elaborazione in background in attività
description: Questo documento descrive come usare le attività in background per eseguire attività con esecuzione prolungata dopo che un'applicazione viene inserita in background.
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 9d304ee64e7716413febc475e721f5eb39043109
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351538"
---
# <a name="ios-backgrounding-with-tasks"></a>iOS elaborazione in background in attività

Il modo più semplice per eseguire l'elaborazione in background in IOS è suddividere i requisiti backgrounding in attività ed eseguire le attività in background. Le attività sono in un limite di tempo strict e ottengono in genere circa 600 secondi (10 minuti) del tempo di elaborazione dopo che un'applicazione è stato spostato in background in iOS 6 e meno di 10 minuti in iOS 7 +.

Le attività in background possono essere suddivisi in tre categorie:

1.  **Le attività in background-Safe** : viene chiamato in qualsiasi punto nel l'applicazione in cui è presente un'attività non si vuole interrotta deve l'applicazione, immettere lo sfondo.
1.  **Attività DidEnterBackground** : viene chiamato durante la `DidEnterBackground` metodo del ciclo di vita delle applicazioni utili per la pulizia e il salvataggio dello stato.
1.  **I trasferimenti (iOS 7 +) di sfondo** -un tipo speciale di attività in background usato per eseguire trasferimenti di rete in iOS 7. A differenza delle normali attività, trasferimenti in background non è un limite di tempo predeterminato.


In background-safe e `DidEnterBackground` attività sono sicuri per l'uso on iOS 6 e iOS 7, con alcune piccole differenze. Verrà esaminato questi due tipi di attività in modo più dettagliato.

## <a name="creating-background-safe-tasks"></a>Creazione di attività in Background-Safe

Alcune applicazioni contengono le attività che non devono essere interrotta da iOS deve modificare l'applicazione dello stato. Un modo per proteggere queste attività venga interrotto deve registrarli con iOS come attività con esecuzione prolungata. È possibile usare questo modello in un punto qualsiasi all'interno dell'applicazione in cui non si desidera un'attività in corso interrotta deve put l'utente dell'app in background. Un ottimo candidato per questo modello potrebbe essere attività quali l'invio di informazioni di registrazione del nuovo utente al server o la verifica per determinare se le informazioni di accesso.

Il frammento di codice seguente illustra la registrazione di un'attività da eseguire in background:

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

Il processo di registrazione un'attività con un identificatore univoco, di coppie `taskID`e quindi ne esegue il wrapping nella corrispondenza `BeginBackgroundTask` e `EndBackgroundTask` chiamate. Per generare l'identificatore, abbiamo effettuare una chiamata ai `BeginBackgroundTask` metodo su di `UIApplication` dell'oggetto e quindi avviare l'attività di lunga durata, in genere in un nuovo thread. Una volta completata l'attività, chiamiamo `EndBackgroundTask` e passare lo stesso identificatore. Questo è importante perché iOS per terminare l'applicazione se un `BeginBackgroundTask` chiamata non ha un corrispondente `EndBackgroundTask`.

> [!IMPORTANT]
> Le attività in background-safe è possono eseguire sul thread principale o un thread in background, in base alle esigenze dell'applicazione.


## <a name="performing-tasks-during-didenterbackground"></a>Esecuzione di attività durante DidEnterBackground

Oltre a creare un'attività a esecuzione prolungata in background-safe, la registrazione può essere utilizzata per avviare le attività come un'applicazione viene inserita in background. iOS offre un metodo di evento nel *AppDelegate* classe denominata `DidEnterBackground` che può essere utilizzato per salvare lo stato dell'applicazione, salvare i dati utente e crittografare i contenuti sensibili prima di un'applicazione passerà in background. Un'applicazione dispone di circa cinque secondi per restituire da questo metodo o verrà terminata. Di conseguenza, le attività di pulizia che potrebbero essere necessari più di cinque secondi per il completamento possono essere chiamate dall'interno il `DidEnterBackground` (metodo). Queste attività devono essere richiamate su un thread separato.

Il processo è quasi identico a quello di registrazione di un'attività a esecuzione prolungata. Il frammento di codice seguente viene illustrata questa in azione:

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

Iniziare eseguendo l'override di `DidEnterBackground` metodo nella `AppDelegate`, in cui si registra l'attività tramite `BeginBackgroundTask` come è stato fatto nell'esempio precedente. Successivamente, è possibile distribuire un nuovo thread ed eseguire l'attività a esecuzione prolungata. Si noti che il `EndBackgroundTask` viene ora effettuata all'interno di attività a esecuzione prolungata, poiché il `DidEnterBackground` metodo sarà ritornato già.

> [!IMPORTANT]
> iOS Usa una [sorveglianza meccanismo](http://developer.apple.com/library/ios/qa/qa1693/_index.html) per assicurare che rimanga reattiva dell'interfaccia utente di un'applicazione. Un'applicazione che impiega troppo tempo in `DidEnterBackground` risponderanno nell'interfaccia utente. Consente l'avvio di attività da eseguire in background `DidEnterBackground` da restituire in modo tempestivo, mantenere reattiva l'interfaccia utente e impedire che il watchdog terminazione dell'applicazione.


## <a name="handling-background-task-time-limits"></a>Limiti di tempo di attività in Background la gestione

iOS consente di definire limiti strict su come long un'attività in background può essere eseguito e se il `EndBackgroundTask` chiamata non viene stabilita entro il tempo stabilito, l'applicazione verrà terminata. Per tenere traccia dell'elaborazione in background in tempo rimanente e utilizzo dei gestori di scadenza quando necessario, è possibile evitare di terminare l'applicazione iOS.

### <a name="accessing-background-time-remaining"></a>L'accesso a sfondo tempo rimanente

Se un'applicazione con attività registrate Ottiene spostata in background eseguire le attività registrate otterrà circa 600 secondi. È possibile controllare quanto tempo l'attività deve essere completata usando il metodo statico `BackgroundTimeRemaining` proprietà del `UIApplication` classe. Il codice riportato di seguito fornirà il tempo, espresso in secondi, che ha lasciato la nostra attività in background:

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>Evitare la chiusura delle App con i gestori di scadenza

Oltre a concedere l'accesso per il `BackgroundTimeRemaining` iOS di proprietà, fornisce un modo per gestire la scadenza di tempo in background mediante un **gestore scadenza**. Si tratta di un blocco di codice verrà eseguiti quando il tempo assegnato per un'attività sta per scadere facoltativo. Il codice nel gestore di scadenza chiama `EndBackgroundTask` e passa l'ID attività, che indica che l'app funzioni correttamente e che impedisce a iOS di terminazione, anche se l'attività viene eseguita all'esterno di tempo. `EndBackgroundTask` deve essere chiamato all'interno del gestore di scadenza, oltre che durante il normale funzionamento di esecuzione. 

Il gestore di scadenza viene espresso come una funzione anonima tramite un'espressione lambda, come illustrato di seguito:

```csharp
Task.Factory.StartNew( () => {

    //expirationHandler only called if background time allowed exceeded
    var taskId = UIApplication.SharedApplication.BeginBackgroundTask(() => {
        Console.WriteLine("Exhausted time");
        UIApplication.SharedApplication.EndBackgroundTask(taskId); 
    });
    while(myFlag == true)
    {
        Console.WriteLine(UIApplication.SharedApplication.TimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

Anche se i gestori di scadenza non sono necessari per eseguire il codice, è sempre necessario utilizzare un gestore di scadenza con un'attività in background.

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>Attività in background in iOS 7 +

Il cambiamento più significativo in iOS 7 per quanto riguarda le attività in background non è modo in cui vengono implementate le attività, ma quando l'esecuzione.

È importante ricordare che pre-iOS 7, un'attività in esecuzione in background doveva 600 secondi completare. Uno dei motivi per questo limite è che un'attività in esecuzione in background consigliabile mantenere i dispositivi attivi per la durata dell'attività:

 [![](ios-backgrounding-with-tasks-images/ios6.png "Grafico dell'attività di app molto attivi pre-iOS 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

l'elaborazione in background di iOS 7 è ottimizzato per la durata della batteria. In iOS 7, elaborazione in background in diventa opportunistico: invece di mantenere i dispositivi attivi, le attività rispettano quando il dispositivo passa alla modalità sospensione ed eseguire invece ordinarne l'elaborazione in blocchi quando il dispositivo viene attivato per gestire chiamate telefoniche, notifiche, in ingresso messaggi di posta elettronica e l'altro interruzioni più comuni. Il diagramma seguente fornisce informazioni approfondite come un'attività potrebbe essere interrotta backup:

 [![](ios-backgrounding-with-tasks-images/ios7.png "Grafico dell'attività viene suddiviso in blocchi di post-iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

Poiché il momento dell'esecuzione dell'attività non è più continua, le attività eseguibili tramite i trasferimenti di rete devono essere gestite in modo diverso in iOS 7. Gli sviluppatori sono invitati a usare il `NSURlSession` API per gestire i trasferimenti di rete. Nella sezione successiva viene fornita una panoramica dei trasferimenti in background.

 <a name="background-transfers" />

## <a name="background-transfers"></a>Trasferimenti in background

La struttura portante di trasferimenti in background in iOS 7 è il nuovo `NSURLSession` API. `NSURLSession` Consente di creare attività per:

1.  Trasferire il contenuto tramite le interruzioni di rete e il dispositivo.
1.  Caricare e scaricare file di grandi dimensioni ( *servizio di trasferimento in Background* ).


Esaminiamo più da vicino il funzionamento.

### <a name="nsurlsession-api"></a>API di NSURLSession

 `NSURLSession` è un'API potente per il trasferimento di contenuto attraverso la rete. Fornisce un set di strumenti per gestire il trasferimento dei dati tramite le interruzioni della rete e le modifiche dello stato dell'applicazione.

Il `NSURLSession` API crea uno o più sessioni, che a sua volta spawn attività per shuttle di blocchi di dati correlati attraverso la rete. Le attività eseguite in modo asincrono per il trasferimento dati rapido e affidabile. Poiché `NSURLSession` è asincrono, ogni sessione richiede un blocco del gestore di completamento per consentire il sistema e l'applicazione conosce quando un trasferimento è stato completato.

Per eseguire un trasferimento tramite rete valida per pre-iOS 7 e post-iOS 7, controllare se un `NSURLSession` è disponibile per i trasferimenti di accodamento e usare un'attività in background regolari per eseguire il trasferimento, se non è:

```csharp
if ([NSURLSession class]) {
  // Create a background session and enqueue transfers
}
else {
  // Start a background task and transfer directly
  // Do NOT make calls to update the UI here!
}
```

> [!IMPORTANT]
> Evitare di effettuare chiamate all'aggiornamento l'interfaccia utente da background in iOS codice conforme a 6, iOS 6 non supporta gli aggiornamenti dell'interfaccia utente in background e terminare l'applicazione.


Il `NSURLSession` API include un'ampia gamma di funzionalità per gestire l'autenticazione, gestire i trasferimenti non riusciti e segnalare errori lato client, ma non sul lato server -. Consente di sfruttare che le interruzioni nelle attività di fase di esecuzione introdotta in iOS 7 e fornisce inoltre supporto per il trasferimento dei file di grandi dimensioni in modo rapido e affidabile. La prossima sezione viene illustrata questa funzionalità di secondo.

### <a name="background-transfer-service"></a>Servizio di trasferimento in background

Prima di iOS 7, caricare o scaricare file in background non erano affidabili. Le attività in background ottengono un periodo di tempo limitato per l'esecuzione, ma il tempo che necessario per trasferire un file varia in base alla rete e le dimensioni del file. In iOS 7, è possibile usare un `NSURLSession` correttamente caricare e scaricare file di grandi dimensioni. Il particolare `NSURLSession` tipo di sessione che gestisce i trasferimenti di rete del file di grandi dimensioni in background è noto come il *servizio di trasferimento in Background*.

Trasferimenti avviati tramite il servizio di trasferimento in Background sono gestiti dal sistema operativo e forniscono le API per gestire l'autenticazione e gli errori. Perché i trasferimenti non sono associati a un limite di tempo arbitrario, possono essere utilizzati per caricare o scaricare file di grandi dimensioni, l'aggiornamento automatico del contenuto in background e altro ancora. Vedere le [procedura dettagliata di trasferimento in Background](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md) per informazioni dettagliate su come implementare il servizio.

Il servizio di trasferimento in Background è spesso accompagnato da recupero in Background o notifiche Remote per consentire le applicazioni di aggiornare il contenuto in background. Nelle prossime due sezioni, si introducono il concetto di registrazione di intere applicazioni per l'esecuzione in background in iOS 6 e iOS 7.

