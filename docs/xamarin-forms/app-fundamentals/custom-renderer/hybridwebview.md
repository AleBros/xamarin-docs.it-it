---
title: Implementazione di un elemento HybridWebView
description: Questo articolo illustra come creare un renderer personalizzato per un controllo personalizzato elemento HybridWebView, che illustra come migliorare i controlli specifici della piattaforma web per consentire al codice c# di essere richiamato da JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: f0b21277b91c44edbb574aece92664de2e49a65a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996360"
---
# <a name="implementing-a-hybridwebview"></a>Implementazione di un elemento HybridWebView

_Xamarin. Forms controlli dell'interfaccia utente personalizzata devono derivare dalla classe di visualizzazione, che viene usata per posizionare i layout e controlli sullo schermo. Questo articolo illustra come creare un renderer personalizzato per un controllo personalizzato elemento HybridWebView, che illustra come migliorare i controlli specifici della piattaforma web per consentire al codice c# di essere richiamato da JavaScript._

Tutte le visualizzazioni di xamarin. Forms sono un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `View` ](xref:Xamarin.Forms.View) viene eseguito il rendering da un'applicazione xamarin. Forms in iOS, il `ViewRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza di nativo `UIView` controllo. La piattaforma Android, il `ViewRenderer` crea un'istanza di classe un `View` controllo. In Universal Windows Platform (UWP), il `ViewRenderer` un'istanza nativa `FrameworkElement` controllo. Per altre informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer di classi di Base e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `View` ](xref:Xamarin.Forms.View) e i corrispondenti controlli nativi che l'implementano:

![](hybridwebview-images/view-classes.png "Relazione tra la classe di visualizzazione e relativa implementazione delle classi Native")

