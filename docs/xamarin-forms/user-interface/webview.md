---
title: WebView Novell. Forms
description: Questo articolo illustra come usare la classe WebView Novell. Forms per presentare i documenti e il contenuto Web locale o di rete agli utenti.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/11/2020
ms.openlocfilehash: 7a2671c47a6d2fceaf2b444cfa8988b4bb8c249c
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517268"
---
# <a name="xamarinforms-webview"></a>WebView Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView)è una visualizzazione per la visualizzazione del contenuto Web e HTML nell'app:

![Nel browser app](webview-images/in-app-browser.png)

## <a name="content"></a>Contenuto

`WebView`supporta i tipi di contenuto seguenti:

- HTML & CSS &ndash; WebView offre supporto completo per i siti Web scritti con HTML & CSS, incluso il supporto JavaScript.
- Documenti &ndash; poiché WebView viene implementato usando componenti nativi in ogni piattaforma, WebView è in grado di visualizzare i documenti visualizzabili in ogni piattaforma. Ciò significa che i file PDF funzionano in iOS e Android.
- Le stringhe &ndash; HTML WebView possono mostrare stringhe HTML dalla memoria.
- I file &ndash; locali WebView possono presentare i tipi di contenuto sopra incorporati nell'app.

> [!NOTE]
> `WebView`in Windows non supporta Silverlight, Flash o tutti i controlli ActiveX, anche se sono supportati da Internet Explorer su tale piattaforma.

### <a name="websites"></a>Siti Web

Per visualizzare un sito Web da Internet, impostare la `WebView`proprietà [`Source`](xref:Xamarin.Forms.WebViewSource) di su un URL stringa:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Gli URL devono essere completamente formati con il protocollo specificato (ad esempio, è necessario che sia stato anteposto "http://" o "https://").

#### <a name="ios-and-ats"></a>iOS e ATS

Dalla versione 9, iOS consentirà all'applicazione di comunicare solo con i server che implementano la sicurezza delle procedure consigliate per impostazione predefinita. Per abilitare la comunicazione con `Info.plist` server non sicuri, è necessario impostare i valori in.

> [!NOTE]
> Se l'applicazione richiede una connessione a un sito Web non sicuro, è sempre necessario immettere il dominio come eccezione usando `NSExceptionDomains` invece di disattivare completamente ATS usando `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads`deve essere usato solo in situazioni di emergenza estreme.

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

Se si desidera presentare una stringa di codice HTML definito in modo dinamico nel codice, è necessario creare un'istanza di [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource):

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
> Potrebbe essere `WidthRequest` necessario impostare le proprietà e `HeightRequest` del [`WebView`](xref:Xamarin.Forms.WebView) per visualizzare il contenuto HTML, a seconda del layout di cui `WebView` è figlio. Ad esempio, questa operazione è necessaria in [`StackLayout`](xref:Xamarin.Forms.StackLayout)un oggetto.

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

Per visualizzare il contenuto locale usando `WebView`un, è necessario aprire il file HTML come qualsiasi altro, quindi caricare il contenuto sotto forma di stringa nella `Html` proprietà di un oggetto `HtmlWebViewSource`. Per ulteriori informazioni sull'apertura di file, vedere [utilizzo dei file](~/xamarin-forms/data-cloud/data/files.md).

Gli screenshot seguenti mostrano il risultato della visualizzazione del contenuto locale in ogni piattaforma:

![Visualizzazione WebView del contenuto locale](webview-images/local-content.png)

Anche se la prima pagina è stata caricata `WebView` , non ha alcuna conoscenza della provenienza del codice HTML. Si tratta di un problema quando si gestiscono pagine che fanno riferimento a risorse locali. Di seguito sono riportati alcuni esempi di casi in cui potrebbe verificarsi un collegamento tra le pagine locali, una pagina che usa un file JavaScript separato o una pagina con collegamenti a un foglio di stile CSS.  

Per risolvere questo problema, è necessario indicare il `WebView` percorso in cui trovare i file nel file System. A tale scopo, impostare `BaseUrl` la proprietà sull' `HtmlWebViewSource` oggetto utilizzato dall' `WebView`oggetto.

Poiché il file System in ogni sistema operativo è diverso, è necessario determinare tale URL in ogni piattaforma. Novell. Forms espone `DependencyService` per la risoluzione delle dipendenze in fase di esecuzione in ogni piattaforma.

