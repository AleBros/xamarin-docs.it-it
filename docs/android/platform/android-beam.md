---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 11f1d38eb10421d4ecef3e2039688b2d9a6d9e6e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027823"
---
# <a name="android-beam"></a>Android Beam

Android Beam è una tecnologia NFC (Near Field Communication) introdotta in Android 4,0 che consente alle applicazioni di condividere informazioni su NFC quando si trovano in prossimità.

[![diagramma che illustra due dispositivi in prossimità delle informazioni di condivisione](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Android Beam funziona eseguendo il push dei messaggi su NFC quando due dispositivi sono in intervallo. I dispositivi circa 4 centimetri gli uni dagli altri possono condividere dati usando Android Beam. Un'attività in un dispositivo crea un messaggio e specifica un'attività (o attività) che può gestire il push. Quando l'attività specificata è in primo piano e i dispositivi sono compresi nell'intervallo, Android Beam eseguirà il push del messaggio al secondo dispositivo. Sul dispositivo ricevente viene richiamato uno scopo contenente i dati del messaggio.

Android supporta due modalità di impostazione dei messaggi con il raggio Android:

- `SetNdefPushMessage`: prima dell'avvio del fascio Android, un'applicazione può chiamare SetNdefPushMessage per specificare un NdefMessage per eseguire il push su NFC e l'attività che lo esegue. Questo meccanismo è particolarmente utile quando un messaggio non viene modificato mentre un'applicazione è in uso.

- `SetNdefPushMessageCallback`: quando viene avviato un fascio Android, un'applicazione può gestire un callback per creare un NdefMessage. Questo meccanismo consente il ritardo della creazione dei messaggi fino a quando i dispositivi non rientrano nell'intervallo. Supporta scenari in cui il messaggio può variare in base a ciò che avviene nell'applicazione.

In entrambi i casi, per inviare dati con Android Beam, un'applicazione invia un `NdefMessage`, creando un pacchetto dei dati in diversi `NdefRecords`. Diamo uno sguardo ai punti chiave che devono essere risolti prima di poter attivare il fascio Android. In primo luogo, si userà lo stile di callback per la creazione di un `NdefMessage`.

## <a name="creating-a-message"></a>Creazione di un messaggio

È possibile registrare i callback con un `NfcAdapter` nel metodo `OnCreate` dell'attività. Ad esempio, supponendo che un `NfcAdapter` denominato `mNfcAdapter` venga dichiarato come variabile di classe nell'attività, è possibile scrivere il codice seguente per creare il callback che costruirà il messaggio:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

L'attività, che implementa `NfcAdapter.ICreateNdefMessageCallback`, viene passata al metodo `SetNdefPushMessageCallback` precedente. Quando viene avviato il fascio Android, il sistema chiamerà `CreateNdefMessage`, da cui l'attività può costruire una `NdefMessage` come illustrato di seguito:

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

Sul lato ricevente, il sistema richiama una finalità con l'azione `ActionNdefDiscovered`, da cui è possibile estrarre il NdefMessage come segue:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Per un esempio di codice completo in cui viene usato Android Beam, illustrato nella schermata seguente, vedere la [demo di Android Beam](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo) nella raccolta di esempi.

[![screenshot di esempio dalla demo di Android Beam](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [Demo di Android Beam (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
- [Introduzione a Ice Cream Sandwich](https://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
