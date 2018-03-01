---
title: Introduzione a autenticazione impronta digitale
ms.topic: article
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: ea9046c0c546a2f331aefd2332008e10ec6db3c4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started-with-fingerprint-authentication"></a>Introduzione a autenticazione impronta digitale

Per iniziare, si illustrerà come configurare un progetto xamarin in modo che l'applicazione è in grado di utilizzare l'autenticazione impronta digitale:

1. Aggiornamento **AndroidManifest.xml** per dichiarare le autorizzazioni necessarie per le API di impronta digitale.
2. Ottenere un riferimento di `FingerprintManager`.
3. Verificare che il dispositivo è in grado di supportare l'analisi di impronta digitale.

## <a name="requesting-permissions-in-the-application-manifest"></a>Richiesta di autorizzazioni nell'applicazione manifesto

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Un'applicazione deve richiedere la `USE_FINGERPRINT` autorizzazione nel manifesto. Nella schermata seguente viene illustrato come aggiungere questa autorizzazione per l'applicazione in Visual Studio 2015:

[![Abilitare l'utilizzo\_impronta digitale nella schermata di manifesto Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Un'applicazione deve richiedere la `USE_FINGERPRINT` autorizzazione nel manifesto. Nella schermata seguente viene illustrato come aggiungere questa autorizzazione per l'applicazione in Visual Studio per Mac:

[![Abilitazione UseFingerprint nella schermata di applicazione Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Ottenere un'istanza di FingerprintManager

Successivamente, l'applicazione deve ottenere un'istanza di `FingerprintManager` o `FingerprintManagerCompat` classe. Per essere compatibile con le versioni precedenti di Android, un'applicazione Android deve usare la compatibilità API trovate nel pacchetto NuGet supporto Android v4. Il frammento di codice seguente viene illustrato come ottenere l'oggetto appropriato dal sistema operativo: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

Nel frammento precedente, il `context` è qualsiasi Android `Android.Content.Context`. In genere si tratta di un'attività che esegue l'autenticazione.

## <a name="checking-for-eligibility"></a>Verifica l'idoneità

Un'applicazione deve eseguire più controlli per assicurarsi che sia possibile utilizzare l'autenticazione impronta digitale. In totale, sono disponibili cinque condizioni che l'applicazione utilizza per controllare idoneità:  
 

**Livello API 23** &ndash; l'API di impronta digitale richiedono il livello API 23 o versione successiva. La `FingerprintManagerCompat` classe passerà il controllo del livello di API per l'utente. Per questo motivo è consigliabile utilizzare il **libreria di supporto Android v4** e `FingerprintManagerCompat`; questo verrà tenuto in considerazione uno di questi controlli.

**Hardware** &ndash; quando l'applicazione viene avviata per la prima volta, è necessario verificare la presenza di uno scanner di impronta digitale:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```
    
**Dispositivo è protetto** &ndash; l'utente deve disporre del dispositivo protetto con un blocco dello schermo. Se l'utente non ha protetto il dispositivo con un blocco dello schermo e la sicurezza è importante per l'applicazione, l'utente deve avvisato che è necessario configurare un blocco dello schermo. Frammento di codice riportato di seguito viene illustrato come controllare questa pre-requiste:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Le impronte digitali registrati** &ndash; l'utente deve disporre almeno delle impronte digitali registrata con il sistema operativo. Questo controllo di autorizzazione debba verificarsi prima di ogni tentativo di autenticazione:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Autorizzazioni** &ndash; l'applicazione deve richiedere l'autorizzazione da parte dell'utente prima di utilizzare l'applicazione. Per Android 5.0 e inferiore, l'utente concede l'autorizzazione come condizione per l'installazione dell'app. Android 6.0 introdotto un nuovo modello di autorizzazione che controlla le autorizzazioni in fase di esecuzione. Questo frammento di codice è riportato un esempio di come controllare le autorizzazioni in Android 6.0:

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
    // http://developer.android.com/training/permissions/requesting.html
}
```

Un'applicazione deve verificare le condizioni, 3, 4 e 5 ogni volta che desidera utilizzare l'autenticazione impronta digitale. Le prime due condizioni possono essere verificate la prima volta che un'applicazione viene eseguita in un dispositivo e i risultati salvati (in condiviso preferenze, ad esempio).

Per ulteriori informazioni su come richiedere le autorizzazioni in Android 6.0, consultare la Guida Android [richiesta di autorizzazioni in fase di esecuzione](http://developer.android.com/training/permissions/requesting.html).



## <a name="related-links"></a>Collegamenti correlati

- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [La richiesta di autorizzazioni in fase di esecuzione](http://developer.android.com/training/permissions/requesting.html)
