---
title: Trasmetti Android
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/06/2017
ms.openlocfilehash: 89e668b8936db9a05fca2353b334b630b8363a74
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="android-beam"></a>Trasmetti Android

Trasmetti Android sono una tecnologia di comunicazione (prossimità) introdotta in Android 4.0 che consente alle applicazioni di condividere informazioni tramite NFC in stretta vicinanza.

[![Diagramma che illustra due dispositivi in prossimità di condivisione delle informazioni](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Trasmetti Android funziona effettuando il push dei messaggi su NFC quando due dispositivi si trovano nell'intervallo. Dispositivi di circa 4cm tra loro possono condividere dati con raggio Android. Crea un messaggio di un'attività in un dispositivo e specifica un'attività (o attività) in grado di gestire il push. Quando l'attività specificata è in primo piano e i dispositivi si trovano nell'intervallo, per il secondo dispositivo Android Trasmetti determinerà il messaggio. Sul dispositivo di ricezione, l'intento viene richiamato contenente i dati del messaggio.

Android supporta due tipi di messaggi di impostazione con raggio Android:

-   `SetNdefPushMessage` -Prima Trasmetti Android viene avviato, è possibile chiamare SetNdefPushMessage per specificare un NdefMessage per effettuare il push su NFC e l'attività viene eseguita, un'applicazione. Questo meccanismo è più adatta quando un messaggio non viene modificato mentre un'applicazione è in uso.

-   `SetNdefPushMessageCallback` -Quando viene avviato il raggio Android, un'applicazione può gestire un callback per creare un NdefMessage. Questo meccanismo consente la creazione del messaggio deve essere ritardata fino a quando non sono dispositivi nell'intervallo. Supporta scenari in cui il messaggio può variare a seconda di ciò che avviene nell'applicazione.


In entrambi i casi, per l'invio di dati con raggio Android, un'applicazione di invia un `NdefMessage`, creazione di pacchetti di dati in diversi `NdefRecords`. Esaminiamo ora i punti chiave che devono essere risolti prima di è possibile attivare Trasmetti Android. In primo luogo, è possibile utilizzare con lo stile di callback di creazione di un `NdefMessage`.


## <a name="creating-a-message"></a>Creazione di un messaggio

Gli utenti possono registrare callback con un `NfcAdapter` dell'attività `OnCreate` metodo. Ad esempio, supponendo un `NfcAdapter` denominato `mNfcAdapter` è dichiarato come una variabile della classe dell'attività, è possibile scrivere il codice seguente per creare il callback che verrà costruito il messaggio:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

L'attività, che implementa `NfcAdapter.ICreateNdefMessageCallback`, viene passato per il `SetNdefPushMessageCallback` dei metodi descritti sopra. Quando viene avviato il raggio Android, verrà chiamato dal sistema `CreateNdefMessage`, da cui l'attività è possibile costruire un `NdefMessage` come illustrato di seguito:

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

Sul lato di ricezione, il sistema richiama un intento con il `ActionNdefDiscovered` azione, da cui è possibile estrarre il NdefMessage come indicato di seguito:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Per un esempio di codice completo che usa Android raggio, illustrato in esecuzione nella schermata riportata di seguito, vedere il [demo Trasmetti Android](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/) nella raccolta di esempio.

[![Schermate di esempio dalla demo Trasmetti Android](android-beam-images/24.png)](android-beam-images/24.png#lightbox)



## <a name="related-links"></a>Collegamenti correlati

- [Demo di raggio Android (esempio)](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [Introduzione a Sandwich gelato](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
