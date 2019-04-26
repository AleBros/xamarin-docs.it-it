---
title: Framework basati su social network in xamarin. IOS
description: Il Framework basati su social network fornisce un'API unificata per l'interazione con i social network, tra cui Facebook e Twitter, nonché SinaWeibo per gli utenti in Cina.
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 08ccd5b5ac78e82bf745764d70e59d2db9ec6776
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61185051"
---
# <a name="social-framework-in-xamarinios"></a>Framework basati su social network in xamarin. IOS

_Il Framework basati su social network fornisce un'API unificata per l'interazione con i social network, tra cui Facebook e Twitter, nonché SinaWeibo per gli utenti in Cina._

Usando il Framework basati su social network consente alle applicazioni di interagire con i social network da una singola API senza dover gestire l'autenticazione. Include un sistema fornito controller di visualizzazione per la composizione di post, nonché un'astrazione che consente l'utilizzo di API di ogni rete basati su social network su HTTP.

> [!IMPORTANT]
> Per un'API multipiattaforma per connettersi a diversi social network, vedere la [Xamarin.Social](http://components.xamarin.com/view/xamarin.social/) componente in Store il componente Xamarin.

## <a name="connecting-to-twitter"></a>La connessione a Twitter

### <a name="twitter-account-settings"></a>Impostazioni dell'Account Twitter

