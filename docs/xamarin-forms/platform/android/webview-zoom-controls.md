---
title: Zoom di WebView in Android
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la piattaforma Android specifica che Abilita lo zoom su una visualizzazione WebView.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9264bdf4ab5644b1cdfa0c37f1c7cacd3ae4ed0a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128336"
---
# <a name="webview-zoom-on-android"></a>Zoom di WebView in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android consente di applicare lo zoom a tocco e zoom su un [`WebView`](xref:Xamarin.Forms.WebView) . Viene utilizzato in XAML impostando le `WebView.EnableZoomControls` `WebView.DisplayZoomControls` proprietà associabili e sui `boolean` valori:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

La `WebView.EnableZoomControls` proprietà associabile controlla se il pinch-to-zoom è abilitato nell'oggetto [`WebView`](xref:Xamarin.Forms.WebView) e la `WebView.DisplayZoomControls` proprietà associabile controlla se i controlli zoom sono sovrapposti a `WebView` .

In alternativa, è possibile usare la specifica della piattaforma da C# usando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

Il `WebView.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. Il `WebView.EnableZoomControls` metodo, nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene usato per controllare se il pinch-to-zoom è abilitato in [`WebView`](xref:Xamarin.Forms.WebView) . Il `WebView.DisplayZoomControls` metodo, nello stesso spazio dei nomi, viene usato per controllare se i controlli zoom sono sovrapposti a `WebView` . Inoltre, i `WebView.ZoomControlsEnabled` metodi e `WebView.ZoomControlsDisplayed` possono essere usati per indicare se sono abilitati rispettivamente i controlli pinch-to-zoom e zoom.

Il risultato è che il pinch-to-zoom può essere abilitato in un oggetto [`WebView`](xref:Xamarin.Forms.WebView) e i controlli zoom possono essere sovrapposti a `WebView` :

[![Screenshot di WebView con zoom in Android](webview-zoom-controls-images/webview-zoom.png "Visualizzazione WebView con zoom")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "Visualizzazione WebView con zoom")

> [!IMPORTANT]
> I controlli zoom devono essere abilitati e visualizzati, tramite le proprietà o i metodi associabili corrispondenti, per essere sovrapposti a un [`WebView`](xref:Xamarin.Forms.WebView) .

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
