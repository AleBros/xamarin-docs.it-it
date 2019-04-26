---
title: Guida introduttiva con autenticazione tramite impronta digitale
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/17/2018
ms.openlocfilehash: 3082dfcd6d0ffbc6404a89a10819e60b57b9c61c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023812"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Guida introduttiva con autenticazione tramite impronta digitale

Per iniziare, è possibile si illustrerà come configurare un progetto xamarin. Android in modo che l'applicazione è in grado di usare l'autenticazione tramite impronta digitale:

1. Update **androidmanifest. XML** per dichiarare le autorizzazioni necessarie per le API di impronta digitale.
2. Ottenere un riferimento di `FingerprintManager`.
3. Verificare che il dispositivo è in grado di analizzare con impronta digitale.

## <a name="requesting-permissions-in-the-application-manifest"></a>Richiesta di autorizzazioni nell'applicazione manifesto

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Un'applicazione Android è necessario richiedere il `USE_FINGERPRINT` autorizzazione nel manifesto. Lo screenshot seguente mostra come aggiungere questa autorizzazione all'applicazione in Visual Studio:

[![Abilitare l'utilizzo\_impronta digitale nella schermata di manifesto Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Un'applicazione Android è necessario richiedere il `USE_FINGERPRINT` autorizzazione nel manifesto. Lo screenshot seguente mostra come aggiungere questa autorizzazione all'applicazione in Visual Studio per Mac:

[![Abilitazione UseFingerprint nella schermata di applicazione Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Ottenere un'istanza di FingerprintManager

Successivamente, l'applicazione deve ottenere un'istanza di `FingerprintManager` o il `FingerprintManagerCompat` classe. Per essere compatibile con le versioni precedenti di Android, un'applicazione Android deve usare l'API di compatibilità sono stati trovati nel pacchetto NuGet supporto Android v4. Il frammento di codice seguente viene illustrato come ottenere l'oggetto sia appropriato dal sistema operativo: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

Nel frammento di codice precedente, il `context` è qualsiasi Android `Android.Content.Context`. In genere si tratta dell'attività che sta eseguendo l'autenticazione.

## <a name="checking-for-eligibility"></a>Verifica idoneità

Un'applicazione deve eseguire più controlli per assicurarsi che sia possibile usare l'autenticazione tramite impronta digitale. In totale, sono disponibili cinque condizioni utilizzati dall'applicazione per controllare idoneità:  

**Livello API 23** &ndash; le API di impronte digitali richiedono il livello API 23 o superiore. Il `FingerprintManagerCompat` classe conclude il controllo a livello di API per l'utente. Per questo motivo è consigliabile usare la **libreria di supporto Android v4** e `FingerprintManagerCompat`; questo verrà tenuto in considerazione per uno di questi controlli.

**Hardware** &ndash; all'avvio l'applicazione per la prima volta, è necessario verificare la presenza di uno scanner di impronta digitale:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**Dispositivo protetta** &ndash; l'utente deve avere il sicurezza con un blocco dello schermo del dispositivo. Se l'utente non ha protetto il dispositivo con un blocco dello schermo e la sicurezza è importante per l'applicazione, quindi l'utente dovrà essere notificato che è necessario configurare un blocco dello schermo. Il frammento di codice seguente viene illustrato come controllare questo prerequisito:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Registrazione di impronte digitali** &ndash; l'utente deve avere almeno un impronte digitali registrate con il sistema operativo. Questo controllo di autorizzazione debba verificarsi prima di ogni tentativo di autenticazione:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Le autorizzazioni** &ndash; l'applicazione deve richiedere l'autorizzazione da parte dell'utente prima di usare l'applicazione. Per Android 5.0 e inferiore, l'utente concede l'autorizzazione come condizione per l'installazione dell'app. Android 6.0 introdotto un nuovo modello di autorizzazione che controlla le autorizzazioni in fase di esecuzione. Questo frammento di codice è un esempio di come verificare le autorizzazioni per Android 6.0:

```csharp
// The context is typically a reference to the current activity.
Android.Content.PM.Permission permissionResult = ContextCompat.CheckSelfPermission(context, Manifest.Permission.UseFingerprint);
if (permissionResult == Android.Content.PM.Permission.Granted)
{
    // Permission granted - go ahead and start the fingerprint scanner.
}
else
{
    // No permission. Go and ask for permissions and don't start the scanner. See
    // https://developer.android.com/training/permissions/requesting.html
}
```

Verifica tutte le condizioni seguenti ogni volta che l'applicazione offre le opzioni di autenticazione assicura che l'utente ottiene la migliore esperienza utente. Le modifiche o aggiornamenti al dispositivo o al sistema operativo potrebbero influire sulla disponibilità dell'autenticazione tramite impronta digitale. Se si sceglie di memorizzare nella cache i risultati di uno di questi controlli, assicurarsi di soddisfare per scenari di aggiornamento.

Per altre informazioni su come richiedere le autorizzazioni in Android 6.0, consultare la Guida di Android [richiesta di autorizzazioni in fase di esecuzione](https://developer.android.com/training/permissions/requesting.html).

## <a name="related-links"></a>Collegamenti correlati

- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Richiesta di autorizzazioni in fase di esecuzione](https://developer.android.com/training/permissions/requesting.html)
