---
title: L'autenticazione degli utenti con App per dispositivi mobili di Azure
description: "Azure App per dispositivi mobili di utilizzare un'ampia gamma di provider di identità esterno per supportare l'autenticazione e autorizzazione degli utenti dell'applicazione, inclusi Facebook, Google, Microsoft, Twitter e Azure Active Directory. Per limitare l'accesso solo agli utenti autenticati, è possano impostare autorizzazioni su tabelle. In questo articolo viene illustrato come utilizzare App mobili di Azure per gestire il processo di autenticazione in un'applicazione di xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: D50D6F56-8B19-44E7-81F3-E0E1C6E240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 823dcdfdaca79045a407b62ec7e75079ee25d72f
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="authenticating-users-with-azure-mobile-apps"></a>L'autenticazione degli utenti con App per dispositivi mobili di Azure

_Azure App per dispositivi mobili di utilizzare un'ampia gamma di provider di identità esterno per supportare l'autenticazione e autorizzazione degli utenti dell'applicazione, inclusi Facebook, Google, Microsoft, Twitter e Azure Active Directory. Per limitare l'accesso solo agli utenti autenticati, è possano impostare autorizzazioni su tabelle. In questo articolo viene illustrato come utilizzare App mobili di Azure per gestire il processo di autenticazione in un'applicazione di xamarin. Forms._

## <a name="overview"></a>Panoramica

Il processo per l'App mobili di Azure di gestire il processo di autenticazione in un'applicazione di xamarin. Forms è come segue:

1. Registrare l'App Mobile di Azure nel sito del provider di identità e quindi impostare le credenziali generato dal provider in App per dispositivi mobili back-end. Per ulteriori informazioni, vedere [registrare l'app per l'autenticazione e configurare servizi App](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#register-your-app-for-authentication-and-configure-app-services).
1. Definire un nuovo schema di URL per l'applicazione di xamarin. Forms, che consente al sistema di autenticazione per il reindirizzamento all'applicazione di xamarin. Forms, una volta completato il processo di autenticazione. Per ulteriori informazioni, vedere [aggiungere app consentita esterno gli URL di reindirizzamento](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#redirecturl).
1. Limitare l'accesso al back-end di App mobili di Azure per solo client autenticati. Per ulteriori informazioni, vedere [limitare le autorizzazioni per gli utenti autenticati](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#restrict-permissions-to-authenticated-users).
1. Richiamare l'autenticazione dell'applicazione di xamarin. Forms. Per ulteriori informazioni, vedere [aggiungere l'autenticazione per la libreria di classi portabile](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-portable-class-library), [aggiungere l'autenticazione all'app di iOS](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-ios-app), [aggiungere l'autenticazione dell'app Android di](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-android-app)e [ Aggiungere l'autenticazione per i progetti di app di Windows 10](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-windows-10-including-phone-app-projects).

> [!NOTE]
> In iOS 9 e versioni successive, sicurezza trasporto App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app, impedendo in tal modo la diffusione accidentale di informazioni riservate. Poiché AT è attivata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.
> Può essere scelto at fuori se non è possibile utilizzare il `HTTPS` del protocollo e proteggere le comunicazioni per le risorse internet. Ciò può essere ottenuto l'aggiornamento dell'app **Info. plist** file. Per ulteriori informazioni vedere [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).

In passato, applicazioni per dispositivi mobili hanno utilizzato visualizzazioni web incorporata per eseguire l'autenticazione con provider di identità. Questo non è più consigliato per i motivi seguenti:

- L'applicazione che ospita la visualizzazione web può accedere a credenziali di autenticazione completa dell'utente, non solo la concessione di autorizzazioni previsto per l'applicazione. Questa condizione viola il principio di privilegio minimo, l'applicazione abbia accesso a credenziali più elevate rispetto a richiesto potenzialmente aumentando la superficie di attacco dell'applicazione.
- L'applicazione host può acquisire i nomi utente e password, automaticamente dell'invio dei moduli e ignorare il consenso dell'utente e copiare i cookie di sessione e usarli per eseguire azioni autenticate come utente.
- Visualizzazioni web incorporato non condividono lo stato di autenticazione con altre applicazioni o browser del dispositivo, richiedere all'utente di accesso per ogni richiesta di autorizzazione che è considerata un'esperienza utente inferiore.
- Alcuni endpoint di autorizzazione eseguire i passaggi per rilevare e bloccare le richieste di autorizzazione che provengono da visualizzazioni web.

L'alternativa consiste nell'utilizzare i web browser del dispositivo per eseguire l'autenticazione, che è l'approccio adottato per la versione più recente del [Client mobili di Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/). Tramite il browser del dispositivo per le richieste di autenticazione migliora l'usabilità di un'applicazione, come gli utenti devono solo per accedere al provider di identità di una volta per ogni dispositivo, migliorando i tassi di conversione dei flussi di accesso e autorizzazione nell'applicazione. Il browser del dispositivo offre inoltre una maggiore sicurezza, come le applicazioni sono in grado di controllare e modificare contenuto in una visualizzazione web, ma non visualizzato nel Visualizzatore.

## <a name="using-an-azure-mobile-apps-instance"></a>Utilizzo di un'istanza di App per dispositivi mobili di Azure

Il [Client mobili di Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornisce il `MobileServiceClient` (classe), viene utilizzato da un'applicazione di xamarin. Forms per accedere all'istanza di App mobili di Azure.

L'applicazione di esempio utilizza Google come provider di identità, che consente agli utenti con account di Google per l'account di accesso per l'applicazione di xamarin. Forms. Mentre Google viene utilizzata come provider di identità in questo articolo, l'approccio è applicabile ad altri provider di identità.

<a name="logging-in" />

### <a name="logging-in-users"></a>La connessione degli utenti

Schermata di accesso nell'applicazione di esempio è illustrata nelle schermate seguenti:

![](azure-images/login.png "Pagina di accesso")

Mentre Google viene utilizzata come provider di identità, un'ampia gamma di altri provider di identità è utilizzabile, tra cui Azure Active Directory, Microsoft, Twitter e Facebook.

Esempio di codice seguente viene illustrato come viene richiamata la procedura di accesso:

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

Il `App.Authenticator` proprietà è un `IAuthenticate` istanza di ogni progetto specifico della piattaforma. Il `IAuthenticate` interfaccia specifica un `AuthenticateAsync` operazione che deve essere fornito per ogni progetto della piattaforma. Pertanto, la chiamata di `App.Authenticator.AuthenticateAsync` il metodo viene eseguito il `IAuthenticate.AuthenticateAsync` metodo in un progetto di piattaforma.

Tutta la piattaforma `IAuthenticate.AuthenticateAsync` chiamata di metodi di `MobileServiceClient.LoginAsync` metodo per visualizzare i dati di interfaccia e cache di un account di accesso. Nell'esempio di codice riportato di seguito viene illustrato il `LoginAsync` metodo per la piattaforma iOS:

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

In tutte le piattaforme, il `MobileServiceAuthenticationProvider` enumerazione viene utilizzata per specificare il provider di identità che verrà utilizzato nel processo di autenticazione. Quando il `MobileServiceClient.LoginAsync` metodo viene richiamato, applicazioni per dispositivi mobili di Azure avvia un flusso di autenticazione per la visualizzazione della pagina di accesso del provider selezionato e generando un token di autenticazione dopo aver eseguito correttamente l'accesso con il provider di identità. Il `MobileServiceClient.LoginAsync` metodo restituisce un `MobileServiceUser` istanza in cui verrà archiviato nel `MobileServiceClient.CurrentUser` proprietà. Questa proprietà fornisce `UserId` e `MobileServiceAuthenticationToken` proprietà. Questi rappresentano l'utente autenticato e un token di autenticazione per l'utente. Il token di autenticazione verrà inclusi in tutte le richieste effettuate all'istanza di App mobili di Azure, consentendo all'applicazione di xamarin. Forms eseguire azioni sull'istanza di App per dispositivi mobili di Azure che richiedono autorizzazioni di utente autenticato.

<a name="logging-out" />

### <a name="logging-out-users"></a>La disconnessione degli utenti

Esempio di codice seguente viene illustrato come viene richiamato la del processo di disconnessione:

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

Il `App.Authenticator` proprietà è un `IAuthenticate` istanza impostato da ogni platformproject. Il `IAuthenticate` interfaccia specifica un `LogoutAsync` operazione che deve essere fornito per ogni progetto della piattaforma. Pertanto, la chiamata di `App.Authenticator.LogoutAsync` il metodo viene eseguito il `IAuthenticate.LogoutAsync` metodo in un progetto di piattaforma.

Tutta la piattaforma `IAuthenticate.LogoutAsync` chiamata di metodi di `MobileServiceClient.LogoutAsync` metodo per deallocare autenticare l'utente connesso con il provider di identità. Nell'esempio di codice riportato di seguito viene illustrato il `LogoutAsync` metodo per la piattaforma iOS:

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

Quando il `IAuthenticate.LogoutAsync` metodo viene richiamato, vengono eliminati i cookie impostati dal provider di identità, prima di `MobileServiceClient.LogoutAsync` metodo viene richiamato per disattivare l'autenticazione dell'utente connesso con il provider di identità.

## <a name="summary"></a>Riepilogo

In questo articolo viene spiegato come utilizzare App mobili di Azure per gestire il processo di autenticazione in un'applicazione di xamarin. Forms. Azure App per dispositivi mobili di utilizzare un'ampia gamma di provider di identità esterno per supportare l'autenticazione e autorizzazione degli utenti dell'applicazione, inclusi Facebook, Google, Microsoft, Twitter e Azure Active Directory. La `MobileServiceClient` classe viene utilizzata dall'applicazione xamarin. Forms per controllare l'accesso all'istanza di App mobili di Azure.


## <a name="related-links"></a>Collegamenti correlati

- [TodoAzureAuth (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)
- [Utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Aggiungere l'autenticazione all'App xamarin. Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users/)
- [Client per dispositivi mobili di Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
