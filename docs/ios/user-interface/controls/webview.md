---
title: Visualizzazioni Web in Novell. iOS
description: Questo documento descrive i vari modi in cui un'app Novell. iOS può visualizzare il contenuto Web. Vengono illustrati WKWebView, SFSafariViewController, Safari e la sicurezza del trasporto app.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 7c469a011a70840cfe94a7f87ed77f03968a3525
ms.sourcegitcommit: ec112800a76089ab1db66fe24b8bbcc510e067b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80159821"
---
# <a name="web-views-in-xamarinios"></a>Visualizzazioni Web in Novell. iOS

Nel corso della durata di iOS Apple ha rilasciato una serie di modi per gli sviluppatori di app di incorporare funzionalità di visualizzazione Web nelle app. La maggior parte degli utenti usa il Web browser Safari incorporato sul dispositivo iOS e quindi prevede che le funzionalità di visualizzazione Web di altre app siano coerenti con questa esperienza. Si aspettano che gli stessi movimenti funzionino, le prestazioni siano uguali e la funzionalità stessa.

iOS 11 ha introdotto nuove modifiche per `WKWebView` e `SFSafariViewController`. Per ulteriori informazioni, vedere la Guida alle [modifiche Web in iOS 11](~/ios/platform/introduction-to-ios11/web.md) .

## <a name="wkwebview"></a>WKWebView

`WKWebView` è stato introdotto in iOS 8 che consente agli sviluppatori di app di implementare un'interfaccia di esplorazione Web simile a quella di Mobile Safari. Ciò è dovuto in parte al fatto che `WKWebView` usa il motore di Nitro JavaScript, lo stesso motore usato da Mobile Safari. `WKWebView` deve sempre essere usato su UIWebView, laddove possibile, a causa delle prestazioni migliori, compilate in movimenti intuitivi e della facilità di interazione tra la pagina Web e l'app.

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

Per impostazione predefinita, implementa anche un pulsante **done** , che consente all'utente di tornare facilmente all'app e i pulsanti di spostamento avanti e indietro, consentendo all'utente di spostarsi tra una pila di pagine Web. Inoltre, fornisce all'utente una barra degli indirizzi, per consentirgli di ricordare che si trovano nella pagina Web prevista. La barra degli indirizzi non consente all'utente di modificare l'URL.

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

## <a name="uiwebview-deprecation"></a>Deprecazione UIWebView

`UIWebView` è il modo legacy di Apple di fornire contenuto Web nell'app. È stata rilasciata in iOS 2,0 ed è stata deprecata a partire da 8,0.

