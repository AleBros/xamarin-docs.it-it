---
title: Introduzione all'autenticazione delle impronte digitali
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/17/2018
ms.openlocfilehash: 746a096f93036e63b29bc917826259f88426cead
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020274"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Introduzione all'autenticazione delle impronte digitali

Per iniziare, è innanzitutto illustrato come configurare un progetto Xamarin.Android in modo che l'applicazione è in grado di utilizzare l'autenticazione delle impronte digitali:To get started, let's first cover how to configure a Xamarin.Android project so that the application is able to use fingerprint authentication:

1. Aggiornare **AndroidManifest.xml** per dichiarare le autorizzazioni necessarie per le API di impronta digitale.
2. Ottenere un riferimento `FingerprintManager`al file .
3. Verificare che il dispositivo sia in grado di eseguire la scansione delle impronte digitali.

## <a name="requesting-permissions-in-the-application-manifest"></a>Richiesta di autorizzazioni nel manifesto dell'applicazioneRequesting Permissions in the Application Manifest

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Un'applicazione Android `USE_FINGERPRINT` deve richiedere l'autorizzazione nel manifesto. The following screenshot shows how to add this permission to the application in Visual Studio:

[![Abilitazione\_di USE FINGERPRINT nella schermata Manifesto Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Un'applicazione Android `USE_FINGERPRINT` deve richiedere l'autorizzazione nel manifesto. The following screenshot shows how to add this permission to the application in Visual Studio for Mac:

[![Abilitazione di UseFingerprint nella schermata dell'applicazione AndroidEnabling UseFingerprint in the Android Application screen](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Recupero di un'istanza di FingerprintManagerGetting an Instance of the FingerprintManager

Successivamente, l'applicazione deve ottenere `FingerprintManager` un'istanza della `FingerprintManagerCompat` classe o . Per essere compatibile con le versioni precedenti di Android, un'applicazione Android deve utilizzare l'API di compatibilità disponibile nel pacchetto di supporto Android v4 NuGet.To be compatible with older versions of Android, an Android application should use the compatibility API's found in the Android Support v4 NuGet package. Nel frammento di codice seguente viene illustrato come ottenere l'oggetto appropriato dal sistema operativo:The following snippet demonstrates how to get the appropriate object from the operating system: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

Nel frammento precedente, `context` il `Android.Content.Context`è qualsiasi Android . In genere si tratta dell'attività che esegue l'autenticazione.

## <a name="checking-for-eligibility"></a>Verifica dell'idoneità

Un'applicazione deve eseguire diversi controlli per garantire che sia possibile utilizzare l'autenticazione delle impronte digitali. In totale, ci sono cinque condizioni che l'applicazione utilizza per verificare l'idoneità:  

**Livello API 23** &ndash; Le API di impronte digitali richiedono il livello API 23 o superiore. La `FingerprintManagerCompat` classe eseguirà automaticamente il wrapping del controllo a livello di API. Per questo motivo si consiglia di utilizzare `FingerprintManagerCompat`la libreria di supporto Android **v4** e ; questo terrà conto di uno di questi controlli.

**Hardware** &ndash; Quando l'applicazione viene avviata per la prima volta, deve verificare la presenza di uno scanner di impronte digitali:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**Il dispositivo è protetto** &ndash; L'utente deve avere il dispositivo protetto con un blocco schermo. Se l'utente non ha protetto il dispositivo con un blocco schermo e la sicurezza è importante per l'applicazione, l'utente deve essere informato che è necessario configurare un blocco schermo. Il frammento di codice seguente mostra come controllare questa verifica preliminare:The following code snippet shows how to check this pre-requiste:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Impronte digitali registrate** &ndash; L'utente deve avere almeno un'impronta digitale registrata con il sistema operativo. Questo controllo di autorizzazione deve essere eseguito prima di ogni tentativo di autenticazione:This permission check should occur prior to each authentication attempt:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Autorizzazioni** &ndash; L'applicazione deve richiedere l'autorizzazione dell'utente prima di utilizzare l'applicazione. Per Android 5.0 e versioni successive, l'utente concede l'autorizzazione come condizione per l'installazione dell'app. Android 6.0 ha introdotto un nuovo modello di autorizzazione che controlla le autorizzazioni in fase di esecuzione. Questo frammento di codice è un esempio di come verificare le autorizzazioni per Android 6.0:This code snippet is an example of how to check for permissions on Android 6.0:

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

Il controllo di tutte queste condizioni ogni volta che l'applicazione offre opzioni di autenticazione garantirà all'utente di ottenere la migliore esperienza utente. Le modifiche o gli aggiornamenti al dispositivo o al sistema operativo potrebbero influire sulla disponibilità dell'autenticazione tramite impronta digitale. Se si sceglie di memorizzare nella cache i risultati di uno di questi controlli, assicurarsi di soddisfare gli scenari di aggiornamento.

Per ulteriori informazioni su come richiedere le autorizzazioni in Android 6.0, consultare la guida di Android [che richiede le autorizzazioni in fase di esecuzione](https://developer.android.com/training/permissions/requesting.html).

## <a name="related-links"></a>Collegamenti correlati

- [Context](xref:Android.Content.Context)
- [KeyguardManager](xref:Android.App.KeyguardManager)
- [ContextCompat](https://developer.android.com/reference/android/support/v4/content/ContextCompat)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Richiesta di autorizzazioni in fase di esecuzioneRequesting Permissions at Run-Time](https://developer.android.com/training/permissions/requesting.html)
