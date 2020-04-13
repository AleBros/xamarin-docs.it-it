---
title: Visualizzazioni Web in Xamarin.iOS
description: Questo documento descrive i vari modi in cui un'app Xamarin.iOS può visualizzare contenuto Web. Vengono illustrati wKWebView, SFSafariViewController, Safari e la sicurezza del trasporto di app.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: cc6c87452bf5312d66e33b7c49d3dc216eceb7d6
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628303"
---
# <a name="web-views-in-xamarinios"></a>Visualizzazioni Web in Xamarin.iOS

Nel corso della vita di iOS Apple ha rilasciato una serie di modi per gli sviluppatori di app per incorporare la funzionalità di visualizzazione web nelle loro applicazioni. La maggior parte degli utenti utilizza il browser web Safari integrato sul proprio dispositivo iOS e quindi si aspetta che la funzionalità di visualizzazione web da altre applicazioni sia coerente con questa esperienza. Si aspettano che gli stessi gesti funzionino, le prestazioni siano alla pari e la funzionalità lo stesso.

iOS 11 ha introdotto nuove modifiche a entrambi `WKWebView` e `SFSafariViewController`. Per altre informazioni su questi, vedere la guida Modifiche Web in iOS 11.For more information on these, see [the Web changes in iOS 11 guide.](~/ios/platform/introduction-to-ios11/web.md)

## <a name="wkwebview"></a>WKWebView (visualizzazione di file WKWebView)

`WKWebView`è stato introdotto in iOS 8 permettendo agli sviluppatori di app di implementare un'interfaccia di navigazione web simile a quella di Safari mobile. Ciò è dovuto, in parte, al fatto che `WKWebView` utilizza il motore Nitro Javascript, lo stesso motore utilizzato dal cellulare Safari. `WKWebView`deve essere sempre usato su UIWebView dove possibile a causa delle prestazioni aumentate, dei gesti di facile utilizzo incorporati e della facilità di interazione tra la pagina Web e l'app.

`WKWebView`può essere aggiunto all'app in modo quasi identico a UIWebView, tuttavia, poiché lo sviluppatore ha molto più controllo sull'interfaccia utente/esperienza utente e sulle funzionalità. La creazione e la visualizzazione dell'oggetto visualizzazione Web visualizzeranno la pagina richiesta, tuttavia è possibile controllare come viene presentata la visualizzazione, come l'utente può spostarsi e come l'utente esce dalla visualizzazione.  

Il codice seguente può essere `WKWebView` usato per avviare un nell'app Xamarin.iOS:

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

È importante notare `WKWebView` che `WebKit` si trova nello spazio dei nomi, pertanto sarà necessario aggiungere questa direttiva using all'inizio della classe.

`WKWebView`può essere utilizzato anche nelle app Xamarin.Mac e dovresti usarlo se stai creando un'app Mac/iOS multipiattaforma.

