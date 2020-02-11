---
title: Avvisi JavaScript WebView per Windows
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. In questo articolo viene illustrato come utilizzare la piattaforma Windows specifica che consente a una visualizzazione WebView di visualizzare gli avvisi JavaScript in una finestra di dialogo di messaggio UWP.
ms.prod: xamarin
ms.assetid: 95A153A1-72A0-4C0B-A452-ACE966BB12CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 2bddf8ad314c49d8865863f6e67fc90a2bf80eb7
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656817"
---
# <a name="webview-javascript-alerts-on-windows"></a>Avvisi JavaScript WebView per Windows

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma consente a un [`WebView`](xref:Xamarin.Forms.WebView) per visualizzare gli avvisi di JavaScript in una finestra di dialogo messaggio UWP. Vengono utilizzati in XAML, impostando il [`WebView.IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) proprietà associata una `boolean` valore:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var webView = new Xamarin.Forms.WebView
{
  Source = new HtmlWebViewSource
  {
    Html = @"<html><body><button onclick=""window.alert('Hello World from JavaScript');"">Click Me</button></body></html>"
  }
};
webView.On<Windows>().SetIsJavaScriptAlertEnabled(true);
```

Il `WebView.On<Windows>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo nella piattaforma Windows universale. Il [`WebView.SetIsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean)) metodo, nelle [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene usata per controllare se sono abilitati gli avvisi di JavaScript. Inoltre, il `WebView.SetIsJavaScriptAlertEnabled` metodo può essere utilizzato per attivare o disattivare gli avvisi di JavaScript chiamando il [`IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) metodo restituisca se sono abilitati:

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

Il risultato è che è possono visualizzare gli avvisi di JavaScript in una finestra di dialogo messaggio UWP:

![Avviso di JavaScript di WebView specifiche della piattaforma](webview-javascript-alert-images/webview-javascript-alert.png "avviso JavaScript di WebView specifiche della piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
