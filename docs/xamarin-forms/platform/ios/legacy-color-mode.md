---
title: Modalità colore legacy di visualElement in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che disabilita la modalità di colore legacy Novell. Forms.
ms.prod: xamarin
ms.assetid: 60FFBA67-6E06-439B-A5EB-8C808285E2CD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 6a991e1fb3b8d0325a5fd5f56f943bf69f9be602
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655440"
---
# <a name="visualelement-legacy-color-mode-on-ios"></a>Modalità colore legacy di visualElement in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Alcune delle viste di xamarin. Forms offrono una modalità colore legacy. In questa modalità, quando la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) della visualizzazione è impostata su `false`, la visualizzazione sostituiranno i colori impostati dall'utente con i colori nativo predefinito per lo stato disabilitato. Per garantire la compatibilità, questa modalità legacy colore rimane con le versioni precedenti il comportamento predefinito per le visualizzazioni supportate.

Questa specifica della piattaforma iOS Disabilita questa modalità di colore legacy in un [`VisualElement`](xref:Xamarin.Forms.VisualElement), in modo che i colori impostati per una visualizzazione da parte dell'utente rimangano anche quando la visualizzazione è disabilitata. Vengono utilizzati in XAML, impostando il [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) proprietà associata `false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

Il `VisualElement.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [`VisualElement.SetIsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean)) metodo, nel [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se la modalità legacy è disabilitata. Inoltre, il [`VisualElement.GetIsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})) metodo può essere utilizzato da restituire se la modalità legacy è disabilitata.

Il risultato è che può essere disabilitata la modalità legacy, in modo che rimangano anche i colori impostati su una vista dall'utente quando la visualizzazione è disabilitata:

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Modalità legacy colore disabilitata")

> [!NOTE]
> Quando si imposta una [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) in una vista, la modalità legacy di colori viene completamente ignorata. Per altre informazioni sugli stati visual, vedere [di xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
