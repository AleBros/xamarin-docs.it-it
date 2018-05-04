---
title: WebView
description: Presentare il contenuto web di rete e locali o documenti.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: a1cba53223567e353194a4fcd52c8e22fa48ddf0
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="webview"></a>WebView

[WebView](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) è una vista per la visualizzazione web e HTML contenuta nell'app. A differenza di `OpenUri`, che accetta l'utente per il web browser nel dispositivo, `WebView` consente di visualizzare il contenuto HTML all'interno dell'app.

Questa guida è costituita da nelle sezioni seguenti:

- **[Contenuto](#Content)**  &ndash; WebView supporta varie origini di contenuto, incluse stringhe HTML, pagine web e HTML incorporato.
- **[Navigazione](#Navigation)**  &ndash; WebView include il supporto per la navigazione a una determinata pagina e se si torna indietro.
- **[Eventi](#Events)**  &ndash; attendere e rispondere alle azioni eseguite dall'utente in WebView.
- **[Prestazioni](#Performance)**  &ndash; informazioni sulle caratteristiche delle prestazioni di WebView in ciascuna piattaforma.
- **[Autorizzazioni](#Permissions)**  &ndash; informazioni su come impostare le autorizzazioni in modo che funzionino WebView nell'app.
- **[Layout](#Layout)**  &ndash; WebView presenta alcuni requisiti molto specifico per la disposizione delle. Informazioni su come verificare che WebView verrà visualizzato correttamente:

![](webview-images/in-app-browser.png "Nel Browser di App")

## <a name="content"></a>Content

WebView fornito con il supporto per i seguenti tipi di contenuto:

- Siti Web HTML e CSS &ndash; WebView contiene il supporto completo per i siti Web scritte con HTML e CSS, incluso il supporto di JavaScript.
- Documenti &ndash; poiché WebView è implementato mediante componenti nativi in ciascuna piattaforma, WebView è in grado di visualizzare i documenti che possono essere visualizzati in ogni piattaforma. Ciò significa che i file PDF funzionano in iOS e Android.
- Stringhe HTML &ndash; WebView è possibile visualizzare le stringhe HTML dalla memoria.
- File locali &ndash; WebView può presentare uno qualsiasi dei tipi di contenuto precedenti incorporato nell'app.

> [!NOTE]
> `WebView` in Windows non supporta Silverlight, Flash o tutti i controlli ActiveX, anche se sono supportate da Internet Explorer nella piattaforma.

### <a name="websites"></a>Siti Web

Per visualizzare un sito Web da internet, impostare il `WebView`del [ `Source` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebViewSource/) proprietà a un URL di stringa:

```csharp
var browser = new WebView {
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Gli URL devono essere completamente formati con il protocollo specificato (ad esempio devono essere "http://" o "https://" anteposto).

#### <a name="ios-and-ats"></a>iOS e AT

Fin dalla versione 9, iOS consentirà solo l'applicazione comunicare con server che implementano sicurezza consigliate per impostazione predefinita. I valori devono essere impostati `Info.plist` per abilitare la comunicazione con i server non protetti.

> [!NOTE]
> Se l'applicazione richiede una connessione a un sito Web di non protetta, è necessario immettere sempre il dominio come un'eccezione utilizzando `NSExceptionDomains` anziché disattivare completamente l'utilizzo di at `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` deve essere utilizzato solo in situazioni di emergenza estremi.

Di seguito viene illustrato come abilitare un dominio specifico (in questo caso xamarin.com) per ignorare i requisiti di AT:

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

È consigliabile attivare solo alcuni domini di ignorare AT, consentendo di usare i siti attendibili durante beneficiare di aggiuntiva di sicurezza in domini non attendibili. Di seguito viene illustrato il metodo meno sicuro di disabilitazione AT per l'app:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
```

Vedere [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md) per ulteriori informazioni su questa nuova funzionalità di iOS 9.

### <a name="html-strings"></a>Stringhe HTML

Se si desidera presentare una stringa HTML definito in modo dinamico nel codice, è necessario creare un'istanza di [ `HtmlWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.HtmlWebViewSource/):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![](webview-images/html-string.png "Stringa di WebView visualizzazione HTML")

Nel codice precedente, `@` viene utilizzato per contrassegnare il codice HTML sotto forma di stringa letterale, vale a dire tutti i caratteri di escape consueti vengono ignorati.

### <a name="local-html-content"></a>Contenuto HTML locale

WebView può visualizzare il contenuto di HTML, CSS e Javascript incorporato all'interno dell'app. Ad esempio:

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

Si noti che i tipi di carattere specificati nel foglio di stile CSS precedente dovranno essere personalizzato per ogni piattaforma, come non a ogni piattaforma prevede stessi tipi di carattere.

Per visualizzare il contenuto utilizzando locale un `WebView`, sarà necessario aprire il file HTML come qualsiasi altro, quindi caricare il contenuto come stringa nel `Html` proprietà di un `HtmlWebViewSource`. Per ulteriori informazioni su apertura di file, vedere [utilizzano file](~/xamarin-forms/app-fundamentals/files.md).

Le schermate seguenti mostrano il risultato della visualizzazione del contenuto locale in ogni piattaforma:

![](webview-images/local-content.png "Contenuto di WebView visualizzazione locale")

Anche se è stata caricata prima pagina, il `WebView` non ha alcuna conoscenza di provenienza HTML. Quando si lavora con pagine che fanno riferimento alle risorse locali, che costituisce un problema. Sono esempi di quando questa situazione potrebbe verificarsi quando il collegamento di pagine locali a fianco, una pagina rende utilizzare un file JavaScript separato o una pagina è collegato a un foglio di stile CSS.  

Per risolvere questo problema, è necessario specificare il `WebView` dove trovare i file nel file System. A tale scopo impostare il `BaseUrl` proprietà il `HtmlWebViewSource` utilizzato dal `WebView`.

Poiché il file System su ciascuno dei sistemi operativi diversi, è necessario determinare l'URL in ciascuna piattaforma. Xamarin. Forms espone il `DependencyService` per la risoluzione delle dipendenze in fase di esecuzione in ciascuna piattaforma.

Utilizzare il `DependencyService`, innanzitutto definire un'interfaccia che può essere implementata in ogni piattaforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Si noti che fino a quando l'interfaccia viene implementata in ciascuna piattaforma, l'app non verrà eseguita. Nel progetto comune, assicurarsi che impostare il `BaseUrl` utilizzando il `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

Quindi è necessario fornire implementazioni dell'interfaccia per ogni piattaforma.

#### <a name="ios"></a>iOS

In iOS, il contenuto web deve trovarsi nella directory radice del progetto o **risorse** directory con azione di compilazione *BundleResource*, come illustrato di seguito:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/ios-vs.png "File locali in iOS")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![](webview-images/ios-xs.png "File locali in iOS")

-----

Il `BaseUrl` deve essere impostato per il percorso del bundle principale:

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

In Android, inserire HTML, CSS e immagini nella cartella asset con l'azione di compilazione *AndroidAsset* come illustrato di seguito:

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

In Android, file di **asset** cartella è anche possibile accedere tramite il contesto corrente Android, che viene esposto dal `MainActivity.Instance` proprietà:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html"))) {
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Nei progetti di piattaforma UWP (Universal Windows), inserire HTML, CSS e immagini nella radice del progetto con l'azione di compilazione impostata su *contenuto*.

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

WebView supporta lo spostamento tra i numerosi metodi e proprietà che rende disponibile:

- **GoForward()** &ndash; se `CanGoForward` è true, la chiamata `GoForward` avanza alla pagina visitata successiva.
- **GoBack()** &ndash; se `CanGoBack` è true, la chiamata `GoBack` verrà visualizzata l'ultima pagina visitata.
- **CanGoBack** &ndash; `true` se sono presenti pagine per passare a, `false` se il browser all'URL iniziale.
- **CanGoForward** &ndash; `true` se l'utente si è spostato con le versioni precedenti e può passare a una pagina che è stato già visitata.

All'interno di pagine, `WebView` non supporta i movimenti di Multi-touch. È importante per assicurarsi che il contenuto è ottimizzata e viene visualizzato senza la necessità di zoom.

È comune per le applicazioni visualizzare un collegamento all'interno di un `WebView`, anziché il browser del dispositivo. In questi casi, è utile consentire lo spostamento normale, ma quando le occorrenze di utente di eseguire il backup mentre si trovano sul collegamento inizio, l'app deve tornare alla visualizzazione normale app.

Utilizzare i metodi di navigazione predefiniti e le proprietà per abilitare questo scenario.

Iniziare creando la pagina per la visualizzazione del browser:

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

![](webview-images/in-app-browser.png "Pulsanti di navigazione di WebView")

## <a name="events"></a>Eventi

WebView genera due eventi che consentono di rispondere alle modifiche di stato:

- **Esplorazione** &ndash; evento generato quando il WebView inizia a caricare una nuova pagina.
- **Ci si sposta** &ndash; evento generato quando viene caricata la pagina e di navigazione è stata arrestata.

Se si prevede l'utilizzo delle pagine Web di richiedere molto tempo per caricare, prendere in considerazione l'utilizzo di tali eventi per implementare un indicatore di stato. Ad esempio il codice XAML seguente aspetto:

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

I gestori di due eventi:

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

L'opzione produce l'output seguente (caricamento):

![](webview-images/loading-start.png "Esempio di evento di spostamento di WebView")

Caricamento completato:

![](webview-images/loading-end.png "Esempio di evento di spostamento di WebView")

## <a name="performance"></a>Prestazioni

Recenti progressi sono visto prima ognuno dei browser web comuni adottare tecnologie, ad esempio la compilazione di JavaScript e il rendering con accelerazione hardware. Sfortunatamente, a causa di restrizioni di sicurezza, la maggior parte di questi miglioramenti non erano disponibile nell'il equaivalent iOS di `WebView`, `UIWebView`. Xamarin. Forms `WebView` utilizza `UIWebView`. Se questo è un problema, è necessario scrivere un renderer personalizzato che utilizza `WKWebView`, che supporta la visualizzazione più veloce. Si noti che `WKWebView` è supportato solo in iOS 8 e versioni successive.

WebView in Android per impostazione predefinita è veloci quasi quanto il browser predefinito.

Il [UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) utilizza il motore di rendering Microsoft Edge. I dispositivi desktop e tablet dovrebbe essere le stesse prestazioni come usando il browser Edge se stesso.

## <a name="permissions"></a>Autorizzazioni

Affinché `WebView` per lavorare, è necessario assicurarsi che le autorizzazioni vengono impostate per ogni piattaforma. Si noti che in alcune piattaforme, `WebView` funzionerà in modalità di debug, ma non quando compilato per il rilascio. Ciò avviene perché alcune autorizzazioni, come quelle per l'accesso a internet in Android, per impostazione predefinita da Visual Studio per Mac in modalità di debug.

- **UWP** &ndash; richiede la funzionalità Internet (Client e Server) quando si visualizza il contenuto di rete.
- **Android** &ndash; richiede `INTERNET` solo quando la visualizzazione di contenuto dalla rete. Il contenuto locale non richiede autorizzazioni speciali.
- **iOS** &ndash; non richiede autorizzazioni speciali.

## <a name="layout"></a>Layout

A differenza la maggior parte delle altre visualizzazioni, xamarin. Forms `WebView` richiede che `HeightRequest` e `WidthRequest` vengono specificati quando è contenuto in StackLayout o RelativeLayout. Se non è possibile specificare queste proprietà, il `WebView` non verrà eseguito il rendering.

Gli esempi seguenti illustrano i layout che comportano un lavoro, per il rendering `WebView`s:

StackLayout con WidthRequest & HeightRequest:

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

Griglia *senza* WidthRequest & HeightRequest. Griglia è uno dei pochi layout che non è necessario specificare larghezze e altezze richieste.:

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


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di WebView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)
- [WebView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView)
