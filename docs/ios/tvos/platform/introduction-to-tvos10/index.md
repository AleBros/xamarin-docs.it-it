---
title: Introduzione a tvOS 10
description: Questo articolo presenta tutte le API e le funzionalità nuove e modificate disponibili in tvOS 10 per gli sviluppatori Novell. tvOS.
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 1b362d91e14d85499099a0fed3637fb128474ef9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657416"
---
# <a name="introduction-to-tvos-10"></a>Introduzione a tvOS 10

_Questo articolo presenta tutte le API e le funzionalità nuove e modificate disponibili in tvOS 10 per gli sviluppatori Novell. tvOS._

Con il nuovo tvOS 10 SDK, Apple ha incluso nuove API e servizi che consentono allo sviluppatore di creare nuove categorie di app e funzionalità. 

Per altre informazioni su tvOS 10, vedere la documentazione di Apple [tvOS + Apps](https://developer.apple.com/tvos/) .

## <a name="whats-new-in-tvos-10"></a>Novità di tvOS 10

Apple ha aggiunto diverse nuove API e servizi in tvOS 10, oltre a numerosi miglioramenti alle funzionalità esistenti, tra cui:

## <a name="new-user-interface-styles"></a>Nuovi stili dell'interfaccia utente

tvOS 10 supporta ora sia un tema di interfaccia utente scuro che chiaro che tutti i controlli UIKit di compilazione verranno adattati automaticamente in base alle preferenze dell'utente.

Quando si creano e implementano nuovi controlli dell'interfaccia utente personalizzati, lo sviluppatore deve usare la classe [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) per adattarsi al tema selezionato dell'utente.

Per ulteriori informazioni, vedere la nuova documentazione relativa agli [stili dell'interfaccia utente](~/ios/tvos/platform/user-interface-styles.md) .

## <a name="security-and-privacy-enhancements"></a>Miglioramenti alla sicurezza e alla privacy

Apple ha apportato diversi miglioramenti alla sicurezza e alla privacy in tvOS 10 che aiuteranno lo sviluppatore a migliorare la sicurezza delle app e garantire la privacy degli utenti finali.

Di conseguenza, le app in esecuzione in watchos 3 (o versioni successive) devono dichiarare in modo statico l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più `Info.plist` chiavi specifiche per la privacy nei file che spiegano all'utente il motivo per cui l'app desidera ottenere l'accesso.

Poiché tvOS 10 condivide queste modifiche con iOS 10, vedere la guida ai miglioramenti per la [sicurezza e la privacy](~/ios/app-fundamentals/security-privacy.md) di iOS 10 per altre informazioni.

## <a name="video-subscriber-account"></a>Account Sottoscrittore video

Una novità di tvOS 10, il Framework di account Sottoscrittore video consente alle app che supportano flussi autenticati o video su richiesta di eseguire l'autenticazione con il provider TV via cavo o satellite usando un'esperienza Single Sign-on per l'utente finale.

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>Wide Color

tvOS 10 estende il supporto per i formati di pixel estesi e gli spazi dei colori a gamma ampia in tutto il sistema, inclusi i Framework come la grafica principale, le immagini di base, i metal e AVFoundation. Il supporto per i dispositivi con schermi Wide Color è ulteriormente facilitato fornendo questo comportamento nell'intero stack di grafica.

Inoltre, `UIKit` è stato modificato per funzionare nel nuovo spazio dei colori di **sRGB** esteso, semplificando la combinazione di colori in gamme di colori Wide senza perdita significativa delle prestazioni.

Apple offre le seguenti procedure consigliate per l'utilizzo di colori estesi:

- `UIColor`USA ora lo spazio dei colori di sRGB e non blocca più i valori `0.0` `1.0` nell'intervallo. Se l'app si basa sul comportamento del morsetto precedente, sarà necessario modificarla per tvOS 10.
- Se l'app esegue il rendering personalizzato `UIImages`di, usare la nuova classe [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) per specificare l'uso dei formati di intervallo esteso o standard.
- Quando si usa un'API di basso livello, ad esempio grafica di base o metal, per fornire l'elaborazione di immagini, l'app deve usare uno spazio dei colori con intervallo esteso e un formato pixel che supporti i valori a virgola mobile a 16 bit. Se necessario, l'app dovrà bloccare manualmente i valori dei componenti dei colori.
- I componenti grafici di base, le immagini di base e gli shader con prestazioni Metal forniscono tutti i nuovi metodi per la conversione tra i due spazi dei colori.

Per saperne di più, vedere la Guida [introduttiva a wide color](~/ios/platform/wide-color.md) .

## <a name="newly-available-existing-frameworks"></a>Framework esistenti appena disponibili

Diversi Framework disponibili in iOS (e non tvOS) sono stati resi disponibili per tvOS 10, ad esempio:

- ExternalAccessory
- HomeKit
- MultipeerConnectivity
- Foto
- ReplayKit
- UserNotification

## <a name="additional-framework-changes"></a>Modifiche aggiuntive del Framework

Oltre alle principali modifiche e aggiunte al Framework elencate in precedenza, Apple ha apportato molte modifiche di Framework secondarie aggiuntive in tvOS 10.

Per ulteriori informazioni, vedere la Guida alle [modifiche aggiuntive del Framework](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) .

## <a name="deprecated-apis"></a>API deprecate

Nessuna API o Framework è stato deprecato da tvOS 10. Per un elenco completo delle modifiche alle API, vedere la documentazione sulle [differenze delle API tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) di Apple.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Novità di tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
