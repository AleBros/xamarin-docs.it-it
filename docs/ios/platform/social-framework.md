---
title: Framework di social networking
description: "Il Framework di social networking fornisce un'API unificata per l'interazione con i social network inclusi Facebook e Twitter, nonché SinaWeibo per gli utenti in Cina."
ms.topic: article
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 7a190014abd3386a3a675d50ce6a89101d0588a7
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="social-framework"></a>Framework di social networking

_Il Framework di social networking fornisce un'API unificata per l'interazione con i social network inclusi Facebook e Twitter, nonché SinaWeibo per gli utenti in Cina._


Tramite il Framework di social networking consente alle applicazioni di interagire con i social network da una singola API senza la necessità di gestire l'autenticazione. Include un sistema fornito controller di visualizzazione per la composizione di post, nonché un'astrazione che consente l'utilizzo API della rete ogni social su HTTP.

> [!IMPORTANT]
> Per un'API multipiattaforma per connettersi a vari social network, vedere la [Xamarin.Social](http://components.xamarin.com/view/xamarin.social/) componente nell'archivio di componenti di Xamarin.

## <a name="connecting-to-twitter"></a>La connessione a Twitter

### <a name="twitter-account-settings"></a>Impostazioni dell'Account Twitter

Per connettersi a Twitter usando il Framework di social networking, un account deve essere configurata nelle impostazioni del dispositivo, come illustrato di seguito:

 [![](social-framework-images/twitter01.png "Impostazioni dell'Account Twitter")](social-framework-images/twitter01.png#lightbox)

Una volta che un account è stato immesso e verificato con Twitter, qualsiasi applicazione nel dispositivo che utilizza le classi Framework sociale per accedere a Twitter utilizzerà questo account.

### <a name="sending-tweets"></a>Invio TWEET

Il Framework di social networking include un controller denominato `SLComposeViewController` che presenta una visualizzazione di sistema fornito per la modifica e l'invio di un tweet. Nella schermata seguente viene illustrato un esempio di questa vista:

 [![](social-framework-images/twitter02.png "Questa schermata è riportato un esempio di SLComposeViewController")](social-framework-images/twitter02.png#lightbox)

Per utilizzare un `SLComposeViewController` con Twitter, è necessario creare un'istanza del controller tramite la chiamata di `FromService` metodo con `SLServiceType.Twitter` come illustrato di seguito:

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

Dopo il `SLComposeViewController` istanza viene restituita, può essere utilizzato per presentare un'interfaccia utente per inviare a Twitter. Tuttavia, la prima cosa da fare è verificare la disponibilità di social network, Twitter in questo caso, chiamando `IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` non invia mai un tweet direttamente senza interazione dell'utente. Tuttavia, possono essere inizializzato con i metodi seguenti:

-   `SetInitialText` : Aggiunge il testo da visualizzare nel tweet iniziale. 
-  `AddUrl` : Aggiunge un Url per il tweet.
-  `AddImage` : Aggiunge un'immagine per il tweet.


Dopo l'inizializzazione, la chiamata `PresentVIewController` creato per la visualizzazione di `SLComposeViewController`. L'utente può quindi, facoltativamente, modificare e inviare il tweet oppure scegliere Annulla inviarlo. In entrambi i casi, il controller deve essere chiusa nel `CompletionHandler`, in cui è possibile anche controllare il risultato per vedere se il tweet è stato inviato o annullato, come illustrato di seguito:

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>Esempio TWEET

Il codice seguente viene illustrato l'utilizzo di `SLComposeViewController` per presentare una vista utilizzata per inviare un tweet:

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

### <a name="calling-twitter-api"></a>Chiamata dell'API di Twitter

Il Framework di social networking include inoltre il supporto per la creazione di richieste HTTP a social network. Incapsula la richiesta in un `SLRequest` classe che viene utilizzato per API della particolare social network di destinazione.

Ad esempio, il codice seguente effettua una richiesta a Twitter per ottenere la sequenza temporale pubblica (espandendo il codice sopra riportato):

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

Si esaminerà questo codice in modo dettagliato. In primo luogo, accede all'archivio di Account e ottiene il tipo di un account Twitter:

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

Successivamente, viene chiesto all'utente se l'app può avere accesso al proprio account Twitter e, se l'accesso viene concesso, l'account viene caricato in memoria e l'interfaccia utente aggiornata:

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

Quando l'utente richiede i dati della sequenza temporale (toccando un pulsante nell'interfaccia utente), l'app form prima di tutto una richiesta per accedere ai dati da Twitter:

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```
In questo esempio è limitare i risultati restituiti per le ultime dieci voci includendo `?count=10` nell'URL. Infine, associa la richiesta per l'account Twitter (che è stato caricato in precedenza) ed esegue la chiamata di Twitter per recuperare i dati:

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

Se i dati è stati caricati correttamente, verranno visualizzati i dati JSON non elaborati (come nell'output di esempio seguente):

[![](social-framework-images/twitter03.png "Un esempio di visualizzazione dei dati non elaborato di JSON")](social-framework-images/twitter03.png#lightbox)

In un'applicazione reale, i risultati JSON può quindi essere analizzati come normale e i risultati presentati all'utente. Vedere [servizi Web di introduzione](~/cross-platform/data-cloud/web-services/index.md) per informazioni su come analizzare JSON.

## <a name="connecting-to-facebook"></a>La connessione a Facebook

### <a name="facebook-account-settings"></a>Impostazioni dell'Account di Facebook

La connessione a Facebook con il Framework di social networking è quasi identica al processo utilizzato per Twitter illustrato in precedenza. Come illustrato di seguito, è necessario configurare un account utente Facebook nelle impostazioni del dispositivo:

[![](social-framework-images/facebook01.png "Impostazioni dell'Account di Facebook")](social-framework-images/facebook01.png#lightbox)

Una volta configurato, qualsiasi applicazione nel dispositivo che usa il Framework di social networking utilizzerà questo account per connettersi a Facebook.

### <a name="posting-to-facebook"></a>Registrazione di Facebook

Il Framework di social networking è un'API unificata progettata per accedere a più di social network, il codice rimane quasi identico, indipendentemente dal fatto di social network in uso.

Ad esempio, il `SLComposeViewController` può essere utilizzato esattamente come nell'esempio di Twitter illustrato in precedenza, i diversi solo passa alle opzioni e impostazioni specifiche di Facebook. Ad esempio:

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

Se usato con Facebook, il `SLComposeViewController` consente di visualizzare una vista che è quasi identico all'esempio Twitter, che mostra **Facebook** come il titolo in questo caso:

[![](social-framework-images/facebook02.png "La visualizzazione SLComposeViewController")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>Chiamata API Graph di Facebook

Simile a Twitter, il Framework di social networking dell'esempio `SLRequest` oggetto può essere utilizzato con l'API graph di Facebook. Ad esempio, il codice seguente restituisce informazioni dall'API graph sull'account di Xamarin (espandendo il codice sopra riportato):

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

L'unica vera differenza tra questo codice e la versione di Twitter presentato in precedenza, è il requisito di Facebook per ottenere un sviluppatore/specifici ID App (che è possibile generare dal portale per sviluppatori di Facebook) che deve essere impostato come opzione, quando si effettua la richiesta:

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

Impossibile impostare questa opzione (o utilizzando una chiave non valida) comporterà un errore o non la restituzione dei dati.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare il Framework di social networking di interagire con Twitter e Facebook. È stato in cui configurare gli account per ogni rete social nelle impostazioni del dispositivo. È stato inoltre illustrato come utilizzare il `SLComposeViewController` per presentare una visualizzazione unificata per la registrazione di social network. Inoltre, esaminato il `SLRequest` classe che viene utilizzata per chiamare l'API della rete ogni sociali.


## <a name="related-links"></a>Collegamenti correlati

- [SocialFrameworkDemo (sample)](https://developer.xamarin.com/samples/SocialFrameworkDemo/)
- [Introduzione ai servizi Web](~/cross-platform/data-cloud/web-services/index.md)
