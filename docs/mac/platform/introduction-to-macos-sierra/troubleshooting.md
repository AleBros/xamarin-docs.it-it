---
title: Xamarin.Mac-Risoluzione dei problemi di macOS Sierra
description: Questo documento fornisce diversi suggerimenti per la risoluzione dei problemi per l'uso di macOS Sierra nelle app Xamarin.Mac. I suggerimenti sono correlati a Mac App Store, Apple Pay, compatibilità binaria, CFNetwork, CloudKit e altro ancora.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/22/2016
ms.openlocfilehash: a4e7f7169e4c7ec0ec2947e17b1434179f47488f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017031"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin.Mac-Risoluzione dei problemi di macOS Sierra

_Questo articolo fornisce alcuni suggerimenti per la risoluzione dei problemi per lavorare con macOS Sierra nelle app Xamarin.Mac._

Le sezioni seguenti elencano alcuni problemi noti che possono verificarsi quando si usa macOS Sierra con Xamarin.Mac e la soluzione a questi problemi:

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

- Se si chiama `NSObject.ValueForKey`, una chiave `null` comporterà un'eccezione.
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

L'API `CIImageProcessor` supporta ora un numero di immagini di input arbitrario. `CIImageProcessor` API inclusa in macOS Sierra beta 1 verrà rimossa.

<a name="Notifications" />

## <a name="notifications"></a>Notifiche

Quando si utilizzano le estensioni del contenuto delle notifiche, i controller di visualizzazione non vengono rilasciati correttamente e potrebbero verificarsi un arresto anomalo quando vengono raggiunti i limiti della memoria di estensione.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Dopo un'operazione di consegna, la proprietà `UserInfo` di un oggetto `NSUserActivity` può essere vuota. Chiamare in modo esplicito `BecomeCurrent` oggetto `NSUserActivity` come soluzione alternativa corrente.

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation richiede un URL sicuro (`https://`) per lavorare sia su iOS 10 che su macOS Sierra per impedire l'uso dannoso dei dati della località.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [Novità di macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
