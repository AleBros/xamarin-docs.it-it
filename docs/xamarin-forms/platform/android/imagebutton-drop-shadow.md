---
title: ImageButton ombreggiature in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare Android specifico della piattaforma che consente un'ombreggiatura in un ImageButton.
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 56415aff251149aee01c2e2eb7e335e157180962
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209956"
---
# <a name="imagebutton-drop-shadows-on-android"></a>ImageButton ombreggiature in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma Android viene usato per abilitare un'ombreggiatura in una `ImageButton`. Vengono utilizzati in XAML, impostando il `ImageButton.IsShadowEnabled` la proprietà associabile per `true`, insieme a un numero di altre proprietà associabili facoltativi che controllano l'ombreggiatura:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
       <ImageButton ...
                    Source="XamarinLogo.png"
                    BackgroundColor="GhostWhite"
                    android:ImageButton.IsShadowEnabled="true"
                    android:ImageButton.ShadowColor="Gray"
                    android:ImageButton.ShadowRadius="12">
            <android:ImageButton.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </android:ImageButton.ShadowOffset>
        </ImageButton>
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var imageButton = new Xamarin.Forms.ImageButton { Source = "XamarinLogo.png", BackgroundColor = Color.GhostWhite, ... };
imageButton.On<Android>()
           .SetIsShadowEnabled(true)
           .SetShadowColor(Color.Gray)
           .SetShadowOffset(new Size(10, 10))
           .SetShadowRadius(12);
```

> [!IMPORTANT]
> Viene disegnata un'ombreggiatura come parte del `ImageButton` in background e lo sfondo viene disegnato solo se il `BackgroundColor` è impostata. Pertanto, un'ombreggiatura non verrà creata se il `ImageButton.BackgroundColor` proprietà non è impostata.

Il `ImageButton.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il `ImageButton.SetIsShadowEnabled` metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usata per controllare se un'ombreggiatura è abilitata nel `ImageButton`. Inoltre, è possibile richiamare i metodi seguenti per controllare l'ombreggiatura:

- `SetShadowColor` : imposta il colore dell'ombreggiatura. Il colore predefinito è [ `Color.Default` ](xref:Xamarin.Forms.Color.Default*).
- `SetShadowOffset` : imposta l'offset dell'ombreggiatura. L'offset viene modificato la direzione dell'ombreggiatura viene eseguito il cast e viene specificato come un [ `Size` ](xref:Xamarin.Forms.Size) valore. Il `Size` struttura valori sono espressi in unità indipendenti dal dispositivo, con il primo valore in corso la distanza a sinistra (valore negativo) o a destra (valore positivo) e il secondo valore da precedente distanza (negativo) o di sotto (positivo) . Il valore predefinito di questa proprietà è (0,0, 0,0), che comporta l'ombreggiatura viene eseguito il cast intorno a ogni lato del `ImageButton`.
- `SetShadowRadius`: imposta il raggio sfocatura utilizzato per il rendering dell'ombreggiatura. Il valore del raggio predefinito è 10,0.

> [!NOTE]
> Lo stato di un'ombreggiatura è possibile eseguire query chiamando il `GetIsShadowEnabled`, `GetShadowColor`, `GetShadowOffset`, e `GetShadowRadius` metodi.

Il risultato è che è possibile abilitare un'ombreggiatura per un `ImageButton`:

![](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png "Elemento ImageButton con ombreggiatura")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
