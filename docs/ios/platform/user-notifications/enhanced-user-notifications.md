---
title: Notifiche utente migliorate in xamarin. IOS
description: Questo articolo descrive il framework per le notifiche utente introdotto in iOS 10. Illustra le notifiche locali, le notifiche remote, gestione delle notifiche, le azioni di notifica e altro ancora.
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 0f77f9014cf7bfad510927f0f12a3e70b387036f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424380"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>Notifiche utente migliorate in xamarin. IOS

Novità di iOS 10, la notifica all'utente consente a framework per il recapito e la gestione delle notifiche locali e remote. Uso di questo framework, un'app o l'estensione dell'App può pianificare il recapito di notifiche locali specificando un set di condizioni, ad esempio posizione o l'ora del giorno.

## <a name="about-user-notifications"></a>Informazioni sulle notifiche utente

Come indicato in precedenza, il nuovo framework di notifica all'utente consente il recapito e la gestione delle notifiche locali e remote. Uso di questo framework, un'app o l'estensione dell'App può pianificare il recapito di notifiche locali specificando un set di condizioni, ad esempio posizione o l'ora del giorno.

Inoltre, l'app o l'estensione può ricevere (e potenzialmente modificare) le notifiche locali e remote quando vengono recapitate al dispositivo iOS dell'utente.

Il nuovo framework di interfaccia utente di notifica utente consente a un'app o l'estensione dell'App per personalizzare l'aspetto delle notifiche locali e remote quando sono presentati all'utente.

Questo framework offre le seguenti modalità che un'app può recapitare le notifiche a un utente:

- **Avvisi Visual** , in cui la notifica esegue il roll verso il basso dalla parte superiore dello schermo sotto forma di banner.
- **File audio e alle vibrazioni** -può essere associato a una notifica.
- **Badge sull'icona di app** - dove l'icona dell'app Visualizza una notifica che mostra che il nuovo contenuto è disponibile, ad esempio il numero di messaggi di posta elettronica non letta.

Inoltre, a seconda del contesto corrente dell'utente, esistono diversi modi in cui verrà visualizzata una notifica:

- Se il dispositivo è sbloccato, la notifica viene continuata verso il basso dalla parte superiore dello schermo sotto forma di banner.
- Se il dispositivo è bloccato, la notifica verrà visualizzata nella schermata di blocco dell'utente.
- Se l'utente ha perso una notifica, possono aprire il centro notifiche e visualizzare le notifiche di attesa disponibili, non esiste.

Un'app xamarin. IOS include due tipi di notifiche utente che è in grado di inviare:

- **Le notifiche locali** -questi vengono inviati dall'App installate in locale nel dispositivo dell'utente.
- **Notifiche remote** -vengono inviati da un computer remoto server e presentata all'utente o attivano un aggiornamento in background di contenuto dell'app.

### <a name="about-local-notifications"></a>Informazioni sulle notifiche locali

Le notifiche locali che può inviare un'app per iOS sono le funzionalità e gli attributi seguenti:

- Vengono inviati dall'App che sono locali nel dispositivo dell'utente. 
- Sono possono essere configurati per usare tempo o percorso basato su trigger. 
- L'app consente di pianificare la notifica con il dispositivo dell'utente e viene visualizzato quando viene soddisfatta la condizione del trigger.
- Quando l'utente interagisce con una notifica, l'applicazione riceverà un callback.

Alcuni esempi delle notifiche locali:

- Avvisi del calendario
- Avvisi di promemoria
- Trigger con riconoscimento percorso

