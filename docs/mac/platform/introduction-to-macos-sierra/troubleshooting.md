---
title: Novell. Mac-Risoluzione dei problemi di macOS Sierra
description: Questo documento fornisce diversi suggerimenti per la risoluzione dei problemi per l'uso di macOS Sierra nelle app Novell. Mac. I suggerimenti sono correlati a Mac App Store, Apple Pay, compatibilità binaria, CFNetwork, CloudKit e altro ancora.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 09/22/2016
ms.openlocfilehash: 3bb2acd5ef560afa787c2746133c05066a15cf9e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511805"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Novell. Mac-Risoluzione dei problemi di macOS Sierra

_Questo articolo fornisce alcuni suggerimenti per la risoluzione dei problemi per lavorare con macOS Sierra nelle app Novell. Mac._

Le sezioni seguenti elencano alcuni problemi noti che possono verificarsi quando si usa macOS Sierra con Novell. Mac e la soluzione a questi problemi:

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [Compatibilità binaria](#Binary-Compatibility)
- [Protocollo HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Immagine principale](#CoreImage)
- [Notifiche](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemi noti:

- Quando si verificano gli acquisti in-app nell'ambiente sandbox, è possibile che la finestra di dialogo di autenticazione venga visualizzata due volte.
- Quando si verificano gli acquisti in-app con contenuto ospitato nell'ambiente sandbox, la finestra di dialogo password viene visualizzata ogni volta che l'app viene portata in primo piano fino al completamento del download del contenuto.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Se viene immesso un codice di sicurezza o una data di scadenza non corretta durante l'aggiunta di una nuova carta di pagamento a Apple Pay, il processo di provisioning delle schede verrà terminato.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilità binaria

Problemi noti:

- Se `NSObject.ValueForKey` si chiama `null` , una chiave genererà un'eccezione.
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

L' `CIImageProcessor` API supporta ora un numero di immagini di input arbitrario. `CIImageProcessor`L'API inclusa in macOS Sierra beta 1 verrà rimossa.

<a name="Notifications" />

## <a name="notifications"></a>Notifiche

Quando si utilizzano le estensioni del contenuto delle notifiche, i controller di visualizzazione non vengono rilasciati correttamente e potrebbero verificarsi un arresto anomalo quando vengono raggiunti i limiti della memoria di estensione.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Dopo un'operazione di continuità, `UserInfo` la proprietà di `NSUserActivity` un oggetto potrebbe essere vuota. `BecomeCurrent` Chiama`NSUserActivity` in modo esplicito Object come soluzione alternativa corrente.

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation richiede un URL sicuro`https://`() per lavorare sia su iOS 10 che su MacOS Sierra per impedire l'uso dannoso dei dati della località.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di Mac](https://developer.xamarin.com/samples/mac/)
- [Novità di macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
