---
title: " Xamarin.Forms WebView" Description: "in questo articolo viene illustrato come usare la Xamarin.Forms classe WebView per presentare i documenti e il contenuto Web locale o di rete per gli utenti".
ms. prod: Novell MS. AssetID: E44F5D0F-DB8E-46C7-8789-114F1652A6C5 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 05/06/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-webview"></a>Xamarin.FormsWebView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView)è una visualizzazione per la visualizzazione del contenuto Web e HTML nell'app:

![Nel browser app](webview-images/in-app-browser.png)

## <a name="content"></a>Contenuto

`WebView`supporta i tipi di contenuto seguenti:

- HTML & CSS &ndash; WebView offre supporto completo per i siti Web scritti con html & CSS, incluso il supporto JavaScript.
- Documenti &ndash; poiché WebView viene implementato usando componenti nativi in ogni piattaforma, WebView è in grado di visualizzare i documenti visualizzabili in ogni piattaforma. Ciò significa che i file PDF funzionano in iOS e Android.
- Le stringhe HTML &ndash; WebView possono mostrare stringhe HTML dalla memoria.
- I file locali &ndash; WebView possono presentare i tipi di contenuto sopra incorporati nell'app.

> [!NOTE]
> `WebView`in Windows non supporta Silverlight, Flash o tutti i controlli ActiveX, anche se sono supportati da Internet Explorer su tale piattaforma.

### <a name="websites"></a>Siti Web

Per visualizzare un sito Web da Internet, impostare la `WebView` [`Source`](xref:Xamarin.Forms.WebViewSource) proprietà di su un URL stringa:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Gli URL devono essere completamente formati con il protocollo specificato (ad esempio, è necessario che sia stato anteposto "http://" o "https://").

#### <a name="ios-and-ats"></a>iOS e ATS

Dalla versione 9, iOS consentirà all'applicazione di comunicare solo con i server che implementano la sicurezza delle procedure consigliate per impostazione predefinita. `Info.plist`Per abilitare la comunicazione con server non sicuri, è necessario impostare i valori in.

> [!NOTE]
> Se l'applicazione richiede una connessione a un sito Web non sicuro, è sempre necessario immettere il dominio come eccezione usando `NSExceptionDomains` invece di disattivare completamente ATS usando `NSAllowsArbitraryLoads` . `NSAllowsArbitraryLoads`deve essere usato solo in situazioni di emergenza estreme.

Di seguito viene illustrato come abilitare un dominio specifico (in questo caso xamarin.com) per ignorare i requisiti di ATS:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSExceptionDomains</key>
        <dict>
            <key>xamarin.com</key>
            <dict>
                <key>NSIncludesSubdomains</key>
                <true/>
                <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSTemporaryExceptionMinimumTLSVersion</key>
                <string>TLSv1.1</string>
            </dict>
        </dict>
    </dict>
    ...
