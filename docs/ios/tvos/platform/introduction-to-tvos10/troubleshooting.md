---
title: Risoluzione dei problemi relativi alle app tvOS 10 compilate con Novell
description: Questo articolo fornisce alcuni suggerimenti per la risoluzione dei problemi per l'uso di tvOS 10 in app Novell. Vengono descritti i problemi correlati all'App Store, alla compatibilità binaria, a CFNetwork HttpProtocol, CloudKit, Core Image, NSUserActivity e UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: c734bfdd1baaf89cf25d687657e5f14bdbc7834c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283444"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Risoluzione dei problemi relativi alle app tvOS 10 compilate con Novell

Le sezioni seguenti elencano alcuni problemi noti che possono verificarsi quando si usa tvOS 10 con Novell e la soluzione a questi problemi:

- [App Store](#App-Store)
- [Compatibilità binaria](#Binary-Compatibility)
- [Protocollo HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Immagine principale](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemi noti:

- Quando si verificano gli acquisti in-app nell'ambiente sandbox, è possibile che la finestra di dialogo di autenticazione venga visualizzata due volte.
- Quando si verificano gli acquisti in-app con contenuto ospitato nell'ambiente sandbox, la finestra di dialogo password viene visualizzata ogni volta che l'app viene portata in primo piano fino al completamento del download del contenuto.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilità binaria

Problemi noti:

- Se `NSObject.ValueForKey` si chiama `null` , una chiave genererà un'eccezione.
- Se si fa riferimento a un tipo `UIFont.WithName` di carattere per nome quando si chiama, verrà generato un arresto
- Sia `NSURLSession` `http://` che `NSURLConnection` non sono più pacchetti di crittografia RC4 durante l'handshake TLS per gli URL.
- Le app possono bloccarsi se modificano la `ViewWillLayoutSubviews` geometria di una supervisione nei metodi o. `LayoutSubviews`
- Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è ora disabilitata per impostazione predefinita. Inoltre, l'API trasporto sicuro non supporta più SSLv3 ed è consigliabile che l'app smetta di usare la crittografia SHA-1 e 3DES il prima possibile.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocollo HTTP CFNetwork

La `HTTPBodyStream` proprietà `NSURLConnection` `NSURLSession` della classe deve essere impostata su un flusso non aperto a partire da e ora impone rigorosamente questo requisito. `NSMutableURLRequest`

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Le operazioni con esecuzione prolungata restituiranno _"non si è autorizzati a salvare il file"._ errore.

<a name="CoreImage" />

## <a name="core-image"></a>Immagine principale

L' `CIImageProcessor` API supporta ora un numero di immagini di input arbitrario. `CIImageProcessor`L'API inclusa in tvOS 10 Beta 1 verrà rimossa.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Dopo un'operazione di continuità, `UserInfo` la proprietà di `NSUserActivity` un oggetto potrebbe essere vuota. Chiamare `BecomeCurrent` in modo esplicito l'oggetto NSUserActivity come soluzione alternativa corrente.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problemi noti:

- Le modifiche apportate all' `UINavigationBar`aspetto `UITabBar` dello `UIToolBar` sfondo di o possono causare un passaggio di layout per risolvere il nuovo aspetto. Il tentativo di modificare questi aspetti all'interno di un `LayoutSubviews`evento `UpdateConstraints` `WillLayoutSubviews` , o `DidUpdateSubviews` può causare un ciclo di layout infinito.
- In tvOS 10, la chiamata `RemoveGestureRecognizer` al metodo di `UIView` un oggetto annulla in modo esplicito qualsiasi riconoscimento di movimento in corso.
- I controller di visualizzazione presentati possono ora influenzare l'aspetto della barra di stato.
- tvOS 10 richiede che lo sviluppatore chiami `base.AwakeFromNib` quando viene `UIViewController` sottoclassato ed eseguendo l' `AwakeFromNib` override del metodo.
- Le app con `UIView` sottoclassi personalizzate che eseguono `LayoutSubviews` l'override e lo sporco del `base.LayoutSubviews` layout prima di chiamare possono attivare un ciclo di layout infinito in tvOS 10.
- Gli asset di immagini specifiche della direzione o di cui è possibile eseguire il capovolgimento `UIButton` non vengono invertiti quando assegnati a oggetti.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Novità di tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
