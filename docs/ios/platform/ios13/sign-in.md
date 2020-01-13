---
title: Accedere con Apple in Xamarin.iOS
description: L'accesso con Apple fornisce la protezione delle identità quando si usano i servizi di autenticazione di terze parti.
ms.prod: xamarin
ms.assetid: CDA59BBF-AAE1-4D4F-B4AE-DB37220D41EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: d8c458ad30d7e281427dad0e29092c55fede7347
ms.sourcegitcommit: fc689c1a6b641c124378dedc1bd157d96fc759a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71319532"
---
# <a name="sign-in-with-apple-in-xamarinios"></a>Accedere con Apple in Xamarin.iOS

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/ios13-addingthesigninwithappleflowtoyourapp/)

L'accesso con Apple è un nuovo servizio che fornisce la protezione delle identità per gli utenti di servizi di autenticazione di terze parti. A partire da iOS 13, Apple richiede che qualsiasi nuova app che usa un servizio di autenticazione di terze parti fornisca anche l'accesso con Apple. Le app esistenti in fase di aggiornamento non devono aggiungere l'accesso con Apple fino al 2020 aprile.

Questo documento illustra come è possibile aggiungere l'accesso con Apple alle applicazioni iOS 13.

## <a name="apple-developer-setup"></a>Installazione di Apple Developer

Prima di compilare ed eseguire un'app usando l'accesso con Apple, è necessario completare questi passaggi. Nei [certificati per sviluppatori Apple, identificatori & portale profili][5] :

1. Creare un nuovo identificatore **ID app** .
2. Impostare una descrizione nel campo **Descrizione** .
3. Scegliere un ID bundle **esplicito** e `com.xamarin.AddingTheSignInWithAppleFlowToYourApp` impostarlo nel campo.
4. Abilitare l' **accesso con** la funzionalità Apple e registrare la nuova identità.
5. Creare un nuovo profilo di provisioning con la nuova identità.
6. Scaricarlo e installarlo nel dispositivo.
7. In Visual Studio abilitare la funzionalità di **accesso con Apple** nel file **titles. plist** .

## <a name="check-sign-in-status"></a>Controllare lo stato di accesso

All'avvio dell'app o quando è necessario controllare lo stato di autenticazione di un utente, creare un' `ASAuthorizationAppleIdProvider` istanza di e controllare lo stato corrente:

```csharp
var appleIdProvider = new ASAuthorizationAppleIdProvider ();
appleIdProvider.GetCredentialState (KeychainItem.CurrentUserIdentifier, (credentialState, error) => {
    switch (credentialState) {
    case ASAuthorizationAppleIdProviderCredentialState.Authorized:
        // The Apple ID credential is valid.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.Revoked:
        // The Apple ID credential is revoked.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.NotFound:
        // No credential was found, so show the sign-in UI.
        InvokeOnMainThread (() => {
            var storyboard = UIStoryboard.FromName ("Main", null);

            if (!(storyboard.InstantiateViewController (nameof (LoginViewController)) is LoginViewController viewController))
                return;

            viewController.ModalPresentationStyle = UIModalPresentationStyle.FormSheet;
            viewController.ModalInPresentation = true;
            Window?.RootViewController?.PresentViewController (viewController, true, null);
        });
        break;
    }
});
```

In questo codice, chiamato durante `FinishedLaunching` `AppDelegate.cs`in, l'applicazione gestirà quando uno `LoginViewController` stato è `NotFound` e presenta all'utente. Se lo stato ha restituito `Authorized` o `Revoked`, un'azione diversa può essere presentata all'utente.

## <a name="a-loginviewcontroller-for-sign-in-with-apple"></a>Un LoginViewController per l'accesso con Apple

Il `UIViewController` che implementa la logica di accesso e offre l'accesso con Apple deve `IASAuthorizationControllerDelegate` implementare `IASAuthorizationControllerPresentationContextProviding` e come nell' `LoginViewController` esempio riportato di seguito.

```csharp
public partial class LoginViewController : UIViewController, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding {
    public LoginViewController (IntPtr handle) : base (handle)
    {
    }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.

        SetupProviderLoginView ();
    }

    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        PerformExistingAccountSetupFlows ();
    }

    void SetupProviderLoginView ()
    {
        var authorizationButton = new ASAuthorizationAppleIdButton (ASAuthorizationAppleIdButtonType.Default, ASAuthorizationAppleIdButtonStyle.White);
        authorizationButton.TouchUpInside += HandleAuthorizationAppleIDButtonPress;
        loginProviderStackView.AddArrangedSubview (authorizationButton);
    }

    // Prompts the user if an existing iCloud Keychain credential or Apple ID credential is found.
    void PerformExistingAccountSetupFlows ()
    {
        // Prepare requests for both Apple ID and password providers.
        ASAuthorizationRequest [] requests = {
            new ASAuthorizationAppleIdProvider ().CreateRequest (),
            new ASAuthorizationPasswordProvider ().CreateRequest ()
        };

        // Create an authorization controller with the given requests.
        var authorizationController = new ASAuthorizationController (requests);
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }

    private void HandleAuthorizationAppleIDButtonPress (object sender, EventArgs e)
    {
        var appleIdProvider = new ASAuthorizationAppleIdProvider ();
        var request = appleIdProvider.CreateRequest ();
        request.RequestedScopes = new [] { ASAuthorizationScope.Email, ASAuthorizationScope.FullName };

        var authorizationController = new ASAuthorizationController (new [] { request });
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }
}
```

