---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 9fcabc90875dda28ecdd5d94f1ca2f263ffe4886
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60954192"
---
# <a name="android-beam"></a>Android Beam

Trasmetti Android sono una tecnologia di quasi Field Communication (NFC) introdotta in Android 4.0 che consente alle applicazioni di condividere informazioni tramite NFC quando in stretta vicinanza.

[![Diagramma che illustra due dispositivi in prossimità di condivisione delle informazioni](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Trasmetti Android funziona effettuando il push dei messaggi tramite NFC quando due dispositivi sono nell'intervallo. I dispositivi di circa 4cm uno da altro possono condividere dati con raggio di Android. Un'attività in un dispositivo crea un messaggio e specifica un'attività (o attività) in grado di gestire il push. Quando l'attività specificata è in primo piano e i dispositivi si trovano nell'intervallo, Trasmetti Android effettuerà il push del messaggio al secondo dispositivo. Sul dispositivo di ricezione, viene richiamato un Intent che contiene i dati del messaggio.

Android supporta due tipi di messaggi di impostazione con raggio di Android:

-   `SetNdefPushMessage` -Prima dell'avvio Trasmetti Android, un'applicazione può chiamare SetNdefPushMessage per specificare un NdefMessage al push tramite NFC e l'attività che è eseguirne il push. Questo meccanismo è più adatta quando un messaggio non viene modificata mentre un'applicazione è in uso.

-   `SetNdefPushMessageCallback` -Quando viene avviato il raggio di Android, un'applicazione può gestire un callback per creare un NdefMessage. Questo meccanismo consente la creazione di messaggio deve essere ritardata fino a quando i dispositivi sono nell'intervallo. Supporta scenari in cui il messaggio può variare a seconda di ciò che avviene nell'applicazione.


In entrambi i casi, per l'invio di dati con raggio di Android, un'applicazione di Invia un' `NdefMessage`, creazione di pacchetti di dati in diverse `NdefRecords`. Esaminiamo ora i punti chiave che deve essere risolto prima che può essere attivato Trasmetti Android. In primo luogo, si collaborerà con lo stile di callback di creazione di un `NdefMessage`.


## <a name="creating-a-message"></a>Creazione di un messaggio

È possibile registrare i callback con un `NfcAdapter` dell'attività `OnCreate` (metodo). Ad esempio, supponendo un `NfcAdapter` denominato `mNfcAdapter` è dichiarato come una variabile di classe nell'attività, è possibile scrivere il codice seguente per creare il callback che costruisce il messaggio:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

L'attività, che implementa `NfcAdapter.ICreateNdefMessageCallback`, viene passato per il `SetNdefPushMessageCallback` metodo precedente. Quando viene avviato il raggio di Android, il sistema chiamerà `CreateNdefMessage`, da cui può essere costruita l'attività di un `NdefMessage` come illustrato di seguito:

```csharp
public NdefMessage CreateNdefMessage (NfcEvent evt)
{
    DateTime time = DateTime.Now;
    var text = ("Beam me up!\n\n" + "Beam Time: " +
        time.ToString ("HH:mm:ss"));
    NdefMessage msg = new NdefMessage (
        new NdefRecord[]{ CreateMimeRecord (
            "application/com.example.android.beam",
            Encoding.UTF8.GetBytes (text)) });
        } };
    return msg;
}

public NdefRecord CreateMimeRecord (String mimeType, byte [] payload)
{
    byte [] mimeBytes = Encoding.UTF8.GetBytes (mimeType);
    NdefRecord mimeRecord = new NdefRecord (
        NdefRecord.TnfMimeMedia, mimeBytes, new byte [0], payload);
    return mimeRecord;
}
```


## <a name="receiving-a-message"></a>Ricezione di un messaggio

Sul lato ricevente, il sistema richiama un Intent con il `ActionNdefDiscovered` azione, da cui è possibile estrarre il NdefMessage come indicato di seguito:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Per un esempio di codice completo che usa Android Trasmetti, illustrato in esecuzione nella schermata riportata di seguito, vedere la [demo Trasmetti Android](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/) nella raccolta di esempio.

[![Screenshot di esempio dalla demo Trasmetti Android](android-beam-images/24.png)](android-beam-images/24.png#lightbox)



## <a name="related-links"></a>Collegamenti correlati

- [Demo per Android Trasmetti (esempio)](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4.0](https://developer.android.com/sdk/android-4.0.html)
