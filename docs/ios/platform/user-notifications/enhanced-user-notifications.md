---
title: Notifiche utente migliorate in Novell. iOS
description: Questo articolo descrive il framework delle notifiche utente introdotto in iOS 10. Vengono illustrate le notifiche locali, le notifiche remote, la gestione delle notifiche, le azioni di notifica e altro ancora.
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 775675e11b85bb53bb51c36b71129a086d9dd60f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651254"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>Notifiche utente migliorate in Novell. iOS

Una novità di iOS 10, il Framework di notifica utente consente la distribuzione e la gestione di notifiche locali e remote. Usando questo Framework, un'estensione app o app può pianificare il recapito delle notifiche locali specificando un set di condizioni, ad esempio la posizione o l'ora del giorno.

## <a name="about-user-notifications"></a>Informazioni sulle notifiche utente

Come indicato in precedenza, il nuovo Framework di notifica utente consente la distribuzione e la gestione di notifiche locali e remote. Usando questo Framework, un'estensione app o app può pianificare il recapito delle notifiche locali specificando un set di condizioni, ad esempio la posizione o l'ora del giorno.

Inoltre, l'app o l'estensione può ricevere e potenzialmente modificare le notifiche locali e remote quando vengono recapitate al dispositivo iOS dell'utente.

Il nuovo Framework dell'interfaccia utente di notifica utente consente a un'app o all'estensione dell'app di personalizzare l'aspetto delle notifiche locali e remote quando vengono presentate all'utente.

Questo Framework fornisce i metodi seguenti che un'app può recapitare le notifiche a un utente:

- **Avvisi visivi** , in cui la notifica viene spostata verso il basso dalla parte superiore dello schermo come banner.
- **Audio e vibrazioni** : possono essere associati a una notifica.
- **Icona dell'app promuovere presso** -dove l'icona dell'app visualizza una notifica che mostra che è disponibile nuovo contenuto, ad esempio il numero di messaggi di posta elettronica non letti.

Inoltre, a seconda del contesto corrente dell'utente, esistono diversi modi in cui verrà presentata una notifica:

- Se il dispositivo è sbloccato, la notifica verrà visualizzata come banner nella parte superiore della schermata.
- Se il dispositivo è bloccato, la notifica verrà visualizzata nella schermata di blocco dell'utente.
- Se l'utente ha perso una notifica, può aprire il centro notifiche e visualizzare eventuali notifiche in attesa disponibili.

Un'app Novell. iOS include due tipi di notifiche utente che è in grado di inviare:

- **Notifiche locali** : vengono inviate dalle app installate localmente nel dispositivo degli utenti.
- **Notifiche remote** : vengono inviate da un server remoto e presentate all'utente o attivano un aggiornamento in background del contenuto dell'app.

### <a name="about-local-notifications"></a>Informazioni sulle notifiche locali

Le notifiche locali che un'app per iOS può inviare hanno le seguenti caratteristiche e attributi:

- Vengono inviati dalle app locali sul dispositivo dell'utente. 
- Possono essere configurati in modo da usare i trigger time o location based. 
- L'app pianifica la notifica con il dispositivo dell'utente e viene visualizzata quando viene soddisfatta la condizione del trigger.
- Quando l'utente interagisce con una notifica, l'app riceverà un callback.

Di seguito sono riportati alcuni esempi di notifiche locali:

- Avvisi del calendario
- Avvisi promemoria
- Trigger con rilevamento della posizione

