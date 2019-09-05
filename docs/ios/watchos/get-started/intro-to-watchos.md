---
title: Introduzione a watchOS
description: Questo documento viene fornita una panoramica di watchOS, che descrive il ciclo di vita dell'applicazione, i tipi di interfaccia utente, le dimensioni dello schermo, limitazioni e altro ancora.
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 59d02db9fa2787e93ad88e4b6f37e0fef50572a5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293106"
---
# <a name="introduction-to-watchos"></a>Introduzione a watchOS

> [!NOTE]
> Consultare il [Introduzione a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) per una panoramica delle funzionalità più recenti.

## <a name="about-watchos"></a>Sulle watchOS

Dispone di una soluzione di app watchOS 3 progetti:

- **Guarda estensione** : un progetto che contiene il codice per l'app watch.
- **Guarda App** – contiene le risorse e storyboard dell'interfaccia utente.
- **iOS App padre** : questa app è un'app per iPhone normale. L'app e l'estensione vengono aggregati nell'app iPhone per il recapito per espressioni di controllo dell'utente.

Nelle App watchOS 1, il codice dell'estensione viene eseguito su iPhone: l'Apple Watch è effettivamente uno schermo esterno. le app watchOS 2 e 3 vengono eseguite interamente sul Apple Watch. Questa differenza è illustrata nel diagramma seguente:

[![](intro-to-watchos-images/arch-sml.png "Il diagramma mostra la differenza tra watchos 1 e watchos 2 (e versioni successive)")](intro-to-watchos-images/arch.png#lightbox)

Indipendentemente dalla versione di watchOS, di destinazione in Visual Studio per il riquadro della soluzione del Mac una soluzione completa avrà un aspetto simile al seguente:

[![](intro-to-watchos-images/projectstructure-sml.png "Il riquadro della soluzione")](intro-to-watchos-images/projectstructure.png#lightbox)

Il *App padre* in watchOS una soluzione è un'app iOS regolari. Questo è l'unico progetto nella soluzione che è visibile **sul telefono**. Casi d'uso per questa app include esercitazioni, schermate amministrative e applicazione di filtri e così via cacheing livello intermedio. Tuttavia, è possibile che all'utente di installare ed eseguire l'app/estensione di espressioni di controllo senza **mai** avere aperto l'app padre, quindi, l'app padre per l'esecuzione per l'inizializzazione unica o l'amministrazione, è necessario programmare le espressioni di controllo App/estensione comunica all'utente che.

Anche se l'app padre distribuisce l'app e l'estensione, vengono eseguite in diversi ambienti sandbox.

In watchOS 1 possono condividere dati tramite un gruppo di app condiviso o tramite la funzione statica `WKInterfaceController.OpenParentApplication`, che attiverà il `UIApplicationDelegate.HandleWatchKitExtensionRequest` metodo dell'App padre `AppDelegate` (vedere [funziona con l'app padre](~/ios/watchos/app-fundamentals/parent-app.md)).

In watchOS 2 o versioni successive il framework di connettività di espressioni di controllo viene utilizzato per comunicare con l'app di padre, utilizzando il `WCSession` classe.

## <a name="application-lifecycle"></a>Ciclo di vita dell'applicazione

Nell'estensione di espressioni di controllo, una sottoclasse del `WKInterfaceController` classe viene creata per ogni scena Storyboard.

Questi `WKInterfaceController` sono analoghe alle classi di `UIViewController` gli oggetti nella programmazione iOS, ma non è lo stesso livello di accesso alla visualizzazione.
Ad esempio, è in modo dinamico non è possibile aggiungere controlli a o ristrutturare l'interfaccia utente.
È possibile, tuttavia, nascondere e visualizzare i controlli e, con alcuni controlli, modificarne la dimensione, trasparenza e opzioni di visualizzazione.

Il ciclo di vita di un `WKInterfaceController` oggetto include le chiamate seguenti:

- [Sveglia](xref:WatchKit.WKInterfaceController.Awake*) : È consigliabile eseguire la maggior parte dell'inizializzazione in questo metodo.
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) : Chiamato poco prima che l'app Watch venga visualizzata all'utente. Usare questo metodo per eseguire l'inizializzazione dell'ultimo momento, avviare le animazioni e così via.
- A questo punto, viene visualizzata l'App di espressioni di controllo e l'estensione inizia risponde all'utente di input e l'aggiornamento di visualizzazione dell'App per le espressioni di controllo per la logica dell'applicazione.
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate) dopo l'App Watch è stato ignorato dall'utente, questo metodo viene chiamato. Quando termina, questo metodo controlli dell'interfaccia utente non possono essere modificati fino al successivo `WillActivate` viene chiamato. Questo metodo anche essere chiamato se la connessione per l'iPhone è interrotta.
- Dopo che l'estensione è stata disattivata, non è accessibile al programma. In attesa di funzioni asincrone **non** essere chiamato. Guarda che le estensioni di Kit non può utilizzare le modalità di elaborazione in background. Se il programma viene riattivato dall'utente, ma l'app non è stata terminata dal sistema operativo, sarà il primo metodo chiamato `WillActivate`.

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Panoramica del ciclo di vita dell'applicazione")

