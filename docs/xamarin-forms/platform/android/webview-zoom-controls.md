---
title: Zoom di WebView in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la piattaforma Android specifica che Abilita lo zoom su una visualizzazione WebView.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655986"
---
# <a name="webview-zoom-on-android"></a>Zoom di WebView in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android consente di applicare lo zoom a tocco e zoom su un [`WebView`](xref:Xamarin.Forms.WebView). Viene utilizzato in XAML impostando le `WebView.EnableZoomControls` proprietà `boolean` associabili e `WebView.DisplayZoomControls` sui valori:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

La `WebView.EnableZoomControls` proprietà associabile controlla se il pinch-to-zoom è abilitato [`WebView`](xref:Xamarin.Forms.WebView)nell'oggetto e `WebView.DisplayZoomControls` la proprietà associabile `WebView`controlla se i controlli zoom sono sovrapposti a.

In alternativa, è possibile usare l'API Fluent per C# la piattaforma specifica:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

Il `WebView.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il `WebView.EnableZoomControls` metodo, [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) nello spazio dei nomi, viene usato per controllare se il pinch-to- [`WebView`](xref:Xamarin.Forms.WebView)zoom è abilitato in. Il `WebView.DisplayZoomControls` metodo, nello stesso spazio dei nomi, viene usato per controllare se i `WebView`controlli zoom sono sovrapposti a. Inoltre, i metodi `WebView.ZoomControlsEnabled` e `WebView.ZoomControlsDisplayed` possono essere usati per indicare se sono abilitati rispettivamente i controlli pinch-to-zoom e zoom.

Il risultato è che il pinch-to-zoom può essere abilitato in [`WebView`](xref:Xamarin.Forms.WebView)un oggetto e i controlli zoom possono essere sovrapposti `WebView`a:

[ ![Screenshot della visualizzazione WebView con zoom in Android](webview-zoom-controls-images/webview-zoom.png "") ] (webview-zoom-controls-images/webview-zoom-large.png#lightbox "Visualizzazione WebView con zoom")

> [!IMPORTANT]
> I controlli zoom devono essere abilitati e visualizzati, tramite le proprietà o i metodi associabili corrispondenti, per essere [`WebView`](xref:Xamarin.Forms.WebView)sovrapposti a un.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