Per ulteriori informazioni, vedere la documentazione della [Guida alla programmazione delle notifiche remote e locali](https://developer.apple.com/documentation/usernotifications) di Apple.

### <a name="about-remote-notifications"></a>Informazioni sulle notifiche remote

Le notifiche remote che un'app per iOS può inviare presentano le funzionalità e gli attributi seguenti:

- L'app dispone di un componente lato server con cui comunica.
- Il Apple Push Notification Service (APNs) viene usato per trasmettere al dispositivo dell'utente un recapito ottimale delle notifiche remote dai server basati sul cloud dello sviluppatore.
- Quando l'app riceve la notifica remota, verrà visualizzata all'utente.
- Quando l'utente interagisce con la notifica, l'app riceverà un callback.

Di seguito sono riportati alcuni esempi di notifiche remote:

- Avvisi notizie
- Aggiornamenti sportivi
- Messaggi di messaggistica immediata

Sono disponibili due tipi di notifiche remote per un'app iOS:

- **Interfaccia utente** : vengono visualizzati dall'utente sul dispositivo.
- **Aggiornamenti automatici** : forniscono un meccanismo per aggiornare il contenuto di un'app iOS in background. Quando viene ricevuto un aggiornamento invisibile all'utente, l'app può raggiungere il contenuto più recente nell'app Rimuovi server.

Per ulteriori informazioni, vedere la documentazione della [Guida alla programmazione delle notifiche remote e locali](https://developer.apple.com/documentation/usernotifications) di Apple.

### <a name="about-the-existing-notifications-api"></a>Informazioni sull'API delle notifiche esistente

Prima di iOS 10, un'app per iOS avrebbe `UIApplication` usato per registrare una notifica con il sistema e per pianificare la modalità di attivazione della notifica (in base all'ora o alla posizione).

Esistono diversi problemi che uno sviluppatore potrebbe incontrare quando si usa l'API di notifica esistente:

- Sono state richieste diverse richiamate per le notifiche locali o remote che potrebbero causare la duplicazione del codice.
- L'app aveva un controllo limitato della notifica dopo che era stata pianificata con il sistema.
- Esistono diversi livelli di supporto per tutte le piattaforme esistenti di Apple.

### <a name="about-the-new-user-notification-framework"></a>Informazioni sul nuovo Framework di notifica utente

Con iOS 10, Apple ha introdotto il nuovo Framework di notifica utente, che sostituisce il `UIApplication` metodo esistente indicato in precedenza.

Il Framework di notifica utente offre quanto segue:

- Un'API familiare che include la parità di funzionalità con i metodi precedenti, semplificando la porta del codice dal Framework esistente.
- Include un set espanso di opzioni di contenuto che consente l'invio di notifiche più complete all'utente.
- Le notifiche locali e remote possono essere gestite con lo stesso codice e i callback.
- Semplifica il processo di gestione dei callback che vengono inviati a un'app quando l'utente interagisce con una notifica.
- Gestione migliorata delle notifiche in sospeso e recapitate, inclusa la possibilità di rimuovere o aggiornare le notifiche.
- Aggiunge la possibilità di eseguire la presentazione delle notifiche in-app.
- Consente di pianificare e gestire le notifiche dall'interno delle estensioni dell'app.
- Aggiunge un nuovo punto di estensione per le notifiche stesse. 

Il nuovo Framework di notifica utente fornisce un'API di notifica unificata sul multiplo di piattaforme supportate da Apple, tra cui: 

- **iOS** : supporto completo per la gestione e la pianificazione delle notifiche.
- **tvOS** : aggiunge la possibilità di notificare le icone dell'app per le notifiche locali e remote.
- **watchos** : aggiunge la possibilità di trasmettere le notifiche dal dispositivo iOS associato all'utente alla Apple Watch e fornisce alle app di controllo la possibilità di eseguire notifiche locali direttamente sull'orologio.

Per ulteriori informazioni, vedere la documentazione di [riferimento di UserNotifications Framework](https://developer.apple.com/reference/usernotifications) e la documentazione di [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) .

## <a name="preparing-for-notification-delivery"></a>Preparazione per il recapito delle notifiche

Prima che un'app iOS possa inviare notifiche all'utente, l'app deve essere registrata con il sistema e, poiché una notifica è un'interruzione dell'utente, un'app deve richiedere esplicitamente l'autorizzazione prima di inviarle.

Sono disponibili tre diversi livelli di richieste di notifica che l'utente può approvare per un'app:

- Visualizzazione banner.
- Avvisi audio.
- Promuovere presso l'icona dell'app.

Inoltre, questi livelli di approvazione devono essere richiesti e impostati per le notifiche locali e remote.

È necessario richiedere l'autorizzazione di notifica non appena l'app viene avviata aggiungendo il codice seguente `FinishedLaunching` al metodo `AppDelegate` di e impostando il tipo di notifica`UNAuthorizationOptions`desiderato ():

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

Inoltre, un utente può sempre modificare i privilegi di notifica per un'app in qualsiasi momento usando l'app **Impostazioni** nel dispositivo. L'app deve verificare i privilegi di notifica richiesti dall'utente prima di presentare una notifica usando il codice seguente:

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
});    
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Configurazione dell'ambiente di notifiche remote

