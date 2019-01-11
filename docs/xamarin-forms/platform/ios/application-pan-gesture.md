---
title: Riconoscimento di movimento zoom simultanee in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare iOS specifiche della piattaforma che consente il riconoscimento di movimento zoom simultanee da utilizzare in un'applicazione.
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 32c3651734fcc94dd75372f0c47f0ffb22b4a4ee
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209706"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>Riconoscimento di movimento zoom simultanee in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Quando un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) collegata a una visualizzazione all'interno di una visualizzazione a scorrimento, tutti il pan movimenti acquisiti dal `PanGestureRecognizer` e non vengono passati alla visualizzazione scorrimento. Pertanto, non è più scorre la visualizzazione a scorrimento.

Questo specifico della piattaforma iOS consente un `PanGestureRecognizer` in una visualizzazione a scorrimento per acquisire e condividere il movimento di zoom con la visualizzazione a scorrimento. Vengono utilizzati in XAML, impostando il [ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) proprietà associata `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

Il `Application.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean)) metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se un riconoscitore di movimento di zoom in una visualizzazione a scorrimento sarà acquisire il movimento di zoom, o acquisire e condividere il pan movimento con la visualizzazione a scorrimento. Inoltre, il [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application})) metodo può essere utilizzato da restituire se il movimento di zoom è condiviso con la visualizzazione a scorrimento che contiene il [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer).

Pertanto, con questo specifico della piattaforma abilitata, quando un [ `ListView` ](xref:Xamarin.Forms.ListView) contiene una [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer), entrambi i `ListView` e il `PanGestureRecognizer` riceverà il movimento di zoom e elaborarlo. Tuttavia, con questo specifico della piattaforma disabilitato, quando un `ListView` contiene un `PanGestureRecognizer`, il `PanGestureRecognizer` sarà acquisire il movimento di zoom ed elaborarlo e il `ListView` non riceverà il movimento di zoom.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