Il processo di rendering può essere usato per implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `View` ](xref:Xamarin.Forms.View) in ogni piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_HybridWebView) il `HybridWebView` controllo personalizzato.
1. [Consumano](#Consuming_the_HybridWebView) il `HybridWebView`da xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per il `HybridWebView` in ogni piattaforma.

Ogni elemento ora verrà a sua volta descritta per implementare un `HybridWebView` renderer che migliora i controlli specifici della piattaforma web per consentire al codice c# di essere richiamato da JavaScript. Il `HybridWebView` istanza verrà usata per visualizzare una pagina HTML in cui viene chiesto all'utente di immettere il proprio nome. Quindi, quando l'utente fa clic sul pulsante HTML, una funzione JavaScript richiamerà c# `Action` che visualizza una finestra popup contenente il nome degli utenti.

Per altre informazioni sul processo per richiamare c# da JavaScript, vedere [richiamare c# da JavaScript](#Invoking_C_from_JavaScript). Per altre informazioni sulla pagina HTML, vedere [creazione di una pagina Web](#Creating_the_Web_Page).

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>Creare l'elemento HybridWebView

Il `HybridWebView` controllo personalizzato può essere creato dalla creazione di una sottoclasse di [ `View` ](xref:Xamarin.Forms.View) classe, come illustrato nell'esempio di codice seguente:

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

Il `HybridWebView` controllo personalizzato viene creato nel progetto della libreria .NET Standard e definisce l'API per il controllo seguente:

- Oggetto `Uri` proprietà che specifica l'indirizzo della pagina web da caricare.
- Oggetto `RegisterAction` metodo che registra un `Action` con il controllo. L'azione registrata verrà richiamato da JavaScript sul contenuto nel file HTML mediante il `Uri` proprietà.
- Oggetto `CleanUp` metodo che rimuove il riferimento a registrato `Action`.
- Un' `InvokeAction` metodo che richiama l'oggetto registrato `Action`. Questo metodo verrà chiamato da un renderer personalizzato in ogni progetto specifico della piattaforma.

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>Utilizza l'elemento HybridWebView

Il `HybridWebView` controllo personalizzato è reperibile in XAML nel progetto della libreria .NET Standard dichiara uno spazio dei nomi per la relativa posizione e usando il prefisso dello spazio dei nomi del controllo personalizzato. Nell'esempio di codice riportato di seguito viene illustrato come il `HybridWebView` controlli personalizzati possono essere usati da una pagina XAML:

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

Il `local` prefisso dello spazio dei nomi può essere denominata alcuna operazione. Tuttavia, il `clr-namespace` e `assembly` valori devono corrispondere i dettagli del controllo personalizzato. Una volta lo spazio dei nomi è dichiarato, il prefisso viene utilizzato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come il `HybridWebView` controlli personalizzati possono essere usati da una pagina di c#:

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

Il `HybridWebView` istanza verrà usata per visualizzare un controllo web nativi in ciascuna piattaforma. Ha `Uri` è impostata su un file HTML che viene archiviato in ogni progetto specifico della piattaforma e che verrà visualizzato dal controllo web nativi. Il codice HTML sottoposto a rendering chiede all'utente di immettere il proprio nome, con una funzione JavaScript richiamare c# `Action` in risposta a un clic sul pulsante HTML.

Il `HybridWebViewPage` registra l'azione da richiamare da JavaScript, come illustrato nell'esempio di codice seguente:

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

Questa azione chiama il [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) metodo per visualizzare una finestra popup modale che presenta il nome immesso nella pagina HTML visualizzata per il `HybridWebView` istanza.

Un renderer personalizzato può ora essere aggiunti a ogni progetto di applicazione per migliorare i controlli specifici della piattaforma web, consentendo a codice c# per essere richiamato da JavaScript.

<a nane="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creare il Renderer personalizzato a seconda della piattaforma

Il processo per la creazione della classe renderer personalizzati è come segue:

1. Creare una sottoclasse del `ViewRenderer<T1,T2>` classe che esegue il rendering del controllo personalizzato. Il primo argomento tipo deve essere il controllo personalizzato in questo caso il renderer è, `HybridWebView`. Il secondo argomento tipo deve essere del controllo nativo che implementerà la visualizzazione personalizzata.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering il controllo e scrivere la logica personalizzata per personalizzarla. Questo metodo viene chiamato quando viene creato il controllo personalizzato di xamarin. Forms corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per eseguire il rendering del controllo personalizzato di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> Per la maggior parte degli elementi di xamarin. Forms, è facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, quindi verrà utilizzato il renderer predefinito per la classe di base del controllo. Renderer personalizzati sono tuttavia necessarie in ogni progetto della piattaforma durante il rendering di un [vista](xref:Xamarin.Forms.View) elemento.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](hybridwebview-images/solution-structure.png "Responsabilità di progetto Renderer personalizzati elemento HybridWebView")

Il `HybridWebView` viene eseguito il rendering di controlli personalizzati dalle classi renderer specifica della piattaforma, che derivano da tutti i `ViewRenderer` classe per ogni piattaforma. Di conseguenza ogni `HybridWebView` controllo personalizzato viene eseguito il rendering con controlli specifici della piattaforma web, come illustrato negli screenshot seguenti:

![](hybridwebview-images/screenshots.png "Elemento HybridWebView in ogni piattaforma")

Il `ViewRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creato il controllo personalizzato di xamarin. Forms per il rendering del controllo web native corrispondenti. Questo metodo accetta un `ElementChangedEventArgs` parametro che contiene `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* collegati rispettivamente. Nell'applicazione di esempio il `OldElement` proprietà sarà `null` e il `NewElement` proprietà conterrà un riferimento al `HybridWebView` istanza.

Una versione override del `OnElementChanged` metodo, in ogni classe renderer specifica della piattaforma, è possibile eseguire la creazione di istanze di web nativi controllo e personalizzazione. Il `SetNativeControl` metodo deve essere usato per creare un'istanza del controllo web native e questo metodo si assegna il riferimento di controllo per il `Control` proprietà. Inoltre, un riferimento al controllo di xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà.

