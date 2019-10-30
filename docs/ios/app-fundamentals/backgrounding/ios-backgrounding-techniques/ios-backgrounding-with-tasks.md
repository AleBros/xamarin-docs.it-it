---
title: Elaborazione in background in iOS con le attività
description: In questo documento viene descritto come utilizzare le attività in background per eseguire attività a esecuzione prolungata dopo che un'applicazione viene posizionata in background.
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: f39ab83e00db1abd6508d26a9280fb708e681445
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004737"
---
# <a name="ios-backgrounding-with-tasks"></a>Elaborazione in background in iOS con le attività

Il modo più semplice per eseguire informazioni in background in iOS è suddividere i requisiti in background in attività ed eseguire le attività in background. Le attività sono soggette a un limite di tempo rigoroso e in genere ottengono circa 600 secondi (10 minuti) di tempo di elaborazione dopo che un'applicazione è stata spostata in background in iOS 6 e meno di 10 minuti su iOS 7 +.

Le attività in background possono essere suddivise in tre categorie:

1. **Attività in background-Safe** : chiamate in qualsiasi punto dell'applicazione in cui è presente un'attività che non si vuole interrompere se l'applicazione entra in background.
1. **Attività DidEnterBackground** : chiamato durante il `DidEnterBackground` metodo del ciclo di vita dell'applicazione per semplificare la pulizia e il salvataggio dello stato.
1. **Trasferimenti in background (iOS 7 +)** : tipo speciale di attività in background usato per eseguire i trasferimenti di rete in iOS 7. Diversamente dalle normali attività, i trasferimenti in background non hanno un limite di tempo predeterminato.

Le attività in background e `DidEnterBackground` sono sicure da usare in iOS 6 e iOS 7, con alcune piccole differenze. Esaminiamo questi due tipi di attività in modo più dettagliato.

## <a name="creating-background-safe-tasks"></a>Creazione di attività in background-Safe

Alcune applicazioni contengono attività che non devono essere interrotte da iOS se lo stato dell'applicazione viene modificato. Un modo per proteggere queste attività dall'interruzione consiste nel registrarle con iOS come attività a esecuzione prolungata. È possibile usare questo modello in qualsiasi punto dell'applicazione in cui non si vuole che un'attività venga interrotta se l'utente inserisce l'app in background. Un candidato ideale per questo modello è costituito da attività come l'invio di informazioni di registrazione di un nuovo utente al server o la verifica delle informazioni di accesso.

Il frammento di codice seguente illustra la registrazione di un'attività da eseguire in background:

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

Il processo di registrazione associa un'attività a un identificatore univoco, `taskID`e quindi ne esegue il wrapping nelle chiamate `BeginBackgroundTask` e `EndBackgroundTask` corrispondenti. Per generare l'identificatore, effettuare una chiamata al metodo `BeginBackgroundTask` sull'oggetto `UIApplication`, quindi avviare l'attività a esecuzione prolungata, in genere in un nuovo thread. Quando l'attività viene completata, viene chiamato `EndBackgroundTask` e viene passato lo stesso identificatore. Questo è importante perché iOS terminerà l'applicazione se una chiamata `BeginBackgroundTask` non dispone di un `EndBackgroundTask`corrispondente.

> [!IMPORTANT]
> Le attività in background possono essere eseguite sul thread principale o su un thread in background, a seconda delle esigenze dell'applicazione.

## <a name="performing-tasks-during-didenterbackground"></a>Esecuzione di attività durante DidEnterBackground

Oltre a rendere un'attività a esecuzione prolungata sicura, la registrazione può essere usata per avviare le attività quando un'applicazione viene messa in background. iOS fornisce un metodo di evento nella classe *AppDelegate* denominata `DidEnterBackground` che può essere usata per salvare lo stato dell'applicazione, salvare i dati utente e crittografare il contenuto sensibile prima che un'applicazione entri in background. Un'applicazione ha circa cinque secondi per restituire da questo metodo o verrà terminata. Di conseguenza, le attività di pulizia che potrebbero richiedere più di cinque secondi per essere completate possono essere chiamate dall'interno del `DidEnterBackground` metodo. Queste attività devono essere richiamate in un thread separato.

