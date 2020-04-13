---
title: Personalizzazione di un elemento WebView
description: Un Xamarin.Forms WebView è una visualizzazione che visualizza contenuto Web e HTML nell'app. In questo articolo viene illustrato come creare un renderer personalizzato che estende il WebView per consentire il codice c'è da richiamare da JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/31/2020
ms.openlocfilehash: c736c083d4a8c424d3e017dae3cc30e35ad4fa3b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "80419057"
---
# <a name="customizing-a-webview"></a>Personalizzazione di un elemento WebView

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_Un file Xamarin.Forms `WebView` è una visualizzazione che visualizza contenuto Web e HTML nell'app. In questo articolo viene illustrato come creare `WebView` un renderer personalizzato che estende l'oggetto per consentire la richiamata di codice C ' da JavaScript._

A ogni vista di Xamarin.Forms è associato un renderer per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando [`WebView`](xref:Xamarin.Forms.WebView) un oggetto viene eseguito il rendering da un'applicazione Xamarin.Forms in iOS, viene creata un'istanza della `WkWebViewRenderer` classe, che a sua volta crea un'istanza di un controllo nativo. `WkWebView` Nella piattaforma Android la classe `WebViewRenderer` crea un'istanza di un controllo `WebView` nativo. Nella piattaforma UWP (Universal Windows Platform) la classe `WebViewRenderer` crea un'istanza di un controllo `WebView` nativo. Per altre informazioni sulle classi del renderer e dei controlli nativi di cui Xamarin.Forms controlla il mapping, vedere [Classi di base e controlli nativi del renderer](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Nel diagramma seguente viene illustrata la relazione tra i [`View`](xref:Xamarin.Forms.View) controlli nativi e quelli corrispondenti che lo implementano:

![](hybridwebview-images/webview-classes.png "Relationship Between the WebView Class and its Implementing Native Classes")

