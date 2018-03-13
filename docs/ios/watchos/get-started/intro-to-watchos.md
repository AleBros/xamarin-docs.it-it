---
title: Introduzione a watchOS
description: Panoramica della struttura di soluzione watchOS e limitazioni
ms.topic: article
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 2276b67fc29f2752e4b178168a12e6e980b788d0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="introduction-to-watchos"></a>Introduzione a watchOS

> [!NOTE]
> **Nota:** estrae il [Introduzione a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) per una panoramica delle funzionalità più recenti.

## <a name="about-watchos"></a>Su watchOS

Una soluzione di app watchOS è 3 progetti:

- **Guardare estensione** : un progetto che contiene il codice per l'applicazione di espressioni di controllo.
- **Guardare App** – contiene le risorse e storyboard dell'interfaccia utente.
- **iOS App padre** : questa applicazione è un'applicazione normale iPhone. L'applicazione di espressioni di controllo e l'estensione sono inclusi nell'app iPhone per il recapito alle espressioni di controllo dell'utente.

Nelle App watchOS 1, il codice dell'estensione viene eseguito su iPhone: l'Apple Watch è effettivamente un display esterno. le app watchOS 2 e 3 vengono eseguite interamente nel Apple Watch. Questa differenza è illustrata nel diagramma seguente:

[ ![](intro-to-watchos-images/arch-sml.png "La differenza tra watchOS 1 e 2 (e versioni successive) di watchOS è illustrata in questo diagramma")](intro-to-watchos-images/arch.png#lightbox)

Indipendentemente dalla versione di watchOS, di destinazione in Visual Studio per il riempimento di soluzione del Mac una soluzione completa avrà un aspetto simile al seguente:

[![](intro-to-watchos-images/projectstructure-sml.png "Il riquadro soluzione")](intro-to-watchos-images/projectstructure.png#lightbox)

Il *App padre* in un watchOS soluzione è un'app iOS regolare. Si tratta del progetto nella soluzione che è visibile solo **sul telefono**. Casi d'uso per questa app includono esercitazioni, schermate di amministrazione e applicazione di filtri e così via cacheing di livello intermedio. Tuttavia, è possibile che l'utente di installare ed eseguire l'app/estensione di espressioni di controllo senza **mai** avere aperto l'app padre, pertanto se è necessario l'app padre per l'esecuzione di un'inizializzazione una tantum o amministrazione, è necessario per le espressioni di controllo del programma / estensione dell'App per informare l'utente che.

Anche se l'app padre recapita l'applicazione di espressioni di controllo e l'estensione, eseguono in sandbox differenti.

In watchOS 1 possono condividere dati tramite un gruppo di app condiviso o la funzione statica `WKInterfaceController.OpenParentApplication`, che attiverà il `UIApplicationDelegate.HandleWatchKitExtensionRequest` metodo nell'applicazione padre `AppDelegate` (vedere [utilizzo con l'app padre](~/ios/watchos/app-fundamentals/parent-app.md)).

In watchOS 2 o versioni successive il framework di connettività di espressioni di controllo viene utilizzato per comunicare con l'applicazione padre, utilizzando la `WCSession` classe.

## <a name="application-lifecycle"></a>Ciclo di vita dell'applicazione

Nell'estensione di espressioni di controllo, una sottoclasse di `WKInterfaceController` classe viene creata per ogni scena Storyboard.

Questi `WKInterfaceController` sono analoghe alle classi di `UIViewController` oggetti nella programmazione iOS ma non è lo stesso livello di accesso alla visualizzazione.
Ad esempio, non è possibile aggiungere controlli a dinamicamente o ristrutturare l'interfaccia utente.
È possibile, tuttavia, nascondere e visualizzare controlli e, con alcuni controlli, modificare le dimensioni, trasparenza e opzioni di visualizzazione.

Il ciclo di vita di un `WKInterfaceController` oggetto include le chiamate seguenti:

- [Attivi](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.Awake/) : È consigliabile eseguire la maggior parte dell'inizializzazione in questo metodo.
- [WillActivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.WillActivate/) : chiamato poco prima che l'App di espressioni di controllo viene visualizzata all'utente. Utilizzare questo metodo per eseguire l'inizializzazione dell'ultimo momento, avviare le animazioni e così via.
- A questo punto, viene visualizzata l'applicazione di espressioni di controllo e l'estensione inizia risponde all'utente di input e l'aggiornamento di visualizzazione dell'App espressioni di controllo per la logica dell'applicazione.
- [DidDeactivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.DidDeactivate/) dopo l'espressione di controllo App è stata chiusa dall'utente, questo metodo viene chiamato. Dopo che questo metodo viene restituito, i controlli dell'interfaccia utente non possono essere modificati fino alla successiva `WillActivate` viene chiamato. Questo metodo anche essere chiamato se viene interrotta la connessione per iPhone.
- Dopo l'estensione è stato disattivato, non è accessibile al programma. In sospeso funzioni asincrone **non** essere chiamato. Le estensioni del Kit di espressioni di controllo non può utilizzare le modalità di elaborazione in background. Se il programma viene riattivato dall'utente, ma l'app non è stata terminata dal sistema operativo, sarà il primo metodo chiamato `WillActivate`.

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Panoramica del ciclo di vita delle applicazioni")

## <a name="types-of-user-interface"></a>Tipi di interfaccia utente