Per usare `DependencyService`, definire innanzitutto un'interfaccia che può essere implementata in ogni piattaforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Si noti che finché l'interfaccia non viene implementata in ogni piattaforma, l'app non verrà eseguita. Nel progetto comune, verificare che sia stato impostato l' `BaseUrl` utilizzo di: `DependencyService`

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

In Android, il `BaseUrl` deve essere impostato su `"file:///android_asset/"`:

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

Il `BaseUrl` deve essere impostato su `"ms-appx-web:///"`:

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

## <a name="navigation"></a>Spostamento

WebView supporta la navigazione attraverso diversi metodi e proprietà che rende disponibili:

- **GoForward ()** &ndash; se `CanGoForward` è true, la `GoForward` chiamata a passa alla pagina visitata successiva.
- **GoBack ()** &ndash; se `CanGoBack` è true, la `GoBack` chiamata a consente di passare all'ultima pagina visitata.
- **CanGoBack** &ndash; CanGoBack `true` se sono presenti pagine a cui tornare, `false` se il browser si trova all'URL iniziale.
- **CanGoForward** &ndash; CanGoForward `true` se l'utente ha esplorato le versioni precedenti e può andare avanti in una pagina già visitata.

All'interno di `WebView` pagine, non supporta i movimenti multitocco. È importante assicurarsi che il contenuto sia ottimizzato per dispositivi mobili e venga visualizzato senza dover eseguire lo zoom.

È normale che le applicazioni mostrino un collegamento all'interno `WebView`di un, anziché il browser del dispositivo. In questi casi, è utile consentire la navigazione normale, ma quando l'utente si trova nel collegamento iniziale, l'app deve tornare alla visualizzazione normale dell'app.

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

Ecco fatto!

![Pulsanti di spostamento WebView](webview-images/in-app-browser.png)

## <a name="events"></a>Eventi

WebView genera gli eventi seguenti che consentono di rispondere alle modifiche dello stato:

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating): evento generato quando la WebView inizia a caricare una nuova pagina.
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated): evento generato quando la pagina viene caricata e l'esplorazione è stata arrestata.
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested): evento generato quando viene effettuata una richiesta per ricaricare il contenuto corrente.

L' [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) oggetto che accompagna l' [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) evento è costituito da quattro proprietà:

- `Cancel`: indica se annullare o meno la navigazione.
- `NavigationEvent`: l'evento di navigazione che è stato generato.
- `Source`: elemento che ha eseguito lo spostamento.
- `Url`: destinazione di navigazione.

L' [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) oggetto che accompagna l' [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) evento è costituito da quattro proprietà:

- `NavigationEvent`: l'evento di navigazione che è stato generato.
- `Result`: descrive il risultato della navigazione, usando un [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) membro di enumerazione. I valori validi sono `Cancel`, `Failure`, `Success` e `Timeout`.
- `Source`: elemento che ha eseguito lo spostamento.
- `Url`: destinazione di navigazione.

Se si prevede di usare le pagine Web che impiegano molto tempo per il caricamento, provare [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) a [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) usare gli eventi e per implementare un indicatore di stato. Ad esempio:

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

[`WebView`](xref:Xamarin.Forms.WebView)dispone di `Reload` un metodo che può essere utilizzato per ricaricare il contenuto corrente:

```csharp
var webView = new WebView();
...
webView.Reload();
```

Quando viene `Reload` richiamato il metodo `ReloadRequested` , l'evento viene generato, a indicare che è stata effettuata una richiesta di ricaricamento del contenuto corrente.

## <a name="performance"></a>Prestazioni

I Web browser più diffusi adottano tecnologie come il rendering con accelerazione hardware e la compilazione JavaScript. Prima di Novell. Forms 4,4, Novell. Forms `WebView` è stato implementato in iOS `UIWebView` dalla classe. Tuttavia, molte di queste tecnologie non erano disponibili in questa implementazione. Pertanto, dal momento che Novell. Forms 4,4, Novell `WebView` . Forms viene implementato in `WkWebView` iOS dalla classe, che supporta l'esplorazione più veloce.

> [!NOTE]
> In iOS, ha `WkWebViewRenderer` un overload del costruttore che accetta un `WkWebViewConfiguration` argomento. In questo modo è possibile configurare il renderer durante la creazione.

