---
title: Altezza delle barre NavigationPage in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare Android specifico della piattaforma che consente di impostare l'altezza della barra di spostamento su un NavigationPage.
ms.prod: xamarin
ms.assetid: C8A73B64-FE70-408A-A72E-8AF147F0C52C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: e803f138c0be85517bbafbfa6e9d39f39abc7e1d
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209156"
---
# <a name="navigationpage-bar-height-on-android"></a>Altezza delle barre NavigationPage in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma Android imposta l'altezza della barra di spostamento in un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Vengono utilizzati in XAML, impostando il [ `NavigationPage.BarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) proprietà associabili a un valore integer:

```xaml
<NavigationPage ...
                xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
                android:NavigationPage.BarHeight="450">
    ...
</NavigationPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

public class AndroidNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public AndroidNavigationPageCS()
    {
        On<Android>().SetBarHeight(450);
    }
}
```

Il `NavigationPage.On<Android>` metodo consente di specificare che questo specifico della piattaforma sarà eseguita solo su compatibilità delle applicazioni Android. Il [ `NavigationPage.SetBarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.SetBarHeight(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.NavigationPage},System.Int32)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) dello spazio dei nomi, viene usato per impostare l'altezza della barra di spostamento in un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Inoltre, il [ `NavigationPage.GetBarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.GetBarHeight(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.NavigationPage})) metodo può essere utilizzato per restituire l'altezza della barra di spostamento nel `NavigationPage`.

Il risultato è che l'altezza della barra di spostamento a un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) può essere impostato:

![](navigationpage-bar-height-images/navigationpage-barheight.png "Altezza della barra di navigazione NavigationPage")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
