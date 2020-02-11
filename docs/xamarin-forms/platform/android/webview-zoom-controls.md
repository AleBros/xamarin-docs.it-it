---
title: Zoom di WebView in Android
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la piattaforma Android specifica che Abilita lo zoom su una visualizzazione WebView.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68655986"
---
# <a name="webview-zoom-on-android"></a>Zoom di WebView in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica per la piattaforma Android consente di applicare lo zoom a tocco e un controllo zoom su un [`WebView`](xref:Xamarin.Forms.WebView). Viene utilizzato in XAML impostando il `WebView.EnableZoomControls` e `WebView.DisplayZoomControls` proprietà associabili ai valori `boolean`:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

Il `WebView.EnableZoomControls` proprietà associabile controlla se il pinch-to-zoom è abilitato nell' [`WebView`](xref:Xamarin.Forms.WebView)e la proprietà `WebView.DisplayZoomControls` associabile controlla se i controlli zoom sono sovrapposti sul `WebView`.

In alternativa, è possibile usare l'API Fluent per C# la piattaforma specifica:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

Il metodo `WebView.On<Android>` specifica che questa specifica della piattaforma verrà eseguita solo in Android. Il metodo `WebView.EnableZoomControls`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , viene usato per controllare se il pinch-to-zoom è abilitato nella [`WebView`](xref:Xamarin.Forms.WebView). Il metodo `WebView.DisplayZoomControls`, nello stesso spazio dei nomi, viene usato per controllare se i controlli zoom sono sovrapposti all'`WebView`. Inoltre, è possibile usare i metodi `WebView.ZoomControlsEnabled` e `WebView.ZoomControlsDisplayed` per restituire rispettivamente se i controlli pinch-to-zoom e zoom sono abilitati.

Il risultato è che il pinch-to-zoom può essere abilitato in una [`WebView`](xref:Xamarin.Forms.WebView)e i controlli zoom possono essere sovrapposti sul `WebView`:

[![Screenshot di WebView con zoom in Android](webview-zoom-controls-images/webview-zoom.png "Visualizzazione WebView con zoom")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "Visualizzazione WebView con zoom")

> [!IMPORTANT]
> I controlli zoom devono essere abilitati e visualizzati, tramite le proprietà o i metodi associabili corrispondenti, da sovrapporre a un [`WebView`](xref:Xamarin.Forms.WebView).

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
