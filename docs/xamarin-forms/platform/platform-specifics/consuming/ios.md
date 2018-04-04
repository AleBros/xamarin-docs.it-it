---
title: le specifiche di piattaforma iOS
description: Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti. In questo articolo viene illustrato come utilizzare la piattaforma-specifiche di iOS che vengono compilate in xamarin. Forms.
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/16/2017
ms.openlocfilehash: 7826962cd3bf9595a63841e3f2d9fb377d1a0574
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="ios-platform-specifics"></a>le specifiche di piattaforma iOS

_Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti. In questo articolo viene illustrato come utilizzare la piattaforma-specifiche di iOS che vengono compilate in xamarin. Forms._

In iOS, xamarin. Forms contiene le specifiche di piattaforma seguenti:

- Sfocatura il supporto per qualunque [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/). Per ulteriori informazioni, vedere [l'applicazione di sfocatura](#blur).
- Controllare se il titolo della pagina viene visualizzato come un titolo di grandi dimensioni nella barra di navigazione della pagina. Per ulteriori informazioni, vedere [titoli di grandi dimensioni di visualizzazione](#large_title).
- Verificare il contenuto della pagina è posizionato su un'area dello schermo sicuro per tutti i dispositivi iOS. Per ulteriori informazioni, vedere [abilitare la Guida di Layout Area sicura](#safe_area_layout).
- Una barra di navigazione semitrasparente. Per ulteriori informazioni, vedere [effettua la barra di navigazione semitrasparente](#translucent_navigation_bar).
- Controllare se il testo della barra di stato di colore in un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) viene regolato in modo da corrispondere la luminosità della barra di spostamento. Per ulteriori informazioni, vedere [regolando la modalità di colore testo barra di stato](#status_bar_color_mode).
- Assicurando che immesso testo si inserisce un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) regolando le dimensioni del carattere. Per ulteriori informazioni, vedere [regolare le dimensioni del carattere di una voce](#adjust_font_size).
- Il controllo quando la selezione di elementi si verifica in un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/). Per ulteriori informazioni, vedere [controllo selezione elemento](#picker_update_mode).
- L'impostazione di visibilità della barra di stato su un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). Per ulteriori informazioni, vedere [l'impostazione di visibilità della barra di stato in una pagina](#set_status_bar_visibility).
- Controllare se un [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) gestisce un gesto tocco o passa al relativo contenuto. Per ulteriori informazioni, vedere [ritardando ritocchi contenuto in un elemento ScrollView](#delay_content_touches).

<a name="blur" />

## <a name="applying-blur"></a>L'applicazione di sfocatura

Questo specifico della piattaforma viene utilizzata di sfocatura del contenuto a più livelli sottostanti e vengono utilizzato in XAML impostando il [ `VisualElement.BlurEffect` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty/) un valore della proprietà associata di [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <AbsoluteLayout HorizontalOptions="Center">
    <Image Source="monkeyface.png" />
    <BoxView x:Name="boxView" ios:VisualElement.BlurEffect="ExtraLight" HeightRequest="300" WidthRequest="300" />
  </AbsoluteLayout>
  ...
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

Il `BoxView.On<iOS>` metodo specifica che questo specifico della piattaforma verrà eseguiti solo su iOS. Il [ `VisualElement.UseBlurEffect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) dello spazio dei nomi, viene utilizzato per applicare l'effetto di sfocatura, con la [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) enumerazione fornisce quattro valori: [ `None` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None/), [ `ExtraLight` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight/), [ `Light` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light/), e [ `Dark` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark/).

Il risultato è che un oggetto specificato [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) viene applicato al [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) istanza, quali sfocature il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) a più livelli sottostanti:

![](ios-images/blur-effect.png "Sfocatura effetto specifico della piattaforma")

<a name="large_title" />

## <a name="displaying-large-titles"></a>Visualizzazione di titoli di grandi dimensioni

Questo specifico della piattaforma viene utilizzato per visualizzare il titolo della pagina come un titolo di grandi dimensioni nella barra di spostamento, per i dispositivi che usano iOS 11 o versione successiva. Un titolo di grandi dimensioni viene allineato a sinistra e viene utilizzato un carattere di dimensioni maggiori e passa a un titolo standard l'utente inizia a scorrimento del contenuto, in modo che l'area dello schermo viene utilizzato in modo efficiente. Tuttavia, con orientamento orizzontale, il titolo verrà restituito al centro della barra di spostamento per ottimizzare il layout del contenuto. Viene utilizzata in XAML impostando i `NavigationPage.PrefersLargeTitles` associata a un `boolean` valore:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

In alternativa può essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

Il `NavigationPage.On<iOS>` metodo specifica che questo specifico della piattaforma verrà eseguiti solo su iOS. Il `NavigationPage.SetPrefersLargeTitle` (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) dello spazio dei nomi, controlla se sono abilitati i titoli di grandi dimensioni.

Condizione che i titoli di grandi dimensioni sono attivati i [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), tutte le pagine nello stack di navigazione visualizzerà i titoli di grandi dimensioni. Questo comportamento può essere sottoposto a override nelle pagine impostando il `Page.LargeTitleDisplay` un valore della proprietà associata di `LargeTitleDisplayMode` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

In alternativa, il comportamento di pagina può essere sottoposto a override dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

Il `Page.On<iOS>` metodo specifica che questo specifico della piattaforma verrà eseguiti solo su iOS. Il `Page.SetLargeTitleDisplay` (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) dello spazio dei nomi, controlla il comportamento di titolo di grandi dimensioni nel [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), con la `LargeTitleDisplayMode` enumerazione fornisce tre possibili valori:

- `Always` – forzare la barra di spostamento e il tipo di carattere dimensioni da utilizzare il formato di grandi dimensioni.
- `Automatic` : utilizzare lo stesso stile (grande o piccolo) dell'elemento precedente nello stack di navigazione.
- `Never` – forzare l'utilizzo della barra di spostamento formato regolare di piccole dimensioni.

Inoltre, il `SetLargeTitleDisplay` metodo può essere utilizzato per attivare o disattivare i valori di enumerazione chiamando il `LargeTitleDisplay` metodo, che restituisce l'attuale `LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

Il risultato è che un oggetto specificato `LargeTitleDisplayMode` viene applicato per la [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), che controlla il comportamento di titolo di grandi dimensioni:

![](ios-images/large-title.png "Sfocatura effetto specifico della piattaforma")

<a name="safe_area_layout" />

## <a name="enabling-the-safe-area-layout-guide"></a>Abilitare la Guida di Layout Area di sicurezza

Questo specifico della piattaforma viene utilizzato per verificare che il contenuto della pagina è posizionato su un'area dello schermo che può essere sicuro per tutti i dispositivi che utilizzano iOS 11 e versioni successive. In particolare, può risultare utile per verificare che il contenuto non è troncato angoli arrotondati dispositivo, l'indicatore principale o in un iPhone X alloggiamento sensore. Viene utilizzata in XAML impostando i `Page.UseSafeArea` associata a un `boolean` valore:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

Il `Page.On<iOS>` metodo specifica che questo specifico della piattaforma verrà eseguiti solo su iOS. Il `Page.SetUseSafeArea` (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) dello spazio dei nomi, controlla se la Guida di layout area di sicurezza è attivata.

Il risultato è che il contenuto della pagina può essere posizionato in un'area dello schermo sicuro per tutti i dispositivi iPhone:

[![](ios-images/safe-area-layout.png "Guida di Layout Area di sicurezza")](ios-images/safe-area-layout-large.png#lightbox "Guida Layout Area di sicurezza")

> [!NOTE]
> Area di sicurezza definito da Apple utilizzato in xamarin. Forms per impostare il [ `Page.Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/) , proprietà e sostituiscono tutti i valori di questa proprietà che sono stati impostati.

Area di sicurezza può essere personalizzato tramite il recupero dei relativi [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) valore con il `Page.SafeAreaInsets` metodo il [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) dello spazio dei nomi. Può quindi essere modificato come richiesto e riassegnato al `Padding` proprietà nel costruttore della pagina o [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) override:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

<a name="translucent_navigation_bar" />

## <a name="making-the-navigation-bar-translucent"></a>Rendere trasparente la barra di spostamento

Questo specifico della piattaforma viene utilizzato per modificare la trasparenza della barra di spostamento e vengono utilizzato in XAML impostando i [ `NavigationPage.IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty/) associata a un `boolean` valore:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

Il `NavigationPage.On<iOS>` metodo specifica che questo specifico della piattaforma verrà eseguiti solo su iOS. Il [ `NavigationPage.EnableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) dello spazio dei nomi, viene utilizzata per rendere trasparente la barra di spostamento. Inoltre, il [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage/) classe il `Xamarin.Forms.PlatformConfiguration.iOSSpecific` spazio dei nomi contiene inoltre un [ `DisableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) metodo che consente di ripristinare lo stato predefinito, la barra di spostamento e un [ `SetIsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/System.Boolean/) metodo che può essere utilizzato per attivare o disattivare la trasparenza di barra di navigazione chiamando il [ `IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) metodo:

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

Il risultato è che può essere modificata la trasparenza della barra di spostamento:

![](ios-images/translucent-navigation-bar.png "Barra di spostamento semitrasparente specifico della piattaforma")

<a name="status_bar_color_mode" />

## <a name="adjusting-the-status-bar-text-color-mode"></a>Modifica lo stato della barra di testo di colore

Questo controlli specifici della piattaforma se il testo della barra di stato di colore in un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) viene regolato in modo da corrispondere la luminosità della barra di spostamento. Viene utilizzata in XAML impostando il [ `NavigationPage.StatusBarTextColorMode` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty/) un valore della proprietà associata di [ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) enumerazione:

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

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

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

Il `NavigationPage.On<iOS>` metodo specifica che questo specifico della piattaforma verrà eseguiti solo su iOS. Il [ `NavigationPage.SetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) dello spazio dei nomi, i controlli se il testo della barra di stato di colore nel [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) viene regolato in modo che corrisponda il luminosità della barra di spostamento, con la [ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) enumerazione fornendo due valori possibili:

- [`DoNotAdjust`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust/) : indica che lo stato della barra di colore del testo non deve essere adattato.
- [`MatchNavigationBarTextLuminosity`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity/) : indica che lo stato della barra di colore del testo deve corrispondere la luminosità della barra di spostamento.

Inoltre, il [ `GetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/) metodo può essere utilizzato per recuperare il valore corrente del [ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) enumerazione che viene applicato al [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/).

Il risultato è che lo stato della barra di colore del testo in un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) può essere modificata per corrispondere la luminosità della barra di spostamento. In questo esempio, il testo Cambia colore della barra di stato come l'utente passa il [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) e [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) pagine di un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

![](ios-images/status-bar-text-color-mode.png "Barra di stato testo colore modalità specifiche della piattaforma")

<a name="adjust_font_size" />

## <a name="adjusting-the-font-size-of-an-entry"></a>Regolare le dimensioni del carattere di una voce

Questo specifico della piattaforma viene utilizzata per ridimensionare le dimensioni del carattere di un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) per verificare che il testo immessi nel controllo. Viene utilizzata in XAML impostando i [ `Entry.AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty/) associata a un `boolean` valore:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

Il `Entry.On<iOS>` metodo specifica che questo specifico della piattaforma verrà eseguiti solo su iOS. Il [ `Entry.EnableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) dello spazio dei nomi, viene utilizzata per ridimensionare le dimensioni del carattere del testo valori immessi per verificare che si adatta il [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/). Inoltre, il [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry/) classe nel `Xamarin.Forms.PlatformConfiguration.iOSSpecific` spazio dei nomi include inoltre un [ `DisableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) metodo che disabilita la piattaforma specifica, e un [ `SetAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/System.Boolean/) metodo che può essere utilizzato per attivare o disattivare dimensione scalabilità chiamando il [ `AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) metodo:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

Il risultato è che le dimensioni del carattere di [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) viene ridimensionata per verificare che il testo immessi nel controllo:

![](ios-images/entry-font-size.png "Modificare una voce carattere dimensioni specifico per la piattaforma")

<a name="picker_update_mode" />

## <a name="controlling-picker-item-selection"></a>Controllo dell'elemento di selezione

Questo specifico della piattaforma controlla quando la selezione di elementi si verifica in un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), consentendo all'utente di specificare che la selezione di elementi si verifica durante l'esplorazione di elementi nel controllo o solo una volta il **eseguita** pulsante premuto. Viene utilizzata in XAML impostando il `Picker.UpdateMode` un valore della proprietà associata di `UpdateMode` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

Il `Picker.On<iOS>` metodo specifica che questo specifico della piattaforma verrà eseguiti solo su iOS. Il `Picker.SetUpdateMode` (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) dello spazio dei nomi, viene utilizzato per controllare quando si verifica la selezione di elementi, con la `UpdateMode` enumerazione fornendo due valori possibili:

- `Immediately` : la selezione di elementi si verifica quando l'utente visualizza gli elementi di [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/). Questo è il comportamento predefinito in xamarin. Forms.
- `WhenFinished` : la selezione di elementi si verifica solo quando l'utente ha premuto il **eseguita** pulsante il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/).

Inoltre, il `SetUpdateMode` metodo può essere utilizzato per attivare o disattivare i valori di enumerazione chiamando il `UpdateMode` metodo, che restituisce l'attuale `UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

Il risultato è che un oggetto specificato `UpdateMode` è collegato il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), che controlla quando si verifica la selezione di elementi:

[![](ios-images/picker-updatemode.png "Selezione UpdateMode specifico della piattaforma")](ios-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Plaform-Specific")

<a name="set_status_bar_visibility" />

## <a name="setting-the-status-bar-visibility-on-a-page"></a>Impostare la barra di stato visibilità in una pagina

Questo specifico della piattaforma viene utilizzato per impostare la visibilità della barra di stato per un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), e include la possibilità di controllare la barra di stato attiva o disattiva il `Page`. Viene utilizzata in XAML impostando il `Page.PrefersStatusBarHidden` proprietà associata a un valore del `StatusBarHiddenMode` , enumerazione e, facoltativamente, il `Page.PreferredStatusBarUpdateAnimation` proprietà associata a un valore del `UIStatusBarAnimation` enumerazione:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

Il `Page.On<iOS>` metodo specifica che questo specifico della piattaforma verrà eseguiti solo su iOS. Il `Page.SetPrefersStatusBarHidden` (metodo), nel `Xamarin.Forms.PlatformConfiguration.iOSSpecific` dello spazio dei nomi, viene utilizzato per impostare la visibilità della barra di stato per un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) specificando uno del `StatusBarHiddenMode` valori di enumerazione: `Default`, `True` , o `False`. Il `StatusBarHiddenMode.True` e `StatusBarHiddenMode.False` valori impostare la visibilità della barra di stato indipendentemente dall'orientamento del dispositivo e `StatusBarHiddenMode.Default` valore nasconde la barra di stato in un ambiente verticalmente compact.

Il risultato è che la visibilità della barra di stato su un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) può essere impostata:

![](ios-images/hide-status-bar.png "Barra di stato visibilità specifico della piattaforma")

> [!NOTE]
> In un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), specificato `StatusBarHiddenMode` valore di enumerazione verrà aggiornate anche la barra di stato in tutte le pagine figlio. In tutti gli altri [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-tipi derivati, specificati `StatusBarHiddenMode` valore di enumerazione aggiornerà solo la barra di stato nella pagina corrente.

Il `Page.SetPreferredStatusBarUpdateAnimation` metodo utilizzato per impostare come la barra di stato attiva o disattiva il [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) specificando uno del `UIStatusBarAnimation` valori di enumerazione: `None`, `Fade`, o `Slide`. Se il `Fade` o `Slide` viene specificato il valore di enumerazione, una seconda 0,25 animazione viene eseguito come la barra di stato attiva o disattiva il `Page`.

<a name="delay_content_touches" />

## <a name="delaying-content-touches-in-a-scrollview"></a>Ritardo ritocchi contenuti in un elemento ScrollView

Un timer implicito viene attivato quando un gesto tocco inizia in una [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) in iOS e `ScrollView` decide, in base all'azione utente all'interno dell'intervallo del timer, se opportuno gestire l'azione o passare al relativo contenuto. Per impostazione predefinita, il file iOS `ScrollView` ritocchi contenuto ritardi, ma ciò può causare problemi in alcune circostanze con il `ScrollView` contenuto non vincente il movimento in un momento opportuno. Pertanto, questo controlli specifici della piattaforma se un `ScrollView` gestisce un gesto tocco o passa al relativo contenuto. Viene utilizzata in XAML impostando i `ScrollView.ShouldDelayContentTouches` associata a un `boolean` valore:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

Il `ScrollView.On<iOS>` metodo specifica che questo specifico della piattaforma verrà eseguiti solo su iOS. Il `ScrollView.SetShouldDelayContentTouches` (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) dello spazio dei nomi, viene utilizzato per controllare se un [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) gestisce un gesto tocco o passa al relativo contenuto. Inoltre, il `SetShouldDelayContentTouches` metodo può essere utilizzato per attivare o disattivare ritardando contenuti tocchi chiamando il `ShouldDelayContentTouches` per restituire se contenuti tocchi vengono posticipati:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

Il risultato è che un [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) possibile disabilitare rimandare la ricezione di contenuto tocchi, in modo che in questo scenario il [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) riceve il movimento di anziché [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) pagina della finestra di [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

[![](ios-images/scrollview-delay-content-touches.png "Ritardo ScrollView contenuto tocca specifico della piattaforma")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Plaform-Specific")

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare la piattaforma-specifiche di iOS che vengono compilate in xamarin. Forms. Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)
