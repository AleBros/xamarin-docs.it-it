---
title: Notifiche utente migliorata
description: Questo articolo vengono illustrate tutte le modalità che notifica gli utenti sono stata migliorata da iOS 10 e sul loro utilizzo in un'app xamarin. IOS.
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: b27d415240f3b8cd25c4bc54f6d176c50e42a250
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="enhanced-user-notifications"></a>Notifiche utente migliorata

_Questo articolo vengono illustrate tutte le modalità che notifica gli utenti sono stata migliorata da iOS 10 e sul loro utilizzo in un'app xamarin. IOS._

Nuovo a 10, la notifica all'utente consente di framework per la gestione delle notifiche locali e remote e il recapito di iOS. Usando questo framework, un'app o l'estensione dell'App può pianificare il recapito delle notifiche locale specificando un set di condizioni, ad esempio percorso o l'ora del giorno.

## <a name="about-user-notifications"></a>Informazioni sulle notifiche utente

Come descritto in precedenza, il nuovo framework di notifica utente consente per la gestione delle notifiche locali e remote e il recapito. Usando questo framework, un'app o l'estensione dell'App può pianificare il recapito delle notifiche locale specificando un set di condizioni, ad esempio percorso o l'ora del giorno.

Inoltre, l'app o l'estensione può ricevere (e potenzialmente modificare) le notifiche locali e remote come vengano recapitati a dispositivo iOS dell'utente.

Il nuovo framework di interfaccia utente di notifica utente consente a un'app o l'estensione dell'App per personalizzare l'aspetto delle notifiche locali e remote quando sono presentati all'utente.

Questo framework fornisce le seguenti che un'app è possibile recapitare le notifiche a un utente:

- **Avvisi Visual** , in cui la notifica esegue il roll verso il basso dalla parte superiore della schermata come un'intestazione.
- **Audio e alle vibrazioni** -può essere associato a una notifica.
- **Icona di App Badging** : dove è visualizzata una notifica che indica che il nuovo contenuto è disponibile, ad esempio il numero di messaggi di posta elettronica non letta icona dell'applicazione.

Inoltre, a seconda del contesto corrente dell'utente, esistono diversi modi in cui verrà visualizzata una notifica:

- Se il dispositivo è sbloccato, la notifica viene continuata verso il basso dalla parte superiore della schermata come banner.
- Se il dispositivo è bloccato, la notifica verrà visualizzata nella schermata di blocco dell'utente.
- Se l'utente venga persa una notifica, possono aprire il centro notifiche e visualizzare le notifiche di attesa disponibili, non esiste.

Un'app xamarin è disponibili due tipi di notifiche utente che è in grado di inviare:

- **Le notifiche locale** -queste vengono inviate da applicazioni installate localmente nel dispositivo dell'utente.
- **Le notifiche remote** -vengono inviati da un remoto server e è presentato all'utente o attivano un aggiornamento in background di contenuto dell'app.

### <a name="about-local-notifications"></a>Informazioni sulle notifiche locale

Le notifiche locale che può inviare un'app iOS sono gli attributi e le funzionalità seguenti:

- In cui vengono inviati da applicazioni che sono locali nel dispositivo dell'utente. 
- Sono configurabili da usare all'ora o al percorso basato su trigger. 
- L'app consente di pianificare la notifica con il dispositivo dell'utente e viene visualizzato quando viene soddisfatta la condizione del trigger.
- Quando l'utente interagisce con una notifica, l'applicazione riceverà un callback.

Alcuni esempi di notifiche locale:

- Avvisi del calendario
- Avvisi di promemoria
- Percorso compatibile con trigger

