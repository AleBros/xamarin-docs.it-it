---
title: Separatore indicatore NavigationPage in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare il iOS specifiche della piattaforma che consente di nascondere la linea di separazione e nella parte inferiore della barra di spostamento su un NavigationPage relativa ombreggiatura.
ms.prod: xamarin
ms.assetid: 5A45748A-6779-4441-82F2-415BD68473B9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: a0483a87270a1ca7a2b00f907d5561c865d03541
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209846"
---
# <a name="navigationpage-bar-separator-on-ios"></a>Separatore indicatore NavigationPage in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Consente di nascondere questo specifico della piattaforma iOS la linea di separazione e sullo shadow che si trova nella parte inferiore della barra di spostamento in un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Vengono utilizzati in XAML, impostando il [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) la proprietà associabile per `false`:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

Il `NavigationPage.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `NavigationPage.SetHideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetHideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean)) metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se il separatore barra di navigazione è nascosto. Inoltre, il [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) metodo può essere utilizzato da restituire se il separatore barra di navigazione è nascosto.

Il risultato è che il separatore barra di navigazione in una [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) possono essere nascosti:

![](navigation-bar-separator-images/navigationpage-hideseparatorbar.png "Barra di spostamento NavigationPage nascosta")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