Per altre informazioni, vedere di Apple [locale e Guida alla programmazione di notifiche Remote](https://developer.apple.com/documentation/usernotifications) documentazione.

### <a name="about-remote-notifications"></a>Informazioni sulle notifiche Remote

Le notifiche Remote che può inviare un'app per iOS sono le funzionalità e gli attributi seguenti:

- L'app ha un componente sul lato server con cui comunica.
- Consente la trasmissione di un recapito sforzo di notifiche Remote per il dispositivo dell'utente dai server basati sul cloud per gli sviluppatori di Apple Push Notification Service (APNs).
- Quando l'app riceve la notifica remota che verrà visualizzato all'utente.
- Quando l'utente interagisce con la notifica, l'applicazione riceverà un callback.

Alcuni esempi di notifiche Remote:

- Avvisi sulle notizie
- Aggiornamenti di sport
- Messaggi di messaggistica immediati

Sono disponibili due tipi di notifiche Remote a un'app iOS:

- **Utente con connessione** -i requisiti vengono visualizzati all'utente nel dispositivo.
- **Aggiornamenti invisibile all'utente** -forniscono un meccanismo per aggiornare il contenuto di un'app per iOS in background. Quando viene ricevuto un aggiornamento invisibile all'utente, l'app può contattare per rimuovere i server a discesa del contenuto più recente.

Per altre informazioni, vedere di Apple [locale e Guida alla programmazione di notifiche Remote](https://developer.apple.com/documentation/usernotifications) documentazione.

### <a name="about-the-existing-notifications-api"></a>Sulle notifiche API esistente

Prima di iOS 10, si userà un'app per iOS `UIApplication` per registrare una notifica con il sistema e per pianificare la modalità di notifica dovrebbe essere attivata (in base al tempo o percorso).

Esistono diversi problema che lo sviluppatore può verificarsi quando si lavora con l'API di notifica esistente:

- Si sono verificati diversi callback necessari per locale o remoto notifiche che potrebbe causare la duplicazione del codice.
- L'app ha un controllo limitato della notifica dopo che è stato pianificato con il sistema.
- Si sono verificati diversi livelli di supporto in tutte le piattaforme esistenti di Apple.

### <a name="about-the-new-user-notification-framework"></a>Sul nuovo Framework di notifica utente

Con iOS 10, Apple ha introdotto il nuovo framework di notifica all'utente, che sostituisce l'oggetto esistente `UIApplication` metodo indicato in precedenza.

Il framework di notifica all'utente offre quanto segue:

- Un'API familiare che include la parità di funzionalità con i metodi precedenti, rendendo più semplice al porting del codice dal framework esistenti.
- Include un'ampia gamma di opzioni relative al contenuto che consente le notifiche più complete da inviare all'utente.
- Locali e le notifiche Remote possono essere gestite mediante lo stesso codice e i callback.
- Semplifica il processo di gestione di callback che vengono inviati a un'app quando l'utente interagisce con una notifica.
- Gestione migliorata delle notifiche in sospeso sia recapitate inclusa la possibilità di rimuovere o aggiornare le notifiche.
- Aggiunge la possibilità di eseguire operazioni in-app presentazione delle notifiche.
- Aggiunge la possibilità di pianificare e gestire le notifiche da entro le estensioni dell'App.
- Aggiunge nuovo punto di estensione per le notifiche stesse. 

Il nuovo framework di notifica all'utente fornisce una notifica unified API tra il più delle piattaforme che Apple supporta tra cui: 

- **iOS** -supporto per gestire e pianificare le notifiche completo.
- **tvOS** -consente di eseguire badge le icone dell'app per le notifiche locali e remote.
- **watchOS** : aggiunge la possibilità di inoltrare le notifiche dal dispositivo iOS associato dell'utente a loro Apple Watch e offre la possibilità di eseguire le notifiche locali direttamente nel stesso Apple watch App watch.

Per altre informazioni, vedere di Apple [riferimento a Framework di UserNotifications](https://developer.apple.com/reference/usernotifications) e [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) documentazione.

## <a name="preparing-for-notification-delivery"></a>Preparazione per il recapito delle notifiche

Prima di iOS app può inviare notifiche all'utente che l'app deve essere registrato con il sistema e, trattandosi di una notifica di un'interruzione all'utente, un'app deve richiedere in modo esplicito l'autorizzazione prima di inviarli.

Esistono tre diversi livelli di richieste di notifica che l'utente può approvare per un'app:

- Banner viene visualizzato.
- Gli avvisi di suoni.
- Badge sull'icona dell'app.

Inoltre, questi livelli di approvazione devono essere richiesto e impostare per le notifiche locali e remote.

Deve essere richieste l'autorizzazione di notifica non appena viene avviata l'app aggiungendo il codice seguente per il `FinishedLaunching` metodo per il `AppDelegate` e impostando il tipo di notifica desiderato (`UNAuthorizationOptions`):

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

Inoltre, un utente può sempre modificare i privilegi di notifica per un'app in qualsiasi momento tramite il **impostazioni** app sul dispositivo. L'app controllerebbe per i privilegi dell'utente notifica richiesta prima di presentare una notifica tramite il codice seguente:

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
}); 
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Configurazione dell'ambiente notifiche Remote

Nuovo ai dispositivi iOS 10, lo sviluppatore deve informare il sistema operativo qual è l'ambiente Push Notification sono in esecuzione in come lo sviluppo o produzione. Non viene fornito queste informazioni può causare l'app verrà rifiutato quando inviato per l'App Store iTunes con una notifica simile al seguente:

> Diritto di notifica Push mancano - l'app include un'API per il servizio di notifica Push di Apple, ma il `aps-environment` entitlement è mancanti dalla firma dell'app.

Per fornire i diritti necessari, procedere come segue:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare doppio clic il `Entitlements.plist` del file nei **riquadro della soluzione** per aprirlo e modificarlo.
2. Passare al **origine** Vista: 

    [![](enhanced-user-notifications-images/setup01.png "La visualizzazione origine")](enhanced-user-notifications-images/setup01.png#lightbox)
3. Scegliere il **+** pulsante per aggiungere una nuova chiave.
4. Immettere `aps-environment` per il **proprietà**, lasciare il **tipo** come `String` e immettere uno `development` oppure `production` per il **valore**: 

    [![](enhanced-user-notifications-images/setup02.png "La proprietà aps-environment")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Salvare le modifiche apportate al file.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare doppio clic il `Entitlements.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
3. Scegliere il **+** pulsante per aggiungere una nuova chiave.
4. Immettere `aps-environment` per il **proprietà**, lasciare il **tipo** come `String` e immettere uno `development` oppure `production` per il **valore**: 

    [![](enhanced-user-notifications-images/setup02w.png "La proprietà aps-environment")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Salvare le modifiche apportate al file.

-----

### <a name="registering-for-remote-notifications"></a>La registrazione per le notifiche Remote

Se l'app verrà l'invio e ricezione di notifiche Remote, sarà comunque necessario eseguire _Token di registrazione_ usando l'oggetto esistente `UIApplication` API. Questa registrazione è necessario il dispositivo deve avere un accesso alla connessione di rete in tempo reale APNs, che genera il token necessario che verrà inviato all'app. L'app deve quindi inoltrare il token dell'app lato server per gli sviluppatori per la registrazione per notifiche remote:

[![](enhanced-user-notifications-images/token01.png "Panoramica di registrazione del token")](enhanced-user-notifications-images/token01.png#lightbox)

Usare il codice seguente per inizializzare la registrazione richiesti:

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

Il token che venga inviato all'app sul lato server per gli sviluppatori dovrà essere incluso come parte del Payload di notifica che get inviati dal server al servizio APN quando si invia una notifica remota:

[![](enhanced-user-notifications-images/token02.png "Il token incluso come parte del Payload della notifica")](enhanced-user-notifications-images/token02.png#lightbox)

Il token funge da chiave che collega la notifica e l'app usata per aprire o rispondere alla notifica.

Per altre informazioni, vedere di Apple [locale e Guida alla programmazione di notifiche Remote](https://developer.apple.com/documentation/usernotifications) documentazione.

## <a name="notification-delivery"></a>Recapito delle notifiche

Con l'app è completamente registrato e le autorizzazioni necessarie richiedano dal e concesso dall'utente, l'app è ora pronto per inviare e ricevere le notifiche. 

### <a name="providing-notification-content"></a>Specificare il contenuto di notifica

Nuovo ai dispositivi iOS 10, tutte le notifiche contengono entrambe una **titolo** e **sottotitolo** che verrà sempre visualizzata con il **corpo** del contenuto della notifica. Nuova, è anche la possibilità di aggiungere **gli allegati multimediali** per il contenuto della notifica.

Per creare il contenuto di una notifica locale, usare il codice seguente:

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

Per le notifiche Remote, il processo è simile:

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

### <a name="scheduling-when-a-notification-is-sent"></a>Quando una notifica di pianificazione viene inviata

Con il contenuto della notifica di creazione, l'app deve pianificare quando verrà visualizzata la notifica all'utente impostando un *Trigger*. iOS 10 offre i quattro diversi tipi di Trigger:

- **Notifica push** : viene utilizzata esclusivamente con le notifiche Remote e viene attivato quando creare un pacchetto servizio APN invia una notifica all'app in esecuzione nel dispositivo.
- **Intervallo di tempo** -consente a una notifica locale di essere pianificati dall'ora di un'intervallo per iniziare adesso e termina un certo punto futuro. Ad esempio, `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`.
- **Data di calendario** -consente le notifiche locali per la pianificazione di una data e ora specifiche.
- **Basato su percorso** -consente le notifiche locali di essere pianificati quando il dispositivo iOS è immettendo o uscire da una posizione geografia specifica o in una determinato prossimità per qualsiasi beacon Bluetooth.

Quando una notifica locale è pronta, l'app deve chiamare il `Add` metodo di `UNUserNotificationCenter` oggetto per pianificare la visualizzazione all'utente. Per le notifiche Remote, l'app sul lato server invia un Payload di notifica per il servizio APNs, che quindi invia il pacchetto al dispositivo dell'utente.

Un esempio di notifica locale innovativo, riunisce tutte le parti, potrebbe essere analogo:

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

## <a name="handling-foreground-app-notifications"></a>Gestione delle notifiche delle App in primo piano

Nuovo ai dispositivi iOS 10, un'app per le possa gestire le notifiche in modo diverso quando è in primo piano e viene attivata una notifica. Fornendo una `UNUserNotificationCenterDelegate` e l'implementazione di `WillPresentNotification` metodo, l'app può assumersi la responsabilità per la visualizzazione della notifica. Ad esempio:

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

Questo codice viene semplicemente scritto il contenuto del `UNNotification` per l'Output dell'applicazione e chiede al sistema per visualizzare l'avviso standard per la notifica. 

Se l'app desidera visualizzare la notifica in sé quando era in primo piano e non utilizza le impostazioni predefinite del sistema, passare `None` al gestore di completamento. Esempio:

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

Con questo codice, aprire il `AppDelegate.cs` per la modifica di file e modificare il `FinishedLaunching` metodo aspetto analogo al seguente:

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

Questo codice riguarda il collegamento personalizzato `UNUserNotificationCenterDelegate` precedente all'attuale `UNUserNotificationCenter` in modo che l'app può gestire la notifica mentre è attiva e in primo piano.

## <a name="notification-management"></a>Gestione delle notifiche

Nuovo ai dispositivi iOS 10, gestione di notifica fornisce l'accesso alle notifiche in sospeso sia recapitate e aggiunge la possibilità di rimuovere, aggiornare o alzare di livello queste notifiche.

Una parte importante della gestione delle notifiche è il _identificatore richiesta_ che è stato assegnato alla notifica quando è stata creata e pianificato con il sistema. Per le notifiche Remote, viene assegnato tramite il nuovo `apps-collapse-id` campo nell'intestazione della richiesta HTTP.

Consente di selezionare la notifica che l'app desidera eseguire la gestione di notifica sull'identificatore della richiesta.

### <a name="removing-notifications"></a>Rimozione delle notifiche

Per rimuovere una notifica in sospeso dal sistema, usare il codice seguente:

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

Per aggiornare una notifica esistente, è sufficiente creare una nuova notifica con i parametri desiderati modificati (ad esempio, una nuova ora di trigger) e aggiungerlo al sistema con lo stesso identificatore richiesta la notifica che deve essere modificato. Esempio:


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

Per le notifiche già recapitate, la notifica esistente otterrà aggiornata e innalzate di livello nella parte superiore dell'elenco nelle schermate Home e blocco e nel centro notifiche se è già stata letta dall'utente.

## <a name="working-with-notification-actions"></a>Uso di azioni di notifica

In iOS 10, le notifiche vengono recapitate per l'utente non sono statiche e forniscono numerose funzionalità che l'utente può interagire con essi (da incorporato per azioni personalizzate).

Esistono tre tipi di azioni in grado di rispondere a un'app per iOS:

- **Azione predefinita** -si tratta di quando l'utente tocca una notifica per aprire l'app e visualizzare i dettagli di notifica specifica.
- **Azioni personalizzate** -questi sono stati aggiunti in iOS 8 e rappresentano un modo rapido per l'utente eseguire un'attività personalizzata direttamente dalla notifica senza la necessità di avviare l'app. Può essere presentati come un elenco di pulsanti con titoli personalizzabili o un campo di immissione testo che può essere eseguito in Background (in cui l'app ha una piccola quantità di tempo per soddisfare la richiesta) o in primo piano (in cui viene avviata l'app in primo piano per /fu lfill richiesta). Azioni personalizzate sono disponibili in iOS e watchOS.
- **Elimina azione** -questa azione viene inviato all'app quando l'utente chiude una notifica specifica.

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

Quando si crea un nuovo `UNNotificationAction`, viene assegnato un ID univoco e il titolo che verrà visualizzato sul pulsante. Per impostazione predefinita, l'azione verrà creato come azione in Background, tuttavia, è possibile specificare le opzioni per modificare il comportamento dell'azione (ad esempio impostandolo come un'azione di primo piano).

Ognuna delle azioni create devono essere associati a una categoria. Quando si crea un nuovo `UNNotificationCategory`, viene assegnato un ID univoco, un elenco di azioni che è possibile eseguire un elenco di ID con finalità di fornire ulteriori informazioni sull'intento delle azioni nella categoria e alcune opzioni per controllare il comportamento della categoria.

Infine, tutte le categorie registrate con il sistema utilizzando la `SetNotificationCategories` (metodo).

### <a name="presenting-custom-actions"></a>Presentazione delle azioni personalizzate

Una volta che un set di azioni personalizzate e le categorie sono creati e registrati con il sistema, può essere presentati da locale o le notifiche Remote.

Per ricevere una notifica remota, impostare un `category` nel Payload della notifica remota che corrisponde a una delle categorie create in precedenza. Ad esempio:

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

Per le notifiche locali, impostare il `CategoryIdentifier` proprietà del `UNMutableNotificationContent` oggetto. Ad esempio:

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

Anche in questo caso, questo ID deve corrispondere a una delle categorie che è stato creato in precedenza.

### <a name="handling-dismiss-actions"></a>Azioni di ignorare la gestione

Come indicato in precedenza, un'azione di ignorare possono essere inviata all'app quando l'utente chiude una notifica. Poiché non si tratta di un'azione standard, un'opzione dovrà essere impostato quando viene creata la categoria. Ad esempio:

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Risposte alle azioni di gestione

Quando l'utente interagisce con le azioni personalizzate e le categorie che sono state create in precedenza, l'app deve soddisfare l'attività richiesta. Ciò avviene specificando una `UNUserNotificationCenterDelegate` e l'implementazione di `UserNotificationCenter` (metodo). Ad esempio:

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

Il valore passato in `UNNotificationResponse` classe ha un `ActionIdentifier` proprietà che può essere l'azione predefinita o l'azione di ignorare. Usare `response.Notification.Request.Identifier` da verificare per tutte le azioni personalizzate.

Il `UserText` proprietà contiene il valore di input di testo qualsiasi utente. Il `Notification` proprietà contiene la notifica di origine che include la richiesta con il Trigger e notifica contenuto. L'app può decidere se fosse una variabile locale o remoto notifica in base al tipo di trigger.

> [!NOTE]
> iOS 12 rende possibile per una notifica di modifica relativi pulsanti di azione in fase di esecuzione personalizzata dell'interfaccia utente. Per altre informazioni, esaminare i [pulsanti di azione di notifica dinamica](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md) documentazione.

## <a name="working-with-service-extensions"></a>Utilizzo di estensioni del servizio

Quando si lavora con le notifiche Remote _estensioni servizio_ offrono un modo per abilitare la crittografia end-to-end all'interno del Payload di notifica. Le estensioni del servizio sono un'estensione dell'interfaccia utente (disponibile in iOS 10) eseguiti in background con lo scopo principale di aumento o sostituendo il contenuto visibile di una notifica prima di essere presentati all'utente. 

[![](enhanced-user-notifications-images/extension01.png "Panoramica di estensione del servizio")](enhanced-user-notifications-images/extension01.png#lightbox)

Le estensioni del servizio devono essere eseguiti rapidamente e vengono fornite solo un breve periodo di tempo per l'esecuzione dal sistema. Nel caso in cui l'estensione del servizio non riesce a completare l'attività nel periodo di tempo stabilito, verrà chiamato un metodo di fallback. Se il fallback non riesce, il contenuto di notifiche originale verrà visualizzato all'utente.

Alcuni possibili usi delle estensioni del servizio includono:

- Fornire la crittografia end-to-end del contenuto della notifica remota.
- Aggiunta di allegati a cui vengono le notifiche Remote.

### <a name="implementing-a-service-extension"></a>Implementazione di un'estensione del servizio

Per implementare un'estensione del servizio in un'app xamarin. IOS, effettuare le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic sul nome della soluzione nel **riquadro della soluzione** e selezionare **Add** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **estensioni** > **le estensioni del servizio di notifica** e fare clic su di **successivo** pulsante: 

    [![](enhanced-user-notifications-images/extension02.png "Selezionare le estensioni del servizio di notifica")](enhanced-user-notifications-images/extension02.png#lightbox)
4. Immettere un **Name** per l'estensione e fare clic sui **successivo** pulsante: 

    [![](enhanced-user-notifications-images/extension03.png "Immettere un nome per l'estensione")](enhanced-user-notifications-images/extension03.png#lightbox)
5. Modificare il **nome progetto** e/o **Nome soluzione** se necessario e fare clic sui **crea** pulsante: 

    [![](enhanced-user-notifications-images/extension04.png "Modificare il nome del progetto e/o il nome della soluzione")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire la soluzione dell'app in Visual Studio.
2. Fare clic sul nome della soluzione nel **Esplora soluzioni** e selezionare **Aggiungi > Nuovo progetto...** .
3. Selezionare **Visual C# > estensioni iOS > estensione per servizio notifiche**:

    [![](enhanced-user-notifications-images/extension01.w157-sml.png "Selezionare le estensioni del servizio di notifica")](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. Immettere un **Name** per l'estensione e fare clic sui **OK** pulsante.

-----

> [!IMPORTANT]
> L'identificatore del Bundle per l'estensione del servizio deve corrispondere all'identificatore del Bundle dell'app principale con `.appnameserviceextension` aggiunto alla fine. Ad esempio, se l'app principale ha un identificatore del Bundle del `com.xamarin.monkeynotify`, l'estensione del servizio deve avere un identificatore del Bundle di `com.xamarin.monkeynotify.monkeynotifyserviceextension`. Questo deve essere impostato automaticamente quando l'estensione viene aggiunta alla soluzione. 

È disponibile una classe principale nell'estensione del servizio di notifica che dovrà essere modificato per fornire le funzionalità richieste. Ad esempio:

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

Il primo metodo, `DidReceiveNotificationRequest`, verrà passato l'identificatore di notifica, nonché il contenuto di notifiche tramite il `request` oggetto. Il valore passato in `contentHandler` dovrà essere chiamato per presentare la notifica all'utente.

Il secondo metodo, `TimeWillExpire`, verrà chiamato appena prima che ora il limite massimo consentito per l'estensione del servizio elaborare la richiesta. Se l'estensione del servizio non riesce a chiamare il `contentHandler` nel periodo di tempo stabilito, il contenuto originale verrà visualizzato all'utente.

### <a name="triggering-a-service-extension"></a>Attivazione di un'estensione del servizio

Con un'estensione del servizio creato e recapitati con l'app può essere attivata modificando il Payload della notifica remota inviata al dispositivo. Ad esempio:

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

Il nuovo `mutable-content` chiave specifica che l'estensione del servizio dovrà essere avviata per aggiornare il contenuto della notifica remota. Il `encrypted-content` chiave contiene i dati crittografati in grado di decrittografare l'estensione del servizio prima di presentare all'utente.

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

Questo codice consente di decrittografare il contenuto crittografato dal `encrypted-content` della chiave, crea un nuovo `UNMutableNotificationContent`, imposta le `Body` viene impostata sul contenuto decrittografato e Usa il `contentHandler` per presentare la notifica all'utente.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato tutte le modalità che notifica gli utenti sono stata migliorata per iOS 10. Presentato il nuovo framework di notifica all'utente e come usarlo in un'app xamarin. IOS o un'estensione dell'App.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Riferimento a Framework di UserNotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guida alla programmazione di notifiche locali e remoti](https://developer.apple.com/documentation/usernotifications)
