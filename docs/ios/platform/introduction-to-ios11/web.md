---
title: Modifiche a WebKit e Safari in iOS 11
description: Questo documento illustra le modifiche apportate a WebKit e al Framework dei Servizi Safari in iOS 11. Viene descritto come usare gli aggiornamenti dello stile in SFSafariViewController e le nuove funzionalità di WKWebView.
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/12/2017
ms.openlocfilehash: ef9577aad756ae67ac9fed685d7e40faea33c316
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032058"
---
# <a name="webkit-and-safari-changes-in-ios-11"></a>Modifiche a WebKit e Safari in iOS 11

iOS 11 introduce una nuova versione del Web browser Safari: Safari 11,0, che include le modifiche apportate a WebKit e SafariServices. In questa guida vengono esaminate queste modifiche.

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` è stato introdotto in iOS 9 come opzione per la visualizzazione di contenuto Web o l'autenticazione degli utenti dall'app. Per ulteriori informazioni sulle funzionalità disponibili, vedere la Guida alle [visualizzazioni Web](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) .

iOS 11 ha introdotto aggiornamenti di stile per il controller di visualizzazione Safari, offrendo agli utenti un'esperienza più semplice tra un'app e il Web. Ad esempio, la rimozione della barra degli indirizzi offre ora a Safari View Controller l'aspetto di un browser in-app, anziché un piccolo browser. È anche possibile personalizzare la combinazione di colori in modo che corrisponda alla combinazione di colori dell'app, impostando le proprietà `preferredBarTintColor` e `PreferredControlTintColor`:

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

Il seguente frammento di codice esegue il rendering delle barre in viola e bianco, come visualizzato nell'immagine seguente:

![Barre SFSafariViewController sottoposte a rendering in viola e bianco](web-images/image1.png)

Il pulsante Ignora visualizzato nel controller di visualizzazione Safari può essere modificato anche impostando la proprietà `DismissButtonStyle` su `Done`, `Close`o `Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![Ignora testo pulsante modificato](web-images/image2.png)

Questo valore può essere modificato mentre viene visualizzato `SFSafariViewController`.

A seconda del contenuto visualizzato all'interno di un controller di visualizzazione Safari, potrebbe essere necessario assicurarsi che le barre dei menu non comprimere quando l'utente scorre. Questa operazione viene attivata impostando la nuova proprietà `BarCollapsedEnabled` su `false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![Compressione barra disabilitata](web-images/image3.png)

Apple ha anche apportato aggiornamenti alla privacy in Safari View Controller in iOS 11. A questo punto, l'esplorazione dei dati, ad esempio i cookie e l'archiviazione locale, esiste solo in base alle singole app, anziché in tutte le istanze del controller di visualizzazione Safari. In questo modo, l'attività di esplorazione dell'utente viene mantenuta privata nell'app.

Sono state anche aggiunte funzionalità aggiuntive, ad esempio il supporto per il trascinamento della selezione per gli URL e il supporto per `window.open()` `SFSafariViewController` in iOS 11. Altre informazioni su queste nuove funzionalità sono disponibili nella [documentazione di Apple SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor).

## <a name="webkit"></a>WebKit

`WKWebView` è stato introdotto come parte di WebKit in iOS 8 come mezzo per visualizzare il contenuto Web per l'utente. È molto più personalizzabile rispetto a `SFSafariViewController`, consentendo di creare un'interfaccia utente e di navigazione personalizzata.

Apple ha introdotto tre miglioramenti principali per `WKWebView` con iOS 11: 

- Possibilità di gestire i cookie
- Filtro del contenuto
- Caricamento di risorse personalizzato. 

La gestione dei cookie viene eseguita tramite la nuova classe [`WKHttpCookieStore`](https://developer.apple.com/documentation/webkit/wkhttpcookiestore) , che consente di aggiungere ed eliminare cookie, ottenere tutti i cookie archiviati in un WKWebView e osservare l'archivio dei cookie per le modifiche.

Il filtro del contenuto consente di gestire il tipo di contenuto che verrà visualizzato dall'utente, consentendo di assicurarsi che sia protetto, familiare e, se necessario, disponibile solo per un gruppo selezionato di utenti. Viene implementato tramite la nuova classe [`WKContentRuleList`](https://developer.apple.com/documentation/webkit/wkcontentrulelist) , fornendo coppie di trigger e azioni in JSON. Altre informazioni su questi trigger e azioni sono disponibili nella Guida [alle regole di blocco del contenuto](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html) di Apple.

iOS 11 consente ora di personalizzare `WKWebView` con il caricamento di risorse personalizzate per il contenuto Web. Questa operazione viene implementata tramite l'interfaccia `IWKUrlSchemeHandler`, che consente di gestire gli schemi URL che non sono nativi del Web Kit. Questa interfaccia dispone di un metodo di avvio e di arresto che deve essere implementato:

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

Una volta implementato il gestore, usarlo per impostare la proprietà `SetUrlSchemeHandler` nel `WKWebViewConfiguration`. Quindi, caricare l'URL di un elemento che usa lo schema personalizzato:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```