In alcune circostanze il `OnElementChanged` metodo può essere chiamato più volte. Pertanto, per evitare perdite di memoria, è necessario prestare attenzione quando si crea un nuovo controllo nativo. L'approccio da usare quando si crea un'istanza di un nuovo controllo nativo in un renderer personalizzato è illustrato nell'esempio di codice seguente:

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

Per un nuovo controllo nativo l'istanza deve essere creata solo una volta, quando la proprietà `Control` è `null`. Configurare il controllo e sottoscrivere i gestori degli eventi solo quando il renderer personalizzato è allegato a un nuovo elemento di Xamarin.Forms. Analogamente, per i gestori degli eventi sottoscritti l'iscrizione deve essere annullata solo in caso di modifica dell'elemento allegato al renderer. Adottando questo approccio consente di creare un renderer personalizzato ad alte prestazioni che non subisce perdite di memoria.

Ogni classe renderer personalizzato è decorata con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo del controllo personalizzato xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano la struttura della pagina web caricato da ogni controllo web nativi, il processo per richiamare c# da JavaScript e l'implementazione di questo oggetto in ogni classe renderer personalizzato specifico della piattaforma.

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>Creazione di una pagina Web

Esempio di codice seguente mostra la pagina web che verrà visualizzato per il `HybridWebView` controllo personalizzato:

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

La pagina web consente all'utente di immettere il proprio nome in un' `input` elemento e fornisce un `button` elemento che verrà richiamato codice c# quando si fa clic. Il processo per raggiungere questo obiettivo è come segue:

- Quando l'utente fa clic sui `button` elemento, il `invokeCSCode` funzione JavaScript viene chiamata, con il valore della `input` elemento passato alla funzione.
- Il `invokeCSCode` chiamate di funzione il `log` funzione per visualizzare i dati di invio per il codice c# `Action`. Chiama poi il `invokeCSharpAction` metodo da richiamare il codice c# `Action`, passando il parametro ricevuto dal `input` elemento.

Il `invokeCSharpAction` funzione JavaScript non è definito nella pagina web e verrà inserita al suo interno per ogni renderer personalizzato.

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>Richiamo di c# da JavaScript

Il processo per richiamare c# da JavaScript è identico in ogni piattaforma:

- Il renderer personalizzato viene creato un controllo web native e carica il file HTML specificato da di `HybridWebView.Uri` proprietà.
- Dopo aver caricato la pagina web, il renderer personalizzato inserisce le `invokeCSharpAction` funzione JavaScript nella pagina web.
- Quando l'utente immette il proprio nome e fa clic sul codice HTML `button` elemento, il `invokeCSCode` funzione viene richiamata, che a sua volta richiama il `invokeCSharpAction` (funzione).
- Il `invokeCSharpAction` funzione richiama un metodo nel renderer personalizzato, che a sua volta richiama il `HybridWebView.InvokeAction` (metodo).
- Il `HybridWebView.InvokeAction` metodo richiama registrato `Action`.

Le sezioni seguenti illustreranno come questo processo viene implementato in ogni piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creare il Renderer personalizzati in iOS

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

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

Il `HybridWebViewRenderer` classe carica la pagina web specificata nel `HybridWebView.Uri` proprietà nativo [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) (controllo) e il `invokeCSharpAction` funzione JavaScript viene inserito nella pagina web. Una volta che l'utente immette il proprio nome e fa clic HTML `button` elemento, il `invokeCSharpAction` funzione JavaScript viene eseguito, con la `DidReceiveScriptMessage` metodo chiamato dopo che viene ricevuto un messaggio dalla pagina web. A sua volta, questo metodo richiama il `HybridWebView.InvokeAction` metodo che richiama l'azione registrata per visualizzare la finestra popup.

Questa funzionalità è disponibile come indicato di seguito:

- A condizione che il `Control` è di proprietà `null`, vengono eseguite le operazioni seguenti:
  - Oggetto [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) viene creata l'istanza, che consente l'invio di messaggi e l'inserimento di script dell'utente in una pagina web.
  - Oggetto [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) istanza viene creata per inserire il `invokeCSharpAction` funzione JavaScript nella pagina web dopo il caricamento della pagina web.
  - Il [ `WKUserContentController.AddScript` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddUserScript/p/WebKit.WKUserScript/) metodo aggiunge il [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) istanza al controller di contenuto.
  - Il [ `WKUserContentController.AddScriptMessageHandler` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddScriptMessageHandler/p/WebKit.IWKScriptMessageHandler/System.String/) metodo aggiunge un gestore di messaggi di script denominato `invokeAction` per il [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) istanza, che causerà la funzione JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` essere definiti in tutte le i frame in tutte le visualizzazioni web che useranno il `WKUserContentController` istanza.
  - Oggetto [ `WKWebViewConfiguration` ](https://developer.xamarin.com/api/type/WebKit.WKWebViewConfiguration/) creazione dell'istanza, con il [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) istanza impostato come controller di contenuto.
  - Oggetto [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) viene creata un'istanza di controllo e il `SetNativeControl` viene chiamato per assegnare un riferimento al `WKWebView` il controllo al `Control` proprietà.
- Condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms:
  - Il [ `WKWebView.LoadRequest` ](https://developer.xamarin.com/api/member/WebKit.WKWebView.LoadRequest/p/Foundation.NSUrlRequest/) metodo carica il file HTML specificato da di `HybridWebView.Uri` proprietà. Il codice specifica che il file è archiviato nel `Content` cartella del progetto. Una volta che viene visualizzata la pagina web, il `invokeCSharpAction` funzione JavaScript verrà inserito nella pagina web.
- Quando l'elemento del renderer è associato alle modifiche:
  - Le risorse vengono rilasciate.

> [!NOTE]
> Il `WKWebView` classe è supportata solo in iOS 8 e versioni successive.

### <a name="creating-the-custom-renderer-on-android"></a>Creare il Renderer personalizzati in Android

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
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
                Control.LoadUrl(string.Format("file:///android_asset/Content/{0}", Element.Uri));
                InjectJS(JavaScriptFunction);
            }
        }

        void InjectJS(string script)
        {
            if (Control != null)
            {
                Control.LoadUrl(string.Format("javascript: {0}", script));
            }
        }
    }
}
```

Il `HybridWebViewRenderer` classe carica la pagina web specificata nel `HybridWebView.Uri` proprietà nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) (controllo) e il `invokeCSharpAction` funzione JavaScript viene inserito nella pagina web, dopo aver caricato la pagina web, con la `InjectJS` (metodo). Una volta che l'utente immette il proprio nome e fa clic HTML `button` elemento, il `invokeCSharpAction` funzione JavaScript viene eseguito. Questa funzionalità è disponibile come indicato di seguito:

- A condizione che il `Control` è di proprietà `null`, vengono eseguite le operazioni seguenti:
  - Nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) istanza viene creata e JavaScript è abilitato nel controllo.
  - Il `SetNativeControl` viene chiamato per assegnare un riferimento all'oggetto nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) controllo il `Control` proprietà.
- Condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms:
  - Il [ `WebView.AddJavascriptInterface` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/) metodo inserisce un nuovo `JSBridge` istanza nel frame principale del contesto di JavaScript di WebView, denominarlo `jsBridge`. In questo modo i metodi nel `JSBridge` classe a cui accedere da JavaScript.
  - Il [ `WebView.LoadUrl` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/) metodo carica il file HTML specificato da di `HybridWebView.Uri` proprietà. Il codice specifica che il file è archiviato nel `Content` cartella del progetto.
  - Il `InjectJS` metodo viene richiamato per l'inserimento di `invokeCSharpAction` funzione JavaScript nella pagina web.
- Quando l'elemento del renderer è associato alle modifiche:
  - Le risorse vengono rilasciate.

