---
title: Autenticazione con impronta digitale
description: Questa guida illustra come aggiungere l'autenticazione con impronta digitale, introdotta in Android 6,0, a un'applicazione Novell. Android.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: a58242e89033d6cd2652495f9466379f63f498f0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761243"
---
# <a name="fingerprint-authentication"></a>Autenticazione con impronta digitale

_Questa guida illustra come aggiungere l'autenticazione con impronta digitale, introdotta in Android 6,0, a un'applicazione Novell. Android._

## <a name="fingerprint-authentication-overview"></a>Panoramica dell'autenticazione con impronta digitale

L'arrivo di scanner di impronte digitali nei dispositivi Android fornisce le applicazioni con un'alternativa al tradizionale metodo di nome utente/password dell'autenticazione utente. L'uso delle impronte digitali per autenticare un utente consente a un'applicazione di incorporare la sicurezza meno intrusiva rispetto a un nome utente e una password.

Le API FingerprintManager sono destinate a dispositivi con scanner di impronte digitali ed eseguono il livello API 23 (Android 6,0) o versione successiva. Le API si trovano nello `Android.Hardware.Fingerprints` spazio dei nomi. La libreria di supporto Android V4 fornisce le versioni delle API per le impronte digitali destinate alle versioni precedenti di Android. Le API di compatibilità si trovano nello `Android.Support.v4.Hardware.Fingerprint` spazio dei nomi e vengono distribuite tramite il [pacchetto NuGet Novell. Android. support. v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (e la relativa controparte della libreria di supporto, [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) è la classe principale per l'uso dell'hardware di analisi delle impronte digitali. Questa classe è un wrapper Android SDK intorno al servizio a livello di sistema che gestisce le interazioni con l'hardware stesso. È responsabile dell'avvio dello scanner per le impronte digitali e della risposta ai commenti e suggerimenti dello scanner. Questa classe dispone di un'interfaccia piuttosto semplice con solo tre membri:

- **`Authenticate`** &ndash; Questo metodo inizializza lo scanner hardware e avvia il servizio in background, in attesa che l'utente analizzi l'impronta digitale.
- **`EnrolledFingerprints`** Questa proprietà restituirà `true` se l'utente ha registrato una o più impronte digitali con il dispositivo. &ndash;
- **`HardwareDetected`** &ndash; Questa proprietà viene usata per determinare se il dispositivo supporta l'analisi delle impronte digitali.

Il `FingerprintManager.Authenticate` metodo viene usato da un'applicazione Android per avviare lo scanner di impronta digitale. Il frammento di codice seguente è un esempio di come richiamarlo usando le API di compatibilità della libreria di supporto:

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

Questa guida illustra come usare le `FingerprintManager` API per migliorare un'applicazione Android con l'autenticazione con impronta digitale. Verrà illustrato come creare un'istanza di e creare un `CryptoObject` per proteggere i risultati dallo scanner di impronte digitali. Si esaminerà il modo in cui `FingerprintManager.AuthenticationCallback` un'applicazione deve sottoporre a sottoclasse e rispondere ai commenti e suggerimenti dallo scanner. Infine, si vedrà come registrare un'impronta digitale in un dispositivo o un emulatore Android e come usare **ADB** per simulare un'analisi delle impronte digitali.

## <a name="requirements"></a>Requisiti

Per l'autenticazione con impronta digitale è necessario Android 6,0 (livello API 23) o superiore e un dispositivo con scanner di impronta digitale. 

È necessario che un'impronta digitale sia già registrata con il dispositivo per ogni utente che deve essere autenticato. Questa operazione comporta l'impostazione di un blocco dello schermo che usa una password, un PIN, un modello di scorrimento o un riconoscimento facciale. È possibile simulare alcune funzionalità di autenticazione con impronta digitale in un emulatore Android.  Per altre informazioni su questi due argomenti, vedere la sezione [registrazione di un'impronta digitale](enrolling-fingerprint.md) . 

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio per la Guida all'impronta digitale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fingerprintguide)
- [Esempio di finestra di dialogo impronta digitale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Richiesta di autorizzazioni in fase di esecuzione](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](xref:Android.Content.Context)
- [API per le impronte digitali e i pagamenti (video)](https://youtu.be/VOn7VrTRlA4)
