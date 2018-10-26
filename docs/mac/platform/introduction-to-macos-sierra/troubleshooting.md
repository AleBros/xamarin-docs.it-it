---
title: Xamarin. Mac - macOS Sierra risoluzione dei problemi
description: Questo documento fornisce alcuni suggerimenti sulla risoluzione dei problemi per l'utilizzo con macOS Sierra nelle App xamarin. Mac. Suggerimenti riguardano la Store di App Mac, Apple Pay, la compatibilità binaria, CFNetwork, CloudKit e altro ancora.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 09/22/2016
ms.openlocfilehash: 1b379bef98e498df4c58ba7209aa46b0b2542fe1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108836"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin. Mac - macOS Sierra risoluzione dei problemi

_Questo articolo fornisce alcuni suggerimenti sulla risoluzione dei problemi per l'utilizzo con macOS Sierra nelle App xamarin. Mac._

Le sezioni seguenti elencano alcuni problemi noti che possono verificarsi quando si usa macOS Sierra con xamarin. Mac e la soluzione per risolvere tali problemi:

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [Compatibilità binaria](#Binary-Compatibility)
- [Protocollo HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Immagine di base](#CoreImage)
- [Notifiche](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemi noti:

- Quando acquisti In-App di test nell'ambiente sandbox, la finestra di dialogo di autenticazione vengano visualizzati due volte.
- Durante il test di acquisti In-App con il contenuto ospitato nell'ambiente sandbox, verrà visualizzata la finestra di dialogo password ogni volta che l'app viene portato in primo piano fino a quando non viene completato il download del contenuto.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Se un scadenza errato data sicurezza codice o (CW Closest) viene immesso quando si aggiunge una nuova carta di pagamento per Apple Pay, la scheda processo di provisioning verrà terminata.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilità binaria

Problemi noti:

- La chiamata `NSObject.ValueForKey` sarà un `null` chiave verrà generata un'eccezione.
- Entrambe `NSURLSession` NSURLConnection e` no longer RC4 cipher suites during the TLS handshake for `http://' URL.
- Le app possono bloccarsi se modificano la geometria della superview una in entrambi i `ViewWillLayoutSubviews` o `LayoutSubviews` metodi.
- Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è disabilitata per impostazione predefinita. Inoltre, l'API di trasporto sicuro non supporta più SSLv3 ed è consigliabile che l'app interrompere l'uso della crittografia SHA-1 e 3DES appena possibile.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocollo HTTP CFNetwork

Il `HTTPBodyStream` proprietà del `NSMutableURLRequest` classe deve essere impostata su un flusso aperto dopo `NSURLConnection` e `NSURLSession` ora applicherà in modo rigoroso questo requisito.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Operazioni a esecuzione prolungata restituirà un _"Non si è autorizzati a salvare il file."_ Errore.

<a name="CoreImage" />

## <a name="core-image"></a>Immagine di base

Il `CIImageProcessor` API supporta ora un numero di immagini input arbitrario. `CIImageProcessor` API che è stato incluso in macOS beta Sierra 1 verrà rimossa.

<a name="Notifications" />

## <a name="notifications"></a>Notifiche

Quando si lavora con le estensioni del contenuto di notifica, i controller di visualizzazione non vengono rilasciati in modo corretto e potrebbe causare un arresto anomalo quando vengono raggiunti i limiti di memoria di estensione.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Dopo un'operazione di consegna, il `UserInfo` proprietà di un `NSUserActivity` oggetto potrebbe essere vuoto. Chiamare in modo esplicito `BecomeCurrent` `NSUserActivity` oggetto come soluzione alternativa corrente.

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation richiede sicuro (`https://`) per lavorare in iOS 10 e macOS Sierra per evitare l'uso dannoso di dati del percorso URL.







## <a name="related-links"></a>Collegamenti correlati

- [Esempi di Mac](https://developer.xamarin.com/samples/mac/)
- [Novità in OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
