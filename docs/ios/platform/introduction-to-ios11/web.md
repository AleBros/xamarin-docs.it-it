---
title: Modifiche di Web in iOS 11
description: Questo documento vengono illustrate le modifiche apportate a WebKit e framework di servizi di Safari in iOS 11. Viene descritto come lavorare con gli aggiornamenti in SFSafariViewController e sulle nuove funzionalità in WKWebView di stile.
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/12/2016
ms.openlocfilehash: f5876a9d201950ebac45e8b1f786b0e97452a7f1
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787448"
---
# <a name="web-changes-in-ios-11"></a>Modifiche di Web in iOS 11

iOS 11 introduce una nuova versione del browser web Safari – Safari 11.0 – che include le modifiche al WebKit e SafariServices. Questa guida illustra queste modifiche.

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` è stata introdotta in iOS 9 come un'opzione per visualizzare il contenuto web o l'autenticazione degli utenti dall'app. Ulteriori informazioni sulle relative funzionalità, vedere il [visualizzazioni Web](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) Guida.

iOS 11 ha introdotto gli aggiornamenti di stile al controllo di visualizzazione Safari garantisce agli utenti un'esperienza più semplice tra un'applicazione e web. Ad esempio, la rimozione dell'indirizzo barra offre ora il Controller di visualizzazione l'aspetto di un browser all'interno dell'applicazione, anziché un mini-browser Safari. È inoltre possibile personalizzare la combinazione di colori per compatibilità con la combinazione di colori della propria applicazione impostando il `preferredBarTintColor` e `PreferredControlTintColor` proprietà:

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

Frammento di codice seguente viene eseguito il rendering le barre in viola e vuoti, come visualizzato nell'immagine seguente:

![Barre di SFSafariViewController visualizzate in viola e vuoti](web-images/image1.png)

Pulsante Elimina presentato nel Controller di visualizzazione Safari può anche essere modificato impostando la `DismissButtonStyle` una proprietà `Done`, `Close`, o `Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![Ignorare il testo del pulsante modificato](web-images/image2.png)

Questo valore può essere modificato mentre `SFSafariViewController` viene presentato.


A seconda del contenuto che viene visualizzato all'interno di un Controller di visualizzazione Safari, potrebbe essere necessario garantire che le barre dei menu non comprimere come l'utente scorre. Questa opzione è abilitata impostando la nuova `BarCollapsedEnabled` proprietà `false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![Barra di compressione disabilitato](web-images/image3.png)

Apple ha anche gli aggiornamenti alla privacy nel Controller di visualizzazione Safari in iOS 11. A questo punto, l'esplorazione di dati, ad esempio i cookie e l'archiviazione locale esiste solo in una base per app, piuttosto che in tutte le istanze del controller di visualizzazione Safari. In questo modo le attività di esplorazione utente privato all'interno dell'app.

Funzionalità aggiuntive, ad esempio trascinare e rilasciare il supporto per gli URL e supporto per `window.open()` sono inoltre stati aggiunti a `SFSafariViewController` in iOS 11. È possibile trovare ulteriori informazioni su queste nuove funzionalità in [documentazione di Apple SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor).


## <a name="webkit"></a>WebKit

`WKWebView` è stato introdotto come parte di WebKit in iOS 8 come mezzo per la visualizzazione di contenuto web per l'utente. È molto più personalizzabile di `SFSafariViewController`, consentendo di creare la propria interfaccia utente e di esplorazione.

Apple ha introdotto tre principali miglioramenti per `WKWebView` con iOS 11: 

- La possibilità di gestire i cookie
- Filtraggio dei contenuti
- Caricamento delle risorse personalizzati. 

La gestione dei cookie viene eseguita tramite il nuovo [ `WKHttpCookieStore` ](https://developer.apple.com/documentation/webkit/wkhttpcookiestore) (classe), che consente di aggiungere ed eliminare i cookie, per ottenere tutti i cookie archiviati in un WKWebView e di osservare il cookie di archiviare le modifiche.

Contenuto di filtro consente di gestire il tipo di contenuto che verrà visualizzato l'utente, che consente di assicurarsi che sia adatto protetta, famiglia e, se necessario, è disponibile solo a un gruppo di utenti. Questa funzione è implementata tramite il nuovo [ `WKContentRuleList` ](https://developer.apple.com/documentation/webkit/wkcontentrulelist) (classe), fornendo coppie di trigger e le azioni in JSON. Ulteriori informazioni su queste azioni e il trigger sono reperibile in Apple [regole di blocco contenuto](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html) Guida.

iOS 11 ora consente di personalizzare `WKWebView` personalizzata il caricamento delle risorse per il contenuto web. Questa funzione è implementata tramite il `IWKUrlSchemeHandler` interfaccia, che consente di gestire gli schemi URL che non sono di tipo nativo al Kit di Web. Questa interfaccia dispone di un metodo di avvio e arresto che deve essere implementato:

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

Una volta che il gestore è stato implementato, utilizzarla per impostare il `SetUrlSchemeHandler` proprietà il `WKWebViewConfiguration`. Quindi, caricare l'URL di un elemento che utilizza lo schema personalizzato:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

