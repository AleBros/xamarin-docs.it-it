---
title: Implementazione di un elemento HybridWebView
description: Questo articolo spiega come creare un renderer personalizzato per un controllo personalizzato HybridWebView, che dimostra come si possono ottimizzare i controlli Web specifici della piattaforma per consentire al codice C# di essere richiamato da JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: 0b91aae1456827625526d7568176a07e7e61f225
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507123"
---
# <a name="implementing-a-hybridwebview"></a>Implementazione di un elemento HybridWebView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)

_I controlli dell'interfaccia utente personalizzata di Xamarin.Forms devono derivare dalla classe View, che viene usata per posizionare layout e controlli sullo schermo. Questo articolo spiega come creare un renderer personalizzato per un controllo personalizzato HybridWebView, che dimostra come si possono ottimizzare i controlli Web specifici della piattaforma per consentire al codice C# di essere richiamato da JavaScript._

A ogni vista di Xamarin.Forms è associato un renderer per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un'applicazione Xamarin.Forms esegue il rendering di un oggetto [`View`](xref:Xamarin.Forms.View) in iOS, viene creata un'istanza della classe `ViewRenderer`, che a sua volta crea un'istanza di un controllo `UIView` nativo. Nella piattaforma Android la classe `ViewRenderer` crea un'istanza di un controllo `View`. Nella piattaforma UWP (Universal Windows Platform) la classe `ViewRenderer` crea un'istanza di un controllo `FrameworkElement` nativo. Per altre informazioni sulle classi del renderer e dei controlli nativi di cui Xamarin.Forms controlla il mapping, vedere [Classi di base e controlli nativi del renderer](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra la classe [`View`](xref:Xamarin.Forms.View) e i controlli nativi corrispondenti che la implementano:

![](hybridwebview-images/view-classes.png "Relazione tra la classe View e le classi native che la implementano")

