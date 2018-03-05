---
title: Introduzione a tvOS 10
description: "Questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in tvOS 10 per gli sviluppatori Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 8bc379e507287cd609dca8440b1210b7f1514114
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-tvos-10"></a>Introduzione a tvOS 10

_Questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in tvOS 10 per gli sviluppatori Xamarin.tvOS._

Con il nuovo tvOS 10 Apple SDK include nuove API e servizi che consentono agli sviluppatori di creare nuove categorie di App e funzionalità. 

Per ulteriori informazioni su tvOS 10, vedere Apple [tvOS + app](https://developer.apple.com/tvos/) documentazione.

## <a name="whats-new-in-tvos-10"></a>Novità in tvOS 10

Apple ha aggiunto diversi servizi e le API di nuovo tvOS 10 insieme a molti miglioramenti alle funzionalità esistenti, tra cui:

## <a name="new-user-interface-styles"></a>Nuovi stili di interfaccia utente

tvOS 10 ora supporta sia un'interfaccia utente chiaro e scuro tema tutti UIKit compilazione aggiuntivo controlli verranno automaticamente adattarsi alle, in base alle preferenze dell'utente.

Durante la creazione e implementazione di nuovi controlli dell'interfaccia utente personalizzati, lo sviluppatore deve utilizzare il [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) classe per adattarsi a tema selezionato dell'utente.

Per ulteriori informazioni, vedere il nostro [gli stili dell'interfaccia utente nuova](~/ios/tvos/platform/user-interface-styles.md) documentazione.

## <a name="security-and-privacy-enhancements"></a>Miglioramenti di Privacy e sicurezza

Apple ha migliorato notevolmente sia sicurezza e privacy in tvOS 10 che lo sviluppatore consente di migliorare la sicurezza delle proprie App e garantire la privacy dell'utente finale.

Di conseguenza, App in esecuzione su watchOS 3 (o versione successiva) dichiarare in modo statico l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più Privacy chiavi specifiche nella loro `Info.plist` file segnalare all'utente perché l'app desidera accedere.

Poiché con iOS 10, 10 tvOS condivide queste modifiche, vedere il nostro iOS 10 [miglioramenti di Privacy e sicurezza](~/ios/app-fundamentals/security-privacy.md) Guida per ulteriori informazioni.

## <a name="video-subscriber-account"></a>Account del sottoscrittore video

Nuovo per tvOS 10, il framework di Video sottoscrittore Account consente alle App tale supporto autenticato streaming o video on Demand per l'autenticazione con il provider via cavo o satellite TV con un'esperienza Single Sign-in per l'utente finale.

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>Colore Wide

tvOS 10 estende il supporto per i formati di pixel di intervallo esteso e spazi colore ampia in tutto il sistema incluso Framework, ad esempio grafica di base, l'immagine di base, Metal e AVFoundation. Supporto per i dispositivi dotati di schermi di colori ampia ulteriormente è agevolato fornendo questo comportamento in tutto lo stack dell'intero grafico.

Inoltre, `UIKit` è stato modificato per funzionare nel nuovo estesi **sRGB** spazio colore, rendendo più semplice la combinazione di colori in gamme di colori ampia senza perdita significativa delle prestazioni.

Apple offre le seguenti procedure consigliate quando si lavora con colori wide:

 - `UIColor` ora utilizza sRGB spazio colore e non verranno più clamp valori per il `0.0` a `1.0` intervallo. Se l'applicazione si basa sul comportamento clamp precedente, è necessario modificare per tvOS 10.
 - Se l'app esegue il rendering personalizzato di `UIImages`, usare il nuovo [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) classe per specificare l'utilizzo dei formati di intervallo esteso o standard-range.
 - Quando si utilizza un'API di basso livello, ad esempio grafica di base o Metal per fornire l'elaborazione di immagini, l'applicazione deve utilizzare un intervallo esteso colore pixel e spazio formato che supporta valori a virgola mobile a 16 bit. Se necessario, l'app dovrà clamp manualmente i valori dei componenti di colore.
 - Grafica di base, immagine di Core e Metal prestazioni shader fornire nuovi metodi per la conversione tra gli spazi di due colori.

Per ulteriori dettagli, vedere il nostro [Introduzione ai colori ampia](~/ios/platform/wide-color.md) Guida.

## <a name="newly-available-existing-frameworks"></a>Framework esistenti rese disponibili

Alcuni Framework che non era disponibile in iOS (e non tvOS), sono state rese disponibili per tvOS, 10, ad esempio:

 - ExternalAccessory
 - HomeKit
 - MultipeerConnectivity
 - Foto
 - ReplayKit
 - UserNotification

## <a name="additional-framework-changes"></a>Modifiche di .NET Framework

Oltre alle modifiche framework principali e le aggiunte elencate in precedenza, Apple ha molte modifiche aggiuntive framework secondaria tvOS 10.

Per ulteriori dettagli, consultare il nostro [ulteriori modifiche di Framework](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) Guida.

## <a name="deprecated-apis"></a>API deprecate

Nessuna API o Framework sono state deprecate per tvOS 10. Vedere Apple [differenze tra API tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) documentazione per un elenco completo delle modifiche delle API.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novità di tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
