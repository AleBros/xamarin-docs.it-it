---
title: Introduzione a watchOS
description: Questo documento fornisce una panoramica dei watchos, che descrivono il ciclo di vita dell'applicazione, i tipi di interfaccia utente, le dimensioni dello schermo, le limitazioni e altro ancora.
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: b3c2908d8ae9a68189fbff4d47afa49da21b88a5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030184"
---
# <a name="introduction-to-watchos"></a>Introduzione a watchOS

> [!NOTE]
> Vedere l' [Introduzione a watchos 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) per una panoramica delle funzionalità più recenti.

## <a name="about-watchos"></a>Informazioni su watchos

Una soluzione di app watchos include 3 progetti:

- **Estensione Watch** : progetto che contiene il codice per l'app Watch.
- **App Watch** : contiene lo storyboard e le risorse dell'interfaccia utente.
- **app padre iOS** : questa app è un'app iPhone normale. L'app Watch e l'estensione vengono aggregate nell'app iPhone per la consegna all'orologio dell'utente.

Nelle app watchos 1 il codice nell'estensione viene eseguito in iPhone, il Apple Watch è effettivamente uno schermo esterno. le app watchos 2 e 3 vengono eseguite interamente sul Apple Watch. Questa differenza è illustrata nel diagramma seguente:

[![](intro-to-watchos-images/arch-sml.png "The difference between watchOS 1 and watchOS 2 (and greater) is shown in this diagram")](intro-to-watchos-images/arch.png#lightbox)

Indipendentemente dalla versione di watchos di destinazione, in Visual Studio per Mac riquadro della soluzione una soluzione completa sarà simile alla seguente:

[![](intro-to-watchos-images/projectstructure-sml.png "The Solution Pad")](intro-to-watchos-images/projectstructure.png#lightbox)

L' *app padre* in una soluzione watchos è una normale app iOS. Si tratta dell'unico progetto della soluzione visibile **sul telefono**. I casi d'uso per questa app includono le esercitazioni, le schermate amministrative e il filtro di livello intermedio, la memorizzazione nella cache e così via. Tuttavia, è possibile che l'utente possa installare ed eseguire l'estensione o l'app Watch senza **mai** aprire l'app padre, quindi se è necessario che l'app padre venga eseguita per l'inizializzazione o l'amministrazione monouso, è necessario programmare l'app o l'estensione Watch per indicare utente che.

Sebbene l'app padre fornisca l'app e l'estensione Watch, vengono eseguite in sandbox differenti.

In watchos 1 possono condividere dati tramite un gruppo di app condivise o tramite la funzione statica `WKInterfaceController.OpenParentApplication`, che attiverà il metodo `UIApplicationDelegate.HandleWatchKitExtensionRequest` nel `AppDelegate` dell'app padre. vedere [uso dell'app padre](~/ios/watchos/app-fundamentals/parent-app.md).

In watchos 2 o versioni successive il Framework di connettività Watch viene usato per comunicare con l'app padre, usando la classe `WCSession`.

## <a name="application-lifecycle"></a>Ciclo di vita dell'applicazione

Nell'estensione Watch viene creata una sottoclasse della classe `WKInterfaceController` per ogni scena dello storyboard.

Queste `WKInterfaceController` classi sono analoghe agli oggetti `UIViewController` nella programmazione iOS, ma non hanno lo stesso livello di accesso alla vista.
Ad esempio, non è possibile aggiungere o ristrutturare dinamicamente i controlli nell'interfaccia utente.
È tuttavia possibile nascondere e rivelare controlli e, con alcuni controlli, modificarne le dimensioni, la trasparenza e le opzioni relative all'aspetto.

Il ciclo di vita di un oggetto `WKInterfaceController` prevede le chiamate seguenti:

- [Svegli](xref:WatchKit.WKInterfaceController.Awake*) : è necessario eseguire la maggior parte dell'inizializzazione in questo metodo.
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) : chiamato poco prima che l'app Watch venga visualizzata all'utente. Usare questo metodo per eseguire l'inizializzazione dell'ultimo momento, avviare animazioni e così via.
- A questo punto, l'app Watch viene visualizzata e l'estensione inizia a rispondere all'input dell'utente e aggiorna la visualizzazione dell'app Watch in base alla logica dell'applicazione.
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate) Dopo che l'app Watch è stata rilasciata dall'utente, questo metodo viene chiamato. Una volta restituito questo metodo, i controlli dell'interfaccia utente non possono essere modificati fino alla successiva chiamata a `WillActivate`. Questo metodo verrà chiamato anche se la connessione all'iPhone è interruppe.
- Dopo che l'estensione è stata disattivata, non è accessibile al programma. Le funzioni asincrone in sospeso **non verranno** chiamate. Le estensioni del kit di controllo non possono usare le modalità di elaborazione in background. Se il programma viene riattivato dall'utente, ma l'app non è stata terminata dal sistema operativo, il primo metodo chiamato sarà `WillActivate`.

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Application Lifecycle overview")

## <a name="types-of-user-interface"></a>Tipi di interfaccia utente

