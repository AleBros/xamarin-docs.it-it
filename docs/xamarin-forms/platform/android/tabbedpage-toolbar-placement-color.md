---
title: Posizionamento e colore della barra degli strumenti TabbedPage in Android
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma Android che imposta la posizione e il colore della barra degli strumenti in un TabbedPage.
ms.prod: xamarin
ms.assetid: A5C68D6A-9A5F-42EE-845D-1E5B0CB1544E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: b95b73759d44631da0525fce16218b8a87ca0507
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842848"
---
# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>Posizionamento e colore della barra degli strumenti TabbedPage in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

> [!IMPORTANT]
> Le specifiche della piattaforma che impostano il colore della barra degli strumenti in una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) sono ora obsolete e sono state sostituite dalle proprietà [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) e [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) . Per altre informazioni, vedere [creare un TabbedPage](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md#create-a-tabbedpage).

Queste specifiche della piattaforma vengono usate per impostare la posizione e il colore della barra degli strumenti in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Vengono utilizzati in XAML impostando il [`TabbedPage.ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) proprietà associata su un valore dell'enumerazione [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) e il [`TabbedPage.BarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) e [`TabbedPage.BarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) le proprietà associate a un [`Color`](xref:Xamarin.Forms.Color):

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

In alternativa, è possibile C# usare l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

Il metodo `TabbedPage.On<Android>` specifica che queste specifiche della piattaforma vengono eseguite solo su Android. Il metodo [`TabbedPage.SetToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement)) , nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , viene usato per impostare la posizione della barra degli strumenti in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), con l'enumerazione [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) che fornisce i valori seguenti:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default) : indica che la barra degli strumenti viene posizionata nella posizione predefinita della pagina. Si tratta della parte superiore della pagina sui telefoni e della parte inferiore della pagina in altri idiomi del dispositivo.
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top) : indica che la barra degli strumenti è posizionata nella parte superiore della pagina.
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom) : indica che la barra degli strumenti è posizionata nella parte inferiore della pagina.

Inoltre, i metodi [`TabbedPage.SetBarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color)) e [`TabbedPage.SetBarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color)) vengono utilizzati per impostare rispettivamente il colore degli elementi della barra degli strumenti e gli elementi selezionati della barra degli strumenti.

> [!NOTE]
> È possibile utilizzare i metodi [`GetToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})), [`GetBarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage}))e [`GetBarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) per recuperare la posizione e il colore della barra degli strumenti [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

Il risultato è che la posizione della barra degli strumenti, il colore degli elementi della barra degli strumenti e il colore dell'elemento della barra degli strumenti selezionato possono essere impostati in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage):

![](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
