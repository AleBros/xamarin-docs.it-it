---
title: Visualizzazione Web
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: c1fcb16bd40b818b27b57b877534e051a789a6c9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029015"
---
# <a name="xamarinandroid-web-view"></a>Visualizzazione Web Novell. Android

[`WebView`](xref:Android.Webkit.WebView) consente di creare una finestra personalizzata per la visualizzazione di pagine Web (o persino per lo sviluppo di un browser completo). In questa esercitazione verrà creata una semplice [`Activity`](xref:Android.App.Activity)
che consente di visualizzare ed esplorare le pagine Web.

Creare un nuovo progetto denominato **HelloWebView**.

Aprire **risorse/layout/Main. aXML** e inserire quanto segue:

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

Poiché l'applicazione accederà a Internet, è necessario aggiungere le autorizzazioni appropriate al file manifesto Android. Aprire le proprietà del progetto per specificare le autorizzazioni necessarie per il funzionamento dell'applicazione. Abilitare l'autorizzazione `INTERNET` come illustrato di seguito:

![Impostazione dell'autorizzazione INTERNET nel manifesto Android](web-view-images/01-set-internet-permissions.png)

A questo punto, aprire **MainActivity.cs** e aggiungere una direttiva using per WebKit:

```csharp
using Android.Webkit;
```

Nella parte superiore della classe `MainActivity` dichiarare un oggetto [`WebView`](xref:Android.Webkit.WebView) :

```csharp
WebView web_view;
```

Quando viene **richiesto** di caricare un URL, la richiesta viene delegata per impostazione predefinita al browser predefinito. Per fare in modo che **WebView** carichi l'URL, anziché il browser predefinito, è necessario sottoclassare `Android.Webkit.WebViewClient` ed eseguire l'override del metodo `ShouldOverriderUrlLoading`. Un'istanza di questa `WebViewClient` personalizzata viene fornita al `WebView`. A tale scopo, aggiungere la seguente classe annidata `HelloWebViewClient` all'interno `MainActivity`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    public override bool ShouldOverrideUrlLoading (WebView view, string url)
    {
        view.LoadUrl(url);
        return false;
    }
}
```

Quando `ShouldOverrideUrlLoading` restituisce `false`, segnala a Android che l'istanza di `WebView` corrente ha gestito la richiesta e che non sono necessarie altre azioni. 

Se la destinazione è il livello API 24 o versione successiva, usare l'overload di `ShouldOverrideUrlLoading` che accetta un `IWebResourceRequest` per il secondo argomento anziché un `string`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    // For API level 24 and later
    public override bool ShouldOverrideUrlLoading (WebView view, IWebResourceRequest request)
    {
        view.LoadUrl(request.Url.ToString());
        return false;
    }
}
```

Usare quindi il codice seguente per il metodo [`OnCreate()`](xref:Android.App.Activity.OnCreate*)):

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    web_view = FindViewById<WebView> (Resource.Id.webview);
    web_view.Settings.JavaScriptEnabled = true;
    web_view.SetWebViewClient(new HelloWebViewClient());
    web_view.LoadUrl ("https://www.xamarin.com/university");
}
```

In questo modo viene inizializzato il membro [`WebView`](xref:Android.Webkit.WebView) con quello del layout [`Activity`](xref:Android.App.Activity) e viene abilitato JavaScript per il [`WebView`](xref:Android.Webkit.WebView) con [`JavaScriptEnabled`](xref:Android.Webkit.WebSettings.JavaScriptEnabled)
`= true` (vedere la [chiamata C\# da JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) Recipe per informazioni su come chiamare le funzioni di\# C da JavaScript). Infine, una pagina Web iniziale viene caricata con [`LoadUrl(String)`](xref:Android.Webkit.WebView).

Compilare ed eseguire l'app. Verrà visualizzata una semplice app visualizzatore di pagine Web come quella illustrata nello screenshot seguente:

[![esempio di app che visualizza una WebView](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

Per gestire la pressione del tasto **indietro** , aggiungere l'istruzione using seguente:

```csharp
using Android.Views;
```

Aggiungere quindi il metodo seguente all'interno dell'attività `HelloWebView`:

```csharp
public override bool OnKeyDown (Android.Views.Keycode keyCode, Android.Views.KeyEvent e)
{
    if (keyCode == Keycode.Back && web_view.CanGoBack ())
    {
        web_view.GoBack ();
        return true;
    }
    return base.OnKeyDown (keyCode, e);
}
```

Questa [`OnKeyDown(int, KeyEvent)`](xref:Android.App.Activity.OnKeyDown*)
il metodo di callback verrà chiamato ogni volta che si preme un pulsante mentre l'attività è in esecuzione. La condizione all'interno di usa il [`KeyEvent`](xref:Android.Views.KeyEvent) per verificare se il tasto premuto è il pulsante **indietro** e se il [`WebView`](xref:Android.Webkit.WebView) è effettivamente in grado di spostarsi indietro (se ha una cronologia). Se entrambi sono true, viene chiamato il metodo [`GoBack()`](xref:Android.Webkit.WebView.GoBack) , che consente di spostarsi indietro di un passaggio nella cronologia [`WebView`](xref:Android.Webkit.WebView) . La restituzione di `true` indica che l'evento è stato gestito. Se questa condizione non viene soddisfatta, l'evento viene restituito al sistema.

Eseguire di nuovo l'applicazione. A questo punto dovrebbe essere possibile seguire i collegamenti e tornare alla cronologia della pagina:

[![screenshot di esempio del pulsante indietro in azione](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)

*Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base ai termini descritti nella* licenza
[*Creative Commons 2,5 Attribution*](https://creativecommons.org/licenses/by/2.5/).

## <a name="related-links"></a>Collegamenti correlati

- [Call C# from JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) (Chiamare C# da JavaScript)
- [Android. WebKit. WebView](xref:Android.Webkit.WebView)
- [KeyEvent](xref:Android.Webkit.WebView)
