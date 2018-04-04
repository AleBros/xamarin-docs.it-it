---
title: watchOS 3 alla risoluzione dei problemi
description: Questo articolo fornisce alcuni suggerimenti sulla risoluzione dei problemi per l'utilizzo di watchOS 3 in App Xamarin Apple Watch.
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 159c6a6dadcaa325abc7fd747abc9b2ba2f26a9c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="watchos-3-troubleshooting"></a>watchOS 3 alla risoluzione dei problemi

_Questo articolo fornisce alcuni suggerimenti sulla risoluzione dei problemi per l'utilizzo di watchOS 3 in App Xamarin Apple Watch._

Questa pagina elenca alcuni problemi noti che possono verificarsi quando si utilizza watchOS 3 con Xamarin e la soluzione per risolvere tali problemi.

## <a name="activities"></a>Attività

Per l'attività di condivisione per il corretto funzionamento, abbinata Apple espressioni di controllo deve essere in esecuzione watchOS 3.

Problemi noti:

- Talvolta, la risposta a una notifica di condivisione delle attività ha esito negativo.
- La risposta a una notifica di condivisione di attività con un messaggio potrebbe non riuscire.
- Testo contesto di sopra di un messaggio di notifica di condivisione delle attività saranno corretto.


## <a name="apple-pay"></a>Apple Pay

Problemi noti:

- Se per un nuovo attenzione pagamento Apple Pay, viene immesso un codice di peso variabile o la data di scadenza non corretto quando raggiunge **Avanti** arresterà il processo in esecuzione.
- Acquisti in-App Apple Pay richiede un numero PIN potrebbero bloccarsi.



## <a name="auto-mac-unlock"></a>Lo sblocco automatico Mac

Tramite watchOS 3 beta 2 (o versioni successive) e macOS beta di Sierra 2 (o versioni successive), se l'autenticazione a due fattori è abilitata nell'account iCloud dell'utente, utilizzano i Apple Watch automaticamente sbloccare il Mac.



## <a name="background-refresh"></a>Aggiornamento in background

Violare le risorse di sistema comporterà un arresto anomalo app watchOS 3 con i seguenti codici di eccezione:

- **0xc51bad01** -l'applicazione utilizza una quantità eccessiva tempo CPU.
- **0xc51bad02** -l'app utilizzata troppa il tempo.
- **0xc51bad03** -app non dispone di sufficiente runtime per completare l'operazione corrente.



## <a name="clock"></a>Clock

Complessità da app di Apple Watch appena installata potrebbero essere visualizzate come spazio vuoto. Riavviare il computer Apple Watch per risolvere il problema.


## <a name="connectivity"></a>Connettività

Problemi noti:

- watchOS non richiederà all'utente l'autorizzazione di accesso per i dati utente protetto sul Apple Watch. Concedere l'accesso nell'app iPhone prima di usare i dati nell'applicazione di espressioni di controllo.
- L'Apple Watch possono entrare in uno stato in cui tutte le trasmissioni WatchConnectivity hanno esito negativo, riavviare il Apple Watch correggere.


## <a name="notifications"></a>Notifiche

Se un allegato di supporto è troppo grande, si visualizzerà su iPhone dell'utente ma non i relativi Apple Watch.


## <a name="nsurlconnection"></a>NSURLConnection

Qualsiasi `NSURLConnection` le connessioni che utilizzano protocolli TLS precedenti avrà esito negativo. Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è disabilitata per impostazione predefinita. Inoltre, l'API di trasporto protetto non supporta più crittografia SSLv3 e, è consigliabile che l'app interrompere l'uso della crittografia SHA-1 e 3DES appena possibile.

A partire da watchOS 3, protezione delle connessioni SSL/TLS è rigorosamente imposta da Apple. Le applicazioni e servizi interessati devono aggiornati i server web per utilizzare le versioni più recenti del protocollo TLS.


## <a name="nsurlsession"></a>NSURLSession

A partire da watchOS 3, il `HTTPBodyStream` proprietà del `NSMutableURLRequest` classe deve essere impostata su un flusso aperto dopo `NSURLConnection` e `NSURLSession` rigorosamente ad applicare questo requisito.


## <a name="privacy"></a>Privacy

Problemi noti:

Quando si lavora con `https://` entrambi gli URL `NSURLSession` e `NSURLConnection` non supportano più pacchetti di crittografia RC4 durante l'handshake TLS. Uno dei seguenti codici di errore può essere generato:

- **-1200 o-98** : per `NSURLErrorSecurityConnectionFailed` e SecureTransport errori.
- **-1200 [3:-9824]** -carico http non riuscita.
- **-1200**  -  `NSURLConnection` terminato con errori.

A partire da watchOS 3, protezione delle connessioni SSL/TLS è rigorosamente imposta da Apple. Le applicazioni e servizi interessati devono aggiornati i server web per utilizzare le versioni più recenti del protocollo TLS. Vedere [NSURLConnection](#NSURLConnection) sopra per altre informazioni.


## <a name="snapshots"></a>Snapshot

App WatchKit che non hanno adottato il nuovo `HandelBackgroundTask` API non riceveranno più aggiornamenti periodici in watchOS 3. 


## <a name="watchkit"></a>WatchKit

Quando un'app entra in background in watchOS Dock, scene SpriteKit e SceneKit verranno messa in pausa.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Novità di watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
