---
title: le notifiche in Xamarin watchOS
description: Questo documento descrive come usare le notifiche di watchOS in Xamarin. Vengono illustrati la creazione i controller di notifica, la generazione di notifiche e notifiche di test.
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 39c77b4016027171a4e76bc4fb15c77d733cf5ba
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065360"
---
# <a name="watchos-notifications-in-xamarin"></a>le notifiche in Xamarin watchOS

Le app Watch possono ricevere notifiche se l'app per iOS contiene supportarle. È presente gestione delle notifiche incorporate in modo che non si *necessario* per aggiungere il supporto per le notifiche aggiuntive descritto di seguito, tuttavia se si vuole personalizzare il comportamento di notifica e l'aspetto, continuare a leggere.

Vedere le [notifiche di iOS](~/ios/platform/user-notifications/deprecated/index.md) doc per altre informazioni sull'aggiunta di supporto per le notifiche all'app iOS nella soluzione.

## <a name="creating-notification-controllers"></a>Creazione di controller di notifica

Sullo storyboard controller notifiche hanno un tipo speciale di elemento segue attivandoli in. Quando si trascina un nuovo **Controller di interfaccia di notifica** in uno storyboard avranno automaticamente un elemento segue collegato:

![](notifications-images/notification-storyboard1.png "Un nuovo Controller di interfaccia di notifica con un elemento segue collegato")

Quando segue la notifica viene selezionata è possibile modificare le relative proprietà:

![](notifications-images/notification-storyboard2.png "La notifica Visualizza selezionato")

Dopo aver personalizzato il controller sarà simile a questo esempio dal WatchKitCatalog:

![](notifications-images/notifications-segue.png "Le proprietà di notifica")


Esistono due tipi di notifica:

- Visualizzazione statica di **breve aspetto** non scorrevole definita dal sistema.

- **Aspetto prolungata** - scorrevole, personalizzabile vista definita dall'utente. Una versione più semplice, statica e una versione dinamica più complessa possono essere specificati.

### <a name="short-look-notification-controller"></a>Funzione di ricerca breve Controller di notifica

L'interfaccia utente breve sguardo è costituita da solo l'icona dell'app, nome dell'app e la stringa del titolo di notifica.

Se l'utente non ignora la notifica, il sistema passerà automaticamente alla notifica prolungata aspetto che fornisce informazioni aggiuntive.


### <a name="long-look-notification-controller"></a>Funzione di ricerca prolungata Controller di notifica

Il sistema operativo decide se mostrare la visualizzazione statica o dinamica in base a diversi fattori. È necessario fornire un'interfaccia statica e può facoltativamente includere anche un'interfaccia dinamica per le notifiche.

#### <a name="static"></a>Static

La visualizzazione statica deve essere semplice e rapida da visualizzare.

![](notifications-images/notification-static.png "La visualizzazione statica")

#### <a name="dynamic"></a>Dynamic

La visualizzazione dinamica può visualizzare più dati e fornire maggiore interattività.

![](notifications-images/notification-dynamic.png "La visualizzazione dinamica")


## <a name="generating-notifications"></a>Generazione di notifiche

Le notifiche possono provenire da un server remoto ([servizio di notifiche Push di Apple](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), o servizio APN) o possono essere generati in locale nell'app per iOS.

Fare riferimento al [procedura dettagliata di notifiche di iOS](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) per un esempio di come generare le notifiche locali e il [esempio WatchNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications) per un esempio funzionante.

Le notifiche locali devono avere il `AlertTitle` impostato da visualizzare nella finestra di di Apple Watch - il `AlertTitle` stringa viene visualizzata nell'interfaccia breve sguardo. Sia la `AlertTitle` e `AlertBody` vengono visualizzati nell'elenco delle notifiche; e il `AlertBody` viene visualizzato nell'interfaccia prolungata la ricerca.

In questo screenshot appare il `AlertTitle` visualizzati nell'elenco delle notifiche e la `AlertBody` visualizzata nell'interfaccia di aspetto prolungata (usando la [esempi di codice](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)):

