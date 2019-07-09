---
title: Visualizzazioni HTML predefiniti con i modelli Razor
description: " Uso di una pagina Web a schermo intero per il rendering HTML può essere un modo semplice ed efficace per eseguire il rendering di formattazione complessa in modo più piattaforme, specialmente se si dispone già di HTML, JavaScript e CSS di un progetto sito Web."
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: asb3993
ms.author: amburns
ms.date: 07/24/2018
ms.openlocfilehash: 539f59b9835cab6281327bcd1a37482ef82b62cc
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650173"
---
# <a name="building-html-views-using-razor-templates"></a>Visualizzazioni HTML predefiniti con i modelli Razor

Nell'ambiente di sviluppo per dispositivi mobili il termine "app ibrida" in genere fa riferimento a un'applicazione che presenta alcune (o tutte) le schermate di come pagine HTML all'interno di un controllo Visualizzatore web ospitate.

Esistono alcuni ambienti di sviluppo che consentono di compilare app per dispositivi mobili completamente in HTML e JavaScript, tuttavia tali App può essere soggetta a problemi di prestazioni durante il tentativo di eseguire un'elaborazione complessa o gli effetti dell'interfaccia utente e sono anche limitati alla piattaforma funzionalità che possono accedere.

Xamarin offre il meglio di entrambi gli approcci, soprattutto se si utilizza il motore di creazione di modelli HTML Razor. Con Xamarin è possibile creare visualizzazioni HTML basato su modelli multipiattaforma che usano JavaScript e CSS, ma anche hanno accesso completo alle API della piattaforma sottostante e veloce l'elaborazione mediante C#.

Questo documento illustra come usare il motore di creazione modello Razor per generare visualizzazioni di JavaScript e HTML + CSS che possono essere usate nelle diverse piattaforme per dispositivi mobili usando Xamarin.

## <a name="using-web-views-programmatically"></a>Tramite le visualizzazioni Web a livello di codice

Prima che contiene informazioni su Razor questa sezione descrive come usare le visualizzazioni web per visualizzare il contenuto HTML direttamente, in particolare contenuto HTML che viene generato all'interno di un'app.

Xamarin offre accesso completo alle API della piattaforma sottostante in iOS e Android, in modo che risulti semplice creare e visualizzare HTML usando c#. Di seguito è riportata la sintassi di base per ogni piattaforma.

### <a name="ios"></a>iOS

Visualizzazione HTML in un controllo UIWebView in xamarin. IOS accetta inoltre solo poche righe di codice:

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

Vedere le [iOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/) ricette per altri dettagli sull'uso del controllo UIWebView.

### <a name="android"></a>Android

Visualizzazione HTML in un controllo WebView usando xamarin. Android viene eseguita in solo poche righe di codice:

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable JavaScript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

