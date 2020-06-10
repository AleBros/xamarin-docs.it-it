---
title: "il separatore della barra NavigationPage su iOS" Description: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che nasconde la linea di separazione e l'ombreggiatura che si trova nella parte inferiore della barra di spostamento in un NavigationPage ".
ms. prod: Novell MS. AssetID: 5A45748A-6779-4441-82F2-415BD68473B9 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="navigationpage-bar-separator-on-ios"></a>Separatore della barra NavigationPage in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS nasconde la linea di separazione e l'ombreggiatura che si trova nella parte inferiore della barra di spostamento in un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Viene utilizzato in XAML impostando la [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) proprietà associabile su `false` :

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

Il `NavigationPage.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. [ `NavigationPage.SetHideNavigationBarSeparator` ] (Xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetHideNavigationBarSeparator ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Il metodo NavigationPage}, System. Boolean)), nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per controllare se il separatore della barra di navigazione è nascosto. Inoltre, [ `NavigationPage.HideNavigationBarSeparator` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. HideNavigationBarSeparator ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})) il metodo può essere usato per restituire se il separatore della barra di navigazione è nascosto.

Il risultato è che il separatore della barra di navigazione in un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) può essere nascosto:

![](navigation-bar-separator-images/navigationpage-hideseparatorbar.png "NavigationPage navigation bar hidden")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
