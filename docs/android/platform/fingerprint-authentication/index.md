---
title: Autenticazione con impronta digitale
description: Questa guida illustra come aggiungere l'autenticazione tramite impronta digitale, introdotto in Android 6.0, per un'applicazione xamarin. Android.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 70e12abdf61a6a0bfb36d281bcaa6214199e567d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023470"
---
# <a name="fingerprint-authentication"></a>Autenticazione con impronta digitale

_Questa guida illustra come aggiungere l'autenticazione tramite impronta digitale, introdotto in Android 6.0, per un'applicazione xamarin. Android._


## <a name="fingerprint-authentication-overview"></a>Panoramica dell'autenticazione tramite impronta digitale

L'arrivo di scanner di impronte digitali nei dispositivi Android fornisce applicazioni con un'alternativa al metodo tradizionale di nome utente/password dell'autenticazione utente. L'uso di impronte digitali per autenticare un utente rende possibile per un'applicazione incorporare la sicurezza che è meno intrusiva di un nome utente e password.

APIs FingerprintManager destinate a dispositivi con uno scanner di impronta digitale e sono in esecuzione il livello API 23 (Android 6.0) o versione successiva. Le API si trovano nella `Android.Hardware.Fingerprints` dello spazio dei nomi. La libreria di supporto Android v4 fornisce versioni dell'impronta digitale API destinate a versioni precedenti di Android. La compatibilità con le API si trovano nella `Android.Support.v4.Hardware.Fingerprint` dello spazio dei nomi, vengono distribuiti tramite il [pacchetto NuGet Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

Il [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (e la controparte, libreria di supporto [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) è la classe primaria per l'uso dell'impronta digitale la scansione dell'hardware. Questa classe è un wrapper di Android SDK per il servizio a livello di sistema che gestisce le interazioni con l'hardware. È responsabile dell'avvio lo scanner di impronta digitale e per rispondere ai commenti e suggerimenti dallo scanner. Questa classe dispone di un'interfaccia piuttosto semplice con solo tre membri:

* **`Authenticate`** &ndash; Questo metodo inizializza lo scanner di hardware e avviare il servizio in background, in attesa per l'utente da analizzare propria impronta digitale.
* **`EnrolledFingerprints`** &ndash; Questa proprietà restituirà `true` se l'utente ha registrato le impronte digitali uno o più con il dispositivo.
* **`HardwareDetected`** &ndash; Questa proprietà viene utilizzata per determinare se il dispositivo supporta l'analisi tramite impronta digitale.

Il `FingerprintManager.Authenticate` metodo viene utilizzato da un'applicazione Android per avviare lo scanner di impronta digitale. Il frammento di codice seguente è un esempio di come richiamare utilizzando la compatibilità della libreria di supporto per le API:

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

Questa guida illustra come usare il `FingerprintManager` API per migliorare un'applicazione Android con l'autenticazione tramite impronta digitale. Illustrerà come creare un'istanza e creare un `CryptoObject` per proteggere i risultati dallo scanner di impronta digitale. Esamineremo il modo in cui un'applicazione deve essere una sottoclasse `FingerprintManager.AuthenticationCallback` e Rispondi ai commenti e suggerimenti dallo scanner di impronta digitale. Infine, si vedrà come registrare un'impronta digitale in un emulatore o dispositivo Android e come usare **adb** per simulare un'analisi di impronta digitale.

## <a name="requirements"></a>Requisiti

Richiede l'autenticazione tramite impronta digitale Android 6.0 (livello API 23) o versione successiva e un dispositivo con uno scanner di impronta digitale. 

Un'impronta digitale deve essere già registrata con il dispositivo per ogni utente che deve essere autenticata. Ciò comporta l'impostazione di un blocco dello schermo che utilizza una password, PIN, modello scorrere rapidamente o il riconoscimento facciale. È possibile simulare alcune delle funzionalità di autenticazione tramite impronta digitale in un emulatore Android.  Per altre informazioni su questi due argomenti, vedere la [la registrazione di un'impronta digitale](enrolling-fingerprint.md) sezione. 






## <a name="related-links"></a>Collegamenti correlati

- [App di esempio Guida impronta digitale](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [Esempio di finestra di dialogo di impronta digitale](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Richiesta di autorizzazioni in fase di esecuzione](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [API di impronta digitale e pagamenti (video)](https://youtu.be/VOn7VrTRlA4)
