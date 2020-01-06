---
title: Touch ID e Face ID con Novell. iOS
description: Questo documento fornisce una descrizione di alto livello dell'autenticazione biometrica in iOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: profexorgeek
ms.author: jusjohns
ms.date: 12/16/2019
ms.openlocfilehash: 744a07343b9da87f196c0664f57b7d950844ab04
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490297"
---
# <a name="use-touch-id-and-face-id-with-xamarinios"></a>Usare Touch ID e Face ID con Novell. iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-faceidsample/)

iOS supporta due sistemi di autenticazione biometrica:

1. **Touch ID** usa un sensore di impronta digitale sotto il pulsante Home.
1. Il **Face ID** usa i sensori della fotocamera front-end per autenticare gli utenti con un'analisi facciale.

Touch ID è stato introdotto in iOS 7 e ID viso in iOS 11.

Questi sistemi di autenticazione si basano su un processore di sicurezza basato su hardware chiamato _Secure Enclave_. L'enclave protetta è responsabile della crittografia delle rappresentazioni matematiche dei dati del viso e delle impronte digitali e dell'autenticazione degli utenti con queste informazioni. In base a Apple, i dati relativi ai visi e alle impronte digitali non lasciano il dispositivo e non vengono sottoposti a backup in iCloud. Le app interagiscono con l'enclave protetta tramite l'API di _autenticazione locale_ e non possono recuperare i dati relativi a visi o impronte digitali o accedere direttamente all'enclave protetta.

L'ID tocco e il Face ID possono essere usati dalle app per autenticare un utente prima di fornire l'accesso al contenuto protetto.

## <a name="local-authentication-context"></a>Contesto di autenticazione locale

L'autenticazione biometrica in iOS si basa su un oggetto del _contesto di autenticazione locale_ , che è un'istanza della classe `LAContext`. La classe `LAContext` consente di:

- Verificare la disponibilità dell'hardware biometrico.
- Valutare i criteri di autenticazione.
- Valutare i controlli di accesso.
- Personalizzare e visualizzare le richieste di autenticazione.
- Riutilizzare o invalidare uno stato di autenticazione.
- Gestisci credenziali.

## <a name="detect-available-authentication-methods"></a>Rilevare i metodi di autenticazione disponibili

Il progetto di esempio include un `AuthenticationView` supportato da un `AuthenticationViewController`. Questa classe esegue l'override del metodo `ViewWillAppear` per rilevare i metodi di autenticazione disponibili:

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    public override void ViewWillAppear(bool animated)
    {
        base.ViewWillAppear(animated);
        unAuthenticatedLabel.Text = "";
    
        var context = new LAContext();
        var buttonText = "";

        // Is login with biometrics possible?
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out var authError1))
        {
            // has Touch ID or Face ID
            if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
            {
                context.LocalizedReason = "Authorize for access to secrets"; // iOS 11
                BiometryType = context.BiometryType == LABiometryType.TouchId ? "Touch ID" : "Face ID";
                buttonText = $"Login with {BiometryType}";
            }
            // No FaceID before iOS 11
            else
            {
                buttonText = $"Login with Touch ID";
            }
        }

        // Is pin login possible?
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out var authError2))
        {
            buttonText = $"Login"; // with device PIN
            BiometryType = "Device PIN";
        }

        // Local authentication not possible
        else
        {
            // Application might choose to implement a custom username/password
            buttonText = "Use unsecured";
            BiometryType = "none";
        }
        AuthenticateButton.SetTitle(buttonText, UIControlState.Normal);
    }
}
```

Il metodo `ViewWillAppear` viene chiamato quando l'interfaccia utente sta per essere visualizzata all'utente. Questo metodo definisce una nuova istanza di `LAContext` e usa il metodo `CanEvaluatePolicy` per determinare se l'autenticazione biometrica è abilitata. In tal caso, controlla la versione del sistema e `BiometryType` enum per determinare quali opzioni biometriche sono disponibili.

Se l'autenticazione biometrica non è abilitata, l'app tenta di eseguire il fallback per l'autenticazione del PIN. Se non è disponibile alcuna autenticazione biometrica o PIN, il proprietario del dispositivo non ha abilitato le funzionalità di sicurezza e il contenuto non può essere protetto tramite l'autenticazione locale.

## <a name="authenticate-a-user"></a>Autenticare un utente

Il `AuthenticationViewController` nel progetto di esempio include un metodo `AuthenticateMe`, che è responsabile dell'autenticazione dell'utente:

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    partial void AuthenticateMe(UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var localizedReason = new NSString("To access secrets");
    
        // Because LocalAuthentication APIs have been extended over time,
        // you must check iOS version before setting some properties
        context.LocalizedFallbackTitle = "Fallback";
    
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            context.LocalizedCancelTitle = "Cancel";
        }
        if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
        {
            context.LocalizedReason = "Authorize for access to secrets";
            BiometryType = context.BiometryType == LABiometryType.TouchId ? "TouchID" : "FaceID";
        }
    
        // Check if biometric authentication is possible
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = $"{BiometryType} Authentication Failed";
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, localizedReason, replyHandler);
        }

        // Fall back to PIN authentication
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = "Device PIN Authentication Failed";
                        AuthenticateButton.Hidden = true;
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, localizedReason, replyHandler);
        }

        // User hasn't configured any authentication: show dialog with options
        else
        {
            unAuthenticatedLabel.Text = "No device auth configured";
            var okCancelAlertController = UIAlertController.Create("No authentication", "This device does't have authentication configured.", UIAlertControllerStyle.Alert);
            okCancelAlertController.AddAction(UIAlertAction.Create("Use unsecured", UIAlertActionStyle.Default, alert => PerformSegue("AuthenticationSegue", this)));
            okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine("Cancel was clicked")));
            PresentViewController(okCancelAlertController, true, null);
        }
    } 
}
```

Il metodo `AuthenticateMe` viene chiamato in risposta all'utente che tocca un pulsante di **accesso** . Viene creata un'istanza di un nuovo oggetto `LAContext` e viene verificata la versione del dispositivo per determinare le proprietà da impostare nel contesto di autenticazione locale.

Viene chiamato il metodo `CanEvaluatePolicy` per verificare se l'autenticazione biometrica è abilitata, eseguire il fallback per aggiungere l'autenticazione, se possibile, e infine offrire una modalità non protetta se non è disponibile alcuna autenticazione. Se è disponibile un metodo di autenticazione, viene usato il metodo `EvaluatePolicy` per visualizzare l'interfaccia utente e completare il processo di autenticazione.

Il progetto di esempio contiene dati fittizi e una visualizzazione per visualizzare i dati se l'autenticazione ha esito positivo.

## <a name="related-links"></a>Collegamenti correlati

- [Autenticazione locale con l'esempio Touch ID o Face ID](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-faceidsample/)
- [Informazioni sul Touch ID](https://support.apple.com/en-us/HT204587) su support.Apple.com
- [Informazioni su ID faccia](https://support.apple.com/en-us/HT208108) su support.Apple.com
