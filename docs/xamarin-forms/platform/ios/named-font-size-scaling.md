---
title: Ridimensionamento dell'accessibilità per le dimensioni dei tipi di carattere
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che disabilita la scalabilità dell'accessibilità per le dimensioni dei tipi di carattere denominati.
ms.prod: xamarin
ms.assetid: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9aa29ba4d8e6bda9126ec211af1b4febebd5bab9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128271"
---
# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>Ridimensionamento dell'accessibilità per le dimensioni dei tipi di carattere

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS Disabilita la scalabilità dell'accessibilità per le dimensioni dei tipi di carattere denominate. Viene utilizzato in XAML impostando la `Application.EnableAccessibilityScalingForNamedFontSizes` proprietà associabile su `false` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

Il `Application.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `Application.SetEnableAccessibilityScalingForNamedFontSizes` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per disabilitare le dimensioni del carattere denominate ridimensionate in base alle impostazioni di accessibilità iOS. Inoltre, il `Application.GetEnableAccessibilityScalingForNamedFontSizes` metodo può essere usato per restituire se le dimensioni dei tipi di carattere denominate vengono ridimensionate in base alle impostazioni di accessibilità iOS.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
