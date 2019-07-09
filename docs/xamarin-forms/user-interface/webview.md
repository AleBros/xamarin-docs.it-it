---
title: WebView di xamarin. Forms
description: Questo articolo illustra come usare la classe WebView di xamarin. Forms per presentare locale o contenuto web di rete e i documenti per gli utenti.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2019
ms.openlocfilehash: 6410be4019772ad11cd97d27c5de3c0300d58519
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649640"
---
# <a name="xamarinforms-webview"></a>WebView di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)

[`WebView`](xref:Xamarin.Forms.WebView) è una vista per la visualizzazione del contenuto HTML e web nell'app. A differenza `OpenUri`, che accetta l'utente per il web browser nel dispositivo, `WebView` consente di visualizzare il contenuto HTML all'interno dell'app.

![](webview-images/in-app-browser.png "Nell'App Browser")

## <a name="content"></a>Content

`WebView` supporta i tipi di contenuto seguenti:

- Siti Web HTML e CSS &ndash; WebView include il supporto completo per i siti Web scritti utilizzando HTML e CSS, incluso il supporto JavaScript.
- Documenti &ndash; poiché WebView viene implementato usando i componenti nativi in ciascuna piattaforma, WebView è in grado di visualizzare i documenti che possono essere visualizzati in ogni piattaforma. Ciò significa che i file PDF funzionano in iOS e Android.
- Stringhe HTML &ndash; WebView visualizzabili stringhe HTML dalla memoria.
- File locali &ndash; WebView può presentare uno qualsiasi dei tipi contenuti indicati in precedenza incorporato nell'app.

> [!NOTE]
> `WebView` in Windows non supporta Silverlight, Flash o tutti i controlli ActiveX, anche se sono supportate da Internet Explorer che utilizzano tale piattaforma.

### <a name="websites"></a>Siti Web

Per visualizzare un sito Web da internet, impostare il `WebView`del [ `Source` ](xref:Xamarin.Forms.WebViewSource) proprietà a un URL di stringa:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> URL deve essere completamente formato con il protocollo specificato (ad esempio deve avere "http://" o "https://" anteposto a esso).

#### <a name="ios-and-ats"></a>iOS e ATS

Dopo la versione 9, iOS consentirà solo l'applicazione può comunicare con server che implementano sicurezza consigliate per impostazione predefinita. I valori devono essere impostati `Info.plist` per abilitare la comunicazione con i server non protetti.

> [!NOTE]
> Se l'applicazione richiede una connessione a un sito Web non protette, è sempre necessario immettere il dominio come un'eccezione usando `NSExceptionDomains` anziché la disattivazione ATS interamente con `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` deve essere utilizzato solo in situazioni di emergenza estreme.

Di seguito viene illustrato come abilitare un dominio specifico (in questo caso xamarin.com) per ignorare i requisiti ATS:

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

È consigliabile abilitare solo alcuni domini di ignorare ATS, consentendo l'utilizzo di siti attendibili di sicurezza aggiuntive in domini non attendibili usufruendo al contempo. Di seguito viene illustrato il metodo meno sicuro disabilitare ATS per l'app:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

Visualizzare [App Transport Security](~/ios/app-fundamentals/ats.md) per altre informazioni su questa nuova funzionalità di iOS 9.

### <a name="html-strings"></a>Stringhe HTML

Se si vuole presentare una stringa del codice HTML definito in modo dinamico nel codice, è necessario creare un'istanza del [ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![](webview-images/html-string.png "Stringa HTML visualizzazione WebView")

Nel codice precedente, `@` viene usato per contrassegnare il codice HTML sotto forma di stringa letterale, vale a dire tutti i caratteri di escape consueti vengono ignorati.

> [!NOTE]
> Potrebbe essere necessario impostare il `WidthRequest` e `HeightRequest` proprietà del [ `WebView` ](xref:Xamarin.Forms.WebView) per visualizzare il contenuto HTML, a seconda del layout di `WebView` è un figlio di. Ad esempio, questa operazione è necessaria un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).

### <a name="local-html-content"></a>Contenuto HTML locale

WebView può visualizzare il contenuto da HTML, CSS e Javascript incorporato all'interno dell'app. Ad esempio:

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

CSS:

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

Si noti che i tipi di carattere specificati nel foglio di stile CSS precedente dovranno essere personalizzate per ogni piattaforma, non tutte le piattaforme abbia i tipi di carattere stesso.

Alla visualizzazione locale contenuto usando un `WebView`, è necessario aprire il file HTML come qualsiasi altro, quindi caricare il contenuto sotto forma di stringa nel `Html` proprietà di un `HtmlWebViewSource`. Per altre informazioni su apertura di file, vedere [uso di file](~/xamarin-forms/data-cloud/data/files.md).

Gli screenshot seguenti mostrano il risultato della visualizzazione del contenuto locale in ogni piattaforma:

![](webview-images/local-content.png "WebView visualizzando il contenuto locale")

Anche se la prima pagina è stata caricata, il `WebView` non conosce la provenienza di HTML. Questo è un problema quando si lavora con le pagine che fanno riferimento a risorse locali. Sono esempi di quando ciò può verificarsi quando collegamento pagine locali a fianco, una pagina consente di utilizzare un file JavaScript separato, o una pagina si collega a un foglio di stile CSS.  

Per risolvere questo problema, è necessario indicare la `WebView` dove trovare i file nel file System. Farlo impostando il `BaseUrl` proprietà di `HtmlWebViewSource` usato dal `WebView`.

Poiché il file System su ciascuno dei sistemi operativi è diverso, è necessario determinare tale URL per ogni piattaforma. Xamarin. Forms espone il `DependencyService` per la risoluzione delle dipendenze in fase di esecuzione in ogni piattaforma.

Usare il `DependencyService`, innanzitutto definire un'interfaccia che può essere implementata in ogni piattaforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Si noti che fino a quando l'interfaccia viene implementata su ogni piattaforma, l'app non verrà eseguita. Nel progetto common, assicurarsi di ricordare di impostare il `BaseUrl` utilizzando il `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

Quindi è necessario fornire le implementazioni dell'interfaccia per ogni piattaforma.

#### <a name="ios"></a>iOS

In iOS, il contenuto web deve trovarsi nella directory radice del progetto oppure **le risorse** directory con azione di compilazione *BundleResource*, come illustrato di seguito:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](webview-images/ios-vs.png "File locali in iOS")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](webview-images/ios-xs.png "File locali in iOS")

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

In Android, inserire HTML, CSS e immagini nella cartella asset con azione di compilazione *AndroidAsset* come illustrato di seguito:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](webview-images/android-vs.png "File locali in Android")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](webview-images/android-xs.png "File locali in Android")

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

In Android, i file nei **asset** cartella è anche possibile accedere tramite il contesto di Android corrente, che viene esposto dal `MainActivity.Instance` proprietà:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Progetti di Universal Windows Platform (UWP), inserire HTML, CSS e immagini nella radice del progetto con l'azione di compilazione impostata su *contenuto*.

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

WebView supporta la navigazione tra diversi metodi e proprietà che rende disponibile:

- **GoForward()** &ndash; se `CanGoForward` è true, la chiamata a `GoForward` si sposta in avanti per la successiva pagina visitata.
- **GoBack()** &ndash; se `CanGoBack` è true, la chiamata a `GoBack` passerà all'ultima pagina visitata.
- **CanGoBack** &ndash; `true` se sono presenti pagine su cui tornare, `false` se il browser all'URL iniziale.
- **CanGoForward** &ndash; `true` se l'utente si è spostato con le versioni precedenti ed è possibile procedere a una pagina che è stato già visitata.

All'interno delle pagine, `WebView` non supporta i movimenti multitocco. È importante assicurarsi che il contenuto è ottimizzata per dispositivi mobili e viene visualizzato senza la necessità di zoom.

È comune per le applicazioni mostrare un collegamento all'interno di un `WebView`, piuttosto che il browser del dispositivo. In questi casi, è utile consentire la navigazione normale, ma quando l'utente riscontri il mentre sono sul collegamento inizio, l'app deve tornare alla visualizzazione normale app.

Usare i metodi di navigazione predefiniti e le proprietà per abilitare questo scenario.

Iniziare creando la pagina per la visualizzazione browser:

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

![](webview-images/in-app-browser.png "Pulsanti di navigazione WebView")

## <a name="events"></a>Eventi

WebView genera gli eventi seguenti che consentono di rispondere alle modifiche nello stato:

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) : evento generato quando la visualizzazione Web inizia a caricare una nuova pagina.
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) : evento generato quando la pagina viene caricata e spostamento è stato arrestato.
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested) : evento generato quando viene effettuata una richiesta di ricaricare il contenuto corrente.

Il [ `WebNavigatingEventArgs` ](xref:Xamarin.Forms.WebNavigatingEventArgs) oggetti che accompagna il [ `Navigating` ](xref:Xamarin.Forms.WebView.Navigating) evento dispone di quattro proprietà:

- `Cancel` : indica se annullare lo spostamento o meno.
- `NavigationEvent` -l'evento di navigazione che è stato generato.
- `Source` : l'elemento che eseguire la navigazione.
- `Url` : destinazione di navigazione.

Il [ `WebNavigatedEventArgs` ](xref:Xamarin.Forms.WebNavigatedEventArgs) oggetti che accompagna il [ `Navigated` ](xref:Xamarin.Forms.WebView.Navigated) evento dispone di quattro proprietà:

- `NavigationEvent` -l'evento di navigazione che è stato generato.
- `Result` – descrive il risultato della navigazione, usando un [ `WebNavigationResult` ](xref:Xamarin.Forms.WebNavigationResult) membro di enumerazione. I valori validi sono `Cancel`, `Failure`, `Success` e `Timeout`.
- `Source` : l'elemento che eseguire la navigazione.
- `Url` : destinazione di navigazione.

Se prevedi di usare le pagine Web che richiedono molto tempo per caricare, è consigliabile usare la [ `Navigating` ](xref:Xamarin.Forms.WebView.Navigating) e [ `Navigated` ](xref:Xamarin.Forms.WebView.Navigated) eventi per implementare un indicatore di stato. Ad esempio:

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

I due gestori di eventi:

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

Ciò produce l'output seguente (caricamento):

![](webview-images/loading-start.png "Esempio di evento di navigazione WebView")

Terminato il caricamento:

![](webview-images/loading-end.png "Esempio di evento di spostamento WebView")

## <a name="reloading-content"></a>Ricaricamento del contenuto

[`WebView`](xref:Xamarin.Forms.WebView) ha un `Reload` metodo che può essere utilizzato per ricaricare il contenuto corrente:

```csharp
var webView = new WebView();
...
webView.Reload();
```

Quando la `Reload` metodo viene richiamato il `ReloadRequested` viene generato l'evento, che indica che sia stata effettuata una richiesta di ricaricare il contenuto corrente.

## <a name="performance"></a>Prestazioni

I browser web più diffusi a questo punto adottano le tecnologie, ad esempio compilazione JavaScript e per il rendering con accelerazione hardware. In iOS, per impostazione predefinita, xamarin. Forms `WebView` implementata dal `UIWebView` classe e molte di queste tecnologie non sono disponibili in questa implementazione. Tuttavia, un'applicazione possa acconsentire esplicitamente all'uso di iOS `WkWebView` classe di implementazione di xamarin. Forms `WebView`, che supporta l'esplorazione più veloce. Questo può essere ottenuto aggiungendo il codice seguente per il **AssemblyInfo.cs** file nel progetto della piattaforma iOS per l'applicazione:

```csharp
// Opt-in to using WkWebView instead of UIWebView.
[assembly: ExportRenderer(typeof(WebView), typeof(Xamarin.Forms.Platform.iOS.WkWebViewRenderer))]
```

`WebView` in Android per impostazione predefinita è veloci quasi quanto quelle il browser predefinito.

Il [UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) Usa il motore di rendering di Microsoft Edge. I dispositivi desktop e tablet dovrebbe essere le stesse prestazioni usando il browser Microsoft Edge se stesso.

## <a name="permissions"></a>Autorizzazioni

Affinché `WebView` per lavorare, è necessario assicurarsi che le autorizzazioni vengono impostate per ogni piattaforma. Si noti che in alcune piattaforme, `WebView` funzionerà in modalità di debug, ma non quando compilato per il rilascio. Ciò avviene perché alcune autorizzazioni, come quelli per l'accesso a internet in Android, per impostazione predefinita da Visual Studio per Mac in modalità di debug.

- **Piattaforma UWP** &ndash; richiede la funzionalità Internet (Client e Server), la visualizzazione di contenuto della rete.
- **Android** &ndash; richiede `INTERNET` solo quando la visualizzazione di contenuto dalla rete. Contenuto locale non richiede autorizzazioni speciali.
- **iOS** &ndash; non richiede autorizzazioni speciali.

## <a name="layout"></a>Layout

A differenza della maggior parte delle altre visualizzazioni, xamarin. Forms `WebView` richiede che `HeightRequest` e `WidthRequest` vengono specificati quando è contenuto in StackLayout o RelativeLayout. Se non si riesce a specificare tali proprietà, il `WebView` non eseguirà il rendering.

Gli esempi seguenti illustrano i layout risultanti in uso, per il rendering `WebView`s:

StackLayout WidthRequest & HeightRequest:

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

RelativeLayout con WidthRequest & HeightRequest:

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

Griglia *senza* WidthRequest & HeightRequest. Griglia è uno dei layout alcuni che non è necessario specificare larghezze e altezze richieste.:

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

## <a name="invoking-javascript"></a>Richiamo di JavaScript

[`WebView`](xref:Xamarin.Forms.WebView) include la possibilità di richiamare una funzione JavaScript da C#e restituire risultati al chiamante C# codice. Questa operazione viene eseguita con il [ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) metodo, che viene visualizzato nell'esempio seguente dal [WebView](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) esempio:

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

Il [ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) metodo valuta il codice JavaScript che viene specificato come argomento e restituisce alcun risultato come un `string`. In questo esempio, il `factorial` funzione JavaScript viene richiamato, che restituisce il fattoriale di `number` di conseguenza. Questo JavaScript funzione è definita nel file HTML locale del file che il [ `WebView` ](xref:Xamarin.Forms.WebView) carica e viene illustrato nell'esempio seguente:

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

- [Utilizzo di WebView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)
- [WebView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView)