</key>
```

È consigliabile abilitare solo alcuni domini per ignorare ATS, in modo da usare siti attendibili, sfruttando al contempo la sicurezza aggiuntiva su domini non trusted. Di seguito viene illustrato il metodo meno sicuro per disabilitare ATS per l'app:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

Vedere [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md) per altre informazioni su questa nuova funzionalità in iOS 9.

### <a name="html-strings"></a>Stringhe HTML

Se si desidera presentare una stringa di codice HTML definito in modo dinamico nel codice, è necessario creare un'istanza di [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) :

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![Visualizzazione WebView della stringa HTML](webview-images/html-string.png)

Nel codice precedente, `@` viene usato per contrassegnare il codice HTML come [valore letterale stringa Verbatim](/dotnet/csharp/programming-guide/strings/#regular-and-verbatim-string-literals), il che significa che la maggior parte dei caratteri di escape viene ignorata.

> [!NOTE]
> Potrebbe essere necessario impostare le `WidthRequest` `HeightRequest` proprietà e del [`WebView`](xref:Xamarin.Forms.WebView) per visualizzare il contenuto HTML, a seconda del layout `WebView` di cui è figlio. Ad esempio, questa operazione è necessaria in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

### <a name="local-html-content"></a>Contenuto HTML locale

WebView può visualizzare contenuti da HTML, CSS e JavaScript incorporati all'interno dell'app. Ad esempio:

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamarin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

CSS

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

Si noti che è necessario personalizzare i tipi di carattere specificati nel CSS precedente per ogni piattaforma, in quanto non tutte le piattaforme hanno gli stessi tipi di carattere.

Per visualizzare il contenuto locale usando un `WebView` , è necessario aprire il file HTML come qualsiasi altro, quindi caricare il contenuto sotto forma di stringa nella `Html` proprietà di un oggetto `HtmlWebViewSource` . Per ulteriori informazioni sull'apertura di file, vedere [utilizzo dei file](~/xamarin-forms/data-cloud/data/files.md).

Gli screenshot seguenti mostrano il risultato della visualizzazione del contenuto locale in ogni piattaforma:

![Visualizzazione WebView del contenuto locale](webview-images/local-content.png)

Anche se la prima pagina è stata caricata, `WebView` non ha alcuna conoscenza della provenienza del codice HTML. Si tratta di un problema quando si gestiscono pagine che fanno riferimento a risorse locali. Di seguito sono riportati alcuni esempi di casi in cui potrebbe verificarsi un collegamento tra le pagine locali, una pagina che usa un file JavaScript separato o una pagina con collegamenti a un foglio di stile CSS.  

Per risolvere questo problema, è necessario indicare il percorso in `WebView` cui trovare i file nel file System. A tale scopo, impostare la `BaseUrl` proprietà sull'oggetto `HtmlWebViewSource` utilizzato dall'oggetto `WebView` .

Poiché il file System in ogni sistema operativo è diverso, è necessario determinare tale URL in ogni piattaforma. Xamarin.Formsespone `DependencyService` per la risoluzione delle dipendenze in fase di esecuzione in ogni piattaforma.

Per usare `DependencyService` , definire innanzitutto un'interfaccia che può essere implementata in ogni piattaforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Si noti che finché l'interfaccia non viene implementata in ogni piattaforma, l'app non verrà eseguita. Nel progetto comune, verificare che sia stato impostato l' `BaseUrl` utilizzo di `DependencyService` :

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

Devono quindi essere fornite le implementazioni dell'interfaccia per ogni piattaforma.

#### <a name="ios"></a>iOS

In iOS, il contenuto Web deve trovarsi nella directory radice del progetto o nella directory **delle risorse** con l'azione di compilazione *BundleResource*, come illustrato di seguito:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![File locali in iOS](webview-images/ios-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![File locali in iOS](webview-images/ios-xs.png)

-----

Il `BaseUrl` deve essere impostato sul percorso del bundle principale:

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS
{
  public class BaseUrl_iOS : IBaseUrl
  {
    public string Get()
    {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

In Android inserire HTML, CSS e immagini nella cartella assets con l'azione di compilazione *AndroidAsset* , come illustrato di seguito:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![File locali in Android](webview-images/android-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![File locali in Android](webview-images/android-xs.png)

-----

In Android, il `BaseUrl` deve essere impostato su `"file:///android_asset/"` :

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android
{
  public class BaseUrl_Android : IBaseUrl
  {
    public string Get()
    {
      return "file:///android_asset/";
    }
  }
}
```

In Android è possibile accedere ai file nella cartella **Asset** anche tramite il contesto Android corrente, esposto dalla `MainActivity.Instance` proprietà:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Nei progetti piattaforma UWP (Universal Windows Platform) (UWP) inserire HTML, CSS e immagini nella radice del progetto con l'azione di compilazione impostata su *contenuto*.

Il `BaseUrl` deve essere impostato su `"ms-appx-web:///"` :

```csharp
[assembly: Dependency(typeof(BaseUrl))]
namespace WorkingWithWebview.UWP
{
    public class BaseUrl : IBaseUrl
    {
        public string Get()
        {
            return "ms-appx-web:///";
        }
    }
}
```

## <a name="navigation"></a>Navigazione

WebView supporta la navigazione attraverso diversi metodi e proprietà che rende disponibili:

- **GoForward ()** &ndash; Se `CanGoForward` è true, `GoForward` la chiamata a passa alla pagina visitata successiva.
- **GoBack ()** &ndash; Se `CanGoBack` è true, `GoBack` la chiamata a passerà all'ultima pagina visitata.
- **CanGoBack** &ndash; `true`se ci sono pagine a cui tornare, `false` se il browser si trova all'URL iniziale.
- **CanGoForward** &ndash; `true`se l'utente ha esplorato le versioni precedenti e può andare avanti in una pagina già visitata.

All'interno di pagine, non `WebView` supporta i movimenti multitocco. È importante assicurarsi che il contenuto sia ottimizzato per dispositivi mobili e venga visualizzato senza dover eseguire lo zoom.

È normale che le applicazioni mostrino un collegamento all'interno di un `WebView` , anziché il browser del dispositivo. In questi casi, è utile consentire la navigazione normale, ma quando l'utente si trova nel collegamento iniziale, l'app deve tornare alla visualizzazione normale dell'app.

Usare i metodi e le proprietà di navigazione predefiniti per abilitare questo scenario.

Per iniziare, creare la pagina per la visualizzazione del browser:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.InAppBrowserXaml"
             Title="Browser">
    <StackLayout Margin="20">
        <StackLayout Orientation="Horizontal">
            <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="OnBackButtonClicked" />
            <Button Text="Forward" HorizontalOptions="EndAndExpand" Clicked="OnForwardButtonClicked" />
        </StackLayout>
        <!-- WebView needs to be given height and width request within layouts to render. -->
        <WebView x:Name="webView" WidthRequest="1000" HeightRequest="1000" />
    </StackLayout>
</ContentPage>
```

Nel code-behind:

```csharp
public partial class InAppBrowserXaml : ContentPage
{
    public InAppBrowserXaml(string URL)
    {
        InitializeComponent();
        webView.Source = URL;
    }

    async void OnBackButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoBack)
        {
            webView.GoBack();
        }
        else
        {
            await Navigation.PopAsync();
        }
    }

    void OnForwardButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoForward)
        {
            webView.GoForward();
        }
    }
}
```

L'operazione è stata completata.

![Pulsanti di spostamento WebView](webview-images/in-app-browser.png)

## <a name="events"></a>Eventi

WebView genera gli eventi seguenti che consentono di rispondere alle modifiche dello stato:

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating): evento generato quando la WebView inizia a caricare una nuova pagina.
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated): evento generato quando la pagina viene caricata e l'esplorazione è stata arrestata.
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested): evento generato quando viene effettuata una richiesta per ricaricare il contenuto corrente.

L' [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) oggetto che accompagna l'evento è costituito da [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) quattro proprietà:

- `Cancel`: indica se annullare o meno la navigazione.
- `NavigationEvent`: l'evento di navigazione che è stato generato.
- `Source`: elemento che ha eseguito lo spostamento.
- `Url`: destinazione di navigazione.

L' [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) oggetto che accompagna l'evento è costituito da [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) quattro proprietà:

- `NavigationEvent`: l'evento di navigazione che è stato generato.
- `Result`: descrive il risultato della navigazione, usando un [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) membro di enumerazione. I valori validi sono `Cancel`, `Failure`, `Success` e `Timeout`.
- `Source`: elemento che ha eseguito lo spostamento.
- `Url`: destinazione di navigazione.

Se si prevede di usare le pagine Web che impiegano molto tempo per il caricamento, provare a usare gli [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) eventi e per implementare un indicatore di stato. Ad esempio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.LoadingLabelXaml"
             Title="Loading Demo">
    <StackLayout>
        <!--Loading label should not render by default.-->
        <Label x:Name="labelLoading" Text="Loading..." IsVisible="false" />
        <WebView HeightRequest="1000" WidthRequest="1000" Source="http://www.xamarin.com" Navigated="webviewNavigated" Navigating="webviewNavigating" />
    </StackLayout>
</ContentPage>
```

I due gestori eventi:

```csharp
void webviewNavigating(object sender, WebNavigatingEventArgs e)
{
    labelLoading.IsVisible = true;
}

void webviewNavigated(object sender, WebNavigatedEventArgs e)
{
    labelLoading.IsVisible = false;
}
```

Viene restituito l'output seguente (caricamento):

![Esempio di evento di esplorazione WebView](webview-images/loading-start.png)

