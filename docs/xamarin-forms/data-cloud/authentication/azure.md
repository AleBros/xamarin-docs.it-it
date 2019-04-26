---
title: Autenticazione degli utenti con App per dispositivi mobili di Azure
description: Questo articolo illustra come usare App per dispositivi mobili di Azure per gestire il processo di autenticazione in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: D50D6F56-8B19-44E7-81F3-E0E1C6E240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 428e536d6895ff16a928f8cc40a8a7976d087471
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331326"
---
# <a name="authenticating-users-with-azure-mobile-apps"></a>Autenticazione degli utenti con App per dispositivi mobili di Azure

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)

_Le App per dispositivi mobili di Azure usare un'ampia gamma di provider di identità esterni per supportare l'autenticazione e autorizzazione degli utenti dell'applicazione, inclusi Facebook, Google, Microsoft, Twitter e Azure Active Directory. Per limitare l'accesso ai soli utenti autenticati, è possono impostare le autorizzazioni sulle tabelle. Questo articolo illustra come usare App per dispositivi mobili di Azure per gestire il processo di autenticazione in un'applicazione xamarin. Forms._

## <a name="overview"></a>Panoramica

Il processo per avere App per dispositivi mobili di Azure di gestire il processo di autenticazione in un'applicazione xamarin. Forms è come segue:

1. Registrare l'App per dispositivi mobili di Azure nel sito di un provider di identità e quindi impostare le credenziali generato dal provider nel back-end di App per dispositivi mobili. Per altre informazioni, vedere [registrare l'app per l'autenticazione e configurare i servizi App](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#register-your-app-for-authentication-and-configure-app-services).
1. Definire un nuovo schema URL per l'applicazione xamarin. Forms, che consente al sistema di autenticazione reindirizza all'applicazione xamarin. Forms, una volta completato il processo di autenticazione. Per altre informazioni, vedere [aggiungere l'app in consentiti di reindirizzamento URL esterni](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#redirecturl).
1. Limitare l'accesso al back-end di App per dispositivi mobili di Azure per soli client autenticati. Per altre informazioni, vedere [limitare le autorizzazioni per gli utenti autenticati](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#restrict-permissions-to-authenticated-users).
1. Richiamare l'autenticazione dall'applicazione xamarin. Forms. Per altre informazioni, vedere [aggiungere l'autenticazione alla libreria di classi portabile](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-portable-class-library), [aggiungere l'autenticazione all'app iOS](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-ios-app), [aggiungere l'autenticazione all'app Android](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-android-app)e [ Aggiungere l'autenticazione a progetti di app di Windows 10](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-windows-10-including-phone-app-projects).

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
> Può essere scelto ATS fuori se non è possibile usare il `HTTPS` protocol e proteggere le comunicazioni per le risorse internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

In passato, applicazioni per dispositivi mobili hanno usato le visualizzazioni web incorporate per eseguire l'autenticazione con un provider di identità. Ciò non è più consigliata per i motivi seguenti:

- L'applicazione che ospita la visualizzazione web sono accessibili le credenziali dell'utente un'autenticazione completa, non solo la concessione di autorizzazione di cui è previsto per l'applicazione. Questa condizione viola il principio del privilegio minimo, come l'applicazione può accedere a credenziali più elevate rispetto a quanto richiesto, aumentando potenzialmente la superficie di attacco dell'applicazione.
- L'applicazione host è stato possibile acquisire i nomi utente e password, automaticamente invio dei moduli e ignorare il consenso dell'utente e copiare i cookie di sessione e usarli per eseguire azioni autenticate come utente.
- Visualizzazioni web incorporate non condividono lo stato dell'autenticazione con altre applicazioni o web browser del dispositivo, richiedere all'utente di effettuare l'accesso per ogni richiesta di autorizzazione che viene considerata come un'esperienza utente sia inferiore.
- Alcuni endpoint di autorizzazione intervenire per rilevare e bloccare le richieste di autorizzazione che provengono da visualizzazioni web.

L'alternativa consiste nell'usare web browser del dispositivo per eseguire l'autenticazione, che è l'approccio adottato per la versione più recente del [Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/). Usando il browser del dispositivo per le richieste di autenticazione migliora l'usabilità di un'applicazione, come gli utenti devono solo eseguire l'accesso al provider di identità una sola volta per ogni dispositivo, migliorando i tassi di conversione dei flussi di accesso e autorizzazione nell'applicazione. Il browser del dispositivo offre inoltre una maggiore sicurezza, come le applicazioni in grado di ispezionare e modificare contenuto in una visualizzazione web, ma non il contenuto visualizzato nel browser.

## <a name="using-an-azure-mobile-apps-instance"></a>Utilizzo di un'istanza di App per dispositivi mobili di Azure

Il [Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornisce il `MobileServiceClient` (classe), che viene usato da un'applicazione xamarin. Forms per accedere all'istanza di App per dispositivi mobili di Azure.

L'applicazione di esempio Usa Google come provider di identità, che consente agli utenti con account Google all'account di accesso per l'applicazione xamarin. Forms. Mentre viene usato Google come provider di identità in questo articolo, l'approccio è ugualmente applicabile ad altri provider di identità.

<a name="logging-in" />

### <a name="logging-in-users"></a>Accesso degli utenti

Schermata di accesso nell'applicazione di esempio viene illustrata negli screenshot seguenti:

![](azure-images/login.png "Pagina di accesso")

Mentre viene utilizzato Google come provider di identità, un'ampia gamma di altri provider di identità può essere utilizzata, inclusi Facebook, Microsoft, Twitter e Azure Active Directory.

Esempio di codice seguente mostra come viene richiamata la procedura di accesso:

```csharp
async void OnLoginButtonClicked(object sender, EventArgs e)
{
  ...
  if (App.Authenticator != null)
  {
    authenticated = await App.Authenticator.AuthenticateAsync();
  }
  ...
}
```

Il `App.Authenticator` proprietà è un `IAuthenticate` istanza in cui è impostato per ogni progetto specifico della piattaforma. Il `IAuthenticate` interfaccia specifica un `AuthenticateAsync` operazione che deve essere fornito per ogni progetto della piattaforma. Richiamo di conseguenza, il `App.Authenticator.AuthenticateAsync` metodo viene eseguito il `IAuthenticate.AuthenticateAsync` metodo in un progetto di piattaforma.

Tutte le piattaforme `IAuthenticate.AuthenticateAsync` chiamata di metodi di `MobileServiceClient.LoginAsync` metodo per visualizzare i dati di interfaccia e cache di un account di accesso. Nell'esempio di codice riportato di seguito viene illustrato il `LoginAsync` metodo per la piattaforma iOS:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    UIApplication.SharedApplication.KeyWindow.RootViewController,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

Nell'esempio di codice riportato di seguito viene illustrato il `LoginAsync` metodo per la piattaforma Android:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    this,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

Nell'esempio di codice riportato di seguito viene illustrato il `LoginAsync` metodo per la piattaforma Windows universale:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

In tutte le piattaforme, il `MobileServiceAuthenticationProvider` enumerazione viene utilizzata per specificare il provider di identità che verrà usato nel processo di autenticazione. Quando il `MobileServiceClient.LoginAsync` metodo viene richiamato, l'App per dispositivi mobili di Azure avvia un flusso di autenticazione visualizzando la pagina di accesso del provider selezionato e generando un token di autenticazione dopo aver eseguito l'accesso con il provider di identità. Il `MobileServiceClient.LoginAsync` metodo restituisce un `MobileServiceUser` istanza in cui verrà archiviato nel `MobileServiceClient.CurrentUser` proprietà. Questa proprietà fornisce `UserId` e `MobileServiceAuthenticationToken` proprietà. Questi rappresentano l'utente autenticato e un token di autenticazione per l'utente. Il token di autenticazione verrà incluso in tutte le richieste effettuate all'istanza di App per dispositivi mobili di Azure, consentendo all'applicazione xamarin. Forms eseguire azioni nell'istanza di App per dispositivi mobili di Azure che richiedono le autorizzazioni utente autenticato.

<a name="logging-out" />

### <a name="logging-out-users"></a>La disconnessione degli utenti

Esempio di codice seguente mostra come viene richiamato il processo di disconnessione:

```csharp
async void OnLogoutButtonClicked(object sender, EventArgs e)
{
  bool loggedOut = false;

  if (App.Authenticator != null)
  {
    loggedOut = await App.Authenticator.LogoutAsync ();
  }
  ...
}
```

Il `App.Authenticator` proprietà è un `IAuthenticate` istanza impostato da ogni platformproject. Il `IAuthenticate` interfaccia specifica un `LogoutAsync` operazione che deve essere fornito per ogni progetto della piattaforma. Richiamo di conseguenza, il `App.Authenticator.LogoutAsync` metodo viene eseguito il `IAuthenticate.LogoutAsync` metodo in un progetto di piattaforma.

Tutte le piattaforme `IAuthenticate.LogoutAsync` chiamata di metodi di `MobileServiceClient.LogoutAsync` metodo deprovisioning autenticare l'utente ha eseguito l'accesso con il provider di identità. Nell'esempio di codice riportato di seguito viene illustrato il `LogoutAsync` metodo per la piattaforma iOS:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  foreach (var cookie in NSHttpCookieStorage.SharedStorage.Cookies)
  {
    NSHttpCookieStorage.SharedStorage.DeleteCookie (cookie);
  }
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

Nell'esempio di codice riportato di seguito viene illustrato il `LogoutAsync` metodo per la piattaforma Android:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  CookieManager.Instance.RemoveAllCookie();
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

Nell'esempio di codice riportato di seguito viene illustrato il `LogoutAsync` metodo per la piattaforma Windows universale:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

Quando la `IAuthenticate.LogoutAsync` metodo viene richiamato, vengono cancellati tutti i cookie impostati dal provider di identità, prima che il `MobileServiceClient.LogoutAsync` metodo viene richiamato per deprovisioning autenticare l'utente ha eseguito l'accesso con il provider di identità.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare App per dispositivi mobili di Azure per gestire il processo di autenticazione in un'applicazione xamarin. Forms. Le App per dispositivi mobili di Azure usare un'ampia gamma di provider di identità esterni per supportare l'autenticazione e autorizzazione degli utenti dell'applicazione, inclusi Facebook, Google, Microsoft, Twitter e Azure Active Directory. Il `MobileServiceClient` classe viene utilizzata dall'applicazione xamarin. Forms per controllare l'accesso all'istanza di App per dispositivi mobili di Azure.


## <a name="related-links"></a>Collegamenti correlati

- [TodoAzureAuth (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)
- [Utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Aggiungere l'autenticazione all'App xamarin. Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users/)
- [Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
