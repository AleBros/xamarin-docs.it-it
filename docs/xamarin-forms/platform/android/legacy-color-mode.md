---
title: Modalità colore legacy di visualElement in Android
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la piattaforma Android specifica che disabilita la Xamarin.Forms modalità di colore legacy.
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9fe0d751b1aa1cb609100e43c0f0015fd804327a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128856"
---
# <a name="visualelement-legacy-color-mode-on-android"></a>Modalità colore legacy di visualElement in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Alcune delle Xamarin.Forms visualizzazioni presentano una modalità colore legacy. In questa modalità, quando la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) proprietà della vista è impostata su `false` , la vista eseguirà l'override dei colori impostati dall'utente con i colori nativi predefiniti per lo stato disabilitato. Per la compatibilità con le versioni precedenti, questa modalità di colore Legacy rimane il comportamento predefinito per le visualizzazioni supportate.

Questa modalità di colore legacy è disabilitata dalla piattaforma Android, in modo che i colori impostati per una visualizzazione da parte dell'utente rimangano anche quando la visualizzazione è disabilitata. Viene utilizzato in XAML impostando la [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) proprietà associata su `false` :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

Il `VisualElement.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. [ `VisualElement.SetIsLegacyColorModeEnabled` ] (Xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. visualElement. SetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Il metodo visualElement}, System. Boolean)), nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene usato per controllare se la modalità colore legacy è disabilitata. Inoltre, [ `VisualElement.GetIsLegacyColorModeEnabled` ] (xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. visualElement. GetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . VisualElement})) il metodo può essere utilizzato per restituire se la modalità di colore legacy è disabilitata.

Il risultato è che la modalità di colore legacy può essere disabilitata, in modo che i colori impostati per una visualizzazione da parte dell'utente rimangano anche quando la visualizzazione è disabilitata:

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Legacy color mode disabled")

> [!NOTE]
> Quando si imposta un oggetto [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) in una vista, la modalità di colore legacy viene ignorata completamente. Per ulteriori informazioni sugli stati visivi, vedere la pagina relativa a [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
