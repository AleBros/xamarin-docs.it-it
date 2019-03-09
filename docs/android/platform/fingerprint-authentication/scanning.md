---
title: Lettura delle impronte digitali
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/23/2016
ms.openlocfilehash: 372fe4c7844448e7fb3cbc768f16feb3a5cc7791
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671442"
---
# <a name="scanning-for-fingerprints"></a>Lettura delle impronte digitali

Ora che abbiamo visto come preparare un'applicazione xamarin. Android per usare l'autenticazione tramite impronta digitale, si esamini il `FingerprintManager.Authenticate` (metodo) e discutere suo posto nell'autenticazione con impronta digitale Android 6.0. Una rapida panoramica del flusso di lavoro per l'autenticazione tramite impronta digitale è descritto in questo elenco:

1. Richiamare `FingerprintManager.Authenticate`, passando un `CryptoObject` e un `FingerprintManager.AuthenticationCallback` istanza. Il `CryptoObject` viene usato per garantire che il risultato dell'autenticazione tramite impronta digitale non è stato manomesso. 
2. Sottoclasse di [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) classe. Un'istanza di questa classe verrà fornita di `FingerprintManager` dell'impronta digitale quando viene avviata l'autenticazione. Quando lo scanner di impronte digitali è terminato, esso venga richiamato uno dei metodi di callback in questa classe.
3. Scrivere codice per aggiornare l'interfaccia utente per informare l'utente che il dispositivo è stato avviato lo scanner di impronta digitale ed è in attesa per l'interazione dell'utente. 
4. Al termine, lo scanner di impronta digitale Android restituirà i risultati all'applicazione richiamando un metodo su di `FingerprintManager.AuthenticationCallback` istanza in cui è stato specificato nel passaggio precedente.
5. L'applicazione di informare l'utente dei risultati dell'autenticazione tramite impronta digitale e reagire ai risultati come appropriato. 

Il frammento di codice seguente è riportato un esempio di un metodo in un'attività che avvierà la lettura delle impronte digitali:

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

Esaminiamo ciascuno di questi parametri, il `Authenticate` metodo in modo più dettagliato:

* Il primo parametro è un _crypto_ dell'oggetto che lo scanner di impronta digitale verrà usato per autenticare i risultati di un'analisi di impronta digitale. Questo oggetto può essere `null`, nel qual caso l'applicazione deve indiscriminato che nulla è manomesso i risultati di impronta digitale. È consigliabile che una `CryptoObject` essere creata un'istanza e passare il `FingerprintManager` anziché null. [Creazione di un CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) descrivono in dettaglio come creare un'istanza di un `CryptoObject` basato su un `Cipher`.
* Il secondo parametro è sempre zero. Documentazione di Android si identifica come set di flag ed è molto probabilmente riservata per usi futuri. 
* Il terzo parametro, `cancellationSignal` è un oggetto utilizzato per disattivare lo scanner di impronta digitale e annullare la richiesta corrente. Si tratta di un' [CancellationSignal Android](https://developer.android.com/reference/android/os/CancellationSignal.html)e non un tipo di .NET framework.
* Il quarto parametro è obbligatorio ed è una classe che rappresenti le sottoclassi di `AuthenticationCallback` classe astratta. Metodi di questa classe verranno richiamati per segnalare ai client quando il `FingerprintManager` ha terminato e quali sono i risultati. Come vi sono molte conoscere sull'implementazione di `AuthenticationCallback`, che verrà trattato [è propria sezione](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
* Il quinto parametro è facoltativo `Handler` istanza. Se un `Handler` oggetto viene fornito, il `FingerprintManager` utilizzerà il `Looper` dall'oggetto in questione quando si elaborano i messaggi dall'hardware impronta digitale. In genere, uno non è necessario fornire un `Handler`, verrà usato il FingerprintManager il `Looper` dall'applicazione.

## <a name="cancelling-a-fingerprint-scan"></a>L'annullamento di un'analisi di impronta digitale

Potrebbe essere necessario per l'utente (o l'applicazione) annullare l'analisi di impronte digitali dopo che è stata avviata. In questo caso, richiamare il [ `IsCancelled` ](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) metodo sul [ `CancellationSignal` ](https://developer.android.com/reference/android/os/CancellationSignal.html) che ha lo scopo di `FingerprintManager.Authenticate` quando è stato richiamato per avviare l'analisi di impronta digitale.

Ora che abbiamo visto il `Authenticate` metodo, esaminiamo alcuni dei parametri più importanti in modo più dettagliato. Prima di tutto verrà esaminato [risponde ai callback di autenticazione](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), che verrà illustrato come sottoclasse il [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), consentendo a rispondere a un'applicazione Android il risultati forniti dallo scanner di impronta digitale.




## <a name="related-links"></a>Collegamenti correlati

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