Esistono tre tipi di interazione che l'utente può avere con l'app Watch.
Tutti sono programmati utilizzando sottoclassi personalizzate di `WKInterfaceController`, quindi la sequenza del ciclo di vita descritta in precedenza viene applicata in modo universale (le notifiche vengono programmate con le sottoclassi di `WKUserNotificationController`, che a sua volta è una sottoclasse di `WKInterfaceController`):

### <a name="normal-interaction"></a>Interazione normale

La maggior parte dell'interazione tra app e estensioni di controllo sarà con le sottoclassi di `WKInterfaceController` scritte in modo da corrispondere alle scene nell'interfaccia dell'app Watch **. Storyboard**. Questa operazione è illustrata in dettaglio negli articoli sull' [installazione](~/ios/watchos/get-started/installation.md) e [Introduzione](~/ios/watchos/get-started/index.md) .
Nella figura seguente è illustrata una parte dello storyboard dell'esempio del [Catalogo di Watch Kit](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) . Per ogni scena mostrata qui, nel progetto di estensione è presente una `WKInterfaceController` personalizzata corrispondente (`LabelDetailController`, `ButtonDetailController`, `SwitchDetailController`e così via).

![](intro-to-watchos-images/scenes.png "Normal Interaction examples")

### <a name="notifications"></a>Notifiche

Le [notifiche](~/ios/watchos/platform/notifications.md) rappresentano un caso d'uso principale per la Apple Watch. Sono supportate sia le notifiche locali che quelle remote. L'interazione con le notifiche avviene in due fasi, denominate short e Long-look.

L'aspetto breve viene visualizzato brevemente e Mostra l'icona dell'app Watch, il nome e il titolo (come specificato con `WKInterfaceController.SetTitle`).

Il lungo aspetto combina un'area di **Sash** fornita dal sistema e il pulsante Ignora con il contenuto personalizzato basato su storyboard.

`WKUserNotificationInterfaceController` estende `WKInterfaceController` con i metodi `DidReceiveLocalNotification` e `DidReceiveRemoteNotification`.
Eseguire l'override di questi metodi per rispondere agli eventi di notifica.

Per ulteriori informazioni sulla progettazione dell'interfaccia utente di notifica, vedere la [Apple Watch linee guida sull'interfaccia umana](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Sample notifications")

## <a name="screen-sizes"></a>Dimensioni dello schermo

Il Apple Watch ha due dimensioni del volto: 38 mm e 42 mm, con un rapporto di visualizzazione 5:4 e una visualizzazione retina. Le dimensioni utilizzabili sono:

- 38 mm: 136 x 170 pixel logici (272 x 340 pixel fisici)
- 42 mm: 156 x 195 pixel logici (312 x 390 pixel fisici).

Usare `WKInterfaceDevice.ScreenBounds` per determinare in quale visualizzazione è in esecuzione l'app Watch.

In genere, è più semplice sviluppare il testo e la progettazione del layout con la visualizzazione di 38 mm più vincolata e quindi aumentare il numero di dimensioni.
Se si inizia con un ambiente più ampio, il ridimensionamento potrebbe causare una sovrapposizione o un troncamento del testo.

Altre informazioni sull' [uso delle dimensioni dello schermo](~/ios/watchos/app-fundamentals/screen-sizes.md).

## <a name="limitations-of-watchos"></a>Limitazioni di watchos

Quando si sviluppano app watchos è necessario tenere presenti alcune limitazioni di watchos:

- I dispositivi Apple Watch hanno un'archiviazione limitata. tenere presente lo spazio disponibile prima di scaricare file di grandi dimensioni, ad esempio file audio o video).

- Molti [controlli](~/ios/watchos/user-interface/index.md) watchos hanno analoghi in UIKit, ma sono classi diverse (`WKInterfaceButton` anziché `UIButton`, `WKInterfaceSwitch` per `UISwitch`e così via) e hanno un set limitato di metodi rispetto ai rispettivi equivalenti di UIKit. Watchos dispone inoltre di alcuni controlli, ad esempio `WKInterfaceDate` (per la visualizzazione di una data e ora) che UIKit non dispone di.

  - Non è possibile instradare le notifiche al solo orologio o solo l'iPhone (il tipo di controllo che l'utente ha sul routing non è stato annunciato da Apple).

Altre limitazioni note/domande frequenti:

- Apple non consentirà visi di controllo personalizzati di terze parti.

- Le API che consentono all'orologio di controllare iTunes sul telefono connesso sono private.

## <a name="further-reading"></a>Ulteriori informazioni

Consultare la documentazione di Apple:

- [Sviluppo per Watch Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [Guida alla programmazione di Watch Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Linee guida per l'interfaccia umana Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)

## <a name="related-links"></a>Collegamenti correlati

- [Catalogo watchos 3 (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Catalogo watchos 1 (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Installazione e installazione](~/ios/watchos/get-started/installation.md)
- [Video dell'app First Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Guida allo sviluppo di Apple per Watch Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Suggerimenti WatchKit Apple](https://developer.apple.com/watchkit/tips/)
- [Introduzione a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
