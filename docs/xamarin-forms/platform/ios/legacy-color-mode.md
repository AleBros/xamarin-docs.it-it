---
title: "la modalità colore legacy di visualElement in iOS Descrizione:" specifiche della piattaforma consente di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che disabilita la Xamarin.Forms modalità di colore legacy. "
ms. prod: Novell MS. AssetID: 60FFBA67-6E06-439B-A5EB-8C808285E2CD ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="visualelement-legacy-color-mode-on-ios"></a>Modalità colore legacy di visualElement in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Alcune delle Xamarin.Forms visualizzazioni presentano una modalità colore legacy. In questa modalità, quando la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) proprietà della vista è impostata su `false` , la vista eseguirà l'override dei colori impostati dall'utente con i colori nativi predefiniti per lo stato disabilitato. Per la compatibilità con le versioni precedenti, questa modalità di colore Legacy rimane il comportamento predefinito per le visualizzazioni supportate.

Questa specifica della piattaforma iOS Disabilita questa modalità di colore legacy in un [`VisualElement`](xref:Xamarin.Forms.VisualElement) , in modo che i colori impostati per una visualizzazione da parte dell'utente rimangano anche quando la visualizzazione è disabilitata. Viene utilizzato in XAML impostando la [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) proprietà associata su `false` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

Il `VisualElement.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. [ `VisualElement.SetIsLegacyColorModeEnabled` ] (Xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. SetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Il metodo visualElement}, System. Boolean)), nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per controllare se la modalità colore legacy è disabilitata. Inoltre, [ `VisualElement.GetIsLegacyColorModeEnabled` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. visualElement. GetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})) il metodo può essere utilizzato per restituire se la modalità di colore legacy è disabilitata.

Il risultato è che la modalità di colore legacy può essere disabilitata, in modo che i colori impostati per una visualizzazione da parte dell'utente rimangano anche quando la visualizzazione è disabilitata:

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Legacy color mode disabled")

> [!NOTE]
> Quando si imposta un oggetto [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) in una vista, la modalità di colore legacy viene ignorata completamente. Per ulteriori informazioni sugli stati visivi, vedere la pagina relativa a [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