Vedere le [WebView Android](http://docs.xamarin.com/recipes/android/controls/webview/) ricette per altri dettagli sull'uso del controllo WebView.

### <a name="specifying-the-base-directory"></a>Specifica la Directory di Base

In entrambe le piattaforme è presente un parametro che specifica la directory di base per la pagina HTML. Questo è il percorso nel file system del dispositivo che viene usato per risolvere i riferimenti relativi a risorse quali immagini e file CSS. Ad esempio, tag, ad esempio

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

Fare riferimento a questi file: **Style. CSS**, **monkey.jpg** e **js**. L'impostazione della directory base indica alla visualizzazione web in cui questi file si trovano in modo che possano essere caricate nella pagina.

#### <a name="ios"></a>iOS

L'output del modello viene eseguito il rendering in iOS con il codice c# seguente:

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

La directory di base viene specificata come `NSBundle.MainBundle.BundleUrl` che fa riferimento alla directory in cui l'applicazione viene installata in. Tutti i file di **le risorse** cartella vengono copiati in questa posizione, ad esempio il **Style. CSS** file riportato di seguito:

 ![soluzione iPhoneHybrid](images/image1_240x163.png)

L'azione di compilazione per tutti i file di contenuto statici deve essere **BundleResource**:

 ![azione di compilazione progetto iOS: BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android richiede anche una directory di base deve essere passato come parametro quando stringhe html vengono visualizzate in una visualizzazione web.

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

La stringa speciale **file:///android_asset/** fa riferimento alla cartella asset Android nell'app, illustrato di seguito che contiene il **Style. CSS** file.

 ![Soluzione AndroidHybrid](images/image3_240x167.png)

L'azione di compilazione per tutti i file di contenuto statici deve essere **AndroidAsset**.

 ![Azione di compilazione progetto Android: AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>La chiamata a C# dal codice HTML e JavaScript

Quando una pagina html viene caricata in una visualizzazione web, considera i collegamenti e il form come se la pagina è stata caricata da un server. Ciò significa che se l'utente fa clic su un collegamento o invia un modulo di visualizzazione web tenteranno di passare alla destinazione specificata.

Se il collegamento a un server esterno (come google.com) la visualizzazione web tenterà di caricare il sito Web esterno (presupponendo che sia disponibile una connessione internet).

```html
<a href="http://google.com/">Google</a>
```

Se il collegamento relativo la visualizzazione web tenterà di caricare tale contenuto dalla directory di base. Ovviamente alcuna connessione di rete non è necessaria per il corretto funzionamento, come il contenuto viene archiviato nell'app sul dispositivo.

```html
<a href="somepage.html">Local content</a>
```

Le azioni modulo seguono la stessa regola.

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

Non intende ospitare un server web nel client. Tuttavia, è possibile usare le stesse tecniche di comunicazione server utilizzate in modelli di progettazione reattiva attuali per chiamare i servizi tramite HTTP GET e gestire le risposte in modo asincrono con la creazione di JavaScript (o chiamante JavaScript già ospitata nella visualizzazione web). In questo modo è possibile passare facilmente i dati dal codice HTML al codice c# per l'elaborazione, quindi visualizzare che i risultati nuovamente nella pagina HTML.

IOS e Android forniscono un meccanismo per il codice dell'applicazione di intercettare questi eventi di navigazione in modo che possa rispondere codice dell'app (se richiesto). Questa funzionalità è fondamentale per la creazione di App ibride perché consente di includere il codice nativo interagire con la visualizzazione web.

#### <a name="ios"></a>iOS

L'evento ShouldStartLoad nella visualizzazione web in iOS può essere sottoposto a override per consentire al codice dell'applicazione di gestire una richiesta di spostamento (ad esempio, fare clic su un collegamento). Tutte le informazioni di fornire i parametri del metodo

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

e quindi assegnare il gestore dell'evento:

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

In Android semplicemente sottoclasse WebViewClient e quindi implementare il codice per rispondere alla richiesta di spostamento.

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

e quindi impostare i client nella visualizzazione web:

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>La chiamata da JavaScriptC#

Oltre a indicare una visualizzazione web per caricare una nuova pagina HTML, C# codice eseguibile anche JavaScript all'interno della pagina attualmente visualizzata. Interi blocchi di codice JavaScript possono essere creati usando C# stringhe ed eseguito, o è possibile elaborare chiamate di metodo per già disponibile nella pagina tramite JavaScript `script` tag.

#### <a name="android"></a>Android

Creare il codice JavaScript da eseguire e quindi con prefisso "javascript:" e indicare la visualizzazione web per caricare tale stringa:

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

iOS web viste forniscono un metodo in particolare per chiamare JavaScript:

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>Riepilogo

In questa sezione è state introdotte le funzionalità dei controlli di visualizzazione web su Android e iOS che consentono di creare applicazioni ibride con Xamarin, tra cui:

-  La possibilità di caricare codice HTML da stringhe generate nel codice,
-  La possibilità di fare riferimento a file locali (CSS, JavaScript, immagini o altri file HTML),
-  La possibilità di intercettare le richieste di navigazione nel codice c#,
-  Consente di chiamare da JavaScript C# codice.


Nella sezione successiva introduce Razor, che rende più semplice creare il codice HTML da usare in App ibride.

## <a name="what-is-razor"></a>Che cos'è Razor?

Razor è un motore di creazione di modelli che è stata introdotta con ASP.NET MVC, originariamente per l'esecuzione nel server e generare codice HTML di essere servite ai web browser.

Il motore di creazione modello Razor estende la sintassi standard HTML con C# in modo che è possibile esprimere il layout e incorporare con facilità i fogli di stile CSS e JavaScript. Il modello può fare riferimento a una classe di modello, che può essere qualsiasi tipo personalizzato e le cui proprietà sono accessibili direttamente dal modello. Uno dei vantaggi principali è la possibilità di combinare facilmente sintassi HTML e c#.

I modelli Razor non sono limitati all'utilizzo sul lato server, può essere incluso anche nelle App Xamarin. Usano i modelli Razor e la possibilità di lavorare con visualizzazioni web a livello di codice consente alle applicazioni ibride multipiattaforma sofisticato essere compilata con Xamarin.

### <a name="razor-template-basics"></a>Nozioni fondamentali sui modelli Razor

File di modello Razor hanno una **cshtml** estensione di file. Possono essere aggiunti a un progetto Xamarin dalla sezione del modello di testo il **nuovo File** finestra di dialogo:

 ![Nuovo File - modello Razor](images/image5_400x201.png)

Un semplice modello Razor ( **RazorView.cshtml**) è illustrato di seguito.

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

Notare le differenze seguenti da un file HTML normale:

-  Il `@` simbolo ha un significato speciale nei modelli di Razor: indica che è la seguente espressione c# da valutare.
- `@model` direttiva viene sempre visualizzata come la prima riga di un file di modello Razor.
-  Il `@model` direttiva deve essere seguita da un tipo. In questo esempio una semplice stringa vengono passata al modello, ma potrebbe trattarsi di qualsiasi classe personalizzata.
-  Quando si `@Model` viene fatto riferimento in tutto il modello, fornisce un riferimento all'oggetto passato al modello quando vengono generati (in questo esempio sarà una stringa).
-  L'IDE genererà automaticamente la classe parziale per i modelli (file con il **cshtml** estensione). È possibile visualizzare questo codice, ma non deve essere modificato.
 ![RazorView.cshtml](images/image6_125x34.png) la classe parziale è denominata RazorView corrisponda al nome di file di modello. cshtml. Si tratta del nome che viene usato per fare riferimento al modello nel codice c#.
- `@using` le istruzioni possono essere incluse nella parte superiore di un modello di Razor per includere altri spazi dei nomi.


L'output HTML finale può quindi essere generato con il codice c# seguente. Si noti che vengono specificati il modello per essere una stringa "Hello World" che verrà incorporato nell'output del modello sottoposto a rendering.

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

Ecco l'output visualizzato in una visualizzazione web in iOS Simulator e dell'emulatore Android:

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>Ulteriori informazioni sulla sintassi Razor

In questa sezione a che verranno introdurre alcune sintassi Razor di base che consentono di iniziare usarlo. Gli esempi in questa sezione popolare la classe seguente con i dati e visualizzarlo utilizzando Razor:

```csharp
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

Tutti gli esempi usano il seguente codice di inizializzazione dei dati

```csharp
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>Visualizzazione delle proprietà del modello

Quando il modello è una classe con le proprietà, sono facilmente viene fatto riferimento nel modello Razor come illustrato in questo modello di esempio:

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

Possibile eseguire il rendering in una stringa utilizzando il codice seguente:

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

Seguito è riportato l'output finale in una visualizzazione web in iOS Simulator e dell'emulatore Android:

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>Istruzioni c#

Codice c# più complessi possono essere inclusi nel modello, ad esempio gli aggiornamenti di proprietà del modello e il calcolo di età in questo esempio:

```html
@model Monkey
<html>
    <body>
    @{
        Model.Name = "Rupert X. Monkey";
        Model.Birthday = new DateTime(2011,3,1);
    }
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    </body>
</html>
```

È possibile scrivere a riga singola c# le espressioni complesse (ad esempio, l'età di formattazione) racchiudendo il codice con `@()`.

Più istruzioni c# possono essere scritti, racchiuderli con `@{}`.

#### <a name="if-else-statements"></a>Istruzioni if-else

Rami del codice possono essere espressi con `@if` come illustrato in questo esempio di modello.

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <p>@Model.FavoriteFoods.Count favorites</p>
    }
    </body>
</html>
```

#### <a name="loops"></a>Cicli

Cicli dei costrutti come `foreach` possono anche essere aggiunti. Il `@` prefisso può essere utilizzato nella variabile del ciclo ( `@food` in questo caso) a eseguirne il rendering in HTML.

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <ul>
            @foreach (var food in @Model.FavoriteFoods) {
                <li>@food</li>
            }
        </ul>
    }
    </body>
</html>
```

Viene riportato l'output del modello precedente che eseguono iOS Simulator e l'emulatore Android:

 ![Rupert X Monkey](images/image9_520x277.png)

In questa sezione ha illustrato le nozioni di base dell'utilizzo di modelli Razor per il rendering delle visualizzazioni di sola lettura semplici. Nella sezione successiva illustra come creare app più completa, utilizzando Razor che può accettare input dell'utente e interazione tra JavaScript nella visualizzazione HTML e C#.

## <a name="using-razor-templates-with-xamarin"></a>Uso di modelli Razor con Xamarin

Questa sezione viene illustrato come utilizzare compilazione la propria applicazione ibrida con i modelli di soluzione in Visual Studio per Mac. Sono disponibili tre modelli dal **File > New > soluzione...**  finestra:

- **Android > App > App WebView Android**
- **iOS > App > App WebView**
- **Progetto ASP.NET MVC**



Il **nuova soluzione** finestra simile per iPhone e i progetti Android: la descrizione della soluzione nella parte destra evidenzia il supporto per il motore di creazione modello Razor.

 ![Creazione di iPhone e Android soluzioni](images/image13_1139x959.png)

Si noti che è possibile aggiungere facilmente una **. cshtml** modello Razor *qualsiasi* progetto Xamarin esistente, non è necessario usare questi modelli di soluzione. i progetti iOS non richiedono uno Storyboard usare Razor. è sufficiente aggiungere un controllo UIWebView qualsiasi visualizzazione a livello di codice ed è possibile eseguire il rendering di modelli Razor intero nel codice c#.

Il contenuto di soluzione del modello predefinito per iPhone e i progetti Android è le seguenti:

 ![iPhone e Android modelli](images/image10_428x310.png)

I modelli forniscono infrastruttura delle applicazioni pronti per caricare un modello Razor con un oggetto modello di dati, elaborare l'input utente e comunicare all'utente tramite JavaScript.

Le parti importanti della soluzione sono:

-  Il contenuto statico, ad esempio la **Style. CSS** file.
-  Ad esempio file di modello Razor. cshtml **RazorView.cshtml** .
-  Modellare le classi che fanno riferimento, ad esempio nei modelli Razor **ExampleModel.cs** .
-  La classe specifica della piattaforma che consente di creare la visualizzazione web ed esegue il rendering del modello, ad esempio la `MainActivity` in Android e `iPhoneHybridViewController` in iOS.


La sezione seguente viene illustrato come funzionano i progetti.

### <a name="static-content"></a>Contenuto statico

Il contenuto statico include fogli di stile CSS, immagini, file JavaScript o altro contenuto che può essere collegato da o fa riferimento un file HTML visualizzato in una visualizzazione web.

I progetti del modello includono un foglio di stile minimo per dimostrare come includere contenuto statico nell'app ibrida. Il foglio di stile CSS viene fatto riferimento nel modello simile al seguente:

```html
<link rel="stylesheet" href="style.css" />
```

È possibile aggiungere qualsiasi foglio di stile e file JavaScript necessari, tra cui Framework quali JQuery.

### <a name="razor-cshtml-templates"></a>Cshtml Razor modelli

Il modello include un Razor **. cshtml** file di codice per agevolare la comunicazione tra i dati tra il codice HTML/JavaScript ha già scritto e C#. Questo modo sarà possibile eseguire il backup di App ibride sofisticato che non solo la visualizzazione dei dati di sola lettura dal modello, ma anche accettare l'input dell'utente nel codice HTML e passarlo al codice c# per l'elaborazione o archiviazione compilazione.

#### <a name="rendering-the-template"></a>Il modello di rendering

La chiamata di `GenerateString` su un modello esegue il rendering di HTML pronto per la visualizzazione in una visualizzazione web. Se il modello Usa un modello, deve essere fornito prima del rendering. Questo diagramma illustra come il rendering funziona – non che le risorse statiche vengono risolti dalla visualizzazione web in fase di esecuzione utilizzando la directory di base fornita per trovare i file specificati.

 ![Diagramma di flusso Razor](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>La chiamata di codice c# dal modello

Comunicazioni da una visualizzazione web sottoposta a rendering richiamata del linguaggio c# viene eseguita impostando l'URL per la visualizzazione web, e quindi intercettare la richiesta in c# per gestire la richiesta nativa senza ricaricare la visualizzazione web.

Un esempio può essere visualizzato sul pulsante del RazorView viene gestito in modo. Il pulsante ha il codice HTML seguente:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

Il `InvokeCSharpWithFormValues` funzione JavaScript legge tutti i valori dalla HTML Form e viene impostata la `location.href` per la visualizzazione web:

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

Si tenta di spostarsi nella visualizzazione web a un URL con schema personalizzato (ad es. `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

Quando la visualizzazione web nativi elabora questa richiesta di spostamento, abbiamo l'opportunità di intercettarla. In iOS, questa operazione viene eseguita tramite la gestione HandleShouldStartLoad evento dell'UIWebView. In Android, è sufficiente sottoclasse il WebViewClient usato nel form ed eseguire l'override ShouldOverrideUrlLoading.

Gli elementi interni di questi due intercettori di navigazione sono essenzialmente lo stesso.

In primo luogo, verificare che l'URL che la visualizzazione web sta tentando di caricare, e se non viene avviato con lo schema personalizzato (`hybrid:`), consentire la navigazione come di consueto.

Per lo schema URL personalizzato, tutti gli elementi nell'URL tra lo schema e i "?" è il nome del metodo da gestire (in questo caso, "UpdateLabel"). Tutti gli elementi nella stringa di query verrà considerato come parametri alla chiamata al metodo:

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` In questo esempio esegue una quantità minima di manipolazione delle stringhe per il parametro nella casella di testo (anteporre "c# says" alla stringa) e quindi chiama nuovamente alla visualizzazione web.

Dopo la gestione degli URL, il metodo interrompe la navigazione in modo che la visualizzazione web non tenta di completare passare all'URL personalizzato.

#### <a name="manipulating-the-template-from-c"></a>Modifica il modello da c#

Comunicazione con una visualizzazione web HTML da C# viene eseguita tramite una chiamata JavaScript nella visualizzazione web. In iOS, questa operazione viene eseguita chiamando `EvaluateJavascript` sul UIWebView:

```csharp
webView.EvaluateJavascript (js);
```

In Android, JavaScript può essere richiamato nella visualizzazione web caricando il codice JavaScript come un URL usando il `"javascript:"` lo schema dell'URL:

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>Rendere un'app veramente ibride

Questi modelli non dovrà inserire Usa dei controlli nativi in ciascuna piattaforma: l'intera schermata viene compilato con una visualizzazione web single.

HTML può essere molto utile per la creazione di prototipi e visualizzazione di tipi di operazioni web è consigliabile, ad esempio testo con formattazione e layout reattivo. Tuttavia non tutte le attività sono adatti per HTML e JavaScript – lo scorrimento di lunghi elenchi di dati, ad esempio, esegue più efficace l'uso di controlli dell'interfaccia utente nativi, ad esempio ListView o (UITableView in iOS) in Android.

Le visualizzazioni web nel modello possono essere integrate facilmente con i controlli specifici della piattaforma: è sufficiente modificare la **Mainstoryboard** nella finestra di progettazione iOS o il **Resources/layout/Main.axml** in Android.

### <a name="razortodo-sample"></a>RazorTodo Sample

Il [RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo) repository contiene due soluzioni separate per visualizzare le differenze tra un'app completamente basata su HTML e un'app che combina HTML con i controlli nativi:

-  **RazorTodo** -app basate su HTML completamente usando i modelli Razor.
-  **RazorNativeTodo** : Usa controlli di visualizzazione elenco native per iOS e Android, ma viene visualizzata la schermata di modifica con HTML e Razor.


Queste App Xamarin eseguite in iOS e Android, che usano le librerie di classi portabile (librerie di classi portabili) per condividere codice comune, ad esempio le classi di database e il modello. Razor **cshtml** modelli possono essere incluso anche nella libreria di classi Portabile, in modo facilmente quelle condivise tra le piattaforme.

Entrambe le app di esempio incorporano la condivisione di Twitter e le API sintesi vocale dalla piattaforma, native dimostrare che le applicazioni ibride xamarin tuttavia ancora possano accedere a tutte le funzionalità sottostanti dalle viste basato su modelli HTML Razor.

Il **RazorTodo** app Usa i modelli HTML Razor per le visualizzazioni elenco e modifica. Ciò significa che è possibile creare l'app quasi completamente in una libreria di classi portabile condiviso (incluso il database e **cshtml** modelli Razor). Gli screenshot seguenti mostrano le App per iOS e Android.

 ![RazorTodo](images/Both_700x290.png)

Il **RazorNativeTodo** app Usa un modello HTML Razor per la visualizzazione di modifica, ma implementa un elenco a discesa nativo in ciascuna piattaforma. Questo offre numerosi vantaggi, tra cui:

-  Prestazioni - controlli nativi scorrimento usano la virtualizzazione per garantire lo scorrimento rapido e senza problemi anche con molto lunghi elenchi di dati.
-  Esperienza nativa - elementi di interfaccia utente specifiche della piattaforma sono facilmente abilitato, ad esempio il supporto di indice lo scorrimento rapido in iOS e Android.


Dei principali vantaggi della creazione di App ibride con Xamarin è che è possibile iniziare con un'interfaccia utente completamente basato su HTML (ad esempio, il primo esempio) e quindi aggiungere funzionalità specifiche della piattaforma quando necessario (come illustrato nel secondo esempio). Le schermate di elenco nativo e HTML Razor modificare schermate in riprodotta in iOS e Android sono illustrate di seguito.

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato le funzionalità dei controlli di visualizzazione web disponibile in iOS e Android che facilitano la compilazione delle applicazioni ibride.

È stato illustrato quindi la sintassi che può essere utilizzata per generare HTML nelle App Xamarin usando in modo semplice e il motore di creazione modello Razor. **cshtml** i file di modello Razor. Spiega anche Visual Studio per Mac modelli di soluzioni che consentono la rapida inizia a creare applicazioni ibride con Xamarin.

Infine, ha introdotto RazorTodo esempi che illustrano come combinare le visualizzazioni web API e interfacce utente native.

### <a name="related-links"></a>Collegamenti correlati

- [RazorTodo Sample](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3 - motore di visualizzazione Razor (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [Introduzione alla programmazione Web ASP.NET usando la sintassi Razor (Microsoft)](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)
