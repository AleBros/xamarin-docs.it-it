---
title: Riconoscimento simultaneo di movimenti di Pan in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la piattaforma iOS specifica che consente di usare il riconoscimento simultaneo dei movimenti di Pan in un'applicazione.
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 64a39fc8a3a1bd764df424271493d95c5863590f
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198000"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>Riconoscimento simultaneo di movimenti di Pan in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Quando un [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) collegata a una visualizzazione all'interno di una visualizzazione a scorrimento, tutti il pan movimenti acquisiti dal `PanGestureRecognizer` e non vengono passati alla visualizzazione scorrimento. Pertanto, non è più scorre la visualizzazione a scorrimento.

Questa specifica della piattaforma iOS consente a `PanGestureRecognizer` un oggetto in una visualizzazione a scorrimento di acquisire e condividere il movimento della panoramica con la visualizzazione a scorrimento. Vengono utilizzati in XAML, impostando il [`Application.PanGestureRecognizerShouldRecognizeSimultaneously`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) proprietà associata `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

Il `Application.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [`Application.SetPanGestureRecognizerShouldRecognizeSimultaneously`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean)) metodo, nelle [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se un riconoscitore di movimento di zoom in una visualizzazione a scorrimento sarà acquisire il movimento di zoom, o acquisire e condividere il pan movimento con la visualizzazione a scorrimento. Inoltre, il [`Application.GetPanGestureRecognizerShouldRecognizeSimultaneously`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application})) metodo può essere utilizzato da restituire se il movimento di zoom è condiviso con la visualizzazione a scorrimento che contiene il [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer).

Pertanto, con questo specifico della piattaforma abilitata, quando un [`ListView`](xref:Xamarin.Forms.ListView) contiene una [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer), entrambi i `ListView` e il `PanGestureRecognizer` riceverà il movimento di zoom e elaborarlo. Tuttavia, con questo specifico della piattaforma disabilitato, quando un `ListView` contiene un `PanGestureRecognizer`, il `PanGestureRecognizer` sarà acquisire il movimento di zoom ed elaborarlo e il `ListView` non riceverà il movimento di zoom.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
