---
title: Notifiche
ms.topic: article
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 600d651eae1f470f17511afaf9bc86c80ffc1a75
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="notifications"></a>Notifiche

Controllare l'App possono ricevere notifiche se l'app iOS contenitore supportarle. È presente la gestione di notifica predefinita in modo da non *necessario* per aggiungere il supporto di notifica aggiuntive descritto di seguito, tuttavia se si desidera personalizzare il comportamento di notifica e la lettura in aspetto.

Consultare la [iOS notifiche](~/ios/platform/user-notifications/deprecated/index.md) doc per ulteriori informazioni sull'aggiunta di supporto di notifica all'app di iOS nella soluzione.

## <a name="creating-notification-controllers"></a>Creazione di controller di notifica

Nello storyboard i controller di notifiche hanno un tipo speciale di definire l'attivazione di essi. Quando si trascina un nuovo **Controller di interfaccia di notifica** in uno storyboard automaticamente avrà un segue collegato:

![](notifications-images/notification-storyboard1.png "Un nuovo Controller di interfaccia di notifica con un segue collegato")

Definire i la notifica quando è selezionata è possibile modificarne le proprietà:

![](notifications-images/notification-storyboard2.png "La notifica definire selezionato")

Dopo aver personalizzato il controller di calcolo potrebbe essere simile a questo esempio dal WatchKitCatalog:

![](notifications-images/notifications-segue.png "Le proprietà di notifica")


Esistono due tipi di notifica:

- **La funzione di ricerca breve** -non scorrevole visualizzazione statica definita dal sistema.

- **Prolungata aspetto** - scorrevole, Vista personalizzabile definito dall'utente. È possibile specificare una versione più semplice, statica e una versione dinamica più complessa.

### <a name="short-look-notification-controller"></a>La funzione di ricerca breve notifica Controller

L'interfaccia utente breve la ricerca è costituita dall'icona di app, il nome dell'app e la stringa del titolo di notifica.

Se l'utente non ignora la notifica, il sistema passerà automaticamente a una notifica prolungata aspetto che fornisce ulteriori informazioni.


### <a name="long-look-notification-controller"></a>Notifica prolungata aspetto Controller

Il sistema operativo determina se visualizzare la visualizzazione statica o dinamica in base a una serie di fattori. È necessario fornire un'interfaccia statica e può facoltativamente includere anche un'interfaccia dinamica per le notifiche.

#### <a name="static"></a>Static

La visualizzazione statica deve essere semplice e rapido da visualizzare.

![](notifications-images/notification-static.png "La visualizzazione statica")

#### <a name="dynamic"></a>Dynamic

La visualizzazione dinamica può visualizzare più dati e fornire altre interattività.

![](notifications-images/notification-dynamic.png "La visualizzazione dinamica")


## <a name="generating-notifications"></a>Generazione di notifiche

Le notifiche possono provenire da un server remoto ([Apple Push Notification Service](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), o APNS) o possono essere generati in locale nell'app iOS.

Fare riferimento al [iOS procedura dettagliata notifiche](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) per un esempio di come generare notifiche locale e [WatchNotifications esempio](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/) per un esempio funzionante.

Notifiche locale devono disporre di `AlertTitle` impostato da visualizzare nella finestra di Apple Watch - il `AlertTitle` stringa viene visualizzata nell'interfaccia breve aspetto. Sia il `AlertTitle` e `AlertBody` vengono visualizzati nell'elenco; le notifiche e `AlertBody` viene visualizzato nell'interfaccia prolungata aspetto.

Questa schermata è riportato il `AlertTitle` visualizzate nell'elenco di notifiche e `AlertBody` visualizzate nell'interfaccia prolungata aspetto (utilizzando il [codice di esempio](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)):

![](notifications-images/watch-notificationslist-sml.png "Questa schermata è riportato il AlertTitle visualizzate nell'elenco delle notifiche di") ![ ] (notifications-images/watch-notificationcontroller-sml.png "AlertBody il visualizzate nell'interfaccia prolungata aspetto")

## <a name="testing-notifications"></a>Test di notifiche

Le notifiche (locali e remote) possono essere correttamente verificate solo in un dispositivo, ma può essere simulati mediante un **JSON** file nel simulatore iOS.

### <a name="testing-on-apple-watch"></a>Test su Apple Watch

