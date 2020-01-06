---
title: Personalizzazione di una visualizzazione WebView
description: Una visualizzazione Web Novell. Forms è una vista che Visualizza il contenuto Web e HTML nell'app. Questo articolo illustra come creare un renderer personalizzato che estende la visualizzazione WebView per consentire C# la chiamata del codice da JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2019
ms.openlocfilehash: 46d0b245246d9e93040cd8591dab8ed3a816268d
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487009"
---
# <a name="customizing-a-webview"></a>Personalizzazione di una visualizzazione WebView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_Un `WebView` Novell. Forms è una vista che Visualizza il contenuto Web e HTML nell'app. Questo articolo illustra come creare un renderer personalizzato che estende la `WebView` per consentire C# la chiamata del codice da JavaScript._

A ogni vista di Xamarin.Forms è associato un renderer per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [`WebView`](xref:Xamarin.Forms.WebView) viene sottoposto a rendering da un'applicazione Novell. Forms in iOS, viene creata un'istanza della classe `WkWebViewRenderer`, che a sua volta crea un'istanza di un controllo `WkWebView` nativo. Nella piattaforma Android la classe `WebViewRenderer` crea un'istanza di un controllo `WebView` nativo. Nella piattaforma UWP (Universal Windows Platform) la classe `WebViewRenderer` crea un'istanza di un controllo `WebView` nativo. Per altre informazioni sulle classi del renderer e dei controlli nativi di cui Xamarin.Forms controlla il mapping, vedere [Classi di base e controlli nativi del renderer](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra la classe [`View`](xref:Xamarin.Forms.View) e i controlli nativi corrispondenti che la implementano:

![](hybridwebview-images/webview-classes.png "Relationship Between the WebView Class and its Implementing Native Classes")