È possibile usare il processo di rendering per implementare personalizzazioni specifiche della piattaforma creando un renderer personalizzato per un elemento [`View`](xref:Xamarin.Forms.View) in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#Creating_the_HybridWebView) il controllo personalizzato `HybridWebView`.
1. [Usare](#Consuming_the_HybridWebView) `HybridWebView` da Xamarin.Forms.
1. [Creare](#creating-the-custom-renderer-on-each-platform) il renderer personalizzato per `HybridWebView` in ogni piattaforma.

Ogni elemento verrà trattato separatamente per implementare un renderer `HybridWebView` che ottimizza i controlli Web specifici della piattaforma per consentire al codice C# di essere richiamato da JavaScript. L'istanza di `HybridWebView` verrà usata per visualizzare una pagina HTML in cui viene chiesto all'utente di immettere il proprio nome. Quindi, quando l'utente fa clic su un pulsante HTML, una funzione JavaScript richiama un elemento `Action` di C# che visualizza una finestra popup contenente il nome dell'utente.

Per altre informazioni sul processo usato per richiamare C# da JavaScript, vedere [Chiamata di C# da JavaScript](#Invoking_C_from_JavaScript). Per altre informazioni sulla pagina HTML, vedere [Creazione della pagina Web](#Creating_the_Web_Page).

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>Creazione dell'elemento HybridWebView

Per creare il controllo personalizzato `HybridWebView`, è possibile sottoclassare la classe [`View`](xref:Xamarin.Forms.View), come illustrato nell'esempio di codice seguente:

```csharp
public class HybridWebView : View
{
  Action<string> action;
  public static readonly BindableProperty UriProperty = BindableProperty.Create (
    propertyName: "Uri",
    returnType: typeof(string),
    declaringType: typeof(HybridWebView),
    defaultValue: default(string));

  public string Uri {
    get { return (string)GetValue (UriProperty); }
    set { SetValue (UriProperty, value); }
  }

  public void RegisterAction (Action<string> callback)
  {
    action = callback;
  }

  public void Cleanup ()
  {
    action = null;
  }

  public void InvokeAction (string data)
  {
    if (action == null || data == null) {
      return;
    }
    action.Invoke (data);
  }
}
```

Il controllo personalizzato `HybridWebView` viene creato nel progetto di libreria .NET Standard e definisce l'API seguente per il controllo:

- Una proprietà `Uri` che specifica l'indirizzo della pagina Web da caricare.
- Un metodo `RegisterAction` che registra un elemento `Action` con il controllo. L'azione registrata verrà richiamata da JavaScript all'interno del file HTML a cui si fa riferimento attraverso la proprietà `Uri`.
- Un metodo `CleanUp` che rimuove il riferimento all'elemento `Action` registrato.
- Un metodo `InvokeAction` che richiama l'elemento `Action` registrato. Questo metodo verrà chiamato da un renderer personalizzato in ogni progetto specifico della piattaforma.

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>Uso dell'elemento HybridWebView

Per fare riferimento al controllo personalizzato `HybridWebView` in XAML nel progetto di libreria .NET Standard, è possibile dichiarare uno spazio dei nomi per il percorso e usare il prefisso dello spazio dei nomi nel controllo personalizzato. Nell'esempio di codice riportato di seguito viene illustrato come il controllo personalizzato `HybridWebView` può essere usato da una pagina XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <local:HybridWebView x:Name="hybridWebView" Uri="index.html"
          HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
    </ContentPage.Content>
</ContentPage>
```

Il prefisso dello spazio dei nomi `local` può avere qualsiasi nome. I valori `clr-namespace` e `assembly` devono tuttavia corrispondere ai dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene usato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come il controllo personalizzato `HybridWebView` può essere usato da una pagina C#:

```csharp
public class HybridWebViewPageCS : ContentPage
{
  public HybridWebViewPageCS ()
  {
    var hybridWebView = new HybridWebView {
      Uri = "index.html",
      HorizontalOptions = LayoutOptions.FillAndExpand,
      VerticalOptions = LayoutOptions.FillAndExpand
    };
    ...
    Padding = new Thickness (0, 20, 0, 0);
    Content = hybridWebView;
  }
}
```

L'istanza di `HybridWebView` verrà usata per visualizzare un controllo Web nativo in ogni piattaforma. La relativa proprietà `Uri` è impostata su un file HTML archiviato in ogni progetto specifico della piattaforma che verrà visualizzato dal controllo Web nativo. Il codice HTML sottoposto a rendering chiede all'utente di immettere il proprio nome, con una funzione JavaScript che richiama un elemento `Action` di C# in risposta al clic su un pulsante HTML.

`HybridWebViewPage` registra l'azione da richiamare da JavaScript, come illustra l'esempio di codice seguente:

```csharp
public partial class HybridWebViewPage : ContentPage
{
  public HybridWebViewPage ()
  {
    ...
    hybridWebView.RegisterAction (data => DisplayAlert ("Alert", "Hello " + data, "OK"));
  }
}
```

Questa azione chiama il metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) per visualizzare un popup modale che presenta il nome immesso nella pagina HTML visualizzata dall'istanza di `HybridWebView`.

Ora è possibile aggiungere un renderer personalizzato a ogni progetto di applicazione per ottimizzare i controlli Web specifici della piattaforma, consentendo al codice C# di essere richiamato da JavaScript.

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione del renderer personalizzato in ogni piattaforma

Di seguito è illustrato il processo di creazione della classe di renderer personalizzato:

1. Creare una sottoclasse della classe `ViewRenderer<T1,T2>` che esegue il rendering del controllo personalizzato. Il primo argomento tipo deve essere il controllo personalizzato per cui si usa il renderer, in questo caso `HybridWebView`. Il secondo argomento tipo deve essere il controllo nativo che implementerà la vista personalizzata.
1. Eseguire l'override del metodo `OnElementChanged` che esegue il rendering del controllo personalizzato e scrivere la logica per personalizzarlo. Questo metodo viene chiamato quando viene creato il controllo Xamarin.Forms personalizzato corrispondente.
1. Aggiungere un attributo `ExportRenderer` alla classe di renderer personalizzato per specificare che verrà usata per eseguire il rendering del controllo Xamarin.Forms personalizzato. L'attributo viene usato per registrare il renderer personalizzato con Xamarin.Forms.

> [!NOTE]
> Per la maggior parte degli elementi di Xamarin.Forms, l'indicazione di un renderer personalizzato in ogni progetto della piattaforma è facoltativa. Se un renderer personalizzato non è registrato, verrà usato il renderer predefinito per la classe di base del controllo. I renderer personalizzati sono tuttavia necessari in ogni progetto della piattaforma quando si esegue il rendering di un elemento [View](xref:Xamarin.Forms.View).

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](hybridwebview-images/solution-structure.png "Responsabilità del progetto di renderer personalizzato HybridWebView")

Il rendering del controllo personalizzato `HybridWebView` viene eseguito dalle classi di renderer specifiche della piattaforma che derivano tutte dalla classe `ViewRenderer` per ogni piattaforma. Di conseguenza il rendering di ogni controllo `HybridWebView` viene eseguito con controlli Web specifici della piattaforma, come illustrato negli screenshot seguenti:

![](hybridwebview-images/screenshots.png "HybridWebView in ogni piattaforma")

La classe `ViewRenderer` espone il metodo `OnElementChanged` che viene chiamato quando si crea il controllo personalizzato Xamarin.Forms per eseguire il rendering del controllo Web nativo corrispondente. Questo metodo accetta un parametro `ElementChangedEventArgs` che contiene le proprietà `OldElement` e `NewElement`. Queste proprietà rappresentano rispettivamente l'elemento Xamarin.Forms a cui il renderer *era* collegato e l'elemento Xamarin.Forms a cui il renderer *è* collegato. Nell'applicazione di esempio la proprietà `OldElement` sarà `null` e la proprietà `NewElement` conterrà un riferimento all'istanza di `HybridWebView`.

La creazione dell'istanza e la personalizzazione del controllo Web nativo devono essere eseguite in una versione sostituita del metodo `OnElementChanged` in ogni classe di renderer specifica della piattaforma. Il metodo `SetNativeControl` deve essere usato per creare un'istanza del controllo Web nativo e assegna anche il riferimento al controllo alla proprietà `Control`. È inoltre possibile ottenere un riferimento al controllo Xamarin.Forms di cui viene eseguito il rendering usando la proprietà `Element`.

In alcune circostanze il metodo `OnElementChanged` può essere chiamato più volte. Di conseguenza, per evitare perdite di memoria, prestare attenzione quando si crea un'istanza di un nuovo controllo nativo. L'approccio da usare quando si crea un'istanza di un nuovo controllo nativo in un renderer personalizzato è illustrato nell'esempio di codice seguente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control and assign it to the Control property with
    // the SetNativeControl method
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

Per un nuovo controllo nativo l'istanza deve essere creata solo una volta, quando la proprietà `Control` è `null`. Configurare il controllo e sottoscrivere i gestori degli eventi solo quando il renderer personalizzato è allegato a un nuovo elemento di Xamarin.Forms. Analogamente, per i gestori degli eventi sottoscritti l'iscrizione deve essere annullata solo in caso di modifica dell'elemento allegato al renderer. L'adozione di questo approccio consente di creare un renderer personalizzato con prestazioni elevate che non subisce perdite di memoria.

Ogni classe del renderer personalizzato è decorata con un attributo `ExportRenderer` che registra il renderer in Xamarin.Forms. L'attributo accetta due parametri: il nome del tipo di controllo personalizzato Xamarin.Forms di cui viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano la struttura della pagina Web caricata da ogni controllo Web nativo, il processo per richiamare C# da JavaScript e l'implementazione di questi elementi in ogni classe di renderer personalizzato specifica della piattaforma.

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>Creazione della pagina Web

L'esempio di codice seguente illustra la pagina Web che verrà visualizzata dal controllo personalizzato `HybridWebView`:

```html
<html>
<body>
<script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
<h1>HybridWebView Test</h1>
<br/>
Enter name: <input type="text" id="name">
<br/>
<br/>
<button type="button" onclick="javascript:invokeCSCode($('#name').val());">Invoke C# Code</button>
<br/>
<p id="result">Result:</p>
<script type="text/javascript">
function log(str)
{
    $('#result').text($('#result').text() + " " + str);
}

function invokeCSCode(data) {
    try {
        log("Sending Data:" + data);
        invokeCSharpAction(data);
    }
    catch (err){
          log(err);
    }
}
</script>
</body>
</html>
```

La pagina Web consente all'utente di immettere il proprio nome in un elemento `input` e indica un elemento `button` che richiama il codice C# quando viene selezionato. Il processo per eseguire questa operazione è il seguente:

- Quando l'utente fa clic sull'elemento `button`, viene chiamata la funzione JavaScript `invokeCSCode`, con il valore dell'elemento `input` passato alla funzione.
- La funzione `invokeCSCode` chiama la funzione `log` per visualizzare i dati inviati all'elemento `Action` di C#. Chiama quindi il metodo `invokeCSharpAction` per richiamare l'elemento `Action` di C#, passando il parametro ricevuto dall'elemento `input`.

La funzione JavaScript `invokeCSharpAction` non è definita nella pagina Web e verrà inserita al suo interno da ogni renderer personalizzato.

In iOS questo file HTML si trova nella cartella del contenuto del progetto della piattaforma, con l'azione di compilazione **BundleResource**. In Android questo file HTML si trova nella cartella degli asset/del contenuto del progetto della piattaforma, con l'azione di compilazione **AndroidAsset**.

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>Chiamata di C# da JavaScript

Il processo per richiamare C# da JavaScript è identico in ogni piattaforma:

- Il renderer personalizzato crea un controllo Web nativo e carica il file HTML specificato dalla proprietà `HybridWebView.Uri`.
- Dopo che la pagina Web è stata caricata, il renderer personalizzato inserisce la funzione JavaScript `invokeCSharpAction` nella pagina Web.
- Quando l'utente immette il proprio nome e fa clic sull'elemento HTML `button`, viene richiamata la funzione `invokeCSCode`, che a sua volta richiama la funzione `invokeCSharpAction`.
- La funzione `invokeCSharpAction` richiama un metodo nel renderer personalizzato, che a sua volta richiama il metodo `HybridWebView.InvokeAction`.
- Il metodo `HybridWebView.InvokeAction` richiama l'elemento `Action` registrato.

Le sezioni seguenti spiegano come viene implementato questo processo in ogni piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione del renderer personalizzato in iOS

L'esempio di codice seguente illustra il renderer di personalizzato per la piattaforma iOS:

```csharp
[assembly: ExportRenderer (typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, WKWebView>, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        protected override void OnElementChanged (ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged (e);

            if (Control == null) {
                userController = new WKUserContentController ();
                var script = new WKUserScript (new NSString (JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
                userController.AddUserScript (script);
                userController.AddScriptMessageHandler (this, "invokeAction");

                var config = new WKWebViewConfiguration { UserContentController = userController };
                var webView = new WKWebView (Frame, config);
                SetNativeControl (webView);
            }
            if (e.OldElement != null) {
                userController.RemoveAllUserScripts ();
                userController.RemoveScriptMessageHandler ("invokeAction");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup ();
            }
            if (e.NewElement != null) {
                string fileName = Path.Combine (NSBundle.MainBundle.BundlePath, string.Format ("Content/{0}", Element.Uri));
                Control.LoadRequest (new NSUrlRequest (new NSUrl (fileName, false)));
            }
        }

        public void DidReceiveScriptMessage (WKUserContentController userContentController, WKScriptMessage message)
        {
            Element.InvokeAction (message.Body.ToString ());
        }
    }
}
```

La classe `HybridWebViewRenderer` carica la pagina Web specificata nella proprietà `HybridWebView.Uri` in un controllo [`WKWebView`](xref:WebKit.WKWebView) nativo e la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web. Dopo che l'utente immette il proprio nome e fa clic sull'elemento HTML `button`, la funzione JavaScript `invokeCSharpAction` viene eseguita, con il metodo `DidReceiveScriptMessage` chiamato dopo che viene ricevuto un messaggio dalla pagina Web. A sua volta, questo metodo richiama il metodo `HybridWebView.InvokeAction`, che richiama l'azione registrata per visualizzare l'elemento popup.

Questa funzionalità si rende disponibile come indicato di seguito:

- Posto che la proprietà `Control` sia `null`, vengono eseguite le operazioni seguenti:
  - Viene creata un'istanza di [`WKUserContentController`](xref:WebKit.WKUserContentController), che consente l'invio di messaggi e l'inserimento di script utente in una pagina Web.
  - Viene creata un'istanza di [`WKUserScript`](xref:WebKit.WKUserScript) per inserire la funzione JavaScript `invokeCSharpAction` nella pagina Web dopo il caricamento della pagina Web.
  - Il metodo [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) aggiunge l'istanza di [`WKUserScript`](xref:WebKit.WKUserScript) al controllo contenuto.
  - Il metodo [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) aggiunge un gestore di messaggi di script denominato `invokeAction` per l'istanza di [`WKUserContentController`](xref:WebKit.WKUserContentController), facendo in modo che la funzione JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` venga definita in tutti i frame in tutte le visualizzazioni Web che useranno l'istanza di `WKUserContentController`.
  - Viene creata un'istanza di [`WKWebViewConfiguration`](xref:WebKit.WKWebViewConfiguration), con l'istanza di [`WKUserContentController`](xref:WebKit.WKUserContentController)  impostata come controllo contenuto.
  - Viene creata un'istanza del controllo [`WKWebView`](xref:WebKit.WKWebView) e viene chiamato il metodo `SetNativeControl` per assegnare un riferimento al controllo `WKWebView` alla proprietà `Control`.
- Se il renderer personalizzato è associato a un nuovo elemento di Xamarin.Forms:
  - Il metodo [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) carica il file HTML specificato dalla proprietà `HybridWebView.Uri`. Il codice specifica che il file è archiviato nella cartella `Content` del progetto. Quando viene visualizzata la pagina Web, la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web.
- Quando l'elemento a cui è associato il renderer cambia:
  - Le risorse vengono rilasciate.

> [!NOTE]
> La classe `WKWebView` è supportata solo in iOS 8 e versioni successive.

È anche necessario aggiornare **Info.plist** in modo da includere i valori seguenti:

```
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

### <a name="creating-the-custom-renderer-on-android"></a>Creazione del renderer personalizzato in Android

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                var webView = new Android.Webkit.WebView(_context);
                webView.Settings.JavaScriptEnabled = true;
                webView.SetWebViewClient(new JavascriptWebViewClient($"javascript: {JavascriptFunction}"));
                SetNativeControl(webView);
            }
            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }
            if (e.NewElement != null)
            {
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{Element.Uri}");
            }
        }
    }
}
```

La classe `HybridWebViewRenderer` carica la pagina Web specificata nella proprietà `HybridWebView.Uri` in un controllo [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) nativo e la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web, al termine del caricamento della pagina Web, con override di `OnPageFinished` nella classe `JavascriptWebViewClient`:

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

- Posto che la proprietà `Control` sia `null`, vengono eseguite le operazioni seguenti:
  - Viene creata un'istanza [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) nativa, JavaScript viene abilitato nel controllo e un'istanza di `JavascriptWebViewClient` viene impostata come implementazione di `WebViewClient`.
  - Viene chiamato il metodo `SetNativeControl` per assegnare un riferimento al controllo nativo [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) alla proprietà `Control`.
- Se il renderer personalizzato è associato a un nuovo elemento di Xamarin.Forms:
  - Il metodo [`WebView.AddJavascriptInterface`](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/) inserisce una nuova istanza di `JSBridge` nel frame principale del contesto JavaScript di WebView, denominandola `jsBridge`. In questo modo è possibile accedere ai metodi presenti nella classe `JSBridge` da JavaScript.
  - Il metodo [`WebView.LoadUrl`](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/) carica il file HTML specificato dalla proprietà `HybridWebView.Uri`. Il codice specifica che il file è archiviato nella cartella `Content` del progetto.
  - Nella classe `JavascriptWebViewClient` la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web al termine del caricamento della pagina.
- Quando l'elemento a cui è associato il renderer cambia:
  - Le risorse vengono rilasciate.

Quando viene eseguita, la funzione JavaScript `invokeCSharpAction` a sua volta richiama il metodo `JSBridge.InvokeAction`, come illustra l'esempio di codice seguente:

```csharp
public class JSBridge : Java.Lang.Object
{
  readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

  public JSBridge (HybridWebViewRenderer hybridRenderer)
  {
    hybridWebViewRenderer = new WeakReference <HybridWebViewRenderer> (hybridRenderer);
  }

  [JavascriptInterface]
  [Export ("invokeAction")]
  public void InvokeAction (string data)
  {
    HybridWebViewRenderer hybridRenderer;

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer))
    {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

La classe deve derivare da `Java.Lang.Object` e i metodi esposti a JavaScript devono essere decorati con gli attributi `[JavascriptInterface]` e `[Export]`. Di conseguenza, quando la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web ed eseguita, chiama il metodo `JSBridge.InvokeAction` poiché è decorata con gli attributi `[JavascriptInterface]` e `[Export("invokeAction")]`. A sua volta, il metodo `InvokeAction` richiama il metodo `HybridWebView.InvokeAction`, che richiamerà l'azione registrata per visualizzare l'elemento popup.

> [!NOTE]
> I progetti che usano l'attributo `[Export]` devono includere un riferimento a `Mono.Android.Export` o si verificherà un errore del compilatore.

Si noti che la classe `JSBridge` mantiene un elemento `WeakReference` per la classe `HybridWebViewRenderer`, per evitare la creazione di un riferimento circolare tra le due classi. Per altre informazioni, vedere [Riferimenti deboli](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx) in MSDN.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creazione del renderer personalizzato in UWP

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Windows.UI.Xaml.Controls.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                SetNativeControl(new Windows.UI.Xaml.Controls.WebView());
            }
            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri(string.Format("ms-appx-web:///Content//{0}", Element.Uri));
            }
        }

        async void OnWebViewNavigationCompleted(WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            Element.InvokeAction(e.Value);
        }
    }
}
```

La classe `HybridWebViewRenderer` carica la pagina Web specificata nella proprietà `HybridWebView.Uri` in un controllo `WebView` nativo e la funzione JavaScript `invokeCSharpAction` viene inserita nella pagina Web, al termine del caricamento della pagina, con il metodo `WebView.InvokeScriptAsync`. Dopo che l'utente immette il proprio nome e fa clic sull'elemento HTML `button`, la funzione JavaScript `invokeCSharpAction` viene eseguita, con il metodo `OnWebViewScriptNotify` chiamato dopo che viene ricevuta una notifica dalla pagina Web. A sua volta, questo metodo richiama il metodo `HybridWebView.InvokeAction`, che richiama l'azione registrata per visualizzare l'elemento popup.

Questa funzionalità si rende disponibile come indicato di seguito:

- Posto che la proprietà `Control` sia `null`, vengono eseguite le operazioni seguenti:
  - Viene chiamato il metodo `SetNativeControl` per creare un'istanza di un nuovo controllo `WebView` nativo e assegnare un riferimento al controllo alla proprietà `Control`.
- Se il renderer personalizzato è associato a un nuovo elemento di Xamarin.Forms:
  - I gestori eventi per gli eventi `NavigationCompleted` e `ScriptNotify` sono registrati. L'evento `NavigationCompleted` viene attivato quando il controllo nativo `WebView` ha completato il caricamento del contenuto corrente o se la navigazione ha avuto esito negativo. L'evento `ScriptNotify` viene attivato quando il contenuto nel controllo nativo `WebView` usa JavaScript per passare una stringa all'applicazione. La pagina Web genera l'evento `ScriptNotify` chiamando `window.external.notify` mentre passa un parametro `string`.
  - La proprietà `WebView.Source` viene impostata sull'URI del file HTML specificato dalla proprietà `HybridWebView.Uri`. Il codice presuppone che il file sia archiviato nella cartella `Content` del progetto. Dopo che è stata visualizzata la pagina Web, viene attivato l'evento `NavigationCompleted` e viene richiamato il metodo `OnWebViewNavigationCompleted`. La funzione JavaScript `invokeCSharpAction` viene quindi inserita nella pagina Web con il metodo `WebView.InvokeScriptAsync`, a condizione che la navigazione sia stata completata correttamente.
- Quando l'elemento a cui è associato il renderer cambia:
  - Gli eventi vengono annullati.

## <a name="summary"></a>Riepilogo

Questo articolo ha spiegato come creare un renderer personalizzato per un controllo personalizzato `HybridWebView`, che dimostra come ottimizzare i controlli Web specifici della piattaforma per consentire al codice C# di essere richiamato da JavaScript.


## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererHybridWebView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [Call C# from JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) (Chiamare C# da JavaScript)
