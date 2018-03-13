---
title: Visualizzazione Web
ms.topic: article
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 0d418786a7364946e4e20100157fa0907b66deeb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="web-view"></a>Visualizzazione Web

[`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) Consente di creare la propria finestra per la visualizzazione delle pagine web (o anche lo sviluppo di un browser completo). In questa esercitazione si creerà un semplice [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/) cui è possibile visualizzare e spostarsi tra le pagine web.

Creare un nuovo progetto denominato **HelloWebView**.

Aprire **Resources/Layout/Main.axml** e inserire il seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

Poiché l'applicazione accede a Internet, è necessario aggiungere che le autorizzazioni appropriate per il Android file manifesto. Aprire le proprietà del progetto per specificare le autorizzazioni richieste dall'applicazione per il funzionamento. Abilitare il `INTERNET` autorizzazione come illustrato di seguito:

![Impostazione di autorizzazioni INTERNET in cui il file manifesto Android](web-view-images/01-set-internet-permissions.png)

Aprire quindi **Mainactivity** e aggiungere un usando la direttiva per Webkit:

```csharp
using Android.Webkit;
```

Nella parte superiore del `MainActivity` classe, dichiarare un [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) oggetto:

```csharp
WebView web_view;
```

Quando il **WebView** viene richiesto di caricare un URL, per impostazione predefinita delegherà la richiesta per il browser predefinito. Per avere il **WebView** caricare l'URL (anziché il browser predefinito), deve creare una sottoclasse `Android.Webkit.WebViewClient` ed eseguire l'override di `ShouldOverriderUrlLoading` (metodo). Un'istanza di questo personalizzato `WebViewClient` viene fornito per il `WebView`. A tale scopo, aggiungere seguenti annidati `HelloWebViewClient` classe all'interno di `MainActivity`:

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

Quando `ShouldOverrideUrlLoading` restituisce `false`, indica al Android che corrente `WebView` istanza gestita la richiesta e che è necessaria alcuna azione ulteriore. 

Se la destinazione è il livello API 24 o versioni successivo, utilizzare l'overload di `ShouldOverrideUrlLoading` che accetta un `IWebResourceRequest` per il secondo argomento anziché un `string`:

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

Successivamente, utilizzare il codice seguente per il [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle)) metodo:

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

Inizializza il membro [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) con quello di [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/) layout e Abilita JavaScript per il [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) con [ `JavaScriptEnabled` ](https://developer.xamarin.com/api/property/Android.Webkit.WebSettings.JavaScriptEnabled/) 
 `= true` (vedere il [chiamare C\# da JavaScript](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript) recipe per informazioni sulla chiamata di C\# funzioni da JavaScript). Infine, una pagina web iniziale viene caricato con [ `LoadUrl(String)` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/%2fM%2fLoadUrl).

Compilare ed eseguire l'app. App Visualizzatore di una semplice pagina web dovrebbe essere come quello illustrato nella schermata seguente:

[![Esempio di app la visualizzazione di WebView](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

Per gestire il **nuovamente** pulsante pressione di tasto, aggiungere la seguente istruzione using:

```csharp
using Android.Views;
```

Successivamente, aggiungere il metodo seguente all'interno di `HelloWebView` attività:

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

Questo [ `OnKeyDown(int, KeyEvent)` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnKeyDown/(Android.Views.Keycode%2cAndroid.Views.KeyEvent)) ogni volta che viene premuto un pulsante mentre è in esecuzione l'attività verrà chiamato il metodo di callback. La condizione all'interno viene utilizzato il [ `KeyEvent` ](https://developer.xamarin.com/api/type/Android.Views.KeyEvent/) per verificare se il tasto premuto viene il **nuovamente** pulsante e se il [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) è effettivamente in grado di spostamento all'indietro (se ha una cronologia). Se entrambi sono true, il [ `GoBack()` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.GoBack/) metodo viene chiamato, che effettua lo spostamento indietro di una fase di [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) cronologia. Restituzione `true` indica che l'evento è stato gestito. Se questa condizione non viene soddisfatta, l'evento viene inviato al sistema.

Eseguire di nuovo l'applicazione. A questo punto dovrebbe essere in grado di seguire i collegamenti e spostarsi indietro nella cronologia della pagina:

[![Schermate di esempio del pulsante Indietro in azione](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)


*Parti di questa pagina sono modifiche basate sul lavoro creati e condivisi per il progetto Android di origine aprire e usare in base alle condizioni descritte nel*
[*Creative Commons 2.5 attribuzione licenza* ](http://creativecommons.org/licenses/by/2.5/).


## <a name="related-links"></a>Collegamenti correlati

- [Chiamare c# da JavaScript](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript)
- [Android.Webkit.WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView)
- [KeyEvent](https://developer.xamarin.com/api/type/Android.Webkit.WebView/Client)