Un'applicazione può tornare all'uso della classe `UIWebView` iOS per implementare Novell. Forms `WebView`, per motivi di compatibilità. Questa operazione può essere eseguita aggiungendo il codice seguente al file **AssemblyInfo.cs** nel progetto della piattaforma iOS per l'applicazione:

```csharp
// Opt-in to using UIWebView instead of WkWebView.
[assembly: ExportRenderer(typeof(Xamarin.Forms.WebView), typeof(Xamarin.Forms.Platform.iOS.WebViewRenderer))]
```

`WebView`per impostazione predefinita, in Android è circa la velocità del browser incorporato.

[UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) usa il motore di rendering Microsoft Edge. I dispositivi desktop e Tablet dovrebbero visualizzare le stesse prestazioni dell'uso del browser Microsoft Edge.

## <a name="permissions"></a>Autorizzazioni

Per consentire il `WebView` funzionamento di, è necessario assicurarsi che le autorizzazioni siano impostate per ogni piattaforma. Si noti che in alcune piattaforme `WebView` funzionerà in modalità di debug, ma non quando viene compilata per la versione. Questo perché alcune autorizzazioni, ad esempio quelle per l'accesso a Internet in Android, vengono impostate per impostazione predefinita da Visual Studio per Mac in modalità di debug.

- **UWP** &ndash; richiede la funzionalità Internet (client & Server) per la visualizzazione del contenuto di rete.
- **Android** &ndash; richiede `INTERNET` solo quando viene visualizzato contenuto dalla rete. Il contenuto locale non richiede autorizzazioni speciali.
- **iOS** &ndash; non richiede autorizzazioni speciali.

## <a name="layout"></a>Layout

A differenza della maggior parte delle altre viste Novell `WebView` . Forms, richiede che `HeightRequest` e `WidthRequest` siano specificati quando sono contenuti in StackLayout o sul relativelayout. Se non si specificano tali proprietà, non `WebView` verrà eseguito il rendering di.

Negli esempi seguenti vengono illustrati i layout che comportano `WebView`il funzionamento, il rendering di:

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

[`WebView`](xref:Xamarin.Forms.WebView)include la possibilità di richiamare una funzione JavaScript da C# e di restituire qualsiasi risultato al codice C# chiamante. Questa operazione viene eseguita con [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) il metodo, illustrato nell'esempio seguente dall'esempio [WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) :

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

Il [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) metodo valuta il codice JavaScript specificato come argomento e restituisce qualsiasi risultato come `string`. In questo esempio viene richiamata la `factorial` funzione JavaScript, che restituisce il fattoriale di `number` come risultato. Questa funzione JavaScript viene definita nel file HTML locale caricato dall'oggetto [`WebView`](xref:Xamarin.Forms.WebView) e viene illustrato nell'esempio seguente:

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

I cookie possono essere impostati su [`WebView`](xref:Xamarin.Forms.WebView)un oggetto, che viene quindi inviato con la richiesta Web all'URL specificato. Questa operazione viene eseguita aggiungendo `Cookie` oggetti a un `CookieContainer`oggetto, che viene quindi impostato come valore della proprietà `WebView.Cookies` associabile. Il codice seguente illustra un esempio di questo:

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

In questo esempio viene aggiunto un `Cookie` singolo all' `CookieContainer` oggetto, che viene quindi impostato come valore della `WebView.Cookies` proprietà. Quando [`WebView`](xref:Xamarin.Forms.WebView) invia una richiesta Web all'URL specificato, il cookie viene inviato con la richiesta.

## <a name="uiwebview-deprecation-and-app-store-rejection-itms-90809"></a>Deprecazione di UIWebView e rifiuto di App Store (ITMS-90809)

A partire dall'aprile 2020, [Apple rifiuterà le app](https://developer.apple.com/news/?id=12232019b) che usano ancora l' `UIWebView` API deprecata. Mentre Novell. Forms è stato impostato `WKWebView` come predefinito, esiste ancora un riferimento all'SDK precedente nei file binari di Novell. Forms. Il comportamento corrente del [linker iOS](~/ios/deploy-test/linker.md) non rimuove questo e, di conseguenza, l' `UIWebView` API deprecata verrà comunque visualizzata come riferimento dall'app quando si invia all'App Store.

Per risolvere il problema, è disponibile una versione di anteprima del linker. Per abilitare l'anteprima, sarà necessario fornire un argomento `--optimize=experimental-xforms-product-type` aggiuntivo al linker.

