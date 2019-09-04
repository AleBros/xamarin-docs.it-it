---
title: Framework di social networking in Novell. iOS
description: Il Framework di social networking fornisce un'API unificata per interagire con i social network, inclusi Twitter e Facebook, oltre a SinaWeibo per gli utenti in Cina.
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: c6e0005207a7c6fc444d029c9577684370b4cbf0
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226616"
---
# <a name="social-framework-in-xamarinios"></a>Framework di social networking in Novell. iOS

_Il Framework di social networking fornisce un'API unificata per interagire con i social network, inclusi Twitter e Facebook, oltre a SinaWeibo per gli utenti in Cina._

L'uso del Framework di social networking consente alle applicazioni di interagire con i social network da un'unica API senza dover gestire l'autenticazione. Include un controller di visualizzazione fornito dal sistema per comporre post, oltre a un'astrazione che consente l'utilizzo dell'API di ogni social network tramite HTTP.

> [!IMPORTANT]
> Per la connessione di un'API multipiattaforma a diversi social network, vedere il componente [Novell. Social](http://components.xamarin.com/view/xamarin.social/) nell'archivio componenti di Novell.

## <a name="connecting-to-twitter"></a>Connessione a Twitter

### <a name="twitter-account-settings"></a>Impostazioni dell'account Twitter

Per connettersi a Twitter usando il Framework di social networking, è necessario configurare un account nelle impostazioni del dispositivo, come illustrato di seguito:

 [![](social-framework-images/twitter01.png "Impostazioni dell'account Twitter")](social-framework-images/twitter01.png#lightbox)

Una volta che un account è stato immesso e verificato con Twitter, qualsiasi applicazione nel dispositivo che usa le classi del Framework di social networking per accedere a Twitter utilizzerà questo account.

### <a name="sending-tweets"></a>Invio di Tweet

Il Framework di social networking include un `SLComposeViewController` controller denominato che presenta una visualizzazione fornita dal sistema per la modifica e l'invio di un tweet. La schermata seguente mostra un esempio di questa visualizzazione:

 [![](social-framework-images/twitter02.png "Questa schermata mostra un esempio di SLComposeViewController")](social-framework-images/twitter02.png#lightbox)

Per usare un `SLComposeViewController` con Twitter, è necessario creare un'istanza del controller chiamando il `FromService` metodo con `SLServiceType.Twitter` come illustrato di seguito:

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

Una volta `SLComposeViewController` restituita, l'istanza può essere usata per presentare un'interfaccia utente per pubblicare post su Twitter. Tuttavia, la prima cosa da fare è controllare la disponibilità del social network, in questo caso Twitter, chiamando `IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController`non invia mai direttamente un tweet senza l'intervento dell'utente. Tuttavia, può essere inizializzato con i metodi seguenti:

- `SetInitialText`: Aggiunge il testo iniziale da visualizzare nel Tweet.
- `AddUrl`: Aggiunge un URL al tweet.
- `AddImage`: Aggiunge un'immagine al tweet.


Una volta inizializzato `PresentVIewController` , chiamando viene visualizzata la vista `SLComposeViewController`creata da. L'utente può quindi modificare e inviare il tweet, oppure annullarne l'invio. In entrambi i casi, il controller deve essere chiuso nella `CompletionHandler`, dove il risultato può essere controllato anche per verificare se il tweet è stato inviato o annullato, come illustrato di seguito:

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>Esempio di Tweet

Il codice seguente illustra l'uso `SLComposeViewController` di per presentare una visualizzazione usata per inviare un tweet:

```csharp
using System;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _twitterComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
        #endregion

        #region Computed Properties
        public bool isTwitterAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Twitter); }
        }

        public SLComposeViewController TwitterComposer {
            get { return _twitterComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {

        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            SendTweet.Enabled = isTwitterAvailable;
        }
        #endregion

        #region Actions
        partial void SendTweet_TouchUpInside (UIButton sender)
        {
            // Set initial message
            TwitterComposer.SetInitialText ("Hello Twitter!");
            TwitterComposer.AddImage (UIImage.FromFile ("Icon.png"));
            TwitterComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (TwitterComposer, true, null);
        }
        #endregion
    }
}
```

### <a name="calling-twitter-api"></a>Chiamata all'API Twitter

Il Framework sociale include anche il supporto per l'esecuzione di richieste HTTP ai social network. Incapsula la richiesta in una `SLRequest` classe usata per la destinazione dell'API del social network specifico.

Il codice seguente, ad esempio, effettua una richiesta a Twitter per ottenere la sequenza temporale pubblica (espandendo il codice riportato sopra):

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _twitterAccount;
#endregion

#region Computed Properties
public ACAccount TwitterAccount {
    get { return _twitterAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    SendTweet.Enabled = isTwitterAvailable;
    RequestTwitterTimeline.Enabled = false;

    // Initialize Twitter Account access
    var accountStore = new ACAccountStore ();
    var accountType = accountStore.FindAccountType (ACAccountType.Twitter);

    // Request access to Twitter account
    accountStore.RequestAccess (accountType, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestTwitterTimeline.Enabled = true;
            });
        }
    });
}
#endregion

#region Actions
partial void RequestTwitterTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
    var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = TwitterAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

Esaminiamo il codice in dettaglio. In primo luogo, accede all'archivio account e ottiene il tipo di un account Twitter:

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

Quindi chiede all'utente se l'app può accedere al proprio account Twitter e, se l'accesso viene concesso, l'account viene caricato in memoria e l'interfaccia utente viene aggiornata:

```csharp
// Request access to Twitter account
accountStore.RequestAccess (accountType, (granted, error) => {
    // Allowed by user?
    if (granted) {
        // Get account
        _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
        InvokeOnMainThread (() => {
            // Update UI
            RequestTwitterTimeline.Enabled = true;
        });
    }
});
```