Quando la `invokeCSharpAction` funzione JavaScript viene eseguito, a sua volta richiama il `JSBridge.InvokeAction` metodo, che viene visualizzato nell'esempio di codice seguente:

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

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer)) {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

La classe deve derivare da `Java.Lang.Object`, e i metodi esposti a JavaScript devono essere decorati con il `[JavascriptInterface]` e `[Export]` attributi. Pertanto, quando la `invokeCSharpAction` funzione JavaScript viene inserito nella pagina web e viene eseguito, verrà chiamato il `JSBridge.InvokeAction` metodo a causa di essere decorata con il `[JavascriptInterface]` e `[Export("invokeAction")]` attributi. A sua volta, il `InvokeAction` metodo richiama il `HybridWebView.InvokeAction` metodo, di cui verrà richiamato l'azione registrata per visualizzare la finestra popup.

> [!NOTE]
> I progetti che utilizzano le `[Export]` attributo deve includere un riferimento a `Mono.Android.Export`, o si verificherà un errore del compilatore.

Si noti che il `JSBridge` classe mantiene una `WeakReference` per il `HybridWebViewRenderer` classe. Questo serve a evitare la creazione di un riferimento circolare tra le due classi. Per altre informazioni, vedere [riferimenti deboli](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx) su MSDN.

> [!IMPORTANT]
> In Android Oreo assicurarsi che i set di manifesto Android il **Target Android version** al **automatica**. In caso contrario, eseguire questo codice verrà generato l'errore messaggio "invokeCSharpAction non è definito".

### <a name="creating-the-custom-renderer-on-uwp"></a>Creare il Renderer personalizzato nella piattaforma UWP

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

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

Il `HybridWebViewRenderer` classe carica la pagina web specificata nel `HybridWebView.Uri` proprietà nativo `WebView` (controllo) e il `invokeCSharpAction` funzione JavaScript viene inserito nella pagina web, dopo aver caricato la pagina web, con la `WebView.InvokeScriptAsync` (metodo). Una volta che l'utente immette il proprio nome e fa clic HTML `button` elemento, il `invokeCSharpAction` funzione JavaScript viene eseguito, con la `OnWebViewScriptNotify` metodo chiamato dopo che viene ricevuta una notifica della pagina web. A sua volta, questo metodo richiama il `HybridWebView.InvokeAction` metodo che richiama l'azione registrata per visualizzare la finestra popup.

Questa funzionalità è disponibile come indicato di seguito:

- A condizione che il `Control` è di proprietà `null`, vengono eseguite le operazioni seguenti:
  - Il `SetNativeControl` metodo viene chiamato per creare un'istanza di un nuovo nativo `WebView` controllano e assegnare un riferimento a esso con il `Control` proprietà.
- Condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms:
  - I gestori eventi per il `NavigationCompleted` e `ScriptNotify` gli eventi vengono registrati. Il `NavigationCompleted` evento viene generato quando il nativo `WebView` controllo ha completato il caricamento del contenuto corrente o se navigazione ha esito negativo. Il `ScriptNotify` evento viene generato quando il contenuto nativo `WebView` controllo Usa JavaScript per passare una stringa per l'applicazione. L'attivazione di pagina web di `ScriptNotify` evento chiamando `window.external.notify` mentre si passano un `string` parametro.
  - Il `WebView.Source` viene impostata per l'URI del file HTML specificato da di `HybridWebView.Uri` proprietà. Nel codice si presuppone che il file è archiviato nel `Content` cartella del progetto. Una volta che viene visualizzata la pagina web, il `NavigationCompleted` viene generato l'evento e `OnWebViewNavigationCompleted` verrà richiamato metodo. Il `invokeCSharpAction` JavaScript funzione verrà quindi inserita nella pagina web con il `WebView.InvokeScriptAsync` metodo, a condizione che la navigazione completata correttamente.
- Quando l'elemento del renderer è associato alle modifiche:
  - Gli eventi sono annullati.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un renderer personalizzato per un `HybridWebView` controllo personalizzato, che illustra come migliorare i controlli specifici della piattaforma web per consentire al codice c# di essere richiamato da JavaScript.


## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererHybridWebView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [Chiamata di codice c# da JavaScript](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript/)
