---
title: Analisi delle impronte digitali
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/23/2016
ms.openlocfilehash: 15afd5b1812e0423097e889cd8c2558ca01a8074
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119748"
---
# <a name="scanning-for-fingerprints"></a>Analisi delle impronte digitali

Ora che si è appreso come preparare un'applicazione Novell. Android per l'uso dell'autenticazione con impronta digitale, tornare `FingerprintManager.Authenticate` al metodo e discutere il suo posto nell'autenticazione con impronta digitale Android 6,0. In questo elenco è descritta una rapida panoramica del flusso di lavoro per l'autenticazione con impronta digitale:

1. Richiamare `FingerprintManager.Authenticate`, passando un `CryptoObject` oggetto e `FingerprintManager.AuthenticationCallback` un'istanza. `CryptoObject` Viene usato per garantire che il risultato dell'autenticazione con impronta digitale non sia stato alterato. 
2. Sottoclasse della classe [FingerprintManager. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) . All'avvio dell'autenticazione con `FingerprintManager` impronta digitale verrà fornita un'istanza di questa classe. Al termine dell'analisi dell'impronta digitale, verrà richiamato uno dei metodi di callback in questa classe.
3. Scrivere il codice per aggiornare l'interfaccia utente per indicare all'utente che il dispositivo ha avviato lo scanner di impronte digitali ed è in attesa dell'interazione dell'utente. 
4. Quando si esegue lo scanner di impronta digitale, Android restituirà i risultati all'applicazione richiamando un metodo `FingerprintManager.AuthenticationCallback` nell'istanza fornita nel passaggio precedente.
5. L'applicazione informa l'utente dei risultati dell'autenticazione con impronta digitale e risponde ai risultati in base alle esigenze. 

Il frammento di codice seguente è un esempio di un metodo in un'attività che avvierà l'analisi delle impronte digitali:

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

Si esamineranno ognuno di questi parametri nel `Authenticate` metodo in modo più dettagliato:

- Il primo parametro è un oggetto _Crypto_ che verrà usato dallo scanner di impronte digitali per consentire l'autenticazione dei risultati di un'analisi delle impronte digitali. Questo oggetto può essere `null`, nel qual caso l'applicazione deve considerare ciecamente attendibile che nulla ha alterato i risultati delle impronte digitali. È consigliabile creare un'istanza `CryptoObject` `FingerprintManager` di un oggetto e fornire al anziché null. Con la [creazione di un CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) viene illustrato in dettaglio come creare `CryptoObject` un'istanza di `Cipher`in base a un oggetto.
- Il secondo parametro è sempre zero. La documentazione di Android identifica questo set di flag ed è probabilmente riservato per un uso futuro. 
- Il terzo parametro `cancellationSignal` è un oggetto usato per disattivare lo scanner di impronta digitale e annullare la richiesta corrente. Si tratta di un [CancellationSignal Android](https://developer.android.com/reference/android/os/CancellationSignal.html)e non di un tipo di .NET Framework.
- Il quarto parametro è obbligatorio ed è una classe che sottoclassa la `AuthenticationCallback` classe astratta. I metodi di questa classe verranno richiamati per segnalare ai client `FingerprintManager` quando l'oggetto è terminato e quali sono i risultati. Poiché è necessario comprendere in modo approfondito l'implementazione `AuthenticationCallback`di, la sezione verrà illustrata in [questa sezione](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
- Il quinto parametro è un'istanza `Handler` facoltativa. Se viene `Handler` fornito un oggetto `FingerprintManager` , `Looper` utilizzerà da tale oggetto durante l'elaborazione dei messaggi dall'hardware dell'impronta digitale. In genere, non è necessario fornire un oggetto `Handler`, FingerprintManager utilizzerà l'oggetto `Looper` dell'applicazione.

## <a name="cancelling-a-fingerprint-scan"></a>Annullamento di un'analisi impronta digitale

Potrebbe essere necessario che l'utente (o l'applicazione) cancelli l'analisi delle impronte digitali dopo che è stata avviata. In questa situazione, richiamare il [`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) metodo sull'oggetto [`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html) fornito a `FingerprintManager.Authenticate` quando è stato richiamato per avviare l'analisi dell'impronta digitale.

Ora che abbiamo visto il `Authenticate` metodo, esaminiamo alcuni dei parametri più importanti in modo più dettagliato. In primo luogo, si esaminerà la [risposta ai callback di autenticazione](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), in cui verrà illustrato come sottoporre a sottoclasse [FingerprintManager. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), consentendo a un'applicazione Android di rispondere ai risultati forniti dallo scanner di impronte digitali.




## <a name="related-links"></a>Collegamenti correlati

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