Quando l'utente richiede i dati della sequenza temporale (toccando un pulsante nell'interfaccia utente), l'app crea prima di tutto una richiesta di accesso ai dati da Twitter:

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```

Questo esempio limita i risultati restituiti alle ultime dieci voci includendo `?count=10` nell'URL. Infine, collega la richiesta all'account Twitter (caricato in precedenza) ed esegue la chiamata a Twitter per recuperare i dati:

```csharp
// Request data
request.Account = TwitterAccount;
request.PerformRequest ((data, response, error) => {
    // Was there an error?
    if (error == null) {
        // Was the request successful?
        if (response.StatusCode == 200) {
            // Yes, display it
            InvokeOnMainThread (() => {
                Results.Text = data.ToString ();
            });
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", response.StatusCode);
            });
        }
    } else {
        // No, display error
        InvokeOnMainThread (() => {
            Results.Text = string.Format ("Error: {0}", error);
        });
    }
});
```

Se i dati sono stati caricati correttamente, verranno visualizzati i dati JSON non elaborati, come nell'esempio di output seguente:

[![](social-framework-images/twitter03.png "Esempio di visualizzazione dei dati JSON non elaborati")](social-framework-images/twitter03.png#lightbox)

In un'app reale, i risultati JSON possono essere analizzati normalmente e i risultati presentati all'utente. Per informazioni su come analizzare JSON, vedere [Introduzione ai servizi Web](~/cross-platform/data-cloud/web-services/index.md) .

## <a name="connecting-to-facebook"></a>Connessione a Facebook

### <a name="facebook-account-settings"></a>Impostazioni account Facebook

La connessione a Facebook con il Framework di social networking è quasi identica al processo usato per Twitter illustrato in precedenza. È necessario configurare un account utente di Facebook nelle impostazioni del dispositivo, come illustrato di seguito:

[![](social-framework-images/facebook01.png "Impostazioni account Facebook")](social-framework-images/facebook01.png#lightbox)

Una volta configurata, tutte le applicazioni nel dispositivo che usano il Framework di social networking useranno questo account per connettersi a Facebook.

### <a name="posting-to-facebook"></a>Pubblicazione su Facebook

Poiché il Framework social è un'API unificata progettata per accedere a più social network, il codice rimane quasi identico, indipendentemente dal social network usato.

Ad esempio, `SLComposeViewController` può essere usato esattamente come nell'esempio di Twitter illustrato in precedenza, l'unico diverso è passare alle impostazioni e alle opzioni specifiche di Facebook. Ad esempio:

```csharp
using System;
using Foundation;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _facebookComposer = SLComposeViewController.FromService (SLServiceType.Facebook);
        #endregion

        #region Computed Properties
        public bool isFacebookAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Facebook); }
        }

        public SLComposeViewController FacebookComposer {
            get { return _facebookComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {

        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            PostToFacebook.Enabled = isFacebookAvailable;
        }
        #endregion

        #region Actions
        partial void PostToFacebook_TouchUpInside (UIButton sender)
        {
            // Set initial message
            FacebookComposer.SetInitialText ("Hello Facebook!");
            FacebookComposer.AddImage (UIImage.FromFile ("Icon.png"));
            FacebookComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (FacebookComposer, true, null);
        }
        #endregion
    }
}
```

Se usato con Facebook, `SLComposeViewController` Visualizza una visualizzazione che sembra quasi identica all'esempio di Twitter, mostrando **Facebook** come titolo in questo caso:

[![](social-framework-images/facebook02.png "Visualizzazione SLComposeViewController")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>Chiamata di Facebook API Graph

Analogamente all'esempio di Twitter, l' `SLRequest` oggetto del Framework di social networking può essere usato con l'API Graph di Facebook. Il codice seguente, ad esempio, restituisce informazioni dall'API Graph sull'account Novell (espandendo il codice riportato sopra):

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _facebookAccount;
#endregion

#region Computed Properties
public ACAccount FacebookAccount {
    get { return _facebookAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    PostToFacebook.Enabled = isFacebookAvailable;
    RequestFacebookTimeline.Enabled = false;

    // Initialize Facebook Account access
    var accountStore = new ACAccountStore ();
    var options = new AccountStoreOptions ();
    var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
    accountType = accountStore.FindAccountType (ACAccountType.Facebook);

    // Request access to Facebook account
    accountStore.RequestAccess (accountType, options, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _facebookAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestFacebookTimeline.Enabled = true;
            });
        }
    });

}
#endregion

#region Actions
partial void RequestFacebookTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl ("https://graph.facebook.com/283148898401104");
    var request = SLRequest.Create (SLServiceKind.Facebook, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = FacebookAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

L'unica differenza reale tra questo codice e la versione di Twitter illustrata in precedenza è la necessità di Facebook di ottenere un ID specifico per sviluppatori/app (che è possibile generare dal portale per sviluppatori di Facebook) che deve essere impostato come opzione quando si effettua la richiesta:

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

Se non si imposta questa opzione (o si utilizza una chiave non valida), verrà generato un errore o non verrà restituito alcun dato.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare il Framework di social networking per interagire con Twitter e Facebook. Mostra dove configurare gli account per ogni social network nelle impostazioni del dispositivo. È stato inoltre illustrato come utilizzare `SLComposeViewController` per presentare una visualizzazione unificata per la pubblicazione nei social network. È stata inoltre esaminata `SLRequest` la classe utilizzata per chiamare l'API di ogni social network.


## <a name="related-links"></a>Collegamenti correlati

- [SocialFrameworkDemo (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/socialframeworkdemo)
- [Introduzione ai servizi Web](~/cross-platform/data-cloud/web-services/index.md)
