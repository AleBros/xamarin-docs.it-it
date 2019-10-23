---
title: WebView Novell. Forms
description: Questo articolo illustra come usare la classe WebView Novell. Forms per presentare i documenti e il contenuto Web locale o di rete agli utenti.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2019
ms.openlocfilehash: cdb2a9f1a37dc7bca458a4291ce6fe5ac9c120aa
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696051"
---
# <a name="xamarinforms-webview"></a>WebView Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) è una vista per la visualizzazione del contenuto Web e HTML nell'app:

![Nel browser app](webview-images/in-app-browser.png)

## <a name="content"></a>Contenuto

`WebView` supporta i tipi di contenuto seguenti:

- I siti Web HTML & CSS &ndash; WebView hanno il supporto completo per i siti Web scritti con HTML & CSS, incluso il supporto JavaScript.
- Documenti &ndash; perché WebView viene implementato usando componenti nativi in ogni piattaforma, WebView è in grado di visualizzare i documenti visualizzabili in ogni piattaforma. Ciò significa che i file PDF funzionano in iOS e Android.
- Le stringhe HTML &ndash; WebView possono visualizzare le stringhe HTML dalla memoria.
- I file locali &ndash; WebView possono presentare tutti i tipi di contenuto sopra incorporati nell'app.

> [!NOTE]
> `WebView` in Windows non supporta Silverlight, Flash o tutti i controlli ActiveX, anche se sono supportati da Internet Explorer su tale piattaforma.

### <a name="websites"></a>Siti Web

Per visualizzare un sito Web da Internet, impostare la proprietà [`Source`](xref:Xamarin.Forms.WebViewSource) del `WebView` su un URL stringa:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Gli URL devono essere completamente formati con il protocollo specificato (ad esempio, è necessario che sia stato anteposto "http://" o "https://").

#### <a name="ios-and-ats"></a>iOS e ATS

Dalla versione 9, iOS consentirà all'applicazione di comunicare solo con i server che implementano la sicurezza delle procedure consigliate per impostazione predefinita. Per abilitare la comunicazione con server non sicuri, è necessario impostare i valori in `Info.plist`.

> [!NOTE]
> Se l'applicazione richiede una connessione a un sito Web non sicuro, è sempre necessario immettere il dominio come eccezione usando `NSExceptionDomains` anziché disattivare completamente ATS usando `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` deve essere usato solo in situazioni di emergenza estreme.

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

Nel codice precedente, `@` viene usato per contrassegnare il codice HTML come valore letterale stringa, ovvero tutti i caratteri di escape usuali vengono ignorati.

> [!NOTE]
> Potrebbe essere necessario impostare le proprietà `WidthRequest` e `HeightRequest` del [`WebView`](xref:Xamarin.Forms.WebView) per visualizzare il contenuto HTML, a seconda del layout di cui il `WebView` è figlio. Questa operazione è necessaria, ad esempio, in una [`StackLayout`](xref:Xamarin.Forms.StackLayout).

### <a name="local-html-content"></a>Contenuto HTML locale

WebView può visualizzare contenuti da HTML, CSS e JavaScript incorporati all'interno dell'app. Esempio:

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamrin.Forms</h1>
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

Per visualizzare il contenuto locale usando un `WebView`, è necessario aprire il file HTML come qualsiasi altro, quindi caricare il contenuto sotto forma di stringa nella proprietà `Html` di un `HtmlWebViewSource`. Per ulteriori informazioni sull'apertura di file, vedere [utilizzo dei file](~/xamarin-forms/data-cloud/data/files.md).

Gli screenshot seguenti mostrano il risultato della visualizzazione del contenuto locale in ogni piattaforma:

![Visualizzazione WebView del contenuto locale](webview-images/local-content.png)

Anche se la prima pagina è stata caricata, il `WebView` non conosce la provenienza del codice HTML. Si tratta di un problema quando si gestiscono pagine che fanno riferimento a risorse locali. Di seguito sono riportati alcuni esempi di casi in cui potrebbe verificarsi un collegamento tra le pagine locali, una pagina che usa un file JavaScript separato o una pagina con collegamenti a un foglio di stile CSS.  