La ricetta [Gestisci avvisi JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) fornisce anche informazioni sull'utilizzo di WKWebView con Javascript.

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController`è l'ultimo modo per fornire contenuti web dalla tua app ed è disponibile in iOS 9 e versioni successive. A `UIWebView` `WKWebView`differenza `SFSafariViewController` di o , è un Controller di visualizzazione e pertanto non può essere utilizzato con altre visualizzazioni. È necessario `SFSafariViewController` presentare come un nuovo Controller di visualizzazione, nello stesso modo in cui si presenterebbe qualsiasi Controller di visualizzazione.

 `SFSafariViewController`è essenzialmente un 'mini safari' che può essere incorporato nella tua app. Come WKWebView utilizza lo stesso motore Nitro Javascript, ma fornisce anche una gamma di funzionalità aggiuntive di Safari come Riempimento automatico, Lettore e la possibilità di condividere cookie e dati con Safari per dispositivi mobili. L'interazione tra `SFSafariViewController` l'utente e l' non è accessibile all'app. L'app non avrà accesso a nessuna delle funzioni predefinite di Safari.

Inoltre, per impostazione predefinita, implementa un pulsante **Fine,** che consente all'utente di tornare facilmente all'app e i pulsanti di spostamento avanti e indietro, consentendo all'utente di spostarsi all'interno di uno stack di pagine Web. Inoltre, fornisce anche all'utente una barra degli indirizzi dando loro la tranquillità che sono sulla pagina web prevista. La barra degli indirizzi non consente all'utente di modificare l'URL.

Queste implementazioni non possono `SFSafariViewController` essere modificate, pertanto è ideale usare come browser predefinito se l'app vuole presentare una pagina Web senza alcuna personalizzazione.

Il codice seguente può essere `SFSafariViewController` usato per avviare un nell'app Xamarin.iOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Questo produce la seguente visualizzazione web:

[![Visualizzazione Web di esempio con SFSafariViewController](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

È anche possibile aprire l'app Safari per dispositivi mobili dall'app, utilizzando il codice seguente:

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

Questo produce la seguente visualizzazione web:

[![Una pagina web presentata in Safari](webview-images/safari.png)](webview-images/safari.png#lightbox)

La navigazione degli utenti dalla tua app a Safari dovrebbe in genere essere sempre evitata. La maggior parte degli utenti non si aspetta la navigazione all'esterno dell'applicazione, pertanto se si esce dall'app, gli utenti potrebbero non restituirla, main parte uccidendo essenzialmente l'engagement.

I miglioramenti di iOS 9 consentono all'utente di tornare facilmente alla tua app tramite un pulsante Indietro fornito nell'angolo superiore sinistro della pagina di Safari.

## <a name="app-transport-security"></a>ATS (App Transport Security)

App Transport Security, o *ATS* è stato introdotto da Apple in iOS 9 per garantire che tutte le comunicazioni Internet siano conformi alle best practice di connessione sicure.

Per altre informazioni su ATS, incluso come implementarlo nell'app, vedere la Guida alla sicurezza del trasporto delle [app.](~/ios/app-fundamentals/ats.md)

## <a name="uiwebview-deprecation"></a>Deprecazione di UIWebView

`UIWebView`è il modo più erede di Apple di fornire contenuti web nella tua app. È stato rilasciato in iOS 2.0 ed è stato deprecato a partire da 8.0.It was released in iOS 2.0, and has been deprecated as of 8.0.

> [!IMPORTANT]
> `UIWebView` è stato deprecato. Le nuove app che utilizzano questo controllo [non verranno accettate nell'App Store a partire da aprile 2020 e gli aggiornamenti delle app che utilizzano questo controllo non saranno accettati entro dicembre 2020.](https://developer.apple.com/news/?id=12232019b)
>
> [La documentazione di `UIWebView` Apple](https://developer.apple.com/documentation/uikit/uiwebview) suggerisce che le app devono invece essere usate. [`WKWebView`](#wkwebview)
>
> Se si cercano risorse relative `UIWebView` all'avviso di deprecazione (ITMS-90809) durante l'utilizzo di Xamarin.Forms, fare riferimento alla documentazione di [Xamarin.Forms WebView.](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809)

Gli sviluppatori che hanno presentato domande iOS negli ultimi sei mesi (o `UIWebView` giù di lì) potrebbero aver ricevuto un avviso dall'App Store, sulla deprecazione.

Le deprecazioni delle API sono comuni. Xamarin.iOS usa gli attributi personalizzati per segnalare tali API (e suggerire sostituzioni quando disponibili) agli sviluppatori. Ciò che è diverso questa volta, e molto meno comune, è che la deprecazione **verrà applicata** dall'App Store di Apple al momento dell'invio.

Sfortunatamente, `UIWebView` la `Xamarin.iOS.dll` rimozione del tipo da è una [modifica di interruzione binaria](https://docs.microsoft.com/dotnet/core/compatibility/categories#binary-compatibility). Questa modifica interromperà le librerie di terze parti esistenti, incluse alcune che potrebbero non essere più supportate o addirittura ricompilabili (ad esempio, closed source). Questo creerà solo ulteriori problemi per gli sviluppatori. Pertanto, non stiamo rimuovendo il tipo *ancora*.

A partire da [Xamarin.iOS 13.16](https://docs.microsoft.com/xamarin/ios/release-notes/13/13.16) sono disponibili nuovi `UIWebView`strumenti di rilevamento e strumenti che consentono di eseguire la migrazione da .

### <a name="detection"></a>Rilevamento

Se hai recentemente inviato un'applicazione iOS all'App Store di Apple, potresti chiederti se questa situazione si applica alle tue applicazioni.

Per scoprirlo, puoi `--warn-on-type-ref=UIKit.UIWebView` aggiungere argomenti **mtouch aggiuntivi** del tuo progetto. Questo avviserà di **qualsiasi** `UIWebView` riferimento al deprecato all'interno dell'applicazione (e tutte le relative dipendenze). Vengono utilizzati avvisi diversi per segnalare i tipi **prima** e **dopo** l'esecuzione del linker gestito.

Gli avvisi, come altri, possono `-warnaserror:`essere trasformati in errori utilizzando . Ciò può essere utile se si desidera `UIWebView` garantire che una nuova dipendenza a non venga aggiunta dopo le verifiche. Ad esempio:

* `-warnaserror:1502`segnalerà errori se vengono trovati riferimenti in assembly pre-collegati.
* `-warnaserror:1503`segnalerà errori se vengono trovati riferimenti negli assembly collegati.

È inoltre possibile disattivare gli avvisi se i risultati del collegamento pre/post non sono utili. Ad esempio:

* `-nowarn:1502`**non** segnalerà avvisi se vengono trovati riferimenti in assembly pre-collegati.
* `-nowarn:1503`**non** segnalerà avvisi se vengono trovati riferimenti negli assembly collegati.

### <a name="removal"></a>Rimozione

Ogni applicazione è unica. La `UIWebView` rimozione dall'applicazione può richiedere passaggi diversi a seconda di come e dove viene usata. Gli scenari più comuni sono i seguenti:The most common scenarios are as follows:

- Non viene utilizzato `UIWebView` all'interno dell'applicazione. Va tutto bene. Non **dovresti** avere avvisi durante l'invio all'AppStore. Nient'altro è richiesto da voi.
- Utilizzo diretto `UIWebView` da parte dell'applicazione. Iniziare rimuovendo l'utilizzo `UIWebView`di , ad esempio, `WKWebView` sostituirlo con i `SFSafariViewController` tipi più recenti (iOS 8) o (iOS 9). Una volta completata questa operazione, il `UIWebView` linker gestito non dovrebbe visualizzare alcun riferimento e il file binario dell'app finale non ne avrà alcuna traccia.
- Utilizzo indiretto. `UIWebView`può essere presente in alcune librerie di terze parti, gestite o native utilizzate dall'applicazione. Iniziare aggiornando le dipendenze esterne alle versioni più recenti poiché questa situazione potrebbe essere già risolta in una versione più recente. In caso contrario, contattare il manutentore delle librerie e chiedere informazioni sui piani di aggiornamento.

In alternativa, è possibile provare i seguenti approcci:

1. Se si utilizza **Xamarin.Forms**, leggere questo post di [blog](https://devblogs.microsoft.com/xamarin/uiwebview-deprecation-xamarin-forms/).
1. Abilitare il linker gestito (nell'intero progetto o, `UIWebView`almeno, sulla dipendenza che utilizza ) in modo che *possa* essere rimosso, se non vi viene fatto riferimento. Ciò risolverà il problema, ma potrebbe essere necessario ulteriore lavoro per rendere il codice linker-safe.
1. Se non è possibile modificare le impostazioni del linker gestito, vedere i casi speciali di seguito.

#### <a name="applications-cannot-use-the-linker-or-change-its-settings"></a>Le applicazioni non possono utilizzare il linker (o modificarne le impostazioni)

Se per qualche motivo **non** si utilizza il linker gestito (ad esempio, **Non collegare**), il `UIWebView` simbolo rimarrà nell'app binaria inviata ad Apple e potrebbe essere rifiutato.

Una soluzione *forte* consiste `--optimize=force-rejected-types-removal` nell'aggiungere al progetto **ulteriori argomenti mtouch**. In questo modo `UIWebView` verranno rimosse le tracce di dall'applicazione. Tuttavia, qualsiasi codice che fa riferimento al tipo **non** funzionerà correttamente (prevedere eccezioni o arresti anomali). Questo approccio deve essere utilizzato solo se si è certi che il codice non è raggiungibile in fase di esecuzione (anche se è raggiungibile tramite l'analisi statica).

#### <a name="support-for-ios-7x-or-earlier"></a>Supporto per iOS 7.x (o versioni precedenti)

`UIWebView`fa parte di iOS fin dalla v2.0. Le sostituzioni più `WKWebView` comuni sono (iOS 8) e `SFSafariViewController` (iOS 9). Se l'applicazione supporta ancora versioni precedenti di iOS, è necessario considerare le opzioni seguenti:If your application still supports older iOS versions you should consider the following options:

* Impostare iOS 8 come versione minima di destinazione (una decisione in fase di compilazione).
* Utilizzare `WKWebView` solo se l'app è in esecuzione su iOS 8 (una decisione di runtime).

#### <a name="applications-not-submitted-to-apple"></a>Applicazioni non inviate ad Apple

Se l'applicazione non viene inviata ad Apple, dovresti pianificare l'opzione Allontanati dall'API deprecata poiché può essere rimossa nelle versioni future di iOS.If your application isn't submitted to Apple, you should plan to move away from the deprecated API since it can be removed in future iOS releases. Tuttavia, è possibile eseguire questa transizione utilizzando il proprio orario.

## <a name="related-links"></a>Collegamenti correlati

- [WebViews (esempio)WebViews (sample)](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
