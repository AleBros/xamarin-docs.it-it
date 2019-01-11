---
title: Sfocatura VisualElement in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare iOS specifiche della piattaforma che riguardano un VisualElement sfocatura.
ms.prod: xamarin
ms.assetid: 2DE3B65E-B96E-4ECD-92DF-AA42D5205C44
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: a877afda5bd65c3a9c3ec104c2f5df884d13c444
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209276"
---
# <a name="visualelement-blur-on-ios"></a>Sfocatura VisualElement in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma iOS consente di sfocare il contenuto a più livelli di sotto e può essere applicato a qualsiasi [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Vengono utilizzati in XAML, impostando il [ `VisualElement.BlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty) proprietà associata a un valore del [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <AbsoluteLayout HorizontalOptions="Center">
      <Image Source="monkeyface.png" />
      <BoxView x:Name="boxView" ios:VisualElement.BlurEffect="ExtraLight" HeightRequest="300" WidthRequest="300" />
  </AbsoluteLayout>
  ...
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

Il `BoxView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `VisualElement.UseBlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usato per applicare l'effetto di sfocatura, con la [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumerazione che fornisce quattro valori: [ `None` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None), [ `ExtraLight` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight), [ `Light` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light), e [ `Dark` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark).

Il risultato è che un oggetto specificato [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) viene applicato al [ `BoxView` ](xref:Xamarin.Forms.BoxView) dell'istanza, quali sfocature il [ `Image` ](xref:Xamarin.Forms.Image) a più livelli sottostanti:

![](applying-blur-images/blur-effect.png "Specifiche della piattaforma effetto di sfocatura")

> [!NOTE]
> Quando si aggiunge un effetto di sfocatura da un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), gli eventi di tocco verranno comunque ricevuti dal `VisualElement`.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