Durante il test di notifiche in un Apple Watch, tenere presente che [documentazione di Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) indica le operazioni seguenti:

> Quando una delle notifiche dell'app locale o remoto arriva in un iPhone dell'utente, iOS determina se visualizzare la notifica su iPhone o l'Apple Watch.

Questo è alluding al fatto che iOS decide se viene visualizzata una notifica su iPhone o l'espressione di controllo. Se l'iPhone associata è attiva quando viene ricevuta una notifica, la notifica è probabile che vengano visualizzati su iPhone e *non* indirizzato per l'espressione di controllo.

Per garantire che la notifica viene visualizzata nell'orologio, disattivare la schermata di iPhone (premendo il pulsante di alimentazione, una volta) o si lascia passare alla modalità sospensione. Se l'orologio associato è compreso nell'intervallo, offre una potenza e attrezzatura su in alcun modo, la notifica verrà indirizzato sono sul e le espressioni di controllo (accompagnato da un tipo complesso).

### <a name="testing-on-the-ios-simulator"></a>Il test nel simulatore iOS

Si *deve* forniscono un payload JSON test durante il test in modalità di notifica nel simulatore iOS. Impostare il percorso di **gli argomenti di esecuzione personalizzato** finestra in Visual Studio per Mac.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Visual Studio per Mac visualizzerà le opzioni aggiuntive quando un'estensione di espressioni di controllo è impostata come il **progetto di avvio**.
Fare clic sul progetto di estensione delle espressioni di controllo e scegliere **eseguire con > parametri personalizzato...** :
    
[![](notifications-images/runwith-customparams-sml.png "In esecuzione con le proprietà personalizzate")](notifications-images/runwith-customparams.png#lightbox)
    
Verrà visualizzata la **argomenti esecuzione** finestra che contiene un **WatchKit** scheda. Selezionare **notifica** e fornire un payload JSON, quindi premere **Execute** per avviare l'applicazione di espressioni di controllo nel simulatore:
    
[![](notifications-images/runwith-execargs-sml.png "Selezionare l'impostazione predefinita di Payload di notifica")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per impostare l'estensione di espressioni di controllo per modificare il payload di notifica di prova rapida di Visual Studio il **le proprietà del progetto**. Passare al **Debug** sezione e selezionare un file JSON notifiche nell'elenco (verranno automaticamente visualizzate tutti i file JSON inclusi nel progetto).
    
[![](notifications-images/runwith-execargs-sml-vs.png "Selezionare un file JSON di notifiche")](notifications-images/runwith-execargs-vs.png#lightbox)

Quando l'estensione di espressioni di controllo è il **progetto di avvio**, Visual Studio visualizzerà le opzioni aggiuntive, come illustrato di seguito. Scegliere una del **notifica** opzioni per avviare l'applicazione di espressioni di controllo **notifica** modalità (utilizzando il file JSON selezionato nella finestra proprietà):
    
![](notifications-images/runwith-vs.png "Il menu di dispositivo")

-----

Quando si verifica nel simulatore con il file JSON di payload predefinito, il controller di notifica predefinito è simile al seguente:

![](notifications-images/notification-debug-sml.png "Una notifica di esempio")

È anche possibile usare il [riga di comando](~/ios/watchos/troubleshooting.md#command_line) per avviare il simulatore iOS.

### <a name="example-notification-payload"></a>Payload di notifica di esempio

Nel [Watch Kit catalogo](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/) esempio vi è un esempio di file payload JSON **NotificationPayload.json** (elencati di seguito).

```csharp
{
    "aps": {
        "alert": "Test message content",
        "title": "Optional title",
        "category": "myCategory"
        },

        "WatchKit Simulator Actions": [
        {
            "title": "First Button",
            "identifier": "firstButtonAction"
        }
        ],

        "customKey": "Use this file to define a testing payload for your notifications. The aps dictionary specifies the category, alert text and title. The WatchKit Simulator Actions array can provide info for one or more action buttons in addition to the standard Dismiss button. Any other top level keys are custom payload. If you have multiple such JSON files in your project, you'll be able to choose between them in when selecting to debug the notification interface of your Watch App."
    }
```



## <a name="related-links"></a>Collegamenti correlati

- [WatchNotifications (notifiche locale) (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)
- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Documenti di Apple Watch Kit notifiche](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
