---
title: Introduzione con l'autenticazione con impronta digitale
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/17/2018
ms.openlocfilehash: 746a096f93036e63b29bc917826259f88426cead
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020274"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Introduzione con l'autenticazione con impronta digitale

Per iniziare, verrà prima di tutto illustrato come configurare un progetto Novell. Android in modo che l'applicazione sia in grado di usare l'autenticazione con impronta digitale:

1. Aggiornare **file AndroidManifest. XML** per dichiarare le autorizzazioni richieste dalle API per le impronte digitali.
2. Ottenere un riferimento al `FingerprintManager`.
3. Verificare che il dispositivo sia in grado di analizzare le impronte digitali.

## <a name="requesting-permissions-in-the-application-manifest"></a>Richiesta di autorizzazioni nel manifesto dell'applicazione

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Un'applicazione Android deve richiedere l'autorizzazione `USE_FINGERPRINT` nel manifesto. Lo screenshot seguente illustra come aggiungere questa autorizzazione all'applicazione in Visual Studio:

[![abilitare l'uso di\_impronta digitale nella schermata del manifesto Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Un'applicazione Android deve richiedere l'autorizzazione `USE_FINGERPRINT` nel manifesto. Lo screenshot seguente illustra come aggiungere questa autorizzazione all'applicazione in Visual Studio per Mac:

[![l'abilitazione di UseFingerprint nella schermata dell'applicazione Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Recupero di un'istanza di FingerprintManager

Successivamente, l'applicazione deve ottenere un'istanza del `FingerprintManager` o la classe `FingerprintManagerCompat`. Per essere compatibile con le versioni precedenti di Android, un'applicazione Android deve usare l'API di compatibilità disponibile nel pacchetto NuGet del supporto Android V4. Il frammento di codice seguente illustra come ottenere l'oggetto appropriato dal sistema operativo: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

Nel frammento di codice precedente, il `context` è qualsiasi `Android.Content.Context`Android. Si tratta in genere dell'attività che esegue l'autenticazione.

## <a name="checking-for-eligibility"></a>Verifica dell'idoneità

Un'applicazione deve eseguire diversi controlli per assicurarsi che sia possibile usare l'autenticazione con impronta digitale. In totale, sono disponibili cinque condizioni che l'applicazione usa per verificare l'idoneità:  

Il **livello API 23** &ndash; le API per le impronte digitali richiedono il livello API 23 o versione successiva. La classe `FingerprintManagerCompat` esegue il wrapping del controllo del livello API. Per questo motivo è consigliabile usare la libreria di **supporto Android V4** e `FingerprintManagerCompat`; verrà considerato uno di questi controlli.

&ndash; **hardware** quando l'applicazione viene avviata per la prima volta, deve verificare la presenza di uno scanner di impronta digitale:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

Il **dispositivo è protetto** &ndash; l'utente deve disporre del dispositivo protetto con un blocco schermo. Se l'utente non ha protetto il dispositivo con un blocco schermo e la sicurezza è importante per l'applicazione, l'utente deve ricevere una notifica che deve essere configurato un blocco dello schermo. Il frammento di codice seguente illustra come verificare questa pre-requiste:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Impronte digitali** registrate &ndash; l'utente deve avere almeno un'impronta digitale registrata con il sistema operativo. Questo controllo delle autorizzazioni deve essere eseguito prima di ogni tentativo di autenticazione:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Autorizzazioni** &ndash; l'applicazione deve richiedere l'autorizzazione all'utente prima di utilizzare l'applicazione. Per Android 5,0 e versioni precedenti, l'utente concede l'autorizzazione come condizione per l'installazione dell'app. Android 6,0 ha introdotto un nuovo modello di autorizzazione che controlla le autorizzazioni in fase di esecuzione. Questo frammento di codice è un esempio di come verificare le autorizzazioni in Android 6,0:

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

Se si verificano tutte queste condizioni ogni volta che l'applicazione offre opzioni di autenticazione, l'utente garantisce la migliore esperienza utente. Le modifiche o gli aggiornamenti al dispositivo o al sistema operativo potrebbero influire sulla disponibilità dell'autenticazione con impronta digitale. Se si sceglie di memorizzare nella cache i risultati di uno di questi controlli, assicurarsi di soddisfare gli scenari di aggiornamento.

Per altre informazioni su come richiedere le autorizzazioni in Android 6,0, vedere la guida per Android che [richiede le autorizzazioni in fase di esecuzione](https://developer.android.com/training/permissions/requesting.html).

## <a name="related-links"></a>Collegamenti correlati

- [Contesto](xref:Android.Content.Context)
- [KeyguardManager](xref:Android.App.KeyguardManager)
- [ContextCompat](https://developer.android.com/reference/android/support/v4/content/ContextCompat)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Richiesta di autorizzazioni in fase di esecuzione](https://developer.android.com/training/permissions/requesting.html)
