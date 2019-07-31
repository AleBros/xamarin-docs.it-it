---
title: Visualizzazione di contenuto misto in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la piattaforma Android specifica che Visualizza il contenuto misto in una visualizzazione WebView nelle applicazioni destinate all'API 21 o versione successiva.
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 286a7dceead327d727110d4ebbcecbc2341345b3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656064"
---
# <a name="webview-mixed-content-on-android"></a>Visualizzazione di contenuto misto in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica piattaforma Android controlla se un [`WebView`](xref:Xamarin.Forms.WebView) può visualizzare contenuto misto nelle applicazioni destinate all'API 21 o versione successiva. Il contenuto misto pone contenuto che viene caricato inizialmente tramite una connessione HTTPS, ma che carica le risorse (ad esempio immagini, audio, video, fogli di stile, script) su una connessione HTTP. Vengono utilizzati in XAML, impostando il [`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) proprietà associata a un valore del [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumerazione:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

Il `WebView.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [`WebView.SetMixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) (metodo), nella [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usata per controllare se il contenuto misto può essere visualizzato, con la [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumerazione che fornisce tre possibili valori:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) : indica che il [ `WebView` ](xref:Xamarin.Forms.WebView) consentirà a un'entità origin HTTPS caricare il contenuto da un'origine HTTP.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) : indica che il [ `WebView` ](xref:Xamarin.Forms.WebView) non consente un'entità origin HTTPS caricare il contenuto da un'origine HTTP.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) : indica che il [ `WebView` ](xref:Xamarin.Forms.WebView) proverà a essere compatibile con l'approccio del web browser del dispositivo più recente. Alcuni contenuti HTTP possono essere autorizzati a essere caricata da un'entità origin HTTPS e altri tipi di contenuto verranno bloccati. I tipi di contenuto che sono bloccati o consentiti possono cambiare a ogni versione del sistema operativo.

Il risultato è che un oggetto specificato [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valore viene applicato per il [`WebView`](xref:Xamarin.Forms.WebView), che controlla se è possibile visualizzare il contenuto misto:

[![WebView mista gestione del contenuto specifico della piattaforma](webview-mixed-content-images/webview-mixedcontent.png "WebView mista gestione del contenuto specifico della piattaforma")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "WebView mista specifiche della piattaforma di gestione del contenuto")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
