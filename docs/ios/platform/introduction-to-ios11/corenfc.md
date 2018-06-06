---
title: Core NFC in xamarin. IOS
description: Questo documento descrive come leggere in prossimità di tag di comunicazione field in xamarin. IOS utilizzando le API introdotte in iOS 11.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2016
ms.openlocfilehash: c42048f9c00238fb73e354ea86322c3d19bae601
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787377"
---
# <a name="core-nfc-in-xamarinios"></a>Core NFC in xamarin. IOS

_Tag di lettura comunicazione (prossimità) usando iOS 11_

CoreNFC è un nuovo framework di iOS che fornisce l'accesso a 11 di _Near Field Communication_ radio (NFC) per la lettura di tag all'interno di applicazioni. Funziona in un iPhone 7, più di 7, 8, 8 oltre a X.

Il lettore di tag NFC nei dispositivi iOS supporta tutti i tipi di tag NFC 1 a 5 che contengono _formato di scambio di dati NFC_ informazioni (NDEF).

Esistono alcune limitazioni da tenere presenti:

- CoreNFC supporta solo tag lettura (non la scrittura o la formattazione).
- Analisi di tag devono essere avviata dall'utente e di timeout dopo 60 secondi.
- Le app devono essere visibili in primo piano per l'analisi.
- CoreNFC può essere testato solo su dispositivi reali (non nel simulatore).

Questa pagina descrive la configurazione è necessaria utilizzare CoreNFC e viene illustrato come utilizzare l'API tramite il ["TFCTagReader" con codice di esempio](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/).

## <a name="configuration"></a>Configurazione

Per abilitare CoreNFC, è necessario configurare tre elementi del progetto:

- Un **Info. plist** chiave per la privacy.
- Un **Entitlements.plist** voce.
- Un profilo di provisioning con **lettura Tag NFC** funzionalità.

### <a name="infoplist"></a>Info. plist

Aggiungere il **NFCReaderUsageDescription** chiave per la privacy e testo, viene visualizzato all'utente durante l'analisi. Uso di un messaggio appropriato per l'applicazione (ad esempio, per illustrare lo scopo dell'analisi):

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

L'applicazione deve richiedere la **quasi lettura Tag di campo comunicazioni** funzionalità tramite il chiave/valore seguente coppia nel **Entitlements.plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>Profilo di provisioning

Creare un nuovo **ID App** e assicurarsi che il **lettura Tag NFC** servizio sia selezionato:

[![Pagina nuovo ID dell'App portale per sviluppatori con la lettura di Tag NFC selezionato](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

Si deve quindi creare un nuovo profilo di provisioning per l'ID di App, quindi scaricare e installarlo in sviluppo Mac.

## <a name="reading-a-tag"></a>Lettura di un Tag

Dopo aver configurato il progetto, aggiungere `using CoreNFC;` all'inizio del file e seguire questi tre passaggi per implementare NFC tag lettura funzionalità:

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. Implementare `INFCNdefReaderSessionDelegate`

L'interfaccia dispone di due metodi per essere implementato:

- `DidDetect` – Chiamato durante la lettura di un tag.
- `DidInvalidate` – Chiamato quando si verifica un errore o viene raggiunto il timeout di 60 secondi.

#### <a name="diddetect"></a>DidDetect

Nell'esempio di codice, ogni messaggio viene aggiunto a una visualizzazione tabella:

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

Questo metodo può essere chiamato più volte (e una matrice di messaggi può essere passata) se la sessione consente più letture dei tag. È impostato tramite il terzo parametro del `Start` metodo (illustrato in [passaggio 2](#step2)).

#### <a name="didinvalidate"></a>DidInvalidate

Invalidamento può verificarsi per diversi motivi:

- Si è verificato un errore durante l'analisi.
- L'app non siano più in primo piano.
- L'utente ha scelto di annullare l'analisi.
- L'analisi è stata annullata dall'app.

Il codice riportato di seguito viene illustrato come gestire un errore:

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

Dopo una sessione è stata invalidata, è necessario creare un nuovo oggetto sessione per ripetere l'operazione.

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2. Avviare un `NFCNdefReaderSession`

L'analisi deve iniziare con una richiesta dell'utente, ad esempio un pulsante.
Il codice seguente crea e avvia una sessione di analisi:

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

I parametri per il `NFCNdefReaderSession` costruttore sono i seguenti:

- `delegate` : Un'implementazione di `INFCNdefReaderSessionDelegate`. Nell'esempio di codice, il delegato viene implementato nel controller di visualizzazione tabella, pertanto `this` viene utilizzato come parametro del delegato.
- `queue` : La coda che vengono gestiti i callback su. Può essere `null`, nel qual caso assicurarsi di utilizzare il `DispatchQueue.MainQueue` durante l'aggiornamento di controlli dell'interfaccia utente (come illustrato nell'esempio).
- `invalidateAfterFirstRead` : Quando `true`, interrompe l'analisi dopo la prima analisi ha esito positivo; quando `false` analisi continuerà e restituiti più risultati, fino a quando l'analisi è stata annullata o viene raggiunto il timeout di 60 secondi.


### <a name="3-cancel-the-scanning-session"></a>3. Annulla la sessione di analisi

L'utente può annullare la sessione di analisi tramite un pulsante fornita dal sistema nell'interfaccia utente:

![Pulsante Annulla durante l'analisi](corenfc-images/scan-cancel-sml.png)

L'app a livello di programmazione annullare l'analisi, chiamare il `InvalidateSession` metodo:

```csharp
Session.InvalidateSession();
```

In del entrambi i casi, il delegato `DidInvalidate` metodo verrà chiamato.

## <a name="summary"></a>Riepilogo

CoreNFC consente all'app di leggere i dati dal tag NFC. Supporta la lettura di una varietà di formati di tag (tipi NDEF 1 a 5), ma non supporta la scrittura o la formattazione.


## <a name="related-links"></a>Collegamenti correlati

- [NFCTagReader (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
- [Introduzione a Core NFC (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/718/)
