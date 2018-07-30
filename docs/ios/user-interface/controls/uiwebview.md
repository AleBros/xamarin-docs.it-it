---
title: Visualizzazioni Web in xamarin. IOS
description: Questo documento descrive le varie modalità di che un'app xamarin. IOS possa visualizzare il contenuto web. Viene descritto UIWebView WKWebView, SFSafariViewController, Safari e sicurezza del trasporto di app.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 56b0f0e910cc95ca50d1e5e460ce71a1c8f669a2
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242040"
---
# <a name="web-views-in-xamarinios"></a>Visualizzazioni Web in xamarin. IOS

Nel corso della durata di iOS Apple ha rilasciato diversi modi per gli sviluppatori di incorporare funzionalità di visualizzazione web nelle proprie app. La maggior parte degli utenti utilizzano il browser web Safari predefinito sul proprio dispositivo iOS e pertanto prevedono che la funzionalità di visualizzazione web da altre App sia coerenza con questa esperienza. Si aspettano i movimenti stesso al lavoro, le prestazioni corrispondere a par e la funzionalità.

In questo articolo, verranno analizzati ognuna delle tre visualizzazioni Web fornite da Apple: `UIWebView`, `WKWebview`, e `SFSafariViewController`, le analogie e differenze e come possono essere usate. 

iOS 11 introdotte nuove modifiche apportate a entrambe `WKWebView` e `SFSafariViewController`. Per altre informazioni, vedere la [Web le modifiche in iOS 11 Guida](~/ios/platform/introduction-to-ios11/web.md) Guida.

## <a name="uiwebview"></a>UIWebView

`UIWebView` viene modalità legacy di Apple di fornire contenuti web nell'app. È stata rilasciata in iOS 2.0 ed è stata deprecata a partire da 8.0.

Se si prevede di supportare le versioni di iOS precedente alla versione 8.0, si dovrà usare `UIWebView`. Dovuto al fatto che `UIWebView` è meno ottimizzata per le prestazioni rispetto alle alternative, è consigliabile, è necessario controllare la versione di iOS dell'utente. Se è 8.0 o versione successiva, tramite una delle opzioni indicato di seguito creerà una migliore esperienza utente.
 
Per aggiungere un UIWebView all'App xamarin. IOS, usare il codice seguente:
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

Questo codice produce la visualizzazione web seguente:

[![](uiwebview-images/webview.png "L'effetto di ScalesPagesToFit")](uiwebview-images/webview.png#lightbox)

Per altre informazioni sull'uso di `UIWebView`, fare riferimento per le soluzioni seguenti:


- [Caricare una pagina Web](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_a_web_page)
- [Caricare il contenuto locale](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_local_content)
- [Caricare i documenti Non Web](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_non-web_documents)

## <a name="wkwebview"></a>WKWebView

`WKWebView` è stato introdotto in iOS 8 che consente agli sviluppatori di app per implementare un'interfaccia simile a quella di Safari per dispositivi mobili di esplorazione web. Il problema si verifica, in parte al fatto che `WKWebView` utilizza il motore Nitro Javascript, lo stesso motore usato da Safari per dispositivi mobili. `WKWebView` deve essere sempre utilizzata over UIWebView erano possibili a causa dell'errore il [prestazioni migliorate](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview), incorporato di movimenti semplici da usare e la facilità di interazione tra la pagina web e l'app.
  
`WKWebView` può essere aggiunto all'app in modo quasi identico a UIWebView, tuttavia, lo sviluppatore avere un maggiore controllo riguardo l'interfaccia utente ed esperienza utente e la funzionalità. Creazione e la visualizzazione dell'oggetto di visualizzazione web visualizzerà la pagina richiesta, tuttavia, è possibile controllare come viene presentata la visualizzazione, come l'utente possa passare e come l'utente esce dalla visualizzazione.  

Il codice riportato di seguito può essere utilizzato per avviare un `WKWebView` nell'app xamarin. ios:

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

Questo codice produce la visualizzazione web seguente:

[![](uiwebview-images/wkwebview.png "Una visualizzazione web di esempio senza ScalesPagesToFit")](uiwebview-images/wkwebview.png#lightbox)

È importante notare che `WKWebView` è nello spazio dei nomi WebKit, pertanto è necessario aggiungere questa direttiva all'inizio della classe using.

`WKWebView` può anche essere usato all'interno delle App xamarin. Mac ed è pertanto possibile è consigliabile usarlo se si sta creando un'app Mac/iOS multipiattaforma.

Il [gestire gli avvisi di JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) recipe fornisce anche informazioni sull'uso WKWebView con Javascript

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController` è il modo più recente di fornire contenuto web dall'app ed è disponibile in iOS 9 e versioni successive. A differenza `UIWebView` oppure `WKWebView`, `SFSafariViewController` è un Controller di visualizzazione e pertanto non può essere usato con altre viste. È consigliabile presentare `SFSafariViewController` come nuovo Controller di visualizzazione, nello stesso modo si presenterebbe alcun Controller di visualizzazione.
 
 `SFSafariViewController` è essenzialmente un 'mini safari' che può essere incorporato nell'app. Ad esempio WKWebView Usa lo stesso motore Javascript Nitro, ma fornisce anche una gamma di funzionalità aggiuntive di Safari, ad esempio riempimento automatico, lettore e la possibilità di condividere i cookie e dati con Safari per dispositivi mobili. Interazione tra l'utente e la `SFSafariViewController` non è possibile accedere all'app. L'app non sarà possibile accedere a tutte le funzionalità di Safari predefinito.
 
Inoltre, per impostazione predefinita, implementa un pulsante **Fatto**, che consente all'utente di tornare facilmente alla tua app e di inoltrare e tornare indietro pulsanti di navigazione, consentendo all'utente di navigare attraverso una pila di pagine web. Inoltre, fornisce inoltre l'utente con un indirizzo barra fornendo loro la massima tranquillità che sono nella pagina web previsto. Barra degli indirizzi non consente all'utente di modificare l'url. 

Queste implementazioni non può essere modificato, pertanto `SFSafariViewController` ideale da utilizzare come browser predefinito, se l'app intende presentare una pagina Web senza eseguire alcuna personalizzazione.

Il codice riportato di seguito può essere utilizzato per avviare un `SFSafariViewController` nell'app xamarin. ios:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Questo codice produce la visualizzazione web seguente:

[![](uiwebview-images/sfsafariviewcontroller.png "Una visualizzazione web di esempio con SFSafariViewController")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

È anche possibile aprire l'app Safari per dispositivi mobili dall'interno dell'app, usando il codice seguente:

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

Questo codice produce la visualizzazione web seguente:

[![](uiwebview-images/safari.png "Una pagina web visualizzata in Safari")](uiwebview-images/safari.png#lightbox)

Lo spostamento di utenti da app a Safari generalmente deve sempre essere evitato. La maggior parte degli utenti non previsti spostamento all'esterno dell'applicazione, pertanto se ci si sposta dall'app, gli utenti non possono mai restituire, essenzialmente la terminazione engagement.

miglioramenti di iOS 9 consentono all'utente di tornare facilmente all'App tramite un pulsante Indietro che viene fornito nell'angolo superiore sinistro della pagina di Safari.

## <a name="app-transport-security"></a>Sicurezza del trasporto di App

Sicurezza del trasporto App, oppure *ATS* è stato introdotto in iOS 9 per verificare che tutte le comunicazioni internet siano conformi per le procedure consigliate di connessione sicura da Apple.

Per altre informazioni su ATS, incluse come implementarla nell'app, vedere la [App Transport Security](~/ios/app-fundamentals/ats.md) Guida.

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazioni Web (esempio)](https://developer.xamarin.com/samples/monotouch/WebView/)