Caricamento completato:

![Esempio di evento di esplorazione WebView](webview-images/loading-end.png)

## <a name="reloading-content"></a>Ricaricamento del contenuto

[`WebView`](xref:Xamarin.Forms.WebView)dispone `Reload` di un metodo che può essere utilizzato per ricaricare il contenuto corrente:

```csharp
var webView = new WebView();
...
webView.Reload();
```

Quando viene `Reload` richiamato il metodo `ReloadRequested` , l'evento viene generato, a indicare che è stata effettuata una richiesta di ricaricamento del contenuto corrente.

## <a name="performance"></a>Prestazioni

I Web browser più diffusi adottano tecnologie come il rendering con accelerazione hardware e la compilazione JavaScript. Prima Xamarin.Forms del 4,4, Xamarin.Forms `WebView` era implementato in iOS dalla `UIWebView` classe. Tuttavia, molte di queste tecnologie non erano disponibili in questa implementazione. Pertanto, dal Xamarin.Forms 4,4, Xamarin.Forms `WebView` viene implementato in iOS dalla `WkWebView` classe, che supporta l'esplorazione più veloce.

> [!NOTE]
> In iOS, `WkWebViewRenderer` ha un overload del costruttore che accetta un `WkWebViewConfiguration` argomento. In questo modo è possibile configurare il renderer durante la creazione.

Un'applicazione può tornare all'uso della `UIWebView` classe iOS per implementare Xamarin.Forms `WebView` , per motivi di compatibilità. Questa operazione può essere eseguita aggiungendo il codice seguente al file **AssemblyInfo.cs** nel progetto della piattaforma iOS per l'applicazione:

```csharp
// Opt-in to using UIWebView instead of WkWebView.
[assembly: ExportRenderer(typeof(Xamarin.Forms.WebView), typeof(Xamarin.Forms.Platform.iOS.WebViewRenderer))]
```

`WebView`per impostazione predefinita, in Android è circa la velocità del browser incorporato.

[UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) usa il motore di rendering Microsoft Edge. I dispositivi desktop e Tablet dovrebbero visualizzare le stesse prestazioni dell'uso del browser Microsoft Edge.

## <a name="permissions"></a>Autorizzazioni

Per consentire `WebView` il funzionamento di, è necessario assicurarsi che le autorizzazioni siano impostate per ogni piattaforma. Si noti che in alcune piattaforme `WebView` funzionerà in modalità di debug, ma non quando viene compilata per la versione. Questo perché alcune autorizzazioni, ad esempio quelle per l'accesso a Internet in Android, vengono impostate per impostazione predefinita da Visual Studio per Mac in modalità di debug.

- **UWP** &ndash; richiede la funzionalità Internet (client & Server) per la visualizzazione del contenuto di rete.
- **Android** &ndash; richiede `INTERNET` solo quando viene visualizzato contenuto dalla rete. Il contenuto locale non richiede autorizzazioni speciali.
- **iOS** &ndash; non richiede autorizzazioni speciali.

## <a name="layout"></a>Layout

A differenza della maggior parte delle altre Xamarin.Forms viste, `WebView` richiede che `HeightRequest` e `WidthRequest` siano specificati quando sono contenuti in StackLayout o sul relativelayout. Se non si specificano tali proprietà, `WebView` non verrà eseguito il rendering di.

Negli esempi seguenti vengono illustrati i layout che comportano il funzionamento, il rendering di `WebView` :

StackLayout con WidthRequest & HeightRequest:

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

Sul relativelayout con WidthRequest & HeightRequest:

```xaml
<RelativeLayout>
    <Label Text="test"
        RelativeLayout.XConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=10}"
        RelativeLayout.YConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=20}" />
    <WebView Source="http://www.xamarin.com/"
        RelativeLayout.XConstraint="{ConstraintExpression Type=Constant,
                                     Constant=10}"
        RelativeLayout.YConstraint="{ConstraintExpression Type=Constant,
                                     Constant=50}"
        WidthRequest="1000" HeightRequest="1000" />
</RelativeLayout>
```

AbsoluteLayout *senza* WidthRequest & HeightRequest:

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

