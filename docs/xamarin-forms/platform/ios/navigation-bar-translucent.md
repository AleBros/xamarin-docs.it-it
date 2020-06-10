---
title: "la traslucidità della barra NavigationPage su iOS" Description: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che modifica la trasparenza della barra di spostamento in un NavigationPage ".
ms. prod: Novell MS. AssetID: 1150941B-56DB-4781-BE36-A4C4F9F2C500 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="navigationpage-bar-translucency-on-ios"></a>Traslucidità della barra NavigationPage in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS viene utilizzata per modificare la trasparenza della barra di spostamento in un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e viene utilizzata in XAML impostando la [`NavigationPage.IsNavigationBarTranslucent`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty) proprietà associata su un `boolean` valore:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

Il `NavigationPage.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. [ `NavigationPage.EnableTranslucentNavigationBar` ] (Xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. EnableTranslucentNavigationBar ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})) il metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per rendere la barra di spostamento trasparente. Inoltre, la [`NavigationPage`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage) classe nello `Xamarin.Forms.PlatformConfiguration.iOSSpecific` spazio dei nomi dispone anche di [ `DisableTranslucentNavigationBar` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. DisableTranslucentNavigationBar ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})) metodo che ripristina lo stato predefinito della barra di navigazione e [ `SetIsNavigationBarTranslucent` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetIsNavigationBarTranslucent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Metodo NavigationPage}, System. Boolean), che può essere usato per impostare la trasparenza della barra di spostamento chiamando il metodo [ `IsNavigationBarTranslucent` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. IsNavigationBarTranslucent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})) metodo:

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

Il risultato è che la trasparenza della barra di spostamento può essere modificata:

![](navigation-bar-translucent-images/translucent-navigation-bar.png "Translucent Navigation Bar Platform-Specific")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
