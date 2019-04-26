---
title: Visualizzazione Web
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: ae0b67de5856e6baef9a4989a93e65ead2854a62
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61310637"
---
# <a name="web-view"></a>Visualizzazione Web

[`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) Consente di creare la propria finestra per la visualizzazione pagine web (o anche lo sviluppo di un browser completato). In questa esercitazione si creerà una semplice [`Activity`](https://developer.xamarin.com/api/type/Android.App.Activity/)
che possono visualizzare e spostarsi tra le pagine web.

Creare un nuovo progetto denominato **HelloWebView**.

Aprire **Resources/Layout/Main.axml** e inserire il codice seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

Poiché l'applicazione accede a Internet, è necessario aggiungere che le autorizzazioni appropriate per l'Android file manifesto. Aprire le proprietà del progetto per specificare le autorizzazioni richieste dall'applicazione per il funzionamento. Abilitare il `INTERNET` autorizzazione come illustrato di seguito:

![Impostazione di autorizzazioni INTERNET nel manifesto Android](web-view-images/01-set-internet-permissions.png)

A questo punto aprire **MainActivity.cs** e aggiungere un'usando la direttiva per Webkit:

```csharp
using Android.Webkit;
```

In cima il `MainActivity` classe, dichiarare una [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) oggetto:

```csharp
WebView web_view;
```

Quando la **WebView** viene richiesto di caricare un URL, per impostazione predefinita delegherà la richiesta del browser predefinito. Per avere la **WebView** caricare l'URL (anziché il browser predefinito), deve creare una sottoclasse `Android.Webkit.WebViewClient` ed eseguire l'override di `ShouldOverriderUrlLoading` (metodo). Un'istanza di questo personalizzato `WebViewClient` viene fornito per il `WebView`. A questo scopo, aggiungere seguenti annidati `HelloWebViewClient` classe `MainActivity`:

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

Quando `ShouldOverrideUrlLoading` restituisce `false`, viene segnalato ad Android che corrente `WebView` istanza gestita la richiesta e che non sia necessaria alcuna azione ulteriore. 

Se la destinazione è il livello API 24 o versioni successivo, usare l'overload del `ShouldOverrideUrlLoading` che accetta un `IWebResourceRequest` per il secondo argomento anziché un `string`:

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

Successivamente, usare il codice seguente per il [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
metodo:

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

Ciò consente di inizializzare il membro [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) con quello del [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/) layout e Abilita JavaScript per il [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) con [ `JavaScriptEnabled` ](https://developer.xamarin.com/api/property/Android.Webkit.WebSettings.JavaScriptEnabled/) 
 `= true` (vedere la [C chiama\# da JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) "ricetta" per informazioni sulla chiamata di C\# funzioni da JavaScript). Infine, una pagina web iniziale viene caricato con [ `LoadUrl(String)` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/%2fM%2fLoadUrl).

Compilare ed eseguire l'app. Si otterrà un'app Visualizzatore di pagina web semplice come quello mostrato nello screenshot seguente:

[![Esempio di app la visualizzazione di una visualizzazione Web](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

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

Questo [`OnKeyDown(int, KeyEvent)`](https://developer.xamarin.com/api/member/Android.App.Activity.OnKeyDown/(Android.Views.Keycode%2cAndroid.Views.KeyEvent))
metodo di callback verrà chiamato ogni volta che viene premuto un pulsante mentre l'attività è in esecuzione. La condizione all'interno Usa la [ `KeyEvent` ](https://developer.xamarin.com/api/type/Android.Views.KeyEvent/) per verificare se premuto il tasto è il **nuovamente** pulsante e indica se il [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) è effettivamente in grado di spostamento all'indietro (se ha una cronologia). Se entrambi sono true, il [ `GoBack()` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.GoBack/) viene chiamato il metodo che effettua lo spostamento indietro di una fase il [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) cronologia. Restituzione `true` indica che l'evento è stato gestito. Se questa condizione non viene soddisfatta, l'evento viene inviato nuovamente al sistema.

Eseguire di nuovo l'applicazione. A questo punto dovrebbe essere in grado di usare i collegamenti e navigare indietro nella cronologia della pagina:

[![Screenshot di esempio del pulsante Indietro in azione](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)


*Parti di questa pagina sono modifiche basate sul lavoro creato e condiviso da Android Open Source Project e usate in base a condizioni descritte nel*
[*licenza Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).


## <a name="related-links"></a>Collegamenti correlati

- [Call C# from JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) (Chiamare C# da JavaScript)
- [Android.Webkit.WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView)
- [KeyEvent](https://developer.xamarin.com/api/type/Android.Webkit.WebView/Client)
