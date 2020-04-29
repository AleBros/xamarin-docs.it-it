---
title: Tema di un'applicazione Novell. Forms
description: Il tema può essere implementato nelle applicazioni Novell. Forms creando un oggetto ResourceDictionary per ogni tema e quindi caricando le risorse con l'estensione di markup DynamicResource.
ms.prod: xamarin
ms.assetId: B7B17F66-4E37-4B50-9A57-351B62BE4FED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2019
ms.openlocfilehash: 43626c1f1581966a5b1ef65f97c177b83f19d0c2
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517607"
---
# <a name="theme-a-xamarinforms-application"></a>Tema di un'applicazione Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)

Le applicazioni Novell. Forms possono rispondere dinamicamente alle modifiche di stile in fase `DynamicResource` di esecuzione usando l'estensione di markup. Questa estensione di markup è simile all' `StaticResource` estensione di markup, in quanto entrambi usano una chiave del dizionario per recuperare un valore [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)da un oggetto. Tuttavia, mentre l' `StaticResource` estensione di markup esegue una singola ricerca nel dizionario `DynamicResource` , l'estensione di markup mantiene un collegamento alla chiave del dizionario. Se pertanto il valore associato alla chiave viene sostituito, la modifica viene applicata a [`VisualElement`](xref:Xamarin.Forms.VisualElement). Ciò consente di implementare i temi di runtime nelle applicazioni Novell. Forms.

Il processo di implementazione dei temi di runtime in un'applicazione Novell. Forms è il seguente:

1. Definire le risorse per ogni tema in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)oggetto.
1. Utilizzare le risorse del tema nell'applicazione, utilizzando `DynamicResource` l'estensione di markup.
1. Impostare un tema predefinito nel file **app. XAML** dell'applicazione.
1. Aggiungere codice per caricare un tema in fase di esecuzione.

> [!NOTE]
> Per modificare un tema in fase di esecuzione, è necessario utilizzare gli stili XAML e non è attualmente possibile utilizzare CSS.

Gli screenshot seguenti mostrano le pagine con tema, con l'applicazione iOS usando un tema chiaro e l'applicazione Android usando un tema scuro:

[![Screenshot della pagina principale di un'app con tema, in iOS e Android](theming-images/main-page-both-themes.png "Pagina principale dell'app con tema")](theming-images/main-page-both-themes-large.png#lightbox "Pagina principale dell'app con tema")
[![screenshot della pagina dei dettagli di un'app con tema, in iOS e Android](theming-images/detail-page-both-themes.png "Pagina dei dettagli dell'app con tema")](theming-images/detail-page-both-themes-large.png#lightbox "Pagina dei dettagli dell'app con tema")

## <a name="define-themes"></a>Definire i temi

Un tema è definito come una raccolta di oggetti risorsa archiviati in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)oggetto.

Nell'esempio seguente viene illustrata l'applicazione `LightTheme` di esempio:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.LightTheme">
    <Color x:Key="PageBackgroundColor">White</Color>
    <Color x:Key="NavigationBarColor">WhiteSmoke</Color>
    <Color x:Key="PrimaryColor">WhiteSmoke</Color>
    <Color x:Key="SecondaryColor">Black</Color>
    <Color x:Key="PrimaryTextColor">Black</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">Gray</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

Nell'esempio seguente viene illustrata l'applicazione `DarkTheme` di esempio:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.DarkTheme">
    <Color x:Key="PageBackgroundColor">Black</Color>
    <Color x:Key="NavigationBarColor">Teal</Color>
    <Color x:Key="PrimaryColor">Teal</Color>
    <Color x:Key="SecondaryColor">White</Color>
    <Color x:Key="PrimaryTextColor">White</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">WhiteSmoke</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

Ogni [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contiene [`Color`](xref:Xamarin.Forms.Color) risorse che definiscono i rispettivi temi, ognuno `ResourceDictionary` dei quali usa valori di chiave identici. Per altre informazioni sui dizionari risorse, vedere [dizionari risorse](~/xamarin-forms/xaml/resource-dictionaries.md).

> [!IMPORTANT]
> Per ogni `ResourceDictionary`oggetto, che chiama il `InitializeComponent` metodo, è necessario un file code-behind. Questa operazione è necessaria in modo che un oggetto CLR che rappresenta il tema scelto possa essere creato in fase di esecuzione.

## <a name="set-a-default-theme"></a>Imposta un tema predefinito

Un'applicazione richiede un tema predefinito, in modo che i controlli dispongano di valori per le risorse che utilizzano. È [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) possibile impostare un tema predefinito unendo il tema nell'oggetto a livello `ResourceDictionary` di applicazione definito in **app. XAML**:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>
        <ResourceDictionary Source="Themes/LightTheme.xaml" />
    </Application.Resources>
</Application>
```

Per altre informazioni sull'Unione dei dizionari risorse, vedere [dizionari risorse Uniti](~/xamarin-forms/xaml/resource-dictionaries.md#merged-resource-dictionaries).

## <a name="consume-theme-resources"></a>Utilizzare le risorse del tema

Quando un'applicazione vuole usare una risorsa archiviata in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) oggetto che rappresenta un tema, questa operazione deve essere eseguita con l' `DynamicResource` estensione di markup. In questo modo si garantisce che se si seleziona un tema diverso in fase di esecuzione, verranno applicati i valori del nuovo tema.

Nell'esempio seguente vengono illustrati tre stili dell'applicazione di esempio che possono essere [`Label`](xref:Xamarin.Forms.Label) applicati agli oggetti:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>

        <Style x:Key="LargeLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource SecondaryTextColor}" />
            <Setter Property="FontSize"
                    Value="30" />
        </Style>

        <Style x:Key="MediumLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource PrimaryTextColor}" />
            <Setter Property="FontSize"
                    Value="25" />
        </Style>

        <Style x:Key="SmallLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource TertiaryTextColor}" />
            <Setter Property="FontSize"
                    Value="15" />
        </Style>

    </Application.Resources>
</Application>
```

Questi stili sono definiti nel dizionario risorse a livello di applicazione, in modo che possano essere utilizzati da più pagine. Ogni stile utilizza le risorse del tema con `DynamicResource` l'estensione di markup.

Questi stili vengono quindi utilizzati dalle pagine:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ThemingDemo"
             x:Class="ThemingDemo.UserSummaryPage"
             Title="User Summary"
             BackgroundColor="{DynamicResource PageBackgroundColor}">
    ...
    <ScrollView>
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="200" />
                <RowDefinition Height="120" />
                <RowDefinition Height="70" />
            </Grid.RowDefinitions>
            <Grid BackgroundColor="{DynamicResource PrimaryColor}">
                <Label Text="Face-Palm Monkey"
                       VerticalOptions="Center"
                       Margin="15"
                       Style="{StaticResource MediumLabelStyle}" />
                ...
            </Grid>
            <StackLayout Grid.Row="1"
                         Margin="10">
                <Label Text="This monkey reacts appropriately to ridiculous assertions and actions."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Cynical but not unfriendly."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Seven varieties of grimaces."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Doesn't laugh at your jokes."
                       Style="{StaticResource SmallLabelStyle}" />
            </StackLayout>
            ...
        </Grid>
    </ScrollView>
</ContentPage>
```

Quando una risorsa del tema viene utilizzata direttamente, deve essere utilizzata con l' `DynamicResource` estensione di markup. Tuttavia, quando viene utilizzato uno stile che `DynamicResource` usa l'estensione di markup, deve essere utilizzato con l' `StaticResource` estensione di markup.

Per altre informazioni sullo stile, vedere [applicazione di stili a Novell. Forms usando gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md). Per altre informazioni sull'estensione `DynamicResource` di markup, vedere [stili dinamici in Novell. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md).

## <a name="load-a-theme-at-runtime"></a>Caricare un tema in fase di esecuzione

Quando si seleziona un tema in fase di esecuzione, l'applicazione deve:

1. Rimuovere il tema corrente dall'applicazione. Questa operazione viene eseguita deselezionando la [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) proprietà a livello [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)di applicazione.
2. Carica il tema selezionato. Questa operazione viene eseguita aggiungendo un'istanza del tema selezionato alla `MergedDictionaries` proprietà a livello `ResourceDictionary`di applicazione.

Tutti [`VisualElement`](xref:Xamarin.Forms.VisualElement) gli oggetti che impostano `DynamicResource` le proprietà con l'estensione di markup applicheranno quindi i nuovi valori del tema. Questo problema si verifica `DynamicResource` perché l'estensione di markup mantiene un collegamento alle chiavi del dizionario. Pertanto, quando i valori associati alle chiavi vengono sostituiti, le modifiche vengono applicate agli `VisualElement` oggetti.

Nell'applicazione di esempio, viene selezionato un tema tramite una pagina modale che contiene un [`Picker`](xref:Xamarin.Forms.Picker)oggetto. Il codice seguente illustra il `OnPickerSelectionChanged` metodo, che viene eseguito quando il tema selezionato cambia:

```csharp
void OnPickerSelectionChanged(object sender, EventArgs e)
{
    Picker picker = sender as Picker;
    Theme theme = (Theme)picker.SelectedItem;

    ICollection<ResourceDictionary> mergedDictionaries = Application.Current.Resources.MergedDictionaries;
    if (mergedDictionaries != null)
    {
        mergedDictionaries.Clear();

        switch (theme)
        {
            case Theme.Dark:
                mergedDictionaries.Add(new DarkTheme());
                break;
            case Theme.Light:
            default:
                mergedDictionaries.Add(new LightTheme());
                break;
        }
    }
}
```

## <a name="related-links"></a>Link correlati

- [Temi (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Rispondi alle modifiche del tema di sistema](system-theme-changes.md)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici in Novell. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
