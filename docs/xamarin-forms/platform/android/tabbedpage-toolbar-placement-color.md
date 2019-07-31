---
title: Posizionamento e colore della barra degli strumenti TabbedPage in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma Android che imposta la posizione e il colore della barra degli strumenti in un TabbedPage.
ms.prod: xamarin
ms.assetid: A5C68D6A-9A5F-42EE-845D-1E5B0CB1544E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 045a2dda350a8d8fc60d8985d94907157f1bcb49
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649834"
---
# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>Posizionamento e colore della barra degli strumenti TabbedPage in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

> [!IMPORTANT]
> Le specifiche della piattaforma che impostano il colore della barra degli strumenti [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) in un oggetto sono ora obsolete e sono [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) state [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) sostituite dalle proprietà e. Per ulteriori informazioni, vedere [creazione di un TabbedPage](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md#creating-a-tabbedpage).

Queste funzionalità specifiche della piattaforma vengono usate per impostare il posizionamento e il colore della barra degli strumenti in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Vengono utilizzati in XAML, impostando il [`TabbedPage.ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) proprietà associata a un valore del [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumerazione e il [`TabbedPage.BarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) e [`TabbedPage.BarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) associate a un [`Color`](xref:Xamarin.Forms.Color):

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

In alternativa, può essere usati da C# usando l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

Il `TabbedPage.On<Android>` metodo consente di specificare che queste funzionalità specifiche della piattaforma verranno eseguito solo in Android. Il [`TabbedPage.SetToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement)) (metodo), nella [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usato per impostare la posizione della barra degli strumenti in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), con la [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumerazione che fornisce i valori seguenti:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default) : indica che la barra degli strumenti viene posizionato in corrispondenza della posizione predefinita della pagina. Questa è la parte superiore della pagina nei telefoni e la parte inferiore della pagina in altri idiomi di dispositivo.
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top) : indica che la barra degli strumenti viene posizionata nella parte superiore della pagina.
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom) : indica che la barra degli strumenti viene posizionata nella parte inferiore della pagina.

Inoltre, il [`TabbedPage.SetBarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color)) e [`TabbedPage.SetBarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color)) metodi vengono usati per impostare il colore degli elementi della barra degli strumenti e gli elementi della barra selezionata, rispettivamente.

> [!NOTE]
> Il [`GetToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})), [`GetBarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})), e [`GetBarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) metodi possono essere utilizzati per recuperare il posizionamento e il colore del [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) sulla barra degli strumenti.

Il risultato è che la posizione della barra degli strumenti, il colore degli elementi della barra degli strumenti e il colore dell'elemento selezionato della barra degli strumenti può essere impostate su un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage):

![](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
