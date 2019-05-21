---
title: Zoom WebView in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare Android specifico della piattaforma che consente lo zoom in una visualizzazione Web.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 0e180ca5019bd4e5d1351cfb2f7a8c28ade2afe2
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971643"
---
# <a name="webview-zoom-on-android"></a>Zoom WebView in Android

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma Android Abilita zoom con avvicinamento delle dita e un controllo zoom in un' [ `WebView` ](xref:Xamarin.Forms.WebView). Vengono utilizzati in XAML, impostando il `WebView.EnableZoomControls` e `WebView.DisplayZoomControls` proprietà associabili a `boolean` valori:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

Il `WebView.EnableZoomControls` proprietà associabili controlla se zoom con avvicinamento delle dita è abilitato nel [ `WebView` ](xref:Xamarin.Forms.WebView)e il `WebView.DisplayZoomControls` proprietà associabili controlla se i controlli zoom sono sovrapposti i `WebView`.

In alternativa, la specifica della piattaforma può essere utilizzata da C# usando l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

Il `WebView.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il `WebView.EnableZoomControls` metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usata per controllare se è abilitato zoom con avvicinamento delle dita sul [ `WebView` ](xref:Xamarin.Forms.WebView). Il `WebView.DisplayZoomControls` metodo, nello stesso spazio dei nomi, viene utilizzato per controllare se i controlli zoom sono sovrapposti i `WebView`. Inoltre, il `WebView.ZoomControlsEnabled` e `WebView.ZoomControlsDisplayed` metodi possono essere utilizzati da restituire se sono abilitati i controlli zoom con avvicinamento delle dita e zoom, rispettivamente.

Il risultato è che zoom indietro-avanti possono essere abilitata in una [ `WebView` ](xref:Xamarin.Forms.WebView), e possono essere sovrapposti controlli zoom allo stato di `WebView`:

[![Screenshot della finestra ingrandita WebView in Android](webview-zoom-controls-images/webview-zoom.png "ingrandita WebView")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "WebView con zoom")

> [!IMPORTANT]
> I controlli zoom devono essere sia abilitati e visualizzati, tramite le rispettive proprietà associabile o i metodi, per essere sovrapposti in un' [ `WebView` ](xref:Xamarin.Forms.WebView).

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
