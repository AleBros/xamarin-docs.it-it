---
title: iOS Backgrounding con attività
description: Questo documento viene descritto come utilizzare le attività in background per eseguire attività a esecuzione prolungata, dopo aver posizionata un'applicazione in background.
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: a95ca128bc6de7b2adc75511a581f5d2779d9c06
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784355"
---
# <a name="ios-backgrounding-with-tasks"></a>iOS Backgrounding con attività

Il modo più semplice per eseguire backgrounding in iOS è per suddividere i requisiti backgrounding in attività ed eseguire le attività in background. Attività in un limite di tempo strict e ottengono in genere circa 600 secondi (10 minuti) di tempo di elaborazione dopo che un'applicazione è stato spostato in background in iOS 6 e minore di 10 minuti in iOS 7 +.

Attività in background possono essere suddiviso in tre categorie:

1.  **Attività di background Safe** : viene chiamato in qualsiasi punto dell'applicazione in cui si dispone di un'attività non si desidera interrotta l'applicazione immettere lo sfondo.
1.  **Attività DidEnterBackground** : viene chiamato durante la `DidEnterBackground` metodo del ciclo di vita delle applicazioni per facilitare la pulizia e di salvataggio dello stato.
1.  **(IOS 7 +) i trasferimenti in background** -un tipo speciale di attività in background usato per eseguire i trasferimenti di rete su iOS 7. A differenza delle normali attività, i trasferimenti in background non sono un limite di tempo predeterminato.


Sfondo-safe e `DidEnterBackground` attività sono sicuri in iOS 6 e iOS 7, con alcune piccole differenze. Consente di analizzare questi due tipi di attività in modo più dettagliato.

## <a name="creating-background-safe-tasks"></a>Creazione di attività in Background-Safe

Alcune applicazioni contengono attività che non deve essere interrotta da iOS deve l'applicazione di modificare lo stato. Un modo per proteggere queste attività, l'interruzione è loro di registrarsi con iOS come attività a esecuzione prolungata. È possibile utilizzare questo modello in un punto qualsiasi nell'applicazione in cui non si desidera un'attività viene interrotta deve put l'utente dell'applicazione in background. Un ottimo candidato per questo motivo sarebbe attività quali l'invio di informazioni di registrazione del nuovo utente al server o la verifica delle informazioni di accesso.

Frammento di codice riportato di seguito viene illustrato come registrare un'attività da eseguire in background:

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

Le coppie di un'attività con un identificatore univoco, il processo di registrazione `taskID`e quindi ne esegue il wrapping in corrispondenza `BeginBackgroundTask` e `EndBackgroundTask` chiamate. Per generare l'identificatore, si effettua una chiamata al `BeginBackgroundTask` metodo il `UIApplication` dell'oggetto e quindi avviare l'attività di lunga durata, in genere in un nuovo thread. Quando l'attività viene completata, viene detta `EndBackgroundTask` e passare lo stesso identificatore. Questo è importante perché iOS terminerà l'applicazione se un `BeginBackgroundTask` chiamata non ha un corrispondente `EndBackgroundTask`.

> [!IMPORTANT]
> Attività in background-safe può eseguire nel thread principale o in un thread in background, in base alle esigenze dell'applicazione.


## <a name="performing-tasks-during-didenterbackground"></a>Esecuzione di attività durante DidEnterBackground

Oltre a rendere un'attività a esecuzione prolungata background-safe, registrazione consente di avviare l'attività come un'applicazione viene messa in background. iOS fornisce un metodo di evento nel *AppDelegate* classe denominata `DidEnterBackground` che può essere utilizzato per salvare lo stato dell'applicazione, salvare i dati utente e crittografare contenuto riservato prima che un'applicazione attivata lo sfondo. Circa cinque secondi per restituire da questo metodo dispone di un'applicazione o terminato. Pertanto, le attività di pulizia che potrebbero richiedere più di cinque secondi possono essere chiamate da all'interno di `DidEnterBackground` metodo. Queste attività devono essere richiamate su un thread separato.

