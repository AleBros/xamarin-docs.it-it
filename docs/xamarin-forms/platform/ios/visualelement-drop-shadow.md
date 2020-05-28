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
ms.openlocfilehash: 7410386e10f605fdeed452fe37755c1e48e6b9b9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136994"
---
# <a name="visualelement-drop-shadows-on-ios"></a>Ombreggiatura di oggetti visivi in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS viene utilizzata per abilitare un'ombreggiatura in un [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Viene utilizzato in XAML impostando la [`VisualElement.IsShadowEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) proprietà associata su `true` , insieme a un numero di proprietà associate facoltative aggiuntive che controllano l'ombreggiatura:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <BoxView ...
                 ios:VisualElement.IsShadowEnabled="true"
                 ios:VisualElement.ShadowColor="Purple"
                 ios:VisualElement.ShadowOpacity="0.7"
                 ios:VisualElement.ShadowRadius="12">
            <ios:VisualElement.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </ios:VisualElement.ShadowOffset>
         </BoxView>
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var boxView = new BoxView { Color = Color.Aqua, WidthRequest = 100, HeightRequest = 100 };
boxView.On<iOS>()
       .SetIsShadowEnabled(true)
       .SetShadowColor(Color.Purple)
       .SetShadowOffset(new Size(10,10))
       .SetShadowOpacity(0.7)
       .SetShadowRadius(12);
```

Il `VisualElement.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. [ `VisualElement.SetIsShadowEnabled` ] (Xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. SetIsShadowEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Il metodo visualElement}, System. Boolean)), nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per controllare se un'ombreggiatura è abilitata in `VisualElement` . Inoltre, è possibile richiamare i metodi seguenti per controllare l'ombreggiatura:

- [ `SetShadowColor` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. SetShadowColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Oggetti visivi}, Xamarin.Forms . Color)): imposta il colore dell'ombreggiatura. Il colore predefinito è [`Color.Default`](xref:Xamarin.Forms.Color.Default*) .
- [ `SetShadowOffset` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. SetShadowOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Oggetti visivi}, Xamarin.Forms . Size): imposta l'offset dell'ombreggiatura. L'offset modifica la direzione in cui viene proiettata l'ombreggiatura e viene specificato come [`Size`](xref:Xamarin.Forms.Size) valore. I `Size` valori della struttura sono espressi in unità indipendenti dal dispositivo, il primo valore è la distanza verso sinistra (valore negativo) o destro (valore positivo) e il secondo valore è la distanza sopra (valore negativo) o sotto (valore positivo). Il valore predefinito di questa proprietà è (0,0, 0,0), che determina il cast dell'ombreggiatura intorno a ogni lato dell'oggetto `VisualElement` .
- [ `SetShadowOpacity` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. SetShadowOpacity ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, System. Double): imposta l'opacità dell'ombreggiatura, con il valore compreso nell'intervallo 0,0 (trasparente) a 1,0 (opaco). Il valore di opacità predefinito è 0,5.
- [ `SetShadowRadius` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. SetShadowRadius ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, System. Double): imposta il raggio di sfocatura usato per il rendering dell'ombreggiatura. Il valore RADIUS predefinito è 10,0.

> [!NOTE]
> È possibile eseguire una query sullo stato di un'ombreggiatura chiamando il [ `GetIsShadowEnabled` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. GetIsShadowEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})), [ `GetShadowColor` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. GetShadowColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})), [ `GetShadowOffset` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. GetShadowOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})), [ `GetShadowOpacity` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. GetShadowOpacity ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})) e [ `GetShadowRadius` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. GetShadowRadius ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})) (metodi).

Il risultato è che un'ombreggiatura può essere abilitata in un [`VisualElement`](xref:Xamarin.Forms.VisualElement) :

![](drop-shadow-images/drop-shadow.png "Drop shadow enabled")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
