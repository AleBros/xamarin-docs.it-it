---
title: "l'elevazione visiva in Android" Descrizione: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la piattaforma Android specifica che controlla l'elevazione di VisualElements nelle applicazioni destinate all'API 21 o versione successiva. "
ms. prod: Novell MS. AssetID: 5BFD6175-2BBD-41CD-B8F9-521B4750B708 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 07/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="visualelement-elevation-on-android"></a>Elevazione degli oggetti visivi in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma Android viene usato per controllare l'elevazione o l'ordine Z degli elementi visivi nelle applicazioni destinate all'API 21 o versione successiva. L'elevazione di un elemento visivo determina il relativo ordine di disegno, con elementi visivi con valori Z più alti occlusione elementi visivi con valori Z inferiori. Viene utilizzato in XAML impostando la `VisualElement.Elevation` proprietà associata su un `boolean` valore:

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

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

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

Il `Button.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. Il `VisualElement.SetElevation` metodo, nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene usato per impostare l'elevazione dell'elemento visivo su un Nullable `float` . Inoltre, il `VisualElement.GetElevation` metodo può essere utilizzato per recuperare il valore di elevazione di un elemento visivo.

Il risultato è che l'elevazione degli elementi visivi può essere controllata in modo che gli elementi visivi con valori Z più alti occludere gli elementi visivi con valori Z inferiori. Pertanto, in questo esempio viene eseguito il rendering del secondo al di [`Button`](xref:Xamarin.Forms.Button) sopra di [`BoxView`](xref:Xamarin.Forms.BoxView) perché ha un valore di elevazione più elevato:

![](visualelement-elevation-images/elevation.png)

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