Esistono tre tipi di interazione con che l'utente può avere con l'applicazione di espressioni di controllo.
Tutti sono programmati utilizzando personalizzati sottoclassi di `WKInterfaceController`, pertanto la sequenza del ciclo di vita illustrate in precedenza si applica universalmente (notifiche vengono programmate con le sottoclassi di `WKUserNotificationController`, che a sua volta è una sottoclasse di `WKInterfaceController`):

### <a name="normal-interaction"></a>Interazione normale

La maggior parte dell'interazione dell'estensione dell'app/espressioni di controllo sarà con le sottoclassi di `WKInterfaceController` che si scrive in modo da corrispondere le scene in espressioni di controllo dell'applicazione **Interface.storyboard**. Questo aspetto è illustrato in dettaglio nel [installazione](~/ios/watchos/get-started/installation.md) e [Introduzione](~/ios/watchos/get-started/index.md) articoli.
L'immagine seguente mostra una parte di [catalogo Kit espressioni di controllo](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) Storyboard dell'esempio. Per ogni scena è stato illustrato in questo caso, è disponibile un oggetto personalizzato corrispondente `WKInterfaceController` (`LabelDetailController`, `ButtonDetailController`, `SwitchDetailController`e così via) nel progetto di estensione.

![](intro-to-watchos-images/scenes.png "Esempi di interazione normale")

### <a name="notifications"></a>Notifiche

[Le notifiche](~/ios/watchos/platform/notifications.md) sono un caso d'uso principali per l'Apple Watch. Le notifiche locali e remote sono supportate. Interazione con le notifiche viene eseguita in due fasi, chiamate breve e lungo aspetto.

Ha un aspetto breve brevemente visualizzato e Mostra l'icona dell'app espressioni di controllo, il nome e il titolo (come specificato con `WKInterfaceController.SetTitle`).

Cerca lungo combina fornita dal sistema **scorrevole** area e il pulsante Elimina con contenuto personalizzato basato su uno Storyboard.

`WKUserNotificationInterfaceController` estende `WKInterfaceController` con i metodi `DidReceiveLocalNotification` e `DidReceiveRemoteNotification`.
Eseguire l'override di questi metodi per rispondere agli eventi di notifica.

Per ulteriori informazioni sulla progettazione dell'interfaccia utente di notifica, vedere il [indicazioni dell'interfaccia umana Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Notifiche di esempio")

## <a name="screen-sizes"></a>Dimensioni dello schermo

L'Apple Watch ha due dimensioni faccia: 38 e 42mm, sia con un rapporto di visualizzazione 5:4 e un display Retina. Le dimensioni utilizzabili sono:

- 38 mm: 136 x 170 pixel logici (pixel fisici 272 x 340)
- 42 mm: 156 x 195 pixel logici (pixel fisici 312 x 390).

Utilizzare `WKInterfaceDevice.ScreenBounds` per determinare quale visualizzazione è in esecuzione l'applicazione di espressioni di controllo.

In genere, è più semplice sviluppare la struttura di testo e il layout con la visualizzazione di 38mm più limitata e quindi applicare la scalabilità verticale.
Se si inizia con l'ambiente di dimensioni maggiore, scalabilità verso il basso può causare problemi si sovrappongono o il troncamento del testo.

Altre informazioni sui [utilizzo dimensioni dello schermo](~/ios/watchos/app-fundamentals/screen-sizes.md).


## <a name="limitations-of-watchos"></a>Limitazioni di watchOS

Esistono alcune limitazioni di watchOS da tenere presenti quando si sviluppano applicazioni watchOS:

- I dispositivi di Apple Watch non dispongono di archiviazione, tenere in considerazione lo spazio disponibile prima di scaricare i file di grandi dimensioni (ad es. file audio o film).

- Molti watchOS [controlli](~/ios/watchos/user-interface/index.md) hanno analoghi in UIKit, ma sono classi diverse (`WKInterfaceButton` anziché `UIButton`, `WKInterfaceSwitch` per `UISwitch`e così via) e dispone di un set limitato di metodi rispetto alle loro UIKit equivalenti. Inoltre, watchOS dispone di alcuni controlli, ad esempio `WKInterfaceDate` (per la visualizzazione di una data e ora) non dispone di UIKit.

  - Impossibile distribuire le notifiche solo le espressioni di controllo o solo l'iPhone (quale tipo di controllo, l'utente ha sul routing è non stata annunciata da Apple).

Altre limitazioni note / domande frequenti:

- Apple non consente caratteri tipografici 3rd party espressioni di controllo personalizzato.

- Le API che consentono di espressioni di controllo controllare iTunes sul telefono connesso sono private.


## <a name="further-reading"></a>Ulteriori informazioni

Consultare la documentazione di Apple:

* [Sviluppo per i Kit di espressioni di controllo](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

* [Controllare la Guida per programmatori Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

* [Linee guida dell'interfaccia umana Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)


## <a name="related-links"></a>Collegamenti correlati

- [watchOS 3 catalogo (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [watchOS 1 catalogo (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Configurazione e installazione](~/ios/watchos/get-started/installation.md)
- [Primo video App Watch](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple di sviluppo per la Guida Kit di espressioni di controllo](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Suggerimenti WatchKit di Apple](https://developer.apple.com/watchkit/tips/)
- [Introduzione a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