Il processo di rendering può essere usato per implementare le personalizzazioni della piattaforma creando un renderer personalizzato per un [`WebView`](xref:Xamarin.Forms.WebView) in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#create-the-hybridwebview) il controllo personalizzato `HybridWebView`.
1. [Usare](#consume-the-hybridwebview)`HybridWebView` da Xamarin.Forms.
1. [Creare](#create-the-custom-renderer-on-each-platform) il renderer personalizzato per `HybridWebView` in ogni piattaforma.

Ogni elemento verrà ora discusso a sua volta per implementare un renderer `HybridWebView` che migliora l' [`WebView`](xref:Xamarin.Forms.WebView) Novell. Forms per consentire C# la chiamata del codice da JavaScript. L'istanza di `HybridWebView` verrà usata per visualizzare una pagina HTML in cui viene chiesto all'utente di immettere il proprio nome. Quindi, quando l'utente fa clic su un pulsante HTML, una funzione JavaScript richiama un elemento `Action` di C# che visualizza una finestra popup contenente il nome dell'utente.

Per ulteriori informazioni sul processo per richiamare C# da JavaScript, vedere [Invoke C# from JavaScript](#invoke-c-from-javascript). Per ulteriori informazioni sulla pagina HTML, vedere [creare la pagina Web](#create-the-web-page).

> [!NOTE]
> Un [`WebView`](xref:Xamarin.Forms.WebView) può richiamare una funzione JavaScript da C#e restituire qualsiasi risultato al codice chiamante C# . Per ulteriori informazioni, vedere la pagina relativa alla [chiamata a JavaScript](~/xamarin-forms/user-interface/webview.md#invoking-javascript).

## <a name="create-the-hybridwebview"></a>Creare il HybridWebView

È possibile creare il `HybridWebView` controllo personalizzato creando una sottoclasse della classe [`WebView`](xref:Xamarin.Forms.WebView) :

```csharp
public class HybridWebView : WebView
{
    Action<string> action;

    public static readonly BindableProperty UriProperty = BindableProperty.Create(
        propertyName: "Uri",
        returnType: typeof(string),
        declaringType: typeof(HybridWebView),
        defaultValue: default(string));

    public string Uri
    {
        get { return (string)GetValue(UriProperty); }
        set { SetValue(UriProperty, value); }
    }

    public void RegisterAction(Action<string> callback)
    {
        action = callback;
    }

    public void Cleanup()
    {
        action = null;
    }

    public void InvokeAction(string data)
    {
        if (action == null || data == null)
        {
            return;
        }
        action.Invoke(data);
    }
}
```

Il controllo personalizzato `HybridWebView` viene creato nel progetto di libreria .NET Standard e definisce l'API seguente per il controllo:

- Una proprietà `Uri` che specifica l'indirizzo della pagina Web da caricare.
- Un metodo `RegisterAction` che registra un elemento `Action` con il controllo. L'azione registrata verrà richiamata da JavaScript all'interno del file HTML a cui si fa riferimento attraverso la proprietà `Uri`.
- Un metodo `CleanUp` che rimuove il riferimento all'elemento `Action` registrato.
- Un metodo `InvokeAction` che richiama l'elemento `Action` registrato. Questo metodo verrà chiamato da un renderer personalizzato in ogni progetto di piattaforma.

## <a name="consume-the-hybridwebview"></a>Utilizzare HybridWebView

Per fare riferimento al controllo personalizzato `HybridWebView` in XAML nel progetto di libreria .NET Standard, è possibile dichiarare uno spazio dei nomi per il percorso e usare il prefisso dello spazio dei nomi nel controllo personalizzato. Nell'esempio di codice riportato di seguito viene illustrato come il controllo personalizzato `HybridWebView` può essere usato da una pagina XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,40,0,0">
    <local:HybridWebView x:Name="hybridWebView"
                         Uri="index.html" />
</ContentPage>

```

Il prefisso dello spazio dei nomi `local` può avere qualsiasi nome. I valori `clr-namespace` e `assembly` devono tuttavia corrispondere ai dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene usato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come il controllo personalizzato `HybridWebView` può essere usato da una pagina C#:

```csharp
public HybridWebViewPageCS()
{
    var hybridWebView = new HybridWebView
    {
        Uri = "index.html"
    };
    // ...
    Padding = new Thickness(0, 40, 0, 0);
    Content = hybridWebView;
}
```

L'istanza di `HybridWebView` verrà usata per visualizzare un controllo Web nativo in ogni piattaforma. È `Uri` proprietà è impostata su un file HTML archiviato in ogni progetto di piattaforma e che verrà visualizzato dal controllo Web nativo. Il codice HTML sottoposto a rendering chiede all'utente di immettere il proprio nome, con una funzione JavaScript che richiama un elemento `Action` di C# in risposta al clic su un pulsante HTML.

`HybridWebViewPage` registra l'azione da richiamare da JavaScript, come illustra l'esempio di codice seguente:

```csharp
public partial class HybridWebViewPage : ContentPage
{
    public HybridWebViewPage()
    {
        // ...
        hybridWebView.RegisterAction(data => DisplayAlert("Alert", "Hello " + data, "OK"));
    }
}
```

Questa azione chiama il metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) per visualizzare un popup modale che presenta il nome immesso nella pagina HTML visualizzata dall'istanza di `HybridWebView`.

È ora possibile aggiungere un renderer personalizzato a ogni progetto di applicazione per migliorare i controlli Web della piattaforma C# consentendo di richiamare il codice da JavaScript.

## <a name="create-the-custom-renderer-on-each-platform"></a>Creare il renderer personalizzato in ogni piattaforma

Il processo di creazione della classe di renderer personalizzato è il seguente:

1. Creare una sottoclasse della classe `WkWebViewRenderer` in iOS e la classe `WebViewRenderer` in Android e UWP, che esegue il rendering del controllo personalizzato.
1. Eseguire l'override del metodo `OnElementChanged` che esegue il rendering della [`WebView`](xref:Xamarin.Forms.WebView) e della logica di scrittura per personalizzarla. Questo metodo viene chiamato quando viene creato un oggetto `HybridWebView`.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzata o *AssemblyInfo.cs*per specificare che verrà usato per eseguire il rendering del controllo personalizzato Novell. Forms. L'attributo viene usato per registrare il renderer personalizzato con Xamarin.Forms.

> [!NOTE]
> Per la maggior parte degli elementi di Xamarin.Forms, l'indicazione di un renderer personalizzato in ogni progetto della piattaforma è facoltativa. Se un renderer personalizzato non è registrato, verrà usato il renderer predefinito per la classe di base del controllo. I renderer personalizzati sono tuttavia necessari in ogni progetto della piattaforma quando si esegue il rendering di un elemento [View](xref:Xamarin.Forms.View).

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](hybridwebview-images/solution-structure.png "HybridWebView Custom Renderer Project Responsibilities")

Il rendering del controllo personalizzato `HybridWebView` viene eseguito dalle classi renderer della piattaforma, che derivano dalla classe `WkWebViewRenderer` in iOS e dalla classe `WebViewRenderer` in Android e UWP. In questo modo, ogni `HybridWebView` controllo personalizzato viene sottoposto a rendering con i controlli Web nativi, come illustrato nelle schermate seguenti:

![](hybridwebview-images/screenshots.png "HybridWebView on each Platform")

Le classi `WkWebViewRenderer` e `WebViewRenderer` espongono il metodo `OnElementChanged`, che viene chiamato quando viene creato il controllo personalizzato Novell. Forms per eseguire il rendering del controllo Web nativo corrispondente. Questo metodo accetta un `VisualElementChangedEventArgs` parametro che contiene le proprietà `OldElement` e `NewElement`. Queste proprietà rappresentano rispettivamente l'elemento Xamarin.Forms a cui il renderer *era* collegato e l'elemento Xamarin.Forms a cui il renderer *è* collegato. Nell'applicazione di esempio la proprietà `OldElement` sarà `null` e la proprietà `NewElement` conterrà un riferimento all'istanza di `HybridWebView`.

Una versione sottoposta a override del metodo `OnElementChanged`, in ogni classe renderer della piattaforma, è la posizione in cui eseguire la personalizzazione del controllo Web nativo. Un riferimento al controllo Novell. Forms di cui è in corso il rendering può essere ottenuto tramite la proprietà `Element`.

Ogni classe del renderer personalizzato è decorata con un attributo `ExportRenderer` che registra il renderer in Xamarin.Forms. L'attributo accetta due parametri: il nome del tipo di controllo personalizzato Xamarin.Forms di cui viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

Nelle sezioni seguenti viene illustrata la struttura della pagina Web caricata da ogni controllo Web nativo, il processo per C# richiamare da JavaScript e l'implementazione di in ogni classe renderer personalizzata della piattaforma.

### <a name="create-the-web-page"></a>Creare la pagina Web

L'esempio di codice seguente illustra la pagina Web che verrà visualizzata dal controllo personalizzato `HybridWebView`:

```html
<html>
<body>
    <script src="http://code.jquery.com/jquery-2.1.4.min.js"></script>
    <h1>HybridWebView Test</h1>
    <br />
    Enter name: <input type="text" id="name">
    <br />
    <br />
    <button type="button" onclick="javascript: invokeCSCode($('#name').val());">Invoke C# Code</button>
    <br />
    <p id="result">Result:</p>
    <script type="text/javascript">function log(str) {
            $('#result').text($('#result').text() + " " + str);
        }

        function invokeCSCode(data) {
            try {
                log("Sending Data:" + data);
                invokeCSharpAction(data);
            }
            catch (err) {
                log(err);
            }
        }</script>
</body>
</html>
```

La pagina Web consente all'utente di immettere il proprio nome in un elemento `input` e indica un elemento `button` che richiama il codice C# quando viene selezionato. Il processo per eseguire questa operazione è il seguente:

- Quando l'utente fa clic sull'elemento `button`, viene chiamata la funzione JavaScript `invokeCSCode`, con il valore dell'elemento `input` passato alla funzione.
- La funzione `invokeCSCode` chiama la funzione `log` per visualizzare i dati inviati all'elemento `Action` di C#. Chiama quindi il metodo `invokeCSharpAction` per richiamare l'elemento `Action` di C#, passando il parametro ricevuto dall'elemento `input`.

La funzione JavaScript `invokeCSharpAction` non è definita nella pagina Web e verrà inserita al suo interno da ogni renderer personalizzato.

In iOS questo file HTML si trova nella cartella del contenuto del progetto della piattaforma, con l'azione di compilazione **BundleResource**. In Android questo file HTML si trova nella cartella degli asset/del contenuto del progetto della piattaforma, con l'azione di compilazione **AndroidAsset**.

### <a name="invoke-c-from-javascript"></a>Richiama C# da JavaScript

Il processo per richiamare C# da JavaScript è identico in ogni piattaforma:

- Il renderer personalizzato crea un controllo Web nativo e carica il file HTML specificato dalla proprietà `HybridWebView.Uri`.
- Dopo che la pagina Web è stata caricata, il renderer personalizzato inserisce la funzione JavaScript `invokeCSharpAction` nella pagina Web.
- Quando l'utente immette il proprio nome e fa clic sull'elemento HTML `button`, viene richiamata la funzione `invokeCSCode`, che a sua volta richiama la funzione `invokeCSharpAction`.
- La funzione `invokeCSharpAction` richiama un metodo nel renderer personalizzato, che a sua volta richiama il metodo `HybridWebView.InvokeAction`.
- Il metodo `HybridWebView.InvokeAction` richiama l'elemento `Action` registrato.

Le sezioni seguenti spiegano come viene implementato questo processo in ogni piattaforma.

### <a name="create-the-custom-renderer-on-ios"></a>Creare il renderer personalizzato in iOS

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : WkWebViewRenderer, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        public HybridWebViewRenderer() : this(new WKWebViewConfiguration())
        {
        }

        public HybridWebViewRenderer(WKWebViewConfiguration config) : base(config)
        {
            userController = config.UserContentController;
            var script = new WKUserScript(new NSString(JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
            userController.AddUserScript(script);
            userController.AddScriptMessageHandler(this, "invokeAction");
        }

        protected override void OnElementChanged(VisualElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                userController.RemoveAllUserScripts();
                userController.RemoveScriptMessageHandler("invokeAction");
                HybridWebView hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }

            if (e.NewElement != null)
            {
                string filename = Path.Combine(NSBundle.MainBundle.BundlePath, $"Content/{((HybridWebView)Element).Uri}");
                LoadRequest(new NSUrlRequest(new NSUrl(filename, false)));
            }
        }

        public void DidReceiveScriptMessage(WKUserContentController userContentController, WKScriptMessage message)
        {
            ((HybridWebView)Element).InvokeAction(message.Body.ToString());
        }
    }
}
```

La classe `HybridWebViewRenderer` carica la pagina Web specificata nella proprietà `HybridWebView.Uri` in un controllo [`WKWebView`](xref:WebKit.WKWebView) nativo e la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web. Dopo che l'utente immette il proprio nome e fa clic sull'elemento HTML `button`, la funzione JavaScript `invokeCSharpAction` viene eseguita, con il metodo `DidReceiveScriptMessage` chiamato dopo che viene ricevuto un messaggio dalla pagina Web. A sua volta, questo metodo richiama il metodo `HybridWebView.InvokeAction`, che richiama l'azione registrata per visualizzare l'elemento popup.

Questa funzionalità si rende disponibile come indicato di seguito:

- Il costruttore renderer crea un oggetto `WkWebViewConfiguration` e recupera il relativo oggetto [`WKUserContentController`](xref:WebKit.WKUserContentController) . L'oggetto `WkUserContentController` consente di inviare messaggi e inserire script utente in una pagina Web.
- Il costruttore renderer crea un oggetto [`WKUserScript`](xref:WebKit.WKUserScript) , che inserisce la funzione JavaScript `invokeCSharpAction` nella pagina Web dopo il caricamento della pagina Web.
- Il costruttore renderer chiama il metodo [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) per aggiungere l'oggetto [`WKUserScript`](xref:WebKit.WKUserScript) al controller di contenuto.
- Il costruttore renderer chiama il metodo [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) per aggiungere un gestore di messaggi di script denominato `invokeAction` all'oggetto [`WKUserContentController`](xref:WebKit.WKUserContentController) , che determinerà la definizione della `window.webkit.messageHandlers.invokeAction.postMessage(data)` funzione JavaScript in tutti i frame in tutte le istanze `WebView` che usano l'oggetto `WKUserContentController`.
- Se il renderer personalizzato è associato a un nuovo elemento di Xamarin.Forms:
  - Il metodo [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) carica il file HTML specificato dalla proprietà `HybridWebView.Uri`. Il codice specifica che il file è archiviato nella cartella `Content` del progetto. Quando viene visualizzata la pagina Web, la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web.
- Quando l'elemento a cui è associato il renderer cambia:
  - Le risorse vengono rilasciate.

> [!NOTE]
> La classe `WKWebView` è supportata solo in iOS 8 e versioni successive.

È anche necessario aggiornare **Info.plist** in modo da includere i valori seguenti:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

### <a name="create-the-custom-renderer-on-android"></a>Creare il renderer personalizzato in Android

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : WebViewRenderer
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<WebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                ((HybridWebView)Element).Cleanup();
            }
            if (e.NewElement != null)
            {
                Control.SetWebViewClient(new JavascriptWebViewClient($"javascript: {JavascriptFunction}"));
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{((HybridWebView)Element).Uri}");
            }
        }
    }
}
```

La classe `HybridWebViewRenderer` carica la pagina Web specificata nella proprietà `HybridWebView.Uri` in un controllo [`WebView`](xref:Android.Webkit.WebView) nativo e la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web, al termine del caricamento della pagina Web, con override di `OnPageFinished` nella classe `JavascriptWebViewClient`:

```csharp
public class JavascriptWebViewClient : WebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(string javascript)
    {
        _javascript = javascript;
    }

    public override void OnPageFinished(WebView view, string url)
    {
        base.OnPageFinished(view, url);
        view.EvaluateJavascript(_javascript, null);
    }
}
```

Dopo che l'utente immette il proprio nome e fa clic sull'elemento HTML `button`, viene eseguita la funzione JavaScript `invokeCSharpAction`. Questa funzionalità si rende disponibile come indicato di seguito:

- Se il renderer personalizzato è associato a un nuovo elemento di Xamarin.Forms:
  - Il metodo `SetWebViewClient` imposta un nuovo oggetto `JavascriptWebViewClient` come implementazione di `WebViewClient`.
  - Il metodo [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*) inserisce una nuova istanza di `JSBridge` nel frame principale del contesto JavaScript di WebView, denominandola `jsBridge`. In questo modo è possibile accedere ai metodi presenti nella classe `JSBridge` da JavaScript.
  - Il metodo [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*) carica il file HTML specificato dalla proprietà `HybridWebView.Uri`. Il codice specifica che il file è archiviato nella cartella `Content` del progetto.
  - Nella classe `JavascriptWebViewClient` la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web al termine del caricamento della pagina.
- Quando l'elemento a cui è associato il renderer cambia:
  - Le risorse vengono rilasciate.

Quando viene eseguita, la funzione JavaScript `invokeCSharpAction` a sua volta richiama il metodo `JSBridge.InvokeAction`, come illustra l'esempio di codice seguente:

```csharp
public class JSBridge : Java.Lang.Object
{
    readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

