---
title: Visualizzazione Web
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 2718953c9e5628374c45fa3741d1ad3be3125dd9
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510154"
---
# <a name="xamarinandroid-web-view"></a>Visualizzazione Web Novell. Android

[`WebView`](xref:Android.Webkit.WebView)consente di creare una finestra personalizzata per la visualizzazione di pagine Web (o persino per lo sviluppo di un browser completo). In questa esercitazione verrà creata una semplice[`Activity`](xref:Android.App.Activity)
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

Poiché l'applicazione accederà a Internet, è necessario aggiungere le autorizzazioni appropriate al file manifesto Android. Aprire le proprietà del progetto per specificare le autorizzazioni necessarie per il funzionamento dell'applicazione. Abilitare l' `INTERNET` autorizzazione come illustrato di seguito:

![Impostazione dell'autorizzazione INTERNET nel manifesto Android](web-view-images/01-set-internet-permissions.png)

A questo punto, aprire **MainActivity.cs** e aggiungere una direttiva using per WebKit:

```csharp
using Android.Webkit;
```

Nella parte superiore della `MainActivity` classe dichiarare un [`WebView`](xref:Android.Webkit.WebView) oggetto:

```csharp
WebView web_view;
```

Quando viene **richiesto** di caricare un URL, la richiesta viene delegata per impostazione predefinita al browser predefinito. Per fare in  modo che WebView carichi l'URL, anziché il browser predefinito, è necessario sottoclassare `Android.Webkit.WebViewClient` ed `ShouldOverriderUrlLoading` eseguire l'override del metodo. Un'istanza di questa personalizzata `WebViewClient` viene fornita `WebView`a. A tale scopo, aggiungere la seguente classe `HelloWebViewClient` annidata `MainActivity`all'interno di:

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

Quando `ShouldOverrideUrlLoading` `WebView` restituisce `false`, segnala a Android che l'istanza corrente ha gestito la richiesta e che non sono necessarie altre azioni. 

Se la destinazione è il livello API 24 o versione successiva, usare l'overload `ShouldOverrideUrlLoading` di che accetta `IWebResourceRequest` un oggetto per il `string`secondo argomento anziché:

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

Usare quindi il codice seguente per il [`OnCreate()`](xref:Android.App.Activity.OnCreate*)metodo):

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

In questo modo viene [`WebView`](xref:Android.Webkit.WebView) inizializzato il membro con [`Activity`](xref:Android.App.Activity) quello dal layout [`WebView`](xref:Android.Webkit.WebView) e viene abilitato JavaScript [`JavaScriptEnabled`](xref:Android.Webkit.WebSettings.JavaScriptEnabled) per con 
 `= true` (vedere la [chiamata\# C da JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) ricetta per informazioni su come chiamare le funzioni\# C da JavaScript. Infine, viene caricata una pagina Web iniziale [`LoadUrl(String)`](xref:Android.Webkit.WebView)con.

Compilare ed eseguire l'app. Verrà visualizzata una semplice app visualizzatore di pagine Web come quella illustrata nello screenshot seguente:

[![Esempio di app che visualizza una WebView](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

Per gestire la pressione del tasto **indietro** , aggiungere l'istruzione using seguente:

```csharp
using Android.Views;
```

Aggiungere quindi il metodo seguente all'interno dell' `HelloWebView` attività:

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

Questo[`OnKeyDown(int, KeyEvent)`](xref:Android.App.Activity.OnKeyDown*)
il metodo di callback verrà chiamato ogni volta che si preme un pulsante mentre l'attività è in esecuzione. La condizione all'interno [`KeyEvent`](xref:Android.Views.KeyEvent) di USA per verificare se il tasto premuto è il [`WebView`](xref:Android.Webkit.WebView) pulsante **indietro** e se è effettivamente in grado di spostarsi indietro (se ha una cronologia). Se entrambi sono true, viene chiamato [`GoBack()`](xref:Android.Webkit.WebView.GoBack) il metodo, che consente [`WebView`](xref:Android.Webkit.WebView) di spostarsi indietro di un passaggio nella cronologia. La `true` restituzione indica che l'evento è stato gestito. Se questa condizione non viene soddisfatta, l'evento viene restituito al sistema.

Eseguire di nuovo l'applicazione. A questo punto dovrebbe essere possibile seguire i collegamenti e tornare alla cronologia della pagina:

[![Schermate di esempio del pulsante indietro in azione](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)

*Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella*
[*licenza Creative Commons 2,5 Attribution*](http://creativecommons.org/licenses/by/2.5/).

## <a name="related-links"></a>Collegamenti correlati

- [Call C# from JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) (Chiamare C# da JavaScript)
- [Android.Webkit.WebView](xref:Android.Webkit.WebView)
- [KeyEvent](xref:Android.Webkit.WebView)
