---
title: ID tocco
description: Touch ID è una tecnologia di autenticazione impronte di Apple.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: d9d70c37de5cb91c4cd1fdc77e27942d851c346b
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="touch-id"></a>ID tocco

Touch ID è stato introdotto in iOS 7 come mezzo per autenticare gli utenti - simili a un passcode. Tuttavia, è limitato a sbloccare il dispositivo, utilizzando l'App Store, iTunes e iCloud keychain solo autenticazione.

Esistono due modi per usare l'ID tocco come meccanismo di autenticazione in un'applicazione iOS 8 tramite l'API di autenticazione locale. Non è attualmente possibile utilizzare l'autenticazione locale per l'autenticazione in modalità remota.

Per una migliore comprensione Touch ID e il relativo valore, è necessario esplorare servizi di portachiavi e il significato di queste nuove modifiche per i dati dell'utente. Accesso portachiavi è stata estesa anche su iOS 8 tramite l'utilizzo della nuova funzionalità di elenchi di controllo di accesso (ACL).

## <a name="keychain--secure-enclave"></a>Keychain & Enclave sicura

Keychain è un database di grandi dimensioni di archiviazione sicura per le password, chiavi, certificati e le note per un singolo ID di Apple. In iOS 8 un'applicazione sempre dispone dell'accesso per i propri elementi portachiavi univoco e non può accedere a tutti gli elementi portachiavi di altre applicazioni. Questo comportamento è diverso da OS X in cui il portachiavi viene sbloccato con una sola password, consentire a qualsiasi applicazione compatibile con Keychain Services di utilizzare il portachiavi. In questo articolo esamineremo il funzionamento di keychain in iOS 8.

Keychain è un database specifico, in cui ogni riga è noto come un _elemento Keychain_. Ogni elemento descritto da attributi portachiavi ed è composto di valori crittografati. Per consentire un utilizzo efficiente di portachiavi, è ottimizzato per elementi di piccole dimensioni, o _segreti_.
Ogni elemento keychain è protetto dal passcode di utenti e un segreto univoco del dispositivo. Elementi Keychain devono essere protetti anche quando gli utenti non usano i propri dispositivi. Ciò viene implementato in iOS, consentendo solo gli elementi da diventano disponibili quando il dispositivo sia sbloccato, ovvero quando il dispositivo è bloccato non sono più disponibili. Essi possono essere archiviate anche in un backup crittografato. Una delle funzionalità chiave di keychain consiste nell'imporre il controllo di accesso. un'applicazione dispone di accesso per la parte di keychain e tutte le altre applicazioni non può essere applicate. Il diagramma seguente illustra come un'applicazione interagisce con il portachiavi:

