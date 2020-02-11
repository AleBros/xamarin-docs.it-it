---
title: Elevazione degli oggetti visivi in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la piattaforma Android specifica che controlla l'elevazione di VisualElements nelle applicazioni destinate all'API 21 o versione successiva.
ms.prod: xamarin
ms.assetid: 5BFD6175-2BBD-41CD-B8F9-521B4750B708
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 243e351f29b056a6d4a567b8e39240a87f37aec2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651878"
---
# <a name="visualelement-elevation-on-android"></a>Elevazione degli oggetti visivi in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma Android viene usato per controllare l'elevazione o l'ordine Z degli elementi visivi nelle applicazioni destinate all'API 21 o versione successiva. L'elevazione di un elemento visivo determina l'ordine di disegno, con elementi visivi con i valori più alti di Z occluding elementi visivi con i valori Z più bassi. Vengono utilizzati in XAML, impostando il `VisualElement.Elevation` proprietà associata una `boolean` valore:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:VisualElement.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

Il `Button.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il `VisualElement.SetElevation` metodo, nelle [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene utilizzato per impostare l'elevazione dei privilegi dell'elemento visivo per un valore nullable `float`. Inoltre, il `VisualElement.GetElevation` metodo può essere utilizzato per recuperare il valore di elevazione dei privilegi di un elemento visivo.

Il risultato è che l'elevazione di elementi visivi può essere controllato in modo che gli elementi visivi con i valori più alti Z nasconde gli elementi visivi con i valori più bassi Z i colori. Pertanto, in questo esempio il secondo [`Button`](xref:Xamarin.Forms.Button) viene eseguito il rendering sopra il [`BoxView`](xref:Xamarin.Forms.BoxView) perché contiene un valore di elevazione dei privilegi più elevato:

![](visualelement-elevation-images/elevation.png)

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