![](notifications-images/watch-notificationslist-sml.png "In questo screenshot appare il AlertTitle visualizzati nell'elenco delle notifiche") ![](notifications-images/watch-notificationcontroller-sml.png "The AlertBody visualizzata nell'interfaccia di Long-Cerca")

## <a name="testing-notifications"></a>Le notifiche di test

Notifiche (locali e remote) possono essere correttamente testate solo in un dispositivo, ma può essere simulati usando un **JSON** file nel simulatore iOS.

### <a name="testing-on-apple-watch"></a>Test su Apple Watch

Quando si testa le notifiche in un Apple Watch, tenere presente che [documentazione di Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) indica quanto segue:

> Quando una delle notifiche locali o remoti dell'app viene recapitato in un iPhone dell'utente, iOS decide se visualizzare la notifica per iPhone o per l'Apple Watch.

Ciò è alluding al fatto che iOS decide se verrà visualizzata una notifica per iPhone o per le espressioni di controllo. Se l'iPhone associato è attiva quando viene ricevuta una notifica, la notifica è probabile che vengano visualizzati su iPhone e *non* indirizzato per l'espressione di controllo.

Per garantire che la notifica viene visualizzata nell'orologio, disattivare lo schermo di iPhone (premendo il pulsante di alimentazione, una volta) o lasciare che vanno in sospensione. Se Watch associato è compreso nell'intervallo, alimentazione e indossato su in alcun modo, la notifica verrà indirizzato esiste e vengono visualizzati nella finestra di espressioni di controllo (accompagnati da un sottile).

### <a name="testing-on-the-ios-simulator"></a>Il test nel simulatore iOS

Si *necessario* fornire un payload JSON di test durante il test in modalità di notifica nel simulatore iOS. Impostare il percorso nel **gli argomenti di esecuzione personalizzata** finestra in Visual Studio per Mac.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Visual Studio per Mac visualizzerà le opzioni aggiuntive quando un'estensione di espressioni di controllo è impostata come la **progetto di avvio**.
Pulsante destro del mouse sul progetto di estensione di espressioni di controllo e scegliere **eseguire con > parametri personalizzati...** :

[![](notifications-images/runwith-customparams-sml.png "Esecuzione con le proprietà personalizzate")](notifications-images/runwith-customparams.png#lightbox)

Verrà visualizzata la **gli argomenti di esecuzione** finestra che contiene un **WatchKit** scheda. Selezionare **Notification** e specificare un payload JSON, quindi premere **Execute** per avviare l'app watch nel simulatore:

[![](notifications-images/runwith-execargs-sml.png "Selezionare l'impostazione predefinita di Payload di notifica")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per impostare il payload di notifica di prova nella scelta di Visual Studio sull'estensione di espressioni di controllo per modificare la **proprietà del progetto**. Andare alla **Debug** sezione e selezionare un file JSON delle notifiche nell'elenco (verrà elencato automaticamente tutti i file JSON inclusi nel progetto).

[![](notifications-images/runwith-execargs-sml-vs.png "Selezionare un file JSON delle notifiche")](notifications-images/runwith-execargs-vs.png#lightbox)

Quando l'estensione di espressioni di controllo è il **progetto di avvio**, Visual Studio visualizzerà le opzioni aggiuntive, come illustrato di seguito. Scegliere una delle **Notification** le opzioni per avviare l'app watch in **notifica** modalità (usando il file JSON selezionato nella finestra proprietà):

![](notifications-images/runwith-vs.png "Menu del dispositivo")

-----

Il controller di notifica predefinito aspetto simile al seguente durante il test nel simulatore con il file JSON di payload predefinito:

![](notifications-images/notification-debug-sml.png "Un esempio di notifica")

È anche possibile usare la [riga di comando](~/ios/watchos/troubleshooting.md#command_line) per avviare il simulatore iOS.

### <a name="example-notification-payload"></a>Payload di notifica di esempio

Nel [catalogo Kit Watch](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) esempio vi è un file JSON di esempio payload **NotificationPayload.json** (elencati di seguito).

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
- [Documentazione di Apple Watch Kit notifiche](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
