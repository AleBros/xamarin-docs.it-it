---
title: "la modalità colore del testo della barra NavigationPage su iOS" Description: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla se il colore del testo della barra di stato in un NavigationPage corrisponde alla luminosità della barra di spostamento.
ms. prod: Novell MS. AssetID: 03698A44-39F1-4030-9AF5-F10A6713828A ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="navigationpage-bar-text-color-mode-on-ios"></a>Modalità colore testo barra NavigationPage in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma controlla se il colore del testo della barra di stato in un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) viene regolato in modo da corrispondere alla luminosità della barra di spostamento. Viene utilizzato in XAML impostando la [`NavigationPage.StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty) proprietà associata su un valore dell' [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumerazione:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

Il `NavigationPage.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. [ `NavigationPage.SetStatusBarTextColorMode` ] (Xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetStatusBarTextColorMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}, Xamarin.Forms . Il metodo PlatformConfiguration. iOSSpecific. StatusBarTextColorMode), nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, controlla se il colore del testo della barra di stato in [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) viene regolato in modo da corrispondere alla luminosità della barra di spostamento, con l' [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumerazione che fornisce due valori possibili:

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust): indica che il colore del testo della barra di stato non deve essere modificato.
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity): indica che il colore del testo della barra di stato deve corrispondere alla luminosità della barra di spostamento.

Inoltre, [ `GetStatusBarTextColorMode` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. GetStatusBarTextColorMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})) il metodo può essere usato per recuperare il valore corrente dell' [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumerazione applicata a [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) .

Il risultato è che il colore del testo della barra di stato su un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) può essere modificato in modo da corrispondere alla luminosità della barra di spostamento. In questo esempio, il colore del testo della barra di stato cambia quando l'utente passa tra le [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) pagine e di un oggetto [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) :

![](status-bar-text-color-images/status-bar-text-color-mode.png "Status Bar Text Color Mode Platform-Specific")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
