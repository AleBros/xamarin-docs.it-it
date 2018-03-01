---
title: Viste di compilazione HTML utilizzando i modelli Razor
description: " Utilizzo di una pagina Web a schermo intero per il rendering HTML può essere un modo semplice ed efficace per eseguire il rendering formattazione complessa in un modo multipiattaforma, soprattutto se si dispone già di HTML, Javascript e CSS da un progetto di sito Web."
ms.topic: article
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 7e4d1cab532a5c81da1dfc47df33aa0628c7f6c6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="building-html-views-using-razor-templates"></a>Viste di compilazione HTML utilizzando i modelli Razor

Nell'ambiente di sviluppo per dispositivi mobili il termine "app ibrida" in genere fa riferimento a un'applicazione che visualizza alcune o tutte di relative schermate come pagine HTML in un controllo Visualizzatore di hosting web.

Esistono alcuni ambienti di sviluppo che consentono di compilare app per dispositivi mobili completamente in HTML e Javascript, tuttavia tali App può essere soggetta a problemi di prestazioni durante il tentativo di eseguire un'elaborazione complessa o effetti dell'interfaccia utente e vengono inoltre limitato nella piattaforma funzionalità che possono accedere.

Xamarin offre il meglio di entrambi, soprattutto se si utilizza il motore del modello HTML Razor. Con Xamarin è la flessibilità necessaria per compilare e multipiattaforma basate su modelli HTML viste che utilizzano Javascript e CSS, ma dispongono anche dell'accesso completo per l'API della piattaforma sottostante e l'elaborazione rapida tramite c#.

Questo documento viene illustrato come utilizzare Razor motore del modello di generare visualizzazioni HTML + Javascript + CSS che possono essere utilizzate tra le piattaforme per dispositivi mobili con Xamarin.

## <a name="using-web-views-programmatically"></a>Utilizzo di visualizzazioni Web a livello di programmazione

Prima di sono disponibili informazioni sui Razor in questa sezione viene illustrato come utilizzare visualizzazioni web per visualizzare il contenuto HTML direttamente, in particolare il contenuto HTML che viene generato all'interno di un'app.

Xamarin offre accesso completo alle API della piattaforma sottostante in iOS e Android, pertanto è semplice creare e visualizzare codice HTML con c#. Di seguito è riportata la sintassi di base per ogni piattaforma.

### <a name="ios"></a>iOS

Visualizzazione HTML in un controllo UIWebView in xamarin. IOS accetta inoltre solo poche righe di codice:

```
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

Vedere il [iOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/) recipe per ulteriori informazioni sull'utilizzo del controllo UIWebView.

### <a name="android"></a>Android

Visualizzazione HTML in un controllo WebView usando xamarin avviene solo poche righe di codice:

```
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

