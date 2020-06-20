---
title: Scorrimento della pagina TabbedPage in Android
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare l'oggetto specifico della piattaforma Android che consente di scorrere con un dito orizzontale tra le pagine di un TabbedPage.
ms.prod: xamarin
ms.assetid: D1C09CCB-7246-41A4-8BD2-FA6FABCF1C72
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 61c0137788303363769fdec80a16542e2d8bea5e
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128609"
---
# <a name="tabbedpage-page-swiping-on-android"></a>Scorrimento della pagina TabbedPage in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma Android viene usato per abilitare il scorrimento rapido con un movimento di dito orizzontale tra le pagine in un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Viene utilizzato in XAML impostando la [`TabbedPage.IsSwipePagingEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) proprietà associata su un `boolean` valore:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

Il `TabbedPage.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. [ `TabbedPage.SetIsSwipePagingEnabled` ] (Xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetIsSwipePagingEnabled ( Xamarin.Forms . Il metodo BindableObject, System. Boolean)), nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene usato per abilitare la strisciatura tra le pagine in un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Inoltre, la `TabbedPage` classe nello `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` spazio dei nomi dispone anche di [ `EnableSwipePaging` ] (xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. EnableSwipePaging ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) metodo che Abilita questo specifico della piattaforma e [ `DisableSwipePaging` ] (xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. DisableSwipePaging ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) metodo che disabilita questa specifica della piattaforma. La [`TabbedPage.OffscreenPageLimit`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) proprietà associata e [ `SetOffscreenPageLimit` ] (xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetOffscreenPageLimit ( Xamarin.Forms . BindableObject, System. Int32)), vengono utilizzati per impostare il numero di pagine che devono essere mantenute in uno stato di inattività su entrambi i lati della pagina corrente.

Il risultato è che il paging di scorrimento nelle pagine visualizzate da un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) è abilitato:

![](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
