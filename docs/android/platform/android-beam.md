---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: aab121ed5f811baf38eed48cf891ccdf076eaf44
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291643"
---
# <a name="android-beam"></a>Android Beam

Android Beam è una tecnologia NFC (Near Field Communication) introdotta in Android 4.0 che consente alle applicazioni di condividere informazioni su NFC nelle immediate vicinanze.

[![Diagramma che illustra due dispositivi nelle informazioni di condivisione nelle immediate vicinanze](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Android Beam funziona spingendo i messaggi su NFC quando due dispositivi sono nel raggio d'azione. I dispositivi di circa 4 cm l'uno dall'altro possono condividere i dati utilizzando Android Beam. Un'attività su un dispositivo crea un messaggio e specifica un'attività (o attività) che può gestire il push. Quando l'attività specificata è in primo piano e i dispositivi sono nel raggio, Android Beam invierà il messaggio al secondo dispositivo. Sul dispositivo ricevente, viene richiamato un intento contenente i dati del messaggio.

Android supporta due modi per impostare i messaggi con Android Beam:

- `SetNdefPushMessage`- Prima dell'avvio di Android Beam, un'applicazione può chiamare SetNdefPushMessage per specificare un NdefMessage per eseguire il push di NFC e l'attività che lo sta spingendo. Questo meccanismo è ideale quando un messaggio non cambia mentre un'applicazione è in uso.

- `SetNdefPushMessageCallback`- Quando viene avviato Android Beam, un'applicazione può gestire un callback per creare un NdefMessage.- When Android Beam is initiated, an application can handle a callback to create an NdefMessage. Questo meccanismo consente di ritardare la creazione dei messaggi fino a quando i dispositivi non si trovano nel raggio d'azione. Supporta scenari in cui il messaggio può variare in base a ciò che accade nell'applicazione.

In entrambi i casi, per inviare dati `NdefMessage`con Android Beam, un'applicazione invia un , imballaggio dei dati in diversi `NdefRecords`. Diamo un'occhiata ai punti chiave che devono essere affrontati prima di poter innescare Android Beam. In primo luogo, lavoreremo con lo `NdefMessage`stile di callback di creazione di un file .

## <a name="creating-a-message"></a>Creazione di un messaggio

È possibile registrare `NfcAdapter` i callback con `OnCreate` un nel metodo dell'attività. Ad esempio, supponendo che un nome venga dichiarato come variabile di classe nell'attività, è possibile scrivere il codice seguente per creare il callback che costruirà il messaggio:For example, assuming an `NfcAdapter` named `mNfcAdapter` is declared as a class variable in the Activity, we can write the following code to create the callback that will construct the message:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

L'oggetto Activity, che `NfcAdapter.ICreateNdefMessageCallback`implementa `SetNdefPushMessageCallback` , viene passato al metodo precedente. Quando Android Beam viene avviato, il sistema `CreateNdefMessage`chiamerà `NdefMessage` , da cui l'attività può costruire un come illustrato di seguito:

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

Sul lato ricevente, il sistema `ActionNdefDiscovered` richiama un intento con l'azione, da cui è possibile estrarre il NdefMessage come segue:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Per un esempio di codice completo che usa Android Beam, mostrato in esecuzione nella schermata seguente, vedere la [demo di Android Beam](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo) nella raccolta di esempio.

[![Screenshot di esempio della demo di Android Beam](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [Android Beam Demo (esempio)Android Beam Demo (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
