---
title: Implementazione di un HybridWebView
description: Xamarin. Forms controlli dell'interfaccia utente personalizzata devono derivare dalla classe di visualizzazione, viene utilizzata per posizionare i controlli nella schermata e layout. In questo articolo viene illustrato come creare un renderer personalizzato per un controllo personalizzato HybridWebView, cui viene illustrato come migliorare i controlli specifici della piattaforma web per consentire al codice c# di essere richiamato da JavaScript.
ms.topic: article
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: e67646e5072f703af71fc3f0a7901fd8485f9710
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="implementing-a-hybridwebview"></a>Implementazione di un HybridWebView

_Xamarin. Forms controlli dell'interfaccia utente personalizzata devono derivare dalla classe di visualizzazione, viene utilizzata per posizionare i controlli nella schermata e layout. In questo articolo viene illustrato come creare un renderer personalizzato per un controllo personalizzato HybridWebView, cui viene illustrato come migliorare i controlli specifici della piattaforma web per consentire al codice c# di essere richiamato da JavaScript._

Tutte le viste di xamarin. Forms sono un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) viene eseguito il rendering da un'applicazione di xamarin. Forms in iOS, il `ViewRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza nativa `UIView` controllo. Nella piattaforma Android, il `ViewRenderer` crea un'istanza di classe un `View` controllo. In Windows Phone e di Windows della piattaforma UWP (Universal), il `ViewRenderer` un'istanza nativa `FrameworkElement` controllo. Per ulteriori informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer classi di Base e i controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) e i corrispondenti controlli nativi che l'implementano:

![](hybridwebview-images/view-classes.png "Relazione tra la classe di visualizzazione e le relative classi Native di implementazione")

