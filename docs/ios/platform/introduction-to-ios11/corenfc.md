---
title: Core NFC in xamarin. IOS
description: Questo documento descrive come leggere in prossimità di tag di comunicazione di campo in xamarin. IOS usando le API introdotte in iOS 11.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: lobrien
ms.author: laobri
ms.date: 09/25/2017
ms.openlocfilehash: 6888f7147796d3c00752d10387c19d0d9f269cad
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106819"
---
# <a name="core-nfc-in-xamarinios"></a>Core NFC in xamarin. IOS

_Tag di lettura quasi Field Communication (NFC) con iOS 11_

CoreNFC è un nuovo framework in iOS 11 che fornisce l'accesso per il _Near Field Communication_ radio (NFC) per leggere i tag da all'interno delle app. Opera su iPhone 7, Plus 7, 8, 8 Plus e X.

Il lettore di tag NFC in dispositivi iOS supporta tutti i tipi di tag NFC da 1 a 5 che contengono _formato di scambio dati NFC_ informazioni (NDEF).

Esistono alcune limitazioni da tenere presenti:

- CoreNFC supporta solo la lettura (non la scrittura o la formattazione) di tag.
- Analisi di tag devono essere avviata dall'utente e di timeout dopo 60 secondi.
- Le app devono essere visibili in primo piano per l'analisi.
- CoreNFC può essere testato solo su dispositivi reali (non nel simulatore).

Questa pagina descrive la configurazione necessaria per usare CoreNFC e illustra come usare l'API tramite il ["NFCTagReader" codice di esempio](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/).

## <a name="configuration"></a>Configurazione

Per abilitare CoreNFC, è necessario configurare tre elementi nel progetto:

- Un' **Info. plist** chiave per la privacy.
- Un' **entitlements. plist** voce.
- Un profilo di provisioning con **lettura di Tag NFC** funzionalità.

### <a name="infoplist"></a>Info. plist

Aggiungere il **NFCReaderUsageDescription** chiave per la privacy e testo, che viene visualizzato all'utente mentre l'analisi è in corso. Uso di un messaggio appropriato per l'applicazione (ad esempio, lo scopo dell'analisi di una spiegazione):

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

L'app deve richiedere il **quasi lettura Tag di campo le comunicazioni** funzionalità usando il chiave/valore seguente coppia nelle **entitlements. plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>Profilo di provisioning

Creare una nuova **App ID** e assicurarsi che il **lettura di Tag NFC** service sia selezionata:

[![Pagina nuovo ID App di portale per sviluppatori con la lettura di Tag NFC selezionato](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

Si deve quindi creare un nuovo profilo di provisioning per questo ID App, scaricare e installarlo di sviluppo Mac.

## <a name="reading-a-tag"></a>La lettura di un Tag

Dopo aver configurato il progetto, aggiungere `using CoreNFC;` all'inizio del file e seguire questi tre passaggi per implementare NFC funzionalità durante la lettura di tag:

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. Implementare `INFCNdefReaderSessionDelegate`

L'interfaccia dispone di due metodi da implementare:

- `DidDetect` : Viene chiamato quando un tag è stato letto correttamente.
- `DidInvalidate` : Viene chiamato quando si verifica un errore o viene raggiunto il timeout di 60 secondi.

#### <a name="diddetect"></a>DidDetect

Nel codice di esempio, ogni messaggio viene aggiunto a una visualizzazione di tabella:

```csharp
public void DidDetect(NFCNdefReaderSession session, NFCNdefMessage[] messages)
{
    foreach (NFCNdefMessage msg in messages)
    {  // adds the messages to a list view
        DetectedMessages.Add(msg);
    }
    DispatchQueue.MainQueue.DispatchAsync(() =>
    {
        this.TableView.ReloadData();
    });
}
```

Questo metodo può essere chiamato più volte (e una matrice di messaggi può essere passata) se la sessione consente più letture tag. È impostato tramite il terzo parametro della `Start` metodo (illustrato nella [passaggio 2](#step2)).

#### <a name="didinvalidate"></a>DidInvalidate

L'invalidamento può verificarsi per diversi motivi:

- Si è verificato un errore durante l'analisi.
- L'app non siano più state in primo piano.
- L'utente ha scelto di annullare l'analisi.
- L'analisi è stata annullata dall'app.

Il codice seguente viene illustrato come gestire un errore:

```csharp
public void DidInvalidate(NFCNdefReaderSession session, NSError error)
{
    var readerError = (NFCReaderError)(long)error.Code;
    if (readerError != NFCReaderError.ReaderSessionInvalidationErrorFirstNDEFTagRead &&
        readerError != NFCReaderError.ReaderSessionInvalidationErrorUserCanceled)
    {
      // some error handling
    }
}
```

Una volta che una sessione è stata invalidata, è necessario creare un nuovo oggetto sessione per eseguire nuovamente la scansione.

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2. Avviare un `NFCNdefReaderSession`

L'analisi deve iniziare con una richiesta dell'utente, ad esempio la pressione di un pulsante.
Il codice seguente crea e avvia una sessione di analisi:

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

I parametri per il `NFCNdefReaderSession` costruttore sono i seguenti:

- `delegate` : Un'implementazione di `INFCNdefReaderSessionDelegate`. Nell'esempio di codice, il delegato viene implementato nel controller di visualizzazione tabella, pertanto `this` viene usato come parametro del delegato.
- `queue` : La coda che i callback vengono gestiti in. Può essere `null`, nel qual caso assicurarsi di usare il `DispatchQueue.MainQueue` durante l'aggiornamento di controlli dell'interfaccia utente (come illustrato nell'esempio).
- `invalidateAfterFirstRead` -Quando `true`, l'analisi si arresta dopo la prima analisi ha esito positivo; quando `false` analisi continuerà e restituiti più risultati, fino a quando l'analisi è stata annullata o viene raggiunto il timeout di 60 secondi.


### <a name="3-cancel-the-scanning-session"></a>3. Annulla la sessione di analisi

L'utente può annullare la sessione di analisi tramite un pulsante fornita dal sistema nell'interfaccia utente:

![Pulsante di annullamento durante l'analisi](corenfc-images/scan-cancel-sml.png)

L'app a livello di codice può annullare l'analisi chiamando il `InvalidateSession` metodo:

```csharp
Session.InvalidateSession();
```

In dell'entrambi i casi, il delegato `DidInvalidate` metodo verrà chiamato.

## <a name="summary"></a>Riepilogo

CoreNFC consente all'app di leggere i dati dai tag NFC. Supporta la lettura di una varietà di formati di tag (tipi NDEF da 1 a 5), ma non supporta la scrittura o la formattazione.


## <a name="related-links"></a>Collegamenti correlati

- [NFCTagReader (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
- [Introduzione a Core NFC (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/718/)