[![](touchid-images/image1.png "Questo diagramma viene illustrata l'interazione di un'applicazione con il portachiavi")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>Proteggere Enclave

Il portachiavi: Impossibile decrittografare l'elemento keychain. al contrario viene eseguita *Secure Enclave*. Il enclave sicura è un elaboratore condivisione all'interno del chip A7 che è responsabile di determinare una corrispondenza corretta dai dati di impronta digitale del sensore ID tocco su una stampa registrata. Verrà quindi decrittografare l'elemento keychain e ripristinare il segreto decrittografato il portachiavi.

### <a name="working-with-keychain"></a>Utilizzo di portachiavi

Prima dell'applicazione deve eseguire una query in Keychain per verificare l'esistenza di una password. Se non esiste, si potrebbe essere necessario richiedere una password l'utente non è sempre richiesta. Se la password deve essere aggiornata, richiedere all'utente una nuova password e passare il valore aggiornato per il portachiavi.

> [!NOTE]
> Dopo che tramite un segreto recuperato dal Keychain, tutti i riferimenti ai dati devono essere eliminati dalla memoria. Mai assegnarlo a una variabile globale.

## <a name="keychain-acl-and-touch-id"></a>ID portachiavi ACL e il tocco

Elenco di controllo di accesso è un nuovo attributo dell'elemento keychain in iOS 8 che descrive le informazioni relative a ciò che deve essere eseguita per consentire una particolare operazione si verifichi. È possibile sotto forma di visualizzazione di una finestra di dialogo di avviso o la richiesta di un passcode. ACL consente di impostare l'accessibilità e l'autenticazione di un elemento portachiavi. Il diagramma seguente mostra come il nuovo attributo di relazione con il resto dell'elemento keychain:

[![](touchid-images/image2.png "Questo diagramma viene illustrato come il nuovo attributo di relazione con il resto dell'elemento keychain")](touchid-images/image2.png#lightbox)

A partire da iOS 8, è ora disponibile un nuovo criterio di presenza di utente, `SecAccessControl`, che viene applicata con il enclave protetta in un iPhone 5s e versioni successive. Si noterà nella tabella seguente, solo come la configurazione del dispositivo può influenzare la valutazione dei criteri:

|Configurazione del dispositivo|Valutazione dei criteri|Meccanismo di backup|
|--- |--- |--- |
|Dispositivo senza Passcode|Nessun accesso|Nessuno|
|Dispositivo con Passcode|Richiede Passcode|Nessuno|
|Dispositivo con ID tocco|Preferisce ID tocco|Consente di Passcode|

Tutte le operazioni all'interno di Enclave Secure possano considerano attendibili reciprocamente. Ciò significa che è possibile utilizzare il risultato dell'autenticazione ID tocco per autorizzare la decrittografia di elemento Keychain. Il Enclave Secure mantiene anche un contatore di corrispondenze non riuscite di Touch ID, in cui sarà necessario tornare a utilizzare il passcode caso un utente.
Un nuovo framework iOS 8, chiamato _autenticazione locale_, supporta questo processo di autenticazione all'interno del dispositivo. Nella sezione successiva verrà illustrato questo.

## <a name="local-authentication"></a>Autenticazione locale

È definito nella sezione precedente, applicazioni possono utilizzare l'autenticazione locale per autenticare l'utente in conformità con i criteri di sicurezza che sono stato impostato sul dispositivo.

Attualmente, l'API fornisce solo due funzionalità: in primo luogo, assiste i servizi di portachiavi esistenti tramite l'utilizzo di nuovo portachiavi controllo elenchi di accesso (ACL). Dati Keychain possono essere sbloccati con l'autenticazione ha esito positivo di un'impronta digitale gli utenti.

In secondo luogo, LocalAuthentication fornisce due metodi per l'autenticazione dell'applicazione in locale. Gli sviluppatori devono usare `CanEvaluatePolicy` per determinare se il dispositivo è in grado di accettare Touch ID e quindi `EvaluatePolicy` per avviare l'operazione di autenticazione.

Mentre entrambe le funzionalità offrano da autenticazione locale, non forniscono un meccanismo per l'applicazione o l'utente per l'autenticazione a un server remoto.
L'autenticazione locale include una nuova interfaccia utente standard per l'autenticazione. Nel caso di Touch ID, si tratta di una vista degli avvisi con due pulsanti, come illustrato di seguito. Un pulsante di annullamento e uno per utilizzare il fallback mezzo di autenticazione: il passcode. È inoltre disponibile un messaggio personalizzato che deve essere impostato. È consigliabile utilizzare questa opzione per segnalare all'utente perché è necessaria l'autenticazione di Touch ID.

[![](touchid-images/image12.png "L'avviso di autenticazione ID tocco")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>Con i servizi di portachiavi

È stato esaminato un po' prima come un elemento keychain viene decrittografato, utilizzando il enclave sicuro per verificare il passcode. In iOS 8, è possibile usare l'autenticazione locale per richiedere la verifica di ID tocco in combinazione con la funzionalità di elenchi di controllo di accesso, che fornisce l'implementazione del meccanismo di fallback, o la password.
Per utilizzare ACL dovremmo usare il `SecAccessControl` criteri e quindi controllare lo stato del dispositivo tramite `SecAccessible.WhenPasscodeSetThisDeviceOnly` o `SecAccessible.WhenUnlocked`.

#### <a name="considerations-with-acl"></a>Considerazioni con ACL

Molte operazioni, che è opportuno tenere presenti quando si utilizza ACL con il portachiavi e alcuni di questi sono elencati di seguito:

-   Utilizzare solo con l'applicazione di primo piano: se si chiama qualsiasi operazione keychain su un thread in background che la chiamata avrà esito negativo.
-   Aggiunta di elementi e aggiornandoli portachiavi potrebbero richiedere l'autenticazione.
-   Se una richiesta restituisce più gli elementi corrispondenti nel portachiavi, autenticazione potrebbe essere necessario.
-   Gli elementi protetti ACL sono dispositivi e pertanto non sincronizzato o solo sottoposto a backup.

### <a name="using-local-authentication-without-keychain-services"></a>Utilizzo dell'autenticazione locale senza servizi portachiavi

L'autenticazione locale è stato creato come un modo per raccogliere le credenziali, ad esempio passcode o ID tocco e l'utilizzo di Enclave Secure per completare l'autenticazione dell'utente. Considerare che funge da ponte tra l'applicazione e il Enclave sicura, che possono comunicare mai direttamente tra loro. E può essere utilizzato anche per la valutazione dei criteri per l'applicazione.

A questo scopo un'applicazione chiama la valutazione dei criteri all'interno di autenticazione locale, che avvia l'operazione all'interno di Enclave Secure. È possibile utilizzare questo metodo per fornire l'autenticazione all'app, senza l'esecuzione di query/accedere direttamente il Enclave Secure.

[![](touchid-images/image13a.png "Utilizzo dell'autenticazione locale senza servizi portachiavi")](touchid-images/image13a.png#lightbox)

Utilizzando l'autenticazione locale dell'applicazione fornisce un modo semplice dell'implementazione di verifica dell'utente, ad esempio per sbloccare la funzionalità esclusivamente per gli occhi del proprietario del dispositivo, ad esempio applicazioni per operazioni bancarie o genitori contribuire ai singoli applicazione. È possibile utilizzare anche un modo per estendere l'autenticazione già esistente: gli utenti come le informazioni di protezione, ma che desiderano inoltre disporre di opzioni.

La sicurezza dell'autenticazione locale è diverso da quello di keychain. Ad esempio, quando si utilizza il portachiavi, la relazione di trust è compreso tra il sistema operativo e il Enclave Secure. Con l'autenticazione locale, è compreso tra l'applicazione e il sistema operativo, il che significa che è possibile accedere solo ai risultati di Enclave sicura, non il Secure Enclave stesso.

In materia di sicurezza, è inoltre estremamente importante sapere che vi sia **Nessun accesso** dita registrate o immagini di impronta digitale. Il Enclave Secure è il proprietario di queste informazioni e, pertanto nessun altro componente di sistema è consentito l'accesso.

Per utilizzare l'ID tocco senza keychain usando l'API di autenticazione locale, esistono alcune funzioni che è possibile utilizzare. Questi sono descritte di seguito:

*   `CanEvaluatePolicy` – Questo semplicemente controllerà se il dispositivo è in grado di accettare ID tocco.
*   `EvaluatePolicy` : Avvia l'operazione di autenticazione e consente di visualizzare l'interfaccia utente e restituisce un `true` o `false` risposta.
*   `DeviceOwnerAuthenticationWithBiometrics` – Questo è il criterio che può essere utilizzato per visualizzare la schermata di Touch ID. Vale la pena notare che non è disponibile alcun meccanismo di fallback passcode qui, è invece necessario implementare questo fallback nell'applicazione per consentire agli utenti di ignorare l'autenticazione ID tocco.

Esistono alcune considerazioni con l'autenticazione locale, che sono elencati di seguito:

*   Come con Keychain, si può essere eseguito solo in primo piano. Chiamata in un thread in background causerà l'errore.
*   Tenere presente che la valutazione dei criteri potrebbe non riuscire. Un pulsante passcode dovrà essere implementata come un fallback.
*   È necessario fornire un `localizedReason` per spiegare perché è necessaria l'autenticazione. Ciò consente di compilare una relazione di trust con l'utente.

Successivamente, nella sezione seguente, esamineremo come implementare l'API tenendo conto di questi aspetti.

## <a name="adding-touch-id-to-your-application"></a>Aggiunta dell'ID tocco per l'applicazione

Nelle sezioni precedenti, è stato esaminato teoria accesso e l'autenticazione tramite Keychain e l'autenticazione locale. Verrà visualizzata un'occhiata a come è possibile integrare ID tocco all'applicazione.

### <a name="walkthrough"></a>Procedura dettagliata

Pertanto si esaminerà l'aggiunta di alcune autenticazione ID tocco per l'applicazione. In questa procedura dettagliata verrà per utilizzare il [tabella Storyboard](https://developer.xamarin.com/samples/StoryboardTable/) esempio. Si desidera assicurarsi che solo il proprietario del dispositivo può aggiungere un elemento all'elenco, non si voglia ingombrare chiunque consentendo di aggiungere un elemento.

1. Scaricare l'esempio ed eseguirlo in Visual Studio per Mac.
2. Fare doppio clic su `MainStoryboard.Storyboard` per aprire l'esempio nella finestra di progettazione iOS. In questo esempio, si vuole aggiungere una nuova schermata all'applicazione, che consentono di controllare l'autenticazione. Questo entra prima corrente `MasterViewController`.
3. Trascinare una nuova **View Controller** dal **della casella degli strumenti** per il **area di progettazione**. Impostare questo elemento come il **radice View Controller** da **Ctrl + trascinare** dal **navigazione Controller**:

    [![](touchid-images/image4.png "Impostare il Controller di visualizzazione radice")](touchid-images/image4.png#lightbox)
4.  Denominare il nuovo Controller di visualizzazione `AuthenticationViewController`.
5. Successivamente, trascinare un pulsante e posizionarlo sul `AuthenticationViewController`. Chiamare questo metodo `AuthenticateButton`e assegnargli il testo `Add a Chore`.
6. Creare un evento nel `AuthenticateButton` chiamato `AuthenticateMe`.
7. Creare un manuale definire da `AuthenticationViewController` facendo clic su barra di colore nero nella parte inferiore e **Ctrl + trascinare** dalla barra per il `MasterViewController` e scegliendo **push** (o **Mostra** Se si utilizza classi di dimensione):

    [![](touchid-images/image5.png "Trascinare dalla barra di MasterViewController e scegliendo push o visualizzare")](touchid-images/image6.png#lightbox)
8. Fare clic su appena creato di definire e assegnare l'identificatore `AuthenticationSegue`, come illustrato di seguito:

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

Questo è tutto il codice che necessario per implementare l'autenticazione di Touch ID utilizzando l'autenticazione locale. Le righe evidenziate nell'immagine seguente mostrano l'uso dell'autenticazione locale:

[![](touchid-images/image8.png "Le righe evidenziate mostrano l'uso dell'autenticazione locale")](touchid-images/image8.png#lightbox)

In primo luogo, è necessario stabilire se il dispositivo è in grado di accettare input, con l'ID tocco di `CanEvaluatePolicy` e passando il criterio `DeviceOwnerAuthenticationWithBiometrics`. Se ciò è vero, è possibile visualizzare l'interfaccia utente ID tocco tramite `EvaluatePolicy`. Esistono tre tipi di informazioni è necessario passare in `EvaluatePolicy` : il criterio, una stringa che spiega perché è necessaria l'autenticazione e un gestore di risposta. Il gestore di risposta indica l'applicazione non viene eseguita nel caso di un esito positivo o negativo, l'autenticazione. Esaminare più da vicino il gestore di risposta:

[![](touchid-images/image9.png "Il gestore di risposta")](touchid-images/image9.png#lightbox)

Viene specificato il gestore di risposta di tipo `LAContextReplyHandler`, che accetta l'esito positivo di parametri: un `bool` valore e un `NSError` chiamato `error`. Se ha esito positivo, si tratta in cui vengono effettivamente eseguiti qualsiasi è che si desidera eseguire l'autenticazione: in questo caso Visualizza la schermata che prendano aggiungerà un nuovo utente doveva avere. Tenere presente uno degli aspetti di autenticazione locale che deve essere eseguito in primo piano, pertanto assicurarsi di utilizzare `InvokeOnMainThread`:

[![](touchid-images/image10.png "Utilizzare InvokeOnMainThread per l'autenticazione locale")](touchid-images/image10.png#lightbox)

Infine, quando l'autenticazione ha avuto esito positivo, si vuole transizione verso la `MasterViewController`. Il `PerformSegue` metodo può essere utilizzato per eseguire questa operazione:

[![](touchid-images/image11.png "Chiamare il metodo PerformSegue per la transizione di MasterViewController")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>Riepilogo
In questa guida è stato esaminato portachiavi e il funzionamento in iOS. È inoltre esplorato il portachiavi ACL e modifiche in iOS. Successivamente, abbiamo presentato esaminerà il framework di autenticazione locale, che è una novità di iOS 8 e quindi esaminato l'implementazione dell'autenticazione Touch ID dell'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di ID tocco](https://developer.xamarin.com/samples/StoryboardTable_LocalAuthentication)
- [Esempio WWDC portachiavi](https://developer.xamarin.com/samples/KeychainTouchID/)
- [Keychain (esempio)](https://developer.xamarin.com/samples/Keychain/)
