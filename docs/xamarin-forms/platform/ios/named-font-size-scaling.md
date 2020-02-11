---
title: Ridimensionamento dell'accessibilità per le dimensioni dei tipi di carattere
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che disabilita la scalabilità dell'accessibilità per le dimensioni dei tipi di carattere denominati.
ms.prod: xamarin
ms.assetid: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: 13ca4945ed447ae811ee95e308238f04e58ac0bd
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200077"
---
# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>Ridimensionamento dell'accessibilità per le dimensioni dei tipi di carattere

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS Disabilita la scalabilità dell'accessibilità per le dimensioni dei tipi di carattere denominate. Vengono utilizzati in XAML, impostando il `Application.EnableAccessibilityScalingForNamedFontSizes` la proprietà associabile per `false`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

Il `Application.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Application.SetEnableAccessibilityScalingForNamedFontSizes` metodo, [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) nello spazio dei nomi, viene usato per disabilitare le dimensioni del carattere denominate ridimensionate in base alle impostazioni di accessibilità iOS. Inoltre, il `Application.GetEnableAccessibilityScalingForNamedFontSizes` metodo può essere usato per restituire se le dimensioni dei tipi di carattere denominate vengono ridimensionate in base alle impostazioni di accessibilità iOS.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