Per risolvere questo problema, è necessario indicare all'`WebView` dove trovare i file nel file System. A tale scopo, impostare la proprietà `BaseUrl` nel `HtmlWebViewSource` utilizzato dal `WebView`.

Poiché il file System in ogni sistema operativo è diverso, è necessario determinare tale URL in ogni piattaforma. Novell. Forms espone il `DependencyService` per la risoluzione delle dipendenze in fase di esecuzione in ogni piattaforma.

Per usare la `DependencyService`, definire innanzitutto un'interfaccia che può essere implementata in ogni piattaforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Si noti che finché l'interfaccia non viene implementata in ogni piattaforma, l'app non verrà eseguita. Nel progetto comune, assicurarsi di impostare il `BaseUrl` utilizzando il `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

Devono quindi essere fornite le implementazioni dell'interfaccia per ogni piattaforma.

#### <a name="ios"></a>iOS

In iOS, il contenuto Web deve trovarsi nella directory radice del progetto o nella directory **delle risorse** con l'azione di compilazione *BundleResource*, come illustrato di seguito:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![File locali in iOS](webview-images/ios-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

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

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![File locali in Android](webview-images/android-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

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

In Android è possibile accedere ai file nella cartella **Asset** anche tramite il contesto Android corrente, esposto dalla proprietà `MainActivity.Instance`:

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

## <a name="navigation"></a>Navigazione

WebView supporta la navigazione attraverso diversi metodi e proprietà che rende disponibili:

- **GoForward ()** &ndash; se `CanGoForward` è true, la chiamata di `GoForward` passa alla pagina visitata successiva.
- **GoBack ()** &ndash; se `CanGoBack` è true, la chiamata `GoBack` passerà all'ultima pagina visitata.
- **CanGoBack** &ndash; `true` se sono presenti pagine a cui tornare, `false` se il browser si trova all'URL iniziale.
- **CanGoForward** &ndash; `true` se l'utente ha esplorato le versioni precedenti e può andare avanti in una pagina già visitata.

All'interno di pagine `WebView` non supporta i movimenti multitocco. È importante assicurarsi che il contenuto sia ottimizzato per dispositivi mobili e venga visualizzato senza dover eseguire lo zoom.

È normale che le applicazioni mostrino un collegamento all'interno di un `WebView`, anziché il browser del dispositivo. In questi casi, è utile consentire la navigazione normale, ma quando l'utente si trova nel collegamento iniziale, l'app deve tornare alla visualizzazione normale dell'app.

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

La procedura è terminata.

![Pulsanti di spostamento WebView](webview-images/in-app-browser.png)

## <a name="events"></a>eventi

WebView genera gli eventi seguenti che consentono di rispondere alle modifiche dello stato:

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) : evento generato quando la WebView inizia a caricare una nuova pagina.
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) : evento generato quando la pagina viene caricata e la navigazione è stata arrestata.
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested) : evento generato quando viene effettuata una richiesta per ricaricare il contenuto corrente.

L'oggetto [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) che accompagna l'evento [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) dispone di quattro proprietà:

- `Cancel`: indica se annullare o meno la navigazione.
- `NavigationEvent`: l'evento di navigazione che è stato generato.
- `Source`: elemento che ha eseguito lo spostamento.
- `Url`: la destinazione di navigazione.

L'oggetto [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) che accompagna l'evento [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) dispone di quattro proprietà:

- `NavigationEvent`: l'evento di navigazione che è stato generato.
- `Result`: descrive il risultato della navigazione, usando un membro dell'enumerazione [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) . I valori validi sono `Cancel`, `Failure`, `Success` e `Timeout`.
- `Source`: elemento che ha eseguito lo spostamento.
- `Url`: la destinazione di navigazione.