Il processo è quasi identico a quella di registrazione di un'attività a esecuzione prolungata. Frammento di codice riportato di seguito viene illustrata questa azione:

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

Iniziare eseguendo l'override di `DidEnterBackground` metodo il `AppDelegate`, in cui si registra l'attività tramite `BeginBackgroundTask` come è stato fatto nell'esempio precedente. Successivamente, è possibile distribuire un nuovo thread ed eseguire l'attività a esecuzione prolungata. Si noti che il `EndBackgroundTask` chiamata viene ora effettuata da all'interno delle attività a esecuzione prolungata, poiché il `DidEnterBackground` metodo sarà ritornato già.

> [!IMPORTANT]
> iOS Usa una [sorveglianza meccanismo](http://developer.apple.com/library/ios/qa/qa1693/_index.html) per assicurare che rimanga reattiva dell'interfaccia utente dell'applicazione. Un'applicazione che impiega troppo tempo `DidEnterBackground` diventerà non risponde nell'interfaccia utente. Iniziando attività da eseguire in background consente `DidEnterBackground` da restituire in modo tempestivo, mantenere reattiva l'interfaccia utente e impedire che il watchdog la terminazione dell'applicazione.


## <a name="handling-background-task-time-limits"></a>Limiti di tempo di attività in Background gestione

iOS colloca rigorosamente limitato in modo prolungata un'attività in background è possibile eseguire e se il `EndBackgroundTask` chiamata non viene effettuata entro il tempo stabilito, l'applicazione verrà terminata. Tenendo traccia del rimanenti backgrounding tempo e utilizzo dei gestori di scadenza quando necessario, è possibile evitare di interrompere l'applicazione iOS.

### <a name="accessing-background-time-remaining"></a>L'accesso a Background tempo rimanente

Se un'applicazione con attività registrate viene spostata per lo sfondo eseguire le attività registrate otterranno circa 600 secondi. È possibile controllare quanto tempo l'attività deve essere completata usando il metodo statico `BackgroundTimeRemaining` proprietà la `UIApplication` classe. Il codice seguente ci consentirà di tempo, espresso in secondi, che ha lasciato l'attività in background:

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>Evitare di terminazione di App con i gestori di scadenza

Oltre a un accesso di `BackgroundTimeRemaining` iOS di proprietà, fornisce un modo per gestire la scadenza ora sfondo mediante un **scadenza gestore**. Si tratta di un blocco di codice che verrà vengono eseguito quando il tempo a disposizione per un'attività sta per scadere facoltativo. Chiamato dal codice nel gestore di scadenza `EndBackgroundTask` e passa l'ID attività, che indica che l'app funzioni correttamente e impedisce che iOS terminato l'app, anche se ora si esaurisce l'attività. `EndBackgroundTask` deve essere chiamato all'interno del gestore di scadenza, nonché nelle normali di esecuzione. 

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

Mentre i gestori di scadenza non sono necessari per l'esecuzione del codice, è necessario utilizzare sempre un gestore di scadenza con un'attività in background.

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>Attività in background in iOS 7 +

La più importante modifica di iOS 7 per quanto riguarda le attività in background non è le attività di implementazione, ma quando l'esecuzione.

Tenere presente che pre-iOS 7, un'attività in esecuzione in background di stato 600 secondi per il completamento. Uno dei motivi per questo limite è che un'attività in esecuzione in background manterrebbe dispositivo attivi per tutta la durata dell'attività:

 [![](ios-backgrounding-with-tasks-images/ios6.png "Grafico dell'attività mantenendo l'app pre-iOS attivi 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

l'elaborazione in background iOS 7 è ottimizzato per la durata della batteria. In iOS 7, backgrounding diventa opportunistico: invece di mantenere attivo il dispositivo, attività rispettano a quando il dispositivo passa alla sospensione ed eseguire invece l'elaborazione in blocchi quando il dispositivo viene riattivato per gestire chiamate telefoniche, notifiche, in ingresso messaggi di posta elettronica e altro interruzioni comuni. Nel diagramma seguente fornisce informazioni approfondite come un'attività potrebbe essere interrotta backup:

 [![](ios-backgrounding-with-tasks-images/ios7.png "Grafico dell'attività verrà suddiviso in blocchi di post-iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

Poiché l'esecuzione di attività non è più continua, le attività che eseguono i trasferimenti di rete devono essere gestite in modo diverso in iOS 7. Gli sviluppatori sono invitati a utilizzare il `NSURlSession` API per gestire i trasferimenti di rete. La sezione successiva viene fornita una panoramica dei trasferimenti in background.

 <a name="background-transfers" />

## <a name="background-transfers"></a>Trasferimenti in background

La backbone dei trasferimenti in background in iOS 7 è il nuovo `NSURLSession` API. `NSURLSession` Consente di creare attività per:

1.  Trasferire il contenuto tramite le interruzioni della rete e dispositivo.
1.  Caricare e scaricare i file di grandi dimensioni ( *servizio trasferimento in Background* ).


Diamo un'occhiata il funzionamento.

### <a name="nsurlsession-api"></a>NSURLSession API

 `NSURLSession` è un'API potente per il trasferimento di contenuto sulla rete. Fornisce un set di strumenti per gestire il trasferimento di dati attraverso le interruzioni della rete e le modifiche degli Stati dell'applicazione.

Il `NSURLSession` API crea uno o più sessioni, che a sua volta generano attività per shuttle blocchi di dati correlati in rete. Attività eseguite in modo asincrono per il trasferimento di dati rapido e affidabile. Poiché `NSURLSession` è asincrono, ogni sessione richiede un blocco del gestore di completamento per consentire il sistema e l'applicazione di sapere quando il trasferimento è stato completato.

Per eseguire un trasferimento tramite rete valido in pre-iOS 7 e post-iOS 7, controllare se un `NSURLSession` è disponibile per i trasferimenti di accodamento e utilizzare un'attività in background regolari per eseguire il trasferimento in caso contrario:

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
> Evitare l'esecuzione di chiamate per aggiornare l'interfaccia utente dallo sfondo di codice conforme a 6 iOS, non supporta gli aggiornamenti dell'interfaccia utente in background, iOS 6 e terminerà l'applicazione.


Il `NSURLSession` API include un'ampia gamma di funzionalità per gestire l'autenticazione, gestire i trasferimenti non riusciti e segnalare gli errori sul lato client, ma non sul lato server -. Consente di sfruttare che le interruzioni di attività runtime introdotte in iOS 7 e fornisce inoltre supporto per il trasferimento di file di grandi dimensioni in modo rapido e affidabile. La sezione successiva illustra questa funzionalità di secondo.

### <a name="background-transfer-service"></a>Servizio di trasferimento in background

Prima di iOS 7, caricare o scaricare file in background non erano affidabili. Attività in background ottengono un periodo di tempo limitato per l'esecuzione, ma il tempo impiegato per trasferire un file varia a seconda della rete e le dimensioni del file. In iOS 7, è possibile utilizzare un `NSURLSession` per caricare e scaricare i file di grandi dimensioni. Il particolare `NSURLSession` tipo di sessione che gestisce i trasferimenti di rete del file di grandi dimensioni in background è noto come il *servizio trasferimento in Background*.

I trasferimenti tramiti il servizio di trasferimento in Background sono gestiti dal sistema operativo e forniscono le API per gestire l'autenticazione e gli errori. Perché i trasferimenti non sono associati un limite di tempo arbitrario, possono essere utilizzati per caricare o scaricare file di grandi dimensioni, l'aggiornamento automatico del contenuto in background e altro ancora. Consultare la [procedura dettagliata di trasferimento in Background](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md) per informazioni dettagliate su come implementare il servizio.

Il servizio di trasferimento in Background è spesso accompagnato da recuperare in Background o remoto notifiche per semplificare le applicazioni di aggiornare il contenuto in background. Nelle due sezioni, si introduce il concetto di registrazione delle applicazioni intere per l'esecuzione in background in iOS 6 e iOS 7.

