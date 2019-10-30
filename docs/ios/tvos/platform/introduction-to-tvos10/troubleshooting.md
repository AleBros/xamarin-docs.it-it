---
title: Risoluzione dei problemi relativi alle app tvOS 10 compilate con Novell
description: Questo articolo fornisce alcuni suggerimenti per la risoluzione dei problemi per l'uso di tvOS 10 in app Novell. Vengono descritti i problemi correlati all'App Store, alla compatibilità binaria, a CFNetwork HttpProtocol, CloudKit, Core Image, NSUserActivity e UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a6588dee675aee3e2580b70dfdea2920c6235775
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030606"
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

- Se si chiama `NSObject.ValueForKey`, una chiave `null` comporterà un'eccezione.
- Il riferimento a un tipo di carattere per nome quando si chiama `UIFont.WithName` provocherà un arresto anomalo.
- Sia `NSURLSession` che `NSURLConnection` non sono più pacchetti di crittografia RC4 durante l'handshake TLS per `http://` URL.
- Le app possono bloccarsi se modificano una geometria di SuperView nei metodi `ViewWillLayoutSubviews` o `LayoutSubviews`.
- Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è ora disabilitata per impostazione predefinita. Inoltre, l'API trasporto sicuro non supporta più SSLv3 ed è consigliabile che l'app smetta di usare la crittografia SHA-1 e 3DES il prima possibile.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocollo HTTP CFNetwork

È necessario impostare la proprietà `HTTPBodyStream` della classe `NSMutableURLRequest` su un flusso non aperto, dal momento che `NSURLConnection` e `NSURLSession` ora applicano esclusivamente questo requisito.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Le operazioni con esecuzione prolungata restituiranno _"non si è autorizzati a salvare il file"._ Errore.

<a name="CoreImage" />

## <a name="core-image"></a>Immagine principale

L'API `CIImageProcessor` supporta ora un numero di immagini di input arbitrario. `CIImageProcessor` API inclusa in tvOS 10 Beta 1 verrà rimossa.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Dopo un'operazione di consegna, la proprietà `UserInfo` di un oggetto `NSUserActivity` può essere vuota. Chiamare in modo esplicito `BecomeCurrent` oggetto NSUserActivity ' come soluzione alternativa corrente.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problemi noti:

- Le modifiche apportate all'aspetto in background di `UINavigationBar`, `UITabBar` o `UIToolBar` possono causare un passaggio di layout per risolvere il nuovo aspetto. Il tentativo di modificare questi aspetti all'interno di un evento `LayoutSubviews`, `UpdateConstraints`, `WillLayoutSubviews` o `DidUpdateSubviews` può produrre un ciclo di layout infinito.
- In tvOS 10, la chiamata del metodo `RemoveGestureRecognizer` di un oggetto `UIView` Annulla in modo esplicito qualsiasi riconoscitore di movimento in corso.
- I controller di visualizzazione presentati possono ora influenzare l'aspetto della barra di stato.
- tvOS 10 richiede che lo sviluppatore chiami `base.AwakeFromNib` durante la sottoclasse `UIViewController` ed eseguendo l'override del metodo `AwakeFromNib`.
- Le app con sottoclassi `UIView` personalizzate che eseguono l'override di `LayoutSubviews` e sporcano il layout prima di chiamare `base.LayoutSubviews` possibile attivare un ciclo di layout infinito in tvOS 10.
- Gli asset di immagini specifiche della direzione o di cui è possibile eseguire il capovolgimento non vengono invertiti quando assegnati a `UIButton` oggetti.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Novità di tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
