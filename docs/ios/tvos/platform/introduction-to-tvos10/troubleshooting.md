---
title: Risoluzione dei problemi relativi alle app tvOS 10 compilate con Novell
description: Questo articolo fornisce alcuni suggerimenti per la risoluzione dei problemi per l'uso di tvOS 10 in app Novell. Vengono descritti i problemi correlati all'App Store, alla compatibilità binaria, a CFNetwork HttpProtocol, CloudKit, Core Image, NSUserActivity e UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: cd04450d1429092453e6d8b65278d87b5d52e45e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571623"
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

<a name="App-Store"></a>

## <a name="app-store"></a>App Store

Problemi noti:

- Quando si verificano gli acquisti in-app nell'ambiente sandbox, è possibile che la finestra di dialogo di autenticazione venga visualizzata due volte.
- Quando si verificano gli acquisti in-app con contenuto ospitato nell'ambiente sandbox, la finestra di dialogo password viene visualizzata ogni volta che l'app viene portata in primo piano fino al completamento del download del contenuto.

<a name="Binary-Compatibility"></a>

## <a name="binary-compatibility"></a>Compatibilità binaria

Problemi noti:

- Se `NSObject.ValueForKey` si chiama `null` , una chiave genererà un'eccezione.
- Se si fa riferimento a un tipo di carattere per nome quando `UIFont.WithName` si chiama, verrà generato un arresto
- Sia `NSURLSession` che `NSURLConnection` non sono più pacchetti di crittografia RC4 durante l'handshake TLS per gli `http://` URL.
- Le app possono bloccarsi se modificano la geometria di una supervisione `ViewWillLayoutSubviews` nei `LayoutSubviews` metodi o.
- Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è ora disabilitata per impostazione predefinita. Inoltre, l'API trasporto sicuro non supporta più SSLv3 ed è consigliabile che l'app smetta di usare la crittografia SHA-1 e 3DES il prima possibile.

<a name="CFNetwork-HTTP-Protocol"></a>

## <a name="cfnetwork-http-protocol"></a>Protocollo HTTP CFNetwork

La `HTTPBodyStream` proprietà della `NSMutableURLRequest` classe deve essere impostata su un flusso non aperto a partire da `NSURLConnection` e `NSURLSession` ora impone rigorosamente questo requisito.

<a name="CloudKit"></a>

## <a name="cloudkit"></a>CloudKit

Le operazioni con esecuzione prolungata restituiranno _"non si è autorizzati a salvare il file"._ .

<a name="CoreImage"></a>

## <a name="core-image"></a>Immagine principale

L' `CIImageProcessor` API supporta ora un numero di immagini di input arbitrario. `CIImageProcessor`L'API inclusa in tvOS 10 Beta 1 verrà rimossa.

<a name="NSUserActivity"></a>

## <a name="nsuseractivity"></a>NSUserActivity

Dopo un'operazione di continuità, la `UserInfo` proprietà di un `NSUserActivity` oggetto potrebbe essere vuota. Chiamare in modo esplicito `BecomeCurrent` l'oggetto NSUserActivity come soluzione alternativa corrente.

<a name="UIKit"></a>

## <a name="uikit"></a>UIKit

Problemi noti:

- Le modifiche apportate all'aspetto dello sfondo di `UINavigationBar` `UITabBar` o `UIToolBar` possono causare un passaggio di layout per risolvere il nuovo aspetto. Il tentativo di modificare questi aspetti all'interno di un `LayoutSubviews` `UpdateConstraints` evento, `WillLayoutSubviews` o `DidUpdateSubviews` può causare un ciclo di layout infinito.
- In tvOS 10, la chiamata al `RemoveGestureRecognizer` metodo di un `UIView` oggetto annulla in modo esplicito qualsiasi riconoscimento di movimento in corso.
- I controller di visualizzazione presentati possono ora influenzare l'aspetto della barra di stato.
- tvOS 10 richiede che lo sviluppatore chiami `base.AwakeFromNib` quando viene sottoclassato `UIViewController` ed eseguendo l'override del `AwakeFromNib` metodo.
- Le app con `UIView` sottoclassi personalizzate che eseguono l'override `LayoutSubviews` e lo sporco del layout prima `base.LayoutSubviews` di chiamare possono attivare un ciclo di layout infinito in tvOS 10.
- Gli asset di immagini specifiche della direzione o di cui è possibile eseguire il capovolgimento non vengono invertiti quando assegnati a `UIButton` oggetti.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Novità di tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
