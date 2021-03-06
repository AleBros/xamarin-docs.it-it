---
title: NFC di base in Xamarin.iOS
description: Questo documento descrive come leggere i tag Near Field Communication in Xamarin.iOS usando le API introdotte in iOS 11.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 2e19fd37270d3c96cb175d30dc786a95a01c3fcf
ms.sourcegitcommit: 2ed3d1c933fce4ce332128f125acb2f23f9e0f1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75753023"
---
# <a name="core-nfc-in-xamarinios"></a>NFC di base in Xamarin.iOS

_Lettura di tag NFC (Near Field Communication) con iOS 11_

CoreNFC è un nuovo Framework in iOS 11 che fornisce l'accesso alla radio NFC ( _Near Field Communication_ ) per la lettura dei tag dalle app. CoreNFC funziona sui modelli iPhone 7, iPhone 7 Plus, iPhone 8, iPhone 8 Plus, iPhone X, iPhone XS e iPhone 11 (mentre i modelli iPhone 6 e iPhone 6 Plus hanno funzionalità di pagamento NFC, non supportano CoreNFC).

Il lettore di tag NFC nei dispositivi iOS supporta tutti i tipi di tag NFC da 1 a 5 contenenti informazioni sul _formato di scambio di dati NFC_ (NDEF).

È necessario tenere presenti alcune restrizioni:

- CoreNFC supporta solo la lettura di tag (non scrittura o formattazione).
- Le analisi di tag devono essere avviate dall'utente e il timeout dopo 60 secondi.
- Per l'analisi, le app devono essere visibili in primo piano.
- CoreNFC può essere testato solo su dispositivi reali (non sul simulatore).

Questa pagina descrive la configurazione necessaria per usare CoreNFC e Mostra come usare l'API usando il [codice di esempio "NFCTagReader"](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-nfctagreader).

## <a name="configuration"></a>Configurazione di

Per abilitare CoreNFC, è necessario configurare tre elementi nel progetto:

- Chiave di privacy **info. plist** .
- Voce **titles. plist** .
- Profilo di provisioning con funzionalità di **lettura dei tag NFC** .

### <a name="infoplist"></a>Info. plist

Aggiungere la chiave e il testo della privacy di **NFCReaderUsageDescription** , che viene visualizzato all'utente durante l'analisi. Usare un messaggio appropriato per l'applicazione (ad esempio, spiegare lo scopo dell'analisi):

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

L'app deve richiedere la funzionalità di **lettura dei tag Near Field Communications** usando la coppia chiave/valore seguente nel file **titles. plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>Profilo di provisioning

Creare un nuovo **ID app** e verificare che il servizio di **lettura tag NFC** sia selezionato:

[pagina nuovo ID app del portale per sviluppatori ![con la lettura dei tag NFC selezionata](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

È quindi necessario creare un nuovo profilo di provisioning per questo ID app, quindi scaricarlo e installarlo nel computer di sviluppo.

## <a name="reading-a-tag"></a>Lettura di un tag

Una volta configurato il progetto, aggiungere `using CoreNFC;` all'inizio del file e seguire questi tre passaggi per implementare la funzionalità di lettura dei tag NFC:

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. implementare `INFCNdefReaderSessionDelegate`

L'interfaccia dispone di due metodi da implementare:

- `DidDetect`: viene chiamato quando un tag viene letto correttamente.
- `DidInvalidate`: viene chiamato quando si verifica un errore o viene raggiunto il timeout di 60 secondi.

#### <a name="diddetect"></a>DidDetect

Nel codice di esempio, ogni messaggio analizzato viene aggiunto a una vista tabella:

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

Questo metodo può essere chiamato più volte (ed è possibile che venga passata una matrice di messaggi) se la sessione consente la lettura di più tag. Viene impostato utilizzando il terzo parametro del metodo `Start` (descritto nel [passaggio 2](#step2)).

#### <a name="didinvalidate"></a>DidInvalidate

L'invalidamento può verificarsi per diversi motivi:

- Si è verificato un errore durante l'analisi.
- L'app non è più in primo piano.
- L'utente ha scelto di annullare l'analisi.
- L'analisi è stata annullata dall'app.

Il codice seguente illustra come gestire un errore:

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

Dopo che una sessione è stata invalidata, è necessario creare un nuovo oggetto sessione per eseguire nuovamente l'analisi.

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2. avviare un `NFCNdefReaderSession`

L'analisi dovrebbe iniziare con una richiesta dell'utente, ad esempio la pressione di un pulsante.
Il codice seguente crea e avvia una sessione di analisi:

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

I parametri per il costruttore `NFCNdefReaderSession` sono i seguenti:

- `delegate`: implementazione di `INFCNdefReaderSessionDelegate`. Nel codice di esempio, il delegato viene implementato nel controller di visualizzazione tabella, pertanto `this` viene utilizzato come parametro del delegato.
- `queue`: la coda in cui vengono gestiti i callback. Può essere `null`. in tal caso, assicurarsi di usare l'`DispatchQueue.MainQueue` quando si aggiornano i controlli dell'interfaccia utente, come illustrato nell'esempio.
- `invalidateAfterFirstRead`: quando `true`, l'analisi si interrompe dopo la prima analisi riuscita; Quando `false` l'analisi continuerà e verranno restituiti più risultati fino a quando l'analisi non viene annullata o viene raggiunto il timeout di 60 secondi.

### <a name="3-cancel-the-scanning-session"></a>3. annullare la sessione di analisi

L'utente può annullare la sessione di analisi tramite un pulsante fornito dal sistema nell'interfaccia utente:

![Pulsante Annulla durante l'analisi](corenfc-images/scan-cancel-sml.png)

L'app può annullare l'analisi a livello di codice chiamando il metodo `InvalidateSession`:

```csharp
Session.InvalidateSession();
```

In entrambi i casi, verrà chiamato il metodo `DidInvalidate` del delegato.

## <a name="summary"></a>Riepilogo

CoreNFC consente all'app di leggere i dati dai tag NFC. Supporta la lettura di un'ampia gamma di formati di tag (tipi NDEF da 1 a 5), ma non supporta la scrittura o la formattazione.

## <a name="related-links"></a>Collegamenti correlati

- [NFCTagReader (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-nfctagreader)
- [Introduzione a base NFC (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/718/)