Una novità di iOS 10, lo sviluppatore deve informare il sistema operativo quali notifiche push dell'ambiente sono in esecuzione in fase di sviluppo o di produzione. L'impossibilità di fornire queste informazioni può causare il rifiuto dell'app quando viene inviata all'App Store di iTune con una notifica simile alla seguente:

> Diritto di notifica push mancante: l'app include un'API per il servizio di notifica push di Apple `aps-environment` , ma il diritto non è presente nella firma dell'app.

Per fornire i diritti necessari, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare doppio clic sul `Entitlements.plist` file nella **riquadro della soluzione** per aprirlo per la modifica.
2. Passare alla visualizzazione **origine** : 

    [![](enhanced-user-notifications-images/setup01.png "Visualizzazione origine")](enhanced-user-notifications-images/setup01.png#lightbox)
3. Fare clic **+** sul pulsante per aggiungere una nuova chiave.
4. Immettere `aps-environment` per la **proprietà**, lasciare il `development` tipo come `String` e immettere o `production` per il **valore**: 

    [![](enhanced-user-notifications-images/setup02.png "Proprietà APS-Environment")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Salvare le modifiche apportate al file.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare doppio clic sul `Entitlements.plist` file nella **Esplora soluzioni** per aprirlo per la modifica.
2. Fare clic **+** sul pulsante per aggiungere una nuova chiave.
3. Immettere `aps-environment` per la **proprietà**, lasciare il `development` tipo come `String` e immettere o `production` per il **valore**: 

    [![](enhanced-user-notifications-images/setup02w.png "Proprietà APS-Environment")](enhanced-user-notifications-images/setup02.png#lightbox)
4. Salvare le modifiche apportate al file.

-----

### <a name="registering-for-remote-notifications"></a>Registrazione per le notifiche remote

Se l'app invierà e riceverà notifiche remote, sarà comunque necessario eseguire la _registrazione del token_ usando l'API `UIApplication` esistente. Questa registrazione richiede che il dispositivo disponga di un accesso alla connessione di rete in tempo reale APNs, che genererà il token necessario che verrà inviato all'app. L'app deve quindi inviare questo token all'app lato server dello sviluppatore per eseguire la registrazione per le notifiche remote:

[![](enhanced-user-notifications-images/token01.png "Cenni preliminari sulla registrazione di token")](enhanced-user-notifications-images/token01.png#lightbox)

Usare il codice seguente per inizializzare la registrazione richiesta:

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

Il token che viene inviato all'app lato server dello sviluppatore deve essere incluso come parte del payload di notifica inviato dal server a APNs quando si invia una notifica remota:

[![](enhanced-user-notifications-images/token02.png "Token incluso come parte del payload di notifica")](enhanced-user-notifications-images/token02.png#lightbox)

Il token funge da chiave che unisce la notifica e l'app usata per aprire o rispondere alla notifica.

Per ulteriori informazioni, vedere la documentazione della [Guida alla programmazione delle notifiche remote e locali](https://developer.apple.com/documentation/usernotifications) di Apple.

## <a name="notification-delivery"></a>Recapito delle notifiche

Con l'app completamente registrata e le autorizzazioni necessarie richieste e concesse dall'utente, l'app è ora pronta per l'invio e la ricezione di notifiche. 

### <a name="providing-notification-content"></a>Fornire il contenuto delle notifiche

Una novità di iOS 10, tutte le notifiche contengono un **titolo** e un sottotitolo che saranno sempre visualizzati con il **corpo** del contenuto della notifica. Inoltre, è la possibilità di aggiungere **allegati multimediali** al contenuto della notifica.

Per creare il contenuto di una notifica locale, usare il codice seguente:

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

Per le notifiche remote, il processo è simile:

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

### <a name="scheduling-when-a-notification-is-sent"></a>Pianificazione quando viene inviata una notifica

Con il contenuto della notifica creata, l'app deve pianificare il momento in cui la notifica verrà presentata all'utente impostando un *trigger*. iOS 10 fornisce quattro tipi di trigger diversi:

- **Notifica push** : viene usato esclusivamente con le notifiche remote e viene attivato quando APNs Invia un pacchetto di notifica all'app in esecuzione nel dispositivo.
- **Intervallo di tempo** : consente di pianificare una notifica locale da un intervallo di tempo che inizia con ora e termina un punto successivo. Ad esempio, `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`.
- **Data calendario** : consente la pianificazione delle notifiche locali per una data e un'ora specifiche.
- **Basato sulla posizione** : consente di pianificare le notifiche locali quando il dispositivo iOS entra o esce da una posizione geografica specifica oppure si trova in una determinata vicinanza a qualsiasi Beacon Bluetooth.

Quando una notifica locale è pronta, l'app deve chiamare il `Add` metodo `UNUserNotificationCenter` dell'oggetto per pianificare la visualizzazione per l'utente. Per le notifiche remote, l'app sul lato server invia un payload di notifica al APNs, che quindi invia il pacchetto al dispositivo dell'utente.

Unendo tutti i componenti, una notifica locale di esempio può essere simile alla seguente:

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

## <a name="handling-foreground-app-notifications"></a>Gestione delle notifiche delle app in primo piano

Una novità di iOS 10, un'app può gestire le notifiche in modo diverso quando si trova in primo piano e viene attivata una notifica. Fornendo un `UNUserNotificationCenterDelegate` e implementando il `WillPresentNotification` metodo, l'app può assumere la responsabilità di visualizzare la notifica. Ad esempio:

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

Questo codice scrive semplicemente il contenuto di `UNNotification` nell'output dell'applicazione e chiede al sistema di visualizzare l'avviso standard per la notifica. 

Se l'app voleva visualizzare la notifica quando era in primo piano e non usa le impostazioni predefinite di sistema, passare `None` al gestore di completamento. Esempio:

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

Con questo codice, aprire il `AppDelegate.cs` file per la modifica e modificare il `FinishedLaunching` metodo in modo che abbia un aspetto simile al seguente:

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

Questo codice collega l'oggetto personalizzato `UNUserNotificationCenterDelegate` da sopra a quello corrente `UNUserNotificationCenter` , in modo che l'app possa gestire la notifica mentre è attiva e in primo piano.

## <a name="notification-management"></a>Gestione notifiche

Una novità di iOS 10, gestione notifiche fornisce accesso alle notifiche in sospeso e recapitate e consente di rimuovere, aggiornare o promuovere queste notifiche.

Una parte importante della gestione delle notifiche è l' _identificatore della richiesta_ assegnato alla notifica quando è stato creato e pianificato con il sistema. Per le notifiche remote, questo viene assegnato tramite il `apps-collapse-id` nuovo campo nell'intestazione della richiesta HTTP.

L'identificatore della richiesta viene usato per selezionare la notifica di cui l'app desidera eseguire la gestione delle notifiche.

### <a name="removing-notifications"></a>Rimozione di notifiche

Per rimuovere una notifica in sospeso dal sistema, usare il codice seguente:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

Per rimuovere una notifica già recapitata, usare il codice seguente:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>Aggiornamento di una notifica esistente

Per aggiornare una notifica esistente, è sufficiente creare una nuova notifica con i parametri desiderati modificati, ad esempio una nuova ora di attivazione, e aggiungerla al sistema con lo stesso identificatore di richiesta della notifica che deve essere modificata. Esempio:


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

Per le notifiche già recapitate, la notifica esistente verrà aggiornata e innalzata di livello all'inizio dell'elenco nelle schermate Home e di blocco e nel centro notifiche se è già stata letta dall'utente.

## <a name="working-with-notification-actions"></a>Utilizzo delle azioni di notifica

In iOS 10, le notifiche che vengono recapitate all'utente non sono statiche e forniscono diversi modi per interagire con l'utente (da azioni predefinite ad azioni personalizzate).

Sono disponibili tre tipi di azioni a cui un'app iOS può rispondere:

- **Azione predefinita** : quando l'utente tocca una notifica per aprire l'app e visualizzare i dettagli della notifica specificata.
- **Azioni personalizzate** : sono state aggiunte in iOS 8 e consentono all'utente di eseguire in modo rapido un'attività personalizzata direttamente dalla notifica senza dover avviare l'app. Possono essere presentati come un elenco di pulsanti con titoli personalizzabili o un campo di input di testo che può essere eseguito in background (in cui l'app ha un piccolo periodo di tempo per soddisfare la richiesta) o in primo piano (in cui l'app viene avviata in primo piano a fu lfill la richiesta). Le azioni personalizzate sono disponibili sia per iOS che per watchos.
- **Ignora azione** : questa azione viene inviata all'app quando l'utente ignora una determinata notifica.

### <a name="creating-custom-actions"></a>Creazione di azioni personalizzate

Per creare e registrare un'azione personalizzata con il sistema, usare il codice seguente:

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

Quando si crea un `UNNotificationAction`nuovo oggetto, viene assegnato un ID univoco e il titolo che verrà visualizzato sul pulsante. Per impostazione predefinita, l'azione verrà creata come un'azione in background, tuttavia è possibile specificare le opzioni per modificare il comportamento dell'azione, ad esempio impostando come un'azione in primo piano.

Ognuna delle azioni create deve essere associata a una categoria. Quando si crea un `UNNotificationCategory`nuovo oggetto, viene assegnato un ID univoco, un elenco di azioni che è possibile eseguire, un elenco di ID finalità per fornire ulteriori informazioni sullo scopo delle azioni nella categoria e alcune opzioni per controllare il comportamento della categoria.

Infine, tutte le categorie vengono registrate con il sistema utilizzando il `SetNotificationCategories` metodo.

### <a name="presenting-custom-actions"></a>Presentazione di azioni personalizzate

Una volta che un set di azioni e categorie personalizzate è stato creato e registrato con il sistema, può essere presentato da notifiche locali o remote.

Per la notifica remota, impostare `category` un nel payload di notifica remoto corrispondente a una delle categorie create in precedenza. Ad esempio:

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

Per le notifiche locali, impostare `CategoryIdentifier` la proprietà `UNMutableNotificationContent` dell'oggetto. Ad esempio:

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

Anche in questo caso, l'ID deve corrispondere a una delle categorie create in precedenza.

### <a name="handling-dismiss-actions"></a>Gestione delle azioni ignorate

Come indicato in precedenza, un'azione di disattivazione può essere inviata all'app quando l'utente ignora una notifica. Poiché non si tratta di un'azione standard, sarà necessario impostare un'opzione quando la categoria viene creata. Ad esempio:

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Gestione delle risposte alle azioni

Quando l'utente interagisce con le azioni e le categorie personalizzate create in precedenza, l'app deve soddisfare l'attività richiesta. Questa operazione viene eseguita fornendo un `UNUserNotificationCenterDelegate` oggetto e implementando il `UserNotificationCenter` metodo. Ad esempio:

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

La `UNNotificationResponse` classe passata ha una `ActionIdentifier` proprietà che può essere l'azione predefinita o l'azione Ignora. Usare `response.Notification.Request.Identifier` per verificare le azioni personalizzate.

La `UserText` proprietà include il valore di qualsiasi input di testo utente. La `Notification` proprietà contiene la notifica di origine che include la richiesta con il trigger e il contenuto della notifica. L'app può decidere se si tratta di una notifica locale o remota basata sul tipo di trigger.

> [!NOTE]
> iOS 12 rende possibile a un'interfaccia utente di notifica personalizzata di modificare i pulsanti di azione in fase di esecuzione. Per ulteriori informazioni, vedere la documentazione relativa ai [pulsanti di azione di notifica dinamica](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md) .

## <a name="working-with-service-extensions"></a>Utilizzo delle estensioni del servizio

Quando si utilizzano le notifiche remote, le _estensioni del servizio_ consentono di abilitare la crittografia end-to-end all'interno del payload di notifica. Le estensioni del servizio sono un'estensione dell'interfaccia non utente (disponibile in iOS 10) che viene eseguita in background con lo scopo principale di aumentare o sostituire il contenuto visibile di una notifica prima che venga presentata all'utente. 

[![](enhanced-user-notifications-images/extension01.png "Panoramica dell'estensione del servizio")](enhanced-user-notifications-images/extension01.png#lightbox)

Le estensioni del servizio sono progettate per essere eseguite rapidamente e vengono fornite solo una breve quantità di tempo per l'esecuzione da parte del sistema. Nel caso in cui l'estensione del servizio non riesca a completare l'attività nell'intervallo di tempo assegnato, verrà chiamato un metodo di fallback. Se il fallback ha esito negativo, il contenuto originale della notifica verrà visualizzato all'utente.

Alcuni possibili utilizzi delle estensioni del servizio includono:

- Fornire la crittografia end-to-end del contenuto della notifica remota.
- Aggiunta di allegati alle notifiche remote per arricchirli.

### <a name="implementing-a-service-extension"></a>Implementazione di un'estensione del servizio

Per implementare un'estensione del servizio in un'app Novell. iOS, seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic con il pulsante destro del mouse sul nome della soluzione nel **riquadro della soluzione** e scegliere **Aggiungi** > **Aggiungi nuovo progetto**.
3. Selezionare **estensioni iOS** >  notifiche del servizio e fare clic sul pulsante Avanti: >  

    [![](enhanced-user-notifications-images/extension02.png "Selezionare le estensioni del servizio di notifica")](enhanced-user-notifications-images/extension02.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic sul pulsante **Avanti** : 

    [![](enhanced-user-notifications-images/extension03.png "Immettere un nome per l'estensione")](enhanced-user-notifications-images/extension03.png#lightbox)
5. Modificare il **nome del progetto** e/o il **nome della soluzione** , se necessario, e fare clic sul pulsante **Crea** : 

    [![](enhanced-user-notifications-images/extension04.png "Modificare il nome del progetto e/o il nome della soluzione")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire la soluzione dell'app in Visual Studio.
2. Fare clic con il pulsante destro del mouse sul nome della soluzione nel **Esplora soluzioni** e scegliere **Aggiungi > nuovo progetto.**
3. Selezionare **Visual C# > estensioni iOS > estensione servizio di notifica**:

    [![](enhanced-user-notifications-images/extension01.w157-sml.png "Selezionare le estensioni del servizio di notifica")](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic sul pulsante **OK** .

-----

> [!IMPORTANT]
> L'identificatore del bundle per l'estensione del servizio deve corrispondere all'identificatore del bundle dell'app `.appnameserviceextension` principale con accodato alla fine. Se ad esempio l'app principale include un identificatore del `com.xamarin.monkeynotify`bundle, l'estensione del servizio deve avere un identificatore di `com.xamarin.monkeynotify.monkeynotifyserviceextension`bundle. Questa impostazione deve essere impostata automaticamente quando l'estensione viene aggiunta alla soluzione. 

Nell'estensione Notification Service è presente una classe principale che deve essere modificata per fornire la funzionalità necessaria. Ad esempio:

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

Al primo metodo, `DidReceiveNotificationRequest`, verrà passato l'identificatore di notifica e il contenuto della notifica tramite l' `request` oggetto. Il passato `contentHandler` deve essere chiamato per presentare la notifica all'utente.

Il secondo metodo, `TimeWillExpire`, verrà chiamato immediatamente prima che sta per essere esaurito per l'estensione del servizio per l'elaborazione della richiesta. Se l'estensione del servizio non riesce a `contentHandler` chiamare il nell'intervallo di tempo assegnato, il contenuto originale verrà visualizzato all'utente.

### <a name="triggering-a-service-extension"></a>Attivazione di un'estensione del servizio

Se un'estensione del servizio è stata creata e distribuita con l'app, può essere attivata modificando il payload di notifica remoto inviato al dispositivo. Ad esempio:

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

La nuova `mutable-content` chiave specifica che è necessario avviare l'estensione del servizio per aggiornare il contenuto della notifica remota. La `encrypted-content` chiave include i dati crittografati che l'estensione del servizio è in grado di decrittografare prima di presentare all'utente.

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

Questo codice `encrypted-content` decrittografa il contenuto crittografato dalla chiave, ne crea uno nuovo `UNMutableNotificationContent`, imposta la `Body` proprietà sul contenuto decrittografato e USA `contentHandler` per presentare la notifica all'utente.

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato tutti i modi in cui la notifica degli utenti è stata migliorata da iOS 10. Viene presentato il nuovo Framework di notifica utente e viene illustrato come usarlo in un'app Novell. iOS o in un'estensione di app.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Guida di riferimento a UserNotifications Framework](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guida alla programmazione di notifiche locali e remote](https://developer.apple.com/documentation/usernotifications)