## <a name="types-of-user-interface"></a>Tipi di interfaccia utente

Esistono tre tipi di interazione con che l'utente può avere l'App watch.
Tutti vengono programmati utilizzando le classi secondarie personalizzate di `WKInterfaceController`, in modo che la sequenza del ciclo di vita illustrati in precedenza applicato universalmente (le notifiche vengono programmate le classi secondarie `WKUserNotificationController`, che a sua volta è una sottoclasse di `WKInterfaceController`):

### <a name="normal-interaction"></a>Interazione normale

La maggior parte dell'interazione tra l'estensione dell'app/espressioni di controllo saranno le classi secondarie `WKInterfaceController` scritto in modo che corrispondano a in background nelle app watch **Interface.storyboard**. Questo aspetto è illustrato in dettaglio nel [installazione](~/ios/watchos/get-started/installation.md) e [introduttiva](~/ios/watchos/get-started/index.md) articoli.
L'immagine seguente mostra una parte del [Watch Kit catalogo](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) Storyboard dell'esempio. Per ogni scena è stato illustrato in questo caso, è disponibile una classe personalizzata corrispondente `WKInterfaceController` (`LabelDetailController`, `ButtonDetailController`, `SwitchDetailController`e così via) nel progetto di estensione.

![](intro-to-watchos-images/scenes.png "Esempi di interazione normale")

### <a name="notifications"></a>Notifiche

[Le notifiche](~/ios/watchos/platform/notifications.md) sono un caso d'uso principali per l'Apple Watch. Sono supportate notifiche locali e remote. L'interazione con le notifiche si verifica in due fasi, denominate aspetto breve e lungo.

Cerca breve vengono visualizzati brevemente e Mostra l'icona dell'app watch, il nome e il titolo (come specificato con `WKInterfaceController.SetTitle`).

L'aspetto lungo combina fornita dal sistema **scorrevole** area e un pulsante Dismiss con uno Storyboard in base al contenuto personalizzato.

`WKUserNotificationInterfaceController` estende `WKInterfaceController` con i metodi `DidReceiveLocalNotification` e `DidReceiveRemoteNotification`.
Eseguire l'override di questi metodi per reagire agli eventi di notifica.

Per ulteriori informazioni sulla progettazione dell'interfaccia utente di notifica, vedere il [Human Interface Guidelines di Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Notifiche di esempio")

## <a name="screen-sizes"></a>Dimensioni dello schermo

Il Apple Watch ha due dimensioni del volto: 38 mm e 42 mm, con un rapporto di visualizzazione 5:4 e una visualizzazione retina. Le relative dimensioni utilizzabili sono:

- 38mm 136 x 170 pixel logici (272 x 340 pixel fisici)
- da 42mm 156 x 195 pixel logici (312 x 390 pixel fisici).

Usare `WKInterfaceDevice.ScreenBounds` per determinare in cui è in esecuzione l'App Watch.

In genere, risulta più semplice sviluppare la progettazione di layout e il testo con la visualizzazione di 38mm più limitata e quindi aumentare le prestazioni.
Se si inizia con l'ambiente di dimensioni maggiori, ridurre le prestazioni possono risultare confusa si sovrappongono o testo troncamento.

Altre informazioni, vedere [funziona con le dimensioni dello schermo](~/ios/watchos/app-fundamentals/screen-sizes.md).


## <a name="limitations-of-watchos"></a>Limitazioni di watchOS

Esistono alcune limitazioni di watchOS da tenere presenti quando si sviluppano App watchOS:

- I dispositivi di Apple Watch non dispongono di archiviazione - essere compatibile con lo spazio disponibile prima di scaricare i file di grandi dimensioni (ad es. file audio o film).

- Molti watchOS [controlli](~/ios/watchos/user-interface/index.md) avere soluzioni analoghe disponibili in UIKit, ma sono classi diverse (`WKInterfaceButton` anziché `UIButton`, `WKInterfaceSwitch` per `UISwitch`e così via) e hanno un set limitato di metodi rispetto alle loro UIKit equivalenti. Inoltre, watchOS dispone di alcuni controlli, ad esempio `WKInterfaceDate` (per la visualizzazione di una data e ora) non ha tale UIKit.

  - È non può instradare le notifiche solo le espressioni di controllo o solo l'iPhone (tipo di controllo, l'utente ha il routing è non stata annunciato da Apple).

Alcune altre limitazioni note / domande frequenti:

- Apple non consente i volti di verifica personalizzato 3rd-party.

- Le API che consentono l'espressione di controllo controllare l'iTunes sul telefono connesso sono private.


## <a name="further-reading"></a>Ulteriori informazioni

Consultare la documentazione di Apple:

- [Sviluppo di applicazioni per Watch Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [Guarda la Guida per programmatori Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Human Interface Guidelines di Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)


## <a name="related-links"></a>Collegamenti correlati

- [watchOS 3 catalogo (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [watchOS 1 catalogo (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Configurazione e installazione](~/ios/watchos/get-started/installation.md)
- [Primo video App Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple di sviluppo per Watch Kit guide](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Suggerimenti di Apple WatchKit](https://developer.apple.com/watchkit/tips/)
- [Introduzione a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
