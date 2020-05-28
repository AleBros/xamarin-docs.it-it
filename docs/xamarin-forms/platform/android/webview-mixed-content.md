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
ms.openlocfilehash: 7269b0617be7199c365f350fc26ecd42256e28f3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128453"
---
# <a name="webview-mixed-content-on-android"></a>Visualizzazione di contenuto misto in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica piattaforma Android controlla se un [`WebView`](xref:Xamarin.Forms.WebView) può visualizzare contenuto misto nelle applicazioni destinate all'API 21 o versione successiva. Il contenuto misto è contenuto inizialmente caricato tramite una connessione HTTPS, ma consente di caricare le risorse (ad esempio immagini, audio, video, fogli di stile, script) su una connessione HTTP. Viene utilizzato in XAML impostando la [`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) proprietà associata su un valore dell' [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumerazione:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

Il `WebView.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. [ `WebView.SetMixedContentMode` ] (Xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. WebView. SetMixedContentMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . WebView}, Xamarin.Forms . Il metodo PlatformConfiguration. AndroidSpecific. MixedContentHandling), nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene usato per controllare se è possibile visualizzare il contenuto misto, con l' [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumerazione che fornisce tre valori possibili:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow): indica che [`WebView`](xref:Xamarin.Forms.WebView) consentirà a un'origine HTTPS di caricare il contenuto da un'origine http.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow): indica che l'oggetto [`WebView`](xref:Xamarin.Forms.WebView) non consentirà a un'origine HTTPS di caricare il contenuto da un'origine http.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode): indica che il tenterà [`WebView`](xref:Xamarin.Forms.WebView) di essere compatibile con l'approccio del browser Web per dispositivi più recente. Potrebbe essere possibile caricare un contenuto HTTP da un'origine HTTPS e altri tipi di contenuto verranno bloccati. I tipi di contenuto bloccati o consentiti possono cambiare in ogni versione del sistema operativo.

Il risultato è che un [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valore specificato viene applicato a [`WebView`](xref:Xamarin.Forms.WebView) , che controlla se è possibile visualizzare il contenuto misto:

[![Panoramica della gestione di contenuto misto per la piattaforma](webview-mixed-content-images/webview-mixedcontent.png "Panoramica della gestione di contenuto misto per la piattaforma")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "Panoramica della gestione di contenuto misto per la piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
