---
title: Creazione di visualizzazioni HTML con i modelli Razor
description: " L'uso di una pagina Web a schermo intero per il rendering del codice HTML può essere un modo semplice ed efficace per eseguire il rendering di una formattazione complessa in modo multipiattaforma, soprattutto se si dispone già di HTML, JavaScript e CSS da un progetto di sito Web."
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: conceptdev
ms.author: crdun
ms.date: 07/24/2018
ms.openlocfilehash: 7f9f45976d0d7db42be18fede2f21825a385bea4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765340"
---
# <a name="building-html-views-using-razor-templates"></a>Creazione di visualizzazioni HTML con i modelli Razor

Nel mondo dello sviluppo per dispositivi mobili il termine "app ibrida" si riferisce in genere a un'applicazione che presenta alcune o tutte le schermate come pagine HTML all'interno di un controllo visualizzatore Web ospitato.

Ci sono alcuni ambienti di sviluppo che consentono di compilare l'app per dispositivi mobili interamente in HTML e JavaScript, tuttavia tali app possono soffrire di problemi di prestazioni quando si tenta di eseguire complesse attività di elaborazione o dell'interfaccia utente e sono limitate anche nella piattaforma funzionalità a cui possono accedere.

Novell offre il meglio di entrambi i mondi, soprattutto quando si usa il motore di creazione di modelli HTML Razor. Con Novell è possibile creare visualizzazioni HTML basate su modelli multipiattaforma che usano JavaScript e CSS, ma anche l'accesso completo alle API della piattaforma sottostante e l'elaborazione veloce con C#.

Questo documento illustra come usare il motore di creazione modelli Razor per compilare visualizzazioni HTML + JavaScript + CSS che possono essere usate tra piattaforme per dispositivi mobili usando Novell.

## <a name="using-web-views-programmatically"></a>Utilizzo di visualizzazioni Web a livello di codice

Prima di acquisire familiarità con Razor questa sezione illustra come usare le visualizzazioni Web per visualizzare direttamente il contenuto HTML, in particolare il contenuto HTML generato all'interno di un'app.

Novell fornisce l'accesso completo alle API della piattaforma sottostante sia per iOS che per Android, quindi è facile creare e visualizzare HTML usando C#. Di seguito è riportata la sintassi di base per ogni piattaforma.

### <a name="ios"></a>iOS

La visualizzazione del codice HTML in un controllo UIWebView in Novell. iOS richiede anche solo poche righe di codice:

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

Per altre informazioni sull'uso del controllo UIWebView, vedere le ricette [UIWebView iOS](http://docs.xamarin.com/recipes/ios/content_controls/web_view/) .

### <a name="android"></a>Android

La visualizzazione di HTML in un controllo WebView con Novell. Android viene eseguita in poche righe di codice:

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable JavaScript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

Per altre informazioni sull'uso del controllo WebView, vedere le ricette di [WebView per Android](http://docs.xamarin.com/recipes/android/controls/webview/) .

### <a name="specifying-the-base-directory"></a>Specifica della directory di base

In entrambe le piattaforme è presente un parametro che specifica la directory di base per la pagina HTML. Si tratta del percorso del file system del dispositivo usato per risolvere i riferimenti relativi a risorse quali immagini e file CSS. Ad esempio, tag come

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

fare riferimento a questi file: **Style. CSS**, **Monkey. jpg** e **JScript. js**. L'impostazione della directory di base indica alla visualizzazione Web dove si trovano questi file in modo che possano essere caricati nella pagina.

#### <a name="ios"></a>iOS

Il rendering dell'output del modello viene eseguito in iOS C# con il codice seguente:

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

Viene specificata `NSBundle.MainBundle.BundleUrl` la directory di base che fa riferimento alla directory in cui è installata l'applicazione. Tutti i file nella cartella **Resources** vengono copiati in questo percorso, ad esempio il file **Style. CSS** illustrato di seguito:

 ![soluzione iPhoneHybrid](images/image1_240x163.png)

