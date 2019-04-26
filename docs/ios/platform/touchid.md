---
title: Touch ID in xamarin. IOS
description: Questo documento descrive come usare Touch ID, tecnologia di autenticazione biometrico impronta digitale di Apple, nelle App xamarin. IOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 2d67bc71361e335515cfba8b5a20e157ed6b6b05
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087859"
---
# <a name="touch-id-in-xamarinios"></a>Touch ID in xamarin. IOS

Touch ID è stato introdotto in iOS 7 come mezzo per autenticare l'utente, simile a un passcode. Tuttavia, era limitato a sbloccare il dispositivo, tramite l'App Store, usando iTunes e l'autenticazione solo il keychain iCloud.

Esistono ora due modi per usare Touch ID come meccanismo di autenticazione in un'applicazione iOS 8 usando l'API di autenticazione locale. Attualmente non è possibile usare l'autenticazione locale per eseguire l'autenticazione in modalità remota.

Per una migliore comprensione di Touch ID e vale, dovremmo esamineremo Keychain servizi e il significato di queste nuove modifiche per i dati dell'utente. L'accesso Keychain è stata estesa anche al momento in iOS 8 tramite l'uso la nuova funzionalità di elenchi di controllo di accesso (ACL).

## <a name="keychain--secure-enclave"></a>Keychain & Enclave protetta

Keychain è un database di grandi dimensioni che fornisce archiviazione protetta per le password, chiavi, i certificati e le note per un singolo ID di Apple. In iOS 8 un'applicazione sempre dispone dell'accesso per i propri elementi keychain univoco e non può accedere a tutti gli elementi keychain di altre applicazioni. Questo comportamento è diverso da OS X in cui viene sbloccato con un'unica password valida, il keychain consente tutte le applicazioni con riconoscimento Keychain Services di utilizzare il keychain. In questo articolo ci concentreremo sul funzionamento di keychain in iOS 8.

Keychain è un database specializzato, in cui ogni riga è nota come un _Keychain elemento_. Ogni elemento è descritta da attributi di keychain ed è costituito da valori crittografati. Per consentire un uso efficiente di portachiavi, è ottimizzato per gli elementi di piccole dimensioni, o _segreti_.
Ogni elemento di keychain è protetta da un segreto univoco del dispositivo e il passcode degli utenti. Gli elementi di Keychain devono essere protetti anche quando gli utenti non usano i propri dispositivi. Ciò viene implementato in iOS consentendo solo gli elementi diventano disponibili quando il dispositivo sia sbloccato, ovvero quando il dispositivo è bloccato non sono più disponibili. Possono anche essere archiviati in un backup crittografato. Una delle funzionalità chiave di keychain consiste nell'imporre il controllo di accesso; un'applicazione può accedere a relativa parte di keychain e tutte le altre applicazioni non saranno possibile. Il diagramma seguente illustra come un'applicazione interagisce con il keychain:

[![](touchid-images/image1.png "Questo diagramma illustra come un'applicazione interagisce con il keychain")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>Enclave protetta

Il Keychain non può decrittografare l'elemento keychain dalla stessa. ma questo avviene nella *Enclave protetta*. L'enclave protetta è un CO-processore all'interno del chip A7 che è responsabile di determinare una corrispondenza corretta dai dati di impronta digitale del sensore di Touch ID per una stampa registrata. Verrà quindi decrittografare l'elemento portachiavi e restituire il segreto decrittografato al Keychain.

### <a name="working-with-keychain"></a>Utilizzo di Keychain

Prima di tutto l'applicazione deve eseguire una query nel Keychain per verificare la presenza di una password. Se non esiste, si potrebbe essere necessario richiedere una password in modo costante non viene chiesto all'utente. Se la password deve essere aggiornato, richiedere all'utente di una nuova password e passare il valore aggiornato nella Keychain.

> [!NOTE]
> Dopo che tramite un segreto recuperato dal Keychain, tutti i riferimenti ai dati devono essere eliminati dalla memoria. Mai assegnarlo a una variabile globale.

## <a name="keychain-acl-and-touch-id"></a>ACL di Keychain e Touch ID

Elenco di controllo di accesso è un nuovo attributo elemento portachiavi iOS 8 che descrive le informazioni relative a ciò che deve essere eseguita per consentire una particolare operazione si verifichi. Ciò potrebbe essere sotto forma di visualizzazione di una finestra di dialogo di avviso o a richiedere un passcode. ACL consente di impostare l'accessibilità e l'autenticazione per un elemento di keychain. Il diagramma seguente mostra come consente di associare il nuovo attributo con il resto dell'articolo keychain:

[![](touchid-images/image2.png "Il diagramma seguente illustra come consente di associare il nuovo attributo con il resto dell'articolo keychain")](touchid-images/image2.png#lightbox)

A partire da iOS 8, è ora disponibile un nuovo criterio di presenza utente, `SecAccessControl`, che viene applicato per l'enclave protetta in un iPhone 5s e versioni successive. È possibile osservare nella tabella seguente, proprio come la configurazione del dispositivo può influenzare la valutazione dei criteri:

|Configurazione del dispositivo|Valutazione dei criteri|Meccanismo di backup|
|--- |--- |--- |
|Dispositivo senza Passcode|Nessun accesso|nessuno|
|Dispositivo con Passcode|Richiede Passcode|nessuno|
|Dispositivo con Touch ID|Preferisce il Touch ID|Consente di Passcode|

Tutte le operazioni all'interno dell'Enclave protetta possono considerino reciprocamente attendibili. Ciò significa che è possibile usare il risultato dell'autenticazione Touch ID per autorizzare la decrittografia di elemento di Keychain. L'Enclave protetta mantiene anche un contatore di corrispondenze non riuscite di Touch ID, in cui sarà necessario ripristinare l'uso del passcode caso un utente.
Un nuovo framework iOS 8, chiamato _l'autenticazione locale_, supporta questo processo di autenticazione all'interno del dispositivo. Ciò è verranno illustrate nella sezione successiva.

## <a name="local-authentication"></a>Autenticazione locale

Abbiamo definito nella sezione precedente, le applicazioni possono utilizzare l'autenticazione locale per autenticare l'utente in conformità con i criteri di sicurezza che sono stato configurato nel dispositivo.

Attualmente, l'API fornisce solo due funzionalità: In primo luogo, assiste i servizi di Keychain esistenti tramite l'uso di nuovi Keychain Access Control Lists (ACL). È possibile sbloccarle con l'autenticazione di utenti impronta digitale Keychain dati.

In secondo luogo, LocalAuthentication fornisce due metodi per eseguire l'autenticazione dell'applicazione in locale. Gli sviluppatori devono usare `CanEvaluatePolicy` per determinare se il dispositivo è in grado di accettare Touch ID e quindi `EvaluatePolicy` per avviare l'operazione di autenticazione.

Entrambe le funzionalità offrono l'autenticazione locale, ma non offrono un meccanismo per l'applicazione o dall'utente per l'autenticazione a un server remoto.
L'autenticazione locale fornisce una nuova interfaccia utente standard per l'autenticazione. Nel caso di Touch ID, si tratta di una visualizzazione avvisi con due pulsanti, come illustrato di seguito. Un pulsante Annulla e quello da utilizzare per il fallback mezzo di autenticazione: il passcode. È inoltre disponibile un messaggio personalizzato che deve essere impostato. È consigliabile utilizzarla per spiegare all'utente perché è necessaria l'autenticazione di Touch ID.

[![](touchid-images/image12.png "L'avviso di autenticazione di Touch ID")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>Con i servizi di Keychain

È stato esaminato un po' prima come un elemento di keychain viene decrittografato, con l'enclave protetta per verificare il passcode. In iOS 8, è possibile usare l'autenticazione locale per richiedere la verifica di Touch ID in combinazione con la funzionalità di Access Control List, che fornisce l'implementazione del meccanismo di fallback o la password.
Per usare ACL sarebbe necessario usare il `SecAccessControl` dei criteri e quindi controllando lo stato del dispositivo usando `SecAccessible.WhenPasscodeSetThisDeviceOnly` o `SecAccessible.WhenUnlocked`.

#### <a name="considerations-with-acl"></a>Considerazioni su con ACL

Esistono molti aspetti che si dovrebbe tenere a mente quando si usa ACL con il portachiavi e alcune di queste sono elencati di seguito:

-   Usare solo con l'applicazione in primo piano: se si chiama qualsiasi operazione keychain su un thread in background che avrà esito negativo della chiamata.
-   Aggiunta e aggiornamento degli elementi di keychain potrebbero richiedere l'autenticazione.
-   Se una richiesta restituisce più elementi corrispondenti nel portachiavi, l'autenticazione potrebbe essere necessaria.
-   Elementi protetti ACL sono dispositivi e pertanto non sincronizzato o solo sottoposto a backup.

### <a name="using-local-authentication-without-keychain-services"></a>Utilizzo dell'autenticazione locale senza servizi Keychain

L'autenticazione locale è stato creato come un modo per raccogliere le credenziali, ad esempio passcode o Touch ID e per lavorare con l'Enclave protetta per completare l'autenticazione dell'utente. Essere considerato un bridge tra l'applicazione e l'Enclave protetta, che possono comunicare mai direttamente tra loro. Può essere utilizzato anche per la valutazione dei criteri per l'applicazione.

A tale scopo un'applicazione chiama la valutazione dei criteri all'interno di autenticazione locale, che avvia l'operazione all'interno dell'Enclave protetta. È possibile usare questo metodo per fornire l'autenticazione all'app, senza l'esecuzione di query/accedere direttamente l'Enclave protetta.

[![](touchid-images/image13a.png "Utilizzo dell'autenticazione locale senza servizi Keychain")](touchid-images/image13a.png#lightbox)

Usando l'autenticazione locale nell'applicazione fornisce un modo semplice dell'implementazione di verifica dell'utente, ad esempio rendere disponibile una funzionalità esclusivamente per gli occhi del proprietario del dispositivo, ad esempio applicazioni per operazioni bancarie, o al controllo genitori contribuire per i singoli applicazione. È inoltre possibile utilizzarlo come un modo per estendere l'autenticazione che esiste già: gli utenti, ad esempio le informazioni al sicuro, ma è anche probabile che si hanno le opzioni.

La sicurezza dell'autenticazione locale è diverso da quello del keychain. Ad esempio, quando si usa il keychain, la relazione di trust è tra il sistema operativo e l'Enclave protetta. Con l'autenticazione locale, è compreso tra l'applicazione e il sistema operativo, il che significa che si ha accesso solo ai risultati dell'Enclave protetta, non il Secure Enclave stesso.

In materia di sicurezza, è inoltre estremamente importante sapere che non esiste **alcun accesso** dita registrate o immagini di impronta digitale. L'Enclave protetta è il proprietario di tali informazioni e quindi, nessun altro componente di sistema è consentito l'accesso.

Per usare Touch ID senza keychain, sfruttando l'API di autenticazione locale, esistono alcune funzioni che è possibile usare. Questi sono descritti di seguito:

*   `CanEvaluatePolicy` – Questo semplicemente controllerà se il dispositivo è in grado di accettare ID tocco.
*   `EvaluatePolicy` – Questo è possibile avviare l'operazione di autenticazione e visualizza l'interfaccia utente e restituisce un `true` o `false` risposte.
*   `DeviceOwnerAuthenticationWithBiometrics` – Questo è il criterio che può essere utilizzato per visualizzare la schermata di Touch ID. Vale la pena notare che non è disponibile alcun meccanismo di fallback passcode qui, è invece necessario implementare questo fallback nell'applicazione per consentire agli utenti di ignorare l'autenticazione di Touch ID.

Esistono alcune limitazioni all'utilizzo di autenticazione locale, che sono elencati di seguito:

*   Analogamente a Keychain, si può essere eseguito solo in primo piano. Viene chiamata su un thread in background causerà l'esito negativo.
*   Tenere presente che la valutazione dei criteri potrebbe non riuscire. Un pulsante di passcode dovranno essere implementate come un fallback.
*   È necessario fornire un `localizedReason` per spiegare il motivo per cui è necessaria l'autenticazione. Ciò consente una fiducia va costruita con l'utente.

Successivamente, nella sezione seguente, si esaminerà come implementare l'API tenendo conto di queste avvertenze.

## <a name="adding-touch-id-to-your-application"></a>Aggiunta di Touch ID per l'applicazione

Nelle sezioni precedenti, abbiamo esaminato la teoria dietro l'accesso e l'autenticazione tramite Keychain e l'autenticazione locale. Prendiamo ora un'occhiata a come è possibile integrare Touch ID all'applicazione.

### <a name="walkthrough"></a>Procedura dettagliata

Quindi, esaminiamo l'aggiunta di alcune Touch ID impostazioni di autenticazione all'applicazione. In questa procedura dettagliata si intende aggiornare il [tabella Storyboard](https://developer.xamarin.com/samples/StoryboardTable/) esempio, aggiungendo l'autenticazione locale, in modo che funziona come il [Storyboard tabella: l'autenticazione locale](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/) campione, che consente solo utenti autenticati per aggiungere le attività all'elenco.

1. Scaricare l'esempio ed eseguirlo in Visual Studio per Mac.
2. Fare doppio clic su `MainStoryboard.Storyboard` per aprire l'esempio in iOS Designer. Con questo esempio, si vuole aggiungere una nuova schermata all'applicazione, che consentono di controllare l'autenticazione. Dovrà essere inserito prima di quella corrente `MasterViewController`.
3. Trascinare una nuova **Controller di visualizzazione** dalle **della casella degli strumenti** per il **nell'area di progettazione**. Impostare questo elemento come il **Controller visualizzazione radice** dal **premere Ctrl e trascinare** dal **Controller di spostamento**:

    [![](touchid-images/image4.png "Impostare il Controller visualizzazione radice")](touchid-images/image4.png#lightbox)
4.  Denominare il nuovo Controller di visualizzazione `AuthenticationViewController`.
5. Successivamente, trascinare un pulsante e posizionalo nel `AuthenticationViewController`. Chiameremo `AuthenticateButton`e assegnarle il testo `Add a Chore`.
6. Creare un evento nel `AuthenticateButton` chiamato `AuthenticateMe`.
7. Creazione manuale di un elemento segue da `AuthenticationViewController` facendo clic sulla barra nera nella parte inferiore e **premere Ctrl e trascinare** dalla barra per il `MasterViewController` e scegliendo **push** (o **Mostra** Se si usa le classi di dimensioni):

    [![](touchid-images/image5.png "Trascinare dalla barra dei MasterViewController e scegliendo push oppure visualizzare")](touchid-images/image6.png#lightbox)
8. Fare clic sull'oggetto appena creato elemento segue e assegnargli l'identificatore `AuthenticationSegue`, come illustrato di seguito:

    [![](touchid-images/image7.png "Impostare l'identificatore di elemento segue su AuthenticationSegue")](touchid-images/image7.png#lightbox)
9. Aggiungi il seguente codice a `AuthenticationViewController`.

    ```csharp
    partial void AuthenticateMe (UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var myReason = new NSString("To add a new chore");

        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError)){
            var replyHandler = new LAContextReplyHandler((success, error) => {
                this.InvokeOnMainThread(()=> {
                    if(success)
                    {
                        Console.WriteLine("You logged in!");
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        // Show fallback mechanism here
                    }
                });
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, myReason, replyHandler);
        };
    }
    ```

Questo è tutto il codice che necessario per implementare l'autenticazione di Touch ID con l'autenticazione locale. Le righe evidenziate nell'immagine seguente mostrano l'uso dell'autenticazione locale:

[![](touchid-images/image8.png "Le righe evidenziate illustrano l'uso dell'autenticazione locale")](touchid-images/image8.png#lightbox)

In primo luogo, è necessario stabilire se il dispositivo è in grado di accettare Touch ID di input, tramite il `CanEvaluatePolicy` e passando il criterio `DeviceOwnerAuthenticationWithBiometrics`. Se ciò è vero, è possibile visualizzare l'interfaccia utente di Touch ID usando `EvaluatePolicy`. Esistono tre tipi di informazioni è necessario passare in `EvaluatePolicy` – i criteri stessi, una stringa che spiega il motivo per cui è necessaria l'autenticazione e un gestore della risposta. Il gestore di risposta indica all'applicazione come comportarsi in caso di un esito positivo o negativo, l'autenticazione. Esaminiamo più da vicino il gestore di risposta:

[![](touchid-images/image9.png "Il gestore di risposta")](touchid-images/image9.png#lightbox)

Viene specificato il gestore di risposta di tipo `LAContextReplyHandler`, che accetta l'esito positivo di parametri: una `bool` valore e un `NSError` chiamato `error`. Se ha esito positivo, si tratta in cui verrà effettivamente eseguita qualsiasi è, vogliamo autenticare – in questo caso Visualizza la schermata che invia i tuoi commenti aggiungerà nuove impegnativa. Tenere presente che uno degli aspetti di autenticazione locale è che deve essere eseguito in primo piano, pertanto assicurarsi di usare `InvokeOnMainThread`:

[![](touchid-images/image10.png "Usare InvokeOnMainThread per l'autenticazione locale")](touchid-images/image10.png#lightbox)

Infine, quando l'autenticazione ha esito positivo, si decide di transizione per il `MasterViewController`. Il `PerformSegue` metodo può essere utilizzato per eseguire questa operazione:

[![](touchid-images/image11.png "Chiamare il metodo PerformSegue per eseguire la transizione a MasterViewController")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>Riepilogo
In questa guida abbiamo esaminato Keychain e il funzionamento in iOS. Abbiamo anche analizzato il keychain ACL e le modifiche apportate a questo in iOS. Successivamente, Daremo un'occhiata al framework di autenticazione locale, che è stato introdotto in iOS 8 e quindi esaminato l'implementazione dell'autenticazione di Touch ID nella nostra applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Storyboard di tabella: l'autenticazione locale](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/) 
- [Esempio di Keychain WWDC](https://developer.xamarin.com/samples/KeychainTouchID/)
- [Keychain (esempio)](https://developer.xamarin.com/samples/Keychain/)
