---
title: Rispondere alle modifiche del tema di sistema nelle applicazioni Novell. Forms
description: Le applicazioni Novell. Forms possono rispondere alle modifiche del tema del sistema operativo usando il tipo OnAppTheme e l'estensione di markup DynamicResource.
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.prod: xamarin
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2020
ms.openlocfilehash: c524ac0809044e576a8d56561642f6c3bf2df4a4
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532842"
---
# <a name="respond-to-system-theme-changes-in-xamarinforms-applications"></a>Rispondere alle modifiche del tema di sistema nelle applicazioni Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

I dispositivi in genere includono temi chiaro e scuro, che fanno riferimento a un ampio set di preferenze di aspetto che possono essere impostate a livello di sistema operativo. Le applicazioni devono rispettare questi temi di sistema e rispondere immediatamente quando il tema del sistema cambia.

Il tema del sistema può variare per diversi motivi, a seconda della configurazione del dispositivo. Ciò include il tema del sistema modificato in modo esplicito dall'utente, che cambia a causa dell'ora del giorno e che cambia a causa di fattori ambientali come la luce bassa.

Le applicazioni Novell. Forms possono rispondere alle modifiche del tema di sistema definendo `AppThemeColor` le risorse con `OnAppTheme<T>` la classe, la `OnAppTheme` classe e l'estensione di markup. Queste risorse devono quindi essere utilizzate con l' `DynamicResource` estensione di markup.

> [!IMPORTANT]
> La risposta a una modifica del tema del sistema è attualmente sperimentale e può essere usata solo `AppTheme_Experimental` impostando il flag. Per ulteriori informazioni, vedere [flag sperimentali](~/xamarin-forms/internals/experimental-flags.md).

I requisiti seguenti devono essere soddisfatti per Novell. Forms per rispondere a una modifica del tema di sistema:

- Novell. Forms 4,6 o versione successiva.
- iOS 13 o versione successiva.
- Android 10 (API 29) o versione successiva.
- UWP Build 14393 o versione successiva.

Gli screenshot seguenti mostrano le pagine con tema, per i temi di sistema chiaro e scuro in iOS e Android:

[![Screenshot della pagina principale di un'app con tema, in iOS e Android](system-theme-changes-images/main-page-both-themes.png "Pagina principale dell'app con tema")](system-theme-changes-images/main-page-both-themes-large.png#lightbox "Pagina principale dell'app con tema")
[![screenshot della pagina dei dettagli di un'app con tema, in iOS e Android](system-theme-changes-images/detail-page-both-themes.png "Pagina dei dettagli dell'app con tema")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "Pagina dei dettagli dell'app con tema")

## <a name="define-and-consume-theme-resources"></a>Definire e utilizzare le risorse del tema

È possibile definire le risorse per i temi chiaro e scuro `AppThemeColor` con la classe `OnAppTheme<T>` , la classe e `OnAppTheme` l'estensione di markup. Con ogni approccio, queste risorse vengono applicate automaticamente in base al valore del tema del sistema corrente. Inoltre, gli oggetti che utilizzano queste risorse vengono aggiornati automaticamente se il tema del sistema cambia mentre un'app è in esecuzione.

### <a name="appthemecolor"></a>AppThemeColor

La `AppThemeColor` classe viene utilizzata per definire [`Color`](xref:Xamarin.Forms.Color) le risorse per i temi di sistema chiaro e scuro. `AppThemeColor`le risorse devono essere definite in [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)un:

```xaml
<Application ...>
    <Application.Resources>
        <AppThemeColor x:Key="PageBackgroundColor"
                       Light="White"
                       Dark="Black" />
        <AppThemeColor x:Key="NavigationBarColor"
                       Light="WhiteSmoke"
                       Dark="Teal" />
        <AppThemeColor x:Key="PrimaryColor"
                       Light="WhiteSmoke"
                       Dark="Teal" />
        <AppThemeColor x:Key="SecondaryColor"
                       Light="Black"
                       Dark="White" />
        <AppThemeColor x:Key="PrimaryTextColor"
                       Light="Black"
                       Dark="White" />
        <AppThemeColor x:Key="SecondaryTextColor"
                       Light="White"
                       Dark="White" />
        <AppThemeColor x:Key="TertiaryTextColor"
                       Light="Gray"
                       Dark="WhiteSmoke" />
        <AppThemeColor x:Key="TransparentColor"
                       Light="Transparent"
                       Dark="Transparent" />
    </Application.Resources>
</Application>
```

Ogni `AppThemeColor` risorsa deve avere un `x:Key` attributo, che fornisce una chiave descrittiva in [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Il valore delle proprietà `Light` e `Dark` deve essere [`Color`](xref:Xamarin.Forms.Color) Objects. Inoltre, una `Default` proprietà può essere impostata su un `Color` oggetto per l'utilizzo da parte dell'oggetto che utilizza per impostazione predefinita.

`AppThemeColor`le risorse possono essere utilizzate inline:

```xaml
<Label Text="This monkey reacts appropriately to ridiculous assertions and actions"
       TextColor="{DynamicResource PrimaryTextColor}" />
```

In alternativa, `AppThemeColor` le risorse possono essere utilizzate da oggetti impliciti o espliciti [`Style`](xref:Xamarin.Forms.Style) :

```xaml
<Style TargetType="NavigationPage">
    <Setter Property="BarBackgroundColor"
            Value="{DynamicResource NavigationBarColor}" />
    <Setter Property="BarTextColor"
            Value="{DynamicResource SecondaryColor}" />
</Style>
```

> [!IMPORTANT]
> `AppThemeColor`le risorse devono essere utilizzate con `DynamicResource` l'estensione di markup. In questo modo si garantisce che l'aspetto dell'oggetto utilizzato venga aggiornato quando il tema del sistema viene modificato.

### <a name="onappthemelttgt"></a>OnAppTheme&lt;T&gt;

La `OnAppTheme<T>` classe viene utilizzata per definire le risorse di qualsiasi tipo per i temi di sistema chiaro e scuro. `OnAppTheme<T>`le risorse devono essere definite in [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)un oggetto, `T` con l'argomento specificato come valore dell' `x:TypeArguments` attributo:

```xaml
<Application ...>
    <Application.Resources>
        <OnAppTheme x:Key="ImageLogo"
                    x:TypeArguments="FileImageSource"
                    Light="lightlogo.png"
                    Dark="darklogo.png" />
    </Application.Resources>
</Application>
```

Ogni `OnAppTheme<T>` risorsa deve avere un `x:Key` attributo, che fornisce una chiave descrittiva in [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Il valore delle proprietà `Light` e `Dark` deve essere costituito da oggetti del tipo definito come `x:TypeArguments` attributo. Inoltre, è possibile `Default` impostare una proprietà su un oggetto di tipo `T` che verrà utilizzato dall'oggetto consumer per impostazione predefinita.

`OnAppTheme<T>`le risorse possono essere utilizzate inline:

```xaml
<Image Source="{DynamicResource ImageLogo}"
       Aspect="AspectFit"
       HeightRequest="200" /
```

In alternativa, `OnAppTheme<T>` le risorse possono essere utilizzate da oggetti impliciti o espliciti [`Style`](xref:Xamarin.Forms.Style) :

```xaml
<Style x:Key="imageLogoStyle"
       TargetType="Image">
    <Setter Property="Source"
            Value="{DynamicResource ImageLogo}" />
    <Setter Property="Aspect"
            Value="AspectFit" />
</Style>
```

> [!IMPORTANT]
> `OnAppTheme<T>`le risorse devono essere utilizzate con `DynamicResource` l'estensione di markup. In questo modo si garantisce che l'aspetto dell'oggetto utilizzato venga aggiornato quando il tema del sistema viene modificato.

### <a name="onapptheme-markup-extension"></a>Estensione di markup OnAppTheme

L' `OnAppTheme` estensione di markup consente di specificare una risorsa da utilizzare, ad esempio un'immagine o un colore, in base al tema corrente del sistema. Fornisce la stessa funzionalità della `OnAppTheme<T>` classe, ma con una rappresentazione più concisa:

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{OnAppTheme Light=Green, Dark=Red}" />
        <Image Source="{OnAppTheme Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

In questo esempio, il colore del testo del primo [`Label`](xref:Xamarin.Forms.Label) viene impostato su verde quando il dispositivo usa il tema chiaro e viene impostato su rosso quando il dispositivo usa il tema scuro. Analogamente, [`Image`](xref:Xamarin.Forms.Image) Visualizza un file di immagine diverso basato sul tema corrente del sistema.

Per ulteriori informazioni sull'estensione `OnAppTheme` di markup, vedere [OnAppTheme Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onapptheme-markup-extension).

## <a name="detect-the-current-system-theme"></a>Rileva il tema del sistema corrente

Il tema del sistema corrente può essere rilevato ottenendo il valore della `Application.RequestedTheme` proprietà:

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

La `RequestedTheme` proprietà restituisce un `OSAppTheme` membro di enumerazione. L'enumerazione `OSAppTheme` definisce i membri seguenti:

- `Unspecified`, che indica che il dispositivo utilizza un tema non specificato.
- `Light`, che indica che il dispositivo sta utilizzando il tema chiaro.
- `Dark`, che indica che il dispositivo usa il tema scuro.

## <a name="react-to-theme-changes"></a>Reagire alle modifiche del tema

Il tema del sistema su un dispositivo può variare per diversi motivi, a seconda della configurazione del dispositivo. Le app Novell. Forms possono ricevere una notifica quando il tema del sistema `Application.RequestedThemeChanged` cambia gestendo l'evento:

```csharp
Application.Current.RequestedThemeChanged += (s, a) =>
{
    // Respond to the theme change
};
```

L' `AppThemeChangedEventArgs` oggetto, che accompagna l' `RequestedThemeChanged` evento, ha una singola proprietà denominata `RequestedTheme`, di tipo. `OSAppTheme` Questa proprietà può essere esaminata per rilevare il tema del sistema richiesto.

## <a name="related-links"></a>Link correlati

- [SystemThemes (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)
- [Estensione di markup OnAppTheme](~/xamarin-forms/xaml/markup-extensions/consuming.md#onapptheme-markup-extension)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici in Novell. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
