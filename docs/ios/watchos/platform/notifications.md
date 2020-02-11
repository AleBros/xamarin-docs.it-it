---
title: Notifiche watchos in Xamarin
description: Questo documento descrive come usare le notifiche watchos in Xamarin. Viene illustrata la creazione di controller di notifica, la generazione di notifiche e il test delle notifiche.
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 6be46d31ac2c16d02749519907d650588dbbcbe6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028221"
---
# <a name="watchos-notifications-in-xamarin"></a>Notifiche watchos in Xamarin

Le app Watch possono ricevere notifiche se l'app iOS che lo contiene li supporta. È disponibile una gestione delle notifiche incorporata, pertanto non è *necessario* aggiungere il supporto per le notifiche aggiuntivo descritto di seguito, tuttavia, se si desidera personalizzare il comportamento e l'aspetto delle notifiche, vedere.

Per altre informazioni sull'aggiunta del supporto per le notifiche all'app iOS nella soluzione, vedere la documentazione relativa alle [Notifiche iOS](~/ios/platform/user-notifications/deprecated/index.md) .

## <a name="creating-notification-controllers"></a>Creazione di controller di notifica

I controller di notifica storyboard hanno un tipo speciale di segue che li attiva. Quando si trascina un nuovo **controller di interfaccia di notifica** in uno storyboard, viene automaticamente associato un segue:

![](notifications-images/notification-storyboard1.png "A new Notification Interface Controller with a segue attached")

Quando viene selezionata la notifica segue, è possibile modificarne le proprietà:

![](notifications-images/notification-storyboard2.png "The notification segue selected")

Dopo aver personalizzato il controller, potrebbe essere simile a questo esempio di WatchKitCatalog:

![](notifications-images/notifications-segue.png "The Notification Properties")

Esistono due tipi di notifica:

- Visualizzazione statica di **breve aspetto** non scorrevole definita dal sistema.

- Visualizzazione personalizzabile e scorrevole a **lungo aspetto** definita dall'utente. È possibile specificare una versione più semplice e statica e una versione dinamica più complessa.

### <a name="short-look-notification-controller"></a>Controller di notifica di breve aspetto

L'interfaccia utente di breve aspetto è costituita solo dall'icona dell'app, dal nome dell'app e dalla stringa del titolo della notifica.

Se l'utente non ignora la notifica, il sistema passa automaticamente a una notifica a lungo termine che fornisce ulteriori informazioni.

### <a name="long-look-notification-controller"></a>Controller di notifica Long-Look

Il sistema operativo decide se visualizzare la visualizzazione statica o dinamica in base a diversi fattori. È necessario fornire un'interfaccia statica e facoltativamente può includere anche un'interfaccia dinamica per le notifiche.

#### <a name="static"></a>Static

La visualizzazione statica dovrebbe essere semplice e rapida da visualizzare.

![](notifications-images/notification-static.png "The static view")

#### <a name="dynamic"></a>Dynamic

La visualizzazione dinamica può visualizzare più dati e fornire più interattività.

![](notifications-images/notification-dynamic.png "The dynamic view")

## <a name="generating-notifications"></a>Generazione di notifiche

Le notifiche possono provenire da un server remoto ([servizio Apple Push Notifications](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)o APNS) oppure possono essere generate localmente nell'app iOS.

Vedere la [procedura dettagliata per le notifiche iOS](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) per un esempio di come generare le notifiche locali e l'esempio [WatchNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications) per un esempio funzionante.

Per le notifiche locali è necessario che il set di `AlertTitle` venga visualizzato nel Apple Watch. la stringa `AlertTitle` viene visualizzata nell'interfaccia di breve aspetto. L'`AlertTitle` e il `AlertBody` vengono visualizzati nell'elenco notifiche; il `AlertBody` viene visualizzato nell'interfaccia a lungo termine.

Questa schermata mostra i `AlertTitle` visualizzati nell'elenco notifiche e il `AlertBody` visualizzato nell'interfaccia a lungo termine (usando il [codice di esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)):

