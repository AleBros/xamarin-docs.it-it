---
title: "l'altezza della barra NavigationPage su Android" Description: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la piattaforma Android specifica che imposta l'altezza della barra di navigazione in un NavigationPage ".
ms. prod: Novell MS. AssetID: C8A73B64-FE70-408A-A72E-8AF147F0C52C ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 07/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="navigationpage-bar-height-on-android"></a>Altezza della barra NavigationPage in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android imposta l'altezza della barra di spostamento su un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Viene utilizzato in XAML impostando la [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) proprietà associabile su un valore integer:

```xaml
<NavigationPage ...
                xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
                android:NavigationPage.BarHeight="450">
    ...
</NavigationPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

public class AndroidNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public AndroidNavigationPageCS()
    {
        On<Android>().SetBarHeight(450);
    }
}
```

Il `NavigationPage.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo su app compatibili con Android. [ `NavigationPage.SetBarHeight` ] (Xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. SetBarHeight ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Il metodo NavigationPage}, System. Int32)), nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) spazio dei nomi, viene usato per impostare l'altezza della barra di spostamento su un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Inoltre, [ `NavigationPage.GetBarHeight` ] (xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. GetBarHeight ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . NavigationPage})) il metodo può essere usato per restituire l'altezza della barra di spostamento in `NavigationPage` .

Il risultato è che è possibile impostare l'altezza della barra di navigazione in un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) :

![](navigationpage-bar-height-images/navigationpage-barheight.png "NavigationPage navigation bar height")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