I prerequisiti per questo lavoro sono i seguenti:

- È possibile usare **Novell. Forms 4,5 o** &ndash; versioni non definitive successive di Novell. Forms 4,5.
- **Novell. iOS 13.10.0.17 o versione successiva** &ndash; controllare la versione di Novell. iOS [in Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#version-information). Questa versione di Novell. iOS è inclusa in Visual Studio per Mac 8.4.1 e Visual Studio 16.4.3.
- **Rimuovere i riferimenti `UIWebView` al** &ndash; codice non devono contenere riferimenti a né `UIWebView` a classi che usano `UIWebView`.

### <a name="configure-the-linker-preview"></a>Configurare l'anteprima del linker

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Per rimuovere `UIWebView` i riferimenti, attenersi alla procedura seguente:

1. **Aprire le proprietà** &ndash; del progetto iOS facendo clic con il pulsante destro del mouse sul progetto iOS e scegliere **proprietà**.
1. **Passare alla sezione** &ndash; compilazione iOS selezionare la sezione **compilazione iOS** .
1. **Aggiornare gli argomenti** &ndash; mTouch aggiuntivi negli **argomenti mTouch aggiuntivi** aggiungere questo flag `--optimize=experimental-xforms-product-type` (oltre a qualsiasi valore che potrebbe essere già presente). Nota: questo flag funziona in combinazione con il **comportamento del linker** impostato **solo su SDK** o collegamento a **tutti**. Se, per qualsiasi motivo, vengono visualizzati errori quando si imposta il comportamento del linker su tutti, probabilmente si tratta di un problema all'interno del codice dell'app o di una libreria di terze parti che non è protetta dal linker. Per altre informazioni sul linker, vedere [collegamento di app Novell. iOS](~/ios/deploy-test/linker.md).
1. **Aggiornare tutte le configurazioni** &ndash; della build usare gli elenchi **configurazione** e **piattaforma** nella parte superiore della finestra per aggiornare tutte le configurazioni della build. La configurazione più importante da aggiornare è la configurazione **release/iPhone** , perché in genere viene usata per creare compilazioni per l'invio di App Store.

È possibile visualizzare la finestra con il nuovo flag sul posto in questa schermata:

[![Impostazione del flag nella sezione di compilazione iOS](webview-images/iosbuildblade-vs-sml.png)](webview-images/iosbuildblade-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Seguire questa procedura per il linker per rimuovere `UIWebView` i riferimenti

1. **Aprire il progetto iOS opzioni** &ndash; fare clic con il pulsante destro del mouse sul progetto iOS e scegliere **Opzioni**.
1. **Passare alla sezione** &ndash; compilazione iOS selezionare la sezione **compilazione iOS** .
1. **Aggiornare gli argomenti _mtouch_ ** &ndash; mTouch aggiuntivi negli **argomenti _mTouch_ aggiuntivi** aggiungere questo flag `--optimize=experimental-xforms-product-type` (oltre a qualsiasi valore che potrebbe essere già presente). Nota: questo flag funziona in combinazione con il **comportamento del linker** impostato **solo su SDK** o collegamento a **tutti**. Se, per qualsiasi motivo, vengono visualizzati errori quando si imposta il comportamento del linker su tutti, probabilmente si tratta di un problema all'interno del codice dell'app o di una libreria di terze parti che non è protetta dal linker. Per altre informazioni sul linker, vedere [collegamento di app Novell. iOS](~/ios/deploy-test/linker.md).
1. **Aggiornare tutte le configurazioni** &ndash; della build usare gli elenchi **configurazione** e **piattaforma** nella parte superiore della finestra per aggiornare tutte le configurazioni della build. La configurazione più importante da aggiornare è la configurazione **release/iPhone** , perché in genere viene usata per creare compilazioni per l'invio di App Store.

È possibile visualizzare la finestra con il nuovo flag sul posto in questa schermata:

[![Impostazione del flag nella sezione di compilazione iOS](webview-images/iosbuildblade-xs-sml.png)](webview-images/iosbuildblade-xs.png#lightbox)

-----

A questo punto, quando si crea una nuova compilazione (versione) e la si invia all'App Store, non dovrebbero essere presenti avvisi sull'API deprecata.

## <a name="related-links"></a>Collegamenti correlati

- [Uso di WebView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [WebView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
