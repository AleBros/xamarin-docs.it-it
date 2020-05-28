---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b7d039d26895b50f937392941e42a92a6e51f322
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137488"
---
# <a name="webview-javascript-alerts-on-windows"></a>Avvisi JavaScript WebView per Windows

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma consente a un oggetto [`WebView`](xref:Xamarin.Forms.WebView) di visualizzare gli avvisi JavaScript in una finestra di dialogo del messaggio UWP. Viene utilizzato in XAML impostando la [`WebView.IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) proprietà associata su un `boolean` valore:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

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

Il `WebView.On<Windows>` metodo specifica che questa specifica della piattaforma verrà eseguita solo sul piattaforma UWP (Universal Windows Platform). [ `WebView.SetIsJavaScriptAlertEnabled` ] (Xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. WebView. SetIsJavaScriptAlertEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . WebView}, System. Boolean)) il metodo, nello [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) spazio dei nomi, viene usato per controllare se gli avvisi JavaScript sono abilitati. Inoltre, il `WebView.SetIsJavaScriptAlertEnabled` metodo può essere usato per attivare/disabilitare gli avvisi JavaScript chiamando il [`IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) metodo per restituire se sono abilitati:

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

Il risultato è che gli avvisi JavaScript possono essere visualizzati in una finestra di dialogo di messaggio UWP:

![WebView-specifico per la piattaforma di avvisi JavaScript](webview-javascript-alert-images/webview-javascript-alert.png "WebView-specifico per la piattaforma di avvisi JavaScript")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
