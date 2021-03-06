---
title: Ombreggiatura di ImageButton in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la piattaforma Android specifica che Abilita un'ombreggiatura in un ImageButton.
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 567216171dd289e849ee0164452e4b876953f2a3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653588"
---
# <a name="imagebutton-drop-shadows-on-android"></a>Ombreggiatura di ImageButton in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma Android viene usato per abilitare un'ombreggiatura in un `ImageButton`. Vengono utilizzati in XAML, impostando il `ImageButton.IsShadowEnabled` la proprietà associabile per `true`, insieme a un numero di altre proprietà associabili facoltativi che controllano l'ombreggiatura:

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

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

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

Il `ImageButton.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il `ImageButton.SetIsShadowEnabled` metodo, nel [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usata per controllare se un'ombreggiatura è abilitata nel `ImageButton`. Inoltre, è possibile richiamare i metodi seguenti per controllare l'ombreggiatura:

- `SetShadowColor` : imposta il colore dell'ombreggiatura. Il colore predefinito è [`Color.Default`](xref:Xamarin.Forms.Color.Default*).
- `SetShadowOffset` : imposta l'offset dell'ombreggiatura. L'offset viene modificato la direzione dell'ombreggiatura viene eseguito il cast e viene specificato come un [`Size`](xref:Xamarin.Forms.Size) valore. Il `Size` struttura valori sono espressi in unità indipendenti dal dispositivo, con il primo valore in corso la distanza a sinistra (valore negativo) o a destra (valore positivo) e il secondo valore da precedente distanza (negativo) o di sotto (positivo) . Il valore predefinito di questa proprietà è (0,0, 0,0), che comporta l'ombreggiatura viene eseguito il cast intorno a ogni lato del `ImageButton`.
- `SetShadowRadius`: imposta il raggio sfocatura utilizzato per il rendering dell'ombreggiatura. Il valore del raggio predefinito è 10,0.

> [!NOTE]
> Lo stato di un'ombreggiatura è possibile eseguire query chiamando il `GetIsShadowEnabled`, `GetShadowColor`, `GetShadowOffset`, e `GetShadowRadius` metodi.

Il risultato è che è possibile abilitare un'ombreggiatura per un `ImageButton`:

![](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png "Elemento ImageButton con ombreggiatura")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