![](notifications-images/watch-notificationslist-sml.png "Questa schermata mostra il AlertTitle visualizzato nell'elenco notifiche") ![](notifications-images/watch-notificationcontroller-sml.png "AlertBody visualizzato nell'interfaccia a lungo termine")

## <a name="testing-notifications"></a>Test delle notifiche

Le notifiche (sia locali che Remote) possono essere testate correttamente solo in un dispositivo, ma possono essere simulate usando un file con **estensione JSON** nel simulatore iOS.

### <a name="testing-on-apple-watch"></a>Test in Apple Watch

Quando si verificano le notifiche in un Apple Watch, tenere presente che la [documentazione di Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) indica quanto segue:

> Quando una delle notifiche locali o remote dell'app arriva all'iPhone dell'utente, iOS decide se visualizzare la notifica sull'iPhone o sul Apple Watch.

Questo sta alludendo al fatto che iOS decide se verrà visualizzata una notifica sull'iPhone o sull'orologio. Se l'iPhone associato è attivo al momento della ricezione di una notifica, è probabile che la notifica venga visualizzata nell'iPhone e *non* instradata all'orologio.

Per assicurarsi che la notifica venga visualizzata nell'orologio, spegnere la schermata iPhone (premendo il pulsante di alimentazione una volta) o lasciare che venga sospesa. Se l'orologio abbinato è compreso nell'intervallo, ha una potenza e viene usato sul polso, la notifica verrà instradata e visualizzata nell'orologio (accompagnato da un sottile).

### <a name="testing-on-the-ios-simulator"></a>Test sul simulatore iOS

È *necessario* fornire un payload JSON di test durante il test della modalità di notifica nel simulatore iOS. Impostare il percorso nella finestra **argomenti di esecuzione personalizzati** in Visual Studio per Mac.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Visual Studio per Mac visualizzerà opzioni aggiuntive quando un'estensione dell'espressione di controllo è impostata come **progetto di avvio**.
Fare clic con il pulsante destro del mouse sul progetto di estensione Watch e scegliere **Esegui con > parametri personalizzati...** :

[![](notifications-images/runwith-customparams-sml.png "Running with Custom Properties")](notifications-images/runwith-customparams.png#lightbox)

Verrà visualizzata la finestra **argomenti di esecuzione** che contiene una scheda **WatchKit** . Selezionare **notifica** e specificare un payload JSON, quindi premere **Esegui** per avviare l'app Watch nel simulatore:

[![](notifications-images/runwith-execargs-sml.png "Select Notification Payload Default")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per impostare il payload della notifica di test in Visual Studio, fare clic con il pulsante destro del mouse sull'estensione Watch per modificare le **proprietà del progetto**. Passare alla sezione **debug** e selezionare un file JSON di notifiche dall'elenco. verranno elencati automaticamente tutti i file JSON inclusi nel progetto.

[![](notifications-images/runwith-execargs-sml-vs.png "Select a notifications JSON file")](notifications-images/runwith-execargs-vs.png#lightbox)

Quando l'estensione Watch è il **progetto di avvio**, Visual Studio visualizzerà opzioni aggiuntive, come illustrato di seguito. Scegliere una delle opzioni di **notifica** per avviare l'app Watch in modalità di **notifica** (usando il file JSON selezionato nella finestra Proprietà):

![](notifications-images/runwith-vs.png "The Device menu")

-----

Il controller di notifica predefinito ha un aspetto simile al seguente quando si esegue il test del simulatore con il file JSON di payload predefinito:

![](notifications-images/notification-debug-sml.png "An example notification")

È anche possibile usare la riga di [comando](~/ios/watchos/troubleshooting.md#command_line) per avviare il simulatore iOS.

### <a name="example-notification-payload"></a>Payload di notifica di esempio

Nell'esempio [Watch Kit Catalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) è disponibile un file JSON di payload di esempio **NotificationPayload. JSON** (elencato di seguito).

```json
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

- [WatchNotifications (notifiche locali) (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)
- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Docs di Apple Watch Kit notifiche](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
