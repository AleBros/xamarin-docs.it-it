---
title: Visualizzazioni Web
description: "Per evitare ambiguità tra le opzioni di visualizzazione web iOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 70703797792a2f667a2eb20cbc45d1736e5e6b9d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="web-views"></a>Visualizzazioni Web

In base alla durata di iOS Apple ha rilasciato un numero di modi per gli sviluppatori di app incorporare funzionalità di visualizzazione web nelle proprie app. La maggior parte degli utenti utilizzano il browser web Safari predefinito sul proprio dispositivo iOS e prevedono che la funzionalità di visualizzazione web da altre App sia coerenza con questa esperienza. Prevedono che gli stessi movimenti siano funzionanti, le prestazioni per corrispondere alle par e alle funzionalità.

In questo articolo vengono analizzate ognuna delle tre visualizzazioni Web fornite da Apple: `UIWebView`, `WKWebview`, e `SFSafariViewController`, le analogie e differenze e come possono essere utilizzati. 

iOS 11 sono state introdotte nuove modifiche sia `WKWebView` e `SFSafariViewController`. Per ulteriori informazioni, vedere il [Web modifiche nella Guida di iOS 11](~/ios/platform/introduction-to-ios11/web.md) Guida.

## <a name="uiwebview"></a>UIWebView

`UIWebView` Consente di Apple legacy di fornire contenuto web nell'applicazione. È stato rilasciato in iOS 2.0 ed è deprecato a partire da 8.0.

Se si prevede di supportare le versioni di iOS precedenti a 8.0, è necessario utilizzare `UIWebView`. Dovuto al fatto che `UIWebView` è minore di ottimizzato per le prestazioni rispetto alle alternative, è consigliabile, è necessario controllare la versione di iOS dell'utente. Se è 8.0 o versione successiva, tramite una delle opzioni indicato di seguito verrà creata una migliore esperienza utente.
 
Per aggiungere un UIWebView all'App xamarin. IOS, usare il codice seguente:
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

Ciò produce la visualizzazione web seguenti:

[![](uiwebview-images/webview.png "L'effetto di ScalesPagesToFit")](uiwebview-images/webview.png#lightbox)

Per ulteriori informazioni sull'utilizzo `UIWebView`, fare riferimento per le soluzioni seguenti:


- [Caricare una pagina Web](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_a_web_page/)
- [Caricare il contenuto locale](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_local_content/)
- [Il caricamento dei documenti Non Web](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_non-web_documents/)

## <a name="wkwebview"></a>WKWebView

`WKWebView` è stata introdotta in iOS 8 sviluppatori dell'applicazione che consente di implementare un web browser interfaccia analoga a quella di Safari per dispositivi mobili. Si tratta di scadenza, in parte, per il fatto che `WKWebView` utilizza il motore Nitro Javascript, lo stesso motore usato da Safari per dispositivi mobili. `WKWebView` deve essere sempre utilizzata over UIWebView sono stati possibili a causa del [il miglioramento delle prestazioni](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview), incorporato movimenti intuitivo e la facilità di interazione tra la pagina web e app.
  
`WKWebView` può essere aggiunto all'app in modo quasi identico a UIWebView, tuttavia, lo sviluppatore si dispone di un maggiore controllo sull'interfaccia/esperienza utente e funzionalità. Creazione e la visualizzazione dell'oggetto visualizzazione web visualizzerà la pagina richiesta, ma è possibile controllare la modalità di presentazione della visualizzazione, come l'utente possa passare e come l'utente chiude la visualizzazione.  

Il codice seguente consente di avviare un `WKWebView` nell'app xamarin:

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

Ciò produce la visualizzazione web seguenti:

[![](uiwebview-images/wkwebview.png "Una visualizzazione web di esempio senza ScalesPagesToFit")](uiwebview-images/wkwebview.png#lightbox)

È importante notare che `WKWebView` è nello spazio dei nomi WebKit, pertanto è necessario aggiungere questa direttiva all'inizio della classe using.

`WKWebView` può essere utilizzato anche in App Xamarin.Mac ed è pertanto consigliabile utilizzarlo se si sta creando un'app Mac/iOS multipiattaforma.

Il [gestire avvisi JavaScript](https://developer.xamarin.com/recipes/ios/content_controls/web_view/handle_javascript_alerts/) recipe fornisce anche informazioni sull'uso di WKWebView con Javascript

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController` è il modo più recente per fornire contenuto web dall'applicazione ed è disponibile in iOS 9 e versioni successive. A differenza di `UIWebView` o `WKWebView`, `SFSafariViewController` è un Controller di visualizzazione e non può essere utilizzato con altre viste. È necessario presentare `SFSafariViewController` come nuovo Controller di visualizzazione, nello stesso modo è presente alcun Controller di visualizzazione.
 
 `SFSafariViewController` è essenzialmente un 'mini safari' che può essere incorporato nell'app. Ad esempio WKWebView utilizza lo stesso motore Javascript Nitro, ma fornisce anche una gamma di funzionalità aggiuntive di Safari come riempimento automatico, lettura e la possibilità di condividere i cookie e dati con Safari per dispositivi mobili. Interazione tra l'utente e la `SFSafariViewController` non è possibile accedere all'app. L'app non avrà accesso a tutte le funzionalità di Safari predefinito.
 
Inoltre, per impostazione predefinita, implementa un **eseguita** pulsante, che consente all'utente di tornare all'app e Avanti e indietro di pulsanti di navigazione che consente all'utente di spostarsi da una pila di pagine web. Inoltre, fornisce inoltre l'utente con un indirizzo barra assegnandogli il tranquilli del fatto che si trovano in una pagina web previsto. Barra degli indirizzi non consente all'utente di modificare l'url. 

Queste implementazioni non può essere modificato, pertanto `SFSafariViewController` è ideale da usare come browser predefinito, se l'app deve presentare una pagina Web senza personalizzazione.

Il codice seguente consente di avviare un `SFSafariViewController` nell'app xamarin:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Ciò produce la visualizzazione web seguenti:

[![](uiwebview-images/sfsafariviewcontroller.png "Una visualizzazione web di esempio con SFSafariViewController")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

È inoltre possibile aprire l'app mobile Safari all'interno dell'app, tramite il codice riportato di seguito:

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

Ciò produce la visualizzazione web seguenti:

[![](uiwebview-images/safari.png "Una pagina web presentata in Safari")](uiwebview-images/safari.png#lightbox)

Spostamento da app in Safari utenti deve sempre essere generalmente evitato. La maggior parte degli utenti non previsti spostamento all'esterno dell'applicazione, pertanto se si esce dall'applicazione, gli utenti possono non restituire, essenzialmente la terminazione di engagement.

miglioramenti di iOS 9 consentono all'utente di tornare all'App tramite un pulsante Indietro che viene fornito nell'angolo superiore sinistro della pagina Safari.

## <a name="app-transport-security"></a>Sicurezza del trasporto di App

Sicurezza del trasporto, App o *at* è stato introdotto da Apple in iOS 9 per assicurare che tutte le comunicazioni internet siano conformi per le procedure consigliate di connessione protetta.

Per ulteriori informazioni su AT, incluso come implementarla nell'app, vedere il [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md) Guida.

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazioni Web (esempio)](https://developer.xamarin.com/samples/monotouch/WebView/)
