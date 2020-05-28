---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 125685150243ba8e8099cbfbdfec90e5a0b4d6b7
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138580"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>Riconoscimento simultaneo di movimenti di Pan in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Quando un oggetto [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) è collegato a una visualizzazione all'interno di una visualizzazione a scorrimento, tutti i movimenti di panning vengono acquisiti da `PanGestureRecognizer` e non vengono passati alla visualizzazione a scorrimento. Quindi, la visualizzazione a scorrimento non scorre più.

Questa specifica della piattaforma iOS consente a un oggetto `PanGestureRecognizer` in una visualizzazione a scorrimento di acquisire e condividere il movimento della panoramica con la visualizzazione a scorrimento. Viene utilizzato in XAML impostando la [`Application.PanGestureRecognizerShouldRecognizeSimultaneously`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) proprietà associata su `true` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

Il `Application.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ] (Xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Application. SetPanGestureRecognizerShouldRecognizeSimultaneously ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Il metodo Application}, System. Boolean)), nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per controllare se un riconoscitore di movimento Pan in una visualizzazione a scorrimento acquisisce il movimento della panoramica oppure acquisisce e condivide il movimento della panoramica con la visualizzazione a scorrimento. Inoltre, [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Application. GetPanGestureRecognizerShouldRecognizeSimultaneously ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Application})) il metodo può essere utilizzato per restituire se il movimento Pan è condiviso con la visualizzazione a scorrimento che contiene [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) .

Pertanto, con questa specifica della piattaforma abilitata, quando un oggetto [`ListView`](xref:Xamarin.Forms.ListView) contiene un oggetto, l'oggetto [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) `ListView` e l'oggetto `PanGestureRecognizer` riceveranno il movimento Pan ed elaborato. Tuttavia, con questa specifica della piattaforma disabilitata, quando un oggetto `ListView` contiene un oggetto `PanGestureRecognizer` , l'oggetto `PanGestureRecognizer` acquisisce il movimento della panoramica ed elabora l'operazione e `ListView` non riceve il gesto di Pan.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