![Animazione dell'app di esempio con l'accesso con Apple](sign-in-images/sign-in-flow.png)

Questo codice di esempio controlla lo stato corrente dell' `PerformExistingAccountSetupFlows` account di accesso in e si connette alla visualizzazione corrente come delegato. Se viene rilevata una credenziale di Keychain o una credenziale ID Apple esistente, all'utente verrà richiesto di usarla.

Apple fornisce `ASAuthorizationAppleIdButton`un pulsante specifico a questo scopo. Quando viene toccato, il pulsante attiverà il flusso di lavoro gestito nel `HandleAuthorizationAppleIDButtonPress`metodo.

## <a name="handling-authorization"></a>Gestione dell'autorizzazione

`IASAuthorizationController` In implementare qualsiasi logica personalizzata per archiviare l'account dell'utente. Nell'esempio seguente viene archiviato l'account dell'utente nel keychain, il servizio di archiviazione di Apple.

```csharp
#region IASAuthorizationController Delegate

[Export ("authorizationController:didCompleteWithAuthorization:")]
public void DidComplete (ASAuthorizationController controller, ASAuthorization authorization)
{
    if (authorization.GetCredential<ASAuthorizationAppleIdCredential> () is ASAuthorizationAppleIdCredential appleIdCredential) {
        var userIdentifier = appleIdCredential.User;
        var fullName = appleIdCredential.FullName;
        var email = appleIdCredential.Email;

        // Create an account in your system.
        // For the purpose of this demo app, store the userIdentifier in the keychain.
        try {
            new KeychainItem ("com.example.apple-samplecode.juice", "userIdentifier").SaveItem (userIdentifier);
        } catch (Exception) {
            Console.WriteLine ("Unable to save userIdentifier to keychain.");
        }

        // For the purpose of this demo app, show the Apple ID credential information in the ResultViewController.
        if (!(PresentingViewController is ResultViewController viewController))
            return;

        InvokeOnMainThread (() => {
            viewController.UserIdentifierText = userIdentifier;
            viewController.GivenNameText = fullName?.GivenName ?? "";
            viewController.FamilyNameText = fullName?.FamilyName ?? "";
            viewController.EmailText = email ?? "";

            DismissViewController (true, null);
        });
    } else if (authorization.GetCredential<ASPasswordCredential> () is ASPasswordCredential passwordCredential) {
        // Sign in using an existing iCloud Keychain credential.
        var username = passwordCredential.User;
        var password = passwordCredential.Password;

        // For the purpose of this demo app, show the password credential as an alert.
        InvokeOnMainThread (() => {
            var message = $"The app has received your selected credential from the keychain. \n\n Username: {username}\n Password: {password}";
            var alertController = UIAlertController.Create ("Keychain Credential Received", message, UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("Dismiss", UIAlertActionStyle.Cancel, null));

            PresentViewController (alertController, true, null);
        });
    }
}

[Export ("authorizationController:didCompleteWithError:")]
public void DidComplete (ASAuthorizationController controller, NSError error)
{
    Console.WriteLine (error);
}

#endregion
```

## <a name="authorization-controller"></a>Controller di autorizzazione

L'ultima parte di questa implementazione è `ASAuthorizationController` che gestisce le richieste di autorizzazione per il provider.

```csharp
#region IASAuthorizationControllerPresentation Context Providing

public UIWindow GetPresentationAnchor (ASAuthorizationController controller) => View.Window;

#endregion
```

## <a name="related-links"></a>Collegamenti correlati

* [Accedere con le linee guida di Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
* [Accedere con diritti Apple.][2]
* [WWDC 2019 sessione 706: Introduzione all'accesso con Apple.][3]
* [Configurare l'accesso con Apple per Xamarin.Forms][4]

[1]: https://developer.apple.com/documentation/authenticationservices/adding_the_sign_in_with_apple_flow_to_your_app
[2]: https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_applesignin
[3]: https://developer.apple.com/videos/play/wwdc19/706/
[4]: ~/xamarin-forms/platform/sign-in-with-apple/setup.md
[5]: https://developer.apple.com/account/resources/identifiers/list