Il processo di rendering può essere utilizzato per implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) in ciascuna piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_HybridWebView) il `HybridWebView` controllo personalizzato.
1. [Utilizzare](#Consuming_the_HybridWebView) il `HybridWebView`da xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per il `HybridWebView` in ciascuna piattaforma.

Ogni elemento ora essere descritti in dettaglio per implementare un `HybridWebView` renderer che migliora i controlli specifici della piattaforma web per consentire al codice c# di essere richiamato da JavaScript. Il `HybridWebView` istanza verrà utilizzata per visualizzare una pagina HTML in cui viene chiesto all'utente di immettere il proprio nome. Quindi, quando l'utente fa clic sul pulsante HTML, verrà richiamare una funzione JavaScript in c# `Action` che visualizza una finestra popup contenente il nome degli utenti.

Per ulteriori informazioni sul processo di chiamata c# da JavaScript, vedere [richiamare c# da JavaScript](#Invoking_C_from_JavaScript). Per ulteriori informazioni sulla pagina HTML, vedere [creazione di una pagina Web](#Creating_the_Web_Page).

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>Creazione di HybridWebView

Il `HybridWebView` controllo personalizzato è possibile creare tramite la creazione di sottoclassi di [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) classe, come illustrato nell'esempio di codice seguente:

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

Il `HybridWebView` controllo personalizzato viene creato nel progetto di libreria (PCL) di classi portabile e definisce l'API per il controllo seguente:

- Oggetto `Uri` proprietà che specifica l'indirizzo della pagina web da caricare.
- Oggetto `RegisterAction` metodo che registra un `Action` con il controllo. Viene richiamato l'azione registrata da JavaScript contenute nel file HTML a cui fa riferimento tramite il `Uri` proprietà.
- Oggetto `CleanUp` metodo che rimuove il riferimento registrato `Action`.
- Un `InvokeAction` metodo che richiama registrato `Action`. Questo metodo verrà chiamato da un renderer personalizzato in ogni progetto specifico della piattaforma.

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>Utilizzo di HybridWebView

Il `HybridWebView` controllo personalizzato può fare riferimento in XAML nel progetto libreria di classi Portabile dichiarare uno spazio dei nomi per la posizione e utilizzando il prefisso dello spazio dei nomi del controllo personalizzato. Nell'esempio di codice riportato di seguito viene illustrato come la `HybridWebView` controllo personalizzato può essere utilizzato da una pagina XAML:

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

Il `local` prefisso dello spazio dei nomi denominato nulla. Tuttavia, il `clr-namespace` e `assembly` i valori devono corrispondere i dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene utilizzato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come la `HybridWebView` controllo personalizzato può essere utilizzato da una pagina in c#:

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

Il `HybridWebView` istanza verrà utilizzata per visualizzare un controllo web nativi in ciascuna piattaforma. Ha `Uri` proprietà è impostata su un file HTML archiviato in ogni progetto specifico della piattaforma e che verrà visualizzato dal controllo web nativi. Il codice HTML viene chiesto all'utente di immettere il proprio nome, con una funzione JavaScript chiamata c# `Action` in risposta a un clic del pulsante HTML.

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

Questa azione chiama il [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) metodo per visualizzare una finestra popup modale che presenta il nome immesso nella pagina HTML visualizzata per il `HybridWebView` istanza.

Un renderer personalizzato può ora essere aggiunto a ogni progetto di applicazione per migliorare i controlli specifici della piattaforma web, consentendo di codice c# per essere richiamato da JavaScript.

<a nane="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione di Renderer personalizzato a seconda della piattaforma

Il processo di creazione della classe renderer personalizzato è come segue:

1. Creare una sottoclasse di `ViewRenderer<T1,T2>` classe che esegue il rendering del controllo personalizzato. Il primo argomento di tipo deve essere il controllo personalizzato, il renderer è, in questo caso `HybridWebView`. Il secondo argomento di tipo deve essere il controllo nativo che consente di implementare la visualizzazione personalizzata.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering di controllo e scrivere la logica personalizzata per personalizzarlo. Questo metodo viene chiamato quando viene creato il controllo personalizzato di xamarin. Forms corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per il rendering del controllo personalizzato di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> Per la maggior parte degli elementi di xamarin. Forms, è facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, verrà utilizzato il renderer predefinito per la classe di base del controllo. Un renderer personalizzato è tuttavia necessarie in ogni progetto della piattaforma per il rendering di un [vista](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) elemento.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](hybridwebview-images/solution-structure.png "Responsabilità di progetto personalizzato HybridWebView Renderer")

Il `HybridWebView` viene eseguito il rendering di controlli personalizzati dalle classi renderer specifico della piattaforma, che derivano dalla `ViewRenderer` classe per ogni piattaforma. Di conseguenza, ogni `HybridWebView` controllo personalizzato viene eseguito il rendering con controlli specifici della piattaforma web, come illustrato nelle schermate seguenti:

![](hybridwebview-images/screenshots.png "HybridWebView in ciascuna piattaforma")

Il `ViewRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creato il controllo personalizzato di xamarin. Forms per il rendering del controllo web native corrispondenti. Questo metodo accetta un `ElementChangedEventArgs` parametro contenente `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* associata rispettivamente. Nell'applicazione di esempio di `OldElement` proprietà sarà `null` e `NewElement` proprietà contiene un riferimento al `HybridWebView` istanza.

Una versione sottoposta a override del `OnElementChanged` (metodo), in ogni classe renderer specifico della piattaforma, è possibile eseguire la creazione di istanze di web nativi controllo e personalizzazione. Il `SetNativeControl` (metodo) deve essere utilizzato per creare il controllo web nativi e questo metodo assegna anche il riferimento al controllo per il `Control` proprietà. Inoltre, un riferimento al controllo di xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà.

In alcuni casi il `OnElementChanged` metodo può essere chiamato più volte. Pertanto, per evitare perdite di memoria, prestare attenzione quando si crea un nuovo controllo nativo. L'approccio da usare quando si crea un'istanza di un nuovo controllo nativo in un renderer personalizzato è illustrato nell'esempio di codice seguente:

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

