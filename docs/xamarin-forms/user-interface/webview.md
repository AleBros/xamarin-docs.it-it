---
title: WebView di xamarin. Forms
description: Questo articolo illustra come usare la classe WebView di xamarin. Forms per presentare locale o contenuto web di rete e i documenti per gli utenti.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: ed7bec4e25628d938218a40d157442debad8f835
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998374"
---
# <a name="xamarinforms-webview"></a>WebView di xamarin. Forms

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
var browser = new WebView {
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
```

È consigliabile abilitare solo alcuni domini di ignorare ATS, consentendo l'utilizzo di siti attendibili di sicurezza aggiuntive in domini non attendibili usufruendo al contempo. Di seguito viene illustrato il metodo meno sicuro disabilitare ATS per l'app:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
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

Alla visualizzazione locale contenuto usando un `WebView`, è necessario aprire il file HTML come qualsiasi altro, quindi caricare il contenuto sotto forma di stringa nel `Html` proprietà di un `HtmlWebViewSource`. Per altre informazioni su apertura di file, vedere [uso di file](~/xamarin-forms/app-fundamentals/files.md).

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

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/ios-vs.png "File locali in iOS")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![](webview-images/ios-xs.png "File locali in iOS")

-----

Il `BaseUrl` deve essere impostato sul percorso del bundle principale:

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS{
  public class BaseUrl_iOS : IBaseUrl {
    public string Get() {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

In Android, inserire HTML, CSS e immagini nella cartella asset con azione di compilazione *AndroidAsset* come illustrato di seguito:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/android-vs.png "File locali in Android")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![](webview-images/android-xs.png "File locali in Android")

-----

In Android, il `BaseUrl` deve essere impostato su `"file:///android_asset/"`:

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android {
  public class BaseUrl_Android : IBaseUrl {
    public string Get() {
      return "file:///android_asset/";
    }
  }
}
```

In Android, i file nei **asset** cartella è anche possibile accedere tramite il contesto di Android corrente, che viene esposto dal `MainActivity.Instance` proprietà:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html"))) {
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
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.InAppDemo"
Title="In App Browser">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout Orientation="Horizontal" Padding="10,10">
                <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="backClicked" />
                <Button Text="Forward" HorizontalOptions="End" Clicked="forwardClicked" />
            </StackLayout>
            <WebView x:Name="Browser" WidthRequest="1000" HeightRequest="1000" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Nel nostro code-behind:

```csharp
public partial class InAppDemo : ContentPage
{
  //sets the URL for the browser in the page at creation
    public InAppDemo (string URL)
    {
        InitializeComponent ();
        Browser.Source = URL;
    }


    private void backClicked(object sender, EventArgs e)
    {
    // Check to see if there is anywhere to go back to
        if (Browser.CanGoBack) {
            Browser.GoBack ();
        } else { // If not, leave the view
            Navigation.PopAsync ();
        }
    }

    private void forwardClicked(object sender, EventArgs e)
    {
        if (Browser.CanGoForward) {
            Browser.GoForward ();
        }
    }
}
```

La procedura è terminata.

![](webview-images/in-app-browser.png "Pulsanti di navigazione WebView")

## <a name="events"></a>Eventi

WebView genera due eventi che consentono di rispondere alle modifiche nello stato:

- **Spostarsi** &ndash; evento generato quando la visualizzazione Web inizia a caricare una nuova pagina.
- **Reindirizzato** &ndash; evento generato quando la pagina viene caricata e spostamento è stato arrestato.

Se prevedi di usare le pagine Web che richiedono molto tempo per caricare, prendere in considerazione l'utilizzo di tali eventi per implementare un indicatore di stato. Ad esempio il XAML aspetto simile al seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.LoadingDemo" Title="Loading Demo">
  <ContentPage.Content>
    <StackLayout>
      <Label x:Name="LoadingLabel"
        Text="Loading..."
        HorizontalOptions="Center"
        IsVisible="false" />
      <WebView x:Name="Browser"
      HeightRequest="1000"
      WidthRequest="1000"
      Navigating="webOnNavigating"
      Navigated="webOnEndNavigating" />
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

I due gestori di eventi:

```csharp
void webOnNavigating (object sender, WebNavigatingEventArgs e)
{
    LoadingLabel.IsVisible = true;
}

void webOnEndNavigating (object sender, WebNavigatedEventArgs e)
{
    LoadingLabel.IsVisible = false;
}
```

Ciò produce l'output seguente (caricamento):

![](webview-images/loading-start.png "Esempio di evento di navigazione WebView")

Terminato il caricamento:

![](webview-images/loading-end.png "Esempio di evento di spostamento WebView")

## <a name="performance"></a>Prestazioni

Recenti progressi visti ognuno dei browser web più diffusi adottare le tecnologie, ad esempio compilazione JavaScript e per il rendering con accelerazione hardware. Purtroppo, a causa di restrizioni di sicurezza, la maggior parte di questi miglioramenti non erano disponibile nell'il equaivalent iOS del `WebView`, `UIWebView`. Xamarin. Forms `WebView` Usa `UIWebView`. Se questo è un problema, è necessario scrivere un renderer personalizzato che usa `WKWebView`, che supporta l'esplorazione più veloce. Si noti che `WKWebView` è supportata solo in iOS 8 e versioni successive.

WebView in Android per impostazione predefinita è veloci quasi quanto quelle il browser predefinito.

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

Il [ `WebView` ](xref:Xamarin.Forms.WebView) include la possibilità di richiamare una funzione JavaScript da c# e restituisce alcun risultato per il codice c# chiamante. Questa operazione viene eseguita con il [ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) metodo, che viene visualizzato nell'esempio seguente dal [WebView](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) esempio:

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
