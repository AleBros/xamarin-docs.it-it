---
title: Rispondere alle modifiche del tema di sistema nelle Xamarin.Forms applicazioni
description: Xamarin.Formsle applicazioni possono rispondere alle modifiche del tema del sistema operativo usando il tipo OnAppTheme e l'estensione di markup DynamicResource.
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.prod: xamarin
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/17/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 28bcbed3a03a2abbec42a619062579419a3063a4
ms.sourcegitcommit: 8a18471b3d96f3f726b66f9bc50a829f1c122f29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988208"
---
# <a name="respond-to-system-theme-changes-in-xamarinforms-applications"></a>Rispondere alle modifiche del tema di sistema nelle Xamarin.Forms applicazioni

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

I dispositivi in genere includono temi chiaro e scuro, che fanno riferimento a un ampio set di preferenze di aspetto che possono essere impostate a livello di sistema operativo. Le applicazioni devono rispettare questi temi di sistema e rispondere immediatamente quando il tema del sistema cambia.

Il tema del sistema può variare per diversi motivi, a seconda della configurazione del dispositivo. Ciò include il tema del sistema modificato in modo esplicito dall'utente, che cambia a causa dell'ora del giorno e che cambia a causa di fattori ambientali come la luce bassa.

Xamarin.Formsle applicazioni possono rispondere alle modifiche del tema del sistema mediante l'utilizzo di risorse con l' `AppThemeBinding` estensione di markup e i `SetAppThemeColor` metodi di `SetOnAppTheme<T>` estensione e.

> [!IMPORTANT]
> La risposta a una modifica del tema del sistema è attualmente sperimentale e può essere usata solo impostando il `AppTheme_Experimental` flag. Per ulteriori informazioni, vedere [flag sperimentali](~/xamarin-forms/internals/experimental-flags.md).

Per Xamarin.Forms rispondere a una modifica del tema di sistema, è necessario soddisfare i requisiti seguenti:

- Xamarin.Forms4.6.0.967 o versione successiva.
- iOS 13 o versione successiva.
- Android 10 (API 29) o versione successiva.
- UWP Build 14393 o versione successiva.

Gli screenshot seguenti mostrano le pagine con tema, per i temi di sistema chiaro e scuro in iOS e Android:

[![Screenshot della pagina principale di un'app con tema, in iOS e Android](system-theme-changes-images/main-page-both-themes.png "Pagina principale dell'app con tema")](system-theme-changes-images/main-page-both-themes-large.png#lightbox "Pagina principale dell'app con tema") 
 [ ![Screenshot della pagina dei dettagli di un'app con tema, in iOS e Android](system-theme-changes-images/detail-page-both-themes.png "Pagina dei dettagli dell'app con tema")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "Pagina dei dettagli dell'app con tema")

## <a name="define-and-consume-theme-resources"></a>Definire e utilizzare le risorse del tema

Le risorse per i temi chiaro e scuro possono essere utilizzate con l' `AppThemeBinding` estensione di markup e i `SetAppThemeColor` `SetOnAppTheme<T>` metodi di estensione e. Con questi approcci, le risorse vengono applicate automaticamente in base al valore del tema del sistema corrente. Inoltre, gli oggetti che utilizzano queste risorse vengono aggiornati automaticamente se il tema del sistema cambia mentre un'app è in esecuzione.

### <a name="appthemebinding-markup-extension"></a>Estensione di markup AppThemeBinding

L' `AppThemeBinding` estensione di markup consente di utilizzare una risorsa, ad esempio un'immagine o un colore, in base al tema del sistema corrente:

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{AppThemeBinding Light=Green, Dark=Red}" />
        <Image Source="{AppThemeBinding Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

In questo esempio, il colore del testo del primo [`Label`](xref:Xamarin.Forms.Label) viene impostato su verde quando il dispositivo usa il tema chiaro e viene impostato su rosso quando il dispositivo usa il tema scuro. Analogamente, [`Image`](xref:Xamarin.Forms.Image) Visualizza un file di immagine diverso basato sul tema corrente del sistema.

Inoltre, le risorse definite in un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) possono essere utilizzate con l' `StaticResource` estensione di markup:

```xaml
<ContentPage ...>
    <ContentPage.Resources>

        <!-- Light colors -->
        <Color x:Key="LightPrimaryColor">WhiteSmoke</Color>
        <Color x:Key="LightSecondaryColor">Black</Color>

        <!-- Dark colors -->
        <Color x:Key="DarkPrimaryColor">Teal</Color>
        <Color x:Key="DarkSecondaryColor">White</Color>

        <Style x:Key="ButtonStyle"
               TargetType="Button">
            <Setter Property="BackgroundColor"
                    Value="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}" />
            <Setter Property="TextColor"
                    Value="{AppThemeBinding Light={StaticResource LightSecondaryColor}, Dark={StaticResource DarkSecondaryColor}}" />
        </Style>

    </ContentPage.Resources>

    <Grid BackgroundColor="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}">
      <Button Text="MORE INFO"
              Style="{StaticResource ButtonStyle}" />
    </Grid>    
</ContentPage>    
```

In questo esempio, il colore di sfondo di [`Grid`](xref:Xamarin.Forms.Grid) e lo [`Button`](xref:Xamarin.Forms.Button) stile cambiano a seconda che il dispositivo usi il tema chiaro o il tema scuro.

Per ulteriori informazioni sull' `AppThemeBinding` estensione di markup, vedere [AppThemeBinding Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension).

### <a name="extension-methods"></a>Metodi di estensione

Xamarin.Formsinclude `SetAppThemeColor` i `SetOnAppTheme<T>` metodi di estensione e che consentono agli [`VisualElement`](xref:Xamarin.Forms.VisualElement) oggetti di rispondere alle modifiche del tema del sistema.

Il `SetAppThemeColor` metodo consente [`Color`](xref:Xamarin.Forms.Color) di specificare oggetti che verranno impostati su una proprietà di destinazione in base al tema del sistema corrente:

```csharp
Label label = new Label();
label.SetAppThemeColor(Label.TextColorProperty, Color.Green, Color.Red);
```

In questo esempio, il colore del testo di [`Label`](xref:Xamarin.Forms.Label) è impostato su verde quando il dispositivo usa il tema chiaro e viene impostato su rosso quando il dispositivo usa il tema scuro.

Il `SetOnAppTheme<T>` metodo consente di specificare oggetti di tipo `T` che verranno impostati su una proprietà di destinazione in base al tema del sistema corrente:

```csharp
Image image = new Image();
image.SetOnAppTheme<FileImageSource>(Image.SourceProperty, "lightlogo.png", "darklogo.png");
```

In questo esempio viene [`Image`](xref:Xamarin.Forms.Image) visualizzato `lightlogo.png` quando il dispositivo usa il tema chiaro e `darklogo.png` quando il dispositivo usa il tema scuro.

## <a name="detect-the-current-system-theme"></a>Rileva il tema del sistema corrente

Il tema del sistema corrente può essere rilevato ottenendo il valore della `Application.RequestedTheme` proprietà:

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

La `RequestedTheme` proprietà restituisce un `OSAppTheme` membro di enumerazione. L'enumerazione `OSAppTheme` definisce i membri seguenti:

- `Unspecified`, che indica che il dispositivo utilizza un tema non specificato.
- `Light`, che indica che il dispositivo sta utilizzando il tema chiaro.
- `Dark`, che indica che il dispositivo usa il tema scuro.

## <a name="set-the-current-user-theme"></a>Imposta il tema dell'utente corrente

Il tema usato dall'applicazione può essere impostato con la `Application.UserAppTheme` proprietà, che è di tipo `OSAppTheme` , indipendentemente dal tema del sistema attualmente operativo:

```csharp
Application.Current.UserAppTheme = OSAppTheme.Dark;
```

In questo esempio, l'applicazione è impostata per utilizzare il tema definito per la modalità scura di sistema, indipendentemente dal tema del sistema attualmente operativo.

> [!NOTE]
> Impostare la `UserAppTheme` proprietà su `OSAppTheme.Unspecified` per impostazione predefinita sul tema del sistema operativo.

## <a name="react-to-theme-changes"></a>Reagire alle modifiche del tema

Il tema del sistema su un dispositivo può variare per diversi motivi, a seconda della configurazione del dispositivo. Xamarin.Formsle app possono ricevere una notifica quando il tema del sistema cambia gestendo l' `Application.RequestedThemeChanged` evento:

```csharp
Application.Current.RequestedThemeChanged += (s, a) =>
{
    // Respond to the theme change
};
```

L' `AppThemeChangedEventArgs` oggetto, che accompagna l' `RequestedThemeChanged` evento, ha una singola proprietà denominata `RequestedTheme` , di tipo `OSAppTheme` . Questa proprietà può essere esaminata per rilevare il tema del sistema richiesto.

## <a name="related-links"></a>Collegamenti correlati

- [SystemThemes (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)
- [Estensione di markup AppThemeBinding](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin.FormsApplicazione di stili alle app usando gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
