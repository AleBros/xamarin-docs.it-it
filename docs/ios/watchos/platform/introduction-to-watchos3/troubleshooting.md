---
title: watchOS 3 risoluzione dei problemi
description: Questo documento fornisce alcuni suggerimenti sulla risoluzione dei problemi utili quando si lavora con watchOS 3 in Xamarin. Suggerimenti riguardano le attività, Apple Pay, aggiornamento in background, NSURLConnection, privacy e altro ancora.
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 6d2aaf12bd6c45f6268cf87a77d2ee03a9d7a888
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224032"
---
# <a name="watchos-3-troubleshooting"></a>watchOS 3 risoluzione dei problemi

_Questo articolo fornisce alcuni suggerimenti sulla risoluzione dei problemi per l'uso di watchOS 3 nelle App Xamarin Apple Watch._

Questa pagina elenca alcuni problemi noti che possono verificarsi quando si usa 3 watchOS con Xamarin e la soluzione per risolvere tali problemi.

## <a name="activities"></a>Attività

Per l'attività di condivisione per funzionare correttamente, tutte le espressioni di controllo Apple associato deve essere in esecuzione watchOS 3.

Problemi noti:

- In alcuni casi la risposta a una notifica di condivisione di attività ha esito negativo.
- La risposta a una notifica la condivisione di attività con un messaggio potrebbe non riuscire.
- Testo contesto di sopra di un messaggio di notifica delle attività di condivisione sarà errato.

## <a name="apple-pay"></a>Apple Pay

Problemi noti:

- Se una data di scadenza non corretto o codice CW viene inserito per un nuovo cura di pagamento in Apple Pay, al raggiungimento **successivo** si arresterà il processo in esecuzione.
- Gli acquisti in-App Apple Pay che richiedono un codice PIN potrebbero bloccarsi.

## <a name="auto-mac-unlock"></a>Lo sblocco automatico Mac

Con watchOS 3 beta 2 (o versioni successiva) e macOS Sierra beta 2 (o versione successiva), se l'autenticazione a due fattori è abilitata nell'account iCloud dell'utente, utilizzano i Apple Watch per auto sbloccare i loro computer Mac.

## <a name="background-refresh"></a>Aggiornamento in background

Che violano le risorse di sistema comporterà un arresto anomalo dell'app watchOS 3 con i codici di eccezione seguente:

- **0xc51bad01** -l'app utilizza una quantità eccessiva tempo di CPU.
- **0xc51bad02** -l'app utilizza una quantità eccessiva tempo parete.
- **0xc51bad03** -l'app non è sufficiente runtime per completare l'attività corrente.

## <a name="clock"></a>Clock

Complicazioni dalle App Apple Watch appena installata possono visualizzata come spazio vuoto. Riavviare il sistema di Apple Watch per risolvere il problema.

## <a name="connectivity"></a>Connettività

Problemi noti:

- watchOS non chiederà all'utente l'autorizzazione di accesso per i dati utente protetti sul Apple Watch. Concedere l'accesso nell'app iPhone prima di usare dati in app watch.
- L'Apple Watch può entrare in uno stato in cui tutte le trasmissioni WatchConnectivity hanno esito negativo, riavviare l'Apple Watch da risolvere.

## <a name="notifications"></a>Notifiche

Se un file multimediale allegato è troppo grande, si visualizzerà in iPhone dell'utente, ma non i Apple Watch.

## <a name="nsurlconnection"></a>NSURLConnection

Qualsiasi `NSURLConnection` connessioni usando i protocolli TLS precedenti avrà esito negativo. Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è disabilitata per impostazione predefinita. Inoltre, l'API di trasporto sicuro non supporta più SSLv3 ed è consigliabile che l'app interrompere l'uso della crittografia SHA-1 e 3DES appena possibile.

A partire da watchOS 3, protezione delle connessioni SSL/TLS è rigorosamente imposta da Apple. Le App e servizi interessati devono aggiornati i server web per usare le versioni del protocollo TLS più recenti.

## <a name="nsurlsession"></a>NSURLSession

A partire da watchOS 3, il `HTTPBodyStream` proprietà del `NSMutableURLRequest` classe deve essere impostata su un flusso aperto dopo `NSURLConnection` e `NSURLSession` ora applicherà in modo rigoroso questo requisito.

## <a name="privacy"></a>Privacy

Problemi noti:

Quando si lavora `https://` entrambi gli URL `NSURLSession` e `NSURLConnection` non supporterà più pacchetti di crittografia RC4 durante l'handshake TLS. Uno dei codici di errore seguenti può essere generato:

- **-1200 o-98** : per `NSURLErrorSecurityConnectionFailed` ed errori SecureTransport.
- **[3:-9824]-1200** -carico http non è riuscita.
- **-1200**  -  `NSURLConnection` terminato con errori.

A partire da watchOS 3, protezione delle connessioni SSL/TLS è rigorosamente imposta da Apple. Le App e servizi interessati devono aggiornati i server web per usare le versioni del protocollo TLS più recenti. Visualizzare [NSURLConnection](#nsurlconnection) sopra per altre informazioni.

## <a name="snapshots"></a>Snapshot

App WatchKit che non hanno adottato il nuovo `HandelBackgroundTask` API non riceverà più gli aggiornamenti periodici in watchOS 3. 

## <a name="watchkit"></a>WatchKit

Le scene SpriteKit e SceneKit verranno sospeso quando lo sfondo entra in un'app nel Dock watchOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Novità in watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