    public JSBridge(HybridWebViewRenderer hybridRenderer)
    {
        hybridWebViewRenderer = new WeakReference<HybridWebViewRenderer>(hybridRenderer);
    }

    [JavascriptInterface]
    [Export("invokeAction")]
    public void InvokeAction(string data)
    {
        HybridWebViewRenderer hybridRenderer;

        if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget(out hybridRenderer))
        {
            ((HybridWebView)hybridRenderer.Element).InvokeAction(data);
        }
    }
}
```

La classe deve derivare da `Java.Lang.Object` e i metodi esposti a JavaScript devono essere decorati con gli attributi `[JavascriptInterface]` e `[Export]`. Di conseguenza, quando la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web ed eseguita, chiama il metodo `JSBridge.InvokeAction` poiché è decorata con gli attributi `[JavascriptInterface]` e `[Export("invokeAction")]`. Il metodo `InvokeAction` richiama a sua volta il metodo `HybridWebView.InvokeAction`, che richiama l'azione registrata per visualizzare il popup.

> [!IMPORTANT]
> I progetti Android che usano l'attributo `[Export]` devono includere un riferimento a `Mono.Android.Export`, altrimenti verrà generato un errore del compilatore.

Si noti che la classe `JSBridge` mantiene un elemento `WeakReference` per la classe `HybridWebViewRenderer`, per evitare la creazione di un riferimento circolare tra le due classi. Per ulteriori informazioni, vedere [riferimenti deboli](/en-us/dotnet/standard/garbage-collection/weak-references).

### <a name="create-the-custom-renderer-on-uwp"></a>Creare il renderer personalizzato in UWP

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : WebViewRenderer
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.WebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri($"ms-appx-web:///Content//{((HybridWebView)Element).Uri}");
            }
        }

        async void OnWebViewNavigationCompleted(Windows.UI.Xaml.Controls.WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            ((HybridWebView)Element).InvokeAction(e.Value);
        }
    }
}
```

