---
title: Pagina TabbedPage animazioni di transizione in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare Android specifico della piattaforma che disabilita le animazioni di transizione durante lo spostamento tra le pagine in un TabbedPage.
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 1e9c46fe9535c313581d6d0053559a28aa327887
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209146"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>Pagina TabbedPage animazioni di transizione in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma Android consente di disabilitare le animazioni di transizione durante la navigazione tra pagine, a livello di programmazione o quando si usa la barra di schede, in un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Vengono utilizzati in XAML, impostando il `TabbedPage.IsSmoothScrollEnabled` la proprietà associabile per `false`:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

Il `TabbedPage.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il `TabbedPage.SetIsSmoothScrollEnabled` metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usata per controllare se le animazioni di transizione verranno visualizzate quando sposti tra pagine in un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Inoltre, il `TabbedPage` classe di `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` dello spazio dei nomi include anche i metodi seguenti:

- `IsSmoothScrollEnabled`, che consente di sapere se le animazioni di transizione verranno visualizzate quando sposti tra pagine in un `TabbedPage`.
- `EnableSmoothScroll`, che consente di abilitare le animazioni di transizione durante la navigazione tra le pagine in un `TabbedPage`.
- `DisableSmoothScroll`, che consente di disabilitare le animazioni di transizione durante la navigazione tra le pagine in un `TabbedPage`.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
