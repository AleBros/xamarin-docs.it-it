---
title: Introduzione a tvOS 10
description: Questo articolo illustra tutte le nuove e modificate le API e funzionalità disponibili in tvOS 10 per gli sviluppatori di xamarin. tvos.
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 260d01d6aa8344dd3cf107f1ffc34167c457a491
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269312"
---
# <a name="introduction-to-tvos-10"></a>Introduzione a tvOS 10

_Questo articolo illustra tutte le nuove e modificate le API e funzionalità disponibili in tvOS 10 per gli sviluppatori di xamarin. tvos._

Con la nuova tvOS 10 Apple SDK è incluso nuove API e servizi che consentono allo sviluppatore di creare nuove categorie di App e funzionalità. 

Per altre informazioni su tvOS 10, vedere di Apple [tvOS + app](https://developer.apple.com/tvos/) documentazione.

## <a name="whats-new-in-tvos-10"></a>What ' s New in tvOS 10

Apple ha aggiunto diverse nuove API e servizi in tvOS 10 insieme a molti miglioramenti alle funzionalità esistenti, tra cui:

## <a name="new-user-interface-styles"></a>Nuovi stili dell'interfaccia utente

tvOS 10 ora supporta sia un'interfaccia utente chiaro e scuro tema che tutti UIKit compilazione aggiuntivo controlli verranno automaticamente adattarsi alle, in base alle preferenze dell'utente.

Durante la creazione e implementazione di nuovi controlli dell'interfaccia utente personalizzati, lo sviluppatore deve usare la [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) classe per adattarsi al tema selezionato dell'utente.

Per altre informazioni, vedere la [nuovi stili dell'interfaccia utente](~/ios/tvos/platform/user-interface-styles.md) documentazione.

## <a name="security-and-privacy-enhancements"></a>Miglioramenti della protezione e Privacy

Apple ha apportato diversi miglioramenti sia sicurezza e privacy con tvOS 10 che aiuta gli sviluppatori migliorare la sicurezza delle proprie App e garantire la privacy dell'utente finale.

Di conseguenza, le App in esecuzione in watchOS 3 (o versioni successive) in modo statico devono dichiarare manifestato l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più delle chiavi specifici sulla Privacy in loro `Info.plist` file in cui viene illustrato all'utente perché l'app desidera ottenere l'accesso.

Poiché tvOS 10 condivide queste modifiche con iOS 10, vedere di iOS 10 [miglioramenti della protezione e Privacy](~/ios/app-fundamentals/security-privacy.md) Guida per altre informazioni.

## <a name="video-subscriber-account"></a>Account sottoscrittore video

Nuovo per tvOS 10, il framework di Account sottoscrittore Video consente App tale supporto autenticato streaming o video on Demand per eseguire l'autenticazione con i rispettivi provider TV satellite o cable usando un'esperienza Single-Sign-in per l'utente finale.

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>Wide Color

tvOS 10 estende il supporto per formati pixel intervalli estesi e gli spazi colore a livello di gamma in tutto il sistema, tra cui Framework quali Core Graphics, immagine di base, bare Metal e AVFoundation. Supporto per i dispositivi dotati di schermi di ampia gamma di colori viene diminuito ulteriormente fornendo questo comportamento in tutto lo stack di grafica intero.

È inoltre `UIKit` è stato modificato a funzionare nella nuova esteso **sRGB** spazio colore, rendendo più semplice combinare i colori usati nel gamme di ampia gamma di colori senza riduzione significativa delle prestazioni.

Apple offre le seguenti procedure ottimali quando si lavora con i colori wide:

 - `UIColor` a questo punto viene utilizzato il sRGB spazio colore e non sarà più limitare i valori per il `0.0` a `1.0` intervallo. Se l'app si basa sul comportamento del clamp precedente, devono essere modificate per tvOS 10.
 - Se l'app esegue il rendering personalizzato della `UIImages`, usare le nuove [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) classe per specificare l'utilizzo dei formati estesi-range o standard-range.
 - Quando si usa un'API di basso livello, ad esempio Core Graphics o bare Metal per fornire l'elaborazione di immagini, l'app deve usare un intervallo esteso colore pixel e spazio di formato che supporta valori a virgola mobile a 16 bit. Se necessario, l'app dovrà limitare manualmente i valori dei componenti di colore.
 - Grafica di base, immagine di base e gli shader di Metal prestazioni offrono nuovi metodi per la conversione tra gli spazi due colore.

Per altre informazioni, vedere la [Introduzione a ampia gamma di colori](~/ios/platform/wide-color.md) Guida.

## <a name="newly-available-existing-frameworks"></a>Framework esistenti appena disponibili

Diversi Framework che non erano disponibili in iOS (e tvOS non), sono stati resi disponibili per tvOS 10, ad esempio:

 - ExternalAccessory
 - HomeKit
 - MultipeerConnectivity
 - Foto
 - ReplayKit
 - UserNotification

## <a name="additional-framework-changes"></a>Modifiche di altri Framework

Oltre alle modifiche principali framework e componenti aggiuntivi elencati in precedenza, Apple ha apportato numerose modifiche di altri framework secondaria tvOS 10.

Per altre informazioni, vedere la [modifiche aggiuntive del Framework](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) Guida.

## <a name="deprecated-apis"></a>API deprecate

Nessuna API o Framework sono deprecate per tvOS 10. Vedere di Apple [differenze tra API tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) documentazione per un elenco completo delle modifiche API.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Che cos'è una novità a tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