L'azione di compilazione per tutti i file di contenuto statici deve essere **BundleResource**:

 ![azione di compilazione del progetto iOS: BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android richiede inoltre che una directory di base venga passata come parametro quando le stringhe HTML vengono visualizzate in una visualizzazione Web.

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

La stringa speciale **file:///android_asset/** fa riferimento alla cartella risorse Android nell'app, mostrata qui contenente il file **Style. CSS** .

 ![Soluzione AndroidHybrid](images/image3_240x167.png)

L'azione di compilazione per tutti i file di contenuto statici dovrebbe essere **AndroidAsset**.

 ![Azione di compilazione del progetto Android: AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>Chiamata C# da HTML e JavaScript

Quando una pagina HTML viene caricata in una visualizzazione Web, considera i collegamenti e i form come se la pagina venisse caricata da un server. Ciò significa che se l'utente fa clic su un collegamento o Invia un modulo, la visualizzazione Web tenterà di passare alla destinazione specificata.

Se il collegamento è a un server esterno, ad esempio google.com, la visualizzazione Web tenterà di caricare il sito Web esterno (supponendo che sia presente una connessione a Internet).

```html
<a href="http://google.com/">Google</a>
```

Se il collegamento è relativo, la visualizzazione Web tenterà di caricare tale contenuto dalla directory di base. Ovviamente, non è necessaria alcuna connessione di rete perché il contenuto venga archiviato nell'app sul dispositivo.

```html
<a href="somepage.html">Local content</a>
```

Le azioni form seguono la stessa regola.

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

Non verrà ospitato un server Web sul client. Tuttavia, è possibile utilizzare le stesse tecniche di comunicazione del server utilizzate nei modelli di progettazione reattivi odierni per chiamare i servizi tramite HTTP GET e gestire le risposte in modo asincrono creando JavaScript (o chiamando JavaScript già ospitato nella visualizzazione Web). In questo modo è possibile passare facilmente i dati dal C# codice HTML al codice per l'elaborazione, quindi visualizzare di nuovo i risultati nella pagina HTML.

IOS e Android forniscono un meccanismo che consente al codice dell'applicazione di intercettare gli eventi di navigazione, in modo che il codice dell'app possa rispondere (se necessario). Questa funzionalità è fondamentale per la creazione di app ibride perché consente al codice nativo di interagire con la visualizzazione Web.

#### <a name="ios"></a>iOS

È possibile eseguire l'override dell'evento ShouldStartLoad sulla visualizzazione Web in iOS per consentire al codice dell'applicazione di gestire una richiesta di navigazione, ad esempio un clic su un collegamento. I parametri del metodo forniscono tutte le informazioni

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

e quindi assegnare il gestore eventi:

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

In Android è sufficiente sottoclasse WebViewClient, quindi implementare il codice per rispondere alla richiesta di navigazione.

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

quindi impostare il client nella visualizzazione Web:

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>Chiamata di JavaScript da C\#

Oltre a indicare a una visualizzazione Web di caricare una nuova pagina HTML, C# il codice può anche eseguire JavaScript all'interno della pagina attualmente visualizzata. Tutti i blocchi di codice JavaScript possono essere C# creati usando stringhe ed eseguiti oppure è possibile creare chiamate di metodo a JavaScript già disponibile nella pagina `script` tramite tag.

#### <a name="android"></a>Android

Creare il codice JavaScript da eseguire, quindi anteporre "JavaScript:" e indicare alla visualizzazione Web di caricare tale stringa:

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

le visualizzazioni Web iOS forniscono un metodo specifico per chiamare JavaScript:

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>Riepilogo

In questa sezione sono state introdotte le funzionalità dei controlli visualizzazione Web sia per Android che per iOS che consentono di creare applicazioni ibride con Novell, tra cui:

- Possibilità di caricare HTML dalle stringhe generate nel codice,
- La possibilità di fare riferimento a file locali (CSS, JavaScript, immagini o altri file HTML),
- Possibilità di intercettare le richieste di C# navigazione nel codice,
- Possibilità di chiamare JavaScript dal C# codice.

Nella sezione successiva viene introdotto Razor, che consente di creare facilmente il codice HTML da usare nelle app ibride.

## <a name="what-is-razor"></a>Che cos'è Razor?

Razor è un motore di creazione di modelli introdotto con ASP.NET MVC, originariamente per l'esecuzione nel server e la generazione di codice HTML da servire ai Web browser.

Il motore di creazione di modelli Razor estende la C# sintassi HTML standard con in modo che sia possibile esprimere il layout e incorporare facilmente i fogli di stile CSS e JavaScript. Il modello può fare riferimento a una classe di modello, che può essere qualsiasi tipo personalizzato e le cui proprietà sono accessibili direttamente dal modello. Uno dei vantaggi principali è la possibilità di combinare facilmente HTML e C# sintassi.

I modelli Razor non sono limitati all'uso sul lato server, ma possono anche essere inclusi nelle app Novell. L'uso di modelli Razor insieme alla possibilità di usare le visualizzazioni Web a livello di codice consente di compilare applicazioni ibride multipiattaforma sofisticate con Novell.

### <a name="razor-template-basics"></a>Nozioni fondamentali sui modelli Razor

I file modello Razor hanno estensione **cshtml** . È possibile aggiungerli a un progetto Novell dalla sezione modello di testo nella finestra di dialogo **nuovo file** :

 ![Nuovo modello di file Razor](images/image5_400x201.png)

Di seguito è riportato un semplice modello Razor ( **RazorView. cshtml**).

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

Si notino le differenze seguenti rispetto a un normale file HTML:

- Il `@` simbolo ha un significato speciale nei modelli Razor: indica che è C# necessario valutare l'espressione seguente.
- `@model`la direttiva viene sempre visualizzata come prima riga di un file di modello Razor.
- La `@model` direttiva deve essere seguita da un tipo. In questo esempio una stringa semplice viene passata al modello, ma può trattarsi di qualsiasi classe personalizzata.
- Quando `@Model` viene fatto riferimento all'intero modello, fornisce un riferimento all'oggetto passato al modello quando viene generato (in questo esempio sarà una stringa).
- L'IDE genererà automaticamente una classe parziale per i modelli (file con estensione **cshtml** ). È possibile visualizzare questo codice, ma non deve essere modificato.
 ![RazorView. cshtml](images/image6_125x34.png) la classe parziale è denominata RazorView in modo che corrisponda al nome del file modello. cshtml. Si tratta del nome utilizzato per fare riferimento al modello nel C# codice.
- `@using`è anche possibile includere le istruzioni all'inizio di un modello Razor per includere spazi dei nomi aggiuntivi.

L'output HTML finale può quindi essere generato con il codice C# seguente. Si noti che il modello viene specificato come stringa "Hello World" che verrà incorporato nell'output del modello sottoposto a rendering.

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

Ecco l'output visualizzato in una visualizzazione Web del simulatore iOS e emulatore Android:

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>Più sintassi Razor

In questa sezione verranno introdotti alcuni sintassi Razor di base utili per iniziare a usarla. Gli esempi in questa sezione popolano la classe seguente con i dati e li visualizzano con Razor:

```csharp
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

Tutti gli esempi usano il codice di inizializzazione dei dati seguente

```csharp
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>Visualizzazione delle proprietà di un modello

Quando il modello è una classe con proprietà, è possibile farvi riferimento nel modello Razor come illustrato nel modello di esempio seguente:

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

È possibile eseguirne il rendering in una stringa usando il codice seguente:

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

L'output finale è riportato qui in una visualizzazione Web del simulatore iOS e emulatore Android:

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>C#istruzioni

Nel modello C# è possibile includere più complesso, ad esempio gli aggiornamenti delle proprietà del modello e il calcolo dell'età in questo esempio:

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

È possibile scrivere espressioni a riga C# singola complesse, ad esempio la formattazione dell'età, circondando il `@()`codice con.

È C# possibile scrivere più istruzioni circondando tali istruzioni `@{}`con.

#### <a name="if-else-statements"></a>Istruzioni if-else

I rami di codice possono essere `@if` espressi con come illustrato in questo esempio di modello.

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

È possibile aggiungere anche costrutti di ciclo come `foreach` . Il `@` prefisso può essere usato sulla `@food` variabile del ciclo, in questo caso, per eseguirne il rendering in HTML.

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

L'output del modello precedente viene visualizzato in esecuzione nel simulatore iOS e emulatore Android:

 ![Scimmia Rupert X](images/image9_520x277.png)

Questa sezione ha trattato le nozioni di base sull'uso di modelli Razor per eseguire il rendering di semplici visualizzazioni di sola lettura. La sezione successiva illustra come creare app più complete usando Razor che può accettare l'input dell'utente e interoperare tra JavaScript nella visualizzazione HTML e C#.

## <a name="using-razor-templates-with-xamarin"></a>Uso dei modelli Razor con Novell

Questa sezione illustra come usare la creazione di un'applicazione ibrida personalizzata usando i modelli di soluzione in Visual Studio per Mac. Sono disponibili tre modelli dal **File > nuova finestra della soluzione >...** :

- **App Android > > applicazione WebView Android**
- **App > iOS > applicazione WebView**
- **Progetto MVC ASP.NET**

La **nuova** finestra della soluzione ha un aspetto simile al seguente per i progetti iPhone e Android: la descrizione della soluzione a destra evidenzia il supporto per il motore di creazione modello Razor.

 ![Creazione di soluzioni iPhone e Android](images/image13_1139x959.png)

Si noti che è possibile aggiungere facilmente un modello Razor **. cshtml** a *qualsiasi* progetto Novell esistente, non è necessario usare questi modelli di soluzione. per i progetti iOS non è necessario uno storyboard per usare Razor. è sufficiente aggiungere un controllo UIWebView a qualsiasi visualizzazione a livello di codice ed è possibile eseguire il C# rendering di modelli Razor interi nel codice.

Il contenuto della soluzione del modello predefinito per i progetti iPhone e Android è illustrato di seguito:

 ![modelli iPhone e Android](images/image10_428x310.png)

I modelli offrono un'infrastruttura di applicazione pronta per il caricamento di un modello Razor con un oggetto modello di dati, l'elaborazione dell'input utente e la comunicazione di nuovo con l'utente tramite JavaScript.

Di seguito sono riportate le parti importanti della soluzione:

- Contenuto statico, ad esempio il file **Style. CSS** .
- File modello Razor. cshtml come **RazorView. cshtml** .
- Classi del modello a cui viene fatto riferimento nei modelli Razor, ad esempio **ExampleModel.cs** .
- Classe specifica della piattaforma che crea la visualizzazione Web ed esegue il rendering del modello, ad esempio `MainActivity` in Android `iPhoneHybridViewController` e in iOS.

Nella sezione seguente viene illustrato il funzionamento dei progetti.

### <a name="static-content"></a>Contenuto statico

Il contenuto statico include fogli di stile CSS, immagini, file JavaScript o altro contenuto a cui è possibile collegare o a cui fa riferimento un file HTML visualizzato in una visualizzazione Web.

I progetti modello includono un foglio di stile minimo per dimostrare come includere contenuto statico nell'app ibrida. Il foglio di stile CSS viene usato come riferimento nel modello seguente:

```html
<link rel="stylesheet" href="style.css" />
```

È possibile aggiungere qualsiasi foglio di stile e file JavaScript necessario, inclusi i Framework come JQuery.

### <a name="razor-cshtml-templates"></a>Modelli Razor cshtml

Il modello include un file Razor **. cshtml** con codice prescritto che consente di comunicare i dati tra HTML/JavaScript e C#. Questo consentirà di creare app ibride sofisticate che non visualizzano solo i dati di sola lettura dal modello, ma anche di accettare l'input dell'utente nel codice C# HTML e passarlo di nuovo al codice per l'elaborazione o l'archiviazione.

#### <a name="rendering-the-template"></a>Rendering del modello

La `GenerateString` chiamata di su un modello rende il codice HTML pronto per la visualizzazione in una visualizzazione Web. Se il modello utilizza un modello, è necessario fornirlo prima del rendering. In questo diagramma viene illustrato il funzionamento del rendering, non per la risoluzione delle risorse statiche da parte della visualizzazione Web in fase di esecuzione, utilizzando la directory di base specificata per trovare i file specificati.

 ![Diagramma di flusso Razor](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>Chiamata C# di codice dal modello

La comunicazione da una visualizzazione Web di cui C# è stato eseguito il rendering viene eseguita impostando l'URL per la visualizzazione Web e quindi intercettando C# la richiesta in per gestire la richiesta nativa senza ricaricare la visualizzazione Web.

È possibile osservare un esempio di come viene gestito il pulsante di RazorView. Il pulsante presenta il codice HTML seguente:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

La `InvokeCSharpWithFormValues` funzione JavaScript legge tutti i valori dal form HTML e `location.href` imposta per la visualizzazione Web:

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

Questa operazione tenta di passare alla visualizzazione Web a un URL con uno schema personalizzato, ad esempio `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

Quando la visualizzazione Web nativa elabora questa richiesta di navigazione, è possibile intercettarla. In iOS questa operazione viene eseguita gestendo l'evento HandleShouldStartLoad di UIWebView. In Android, è sufficiente sottocreare una sottoclasse dei WebViewClient usati nel form ed eseguire l'override di ShouldOverrideUrlLoading.

Gli elementi interni di questi due intercettori di navigazione sono essenzialmente identici.

Prima di tutto, controllare l'URL che si sta tentando di caricare la visualizzazione Web e, se non inizia con lo`hybrid:`schema personalizzato (), consentire l'esecuzione della navigazione normalmente.

Per lo schema URL personalizzato, tutti gli elementi dell'URL tra lo schema e il "?" nome del metodo da gestire (in questo caso, "UpdateLabel"). Tutti gli elementi nella stringa di query verranno considerati come parametri della chiamata al metodo:

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel`in questo esempio viene eseguita una quantità minima di modifiche di stringa sul parametro TextBox (anteposto "C# dice" alla stringa), quindi viene richiamata la visualizzazione Web.

Dopo aver gestito l'URL, il metodo interrompe la navigazione in modo che la visualizzazione Web non tenti di completare la navigazione all'URL personalizzato.

#### <a name="manipulating-the-template-from-c"></a>Modifica del modello da C\#

La comunicazione con una visualizzazione Web HTML sottoposta a rendering da C# viene eseguita chiamando JavaScript nella visualizzazione Web. In iOS questa operazione viene eseguita chiamando `EvaluateJavascript` in UIWebView:

```csharp
webView.EvaluateJavascript (js);
```

In Android è possibile richiamare JavaScript nella visualizzazione Web caricando JavaScript come URL usando lo `"javascript:"` schema URL:

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>Creazione di un'app realmente ibrida

Questi modelli non usano controlli nativi in ogni piattaforma. l'intera schermata viene riempita con una singola visualizzazione Web.

HTML può essere ideale per la realizzazione di prototipi e visualizzare i tipi di elementi più adatti al Web, ad esempio il testo RTF e il layout reattivo. Tuttavia, non tutte le attività sono adatte per HTML e JavaScript: lo scorrimento attraverso lunghi elenchi di dati, ad esempio, offre prestazioni migliori usando i controlli dell'interfaccia utente nativi (ad esempio UITableView in iOS o ListView in Android).

Le visualizzazioni Web nel modello possono essere facilmente ampliate con i controlli specifici della piattaforma. è sufficiente modificare **file mainstoryboard. Storyboard** in iOS designer o **Resources/layout/Main. aXML** in Android.

### <a name="razortodo-sample"></a>Esempio RazorTodo

Il repository [RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo) contiene due soluzioni separate per mostrare le differenze tra un'app completamente basata su HTML e un'app che combina HTML con controlli nativi:

- **RazorTodo** -app completamente basata su HTML con i modelli Razor.
- **RazorNativeTodo** : USA i controlli di visualizzazione elenco nativi per iOS e Android, ma Visualizza la schermata di modifica con HTML e Razor.

Queste app Novell vengono eseguite su iOS e Android, usando le librerie di classi portabili (classi portabili) per condividere codice comune, ad esempio le classi del database e del modello. I modelli Razor **. cshtml** possono anche essere inclusi nella libreria di classi portabile, in modo che siano facilmente condivisi tra le piattaforme.

Entrambe le app di esempio includono la condivisione di Twitter e le API sintesi vocale dalla piattaforma nativa, dimostrando che le applicazioni ibride con Novell hanno ancora accesso a tutte le funzionalità sottostanti dalle visualizzazioni basate su modelli Razor HTML.

L'app **RazorTodo** usa i modelli Razor HTML per le visualizzazioni elenco e modifica. Ciò significa che è possibile compilare l'app quasi completamente in una libreria di classi portabile condivisa, inclusi il database e i modelli Razor **. cshtml** . Gli screenshot seguenti mostrano le app iOS e Android.

 ![RazorTodo](images/Both_700x290.png)

L'app **RazorNativeTodo** usa un modello Razor HTML per la visualizzazione di modifica, ma implementa un elenco di scorrimento nativo in ogni piattaforma. Questo offre diversi vantaggi, tra cui:

- Prestazioni: i controlli di scorrimento nativi usano la virtualizzazione per garantire lo scorrimento veloce e uniforme anche con elenchi di dati molto lunghi.
- Esperienza nativa: gli elementi dell'interfaccia utente specifici della piattaforma sono facilmente abilitati, ad esempio il supporto per gli indici a scorrimento rapido in iOS e Android.

Un vantaggio fondamentale della creazione di app ibride con Novell è che è possibile iniziare con un'interfaccia utente completamente basata su HTML, ad esempio il primo esempio, e quindi aggiungere funzionalità specifiche della piattaforma quando necessario (come illustrato nel secondo esempio). Di seguito sono illustrate le schermate degli elenchi nativi e le schermate di modifica di Razor HTML in iOS e Android.

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato le funzionalità dei controlli di visualizzazione Web disponibili in iOS e Android che semplificano la creazione di applicazioni ibride.

Viene quindi illustrato il motore di creazione modello Razor e la sintassi che è possibile usare per generare facilmente codice HTML nelle app Novell usando. **cshtml** File modello Razor. Descrive anche i modelli di soluzione Visual Studio per Mac che consentono di iniziare rapidamente a creare applicazioni ibride con Novell.

Infine, sono stati introdotti gli esempi di RazorTodo che illustrano come combinare visualizzazioni Web con interfacce utente e API native.

### <a name="related-links"></a>Collegamenti correlati

- [Esempio RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3-motore di visualizzazione Razor (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [Introduzione alla programmazione Web ASP.NET con la sintassi Razor (Microsoft)](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)
