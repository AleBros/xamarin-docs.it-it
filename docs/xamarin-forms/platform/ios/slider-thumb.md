---
title: Dispositivo di scorrimento controllo Thumb toccare in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare iOS specifiche della piattaforma che consente la proprietà Slider deve essere impostata dalla se si tocca la barra di scorrimento.
ms.prod: xamarin
ms.assetid: D0915D37-9A59-4728-BB6A-FE094A661275
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: b195a277defa04bac88ad65b928957c6efff4601
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925344"
---
# <a name="slider-thumb-tap-on-ios"></a>Dispositivo di scorrimento controllo Thumb toccare in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Questo specifico della piattaforma iOS consente i [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) proprietà da impostare toccando in base a una posizione per il [ `Slider` ](xref:Xamarin.Forms.Slider) barra, anziché dover trascinare il `Slider` thumb. Vengono utilizzati in XAML, impostando il [ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) la proprietà associabile per `true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

Il `Slider.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `Slider.SetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.SetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider},System.Boolean)) (metodo), nella [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se una scelta nel `Slider` barra imposterà il [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) proprietà. Inoltre, il [ `Slider.GetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.GetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider})) metodo può essere utilizzato per restituire se una scelta nel `Slider` barra imposterà il `Slider.Value` proprietà.

Il risultato è che un tocco sul [ `Slider` ](xref:Xamarin.Forms.Slider) barra è possibile spostare il `Slider` thumb e impostare il [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) proprietà:

![](slider-thumb-images/slider-updateontap.png "Aggiornamento dispositivo di scorrimento al tocco abilitata")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
