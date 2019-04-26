---
title: Modifiche per il Web in iOS 11
description: Questo documento vengono illustrate le modifiche apportate a WebKit e il framework di servizi di Safari in iOS 11. Viene descritto come lavorare con lo stile degli aggiornamenti in SFSafariViewController e nuove funzionalità in WKWebView.
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/12/2017
ms.openlocfilehash: ba691a6605dcf7e86a76ed13d4c8ef5f0984ff6e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399739"
---
# <a name="web-changes-in-ios-11"></a>Modifiche per il Web in iOS 11

iOS 11 introduce una nuova versione del browser web Safari – Safari 11.0 – che include le modifiche ai SafariServices e WebKit. Questa guida illustra queste modifiche.

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` è stato introdotto in iOS 9 come opzione per visualizzare il contenuto web o l'autenticazione degli utenti dall'app. Altre informazioni sulle relative funzionalità sono reperibili nel [visualizzazioni Web](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) Guida.

iOS 11 ha introdotto gli aggiornamenti di stile per il Controller di visualizzazione Safari, offrendo agli utenti un'esperienza più semplice tra un'app e web. Ad esempio, la rimozione dell'indirizzo barra offre ora il Controller di visualizzazione l'aspetto di un browser in-app, anziché un mini-browser Safari. È anche possibile personalizzare la combinazione di colori a si integrano con la combinazione di colori dell'app impostando il `preferredBarTintColor` e `PreferredControlTintColor` proprietà:

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

Il frammento di codice seguente esegue il rendering le barre in viola e il bianco, come visualizzato nell'immagine seguente:

![Barre SFSafariViewController sottoposto a rendering in viola e vuoti](web-images/image1.png)

Pulsante Elimina presentato nel Controller di visualizzazione Safari può anche essere modificato impostando il `DismissButtonStyle` proprietà a una delle due `Done`, `Close`, o `Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![Ignora il testo del pulsante modificato](web-images/image2.png)

Questo valore può essere modificato mentre `SFSafariViewController` viene presentato.


A seconda del contenuto che viene visualizzato all'interno di un Controller di visualizzazione Safari, potrebbe essere necessario per assicurare che le barre dei menu non comprimere come l'utente scorre. Ciò viene abilitato impostando la nuova `BarCollapsedEnabled` proprietà `false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![Barra di compressione disabilitata](web-images/image3.png)

Apple ha anche reso gli aggiornamenti per la privacy nel Controller di visualizzazione Safari in iOS 11. A questo punto, i dati di esplorazione, ad esempio cookie e l'archiviazione locale sono disponibili solo in base a una per ogni app, invece che in tutte le istanze del controller di visualizzazione di Safari. In questo modo le attività di esplorazione utente privato all'interno dell'app.

Funzionalità aggiuntive, ad esempio trascinare e rilasciare il supporto per gli URL e supporto per `window.open()` sono stati aggiunti anche alla `SFSafariViewController` in iOS 11. È possibile trovare altre informazioni su queste nuove funzionalità nella [documentazione di Apple SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor).


## <a name="webkit"></a>WebKit

`WKWebView` è stata introdotta come parte di WebKit in iOS 8 come mezzo di visualizzazione del contenuto web a un utente. È molto più opzioni di personalizzazione rispetto `SFSafariViewController`, permettendo di creare il proprio navigazione e l'interfaccia utente.

Apple ha introdotto tre miglioramenti principali per `WKWebView` con iOS 11: 

- La possibilità di gestire i cookie
- Filtri dei contenuti
- Caricamento delle risorse personalizzati. 

Gestione dei cookie viene eseguita tramite il nuovo [ `WKHttpCookieStore` ](https://developer.apple.com/documentation/webkit/wkhttpcookiestore) (classe), che consente di aggiungere ed eliminare i cookie, per ottenere tutti i cookie archiviati in un WKWebView e osservare il cookie di archiviare le modifiche.

Contenuto il filtro consente di gestire il tipo di contenuto che viene visualizzato l'utente, consentendo di assicurarsi che sia descrittivo sicura e della famiglia e, se necessario, disponibile solo per un gruppo selezionato di utenti. Ciò viene implementato tramite la nuova [ `WKContentRuleList` ](https://developer.apple.com/documentation/webkit/wkcontentrulelist) (classe), fornendo coppie di trigger e azioni in formato JSON. Altre informazioni su questi trigger e azioni sono reperibili in Apple [regole di blocco contenuto](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html) Guida.

iOS 11 ora consente di personalizzare `WKWebView` personalizzata il caricamento delle risorse per il contenuto web. Ciò viene implementato tramite la `IWKUrlSchemeHandler` interfaccia, che ti permette di gestire gli schemi URL che non sono nativo al Kit di Web. Questa interfaccia dispone di un metodo di avvio e arresto che deve essere implementato:

```csharp
public class MyHandler : NSObject, IWKUrlSchemeHandler {

    [Export("webView:startURLSchemeTask:")]
    public void StartUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        
        // Implement a IWKUrlSchemeTask here
        var response = new NSUrlResponse(urlSchemeTask.Request.Url, "text/html", ContentLength, null);
        urlSchemeTask.DidReceiveResponse(response);
        urlSchemeTask.DidReceiveData(someData);
        urlSchemeTask.DidFinish();
    }

    [Export("webView:stopURLSchemeTask:")]
    public void StopUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        throw new NotImplementedException();
    }

}
``` 

Dopo il gestore di è stato implementato, utilizzarla per impostare il `SetUrlSchemeHandler` proprietà di `WKWebViewConfiguration`. Quindi, caricare l'URL di un elemento che utilizza lo schema personalizzato:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