Griglia *senza* WidthRequest & HeightRequest. Grid è uno dei pochi layout che non richiede di specificare le altezze e le larghezze richieste.:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="100" />
        <RowDefinition Height="*" />
    </Grid.RowDefinitions>
    <Label Text="test" Grid.Row="0" />
    <WebView Source="http://www.xamarin.com/" Grid.Row="1" />
</Grid>
```

## <a name="invoking-javascript"></a>Chiamata di JavaScript

[`WebView`](xref:Xamarin.Forms.WebView)include la possibilità di richiamare una funzione JavaScript da C# e di restituire qualsiasi risultato al codice C# chiamante. Questa operazione viene eseguita con il [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) metodo, illustrato nell'esempio seguente dall'esempio [WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) :

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

Il [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) metodo valuta il codice JavaScript specificato come argomento e restituisce qualsiasi risultato come `string` . In questo esempio `factorial` viene richiamata la funzione JavaScript, che restituisce il fattoriale di `number` come risultato. Questa funzione JavaScript viene definita nel file HTML locale caricato dall'oggetto [`WebView`](xref:Xamarin.Forms.WebView) e viene illustrato nell'esempio seguente:

```html
<html>
<body>
<script type="text/javascript">
function factorial(num) {
        if (num === 0 || num === 1)
            return 1;
        for (var i = num - 1; i >= 1; i--) {
            num *= i;
        }
        return num;
}
</script>
</body>
</html>
```

## <a name="cookies"></a>Cookie

I cookie possono essere impostati su un oggetto [`WebView`](xref:Xamarin.Forms.WebView) , che viene quindi inviato con la richiesta Web all'URL specificato. Questa operazione viene eseguita aggiungendo `Cookie` oggetti a un oggetto `CookieContainer` , che viene quindi impostato come valore della `WebView.Cookies` proprietà associabile. Il codice seguente illustra un esempio di questo:

```csharp
using System.Net;
using Xamarin.Forms;
// ...

CookieContainer cookieContainer = new CookieContainer();
Uri uri = new Uri("https://dotnet.microsoft.com/apps/xamarin", UriKind.RelativeOrAbsolute);

