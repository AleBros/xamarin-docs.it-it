---
title: L'analisi per le impronte digitali
ms.topic: article
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/23/2016
ms.openlocfilehash: 678ceaf122550c6561541533405fe3500d192110
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="scanning-for-fingerprints"></a>L'analisi per le impronte digitali

Ora che abbiamo visto come preparare un'applicazione di xamarin per utilizzare l'autenticazione impronta digitale, torniamo il `FingerprintManager.Authenticate` (metodo) e discutere al suo posto nell'autenticazione impronta digitale Android 6.0. Una rapida panoramica del flusso di lavoro per l'autenticazione delle impronte digitali è descritto in questo elenco:

1. Richiamare `FingerprintManager.Authenticate`, passando un `CryptoObject` e `FingerprintManager.AuthenticationCallback` istanza. Il `CryptoObject` viene utilizzato per garantire che il risultato dell'autenticazione impronta digitale non è stato manomesso. 
2. Sottoclasse di [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) classe. Un'istanza di questa classe verrà fornita ai `FingerprintManager` quando inizia l'autenticazione di impronte digitali. Al termine dell'operazione lo scanner di impronta digitale, verrà richiamato uno dei metodi di callback in questa classe.
3. Scrivere codice per aggiornare l'interfaccia utente per informare l'utente che il dispositivo è stato avviato lo scanner di impronta digitale ed è in attesa per l'interazione dell'utente. 
4. Una volta completato lo scanner di impronta digitale, Android restituirà risultati all'applicazione richiamando un metodo sul `FingerprintManager.AuthenticationCallback` istanza fornito nel passaggio precedente.
5. L'applicazione verrà informare l'utente dei risultati dell'autenticazione impronta digitale e reagire ai risultati come appropriato. 

Frammento di codice seguente è riportato un esempio di un metodo in un'attività che verrà avviata l'analisi per le impronte digitali:

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerPrintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Esaminiamo ognuno di questi parametri, il `Authenticate` metodo in modo più dettagliato:

* Il primo parametro è un _crittografia_ che lo scanner di impronta digitale verrà utilizzato per autenticare i risultati di un'analisi delle impronte digitali. Questo oggetto può essere `null`, nel qual caso l'applicazione deve indiscriminato che nulla è manomesso i risultati delle impronte digitali. È consigliabile che un `CryptoObject` essere creata un'istanza e non fornito per il `FingerprintManager` anziché null. [Creazione di un CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) verrà illustrato in dettaglio come creare un'istanza di un `CryptoObject` in base a un `Cipher`.
* Il secondo parametro è sempre zero. La documentazione di Android identifica il set di flag ed è probabilmente riservata per utilizzi futuri. 
* Il terzo parametro `cancellationSignal` è un oggetto utilizzato per disattivare lo scanner di impronta digitale e annullare la richiesta corrente. Si tratta di un [CancellationSignal Android](http://developer.android.com/reference/android/os/CancellationSignal.html)e non un tipo di .NET framework.
* Il quarto parametro è obbligatorio ed è una classe che rappresenta una sottoclasse di `AuthenticationCallback` classe astratta. Metodi di questa classe verranno richiamati per segnalare ai client quando il `FingerprintManager` è terminata e quali sono i risultati. Come ci sono molte comprendere sull'implementazione di `AuthenticationCallback`, che verrà trattato [è proprio sezione](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
* Il quinto parametro è facoltativo `Handler` istanza. Se un `Handler` oggetto viene fornito, il `FingerprintManager` utilizzerà il `Looper` dall'oggetto in questione durante l'elaborazione dei messaggi dall'hardware impronta digitale. In genere, uno non è necessario fornire un `Handler`, verrà utilizzato il FingerprintManager il `Looper` dall'applicazione.

## <a name="cancelling-a-fingerprint-scan"></a>L'annullamento di un'analisi delle impronte digitali

Potrebbe essere necessario per l'utente (o l'applicazione) annullare l'analisi delle impronte digitali dopo che è stata avviata. In questo caso, è possibile richiamare il [ `IsCancelled` ](http://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) metodo il [ `CancellationSignal` ](http://developer.android.com/reference/android/os/CancellationSignal.html) che è stato specificato per `FingerprintManager.Authenticate` quando è stato richiamato per avviare l'analisi delle impronte digitali.

Ora che abbiamo visto il `Authenticate` metodo, si esamineranno alcuni parametri più importanti in modo più dettagliato. In primo luogo, esamineremo [risponde ai callback di autenticazione](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), che illustra come sottoclasse il [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), consentendo a rispondere a un'applicazione Android il risultati forniti dall'utilità di analisi delle impronte digitali.




## <a name="related-links"></a>Collegamenti correlati

- [CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
