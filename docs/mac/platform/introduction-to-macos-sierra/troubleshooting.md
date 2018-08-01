---
title: Xamarin.Mac - macOS Sierra risoluzione dei problemi
description: Questo documento vengono forniti numerosi suggerimenti sulla risoluzione dei problemi per l'utilizzo di macOS Sierra nelle App Xamarin.Mac. Suggerimenti relativi alla Mac App Store, Apple Pay, la compatibilità binaria, CFNetwork, CloudKit e altro ancora.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/22/2016
ms.openlocfilehash: 5b2571d9562fd137257e2dd0ea2ada8f071bab92
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792325"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin.Mac - macOS Sierra risoluzione dei problemi

_Questo articolo fornisce alcuni suggerimenti sulla risoluzione dei problemi per l'utilizzo di macOS Sierra nelle App Xamarin.Mac._

Le sezioni seguenti elencano alcuni problemi noti che possono verificarsi quando si utilizza macOS Sierra con Xamarin.mac e la soluzione a questi problemi:

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

- Quando test acquisti In-App nell'ambiente sandbox, è possibile che la finestra di dialogo di autenticazione vengano visualizzati due volte.
- Quando si verifica acquisti In-App con il contenuto ospitato nell'ambiente sandbox, verrà visualizzata la finestra di dialogo password ogni volta che l'app viene portata in primo piano fino al completamento del download del contenuto.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Se viene immesso un scadenza corretto data o sicurezza codice (CW) quando si aggiunge una nuova scheda di pagamento per pagare Apple, scheda processo di provisioning verrà terminata.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilità binaria

Problemi noti:

- La chiamata `NSObject.ValueForKey` sarà un `null` chiave genererà un'eccezione.
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

Il `CIImageProcessor` API supporta ora un conteggio delle immagini di input non autorizzato. `CIImageProcessor` API che è stata inclusa nel macOS beta Sierra 1 verrà rimossa.

<a name="Notifications" />

## <a name="notifications"></a>Notifiche

Quando si utilizzano le estensioni di contenuto di notifica, visualizzazione controller non sono viene rilasciato correttamente e potrebbe causare un arresto anomalo quando vengono raggiunti i limiti di memoria di estensione.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Dopo un'operazione di passaggio di consegne il `UserInfo` proprietà di un `NSUserActivity` oggetto può essere vuoto. Chiamare in modo esplicito `BecomeCurrent` `NSUserActivity` oggetto come soluzione alternativa corrente.

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation richiede sicura (`https://`) URL per lavorare su iOS 10 e macOS Sierra per impedire l'uso dannoso di dati sulla località.







## <a name="related-links"></a>Collegamenti correlati

- [Esempi di Mac](https://developer.xamarin.com/samples/mac/)
- [Novità di OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
