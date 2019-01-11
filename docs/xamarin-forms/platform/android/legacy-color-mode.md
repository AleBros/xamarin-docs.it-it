---
title: Modalità colore VisualElement Legacy in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare Android specifico della piattaforma che disabilita la modalità di colore legacy di xamarin. Forms.
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: c04cb4a48a984ed9854ae791e554d33b665241ea
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208746"
---
# <a name="visualelement-legacy-color-mode-on-android"></a>Modalità colore VisualElement Legacy in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Alcune delle viste di xamarin. Forms offrono una modalità colore legacy. In questa modalità, quando la [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) della visualizzazione è impostata su `false`, la visualizzazione sostituiranno i colori impostati dall'utente con i colori nativo predefinito per lo stato disabilitato. Per garantire la compatibilità, questa modalità legacy colore rimane con le versioni precedenti il comportamento predefinito per le visualizzazioni supportate.

Questo specifico della piattaforma Android disabilita questa modalità colore legacy, in modo che i colori impostati su una vista dall'utente rimangano anche quando la visualizzazione è disabilitata. Vengono utilizzati in XAML, impostando il [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) proprietà associata `false`:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

Il `VisualElement.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usata per controllare se la modalità legacy è disabilitata. Inoltre, il [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement})) metodo può essere utilizzato da restituire se la modalità legacy è disabilitata.

Il risultato è che può essere disabilitata la modalità legacy, in modo che rimangano anche i colori impostati su una vista dall'utente quando la visualizzazione è disabilitata:

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Modalità legacy colore disabilitata")

> [!NOTE]
> Quando si imposta una [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) in una vista, la modalità legacy di colori viene completamente ignorata. Per altre informazioni sugli stati visual, vedere [di xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
