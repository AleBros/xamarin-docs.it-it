---
title: Visibilità dell'indicatore Home in iOS
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che imposta la visibilità dell'indicatore Home in una pagina.
ms.prod: xamarin
ms.assetid: F81022E0-3C6C-49C0-A000-FAF6574D3FB7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e76684ffb293380c283153c35c907acc50e40aab
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128076"
---
# <a name="home-indicator-visibility-on-ios"></a>Visibilità dell'indicatore Home in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS imposta la visibilità dell'indicatore Home in un [`Page`](xref:Xamarin.Forms.Page) . Viene utilizzato in XAML impostando la [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHiddenProperty) proprietà associabile su un oggetto `boolean` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersHomeIndicatorAutoHidden="true">
    ...
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersHomeIndicatorAutoHidden(true);
```

Il `Page.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. [ `Page.SetPrefersHomeIndicatorAutoHidden` ] (Xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Page. SetPrefersHomeIndicatorAutoHidden ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Il metodo Page}, System. Boolean)), nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, controlla la visibilità dell'indicatore Home. Inoltre, [ `Page.PrefersHomeIndicatorAutoHidden` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Page. PrefersHomeIndicatorAutoHidden ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Page})) il metodo può essere usato per recuperare la visibilità dell'indicatore Home.

Il risultato è che è possibile controllare la visibilità dell'indicatore Home su un oggetto [`Page`](xref:Xamarin.Forms.Page) :

![Screenshot della visibilità dell'indicatore Home in una pagina iOS](page-home-indicator-images/home-indicator-visibility.png "Visibilità dell'indicatore Home di una pagina")

> [!NOTE]
> Questa specifica della piattaforma può essere applicata agli [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) oggetti,, [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