La classe `HybridWebViewRenderer` carica la pagina Web specificata nella proprietà `HybridWebView.Uri` in un controllo `WebView` nativo e la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web, al termine del caricamento della pagina, con il metodo `WebView.InvokeScriptAsync`. Dopo che l'utente immette il proprio nome e fa clic sull'elemento HTML `button`, la funzione JavaScript `invokeCSharpAction` viene eseguita, con il metodo `OnWebViewScriptNotify` chiamato dopo che viene ricevuta una notifica dalla pagina Web. A sua volta, questo metodo richiama il metodo `HybridWebView.InvokeAction`, che richiama l'azione registrata per visualizzare l'elemento popup.

Questa funzionalità si rende disponibile come indicato di seguito:

- Se il renderer personalizzato è associato a un nuovo elemento di Xamarin.Forms:
  - I gestori eventi per gli eventi `NavigationCompleted` e `ScriptNotify` sono registrati. L'evento `NavigationCompleted` viene attivato quando il controllo nativo `WebView` ha completato il caricamento del contenuto corrente o se la navigazione ha avuto esito negativo. L'evento `ScriptNotify` viene attivato quando il contenuto nel controllo nativo `WebView` usa JavaScript per passare una stringa all'applicazione. La pagina Web genera l'evento `ScriptNotify` chiamando `window.external.notify` mentre passa un parametro `string`.
  - La proprietà `WebView.Source` viene impostata sull'URI del file HTML specificato dalla proprietà `HybridWebView.Uri`. Il codice presuppone che il file sia archiviato nella cartella `Content` del progetto. Dopo che è stata visualizzata la pagina Web, viene attivato l'evento `NavigationCompleted` e viene richiamato il metodo `OnWebViewNavigationCompleted`. La funzione JavaScript `invokeCSharpAction` viene quindi inserita nella pagina Web con il metodo `WebView.InvokeScriptAsync`, a condizione che la navigazione sia stata completata correttamente.
- Quando l'elemento a cui è associato il renderer cambia:
  - Gli eventi vengono annullati.

## <a name="related-links"></a>Collegamenti correlati

- [HybridWebView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)