Il processo è quasi identico a quello di registrazione di un'attività a esecuzione prolungata. Il frammento di codice seguente illustra questo comportamento:

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

Si inizia eseguendo l'override del metodo `DidEnterBackground` nel `AppDelegate`, in cui l'attività viene registrata tramite `BeginBackgroundTask` come nell'esempio precedente. Successivamente, si genera un nuovo thread e si esegue l'attività a esecuzione prolungata. Si noti che la chiamata `EndBackgroundTask` viene ora eseguita dall'interno dell'attività a esecuzione prolungata, perché il metodo di `DidEnterBackground` sarà già stato restituito.

> [!IMPORTANT]
> iOS usa un [meccanismo watchdog](https://developer.apple.com/library/ios/qa/qa1693/_index.html) per garantire che l'interfaccia utente di un'applicazione rimanga reattiva. Un'applicazione che spenda troppo tempo in `DidEnterBackground` non risponderà nell'interfaccia utente. Avviare le attività per l'esecuzione in background consente `DidEnterBackground` il ritorno tempestivo, mantenendo la risposta dell'interfaccia utente e impedendo al watchdog di terminare l'applicazione.

## <a name="handling-background-task-time-limits"></a>Gestione dei limiti temporali delle attività in background

iOS pone limiti rigidi per quanto tempo può essere eseguita un'attività in background e se la chiamata `EndBackgroundTask` non viene effettuata entro il tempo assegnato, l'applicazione verrà terminata. Tenendo traccia del tempo di background rimanente e usando i gestori di scadenza quando necessario, è possibile evitare che iOS interrompa l'applicazione.

### <a name="accessing-background-time-remaining"></a>Accesso al tempo rimanente in background

Se un'applicazione con attività registrate viene spostata in background, le attività registrate otterranno circa 600 secondi per l'esecuzione. È possibile controllare il tempo necessario per il completamento dell'attività usando la proprietà statica `BackgroundTimeRemaining` della classe `UIApplication`. Il codice seguente ci fornirà il tempo, in secondi, che l'attività in background ha lasciato:

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>Evitare la terminazione dell'app con gestori di scadenza

Oltre a fornire l'accesso alla proprietà `BackgroundTimeRemaining`, iOS fornisce un modo normale per gestire la scadenza del tempo di background tramite un **gestore di scadenza**. Si tratta di un blocco facoltativo di codice che verrà eseguito quando il tempo assegnato a un'attività sta per scadere. Il codice nel gestore di scadenza chiama `EndBackgroundTask` e passa l'ID attività, che indica che l'app funziona correttamente e impedisce a iOS di terminare l'app anche se l'attività non è più disponibile. `EndBackgroundTask` deve essere chiamato all'interno del gestore di scadenza, così come nel normale corso di esecuzione. 

Il gestore della scadenza viene espresso come funzione anonima usando un'espressione lambda, come illustrato di seguito:

```csharp
Task.Factory.StartNew( () => {

    //expirationHandler only called if background time allowed exceeded
    var taskId = UIApplication.SharedApplication.BeginBackgroundTask(() => {
        Console.WriteLine("Exhausted time");
        UIApplication.SharedApplication.EndBackgroundTask(taskId); 
    });
    while(myFlag == true)
    {
        Console.WriteLine(UIApplication.SharedApplication.BackgroundTimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

Sebbene i gestori di scadenza non siano necessari per l'esecuzione del codice, è consigliabile utilizzare sempre un gestore scadenza con un'attività in background.

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>Attività in background in iOS 7 +

La modifica più importante in iOS 7 per quanto riguarda le attività in background non è la modalità di implementazione delle attività, ma quando vengono eseguite.

Tenere presente che prima di iOS 7, un'attività in esecuzione in background aveva 600 secondi per il completamento. Uno dei motivi di questo limite è che un'attività in esecuzione in background mantiene il dispositivo sveglio per la durata dell'attività:

 [![](ios-backgrounding-with-tasks-images/ios6.png "Graph of the task keeping the app awake pre-iOS 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

l'elaborazione in background di iOS 7 è ottimizzata per la durata della batteria maggiore. In iOS 7, lo sfondo diventa opportunistico: invece di tenere il dispositivo sveglio, le attività rispettano quando il dispositivo passa alla modalità di sospensione e invece eseguono la loro elaborazione in blocchi quando il dispositivo viene riattivato per gestire chiamate telefoniche, notifiche, messaggi di posta elettronica in arrivo e altro interruzioni comuni. Il diagramma seguente illustra in che modo è possibile suddividere un'attività:

 [![](ios-backgrounding-with-tasks-images/ios7.png "Graph of the task being broken into chunks post-iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

Poiché il tempo di esecuzione dell'attività non è più continuo, le attività che eseguono trasferimenti di rete devono essere gestite in modo diverso in iOS 7. Gli sviluppatori sono invitati a usare l'API `NSURlSession` per gestire i trasferimenti di rete. La sezione successiva è una panoramica dei trasferimenti in background.

 <a name="background-transfers" />

## <a name="background-transfers"></a>Trasferimenti in background

Il backbone dei trasferimenti in background in iOS 7 è la nuova API `NSURLSession`. `NSURLSession` consente di creare attività per:

1. Trasferire il contenuto attraverso le interruzioni della rete e dei dispositivi.
1. Caricare e scaricare file di grandi dimensioni ( *servizio trasferimento in background* ).

Esaminiamo in dettaglio il funzionamento.

### <a name="nsurlsession-api"></a>API NSURLSession

 `NSURLSession` è un'API potente per il trasferimento di contenuto in rete. Fornisce un set di strumenti per gestire il trasferimento di dati tramite interruzioni di rete e modifiche negli Stati dell'applicazione.

L'API `NSURLSession` crea una o più sessioni, che a loro volta generano attività per i blocchi di dati correlati attraverso la rete. Le attività vengono eseguite in modo asincrono per trasferire i dati in modo rapido e affidabile. Poiché `NSURLSession` è asincrono, ogni sessione richiede un blocco del gestore di completamento per consentire al sistema e all'applicazione di stabilire quando un trasferimento è stato completato.

Per eseguire un trasferimento di rete valido sia in pre-iOS 7 che dopo iOS 7, controllare se è disponibile un `NSURLSession` per l'accodamento dei trasferimenti e utilizzare un'attività in background regolare per eseguire il trasferimento in caso contrario:

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
> Evitare di effettuare chiamate per aggiornare l'interfaccia utente dallo sfondo del codice conforme a iOS 6, perché iOS 6 non supporta gli aggiornamenti dell'interfaccia utente in background e interromperà l'applicazione.

L'API `NSURLSession` include un set completo di funzionalità per gestire l'autenticazione, gestire i trasferimenti non riusciti e segnalare gli errori sul lato client, ma non sul lato server. Consente di colmare le interruzioni nel tempo di esecuzione delle attività introdotte in iOS 7 e fornisce anche il supporto per trasferire file di grandi dimensioni in modo rapido e affidabile. Nella sezione successiva viene esaminata questa seconda funzionalità.

### <a name="background-transfer-service"></a>Servizio trasferimento in background

Prima di iOS 7, il caricamento o il download di file in background non era affidabile. Le attività in background ottengono un tempo di esecuzione limitato, ma il tempo necessario per il trasferimento di un file varia a seconda della rete e delle dimensioni del file. In iOS 7, è possibile usare un `NSURLSession` per caricare e scaricare correttamente file di grandi dimensioni. Il tipo di sessione `NSURLSession` specifico che gestisce i trasferimenti di rete di file di grandi dimensioni in background è noto come *servizio di trasferimento in background*.

I trasferimenti avviati tramite il servizio trasferimento in background vengono gestiti dal sistema operativo e forniscono API per gestire l'autenticazione e gli errori. Poiché i trasferimenti non sono associati da un limite di tempo arbitrario, possono essere usati per caricare o scaricare file di grandi dimensioni, aggiornare automaticamente il contenuto in background e altro ancora. Per informazioni dettagliate su come implementare il servizio, vedere la [procedura dettagliata](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md) relativa al trasferimento in background.

Il servizio trasferimento in background è spesso associato a recupero in background o a notifiche remote per consentire alle applicazioni di aggiornare il contenuto in background. Nelle due sezioni successive viene introdotto il concetto di registrazione di intere applicazioni per l'esecuzione in background in iOS 6 e iOS 7.
