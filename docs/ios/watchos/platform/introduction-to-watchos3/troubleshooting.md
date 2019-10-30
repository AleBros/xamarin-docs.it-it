---
title: watchos 3 risoluzione dei problemi
description: Questo documento offre diversi suggerimenti per la risoluzione dei problemi quando si lavora con watchos 3 in Novell. I suggerimenti sono correlati a attività, Apple Pay, aggiornamento in background, NSURLConnection, privacy e altro ancora.
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 093ac4a3242866413042de0b650433d4369ad35f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028246"
---
# <a name="watchos-3-troubleshooting"></a>watchos 3 risoluzione dei problemi

_Questo articolo fornisce alcuni suggerimenti per la risoluzione dei problemi per l'uso di watchos 3 in Novell Apple Watch app._

Questa pagina elenca alcuni problemi noti che possono verificarsi quando si usa watchos 3 con Novell e la soluzione a tali problemi.

## <a name="activities"></a>Attività

Per il corretto funzionamento della condivisione delle attività, tutti gli Apple Watch associati devono eseguire watchos 3.

Problemi noti:

- La risposta a una notifica di condivisione attività a volte ha esito negativo.
- La risposta a una notifica di condivisione attività con un messaggio potrebbe non riuscire.
- Il testo contestuale sopra un messaggio di notifica di condivisione attività non sarà corretto.

## <a name="apple-pay"></a>Apple Pay

Problemi noti:

- Se viene immessa una data di scadenza non corretta o un codice CW per una nuova assistenza per il pagamento nel Apple Pay, quando si **raggiunge il processo** in esecuzione si arresterà in modo anomalo
- Gli acquisti in-app Apple Pay che richiedono un numero PIN potrebbero arrestarsi in modo anomalo.

## <a name="auto-mac-unlock"></a>Sblocco automatico Mac

Se si usa watchos 3 Beta 2 (o versione successiva) e macOS Sierra Beta 2 (o versione successiva), se l'autenticazione a due fattori è abilitata nell'account iCloud dell'utente, può usare i Apple Watch per sbloccare automaticamente il Mac.

## <a name="background-refresh"></a>Aggiornamento in background

Se si violano le risorse di sistema, si verifica un arresto anomalo dell'app watchos 3 con i seguenti codici di eccezione:

- **0xc51bad01** : l'app ha utilizzato un tempo di CPU troppo elevato.
- **0xc51bad02** : l'app ha usato troppi tempi di incasso.
- **0xc51bad03** : l'app non dispone di un runtime sufficiente per completare l'attività corrente.

## <a name="clock"></a>Clock

Le complicazioni delle app Apple Watch appena installate possono essere visualizzate come vuote. Riavviare Apple Watch per risolvere il problema.

## <a name="connectivity"></a>Connettività

Problemi noti:

- watchos non richiede all'utente l'autorizzazione di accesso per i dati utente protetti nella Apple Watch. Concedere l'accesso all'app iPhone prima di usare i dati nell'app Watch.
- Il Apple Watch può entrare in uno stato in cui tutte le trasmissioni WatchConnectivity hanno esito negativo, riavviare il Apple Watch per correggere il problema.

## <a name="notifications"></a>Notifiche

Se un allegato multimediale è troppo grande, verrà visualizzato nell'iPhone dell'utente, ma non in Apple Watch.

## <a name="nsurlconnection"></a>NSURLConnection

Tutte le connessioni `NSURLConnection` che usano protocolli TLS meno recenti avranno esito negativo. Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è ora disabilitata per impostazione predefinita. Inoltre, l'API trasporto sicuro non supporta più SSLv3 ed è consigliabile che l'app smetta di usare la crittografia SHA-1 e 3DES il prima possibile.

A partire da watchos 3, la sicurezza delle connessioni SSL/TLS viene applicata rigorosamente da Apple. I servizi e le app interessati devono aggiornare i server Web per usare le versioni più recenti del protocollo TLS.

## <a name="nsurlsession"></a>NSURLSession

A partire da watchos 3, la proprietà `HTTPBodyStream` della classe `NSMutableURLRequest` deve essere impostata su un flusso non aperto poiché `NSURLConnection` e `NSURLSession` ora applicano esclusivamente questo requisito.

## <a name="privacy"></a>Privacy

Problemi noti:

Quando si lavora con gli URL `https://` sia `NSURLSession` che `NSURLConnection` non supportano più i pacchetti di crittografia RC4 durante l'handshake TLS. È possibile che venga generato uno dei codici di errore seguenti:

- **-1200 o-98** -per gli errori `NSURLErrorSecurityConnectionFailed` e SecureTransport.
- **-1200 [3:-9824]** -il caricamento http non è riuscito.
- **-1200** - `NSURLConnection` terminato con un errore.

A partire da watchos 3, la sicurezza delle connessioni SSL/TLS viene applicata rigorosamente da Apple. I servizi e le app interessati devono aggiornare i server Web per usare le versioni più recenti del protocollo TLS. Per ulteriori informazioni, vedere [NSURLConnection](#nsurlconnection) .

## <a name="snapshots"></a>Snapshot

Le app WatchKit che non hanno adottato la nuova API `HandelBackgroundTask` non riceveranno più aggiornamenti periodici in watchos 3. 

## <a name="watchkit"></a>WatchKit

Le scene SpriteKit e SceneKit verranno sospese quando un'app entra in background nel Dock watchos.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [Novità di watchos 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
