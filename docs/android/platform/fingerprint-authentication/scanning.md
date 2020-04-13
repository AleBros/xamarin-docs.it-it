---
title: Scansione delle impronte digitali
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/23/2016
ms.openlocfilehash: 61edd0e4b532f18a8fc28502e5bb990703068776
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027498"
---
# <a name="scanning-for-fingerprints"></a>Scansione delle impronte digitali

Ora che abbiamo visto come preparare un'applicazione Xamarin.Android per utilizzare `FingerprintManager.Authenticate` l'autenticazione delle impronte digitali, torniamo al metodo e discutere il suo posto nell'autenticazione delle impronte digitali Android 6.0. Una rapida panoramica del flusso di lavoro per l'autenticazione delle impronte digitali è descritta in questo elenco:A quick overview of the workflow for fingerprint authentication is described in this list:

1. Richiamare `FingerprintManager.Authenticate`, `CryptoObject` passando `FingerprintManager.AuthenticationCallback` un'istanza e un'istanza. Il `CryptoObject` viene utilizzato per garantire che il risultato dell'autenticazione delle impronte digitali non è stato manomesso. 
2. Sottoclasse della classe [FingerprintManager.AuthenticationCallback.](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) Verrà fornita un'istanza di `FingerprintManager` questa classe all'avvio dell'autenticazione tramite impronta digitale. Al termine, lo scanner di impronte digitali richiamerà uno dei metodi di callback su questa classe.
3. Scrivere codice per aggiornare l'interfaccia utente per informare all'utente che il dispositivo ha avviato lo scanner di impronte digitali ed è in attesa dell'interazione dell'utente. 
4. Al termine dello scanner di impronte digitali, Android restituirà `FingerprintManager.AuthenticationCallback` i risultati all'applicazione richiamando un metodo sull'istanza fornita nel passaggio precedente.
5. L'applicazione informerà l'utente dei risultati dell'autenticazione delle impronte digitali e reagirà ai risultati in base alle esigenze. 

Il frammento di codice seguente è un esempio di un metodo in un'attività che avvierà la scansione delle impronte digitali:The following code snippet is an example of a method in an Activity that will start scanning for fingerprints:

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Esaminiamo ciascuno di questi parametri `Authenticate` nel metodo in modo più dettagliato:Let's discuss each of these parameters in the method in a bit more detail:

- Il primo parametro è un oggetto _crittografico_ che verrà utilizzato dallo scanner di impronte digitali per autenticare i risultati di una scansione delle impronte digitali. Questo oggetto `null`può essere , nel qual caso l'applicazione deve fidarsi ciecamente che nulla ha manomesso i risultati delle impronte digitali. È consigliabile `CryptoObject` creare un'istanza e `FingerprintManager` fornirla a anziché null. [La creazione di un oggetto CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) spiegherà in dettaglio come creare un'istanza di un `CryptoObject` oggetto basato su un `Cipher`oggetto .
- Il secondo parametro è sempre zero. La documentazione di Android identifica questo come set di flag ed è molto probabilmente riservato per un utilizzo futuro. 
- Il terzo `cancellationSignal` parametro, è un oggetto utilizzato per disattivare lo scanner di impronte digitali e annullare la richiesta corrente. Si tratta di un [CancellationSignal Android](https://developer.android.com/reference/android/os/CancellationSignal.html)e non un tipo dal framework .NET.
- Il quarto parametro è obbligatorio ed `AuthenticationCallback` è una classe che sottoclassi la classe astratta. I metodi su questa classe verranno richiamati per segnalare ai client quando il `FingerprintManager` è terminato e quali sono i risultati. Come c'è molto da `AuthenticationCallback`capire circa l'implementazione del , sarà coperto nella [sua sezione .](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)
- Il quinto parametro `Handler` è un'istanza facoltativa. Se `Handler` viene fornito un `FingerprintManager` oggetto, `Looper` il verrà utilizzato da tale oggetto durante l'elaborazione dei messaggi dall'hardware di impronte digitali. In genere, non è `Handler`necessario fornire un , `Looper` il FingerprintManager utilizzerà il dall'applicazione.

## <a name="cancelling-a-fingerprint-scan"></a>Annullamento di una scansione delle impronte digitali

Potrebbe essere necessario per l'utente (o l'applicazione) annullare la scansione delle impronte digitali dopo che è stata avviata. In questo caso, [`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) richiamare [`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html) il metodo `FingerprintManager.Authenticate` sul che è stato fornito a quando è stato richiamato per avviare l'analisi delle impronte digitali.

Ora che abbiamo `Authenticate` visto il metodo, esaminiamo alcuni dei parametri più importanti in modo più dettagliato. In primo luogo, verrà esaminato [Risposta ai callback](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)di autenticazione , che verrà illustrato come creare una sottoclasse di [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), consentendo a un'applicazione Android di reagire ai risultati forniti dallo scanner di impronte digitali.

## <a name="related-links"></a>Collegamenti correlati

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