> [!IMPORTANT]
> `UIWebView` è stato deprecato. Le nuove app che usano questo controllo [non verranno accettate nell'App Store a partire dal 2020 aprile e gli aggiornamenti delle app che usano questo controllo non verranno accettati dal 2020 dicembre](https://developer.apple.com/news/?id=12232019b).
>
> [La documentazione di Apple `UIWebView`](https://developer.apple.com/documentation/uikit/uiwebview) suggerisce che le app usino invece [`WKWebView`](#wkwebview) .
>
> Per informazioni sulle risorse relative all'`UIWebView` avviso di deprecazione (ITMS-90809) durante l'uso di Novell. Forms, vedere la documentazione relativa a [Novell. Forms WebView](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809) .

Gli sviluppatori che hanno inviato applicazioni iOS negli ultimi sei mesi (o così via) potrebbero aver ricevuto un avviso dall'App Store, circa `UIWebView` essere deprecato.

Le deprecazioni delle API sono comuni. Novell. iOS USA attributi personalizzati per segnalare tali API (e suggerire sostituzioni quando disponibili) agli sviluppatori. Ciò che è diverso questa volta e molto meno comune è che la deprecazione **verrà applicata** dall'App Store di Apple al momento dell'invio.

Sfortunatamente, la rimozione del tipo di `UIWebView` da `Xamarin.iOS.dll` è una [modifica di rilievo binaria](https://docs.microsoft.com/dotnet/core/compatibility/categories#binary-compatibility). Questa modifica interrompe le librerie di terze parti esistenti, incluse alcune che potrebbero non essere supportate o persino ricompilabili (ad esempio, l'origine chiusa). Questa operazione creerà solo altri problemi per gli sviluppatori. Pertanto, il tipo non è *ancora*stato rimosso.

A partire da [Novell. iOS 13,16](https://docs.microsoft.com/xamarin/ios/release-notes/13/13.16) sono disponibili nuovi strumenti e rilevamento che consentono di eseguire la migrazione da `UIWebView`.

### <a name="detection"></a>Rilevamento

Se si have ' NT di recente un'applicazione iOS all'App Store di Apple, è possibile chiedersi se questa situazione si applica alle applicazioni.

Per scoprirlo, è possibile aggiungere `--warn-on-type-ref=UIKit.UIWebView` agli **argomenti aggiuntivi di mTouch** del progetto. In questo modo verrà visualizzato un avviso per **qualsiasi** riferimento all'`UIWebView` deprecato all'interno dell'applicazione (e tutte le relative dipendenze). Vengono usati avvisi diversi per segnalare i tipi **prima** e **dopo** l'esecuzione del linker gestito.

Gli avvisi, come altri, possono essere trasformati in errori usando `-warnaserror:`. Questa operazione può essere utile se si desidera assicurarsi che una nuova dipendenza da `UIWebView` non venga aggiunta dopo le verifiche. Ad esempio,

* `-warnaserror:1502` segnalerà gli errori se vengono trovati riferimenti in assembly precollegati.
* `-warnaserror:1503` segnalerà gli errori se vengono trovati riferimenti negli assembly post-collegamento.

È anche possibile silenziare gli avvisi se i risultati di pre/post-collegamento non sono utili. Ad esempio,

* `-nowarn:1502` **non** segnalerà gli avvisi se vengono trovati riferimenti negli assembly precollegati.
* `-nowarn:1503` **non** segnalerà gli avvisi se vengono trovati riferimenti negli assembly post-collegamento.

### <a name="removal"></a>Rimozione

Ogni applicazione è univoca. La rimozione `UIWebView` dall'applicazione può richiedere passaggi diversi a seconda di come e dove viene utilizzata. Gli scenari più comuni sono i seguenti:

- Non è possibile usare `UIWebView` all'interno dell'applicazione. Tutto funziona correttamente. **Non** devono essere presenti avvisi quando si inviano ad AppStore. Non è necessario alcun altro elemento.
- Utilizzo diretto dei `UIWebView` dall'applicazione. Per iniziare, rimuovere l'uso di `UIWebView`, ad esempio, sostituirlo con i tipi di `WKWebView` (iOS 8) o `SFSafariViewController` (iOS 9) più recenti. Al termine di questa operazione, il linker gestito non dovrebbe vedere alcun riferimento a `UIWebView` e il file binario dell'app finale non avrà alcuna traccia.
- Utilizzo indiretto. `UIWebView` possono essere presenti in alcune librerie di terze parti, gestite o native, utilizzate dall'applicazione. Per iniziare, aggiornare le dipendenze esterne alle versioni più recenti perché questa situazione potrebbe essere già stata risolta in una versione più recente. In caso contrario, contattare il responsabile o i gestori delle librerie e richiedere i piani di aggiornamento.

In alternativa, è possibile provare gli approcci seguenti:

1. Se si usa **Novell. Forms**, leggere questo [post di Blog](https://devblogs.microsoft.com/xamarin/uiwebview-deprecation-xamarin-forms/).
1. Abilitare il linker gestito (sull'intero progetto o, almeno, sulla dipendenza usando `UIWebView`), in modo che *possa* essere rimosso, se non vi si fa riferimento. Questo consente di risolvere il problema, ma potrebbe essere necessario ulteriore lavoro per rendere il codice del linker sicuro.
1. Se non si riesce a modificare le impostazioni del linker gestito, vedere i casi speciali indicati di seguito.

#### <a name="applications-cannot-use-the-linker-or-change-its-settings"></a>Le applicazioni non possono usare il linker (o modificare le impostazioni)

Se per qualche motivo **non** si usa il linker gestito, ad esempio **non collegare**, il simbolo di `UIWebView` rimarrà nell'app binaria che verrà inviata ad Apple e potrebbe essere rifiutato.

Una soluzione *valida* consiste nell'aggiungere `--optimization=force-rejected-types-removal` agli **argomenti mTouch aggiuntivi**del progetto. Questa operazione eliminerà le tracce di `UIWebView` dall'applicazione. Tuttavia, il codice che fa riferimento al tipo **non** funzionerà correttamente (prevedere eccezioni o arresti anomali). Questo approccio deve essere usato solo se si è certi che il codice non sia raggiungibile in fase di esecuzione (anche se è raggiungibile tramite analisi statica).

#### <a name="support-for-ios-7x-or-earlier"></a>Supporto per iOS 7. x (o versioni precedenti)

`UIWebView` è stato parte di iOS dalla versione 2.0. Le sostituzioni più comuni sono `WKWebView` (iOS 8) e `SFSafariViewController` (iOS 9). Se l'applicazione supporta ancora versioni precedenti di iOS, è necessario considerare le opzioni seguenti:

* Rendere iOS 8 la versione di destinazione minima (una decisione relativa al tempo di compilazione).
* Usare `WKWebView` solo se l'app è in esecuzione in iOS 8 + (decisione in fase di esecuzione).

#### <a name="applications-not-submitted-to-apple"></a>Applicazioni non inviate ad Apple

Se l'applicazione non viene inviata ad Apple, è opportuno pianificare l'allontanamento dall'API deprecata perché può essere rimossa nelle versioni future di iOS. Tuttavia, è possibile eseguire questa transizione usando un calendario personalizzato.

## <a name="related-links"></a>Collegamenti correlati

- [Webviews (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
