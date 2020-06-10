---
Titolo: "personalizzazione di una WebView" Descrizione: "una visualizzazione Xamarin.Forms Web è una vista che Visualizza il contenuto Web e HTML nell'app. In questo articolo viene illustrato come creare un renderer personalizzato che estende la visualizzazione WebView per consentire che il codice C# venga richiamato da JavaScript. "
ms. prod: Novell MS. AssetID: 58DFFA52-4057-49A8-8682-50A58C7E842C ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 03/31/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="customizing-a-webview"></a>Personalizzazione di un elemento WebView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_Un Xamarin.Forms `WebView` è una vista che Visualizza il contenuto Web e HTML nell'app. Questo articolo illustra come creare un renderer personalizzato che estende `WebView` per consentire la chiamata del codice C# da JavaScript._

Ogni Xamarin.Forms visualizzazione dispone di un renderer associato per ogni piattaforma che crea un'istanza di un controllo nativo. Quando un oggetto viene sottoposto a [`WebView`](xref:Xamarin.Forms.WebView) rendering da un' Xamarin.Forms applicazione in iOS, `WkWebViewRenderer` viene creata un'istanza della classe, che a sua volta crea un'istanza di un `WkWebView` controllo nativo. Nella piattaforma Android la classe `WebViewRenderer` crea un'istanza di un controllo `WebView` nativo. Nella piattaforma UWP (Universal Windows Platform) la classe `WebViewRenderer` crea un'istanza di un controllo `WebView` nativo. Per ulteriori informazioni sul renderer e sulle classi di controlli native che Xamarin.Forms controllano la mappa a, vedere [classi base renderer e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra [`View`](xref:Xamarin.Forms.View) e i controlli nativi corrispondenti che lo implementano:

![](hybridwebview-images/webview-classes.png "Relationship Between the WebView Class and its Implementing Native Classes")

Il processo di rendering può essere usato per implementare le personalizzazioni della piattaforma creando un renderer personalizzato per un [`WebView`](xref:Xamarin.Forms.WebView) in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#create-the-hybridwebview) il controllo personalizzato `HybridWebView`.
1. [Utilizzare](#consume-the-hybridwebview) l'oggetto `HybridWebView` da Xamarin.Forms .
1. [Creare](#create-the-custom-renderer-on-each-platform) il renderer personalizzato per `HybridWebView` in ogni piattaforma.

Ogni elemento verrà ora discusso a sua volta per implementare un `HybridWebView` renderer che migliora Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) per consentire la chiamata del codice C# da JavaScript. L'istanza di `HybridWebView` verrà usata per visualizzare una pagina HTML in cui viene chiesto all'utente di immettere il proprio nome. Quindi, quando l'utente fa clic su un pulsante HTML, una funzione JavaScript richiama un elemento `Action` di C# che visualizza una finestra popup contenente il nome dell'utente.

Per ulteriori informazioni sul processo per richiamare C# da JavaScript, vedere [Invoke c# from JavaScript](#invoke-c-from-javascript). Per ulteriori informazioni sulla pagina HTML, vedere [creare la pagina Web](#create-the-web-page).

> [!NOTE]
> Un oggetto [`WebView`](xref:Xamarin.Forms.WebView) può richiamare una funzione JavaScript da C# e restituire qualsiasi risultato al codice c# chiamante. Per ulteriori informazioni, vedere la pagina relativa alla [chiamata a JavaScript](~/xamarin-forms/user-interface/webview.md#invoking-javascript).

## <a name="create-the-hybridwebview"></a>Creare il HybridWebView

Il `HybridWebView` controllo personalizzato può essere creato mediante la creazione di una sottoclasse della [`WebView`](xref:Xamarin.Forms.WebView) classe:

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

L'istanza di `HybridWebView` verrà usata per visualizzare un controllo Web nativo in ogni piattaforma. `Uri`La proprietà è impostata su un file HTML archiviato in ogni progetto di piattaforma e che verrà visualizzato dal controllo Web nativo. Il codice HTML sottoposto a rendering chiede all'utente di immettere il proprio nome, con una funzione JavaScript che richiama un elemento `Action` di C# in risposta al clic su un pulsante HTML.

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

Questa azione chiama il [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) metodo per visualizzare un popup modale che presenta il nome immesso nella pagina HTML visualizzata dall' `HybridWebView` istanza.

È ora possibile aggiungere un renderer personalizzato a ogni progetto di applicazione per migliorare i controlli Web della piattaforma consentendo di richiamare il codice C# da JavaScript.

## <a name="create-the-custom-renderer-on-each-platform"></a>Creare il renderer personalizzato in ogni piattaforma

Il processo di creazione della classe di renderer personalizzato è il seguente:

1. Creare una sottoclasse della `WkWebViewRenderer` classe in iOS e la `WebViewRenderer` classe in Android e UWP che esegue il rendering del controllo personalizzato.
1. Eseguire l'override del `OnElementChanged` metodo che esegue il rendering [`WebView`](xref:Xamarin.Forms.WebView) di e scrivere la logica per personalizzarla. Questo metodo viene chiamato quando `HybridWebView` viene creato un oggetto.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzata o *AssemblyInfo.cs*per specificare che verrà usato per eseguire il rendering del Xamarin.Forms controllo personalizzato. Questo attributo viene usato per registrare il renderer personalizzato con Xamarin.Forms .

> [!NOTE]
> Per la maggior parte Xamarin.Forms degli elementi, è facoltativo fornire un renderer personalizzato in ogni progetto di piattaforma. Se un renderer personalizzato non è registrato, verrà usato il renderer predefinito per la classe di base del controllo. I renderer personalizzati sono tuttavia necessari in ogni progetto della piattaforma quando si esegue il rendering di un elemento [View](xref:Xamarin.Forms.View).

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](hybridwebview-images/solution-structure.png "HybridWebView Custom Renderer Project Responsibilities")

Il `HybridWebView` rendering del controllo personalizzato viene eseguito dalle classi renderer della piattaforma, che derivano dalla `WkWebViewRenderer` classe in iOS e dalla `WebViewRenderer` classe in Android e UWP. In questo `HybridWebView` modo viene eseguito il rendering di ogni controllo personalizzato con controlli Web nativi, come illustrato nelle schermate seguenti:

![](hybridwebview-images/screenshots.png "HybridWebView on each Platform")

Le `WkWebViewRenderer` `WebViewRenderer` classi e espongono il `OnElementChanged` metodo, che viene chiamato quando Xamarin.Forms viene creato il controllo personalizzato per eseguire il rendering del controllo Web nativo corrispondente. Questo metodo accetta un `VisualElementChangedEventArgs` parametro che contiene `OldElement` le `NewElement` proprietà e. Queste proprietà rappresentano l' Xamarin.Forms elemento a cui è *stato* collegato il renderer e l' Xamarin.Forms elemento a cui *è* collegato rispettivamente il renderer. Nell'applicazione di esempio la proprietà `OldElement` sarà `null` e la proprietà `NewElement` conterrà un riferimento all'istanza di `HybridWebView`.

Una versione sottoposta a override del `OnElementChanged` metodo, in ogni classe renderer della piattaforma, è la posizione in cui eseguire la personalizzazione del controllo Web nativo. Un riferimento al Xamarin.Forms controllo di cui è in corso il rendering può essere ottenuto tramite la `Element` Proprietà.

Ogni classe renderer personalizzata è decorata con un `ExportRenderer` attributo che registra il renderer con Xamarin.Forms . L'attributo accetta due parametri: il nome del tipo del Xamarin.Forms controllo personalizzato di cui viene eseguito il rendering e il nome del tipo del renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

Nelle sezioni seguenti viene illustrata la struttura della pagina Web caricata da ogni controllo Web nativo, il processo per richiamare C# da JavaScript e l'implementazione di in ogni classe renderer personalizzata della piattaforma.

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

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

La `HybridWebViewRenderer` classe carica la pagina Web specificata nella `HybridWebView.Uri` Proprietà in un controllo nativo [`WKWebView`](xref:WebKit.WKWebView) e la `invokeCSharpAction` funzione JavaScript viene inserita nella pagina Web. Dopo che l'utente immette il proprio nome e fa clic sull'elemento HTML `button`, la funzione JavaScript `invokeCSharpAction` viene eseguita, con il metodo `DidReceiveScriptMessage` chiamato dopo che viene ricevuto un messaggio dalla pagina Web. A sua volta, questo metodo richiama il metodo `HybridWebView.InvokeAction`, che richiama l'azione registrata per visualizzare l'elemento popup.

Questa funzionalità si rende disponibile come indicato di seguito:

- Il costruttore renderer crea un `WkWebViewConfiguration` oggetto e recupera il relativo [`WKUserContentController`](xref:WebKit.WKUserContentController) oggetto. L' `WkUserContentController` oggetto consente di inviare messaggi e inserire script utente in una pagina Web.
- Il costruttore renderer crea un [`WKUserScript`](xref:WebKit.WKUserScript) oggetto che inserisce la `invokeCSharpAction` funzione JavaScript nella pagina Web dopo il caricamento della pagina Web.
- Il costruttore renderer chiama il [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) metodo per aggiungere l' [`WKUserScript`](xref:WebKit.WKUserScript) oggetto al controller di contenuto.
- Il costruttore renderer chiama il [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) metodo per aggiungere un gestore di messaggi di script denominato `invokeAction` all' [`WKUserContentController`](xref:WebKit.WKUserContentController) oggetto, in modo che la funzione JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` venga definita in tutti i frame in tutte le `WebView` istanze che usano l' `WKUserContentController` oggetto.
- Purché il renderer personalizzato sia collegato a un nuovo Xamarin.Forms elemento:
  - Il [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) metodo carica il file HTML specificato dalla `HybridWebView.Uri` Proprietà. Il codice specifica che il file è archiviato nella cartella `Content` del progetto. Quando viene visualizzata la pagina Web, la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web.
- Le risorse vengono rilasciate quando l'elemento a cui è associato il renderer viene modificato.
- L' Xamarin.Forms elemento viene pulito quando il renderer viene eliminato.

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
                Control.SetWebViewClient(new JavascriptWebViewClient(this, $"javascript: {JavascriptFunction}"));
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{((HybridWebView)Element).Uri}");
            }
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

