---
title: Visualizzazioni Web in Novell. iOS
description: Questo documento descrive i vari modi in cui un'app Novell. iOS può visualizzare il contenuto Web. Vengono illustrati WKWebView, SFSafariViewController, Safari e la sicurezza del trasporto app.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: da477ca647de765d0173b223e2df2fe04959221b
ms.sourcegitcommit: bad1ab3f78d7f94d48511666626b54f8ba155689
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663541"
---
# <a name="web-views-in-xamarinios"></a>Visualizzazioni Web in Novell. iOS

Nel corso della durata di iOS Apple ha rilasciato una serie di modi per gli sviluppatori di app di incorporare funzionalità di visualizzazione Web nelle app. La maggior parte degli utenti usa il Web browser Safari incorporato sul dispositivo iOS e quindi prevede che le funzionalità di visualizzazione Web di altre app siano coerenti con questa esperienza. Si aspettano che gli stessi movimenti funzionino, le prestazioni siano uguali e la funzionalità stessa.

iOS 11 ha introdotto nuove modifiche per `WKWebView` e `SFSafariViewController`. Per ulteriori informazioni, vedere la Guida alle [modifiche Web in iOS 11](~/ios/platform/introduction-to-ios11/web.md) .

## <a name="wkwebview"></a>WKWebView

`WKWebView` è stato introdotto in iOS 8 che consente agli sviluppatori di app di implementare un'interfaccia di esplorazione Web simile a quella di Mobile Safari. Ciò è dovuto in parte al fatto che `WKWebView` usa il motore di Nitro JavaScript, lo stesso motore usato da Mobile Safari. è possibile utilizzare sempre `WKWebView` su UIWebView grazie alle [prestazioni migliorate](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview), ai movimenti intuitivi e alla facilità di interazione tra la pagina Web e l'app.
  
`WKWebView` possono essere aggiunti all'app in modo quasi identico a UIWebView. Tuttavia, lo sviluppatore dispone di un maggiore controllo sull'interfaccia utente/UX e sulle funzionalità. Con la creazione e la visualizzazione dell'oggetto visualizzazione Web verrà visualizzata la pagina richiesta, tuttavia è possibile controllare la modalità di presentazione della visualizzazione, il modo in cui l'utente può spostarsi e il modo in cui l'utente esce dalla visualizzazione.  

Il codice seguente può essere usato per avviare un `WKWebView` nell'app Novell. iOS:

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

È importante notare che `WKWebView` si trova nello spazio dei nomi `WebKit`, pertanto sarà necessario aggiungere questa direttiva using all'inizio della classe.

`WKWebView` può essere usato anche nelle app Novell. Mac ed è consigliabile usarlo per creare un'app Mac/iOS multipiattaforma.

La ricetta relativa alla [gestione degli avvisi di JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) fornisce anche informazioni sull'uso di WKWebView con JavaScript.

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController` è il modo più recente per fornire contenuto Web dall'app ed è disponibile in iOS 9 e versioni successive. Diversamente da `UIWebView` o `WKWebView`, `SFSafariViewController` è un controller di visualizzazione e pertanto non può essere usato con altre visualizzazioni. È necessario presentare `SFSafariViewController` come un nuovo controller di visualizzazione, nello stesso modo in cui si presenta qualsiasi controller di visualizzazione.

 `SFSafariViewController` è essenzialmente un'mini safari ' che può essere incorporato nell'app. Analogamente a WKWebView, USA lo stesso motore di Nitro JavaScript, ma offre anche una gamma di funzionalità Safari aggiuntive, come il riempimento automatico, il lettore e la possibilità di condividere cookie e dati con Safari per dispositivi mobili. L'interazione tra l'utente e il `SFSafariViewController` non è accessibile all'app. L'app non avrà accesso a nessuna delle funzionalità predefinite di Safari.

Inoltre, per impostazione predefinita, implementa un pulsante **Fatto**, che consente all'utente di tornare facilmente alla tua app e di inoltrare e tornare indietro pulsanti di navigazione, consentendo all'utente di navigare attraverso una pila di pagine web. Inoltre, fornisce all'utente una barra degli indirizzi, per consentirgli di ricordare che si trovano nella pagina Web prevista. La barra degli indirizzi non consente all'utente di modificare l'URL. 

Queste implementazioni non possono essere modificate, quindi `SFSafariViewController` è ideale da usare come browser predefinito se l'app vuole presentare una pagina Web senza alcuna personalizzazione.

Il codice seguente può essere usato per avviare un `SFSafariViewController` nell'app Novell. iOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Viene prodotta la seguente visualizzazione Web:

[![una visualizzazione Web di esempio con SFSafariViewController](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

È anche possibile aprire l'app Mobile Safari dall'interno dell'app, usando il codice seguente:

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

Viene prodotta la seguente visualizzazione Web:

[![una pagina Web visualizzata in Safari](webview-images/safari.png)](webview-images/safari.png#lightbox)

È in genere consigliabile evitare di esplorare gli utenti dall'app a Safari. La maggior parte degli utenti non prevede la navigazione all'esterno dell'applicazione, pertanto se ci si allontana dall'app, gli utenti potrebbero non restituirla, in sostanza uccidendo Engagement.

i miglioramenti di iOS 9 consentono all'utente di tornare facilmente all'app tramite un pulsante indietro disponibile nell'angolo superiore sinistro della pagina di Safari.

## <a name="app-transport-security"></a>ATS (App Transport Security)

La sicurezza del trasporto delle app o *ATS* è stata introdotta da Apple in iOS 9 per garantire che tutte le comunicazioni Internet siano conformi alle procedure consigliate per la connessione sicura.

Per altre informazioni su ATS, incluso come implementarlo nell'app, vedere la guida alla [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md) .

## <a name="uiwebview-deprecated"></a>UIWebView (deprecato)

> [!IMPORTANT]
> La [documentazione di Apple `UIWebView`](https://developer.apple.com/documentation/uikit/uiwebview) indica che il controllo è deprecato. Tutte le app devono usare invece [`WKWebKit`](#wkwebview) .

`UIWebView` è il modo legacy di Apple di fornire contenuto Web nell'app. È stata rilasciata in iOS 2,0 ed è stata deprecata a partire da 8,0.

Se si prevede di supportare le versioni di iOS precedenti alla 8,0, sarà necessario usare `UIWebView`. Dato che `UIWebView` è meno ottimizzato per le prestazioni rispetto alle alternative, è consigliabile controllare la versione di iOS dell'utente. Se 8,0 o versione successiva, l'utilizzo di una delle opzioni descritte di seguito creerà un'esperienza utente migliore.

Per aggiungere un UIWebView all'app Novell. iOS, usare il codice seguente:

```csharp
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://docs.microsoft.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

Viene prodotta la seguente visualizzazione Web:

[![l'effetto di ScalesPagesToFit](webview-images/webview.png)](webview-images/webview.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [Webviews (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
