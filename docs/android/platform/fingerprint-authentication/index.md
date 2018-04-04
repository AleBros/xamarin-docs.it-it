---
title: Autenticazione impronta digitale
description: Questa guida viene descritto come aggiungere l'autenticazione impronta digitale, introdotto in Android 6.0, per un'applicazione di xamarin.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1b28b16dfd92ef3a31201ef2e86681a425a58ab8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="fingerprint-authentication"></a>Autenticazione impronta digitale

_Questa guida viene descritto come aggiungere l'autenticazione impronta digitale, introdotto in Android 6.0, per un'applicazione di xamarin._


## <a name="fingerprint-authentication-overview"></a>Panoramica dell'autenticazione impronta digitale

L'arrivo degli scanner di impronta digitale nei dispositivi Android fornisce applicazioni con un'alternativa al metodo tradizionale di nome utente e password di autenticazione dell'utente. L'utilizzo delle impronte digitali per autenticare un utente rende possibile per un'applicazione incorporare la sicurezza che è meno intrusiva di un nome utente e password.

Le APIs FingerprintManager dispositivi di destinazione con uno scanner di impronta digitale e sono in esecuzione il livello API 23 (Android 6.0) o versione successiva. Le API disponibili nel `Android.Hardware.Fingerprints` dello spazio dei nomi. V4 la libreria di supporto Android include versioni dell'API per le versioni precedenti di Android impronta digitale. La compatibilità API, vedere il `Android.Support.v4.Hardware.Fingerprint` dello spazio dei nomi, vengono distribuiti attraverso il [pacchetto NuGet Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

Il [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (e la relativa controparte della libreria di supporto, [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) è la classe primaria per l'utilizzo dell'impronta digitale la scansione di hardware. Questa classe è un wrapper di Android SDK per il servizio di livello di sistema che gestisce le interazioni con l'hardware stesso. È responsabile per l'avvio dello scanner di impronta digitale e per la risposta ai commenti e suggerimenti dallo scanner. Questa classe è un'interfaccia semplice con solo tre membri:

* **`Authenticate`** &ndash; Questo metodo verrà inizializzare lo scanner di hardware e avviare il servizio in background, in attesa per l'utente analizzare le impronte digitali.
* **`EnrolledFingerprints`** &ndash; Questa proprietà restituirà `true` se l'utente ha registrato le impronte digitali uno o più con il dispositivo.
* **`HardwareDetected`** &ndash; Questa proprietà viene utilizzata per determinare se il dispositivo supporta l'analisi delle impronte digitali.

Il `FingerprintManager.Authenticate` metodo viene utilizzato da un'applicazione Android per avviare lo scanner di impronta digitale. Nel frammento seguente è riportato un esempio di come richiamare utilizzando la compatibilità della libreria di supporto per le API:

```csharp
// context is any Android.Content.Context instance, typically the Activity 
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
fingerprintManager.Authenticate(FingerprintManager.CryptoObject crypto,
                                int flags,
                                CancellationSignal cancel,
                                FingerprintManagerCompat.AuthenticationCallback callback,
                                Handler handler
                               );
```

Questa guida viene illustrato come utilizzare il `FingerprintManager` API per migliorare un'applicazione Android con l'autenticazione impronta digitale. Illustra come creare un'istanza e creare un `CryptoObject` per proteggere i risultati dallo scanner impronta digitale. Verrà esaminato come un'applicazione deve essere una sottoclasse `FingerprintManager.AuthenticationCallback` e rispondere ai commenti e suggerimenti da scanner di impronte digitali. Infine, vedremo come registrare un'impronta digitale in un emulatore o dispositivo Android e come utilizzare **adb** per simulare un'analisi delle impronte digitali.

## <a name="requirements"></a>Requisiti

L'autenticazione impronta digitale richiede Android 6.0 (livello API 23) o versione successiva e un dispositivo con uno scanner di impronta digitale. 

Un'impronta digitale deve essere già registrata con il dispositivo per ogni utente che deve essere autenticato. Questo implica l'impostazione di un blocco dello schermo che utilizza una password, PIN, il modello di scorrere o il riconoscimento facciale. È possibile simulare alcune delle funzionalità di autenticazione impronta digitale in un emulatore Android.  Per ulteriori informazioni su questi due argomenti, vedere il [registrazione un'impronta digitale](enrolling-fingerprint.md) sezione. 






## <a name="related-links"></a>Collegamenti correlati

- [App di esempio di Guida di impronta digitale](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [Esempio di finestra di dialogo delle impronte digitali](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [La richiesta di autorizzazioni in fase di esecuzione](http://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](http://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [API di impronta digitale e pagamenti (video)](https://youtu.be/VOn7VrTRlA4)