La `HybridWebViewRenderer` classe carica la pagina Web specificata nella `HybridWebView.Uri` Proprietà in un controllo nativo [`WebView`](xref:Android.Webkit.WebView) e la `invokeCSharpAction` funzione JavaScript viene inserita nella pagina Web, al termine del caricamento della pagina Web, con l' `OnPageFinished` override nella `JavascriptWebViewClient` classe:

```csharp
public class JavascriptWebViewClient : FormsWebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(HybridWebViewRenderer renderer, string javascript) : base(renderer)
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

- Purché il renderer personalizzato sia collegato a un nuovo Xamarin.Forms elemento:
  - Il `SetWebViewClient` metodo imposta un nuovo `JavascriptWebViewClient` oggetto come implementazione di `WebViewClient` .
  - Il [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*) metodo inserisce una nuova `JSBridge` istanza nel frame principale del contesto JavaScript della visualizzazione WebView, assegnando loro un nome `jsBridge` . In questo modo è possibile accedere ai metodi presenti nella classe `JSBridge` da JavaScript.
  - Il [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*) metodo carica il file HTML specificato dalla `HybridWebView.Uri` Proprietà. Il codice specifica che il file è archiviato nella cartella `Content` del progetto.
  - Nella classe `JavascriptWebViewClient` la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web al termine del caricamento della pagina.
- Le risorse vengono rilasciate quando l'elemento a cui è associato il renderer viene modificato.
- L' Xamarin.Forms elemento viene pulito quando il renderer viene eliminato.

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

La classe deve derivare da `Java.Lang.Object` e i metodi esposti a JavaScript devono essere decorati con gli attributi `[JavascriptInterface]` e `[Export]`. Di conseguenza, quando la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web ed eseguita, chiama il metodo `JSBridge.InvokeAction` poiché è decorata con gli attributi `[JavascriptInterface]` e `[Export("invokeAction")]`. Il metodo richiama a sua volta il `InvokeAction` `HybridWebView.InvokeAction` metodo, che richiama l'azione registrata per visualizzare il popup.

> [!IMPORTANT]
> I progetti Android che usano l' `[Export]` attributo devono includere un riferimento a `Mono.Android.Export` . in alternativa, verrà generato un errore del compilatore.

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

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

La classe `HybridWebViewRenderer` carica la pagina Web specificata nella proprietà `HybridWebView.Uri` in un controllo `WebView` nativo e la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web, al termine del caricamento della pagina, con il metodo `WebView.InvokeScriptAsync`. Dopo che l'utente immette il proprio nome e fa clic sull'elemento HTML `button`, la funzione JavaScript `invokeCSharpAction` viene eseguita, con il metodo `OnWebViewScriptNotify` chiamato dopo che viene ricevuta una notifica dalla pagina Web. A sua volta, questo metodo richiama il metodo `HybridWebView.InvokeAction`, che richiama l'azione registrata per visualizzare l'elemento popup.

Questa funzionalità si rende disponibile come indicato di seguito:

- Purché il renderer personalizzato sia collegato a un nuovo Xamarin.Forms elemento:
  - I gestori eventi per gli eventi `NavigationCompleted` e `ScriptNotify` sono registrati. L'evento `NavigationCompleted` viene attivato quando il controllo nativo `WebView` ha completato il caricamento del contenuto corrente o se la navigazione ha avuto esito negativo. L'evento `ScriptNotify` viene attivato quando il contenuto nel controllo nativo `WebView` usa JavaScript per passare una stringa all'applicazione. La pagina Web genera l'evento `ScriptNotify` chiamando `window.external.notify` mentre passa un parametro `string`.
  - La proprietà `WebView.Source` viene impostata sull'URI del file HTML specificato dalla proprietà `HybridWebView.Uri`. Il codice presuppone che il file sia archiviato nella cartella `Content` del progetto. Dopo che è stata visualizzata la pagina Web, viene attivato l'evento `NavigationCompleted` e viene richiamato il metodo `OnWebViewNavigationCompleted`. La funzione JavaScript `invokeCSharpAction` viene quindi inserita nella pagina Web con il metodo `WebView.InvokeScriptAsync`, a condizione che la navigazione sia stata completata correttamente.
- La sottoscrizione dell'evento viene annullata quando l'elemento a cui è associato il renderer viene modificato.
- L' Xamarin.Forms elemento viene pulito quando il renderer viene eliminato.

## <a name="related-links"></a>Collegamenti correlati

- [HybridWebView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)
