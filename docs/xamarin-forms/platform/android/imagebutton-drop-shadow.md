---
title: "ImageButton Drop Shadows in Android" Description: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la piattaforma Android specifica che Abilita un'ombreggiatura in un ImageButton ".
ms. prod: Novell MS. AssetID: D3604D87-9F9F-4FE2-8B10-DF3B143C0734 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 07/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="imagebutton-drop-shadows-on-android"></a>Ombreggiatura di ImageButton in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma Android viene usato per abilitare un'ombreggiatura in un `ImageButton` . Viene utilizzato in XAML impostando la `ImageButton.IsShadowEnabled` proprietà associabile su `true` , insieme a una serie di proprietà associabili facoltative aggiuntive che controllano l'ombreggiatura:

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

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

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
> Un'ombreggiatura viene disegnata come parte dello `ImageButton` sfondo e lo sfondo viene disegnato solo se la `BackgroundColor` proprietà è impostata. Non verrà quindi disegnata un'ombreggiatura se la `ImageButton.BackgroundColor` proprietà non è impostata.

Il `ImageButton.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. Il `ImageButton.SetIsShadowEnabled` metodo, nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene utilizzato per controllare se un'ombreggiatura è abilitata in `ImageButton` . Inoltre, è possibile richiamare i metodi seguenti per controllare l'ombreggiatura:

- `SetShadowColor`: imposta il colore dell'ombreggiatura. Il colore predefinito è [`Color.Default`](xref:Xamarin.Forms.Color.Default*) .
- `SetShadowOffset`: imposta l'offset dell'ombreggiatura. L'offset modifica la direzione in cui viene proiettata l'ombreggiatura e viene specificato come [`Size`](xref:Xamarin.Forms.Size) valore. I `Size` valori della struttura sono espressi in unità indipendenti dal dispositivo, il primo valore è la distanza verso sinistra (valore negativo) o destro (valore positivo) e il secondo valore è la distanza sopra (valore negativo) o sotto (valore positivo). Il valore predefinito di questa proprietà è (0,0, 0,0), che determina il cast dell'ombreggiatura intorno a ogni lato dell'oggetto `ImageButton` .
- `SetShadowRadius`: imposta il raggio di sfocatura utilizzato per il rendering dell'ombreggiatura. Il valore RADIUS predefinito è 10,0.

> [!NOTE]
> È possibile eseguire una query sullo stato di un'ombreggiatura chiamando i `GetIsShadowEnabled` metodi,, `GetShadowColor` `GetShadowOffset` e `GetShadowRadius` .

Il risultato è che un'ombreggiatura può essere abilitata in un `ImageButton` :

![](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png "ImageButton with drop shadow")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