Per connettersi a Twitter usando il Framework basati su social network, un account può essere configurato nelle impostazioni del dispositivo come illustrato di seguito:

 [![](social-framework-images/twitter01.png "Impostazioni dell'Account Twitter")](social-framework-images/twitter01.png#lightbox)

Dopo aver immesso un account ed verificato con Twitter, qualsiasi applicazione nel dispositivo che usa le classi di Framework basati su social network per accedere a Twitter utilizzerà questo account.

### <a name="sending-tweets"></a>L'invio di tweet

Il Framework basati su social network include un controller denominato `SLComposeViewController` che presenta una vista di sistema fornita per la modifica e inviare un tweet. Lo screenshot seguente mostra un esempio di questa vista:

 [![](social-framework-images/twitter02.png "Questo screenshot Mostra un esempio del SLComposeViewController")](social-framework-images/twitter02.png#lightbox)

Usare un `SLComposeViewController` con Twitter, è necessario creare un'istanza del controller tramite la chiamata di `FromService` metodo con `SLServiceType.Twitter` come illustrato di seguito:

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

Dopo il `SLComposeViewController` istanza viene restituita, può essere utilizzato per presentare un'interfaccia utente di post su Twitter. Tuttavia, la prima cosa da fare è controllare la disponibilità della rete basati su social network, Twitter in questo caso, tramite la chiamata `IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` mai invia un tweet direttamente senza interazione dell'utente. Tuttavia, possono essere inizializzato con i metodi seguenti:

-   `SetInitialText` : Aggiunge il testo iniziale da visualizzare nel tweet. 
-  `AddUrl` : Aggiunge un Url per il tweet.
-  `AddImage` : Aggiunge un'immagine per il tweet.


Una volta inizializzato, la chiamata `PresentVIewController` consente di visualizzare la vista creata dal `SLComposeViewController`. L'utente può quindi, facoltativamente, modificare e inviare tweet o annullare l'invio. In entrambi i casi, il controller deve essere chiusa nel `CompletionHandler`, in cui il risultato può anche essere controllato per verificare se il tweet è stato inviato o annullato, come illustrato di seguito:

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>Esempio di tweet

Il codice seguente illustra l'uso di `SLComposeViewController` per offrire una visualizzazione consente di inviare un tweet:

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

### <a name="calling-twitter-api"></a>Chiamata API di Twitter

Il Framework basati su social network include anche il supporto per la creazione di richieste HTTP ai social network. Incapsula la richiesta in un `SLRequest` classe utilizzato per API della particolare social network di destinazione.

Ad esempio, il codice seguente esegue una richiesta a Twitter per ottenere la sequenza temporale pubblica (espandendo il codice sopra riportato):

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

Esaminiamo questo codice in modo dettagliato. In primo luogo, ottiene l'accesso a Store l'Account e ottiene il tipo di un account Twitter:

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

Successivamente, chiede all'utente se l'app può avere accesso all'account Twitter e, se l'accesso viene concesso l'account viene caricato in memoria e l'interfaccia utente aggiornata:

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

Quando l'utente richiede i dati della sequenza temporale (toccando un pulsante nell'interfaccia utente), l'app forms prima di tutto una richiesta per accedere ai dati da Twitter:

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```
In questo esempio limita i risultati restituiti per le ultime dieci voci includendo `?count=10` nell'URL. Infine, associa la richiesta per l'account Twitter (che è stato caricato in precedenza) ed esegue la chiamata a Twitter per recuperare i dati:

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

Se i dati sono stati caricati correttamente, verranno visualizzati i dati JSON non elaborati (ad esempio l'output di esempio riportato di seguito):

[![](social-framework-images/twitter03.png "Un esempio di visualizzazione dei dati JSON non elaborato")](social-framework-images/twitter03.png#lightbox)

In un'app reale, i risultati JSON è stato possibile analizzare quindi come normale e i risultati presentati all'utente. Visualizzare [servizi Web di introduzione](~/cross-platform/data-cloud/web-services/index.md) per informazioni su come analizzare JSON.

## <a name="connecting-to-facebook"></a>La connessione a Facebook

### <a name="facebook-account-settings"></a>Impostazioni dell'Account di Facebook

La connessione a Facebook con il Framework basati su social network è quasi identica al processo usato per Twitter illustrato in precedenza. Un account utente Facebook deve essere configurato nelle impostazioni del dispositivo come illustrato di seguito:

[![](social-framework-images/facebook01.png "Impostazioni dell'Account di Facebook")](social-framework-images/facebook01.png#lightbox)

Una volta configurato, qualsiasi applicazione nel dispositivo che usa il Framework basati su social network utilizzerà questo account per connettersi a Facebook.

### <a name="posting-to-facebook"></a>La registrazione a Facebook

Poiché il Framework basati su social network è un'API unificata progettata per accedere a più reti basati su social network, il codice rimane pressoché identico, indipendentemente dal social network in uso.

Ad esempio, il `SLComposeViewController` può essere utilizzato esattamente come nell'esempio di Twitter illustrato in precedenza, l'unica differenza sta passando alle opzioni e impostazioni specifiche di Facebook. Ad esempio:

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

Se usato con Facebook, il `SLComposeViewController` apre una visualizzazione che è quasi identica a quello di Twitter, che illustra **Facebook** come il titolo in questo caso:

[![](social-framework-images/facebook02.png "La visualizzazione SLComposeViewController")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>Chiamata API Graph di Facebook

Simile a Twitter, il Framework basati su social network di esempio `SLRequest` oggetto può essere utilizzato con l'API graph di Facebook. Ad esempio, il codice seguente restituisce informazioni dall'API graph relative all'account Xamarin (espandendo il codice sopra riportato):

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

L'unica vera differenza tra questo codice e la versione di Twitter presentata nell'esempio precedente, è il requisito di Facebook per ottenere un sviluppatore/App ID specifico (che è possibile generare dal portale per sviluppatori di Facebook) che deve essere impostato come opzione quando si effettua la richiesta:

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

Impossibile impostare questa opzione (o utilizzando una chiave non valida) comporterà un errore o senza dati restituiti.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare il Framework basati su social network per interagire con Twitter e Facebook. È stato illustrato come configurare gli account per ogni rete basati su social network nelle impostazioni del dispositivo. È stato inoltre illustrato come usare il `SLComposeViewController` per offrire una visualizzazione unificata per la registrazione ai social network. Inoltre, ha esaminato il `SLRequest` classe usato per chiamare l'API di ogni rete basati su social network.


## <a name="related-links"></a>Collegamenti correlati

- [SocialFrameworkDemo (esempio)](https://developer.xamarin.com/samples/SocialFrameworkDemo/)
- [Introduzione ai servizi Web](~/cross-platform/data-cloud/web-services/index.md)
