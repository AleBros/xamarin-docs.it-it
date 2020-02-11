---
title: Animazioni di transizione di pagina TabbedPage in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la piattaforma Android specifica che disabilita le animazioni di transizione quando si esplorano le pagine in un TabbedPage.
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 4f8d6ec2b06855364970bc9b672c3d3f7b9bfdfc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649848"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>Animazioni di transizione di pagina TabbedPage in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma Android viene usato per disabilitare le animazioni di transizione durante l'esplorazione delle pagine, a livello di codice o quando si usa la barra [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)delle schede, in un. Vengono utilizzati in XAML, impostando il `TabbedPage.IsSmoothScrollEnabled` la proprietà associabile per `false`:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

Il `TabbedPage.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il `TabbedPage.SetIsSmoothScrollEnabled` metodo, nelle [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usata per controllare se le animazioni di transizione verranno visualizzate quando sposti tra pagine in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Inoltre, il `TabbedPage` classe di `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` dello spazio dei nomi include anche i metodi seguenti:

- `IsSmoothScrollEnabled`, che consente di sapere se le animazioni di transizione verranno visualizzate quando sposti tra pagine in un `TabbedPage`.
- `EnableSmoothScroll`, che consente di abilitare le animazioni di transizione durante la navigazione tra le pagine in un `TabbedPage`.
- `DisableSmoothScroll`, che consente di disabilitare le animazioni di transizione durante la navigazione tra le pagine in un `TabbedPage`.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