Cookie cookie = new Cookie
{
    Name = "XamarinCookie",
    Expires = DateTime.Now.AddDays(1),
    Value = "My cookie",
    Domain = uri.Host,
    Path = "/"
};
cookieContainer.Add(uri, cookie);
webView.Cookies = cookieContainer;
webView.Source = new UrlWebViewSource { Url = uri.ToString() };
```

In questo esempio `Cookie` viene aggiunto un singolo all' `CookieContainer` oggetto, che viene quindi impostato come valore della `WebView.Cookies` Proprietà. Quando [`WebView`](xref:Xamarin.Forms.WebView) Invia una richiesta Web all'URL specificato, il cookie viene inviato con la richiesta.

## <a name="uiwebview-deprecation-and-app-store-rejection-itms-90809"></a>Deprecazione di UIWebView e rifiuto di App Store (ITMS-90809)

A partire dall'aprile 2020, [Apple rifiuterà le app](https://developer.apple.com/news/?id=12232019b) che usano ancora l' `UIWebView` API deprecata. Mentre Xamarin.Forms è stato impostato `WKWebView` come predefinito, esiste ancora un riferimento all'SDK precedente nei Xamarin.Forms file binari. Il comportamento corrente del [linker iOS](~/ios/deploy-test/linker.md) non rimuove questo e, di conseguenza, l'API deprecata `UIWebView` verrà comunque visualizzata come riferimento dall'app quando si invia all'App Store.

Per risolvere il problema, è disponibile una versione di anteprima del linker. Per abilitare l'anteprima, sarà necessario fornire un argomento aggiuntivo `--optimize=experimental-xforms-product-type` al linker.

I prerequisiti per questo lavoro sono i seguenti:

- ** Xamarin.Forms 4,5 o versione successiva**. Xamarin.Forms4,6, o versione successiva, è obbligatorio se l'app usa oggetti visivi materiali.
- **Novell. iOS 13.10.0.17 o versione successiva**. Verificare la versione di Novell. iOS [in Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#version-information). Questa versione di Novell. iOS è inclusa in Visual Studio per Mac 8.4.1 e Visual Studio 16.4.3.
- **Rimuovere i riferimenti `UIWebView` a **. Il codice non deve contenere riferimenti a `UIWebView` né a classi che usano `UIWebView` .

Per ulteriori informazioni sul rilevamento e la rimozione dei `UIWebView` riferimenti, vedere [deprecazione di UIWebView](~/ios/user-interface/controls/webview.md#uiwebview-deprecation).

### <a name="configure-the-linker"></a>Configurare il linker

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Per rimuovere i riferimenti, attenersi alla procedura seguente `UIWebView` :

1. **Aprire le proprietà** &ndash; del progetto iOS Fare clic con il pulsante destro del mouse sul progetto iOS e scegliere **Proprietà**.
1. **Passare alla sezione** &ndash; compilazione iOS Selezionare la sezione **compilazione iOS** .
1. **Aggiornare gli argomenti** &ndash; aggiuntivi di mTouch Negli **argomenti aggiuntivi di mTouch** aggiungere questo flag `--optimize=experimental-xforms-product-type` (oltre a qualsiasi valore che potrebbe essere già presente). Nota: questo flag funziona in combinazione con il **comportamento del linker** impostato **solo su SDK** o collegamento a **tutti**. Se, per qualsiasi motivo, vengono visualizzati errori quando si imposta il comportamento del linker su tutti, probabilmente si tratta di un problema all'interno del codice dell'app o di una libreria di terze parti che non è protetta dal linker. Per altre informazioni sul linker, vedere [collegamento di app Novell. iOS](~/ios/deploy-test/linker.md).
1. **Aggiorna tutte le configurazioni** &ndash; di compilazione Usare gli elenchi **configurazione** e **piattaforma** nella parte superiore della finestra per aggiornare tutte le configurazioni della build. La configurazione più importante da aggiornare è la configurazione **release/iPhone** , perché in genere viene usata per creare compilazioni per l'invio di App Store.

È possibile visualizzare la finestra con il nuovo flag sul posto in questa schermata:

[![Impostazione del flag nella sezione di compilazione iOS](webview-images/iosbuildblade-vs-sml.png)](webview-images/iosbuildblade-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Per rimuovere i riferimenti, attenersi alla procedura seguente `UIWebView` :

1. **Apri le opzioni** &ndash; del progetto iOS Fare clic con il pulsante destro del mouse sul progetto iOS e scegliere **Opzioni**.
1. **Passare alla sezione** &ndash; compilazione iOS Selezionare la sezione **compilazione iOS** .
1. **Aggiornare gli argomenti _mTouch_ aggiuntivi** &ndash; negli **argomenti _mTouch_ aggiuntivi** aggiungere questo flag `--optimize=experimental-xforms-product-type` (oltre a qualsiasi valore che potrebbe essere già presente). Nota: questo flag funziona in combinazione con il **comportamento del linker** impostato **solo su SDK** o collegamento a **tutti**. Se, per qualsiasi motivo, vengono visualizzati errori quando si imposta il comportamento del linker su tutti, probabilmente si tratta di un problema all'interno del codice dell'app o di una libreria di terze parti che non è protetta dal linker. Per altre informazioni sul linker, vedere [collegamento di app Novell. iOS](~/ios/deploy-test/linker.md).
1. **Aggiorna tutte le configurazioni** &ndash; di compilazione Usare gli elenchi **configurazione** e **piattaforma** nella parte superiore della finestra per aggiornare tutte le configurazioni della build. La configurazione più importante da aggiornare è la configurazione **release/iPhone** , perché in genere viene usata per creare compilazioni per l'invio di App Store.

È possibile visualizzare la finestra con il nuovo flag sul posto in questa schermata:

[![Impostazione del flag nella sezione di compilazione iOS](webview-images/iosbuildblade-xs-sml.png)](webview-images/iosbuildblade-xs.png#lightbox)

-----

A questo punto, quando si crea una nuova compilazione (versione) e la si invia all'App Store, non dovrebbero essere presenti avvisi sull'API deprecata.

## <a name="related-links"></a>Collegamenti correlati

- [Uso di WebView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [WebView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
- [Deprecazione UIWebView](~/ios/user-interface/controls/webview.md#uiwebview-deprecation)
