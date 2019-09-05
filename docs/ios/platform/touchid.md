---
title: Touch ID in Novell. iOS
description: Questo documento descrive come usare Touch ID, la tecnologia di autenticazione con impronta digitale biometrica di Apple, nelle app Novell. iOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: bc797d250b4b66ebfd06bad76c3f43759a65e7c3
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292509"
---
# <a name="touch-id-in-xamarinios"></a>Touch ID in Novell. iOS

Touch ID è stato introdotto in iOS 7 come mezzo per l'autenticazione dell'utente, simile a un codice di accesso. Tuttavia, era limitato allo sblocco del dispositivo, usando l'App Store, usando iTunes e autenticando solo il keychain di iCloud.

Sono ora disponibili due modi per usare Touch ID come meccanismo di autenticazione in un'applicazione iOS 8 con l'API di autenticazione locale. Attualmente non è possibile usare l'autenticazione locale per l'autenticazione in modalità remota.

Per comprendere appieno l'ID tocco e il suo valore, è consigliabile esaminare i servizi keychain e le nuove modifiche significative per i dati dell'utente. L'accesso keychain è stato inoltre ampliato in iOS 8 tramite l'utilizzo della nuova funzionalità elenchi di controllo di accesso (ACL).

## <a name="keychain--secure-enclave"></a>Keychain & Secure Enclave

Keychain è un database di grandi dimensioni che fornisce archiviazione sicura per password, chiavi, certificati e note per un singolo ID Apple. In iOS 8 un'applicazione ha sempre accesso ai propri elementi Keychain univoci e non può accedere ad alcun elemento keychain di altre applicazioni. Questo comportamento è diverso da OS X, in cui il keychain viene sbloccato con una singola password, consentendo a qualsiasi applicazione in grado di riconoscere servizi keychain di usare il keychain. In questo articolo verrà illustrato il funzionamento del keychain in iOS 8.

Keychain è un database specializzato, in cui ogni riga è nota come _elemento Keychain_. Ogni elemento è descritto dagli attributi Keychain ed è costituito da valori crittografati. Per consentire un uso efficiente del keychain, è ottimizzato per piccoli elementi o _segreti_.
Ogni elemento keychain è protetto dal codice utente e da un segreto del dispositivo univoco. Gli elementi Keychain devono essere protetti anche quando gli utenti non usano i dispositivi. Questa operazione è implementata in iOS consentendo solo agli elementi di diventare disponibili quando il dispositivo viene sbloccato, quando il dispositivo è bloccato, non è più disponibile. Possono anche essere archiviati in un backup crittografato. Una delle funzionalità principali di keychain è l'applicazione del controllo di accesso. un'applicazione può accedere alla relativa parte del keychain e tutte le altre applicazioni verranno impedite. Il diagramma seguente illustra il modo in cui un'applicazione interagisce con il keychain:

[![](touchid-images/image1.png "Questo diagramma illustra il modo in cui un'applicazione interagisce con il keychain")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>Enclave sicura

Il keychain non può decrittografare l'elemento Keychain autonomamente; viene invece eseguita nell' *enclave protetta*. L'enclave protetta è un coprocessore all'interno del chip A7 responsabile della determinazione di una corrispondenza corretta dai dati delle impronte digitali dal sensore Touch ID rispetto a una stampa registrata. Viene quindi decrittografato l'elemento keychain e viene restituito il segreto decrittografato nel keychain.

### <a name="working-with-keychain"></a>Uso del keychain

Prima l'applicazione deve eseguire una query nel Keychain per verificare se esiste una password. Se non esiste, potrebbe essere necessario richiedere una password in modo che l'utente non venga continuamente richiesto. Se è necessario aggiornare la password, richiedere una nuova password all'utente e passare il valore aggiornato al keychain.

> [!NOTE]
> Dopo aver usato un segreto recuperato dal keychain, tutti i riferimenti ai dati devono essere eliminati dalla memoria. Non assegnarlo mai a una variabile globale.

## <a name="keychain-acl-and-touch-id"></a>ACL del keychain e ID tocco

L'elenco di controllo di accesso è un nuovo attributo dell'elemento keychain in iOS 8 che descrive le informazioni relative a ciò che deve verificarsi per consentire l'esecuzione di un'operazione specifica. Questo può essere nel formato di visualizzazione di una finestra di dialogo di avviso o di richiesta di un codice di accesso. ACL consente di impostare l'accessibilità e l'autenticazione per un elemento keychain. Il diagramma seguente illustra il modo in cui questo nuovo attributo si collega con il resto dell'elemento Keychain:

[![](touchid-images/image2.png "Questo diagramma mostra il modo in cui questo nuovo attributo si collega con il resto dell'elemento Keychain")](touchid-images/image2.png#lightbox)

A partire da iOS 8, è ora disponibile un nuovo criterio di presenza `SecAccessControl`utente,, che viene applicato dall'enclave protetta in un iPhone 5S e versioni successive. Nella tabella seguente è possibile vedere come la configurazione del dispositivo può influenzare la valutazione dei criteri:

|Configurazione del dispositivo|Valutazione dei criteri|Meccanismo di backup|
|--- |--- |--- |
|Dispositivo senza codice di accesso|Nessun accesso|Nessuna|
|Dispositivo con codice di accesso|Richiede il codice di accesso|Nessuna|
|Dispositivo con ID tocco|Preferisce l'ID tocco|Consente il codice|

Tutte le operazioni all'interno dell'enclave protetta possono considerarsi attendibili. Ciò significa che è possibile usare il risultato dell'autenticazione di Touch ID per autorizzare la decrittografia dell'elemento keychain. L'enclave protetta mantiene anche un contatore di corrispondenze di ID tocco non riuscite, nel qual caso un utente dovrà ripristinare l'utilizzo del codice.
Un nuovo Framework in iOS 8, denominato _autenticazione locale_, supporta questo processo di autenticazione all'interno del dispositivo. Questo passaggio verrà esaminato nella sezione successiva.

## <a name="local-authentication"></a>Autenticazione locale

Come stabilito nella sezione precedente, le applicazioni possono usare l'autenticazione locale per autenticare l'utente in conformità con i criteri di sicurezza configurati nel dispositivo.

Attualmente, l'API offre solo due funzionalità: In primo luogo, consente ai servizi Keychain esistenti di usare nuovi elenchi di controllo di accesso (ACL) keychain. I dati Keychain possono essere sbloccati con l'autenticazione corretta di un'impronta digitale degli utenti.

In secondo luogo, LocalAuthentication offre due metodi per autenticare l'applicazione in locale. Gli sviluppatori devono `CanEvaluatePolicy` usare per determinare se il dispositivo è in grado di accettare l'ID tocco `EvaluatePolicy` e quindi avviare l'operazione di autenticazione.

Sebbene entrambe le funzionalità offrano l'autenticazione locale, non forniscono un meccanismo che consente all'applicazione o all'utente di eseguire l'autenticazione a un server remoto.
L'autenticazione locale fornisce una nuova interfaccia utente standard per l'autenticazione. Nel caso di Touch ID, si tratta di una vista avvisi con due pulsanti, come illustrato di seguito. Un pulsante da annullare e uno per usare la modalità di fallback per l'autenticazione, ovvero il codice di accesso. È inoltre necessario impostare un messaggio personalizzato. È consigliabile usare questa procedura per spiegare all'utente perché è necessaria l'autenticazione con ID tocco.

[![](touchid-images/image12.png "Avviso di autenticazione Touch ID")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>Con i servizi Keychain

Si è appreso come un elemento Keychain decrittografato, usando l'enclave sicura per verificare il codice di accesso. In iOS 8, è possibile usare l'autenticazione locale per richiedere la verifica dell'ID tocco insieme alla funzionalità degli elenchi di controllo di accesso, che fornisce l'implementazione del meccanismo di fallback o la password.
Per usare ACL è necessario usare il `SecAccessControl` criterio e quindi controllare lo stato del dispositivo usando `SecAccessible.WhenPasscodeSetThisDeviceOnly` o `SecAccessible.WhenUnlocked`.

#### <a name="considerations-with-acl"></a>Considerazioni con ACL

Quando si usa l'ACL con il keychain, è necessario tenere presente molti aspetti, che sono elencati di seguito:

- Usare solo con l'applicazione in primo piano: se si chiama un'operazione di Keychain su un thread in background, la chiamata avrà esito negativo.
- L'aggiunta e l'aggiornamento degli elementi Keychain possono richiedere l'autenticazione.
- Se una richiesta restituisce più elementi corrispondenti nel keychain, potrebbe essere necessaria l'autenticazione.
- Gli elementi protetti da ACL sono di solo dispositivo e pertanto non sono sincronizzati o sottoposti a backup.

### <a name="using-local-authentication-without-keychain-services"></a>Uso dell'autenticazione locale senza i servizi Keychain

L'autenticazione locale è stata creata come metodo per raccogliere le credenziali, ad esempio il codice di accesso o l'ID tocco, e per lavorare con l'enclave protetta per completare l'autenticazione dell'utente. Considerarlo come un ponte tra l'applicazione e l'enclave protetta, che non può mai comunicare direttamente tra loro. Può essere usato anche per la valutazione dei criteri per l'applicazione.

A tale scopo, un'applicazione chiama la valutazione dei criteri all'interno dell'autenticazione locale, che avvia l'operazione all'interno dell'enclave protetta. Questa operazione può essere usata per fornire l'autenticazione all'app, senza eseguire direttamente query o accedere all'enclave protetta.

[![](touchid-images/image13a.png "Uso dell'autenticazione locale senza i servizi Keychain")](touchid-images/image13a.png#lightbox)

L'uso dell'autenticazione locale nell'applicazione fornisce un modo semplice per implementare la verifica degli utenti, ad esempio per sbloccare una funzionalità esclusivamente per gli occhi del proprietario del dispositivo, ad esempio per le applicazioni bancarie, o per facilitare i controlli parentali per i singoli applicazione. È anche possibile usarlo come metodo per estendere l'autenticazione già esistente. gli utenti come le loro informazioni sono protetti, ma hanno anche le opzioni desiderate.

La sicurezza dell'autenticazione locale è diversa da quella del keychain. Ad esempio, quando si usa il keychain, il trust è tra il sistema operativo e l'enclave protetta. Con l'autenticazione locale, si trova tra l'applicazione e il sistema operativo, il che significa che è possibile accedere solo ai risultati dell'enclave protetta, non all'enclave protetta stessa.

Per quanto riguarda la sicurezza, è inoltre estremamente importante tenere presente che non è possibile **accedere** a dita registrate o immagini di impronta digitale. L'enclave protetta è il proprietario di queste informazioni, pertanto nessun altro componente di sistema può accedervi.

Per usare Touch ID senza keychain sfruttando l'API di autenticazione locale, è possibile usare alcune funzioni. Queste informazioni sono descritte di seguito:

- `CanEvaluatePolicy`: Verrà semplicemente verificato se il dispositivo è in grado di accettare l'ID tocco.
- `EvaluatePolicy`: Viene avviata l'operazione di autenticazione e viene visualizzata l'interfaccia utente `true` e `false` viene restituita una risposta o.
- `DeviceOwnerAuthenticationWithBiometrics`: Criteri che possono essere usati per visualizzare la schermata Touch ID. Vale la pena notare che in questo caso non è presente alcun meccanismo di fallback del codice, ma è necessario implementare questo fallback nell'applicazione per consentire agli utenti di ignorare l'autenticazione con ID tocco.

Esistono alcune avvertenze sull'uso dell'autenticazione locale, elencate di seguito:

- Come con keychain, può essere eseguito solo in primo piano. La chiamata in un thread in background ne causerà l'esito negativo.
- Tenere presente che la valutazione del criterio potrebbe non riuscire. Un pulsante di accesso di codice deve essere implementato come un fallback.
- È necessario fornire un `localizedReason` oggetto per spiegare il motivo per cui è necessaria l'autenticazione. Questo consente di creare una relazione di trust con l'utente.

Successivamente, nella sezione seguente verrà illustrato come implementare l'API tenendo in considerazione queste considerazioni.

## <a name="adding-touch-id-to-your-application"></a>Aggiunta di Touch ID all'applicazione

Nelle sezioni precedenti è stata esaminata la teoria sulla base dell'accesso e dell'autenticazione tramite keychain e l'autenticazione locale. Si esaminerà ora come è possibile integrare Touch ID nell'applicazione.

### <a name="walkthrough"></a>Procedura dettagliata

Si esaminerà quindi l'aggiunta di un'autenticazione Touch ID all'applicazione. In questa procedura dettagliata verrà aggiornato l'esempio di [tabella storyboard](https://docs.microsoft.com/samples/xamarin/ios-samples/data/storyboardtable/) , aggiungendo l'autenticazione locale in modo che funzioni come l'esempio di [autenticazione locale della tabella storyboard](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication) , che consente solo agli utenti autenticati di aggiungere faccende all'elenco.

1. Scaricare l'esempio ed eseguirlo in Visual Studio per Mac.
2. Fare doppio clic `MainStoryboard.Storyboard` su per aprire l'esempio in iOS designer. Con questo esempio, si vuole aggiungere una nuova schermata all'applicazione, che controllerà l'autenticazione. Questo passerà prima dell'oggetto `MasterViewController`corrente.
3. Trascinare un nuovo **controller di visualizzazione** dalla **casella degli strumenti** al **area di progettazione**. Impostare questa impostazione come **controller visualizzazione radice** premendo **Ctrl + trascina** dal **controller di spostamento**:

    [![](touchid-images/image4.png "Imposta il controller visualizzazione radice")](touchid-images/image4.png#lightbox)
4. Assegnare un nome al nuovo `AuthenticationViewController`controller di visualizzazione.
5. Trascinare quindi un pulsante e posizionarlo in `AuthenticationViewController`. Chiamare questo `AuthenticateButton`oggetto e assegnargli il testo `Add a Chore`.
6. Creare un evento nell'oggetto `AuthenticateButton` chiamato `AuthenticateMe`.
7. Creare un segue manuale da `AuthenticationViewController` facendo clic sulla barra nera nella parte inferiore e **Ctrl + trascina** dalla barra fino `MasterViewController` al e scegliendo **push** (o **Mostra** se si usano le classi di dimensioni):

    [![](touchid-images/image5.png "Trascinare dalla barra fino al MasterViewController e scegliendo push o show")](touchid-images/image6.png#lightbox)
8. Fare clic sul segue appena creato e assegnargli l'identificatore `AuthenticationSegue`, come illustrato di seguito:

    [![](touchid-images/image7.png "Impostare l'identificatore segue su AuthenticationSegue")](touchid-images/image7.png#lightbox)
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

Questo è tutto il codice necessario per implementare l'autenticazione con ID tocco usando l'autenticazione locale. Le righe evidenziate nell'immagine seguente illustrano l'uso dell'autenticazione locale:

[![](touchid-images/image8.png "Le righe evidenziate mostrano l'uso dell'autenticazione locale")](touchid-images/image8.png#lightbox)

Prima di tutto, è necessario stabilire se il dispositivo è in grado di accettare l'input Touch ID, `CanEvaluatePolicy` usando il e passando i `DeviceOwnerAuthenticationWithBiometrics`criteri. Se il valore è true, è possibile visualizzare l'interfaccia utente di touch `EvaluatePolicy`ID usando. Ci sono tre tipi di informazioni che è necessario passare `EvaluatePolicy` , ovvero i criteri stessi, una stringa che spiega perché è necessaria l'autenticazione e un gestore di risposta. Il gestore di risposta indica all'applicazione l'operazione da eseguire in caso di autenticazione riuscita o non riuscita. Si osservi più vicino al gestore di risposta:

[![](touchid-images/image9.png "Gestore di risposta")](touchid-images/image9.png#lightbox)

Il gestore di risposta è specificato di `LAContextReplyHandler`tipo, che accetta i parametri riusciti, `bool` ovvero un valore e `NSError` un `error`oggetto chiamato. Se l'operazione ha esito positivo, in questo caso verrà effettivamente eseguito qualsiasi tipo di autenticazione, in questo caso visualizzando la schermata che consente di aggiungere un nuovo lavoro. Tenere presente che una delle avvertenze dell'autenticazione locale è che deve essere eseguita in primo piano, quindi assicurarsi di usare `InvokeOnMainThread`:

[![](touchid-images/image10.png "Usare InvokeOnMainThread per l'autenticazione locale")](touchid-images/image10.png#lightbox)

Infine, quando l'autenticazione ha avuto esito positivo, si desidera eseguire la `MasterViewController`transizione a. Per `PerformSegue` eseguire questa operazione, è possibile usare il metodo:

[![](touchid-images/image11.png "Chiamare il metodo PerformSegue per passare a MasterViewController")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>Riepilogo

In questa guida è stato esaminato keychain e come funziona in iOS. È stato anche esplorato l'ACL keychain e le modifiche apportate a questo in iOS. A questo punto, è stato esaminato il Framework di autenticazione locale, che è una novità di iOS 8 e quindi si è appreso come implementare l'autenticazione di Touch ID nell'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Tabella storyboard-autenticazione locale](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication)
- [Esempio WWDC Keychain](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-keychaintouchid/)
- [Keychain (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/keychain/)