Per un nuovo controllo nativo l'istanza deve essere creata solo una volta, quando la proprietà `Control` è `null`. Configurare il controllo e sottoscrivere i gestori degli eventi solo quando il renderer personalizzato è allegato a un nuovo elemento di Xamarin.Forms. Analogamente, per i gestori degli eventi sottoscritti l'iscrizione deve essere annullata solo in caso di modifica dell'elemento allegato al renderer. Questo approccio consente di creare un renderer personalizzato ad alte prestazioni che non subisca delle perdite di memoria.

Ogni classe renderer personalizzato è decorato con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo del controllo personalizzato xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo si applica all'intero assembly.

Le sezioni seguenti illustrano la struttura della pagina web caricata da ogni controllo web nativi, il processo di chiamata c# da JavaScript e l'implementazione di questo oggetto in ogni classe renderer personalizzato specifico della piattaforma.

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>Creazione di una pagina Web

Esempio di codice seguente mostra la pagina web che verranno visualizzate le `HybridWebView` controllo personalizzato:

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

La pagina web consente all'utente di immettere il proprio nome in un `input` elemento e fornisce un `button` elemento che richiama il codice c# quando si fa clic. Il processo per raggiungere questo obiettivo è come segue:

- Quando l'utente fa clic su di `button` elemento, il `invokeCSCode` funzione JavaScript viene chiamata con il valore della `input` elemento passato alla funzione.
- Il `invokeCSCode` chiamate di funzione di `log` funzione per visualizzare i dati di invio alla c# `Action`. Chiama quindi il `invokeCSharpAction` metodo da richiamare c# `Action`, passando il parametro ricevuto dal `input` elemento.

Il `invokeCSharpAction` funzione JavaScript non è definito nella pagina web e verrà inserita al suo interno per ogni renderer personalizzato.

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>Richiamo di c# da JavaScript

Il processo per richiamare c# da JavaScript è identico in ogni piattaforma:

- Il renderer personalizzato viene creato un controllo web nativi e carica il file HTML specificato da di `HybridWebView.Uri` proprietà.
- Una volta caricata la pagina web, il renderer personalizzato inserisce le `invokeCSharpAction` funzione JavaScript nella pagina web.
- Quando l'utente immette il nome e fa clic su HTML `button` elemento, il `invokeCSCode` funzione viene richiamata, che a sua volta richiama il `invokeCSharpAction` (funzione).
- Il `invokeCSharpAction` funzione richiama un metodo nel renderer del personalizzato, che a sua volta richiama il `HybridWebView.InvokeAction` metodo.
- Il `HybridWebView.InvokeAction` metodo richiama registrato `Action`.

Nelle sezioni seguenti verranno illustrate come questo processo viene implementato in ogni piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione di Renderer personalizzato in iOS

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma iOS:

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

Il `HybridWebViewRenderer` classe carica la pagina web specificata nel `HybridWebView.Uri` proprietà nativo [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) (controllo) e `invokeCSharpAction` funzione JavaScript viene inserito nella pagina web. Dopo che l'utente immette il nome e fa clic su HTML `button` elemento, il `invokeCSharpAction` funzione JavaScript viene eseguito, con la `DidReceiveScriptMessage` metodo chiamato dopo che viene ricevuto un messaggio dalla pagina web. A sua volta, questo metodo richiama il `HybridWebView.InvokeAction` metodo che richiama l'azione registrato per visualizzare la finestra popup.

Questa funzionalità viene ottenuta nel modo seguente:

- Purché il `Control` proprietà `null`, vengono eseguite le operazioni seguenti:
  - Oggetto [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) viene creata l'istanza, che consente l'invio di messaggi e l'inserimento di script dell'utente in una pagina web.
  - Oggetto [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) istanza viene creata per inserire il `invokeCSharpAction` funzione JavaScript nella pagina web dopo il caricamento della pagina web.
  - Il [ `WKUserContentController.AddScript` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddUserScript/p/WebKit.WKUserScript/) metodo aggiunge il [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) istanza al controller di contenuto.
  - Il [ `WKUserContentController.AddScriptMessageHandler` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddScriptMessageHandler/p/WebKit.IWKScriptMessageHandler/System.String/) metodo aggiunge un gestore di messaggi di script denominato `invokeAction` per il [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) istanza, che provoca la funzione JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` da definire in tutto i frame in tutte le visualizzazioni web che verranno utilizzato il `WKUserContentController` istanza.
  - Oggetto [ `WKWebViewConfiguration` ](https://developer.xamarin.com/api/type/WebKit.WKWebViewConfiguration/) viene creata l'istanza, con la [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) istanza viene impostata come controller di contenuto.
  - A [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) viene creata un'istanza di controllo e `SetNativeControl` metodo viene chiamato per assegnare un riferimento al `WKWebView` controllo il `Control` proprietà.
- Condizione che il renderer personalizzato è associato a un nuovo elemento di xamarin. Forms:
  - Il [ `WKWebView.LoadRequest` ](https://developer.xamarin.com/api/member/WebKit.WKWebView.LoadRequest/p/Foundation.NSUrlRequest/) metodo carica il file HTML specificato dal `HybridWebView.Uri` proprietà. Il codice specifica che il file è archiviato nel `Content` nella cartella del progetto. Dopo aver visualizzato la pagina web, il `invokeCSharpAction` funzione JavaScript verrà inserita nella pagina web.
- Quando l'elemento del renderer è associato alle modifiche:
  - Le risorse vengono rilasciate.

> [!NOTE]
> La `WKWebView` classe è supportata solo in iOS 8 e versioni successive.

### <a name="creating-the-custom-renderer-on-android"></a>Creazione di Renderer personalizzato in Android

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma Android:

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

Il `HybridWebViewRenderer` classe carica la pagina web specificata nel `HybridWebView.Uri` proprietà nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) (controllo) e `invokeCSharpAction` funzione JavaScript viene inserito nella pagina web, dopo aver caricato la pagina web, con il `InjectJS` metodo. Dopo che l'utente immette il nome e fa clic su HTML `button` elemento, il `invokeCSharpAction` viene eseguita la funzione JavaScript. Questa funzionalità viene ottenuta nel modo seguente:

- Purché il `Control` proprietà `null`, vengono eseguite le operazioni seguenti:
  - Nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) istanza viene creata e JavaScript è abilitato nel controllo.
  - Il `SetNativeControl` metodo viene chiamato per assegnare un riferimento nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) controllo il `Control` proprietà.
- Condizione che il renderer personalizzato è associato a un nuovo elemento di xamarin. Forms:
  - Il [ `WebView.AddJavascriptInterface` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/) inserisce un nuovo metodo `JSBridge` istanza nel frame principale del contesto di JavaScript di WebView, denominarla `jsBridge`. In questo modo i metodi di `JSBridge` classe a cui accedere da JavaScript.
  - Il [ `WebView.LoadUrl` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/) metodo carica il file HTML specificato dal `HybridWebView.Uri` proprietà. Il codice specifica che il file è archiviato nel `Content` nella cartella del progetto.
  - Il `InjectJS` metodo viene richiamato per inserire il `invokeCSharpAction` funzione JavaScript nella pagina web.
- Quando l'elemento del renderer è associato alle modifiche:
  - Le risorse vengono rilasciate.

Quando il `invokeCSharpAction` funzione JavaScript viene eseguito, a sua volta richiama il `JSBridge.InvokeAction` (metodo), come illustrato nell'esempio di codice seguente:

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

La classe deve derivare da `Java.Lang.Object`, e metodi esposti a JavaScript devono essere provvisto di `[JavascriptInterface]` e `[Export]` gli attributi. Pertanto, quando il `invokeCSharpAction` funzione JavaScript viene inserito nella pagina web e viene eseguito, verrà chiamato il `JSBridge.InvokeAction` metodo a causa di essere decorata con il `[JavascriptInterface]` e `[Export("invokeAction")]` gli attributi. A sua volta, il `InvokeAction` metodo richiama il `HybridWebView.InvokeAction` metodo che verrà richiamato l'azione registrato per visualizzare la finestra popup.

> [!NOTE]
> I progetti che utilizzano il `[Export]` attributo deve includere un riferimento a `Mono.Android.Export`, o si verificherà un errore del compilatore.

Si noti che il `JSBridge` classe mantiene una `WeakReference` per la `HybridWebViewRenderer` classe. Questo permette di evitare la creazione di un riferimento circolare tra le due classi. Per ulteriori informazioni vedere [riferimenti deboli](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx) su MSDN.

> [!IMPORTANT]
> In Android Oreo assicurarsi che il manifesto Android imposta il **versione di destinazione Android** a **automatica**. In caso contrario, l'esecuzione del codice verrà generato l'errore messaggio "invokeCSharpAction non è definito".

### <a name="creating-the-custom-renderer-on-windows-phone-and-uwp"></a>Creazione di Renderer personalizzato in Windows Phone e UWP

Esempio di codice seguente viene illustrato il renderer personalizzato per Windows Phone e UWP:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.WinPhone81
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

Il `HybridWebViewRenderer` classe carica la pagina web specificata nel `HybridWebView.Uri` proprietà nativo `WebView` (controllo) e `invokeCSharpAction` funzione JavaScript viene inserito nella pagina web, dopo aver caricato la pagina web, con il `WebView.InvokeScriptAsync` metodo. Dopo che l'utente immette il nome e fa clic su HTML `button` elemento, il `invokeCSharpAction` funzione JavaScript viene eseguito, con la `OnWebViewScriptNotify` metodo chiamato dopo che viene ricevuta una notifica dalla pagina web. A sua volta, questo metodo richiama il `HybridWebView.InvokeAction` metodo che richiama l'azione registrato per visualizzare la finestra popup.

Questa funzionalità viene ottenuta nel modo seguente:

- Purché il `Control` proprietà `null`, vengono eseguite le operazioni seguenti:
  - Il `SetNativeControl` metodo viene chiamato per creare un'istanza nuova nativo `WebView` controllare e assegnare un riferimento a questo argomento per il `Control` proprietà.
- Condizione che il renderer personalizzato è associato a un nuovo elemento di xamarin. Forms:
  - Gestori eventi per il `NavigationCompleted` e `ScriptNotify` gli eventi vengono registrati. Il `NavigationCompleted` evento viene generato quando il nativo `WebView` controllo ha terminato il caricamento del contenuto corrente o se navigazione ha esito negativo. Il `ScriptNotify` evento viene generato quando il contenuto nativo `WebView` controllo utilizza JavaScript per passare una stringa per l'applicazione. L'attivazione della pagina web di `ScriptNotify` evento chiamando `window.external.notify` durante il passaggio di un `string` parametro.
  - Il `WebView.Source` proprietà è impostata per l'URI del file HTML specificato dal `HybridWebView.Uri` proprietà. Nel codice si presuppone che il file è archiviato nel `Content` nella cartella del progetto. Dopo aver visualizzato la pagina web, il `NavigationCompleted` evento verrà generato e `OnWebViewNavigationCompleted` metodo verrà richiamato. Il `invokeCSharpAction` funzione JavaScript verrà quindi inserita nella pagina web con il `WebView.InvokeScriptAsync` (metodo), a condizione che la navigazione completata.
- Quando l'elemento del renderer è associato alle modifiche:
  - Gli eventi sono annullati da.

## <a name="summary"></a>Riepilogo

In questo articolo ha illustrato come creare un renderer personalizzato per un `HybridWebView` controllo personalizzato, che viene descritto come migliorare i controlli specifici della piattaforma web per consentire al codice c# di essere richiamato da JavaScript.


## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererHybridWebView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [Chiamare c# da JavaScript](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript/)