Per ulteriori informazioni, vedere Apple [locale e remoto Guida per programmatori notifica](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentazione.

### <a name="about-remote-notifications"></a>Informazioni sulle notifiche Remote

Le notifiche remoto che può inviare un'app iOS sono gli attributi e le funzionalità seguenti:

- L'applicazione dispone di un componente sul lato server con cui comunica.
- Il servizio di notifica Push Apple (APN) viene utilizzato per trasmettere un recapito sforzo di notifiche remoto al dispositivo dell'utente dai server basati sul cloud dello sviluppatore.
- Quando l'applicazione riceve la notifica remoto verrà visualizzato all'utente.
- Quando l'utente interagisce con la notifica, l'applicazione riceverà un callback.

Alcuni esempi di notifiche remoto:

- Avvisi di notizie
- Aggiornamenti sportivi
- Messaggi di messaggistica immediati

Sono disponibili due tipi di notifiche remoto a un'app iOS:

- **Connessione utente** -questi vengono visualizzati all'utente del dispositivo.
- **Aggiornamenti invisibile all'utente** -forniscono un meccanismo per aggiornare il contenuto di un'app iOS in background. Quando viene ricevuto un aggiornamento invisibile all'utente, l'app può raggiungere per rimuovere i server a discesa il contenuto più recente.

Per ulteriori informazioni, vedere Apple [locale e remoto Guida per programmatori notifica](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentazione.

### <a name="about-the-existing-notifications-api"></a>Sulle notifiche API esistente

Prima di iOS 10, utilizzare un'app iOS `UIApplication` per registrare una notifica con il sistema e per pianificare la modalità di notifica dovrebbe essere attivata (in base al tempo o percorso).

Esistono diversi problema che uno sviluppatore potrebbe verificarsi quando si lavora con l'API di notifica esistente:

- Si sono verificati diversi callback richiesti per locale o remoto notifiche che potrebbe provocare la duplicazione del codice.
- L'app fosse un controllo limitato della notifica dopo che è stato pianificato con il sistema.
- Si sono verificati diversi livelli di supporto in tutte le piattaforme esistenti di Apple.

### <a name="about-the-new-user-notification-framework"></a>Sul nuovo Framework di notifica utente

Con iOS 10, Apple ha introdotto il nuovo framework di notifica all'utente, che sostituisce esistente `UIApplication` metodo indicato in precedenza.

Il framework di notifica utente fornisce le operazioni seguenti:

- Un'API familiare che include la parità di funzionalità con i metodi precedenti semplificando porting del codice dal framework esistenti.
- Include un set espanso di opzioni relative al contenuto che consente più notifiche da inviare all'utente.
- Sia locale e remoto possa essere gestita dal codice e i callback.
- Semplifica il processo di gestione di callback che vengono inviati a un'app quando l'utente interagisce con una notifica.
- Gestione migliorata di notifiche recapitate e in sospeso inclusa la possibilità di rimuovere o aggiornare le notifiche.
- Aggiunge la possibilità di eseguire la presentazione in-app delle notifiche.
- Consente di pianificare e gestire le notifiche all'interno di estensioni dell'App.
- Aggiunge nuovo punto di estensione per le notifiche stesse. 

Il nuovo framework di notifica utente fornisce una notifica unificata API in più delle piattaforme che Apple supporti inclusi: 

- **iOS** -supporto per gestire e pianificare le notifiche completo.
- **tvOS** -consente di eseguire badge icone dell'app per le notifiche locali e remote.
- **watchOS** : aggiunge la possibilità di inoltrare le notifiche dal dispositivo iOS associato dell'utente per i relativi Apple Watch e fornisce le app di espressioni di controllo che consentono di effettuare le notifiche locale direttamente nel controllo stesso.

Per ulteriori informazioni, vedere Apple [UserNotifications Framework riferimento](https://developer.apple.com/reference/usernotifications) e [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) documentazione.

## <a name="preparing-for-notification-delivery"></a>Preparazione per il recapito delle notifiche

Prima di iOS app può inviare notifiche all'utente che l'app deve essere registrato con il sistema e, poiché una notifica di un'interruzione all'utente, un'app deve richiedere in modo esplicito l'autorizzazione prima di inviarli.

Esistono tre diversi livelli di richieste di notifica che l'utente può approvare per un'app:

- Visualizza intestazione.
- Suoni degli avvisi.
- Badging l'icona dell'app.

Questi livelli di approvazione, inoltre, devono essere richiesto e impostati per le notifiche locali e remote.

Deve richieste di autorizzazione di notifica non appena viene avviata l'app aggiungendo il codice seguente per il `FinishedLaunching` metodo il `AppDelegate` e impostare il tipo di notifica desiderato (`UNAuthorizationOptions`):

```csharp
using UserNotifications;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    return true;
}
```

Inoltre, un utente modificabili sempre i privilegi di notifica per un'app in qualsiasi momento usando il **impostazioni** app sul dispositivo. L'app deve ricercare i privilegi dell'utente richiesta di notifica prima di presentare una notifica tramite il codice seguente:

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
}); 
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Configurazione dell'ambiente remoto notifiche

Nuovo in iOS 10, lo sviluppatore deve informare il sistema operativo quali notifica Push di ambiente sono in esecuzione in come lo sviluppo o di produzione. Se non viene fornito queste informazioni può causare l'app viene rifiutato quando viene inviato al iTune App Store con una notifica simile al seguente:

> Diritto di notifica Push mancano - app include un'API per servizi di notifica Push di Apple, ma la `aps-environment` diritto è mancano dalla firma dell'app.

Per fornire i diritti necessari, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic su di `Entitlements.plist` file nel **soluzione riempimento** per aprirlo e modificarlo.
2. Passare il **origine** Vista: 

    [![](enhanced-user-notifications-images/setup01.png "La visualizzazione origine")](enhanced-user-notifications-images/setup01.png#lightbox)
3. Fare clic su di **+** pulsante per aggiungere una nuova chiave.
4. Immettere `aps-environment` per il **proprietà**, lasciare il **tipo** come `String` e immettere una `development` o `production` per il **valore**: 

    [![](enhanced-user-notifications-images/setup02.png "La proprietà di ambiente di punti di accesso")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Salvare le modifiche apportate al file.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic su di `Entitlements.plist` file nel **Esplora** per aprirlo e modificarlo.
3. Fare clic su di **+** pulsante per aggiungere una nuova chiave.
4. Immettere `aps-environment` per il **proprietà**, lasciare il **tipo** come `String` e immettere una `development` o `production` per il **valore**: 

    [![](enhanced-user-notifications-images/setup02w.png "La proprietà di ambiente di punti di accesso")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Salvare le modifiche apportate al file.

-----

### <a name="registering-for-remote-notifications"></a>Registrazione per le notifiche Remote

Se l'app verrà l'invio e ricezione di notifiche remoto, sarà comunque necessario eseguire _Token registrazione_ utilizzando esistente `UIApplication` API. Questa registrazione, è necessario avere un accesso alla connessione di rete in tempo reale APN, che genera il token necessario che verrà inviato all'app del dispositivo. L'app deve quindi inoltrare il token all'app di lato server per gli sviluppatori a registrare le notifiche remote:

[![](enhanced-user-notifications-images/token01.png "Panoramica di registrazione del token")](enhanced-user-notifications-images/token01.png#lightbox)

Utilizzare il codice seguente per inizializzare la registrazione richiesta:

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

Il token viene inviato all'app di lato server per gli sviluppatori dovranno essere inclusi come parte del Payload di notifica che get inviati dal server per servizio APN quando si invia una notifica remoto:

[![](enhanced-user-notifications-images/token02.png "Il token incluso come parte del Payload di notifica")](enhanced-user-notifications-images/token02.png#lightbox)

Il token funge da chiave che collega la notifica e l'applicazione utilizzata per aprire o rispondere alla notifica.

Per ulteriori informazioni, vedere Apple [locale e remoto Guida per programmatori notifica](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentazione.

## <a name="notification-delivery"></a>Recapito delle notifiche

Con l'app è completamente registrato e le autorizzazioni necessarie richiedano dal concesso dall'utente, l'applicazione è ora pronto per inviare e ricevere le notifiche. 

### <a name="providing-notification-content"></a>Specificare il contenuto di notifica

Nuovo in iOS 10, tutte le notifiche contengono entrambe una **titolo** e **sottotitolo** che verrà sempre visualizzato con il **corpo** del contenuto della notifica. Nuova, è anche la possibilità di aggiungere **allegati multimediali** per il contenuto di notifica.

Per creare il contenuto di una notifica locale, utilizzare il codice seguente:

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

Per le notifiche remoto, il processo è simile:

```csharp
{
    "aps":{
        "alert":{
            "title":"Notification Title",
            "subtitle":"Notification Subtitle",
            "body":"This is the message body of the notification."
        },
        "badge":1
    }
}
```

### <a name="scheduling-when-a-notification-is-sent"></a>Notifica a quando la pianificazione viene inviata

Con il contenuto della notifica creato, l'app è necessario per la pianificazione verrà visualizzata la notifica all'utente impostando un *Trigger*. iOS 10 offre quattro diversi tipi di Trigger:

- **Notifica push** - viene utilizzato esclusivamente con le notifiche remoto e viene attivato quando crea il pacchetto APN invia una notifica all'app in esecuzione nel dispositivo.
- **Intervallo di tempo** -consente una notifica locale devono essere pianificate da un'ora intervallo iniziano con l'ora e di fine un un dato momento futuro. Ad esempio, `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`.
- **Data di calendario** -consente di notifiche locale per la pianificazione di una data e ora specifiche.
- **Percorso base** -consente di notifiche locale devono essere pianificate quando il dispositivo iOS entra o lasciare una posizione geografia specifica o in una determinato prossimità per qualsiasi beacon Bluetooth.

Quando una notifica locale è pronta, l'applicazione deve chiamare il `Add` metodo il `UNUserNotificationCenter` oggetto per pianificare la visualizzazione per l'utente. Per le notifiche remoto, l'app sul lato server invia un Payload di notifica per il servizio APNs, che quindi invia il pacchetto al dispositivo dell'utente.

Unione di tutte le parti, un esempio di notifica locale potrebbe essere simile:

```csharp
using UserNotifications;
...

var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);

var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

## <a name="handling-foreground-app-notifications"></a>Gestione delle notifiche di App in primo piano

Nuovo in iOS 10, un'applicazione può gestire le notifiche in modo diverso quando è in primo piano e viene attivata una notifica. Fornendo un `UNUserNotificationCenterDelegate` e l'implementazione di `UserNotificationCenter` metodo, l'app può assumere la responsabilità per la visualizzazione della notifica. Ad esempio:

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        #region Constructors
        public UserNotificationCenterDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void WillPresentNotification (UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
        {
            // Do something with the notification
            Console.WriteLine ("Active Notification: {0}", notification);

            // Tell system to display the notification anyway or use
            // `None` to say we have handled the display locally.
            completionHandler (UNNotificationPresentationOptions.Alert);
        }
        #endregion
    }
}
```

Questo codice viene semplicemente scritto il contenuto del `UNNotification` dell'Output dell'applicazione e richiede al sistema per visualizzare l'avviso standard per la notifica. 

Se l'applicazione si desidera visualizzare la notifica in sé quando era in primo piano, non usare le impostazioni predefinite di sistema, passare `None` al gestore di completamento. Esempio:

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

Con questo codice, aprire il `AppDelegate.cs` per la modifica di file e modificare il `FinishedLaunching` metodo per la ricerca è simile alla seguente:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    // Watch for notifications while the app is active
    UNUserNotificationCenter.Current.Delegate = new UserNotificationCenterDelegate ();

    return true;
}
```

Questo codice si sta connettendo personalizzata `UNUserNotificationCenterDelegate` precedente corrente `UNUserNotificationCenter` affinché l'applicazione può gestire la notifica mentre è attivo e in primo piano.

## <a name="notification-management"></a>Gestione delle notifiche

Nuovo in iOS 10, gestione delle notifiche fornisce l'accesso alle notifiche recapitate e in sospeso e la possibilità di rimuovere, aggiornare o alzare di livello queste notifiche.

È una parte importante della gestione delle notifiche di _identificatore della richiesta_ che è stato assegnato alla notifica quando è stata creata e pianificata con il sistema. Per le notifiche remoto, viene assegnato tramite la nuova `apps-collapse-id` campo nell'intestazione della richiesta HTTP.

L'identificatore della richiesta viene utilizzato per selezionare la notifica che si desidera eseguire la gestione di notifica in app.

### <a name="removing-notifications"></a>Rimozione delle notifiche

Per rimuovere una notifica in sospeso dal sistema, utilizzare il codice seguente:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

Per rimuovere una notifica già recapitata, utilizzare il codice seguente:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>L'aggiornamento di una notifica esistente

Per aggiornare una notifica esistente, creare una nuova notifica con i parametri desiderati modificati (ad esempio una nuova ora di trigger) e aggiungerlo al sistema con lo stesso identificatore richiesta la notifica che deve essere modificata. Esempio:


```csharp
using UserNotifications;
...

// Rebuild notification
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

// New trigger time
var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger (10, false);

// ID of Notification to be updated
var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

// Add to system to modify existing Notification
UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

Per le notifiche già recapitate, la notifica esistente otterrà aggiornata e promossi alla parte superiore dell'elenco nelle schermate Home e di blocco e nel centro notifiche se è già stato letto dall'utente.

## <a name="working-with-notification-actions"></a>Utilizzo di azioni di notifica

In iOS 10, le notifiche che vengono recapitate all'utente non sono statiche e forniscono numerose che l'utente può interagire con essi (da incorporato per azioni personalizzate).

Esistono tre tipi di azioni in grado di rispondere a un'app per iOS:

- **Azione predefinita** -si tratta di quando l'utente digita una notifica per aprire l'app e visualizzare i dettagli della notifica specificato.
- **Azioni personalizzate** -questi sono stati aggiunti in iOS 8 e forniscono un modo rapido per l'utente di eseguire un'attività personalizzata direttamente dalla notifica senza la necessità di avviare l'app. Può essere presentate come un elenco di pulsanti con titoli personalizzabili o un campo di immissione testo che può essere eseguita in Background (dove l'applicazione è specificato un breve periodo di tempo per completare la richiesta) o di primo piano (in cui viene avviata l'app in primo piano per fu lfill la richiesta). Azioni personalizzate sono disponibili in iOS e watchOS.
- **Elimina azione** -questa azione viene inviato all'app quando l'utente chiude una notifica specificata.

### <a name="creating-custom-actions"></a>Creazione di azioni personalizzate

Per creare e registrare un'azione personalizzata con il sistema, utilizzare il codice seguente:

```csharp
// Create action
var actionID = "reply";
var title = "Reply";
var action = UNNotificationAction.FromIdentifier (actionID, title, UNNotificationActionOptions.None);

// Create category
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);
    
// Register category
var categories = new UNNotificationCategory [] { category };
UNUserNotificationCenter.Current.SetNotificationCategories (new NSSet<UNNotificationCategory>(categories)); 
```

Quando si crea un nuovo `UNNotificationAction`, viene assegnato un ID univoco e il titolo che verrà visualizzato nel pulsante. Per impostazione predefinita, l'azione verrà creato durante un'operazione in Background, tuttavia è possibile specificare le opzioni per modificare il comportamento dell'azione (ad esempio impostandolo come un'azione di primo piano).

Ognuna delle azioni create deve essere associato a una categoria. Quando si crea un nuovo `UNNotificationCategory`, viene assegnato un ID univoco, un elenco di azioni che è possibile eseguire un elenco di ID finalità per fornire ulteriori informazioni lo scopo delle azioni nella categoria e alcune opzioni per controllare il comportamento della categoria.

Infine, tutte le categorie sono registrate con il sistema con il `SetNotificationCategories` metodo.

### <a name="presenting-custom-actions"></a>Presentazione di azioni personalizzate

Dopo aver creato un set di azioni personalizzate e le categorie e registrati con il sistema, può essere presentate dal locale o remoto notifiche.

Per le notifiche remoto, impostare un `category` nel Payload della notifica remoto che corrisponde a una delle categorie create in precedenza. Ad esempio:

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

Per le notifiche locale, impostare il `CategoryIdentifier` proprietà del `UNMutableNotificationContent` oggetto. Ad esempio:

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

Nuovamente, l'ID deve corrispondere a una delle categorie che è stato creato in precedenza.

### <a name="handling-dismiss-actions"></a>Gestione chiudere azioni

Come descritto in precedenza, quando l'utente chiude una notifica di un'azione chiudere possono essere inviata all'app. Poiché non si tratta di un'azione standard, un'opzione sarà necessario impostare quando viene creata la categoria. Ad esempio:

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Gestione delle risposte di azione

Quando l'utente interagisce con le azioni personalizzate e le categorie che sono state create in precedenza, è necessario per completare l'attività richiesta l'app. Questa operazione viene eseguita, fornendo un `UNUserNotificationCenterDelegate` e l'implementazione di `UserNotificationCenter` metodo. Ad esempio:

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        ...

        #region Override Methods
        public override void DidReceiveNotificationResponse (UNUserNotificationCenter center, UNNotificationResponse response, Action completionHandler)
        {
            // Take action based on Action ID
            switch (response.ActionIdentifier) {
            case "reply":
                // Do something
                break;
            default:
                // Take action based on identifier
                if (response.IsDefaultAction) {
                    // Handle default action...
                } else if (response.IsDismissAction) {
                    // Handle dismiss action
                }
                break;
            }

            // Inform caller it has been handled
            completionHandler();
        }
        #endregion
    }
}
```

Il valore passato in `UNNotificationResponse` classe dispone di un `ActionIdentifier` proprietà che possono essere azione predefinito o l'azione chiudere. Utilizzare `response.Notification.Request.Identifier` per verificare la presenza di tutte le azioni personalizzate.

Il `UserText` proprietà contiene il valore di input di testo qualsiasi utente. Il `Notification` proprietà contiene la notifica di origine che include la richiesta con il Trigger e notifica contenuto. L'app può decidere se è locale o remoto notifica in base al tipo di trigger.

## <a name="working-with-service-extensions"></a>Utilizzo di estensioni del servizio

Quando si utilizzano le comunicazioni Remote, _le estensioni del servizio_ consentono di abilitare la crittografia end-to-end all'interno del Payload di notifica. Le estensioni del servizio sono un'estensione dell'interfaccia utente (disponibile in iOS 10) in esecuzione in background con lo scopo principale di aumento o sostituire il contenuto di una notifica visibile prima viene presentato all'utente. 

[![](enhanced-user-notifications-images/extension01.png "Panoramica di estensione del servizio")](enhanced-user-notifications-images/extension01.png#lightbox)

Le estensioni del servizio devono essere eseguiti rapidamente e vengono fornite solo un breve periodo di tempo per l'esecuzione dal sistema. Nel caso in cui l'estensione del servizio non riesce a completare l'attività nel periodo di tempo stabilito, verrà chiamato un metodo di fallback. Se il fallback non riesce, il contenuto di notifica originale verrà visualizzato all'utente.

Alcuni possibili utilizzi delle estensioni del servizio includono:

- Fornisce la crittografia end-to-end del contenuto della notifica remota.
- Per aggiungere allegati remoto notifiche per arricchire li.

### <a name="implementing-a-service-extension"></a>Implementazione di un'estensione del servizio

Per implementare un'estensione del servizio in un'app xamarin. IOS, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic sul nome della soluzione nel **soluzione riempimento** e selezionare **Aggiungi** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **estensioni** > **le estensioni del servizio di notifica** e fare clic su di **Avanti** pulsante: 

    [![](enhanced-user-notifications-images/extension02.png "Selezionare le estensioni del servizio di notifica")](enhanced-user-notifications-images/extension02.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic su di **Avanti** pulsante: 

    [![](enhanced-user-notifications-images/extension03.png "Immettere un nome per l'estensione")](enhanced-user-notifications-images/extension03.png#lightbox)
5. Regolare il **nome progetto** e/o **Nome soluzione** se necessario, fare clic su di **crea** pulsante: 

    [![](enhanced-user-notifications-images/extension04.png "Modificare il nome del progetto e/o il nome della soluzione")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Aprire la soluzione dell'app in Visual Studio.
2. Fare clic sul nome della soluzione nel **Esplora soluzioni** e selezionare **Aggiungi > Nuovo progetto...** .
3. Selezionare **Visual c# > iOS estensioni > estensione del servizio di notifica**:

    [![](enhanced-user-notifications-images/extension01.w157-sml.png "Selezionare le estensioni del servizio di notifica")](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic su di **OK** pulsante.

-----

> [!IMPORTANT]
> L'identificatore Bundle per l'estensione del servizio deve corrispondere all'identificatore Bundle dell'app principale con `.appnameserviceextension` aggiunto alla fine. Ad esempio, se l'app principale contiene un identificatore Bundle di `com.xamarin.monkeynotify`, l'estensione del servizio deve avere un identificatore Bundle di `com.xamarin.monkeynotify.monkeynotifyserviceextension`. Questo deve essere impostato automaticamente quando l'estensione viene aggiunta alla soluzione. 

L'estensione del servizio di notifica che dovrà essere modificato per fornire la funzionalità richiesta è una classe principale. Ad esempio:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyChatServiceExtension
{
    [Register ("NotificationService")]
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Computed Properties
        public Action<UNNotificationContent> ContentHandler { get; set; }
        public UNMutableNotificationContent BestAttemptContent { get; set; }
        #endregion

        #region Constructors
        protected NotificationService (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            ContentHandler = contentHandler;
            BestAttemptContent = (UNMutableNotificationContent)request.Content.MutableCopy ();

            // Modify the notification content here...
            BestAttemptContent.Title = $"{BestAttemptContent.Title}[modified]";

            ContentHandler (BestAttemptContent);
        }

        public override void TimeWillExpire ()
        {
            // Called just before the extension will be terminated by the system.
            // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.

            ContentHandler (BestAttemptContent);
        }
        #endregion
    }
}
```

Il primo metodo, `DidReceiveNotificationRequest`, verrà passato l'identificatore di notifica, nonché il contenuto di notifica tramite il `request` oggetto. Il valore passato in `contentHandler` dovranno essere chiamati per presentare la notifica all'utente.

Il secondo metodo, `TimeWillExpire`, verrà chiamato prima dell'ora sta per esaurirsi per l'estensione del servizio elaborare la richiesta. Se l'estensione del servizio non riesce a chiamare il `contentHandler` nell'intervallo di tempo assegnato, il contenuto originale verrà visualizzato all'utente.

### <a name="triggering-a-service-extension"></a>Attivazione di un'estensione del servizio

Con un'estensione del servizio di creazione e il recapito con l'app, può essere attivata modificando il Payload di notifica remoto inviato al dispositivo. Ad esempio:

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

Il nuovo `mutable-content` chiave specifica che l'estensione del servizio dovrà essere avviata per aggiornare il contenuto di notifica remota. Il `encrypted-content` chiave contiene i dati crittografati in grado di decrittografare l'estensione del servizio prima di presentare all'utente.

Esaminare l'estensione del servizio di esempio seguente:

```csharp
using UserNotification;

namespace myApp {
    public class NotificationService : UNNotificationServiceExtension {
    
        public override void DidReceiveNotificationRequest(UNNotificationRequest request, contentHandler) {
            // Decrypt payload
            var decryptedBody = Decrypt(Request.Content.UserInfo["encrypted-content"]);
            
            // Modify Notification body
            var newContent = new UNMutableNotificationContent();
            newContent.Body = decryptedBody;
            
            // Present to user
            contentHandler(newContent);
        }
        
        public override void TimeWillExpire() {
            // Handle out-of-time fallback event
            ...
        }
        
    }
}
```

Questo codice consente di decrittografare il contenuto crittografato dal `encrypted-content` chiave, crea un nuovo `UNMutableNotificationContent`, imposta il `Body` proprietà per il contenuto decrittografato e Usa il `contentHandler` per presentare la notifica all'utente.

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati tutte le modalità che notifica gli utenti sono stata migliorata da iOS 10. Presentato il nuovo framework di notifica all'utente e come utilizzarla in un'app xamarin o un'estensione dell'App.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Riferimento di Framework UserNotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guida per programmatori notifica locali e remoti](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
