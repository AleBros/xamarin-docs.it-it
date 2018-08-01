---
title: Risoluzione dei problemi tvOS 10 App compilata con Xamarin
description: Questo articolo fornisce alcuni suggerimenti sulla risoluzione dei problemi per l'utilizzo di tvOS 10 in App Xamarin. Descrive i problemi relativi all'archivio di App, la compatibilità binaria, il CFNetwork HttpProtocol, CloudKit, immagine di Core, NSUserActivity e UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 4332caca2804da52bb565fe382932af691c39dab
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788810"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Risoluzione dei problemi tvOS 10 App compilata con Xamarin

Le sezioni seguenti elencano alcuni problemi noti che possono verificarsi quando si utilizza tvOS 10 con Xamarin e la soluzione a questi problemi:

- [App Store](#App-Store)
- [Compatibilità binaria](#Binary-Compatibility)
- [Protocollo HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Immagine di base](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemi noti:

 - Quando test acquisti In-App nell'ambiente sandbox, è possibile che la finestra di dialogo di autenticazione vengano visualizzati due volte.
 - Quando si verifica acquisti In-App con il contenuto ospitato nell'ambiente sandbox, verrà visualizzata la finestra di dialogo password ogni volta che l'app viene portata in primo piano fino al completamento del download del contenuto.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilità binaria

Problemi noti:

 - La chiamata `NSObject.ValueForKey` sarà un `null` chiave genererà un'eccezione.
 - Riferimento a un tipo di carattere in base al nome quando si chiama `UIFont.WithName` causa un arresto anomalo.
 - Entrambi `NSURLSession` e NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http://' URL.
 - App possono bloccarsi se modificano la geometria della superview una in entrambi i `ViewWillLayoutSubviews` o `LayoutSubviews` metodi.
 - Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è disabilitata per impostazione predefinita. Inoltre, l'API di trasporto protetto non supporta più crittografia SSLv3 e, è consigliabile che l'app interrompere l'uso della crittografia SHA-1 e 3DES appena possibile.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocollo HTTP CFNetwork

Il `HTTPBodyStream` proprietà del `NSMutableURLRequest` classe deve essere impostata su un flusso aperto dopo `NSURLConnection` e `NSURLSession` rigorosamente ad applicare questo requisito.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Operazioni a esecuzione prolungata restituirà un _"non dispone dell'autorizzazione per salvare il file"._ Errore.

<a name="CoreImage" />

## <a name="core-image"></a>Immagine di base

Il `CIImageProcessor` API supporta ora un conteggio delle immagini di input non autorizzato. `CIImageProcessor` API che è stato incluso nella versione beta tvOS 10 1 verrà rimossa.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Dopo un'operazione di passaggio di consegne il `UserInfo` proprietà di un `NSUserActivity` oggetto può essere vuoto. Chiamare in modo esplicito `BecomeCurrent` NSUserActivity' oggetto come soluzione alternativa corrente.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problemi noti:

 - Diventa l'aspetto dello sfondo `UINavigationBar`, `UITabBar` o `UIToolBar` può comportare un passaggio di layout per risolvere il nuovo aspetto. Il tentativo di modificare questi aspetti all'interno di un `LayoutSubviews`, `UpdateConstraints`, `WillLayoutSubviews` o `DidUpdateSubviews` eventi possono causare un ciclo infinito di layout.
 - In tvOS 10, la chiamata di `RemoveGestureRecognizer` metodo di un `UIView` oggetto Annulla in modo esplicito qualsiasi riconoscitore di movimento in corso.
 - Presentato visualizzazione controller può ora influiscono sull'aspetto della barra di stato.
 - tvOS 10 richiede allo sviluppatore di chiamare `base.AwakeFromNib` quando si crea una sottoclasse `UIViewController` ed eseguendo l'override di `AwakeFromNib` metodo.
 - App con personalizzato `UIView` sottoclassi che eseguono l'override `LayoutSubviews` e il layout prima di chiamare dirty `base.LayoutSubviews` può attivare un ciclo infinito di layout in tvOS 10.
 - Gli asset di immagini specifiche di direzione o flippable non sono alcun capovolgimento quando assegnato a `UIButton` oggetti.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novità di tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