Vedere il [WebView Android](http://docs.xamarin.com/recipes/android/controls/webview/) recipe per ulteriori informazioni sull'utilizzo del controllo WebView.

### <a name="specifying-the-base-directory"></a>Specifica la Directory di Base

In entrambe le piattaforme è un parametro che specifica la directory di base per la pagina HTML. Questo è il percorso nel file system del dispositivo che viene utilizzato per risolvere i riferimenti relativi a risorse quali immagini e file CSS. Ad esempio, i tag come


    <link rel="stylesheet" href="style.css" />
    <img src="monkey.jpg" />
    <script type="text/javascript" src="jscript.js">


Fare riferimento a questi file: **style.css**, **monkey.jpg** e **js**. L'impostazione di directory di base indica la visualizzazione web in cui questi file si trovano in modo possono essere caricati nella pagina.

#### <a name="ios"></a>iOS

L'output del modello viene eseguito il rendering in iOS con il codice C# # seguente:

```
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

La directory di base viene specificata come `NSBundle.MainBundle.BundleUrl` che fa riferimento alla directory in cui l'applicazione viene installata in. Tutti i file di **risorse** cartella vengono copiati in questa posizione, ad esempio il **style.css** file illustrato di seguito:

 ![soluzione iPhoneHybrid](images/image1_240x163.png)

L'azione di compilazione per tutti i file di contenuto statici deve essere **BundleResource**:

 ![azione di compilazione progetto iOS: BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android richiede inoltre una directory di base deve essere passato come parametro quando stringhe html vengono visualizzate in una visualizzazione web.

```
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

La stringa speciale **file:///android_asset/** fa riferimento alla cartella Assets Android nell'app, illustrato di seguito che contiene il **style.css** file.

 ![Soluzione AndroidHybrid](images/image3_240x167.png)

L'azione di compilazione per tutti i file di contenuto statici deve essere **AndroidAsset**.

 ![Azione di compilazione progetto Android: AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>Chiamata di C# # da HTML e Javascript

Quando una pagina html viene caricata in una visualizzazione web, considera i collegamenti e il form come se la pagina è stata caricata da un server. Ciò significa che se l'utente fa clic su un collegamento o un form invia la visualizzazione web tenterà di passare alla destinazione specificata.

Se il collegamento a un server esterno (ad esempio google.com) la visualizzazione web tenterà di caricare il sito Web esterno (presupponendo che sia disponibile una connessione internet).

```
<a href="http://google.com/">Google</a>
```

Se il collegamento relativo la visualizzazione web tenterà di caricare tale contenuto dalla directory di base. Ovviamente alcuna connessione di rete non è necessaria per questo scopo, in quanto il contenuto viene archiviato nell'app sul dispositivo.

```
<a href="somepage.html">Local content</a>
```

Azioni modulo seguono le stesse regole.

```
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

Non si intende ospitare un server web nel client. Tuttavia, è possibile utilizzare le stesse tecniche di comunicazione server utilizzate nei modelli di progettazione reattiva odierna per chiamare i servizi tramite HTTP GET e gestire le risposte in modo asincrono con la creazione di Javascript (o chiamante Javascript già ospitata nella visualizzazione web). Ciò consente di passare facilmente i dati dal codice HTML nel codice C# # per l'elaborazione, quindi visualizzare che i risultati nuovamente nella pagina HTML.

IOS e Android forniscono un meccanismo per il codice dell'applicazione intercettare questi eventi di spostamento, in modo che il codice di app può rispondere (se richiesto). Questa funzionalità è essenziale per la creazione di applicazioni ibride perché consente il codice nativo di interagire con la visualizzazione web.

#### <a name="ios"></a>iOS

L'evento ShouldStartLoad nella visualizzazione web in iOS può essere sottoposto a override per consentire il codice dell'applicazione gestire una richiesta di spostamento (ad esempio, fare clic su un collegamento). Tutte le informazioni di fornire i parametri del metodo

```
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

e quindi assegnare il gestore dell'evento:

```
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

In Android semplicemente sottoclasse WebViewClient e quindi implementare il codice per rispondere alla richiesta di spostamento.

```
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, string url) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

e quindi impostare i client nella visualizzazione web:

```
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>Chiamata di Javascript in C#

Oltre alla fornitura di una visualizzazione web per caricare una nuova pagina HTML, C# # possibile anche eseguire codice Javascript all'interno della pagina attualmente visualizzata. Interi blocchi di codice Javascript possono essere creati utilizzando le stringhe di C# # ed eseguiti, o è possibile elaborare a Javascript già disponibile nella pagina tramite chiamate al metodo `script` tag.

#### <a name="android"></a>Android

Creare il codice Javascript da eseguire e quindi prefisso "javascript:" e impostare la visualizzazione web di caricare la stringa:

```
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

visualizzazioni web iOS offrono un metodo in particolare per chiamare Javascript:

```
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>Riepilogo

In questa sezione è state introdotte le funzionalità dei controlli di visualizzazione web in Android e iOS che consentono di compilare applicazioni ibride con Xamarin, tra cui:

-  La possibilità di caricare HTML da stringhe generate nel codice,
-  La possibilità di fare riferimento a file locali (CSS, Javascript, immagini o altri file HTML)
-  La possibilità di intercettare le richieste di navigazione nel codice C# #
-  La possibilità di chiamare Javascript dal codice C# #.


La sezione successiva introduce Razor, che rende più semplice creare il codice HTML da usare in App ibride.

## <a name="what-is-razor"></a>Che cos'è Razor?

Razor è un motore del modello che è stato introdotto in ASP.NET MVC, originariamente per l'esecuzione nel server e generare HTML per essere disponibile per i browser web.

Il motore del modello Razor estende la sintassi HTML standard con C# # in modo che sia possibile esprimere il layout e incorporare con facilità i fogli di stile CSS e Javascript. Il modello può fare riferimento a una classe di modello, che può essere qualsiasi tipo personalizzato e le cui proprietà sono accessibili direttamente dal modello. Uno dei vantaggi principali è la possibilità di combinare facilmente sintassi HTML e C# #.

Modelli Razor non sono limitati all'utilizzo sul lato server, possono essere inclusi anche in App Xamarin. Utilizzo modelli Razor nonché la possibilità per lavorare con le visualizzazioni web a livello di programmazione consente alle applicazioni di sofisticate ibrida multipiattaforma essere compilata con Xamarin.

### <a name="razor-template-basics"></a>Nozioni fondamentali sui modelli Razor

File di modello Razor hanno un **. cshtml** estensione di file. Possono essere aggiunti a un progetto Xamarin dalla sezione del modello di testo di **nuovo File** finestra di dialogo:

 ![Nuovo File - modello Razor](images/image5_400x201.png)

Un semplice modello Razor ( **RazorView.cshtml**) è illustrato di seguito.

```
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

Notare le differenze seguenti da un file HTML regolari:

-  Il `@` simbolo ha un significato speciale nei modelli Razor: indica che è la seguente espressione C# # da valutare.
- `@model` direttiva viene sempre visualizzata come prima riga di un file di modello Razor.
-  Il `@model` direttiva deve essere seguita da un tipo. In questo esempio una stringa semplice vengono passata al modello, ma potrebbe trattarsi di qualsiasi classe personalizzata.
-  Quando `@Model` viene fatto riferimento in tutto il modello, fornisce un riferimento all'oggetto passato al modello quando viene generato (in questo esempio sarà una stringa).
-  L'IDE genera automaticamente la classe parziale per i modelli (file con il **. cshtml** estensione). È possibile visualizzare questo codice, ma non deve essere modificato.
 ![RazorView.cshtml](images/image6_125x34.png) la classe parziale è denominata RazorView corrispondere al nome di file di modello. cshtml. È il nome utilizzato per fare riferimento al modello nel codice C# #.
- `@using` istruzioni possono anche essere incluse nella parte superiore di un modello Razor per includere ulteriori spazi dei nomi.


L'output HTML finale può quindi essere generato con il seguente codice C# #. Si noti che è specificare il modello per essere una stringa "Hello World" che verranno incorporate nell'output del modello sottoposto a rendering.

```
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

Di seguito è riportato l'output visualizzato in una visualizzazione web nel simulatore ed emulatore Android iOS:

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>Ulteriori informazioni sulla sintassi Razor

In questa sezione a che si intende introdurre alcune sintassi Razor di base che consentono di iniziare utilizzarlo. Negli esempi di questa sezione popolano la classe seguente con i dati e visualizzarli utilizzando Razor:

```
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

Tutti gli esempi usano il seguente codice di inizializzazione di dati

```
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>Visualizzazione delle proprietà di modello

Quando il modello è una classe con le proprietà, essi sono facilmente a cui fa riferimento nel modello Razor come illustrato in questo modello di esempio:

```
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

Questo può essere visualizzato in una stringa utilizzando il codice seguente:

```
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

L'output finale è illustrato di seguito in una visualizzazione web nel simulatore ed emulatore Android iOS:

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>Istruzioni di C# #

C# # più complesse possono essere inclusi nel modello, ad esempio gli aggiornamenti di proprietà di modello e il calcolo dell'età in questo esempio:

```
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

È possibile scrivere una riga singola C# # espressioni complesse (ad esempio l'età di formattazione) racchiudendo il codice con `@()`.

È possibile creare più istruzioni C# # racchiudendo con `@{}`.

#### <a name="if-else-statements"></a>Istruzioni if-else

Rami del codice possono essere espresso con `@if` come illustrato in questo esempio di modello.

```
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

Ciclo costrutti come `foreach` possono anche essere aggiunti. Il `@` prefisso può essere utilizzato in una variabile del ciclo ( `@food` in questo caso) per eseguirne il rendering in HTML.

```
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

L'output del modello precedente viene illustrato in esecuzione nel simulatore ed emulatore Android iOS:

 ![Rupert X Monkey](images/image9_520x277.png)

In questa sezione è illustrati i concetti di base dell'utilizzo di modelli Razor per il rendering di visualizzazioni di sola lettura semplici. La sezione successiva viene illustrato come creare applicazioni più complete utilizzando Razor che possono accettare l'input dell'utente e l'interazione tra Javascript in visualizzazione HTML e c#.

## <a name="using-razor-templates-with-xamarin"></a>Utilizzo di modelli Razor con Xamarin

In questa sezione viene illustrato come utilizzare compilazione applicazione ibrida con i modelli di soluzione in Visual Studio per Mac. Sono disponibili tre modelli di **File > Nuovo > soluzione...**  finestra:

-  Android > App > applicazione WebView Android
-  iOS > App > applicazione WebView
- Progetto ASP.NET MVC



Il **nuova soluzione** finestra è simile al seguente per iPhone e i progetti Android: la descrizione della soluzione a destra illustra il supporto per il motore del modello Razor.

 ![Creazione di soluzioni Android e iPhone](images/image13_1139x959.png)

Si noti che è possibile aggiungere facilmente un **. cshtml** modello Razor *qualsiasi* esistenti progetto Xamarin, non è necessario usare questi modelli di soluzione. progetti iOS non richiedono uno Storyboard da utilizzare Razor. aggiungere semplicemente un controllo UIWebView a una visualizzazione a livello di codice e si possono eseguire il rendering di modelli Razor intera nel codice C# #.

Il contenuto di soluzione del modello predefinito per iPhone e i progetti Android è le seguenti:

 ![iPhone e modelli di Android](images/image10_428x310.png)

I modelli consentono di infrastruttura dell'applicazione ready to go per caricare un modello Razor con un oggetto modello di dati, elaborare l'input dell'utente e comunicare all'utente tramite Javascript.

Le parti importanti della soluzione sono:

-  Contenuto statico, ad esempio il **style.css** file.
-  Ad esempio i file di modello. cshtml Razor **RazorView.cshtml** .
-  Classi a cui fa riferimento nei modelli Razor, ad esempio modello **ExampleModel.cs** .
-  La classe specifica della piattaforma che crea la visualizzazione web e viene eseguito il rendering del modello, ad esempio il `MainActivity` in Android e `iPhoneHybridViewController` in iOS.


Nella sezione seguente viene illustrato il funzionamento dei progetti.

### <a name="static-content"></a>Contenuto statico

Contenuto statico include fogli di stile CSS, immagini, file Javascript o altro contenuto che può essere collegato da o a un file HTML visualizzato in una visualizzazione web a cui fa riferimento.

I progetti del modello includono un foglio di stile minimo per dimostrare come includere contenuto statico in app ibride. Il foglio di stile CSS fa riferimento nel modello simile al seguente:

```
<link rel="stylesheet" href="style.css" />
```

È possibile aggiungere qualsiasi foglio di stile e i file Javascript necessari, compresi Framework come JQuery.

### <a name="razor-cshtml-templates"></a>Razor cshtml modelli

Il modello include un Razor **. cshtml** file che contiene già il codice per agevolare la comunicazione dati tra HTML/Javascript e c#. Ciò consente di compilazione di App ibrida sofisticato che non solo la visualizzazione dei dati di sola lettura dal modello, ma anche accettare l'input dell'utente nel codice HTML e passarlo al codice C# # per l'elaborazione o di archiviazione.

#### <a name="rendering-the-template"></a>Il rendering del modello

La chiamata di `GenerateString` su un modello viene eseguito il rendering HTML pronto per la visualizzazione in una visualizzazione web. Se il modello utilizza un modello, quindi deve essere specificato prima del rendering. Questo diagramma vengono illustrati il rendering funzionamento – non risolti risorse statiche per la visualizzazione web in fase di esecuzione utilizzando la directory di base fornita per individuare i file specificati.

 ![Diagramma di flusso Razor](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>Chiamata di codice C# # dal modello

Comunicazione da una visualizzazione web visualizzabile chiamare nuovamente C# # viene eseguita impostando l'URL per la visualizzazione web, e quindi intercettare la richiesta di C# # per gestire la richiesta nativa senza ricaricare la visualizzazione web.

Un esempio può essere visualizzato il pulsante del RazorView viene gestito in modo. Il pulsante ha il codice HTML seguente:

```
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

Il `InvokeCSharpWithFormValues` funzione Javascript legge tutti i valori di HTML Form e imposta il `location.href` per la visualizzazione web:

```
location.href = "hybrid:" + elm.name + "?" + qs;
```

Si tenta di spostarsi nella visualizzazione web a un URL con uno schema personalizzato che sono state apportate backup (`hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

Quando la visualizzazione web nativi elabora la richiesta di spostamento, si ha la possibilità di intercettarla. IOS, questa operazione viene eseguita mediante la gestione HandleShouldStartLoad evento del UIWebView. In Android, è sufficiente sottoclasse il WebViewClient utilizzato nel modulo ed eseguire l'override ShouldOverrideUrlLoading.

Le caratteristiche interne di questi due intercettori di navigazione sono essenzialmente lo stesso.

È in primo luogo, verificare che l'URL di visualizzazione web sta tentando di caricare, e se non si avvia con lo schema personalizzato (`hybrid:`), si consente la navigazione come di consueto.

Per lo schema URL personalizzato, si considera tutti gli elementi nell'URL tra lo schema e "?" come nome del metodo deve essere gestito (in questo caso, "UpdateLabel"). Tutti gli elementi nella stringa di query sarà considerato come parametri alla chiamata al metodo:

```
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

UpdateLabel in questo esempio viene eseguita una quantità minima di modifica di stringhe per il parametro di tipo casella di testo (anteponendo "afferma di C# # '" nella stringa) e quindi richiama la visualizzazione web.

Alla fine della gestione degli URL, la navigazione è interrotto in modo che la visualizzazione web non tenta di completare la navigazione per l'URL personalizzato.

#### <a name="manipulating-the-template-from-c"></a>Modifica il modello da c#

Comunicazione con una visualizzazione web HTML da C# # viene eseguita chiamando Javascript nella visualizzazione web. In iOS, questa operazione viene eseguita chiamando `EvaluateJavascript` sul UIWebView:

```
webView.EvaluateJavascript (js);
```

In Android, Javascript è possibile richiamare la visualizzazione web caricando il codice Javascript come URL usando il `"javascript:"` lo schema dell'URL:

```
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>Effettua un'app realmente ibrido

Questi modelli non apportare usare di controlli nativi in ciascuna piattaforma, l'intero schermo viene riempito con una visualizzazione sola web.

HTML può essere elevato per la creazione di prototipi, e la visualizzazione di tipi di elementi web è ottimale, ad esempio testo con formattazione e layout reattivo. Tuttavia, non tutte le attività sono adatti per HTML e Javascript – scorrimento lunghi elenchi di dati, ad esempio, esegue meglio utilizzando controlli dell'interfaccia utente nativi, ad esempio (UITableView in iOS) o ListView in Android.

Visualizzazioni web nel modello possono essere ampliate facilmente con controlli specifici della piattaforma: modifica semplicemente il **Mainstoryboard** nella finestra di progettazione iOS o **Resources/layout/Main.axml** in Android.

### <a name="razortodo-sample"></a>RazorTodo Sample

Il [RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo) repository contiene due soluzioni separate per visualizzare le differenze tra un'applicazione completamente basati su HTML e un'app che combina HTML con controlli nativi:

-  **RazorTodo** -app basate su HTML completamente utilizzando i modelli Razor.
-  **RazorNativeTodo** : Usa controlli di visualizzazione elenco native per iOS e Android, ma visualizza la schermata di modifica con HTML e Razor.


Queste App Xamarin eseguiti su iOS e Android, che utilizzano le librerie di classi portabili (PCLs) per condividere il codice, ad esempio le classi di database e il modello. Razor **. cshtml** modelli inoltre possono essere inclusi nella libreria di classi Portabile, in modo facilmente condiviso tra le piattaforme.

Entrambe le app di esempio incorporano Twitter condivisione e le API sintesi vocale da piattaforma nativa, dimostrando che applicazioni ibride con Xamarin abbiano accesso a tutte le funzionalità sottostanti da visualizzazioni basate su modello HTML Razor.

Il **RazorTodo** app Usa modelli HTML Razor per le visualizzazioni elenco e modifica. Ciò significa che è possibile compilare l'applicazione quasi completamente in una libreria di classi portabile condiviso (incluso il database e **. cshtml** modelli Razor). Schermate riportate di seguito mostrano l'App iOS e Android.

 ![RazorTodo](images/Both_700x290.png)

Il **RazorNativeTodo** app utilizza un modello HTML Razor per la visualizzazione di modifica, ma implementa un elenco a discesa nativo in ciascuna piattaforma. Questo fornisce una serie di vantaggi tra cui:

-  Prestazioni - controlli nativi di scorrimento di utilizzare la virtualizzazione per garantire lo scorrimento rapido e senza problemi anche con molto lunghi elenchi di dati.
-  Esperienza nativa - elementi di interfaccia utente specifiche della piattaforma sono facilmente attivate, ad esempio il supporto di indice lo scorrimento rapido in iOS e Android.


Compilazione di applicazioni ibride con Xamarin un vantaggio chiave è che è possibile iniziare con un'interfaccia utente basata su HTML completamente (ad esempio nel primo esempio) e quindi aggiungere le funzionalità specifiche della piattaforma quando richiesto (come illustrato nell'esempio secondo). Le schermate di elenco nativo e l'HTML Razor modificare schermate in entrambi iOS e Android sono illustrati di seguito.

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>Riepilogo

Questo articolo è stato spiegato le funzionalità dei controlli di visualizzazione web disponibile in iOS e Android che semplificano la compilazione di applicazioni ibride.

Quindi è stato illustrato il motore del modello Razor e la sintassi che può essere utilizzata per generare HTML facilmente in App Xamarin mediante. **cshtml** file modello Razor. Descritta anche di Visual Studio per Mac modelli di soluzioni che consentono di iniziare a creare applicazioni ibride con Xamarin.

Infine, ha introdotto RazorTodo gli esempi che illustrano come combinare le visualizzazioni web API e interfacce utente native.

### <a name="related-links"></a>Collegamenti correlati

- [Esempio RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3 - motore di visualizzazione Razor (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [Introduzione alla programmazione Web ASP.NET utilizzando la sintassi Razor (Microsoft)](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)