Il processo di rendering può essere utilizzato per implementare [`WebView`](xref:Xamarin.Forms.WebView) le personalizzazioni della piattaforma creando un renderer personalizzato per un in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#create-the-hybridwebview) il controllo personalizzato `HybridWebView`.
1. [Usare](#consume-the-hybridwebview)`HybridWebView` da Xamarin.Forms.
1. [Creare](#create-the-custom-renderer-on-each-platform) il renderer personalizzato per `HybridWebView` in ogni piattaforma.

Ogni elemento verrà ora discusso a `HybridWebView` sua volta per implementare un [`WebView`](xref:Xamarin.Forms.WebView) renderer che migliora il Xamarin.Forms per consentire il codice c'è da richiamare da JavaScript. L'istanza di `HybridWebView` verrà usata per visualizzare una pagina HTML in cui viene chiesto all'utente di immettere il proprio nome. Quindi, quando l'utente fa clic su un pulsante HTML, una funzione JavaScript richiama un elemento `Action` di C# che visualizza una finestra popup contenente il nome dell'utente.

Per ulteriori informazioni sul processo di chiamata di C' da JavaScript, consultate [Richiamare C'è da JavaScript.](#invoke-c-from-javascript) Per ulteriori informazioni sulla pagina HTML, vedere [Creare la pagina Web](#create-the-web-page).

> [!NOTE]
> Un [`WebView`](xref:Xamarin.Forms.WebView) oggetto può richiamare una funzione JavaScript da C, e restituire qualsiasi risultato al codice di chiamata in C. Per ulteriori informazioni, consultate [Richiamo di JavaScript.](~/xamarin-forms/user-interface/webview.md#invoking-javascript)

## <a name="create-the-hybridwebview"></a>Creare HybridWebView

Il `HybridWebView` controllo personalizzato può essere creato [`WebView`](xref:Xamarin.Forms.WebView) creando una sottoclasse della classe:The custom control can be created by subclassing the class:

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

## <a name="consume-the-hybridwebview"></a>Usare HybridWebViewConsume the HybridWebView

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

L'istanza di `HybridWebView` verrà usata per visualizzare un controllo Web nativo in ogni piattaforma. La sua `Uri` proprietà è impostata su un file HTML che viene archiviato in ogni progetto di piattaforma e che verrà visualizzato dal controllo web nativo. Il codice HTML sottoposto a rendering chiede all'utente di immettere il proprio nome, con una funzione JavaScript che richiama un elemento `Action` di C# in risposta al clic su un pulsante HTML.

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

Questa azione [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) chiama il metodo per visualizzare un popup modale che presenta `HybridWebView` il nome immesso nella pagina HTML visualizzata dall'istanza.

È ora possibile aggiungere un renderer personalizzato a ogni progetto di applicazione per migliorare i controlli Web della piattaforma, consentendo di richiamare il codice In linguaggio C.

## <a name="create-the-custom-renderer-on-each-platform"></a>Creare il renderer personalizzato in ogni piattaformaCreate the custom renderer on each platform

Il processo di creazione della classe di renderer personalizzato è il seguente:

1. Creare una sottoclasse `WkWebViewRenderer` della classe in iOS e la `WebViewRenderer` classe in Android e UWP, che esegue il rendering del controllo personalizzato.
1. Eseguire `OnElementChanged` l'override [`WebView`](xref:Xamarin.Forms.WebView) del metodo che esegue il rendering della logica e di scrittura per personalizzarla. Questo metodo viene `HybridWebView` chiamato quando viene creato un oggetto.
1. Aggiungere `ExportRenderer` un attributo alla classe renderer personalizzata o a *AssemblyInfo.cs*, per specificare che verrà utilizzato per eseguire il rendering del controllo personalizzato Xamarin.Forms. L'attributo viene usato per registrare il renderer personalizzato con Xamarin.Forms.

> [!NOTE]
> Per la maggior parte degli elementi di Xamarin.Forms, l'indicazione di un renderer personalizzato in ogni progetto della piattaforma è facoltativa. Se un renderer personalizzato non è registrato, verrà usato il renderer predefinito per la classe di base del controllo. I renderer personalizzati sono tuttavia necessari in ogni progetto della piattaforma quando si esegue il rendering di un elemento [View](xref:Xamarin.Forms.View).

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](hybridwebview-images/solution-structure.png "HybridWebView Custom Renderer Project Responsibilities")

Il `HybridWebView` controllo personalizzato viene eseguito il rendering `WkWebViewRenderer` da classi di renderer `WebViewRenderer` della piattaforma, che derivano dalla classe in iOS, e dalla classe su Android e UWP. In questo `HybridWebView` modo viene eseguito il rendering di ogni controllo personalizzato con controlli Web nativi, come illustrato nelle schermate seguenti:This results in each custom control being rendered with native web controls, as shown in the following screenshots:

![](hybridwebview-images/screenshots.png "HybridWebView on each Platform")

Le `WkWebViewRenderer` `WebViewRenderer` classi e `OnElementChanged` espongono il metodo , che viene chiamato quando viene creato il controllo personalizzato Xamarin.Forms per eseguire il rendering del controllo Web nativo corrispondente. Questo metodo `VisualElementChangedEventArgs` accetta un `OldElement` `NewElement` parametro che contiene e proprietà. Queste proprietà rappresentano rispettivamente l'elemento Xamarin.Forms a cui il renderer *era* collegato e l'elemento Xamarin.Forms a cui il renderer *è* collegato. Nell'applicazione di esempio la proprietà `OldElement` sarà `null` e la proprietà `NewElement` conterrà un riferimento all'istanza di `HybridWebView`.

Una versione sottoposta `OnElementChanged` a override del metodo, in ogni classe del renderer della piattaforma, è la posizione in cui eseguire la personalizzazione del controllo Web nativo. È possibile ottenere un riferimento al controllo Xamarin.Forms `Element` di cui viene eseguito il rendering tramite la proprietà .

Ogni classe del renderer personalizzato è decorata con un attributo `ExportRenderer` che registra il renderer in Xamarin.Forms. L'attributo accetta due parametri: il nome del tipo di controllo personalizzato Xamarin.Forms di cui viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

Nelle sezioni seguenti viene illustrata la struttura della pagina Web caricata da ogni controllo Web nativo, il processo per la chiamata di C 'NET da JavaScript e l'implementazione di questo in ogni classe renderer personalizzata della piattaforma.

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

### <a name="invoke-c-from-javascript"></a>Richiamare il linguaggio C' da JavaScript

Il processo per richiamare C# da JavaScript è identico in ogni piattaforma:

- Il renderer personalizzato crea un controllo Web nativo e carica il file HTML specificato dalla proprietà `HybridWebView.Uri`.
- Dopo che la pagina Web è stata caricata, il renderer personalizzato inserisce la funzione JavaScript `invokeCSharpAction` nella pagina Web.
- Quando l'utente immette il proprio nome e fa clic sull'elemento HTML `button`, viene richiamata la funzione `invokeCSCode`, che a sua volta richiama la funzione `invokeCSharpAction`.
- La funzione `invokeCSharpAction` richiama un metodo nel renderer personalizzato, che a sua volta richiama il metodo `HybridWebView.InvokeAction`.
- Il metodo `HybridWebView.InvokeAction` richiama l'elemento `Action` registrato.

Le sezioni seguenti spiegano come viene implementato questo processo in ogni piattaforma.

### <a name="create-the-custom-renderer-on-ios"></a>Creare il renderer personalizzato in iOSCreate the custom renderer on iOS

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

La `HybridWebViewRenderer` classe carica la pagina `HybridWebView.Uri` Web specificata [`WKWebView`](xref:WebKit.WKWebView) nella proprietà `invokeCSharpAction` in un controllo nativo e la funzione JavaScript viene inserita nella pagina Web. Dopo che l'utente immette il proprio nome e fa clic sull'elemento HTML `button`, la funzione JavaScript `invokeCSharpAction` viene eseguita, con il metodo `DidReceiveScriptMessage` chiamato dopo che viene ricevuto un messaggio dalla pagina Web. A sua volta, questo metodo richiama il metodo `HybridWebView.InvokeAction`, che richiama l'azione registrata per visualizzare l'elemento popup.

Questa funzionalità si rende disponibile come indicato di seguito:

- Il costruttore del `WkWebViewConfiguration` renderer crea [`WKUserContentController`](xref:WebKit.WKUserContentController) un oggetto e ne recupera l'oggetto. L'oggetto `WkUserContentController` consente la pubblicazione di messaggi e l'inserimento di script utente in una pagina Web.
- Il costruttore renderer crea un [`WKUserScript`](xref:WebKit.WKUserScript) `invokeCSharpAction` oggetto, che inserisce la funzione JavaScript nella pagina Web dopo il caricamento della pagina Web.
- Il costruttore del [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) renderer [`WKUserScript`](xref:WebKit.WKUserScript) chiama il metodo per aggiungere l'oggetto al controller del contenuto.
- Il costruttore renderer chiama il [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) metodo `invokeAction` per [`WKUserContentController`](xref:WebKit.WKUserContentController) aggiungere un gestore di `window.webkit.messageHandlers.invokeAction.postMessage(data)` messaggi di script denominato `WebView` all'oggetto, `WKUserContentController` che causerà la funzione JavaScript da definire in tutti i frame in tutte le istanze che utilizzano l'oggetto.
- Se il renderer personalizzato è associato a un nuovo elemento di Xamarin.Forms:
  - Il [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) metodo carica il file HTML `HybridWebView.Uri` specificato dalla proprietà . Il codice specifica che il file è archiviato nella cartella `Content` del progetto. Quando viene visualizzata la pagina Web, la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web.
- Le risorse vengono rilasciate quando l'elemento a cui il renderer è associato alle modifiche.
- L'elemento Xamarin.Forms viene pulito quando il renderer viene eliminato.

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

### <a name="create-the-custom-renderer-on-android"></a>Creare il renderer personalizzato su AndroidCreate the custom renderer on android

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

La `HybridWebViewRenderer` classe carica la pagina `HybridWebView.Uri` Web specificata [`WebView`](xref:Android.Webkit.WebView) nella proprietà `invokeCSharpAction` in un controllo nativo e la funzione JavaScript viene inserita nella pagina Web, al termine del caricamento della pagina Web, con l'override `OnPageFinished` nella `JavascriptWebViewClient` classe:

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

- Se il renderer personalizzato è associato a un nuovo elemento di Xamarin.Forms:
  - Il `SetWebViewClient` metodo imposta `JavascriptWebViewClient` un nuovo `WebViewClient`oggetto come implementazione di .
  - Il [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*) metodo inserisce `JSBridge` una nuova istanza nel frame principale del contesto `jsBridge`JavaScript di WebView, denominandola . In questo modo è possibile accedere ai metodi presenti nella classe `JSBridge` da JavaScript.
  - Il [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*) metodo carica il file HTML `HybridWebView.Uri` specificato dalla proprietà . Il codice specifica che il file è archiviato nella cartella `Content` del progetto.
  - Nella classe `JavascriptWebViewClient` la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web al termine del caricamento della pagina.
- Le risorse vengono rilasciate quando l'elemento a cui il renderer è associato alle modifiche.
- L'elemento Xamarin.Forms viene pulito quando il renderer viene eliminato.

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

La classe deve derivare da `Java.Lang.Object` e i metodi esposti a JavaScript devono essere decorati con gli attributi `[JavascriptInterface]` e `[Export]`. Di conseguenza, quando la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web ed eseguita, chiama il metodo `JSBridge.InvokeAction` poiché è decorata con gli attributi `[JavascriptInterface]` e `[Export("invokeAction")]`. A sua `InvokeAction` volta, il `HybridWebView.InvokeAction` metodo richiama il metodo , che richiamerà l'azione registrata per visualizzare il popup.

> [!IMPORTANT]
> I progetti Android `[Export]` che utilizzano `Mono.Android.Export`l'attributo devono includere un riferimento a , altrimenti verrà generato un errore del compilatore.

Si noti che la classe `JSBridge` mantiene un elemento `WeakReference` per la classe `HybridWebViewRenderer`, per evitare la creazione di un riferimento circolare tra le due classi. Per ulteriori informazioni, vedere [Riferimenti deboli](/en-us/dotnet/standard/garbage-collection/weak-references).

### <a name="create-the-custom-renderer-on-uwp"></a>Creare il renderer personalizzato in UWPCreate the custom renderer on UWP

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

- Se il renderer personalizzato è associato a un nuovo elemento di Xamarin.Forms:
  - I gestori eventi per gli eventi `NavigationCompleted` e `ScriptNotify` sono registrati. L'evento `NavigationCompleted` viene attivato quando il controllo nativo `WebView` ha completato il caricamento del contenuto corrente o se la navigazione ha avuto esito negativo. L'evento `ScriptNotify` viene attivato quando il contenuto nel controllo nativo `WebView` usa JavaScript per passare una stringa all'applicazione. La pagina Web genera l'evento `ScriptNotify` chiamando `window.external.notify` mentre passa un parametro `string`.
  - La proprietà `WebView.Source` viene impostata sull'URI del file HTML specificato dalla proprietà `HybridWebView.Uri`. Il codice presuppone che il file sia archiviato nella cartella `Content` del progetto. Dopo che è stata visualizzata la pagina Web, viene attivato l'evento `NavigationCompleted` e viene richiamato il metodo `OnWebViewNavigationCompleted`. La funzione JavaScript `invokeCSharpAction` viene quindi inserita nella pagina Web con il metodo `WebView.InvokeScriptAsync`, a condizione che la navigazione sia stata completata correttamente.
- Evento sono annullati da quando l'elemento il renderer è associato alle modifiche.
- L'elemento Xamarin.Forms viene pulito quando il renderer viene eliminato.

## <a name="related-links"></a>Collegamenti correlati

- [HybridWebView (esempio)HybridWebView (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)
