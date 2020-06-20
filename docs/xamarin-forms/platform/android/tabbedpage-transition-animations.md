---
title: Animazioni di transizione di pagina TabbedPage in Android
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la piattaforma Android specifica che disabilita le animazioni di transizione quando si esplorano le pagine in un TabbedPage.
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d3ae03ec6cbc3469422e6a2d57f186254e87f40c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140018"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>Animazioni di transizione di pagina TabbedPage in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma Android viene usato per disabilitare le animazioni di transizione durante l'esplorazione delle pagine, a livello di codice o quando si usa la barra delle schede, in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Viene utilizzato in XAML impostando la `TabbedPage.IsSmoothScrollEnabled` proprietà associabile su `false` :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

Il `TabbedPage.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. Il `TabbedPage.SetIsSmoothScrollEnabled` metodo, nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene usato per controllare se le animazioni di transizione verranno visualizzate quando si passa da una pagina all'altra all'interno di un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Inoltre, la `TabbedPage` classe nello `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` spazio dei nomi dispone anche dei metodi seguenti:

- `IsSmoothScrollEnabled`, utilizzato per recuperare se le animazioni di transizione verranno visualizzate quando si passa da una pagina all'altra in un oggetto `TabbedPage` .
- `EnableSmoothScroll`, che viene usato per abilitare le animazioni di transizione durante lo spostamento tra le pagine in un oggetto `TabbedPage` .
- `DisableSmoothScroll`, che viene usato per disabilitare le animazioni di transizione durante l'esplorazione tra le pagine in un oggetto `TabbedPage` .

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
