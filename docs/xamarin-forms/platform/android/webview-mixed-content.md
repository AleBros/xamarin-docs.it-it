---
title: Visualizzazione di contenuto misto in Android
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la piattaforma Android specifica che Visualizza contenuto misto in una visualizzazione WebView nelle applicazioni destinate all'API 21 o versione successiva.
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: f9b4a12d3049cea37235cc04805a7411a9bdb236
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696498"
---
# <a name="webview-mixed-content-on-android"></a>Visualizzazione di contenuto misto in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android controlla se un [`WebView`](xref:Xamarin.Forms.WebView) può visualizzare contenuto misto nelle applicazioni destinate all'API 21 o versione successiva. Il contenuto misto è contenuto inizialmente caricato tramite una connessione HTTPS, ma consente di caricare le risorse (ad esempio immagini, audio, video, fogli di stile, script) su una connessione HTTP. Viene utilizzato in XAML impostando la proprietà associata [`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) su un valore dell'enumerazione [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

In alternativa, è possibile C# usare l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

Il metodo `WebView.On<Android>` specifica che questa specifica della piattaforma verrà eseguita solo in Android. Il metodo [`WebView.SetMixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) , nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , viene usato per controllare se è possibile visualizzare il contenuto misto, con l'enumerazione [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) che fornisce tre valori possibili:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) : indica che l' [`WebView`](xref:Xamarin.Forms.WebView) consentirà a un'origine HTTPS di caricare il contenuto da un'origine http.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) : indica che l' [`WebView`](xref:Xamarin.Forms.WebView) non consentirà a un'origine HTTPS di caricare il contenuto da un'origine http.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) : indica che il [`WebView`](xref:Xamarin.Forms.WebView) tenterà di essere compatibile con l'approccio del Web browser del dispositivo più recente. Potrebbe essere possibile caricare un contenuto HTTP da un'origine HTTPS e altri tipi di contenuto verranno bloccati. I tipi di contenuto bloccati o consentiti possono cambiare in ogni versione del sistema operativo.

Il risultato è che un valore [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) specificato viene applicato al [`WebView`](xref:Xamarin.Forms.WebView), che controlla se è possibile visualizzare il contenuto misto:

[![Panoramica della gestione di contenuto misto per la piattaforma](webview-mixed-content-images/webview-mixedcontent.png "Panoramica della gestione di contenuto misto per la piattaforma")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "Panoramica della gestione di contenuto misto per la piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
