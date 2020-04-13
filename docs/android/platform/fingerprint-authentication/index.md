---
title: Autenticazione con impronta digitale
description: Questa guida illustra come aggiungere l'autenticazione delle impronte digitali, introdotta in Android 6.0, a un'applicazione Xamarin.Android.This guide discusses how to add fingerprint authentication, introduced in Android 6.0, to a Xamarin.Android application.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 4a4b6ee7a123683a9d5a140c46c0b3542767ffa3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027516"
---
# <a name="fingerprint-authentication"></a>Autenticazione con impronta digitale

_Questa guida illustra come aggiungere l'autenticazione delle impronte digitali, introdotta in Android 6.0, a un'applicazione Xamarin.Android.This guide discusses how to add fingerprint authentication, introduced in Android 6.0, to a Xamarin.Android application._

## <a name="fingerprint-authentication-overview"></a>Cenni preliminari sull'autenticazione delle impronte

L'arrivo di scanner di impronte digitali su dispositivi Android fornisce alle applicazioni un'alternativa al metodo tradizionale di autenticazione utente nome utente/password. L'uso delle impronte digitali per autenticare un utente consente a un'applicazione di incorporare una sicurezza meno intrusiva di un nome utente e di una password.

Le API FingerprintManager sono destinate ai dispositivi con uno scanner di impronte digitali e eseguono il livello API 23 (Android 6.0) o versione successiva. Le API si trovano `Android.Hardware.Fingerprints` nello spazio dei nomi. The Android Support Library v4 provides versions of the fingerprint APIs meant for older versions of Android. Le API di compatibilità `Android.Support.v4.Hardware.Fingerprint` si trovano nello spazio dei nomi, vengono distribuite tramite il [pacchetto Xamarin.Android.Support.v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (e la relativa controparte della libreria di supporto, [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) è la classe principale per l'utilizzo dell'hardware di scansione delle impronte digitali. Questa classe è un wrapper di Android SDK intorno al servizio a livello di sistema che gestisce le interazioni con l'hardware stesso. È responsabile dell'avvio dello scanner di impronte digitali e della risposta al feedback proveniente dallo scanner. Questa classe ha un'interfaccia abbastanza semplice con solo tre membri:This class has a fairly straightforward interface with only three members:

- **`Authenticate`**&ndash; Questo metodo inizializzerà lo scanner hardware e avvierà il servizio in background, in attesa che l'utente eserirà l'impronta digitale.
- **`EnrolledFingerprints`**&ndash; Questa proprietà `true` restituirà un valore se l'utente ha registrato una o più impronte digitali con il dispositivo.
- **`HardwareDetected`**&ndash; Questa proprietà viene utilizzata per determinare se il dispositivo supporta la scansione delle impronte digitali.

Il `FingerprintManager.Authenticate` metodo viene utilizzato da un'applicazione Android per avviare lo scanner di impronte digitali. The following snippet is an example of how to invoke it using the Support Library compatibility APIs:

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

Questa guida verrà illustrato `FingerprintManager` come utilizzare le API per migliorare un'applicazione Android con l'autenticazione delle impronte digitali. Verrà illustrato come creare un'istanza e creare un `CryptoObject` per proteggere i risultati dallo scanner di impronte digitali. Esamineremo il modo in cui `FingerprintManager.AuthenticationCallback` un'applicazione deve sottoclasse e rispondere al feedback dallo scanner di impronte digitali. Infine, vedremo come registrare un'impronta digitale su un dispositivo Android o un emulatore e come utilizzare **adb** per simulare una scansione delle impronte digitali.

## <a name="requirements"></a>Requisiti

L'autenticazione delle impronte digitali richiede Android 6.0 (livello API 23) o superiore e un dispositivo con uno scanner di impronte digitali. 

Un'impronta digitale deve essere già registrata con il dispositivo per ogni utente da autenticare. Ciò comporta l'impostazione di un blocco schermo che utilizza una password, PIN, modello di scorrimento rapido o riconoscimento facciale. È possibile simulare alcune delle funzionalità di autenticazione delle impronte digitali in un emulatore Android.It is possible to simulate some of the fingerprint authentication functionality in an Android Emulator.  Per ulteriori informazioni su questi due argomenti, vedere la sezione [Registrazione di un'impronta digitale.](enrolling-fingerprint.md) 

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio della guida alle impronte digitali](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fingerprintguide)
- [Esempio di finestra di dialogo impronta digitale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Richiesta di autorizzazioni in fase di esecuzioneRequesting Permissions at Runtime](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](xref:Android.Content.Context)
- [API per le impronte digitali e i pagamenti (video)](https://youtu.be/VOn7VrTRlA4)