Se si prevede di usare le pagine Web che impiegano molto tempo per il caricamento, provare a usare gli eventi [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) e [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) per implementare un indicatore di stato. Esempio:

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

[`WebView`](xref:Xamarin.Forms.WebView) dispone di un metodo `Reload` che può essere utilizzato per ricaricare il contenuto corrente:

```csharp
var webView = new WebView();
...
webView.Reload();
```

Quando viene richiamato il metodo `Reload`, viene generato l'evento `ReloadRequested`, che indica che è stata eseguita una richiesta di ricaricamento del contenuto corrente.

## <a name="performance"></a>Prestazioni

I Web browser più diffusi ora adottano tecnologie come il rendering con accelerazione hardware e la compilazione JavaScript. In iOS, per impostazione predefinita, il `WebView` Novell. Forms viene implementato dalla classe `UIWebView` e molte di queste tecnologie non sono disponibili in questa implementazione. Tuttavia, un'applicazione può acconsentire esplicitamente all'uso della classe `WkWebView` iOS per implementare il `WebView` Novell. Forms, che supporta l'esplorazione più veloce. Questa operazione può essere eseguita aggiungendo il codice seguente al file **AssemblyInfo.cs** nel progetto della piattaforma iOS per l'applicazione:

```csharp
// Opt-in to using WkWebView instead of UIWebView.
[assembly: ExportRenderer(typeof(WebView), typeof(Xamarin.Forms.Platform.iOS.WkWebViewRenderer))]
```

> [!NOTE]
> In iOS, il `WkWebViewRenderer` dispone di un overload del costruttore che accetta un argomento `WkWebViewConfiguration`. In questo modo è possibile configurare il renderer durante la creazione.

per impostazione predefinita, `WebView` in Android è il più veloce del browser predefinito.

[UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) usa il motore di rendering Microsoft Edge. I dispositivi desktop e Tablet dovrebbero visualizzare le stesse prestazioni dell'uso del browser Microsoft Edge.

## <a name="permissions"></a>Autorizzazioni

Affinché `WebView` funzioni, è necessario assicurarsi che le autorizzazioni siano impostate per ogni piattaforma. Si noti che in alcune piattaforme `WebView` funzionerà in modalità di debug, ma non quando viene compilata per la versione. Questo perché alcune autorizzazioni, ad esempio quelle per l'accesso a Internet in Android, vengono impostate per impostazione predefinita da Visual Studio per Mac in modalità di debug.

- **UWP** &ndash; richiede la funzionalità Internet (client & Server) per la visualizzazione del contenuto di rete.
- **Android** &ndash; richiede `INTERNET` solo quando viene visualizzato contenuto dalla rete. Il contenuto locale non richiede autorizzazioni speciali.
- **iOS** &ndash; non richiede autorizzazioni speciali.

## <a name="layout"></a>Layout

A differenza della maggior parte delle altre viste Novell. Forms, `WebView` richiede che siano specificati `HeightRequest` e `WidthRequest` quando sono contenuti in StackLayout o sul relativelayout. Se non si specificano tali proprietà, non verrà eseguito il rendering del `WebView`.

Negli esempi seguenti vengono illustrati i layout che comportano il funzionamento, il rendering `WebView`s:

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

[`WebView`](xref:Xamarin.Forms.WebView) include la possibilità di richiamare una funzione JavaScript da C#e restituire qualsiasi risultato al codice chiamante C# . Questa operazione viene eseguita con il metodo [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) , illustrato nell'esempio seguente dall'esempio [WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) :

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

Il metodo [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) valuta il codice JavaScript specificato come argomento e restituisce qualsiasi risultato come `string`. In questo esempio viene richiamata la funzione `factorial` JavaScript, che restituisce il fattoriale di `number` di conseguenza. Questa funzione JavaScript viene definita nel file HTML locale caricato dal [`WebView`](xref:Xamarin.Forms.WebView) e viene illustrato nell'esempio seguente:

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

## <a name="related-links"></a>Collegamenti correlati

- [Uso di WebView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [WebView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
