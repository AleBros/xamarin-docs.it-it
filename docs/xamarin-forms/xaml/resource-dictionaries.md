---
title: Dizionari delle risorse
description: Risorse XAML sono definizioni di oggetti che possono essere utilizzati più volte. Un oggetto ResourceDictionary consente risorse siano definiti in un'unica posizione e riutilizzate in un'applicazione di xamarin. Forms. In questo articolo viene illustrato come creare e utilizzare un oggetto ResourceDictionary e come unire i dizionari delle risorse.
ms.topic: article
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/17/2017
ms.openlocfilehash: aa3ae9fed67b6cd7521e5c59edcb54f05cc6b7c5
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="resource-dictionaries"></a>Dizionari delle risorse

_Risorse XAML sono definizioni di oggetti che possono essere utilizzati più volte. Un oggetto ResourceDictionary consente risorse siano definiti in un'unica posizione e riutilizzate in un'applicazione di xamarin. Forms. In questo articolo viene illustrato come creare e utilizzare un oggetto ResourceDictionary e come unire i dizionari delle risorse._

## <a name="overview"></a>Panoramica

Oggetto [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) è un repository per le risorse utilizzate da un'applicazione di xamarin. Forms. Le risorse che vengono archiviate in un `ResourceDictionary` includono [stili](~/xamarin-forms/user-interface/styles/index.md), [modelli di controllo](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [modelli di data](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), colori e i convertitori di tipi.

In XAML, le risorse sono definite in un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) quindi recuperati e applicati agli elementi utilizzando il `StaticResource` estensione di markup. In c#, le risorse sono definite in un `ResourceDictionary` quindi recuperati e applicati agli elementi con un indicizzatore basato su stringa. Tuttavia, è molto vantaggiosa all'utilizzo di un `ResourceDictionary` in c#, come risorse può facilmente essere assegnati direttamente alle proprietà degli elementi visivi senza dover prima recuperare da un `ResourceDictionary`.

## <a name="creating-and-consuming-a-resourcedictionary"></a>Creazione e utilizzo di un oggetto ResourceDictionary

Le risorse possono essere definite in un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) associato al [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) raccolta di una pagina o un controllo o per il [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/) raccolta dell'applicazione. Scelta di come definire un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) impatti dove possono essere usato:

- Le risorse in un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definito il controllo livello può essere applicato solo per il controllo e nei relativi elementi figlio.
- Le risorse in un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definiti nella pagina di livello può essere applicato solo alla pagina e ai relativi figli.
- Le risorse in un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definito l'applicazione di livello può essere applicato in tutta l'applicazione.

L'esempio di codice XAML seguente mostra le risorse definite in un livello di applicazione [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/):

```xaml
<Application ...>
    <Application.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Yellow</Color>
            <Color x:Key="HeadingTextColor">Black</Color>
            <Color x:Key="NormalTextColor">Blue</Color>
            <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
                <Setter Property="FontAttributes" Value="Bold" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Questo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definisce tre [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) risorse e un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) risorse. Per ulteriori informazioni sulla creazione di un file XAML `App` classe, vedere [classe App](~/xamarin-forms/app-fundamentals/application-class.md).

Ogni risorsa ha una chiave specificata utilizzando il `x:Key` attributo, che offre una chiave descrittiva nel `ResourceDictionary`. La chiave viene usata per recuperare una risorsa dal [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) dal `StaticResource` estensione di markup, come illustrato nell'esempio di codice XAML seguente che mostra le risorse aggiuntive definite in un controllo livello `ResourceDictionary`:

```xaml
<StackLayout Margin="0,20,0,0">
  <StackLayout.Resources>
    <ResourceDictionary>
      <Style x:Key="LabelNormalStyle" TargetType="Label">
        <Setter Property="TextColor" Value="{StaticResource NormalTextColor}" />
      </Style>
      <Style x:Key="MediumBoldText" TargetType="Button">
        <Setter Property="FontSize" Value="Medium" />
        <Setter Property="FontAttributes" Value="Bold" />
      </Style>
    </ResourceDictionary>
  </StackLayout.Resources>
  <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
    <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
           Margin="10,20,10,0"
           Style="{StaticResource LabelNormalStyle}" />
    <Button Text="Navigate"
            Clicked="OnNavigateButtonClicked"
            TextColor="{StaticResource NormalTextColor}"
            Margin="0,20,0,0"
            HorizontalOptions="Center"
            Style="{StaticResource MediumBoldText}" />
</StackLayout>
```

Il primo [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanza recupera e utilizza il `LabelPageHeadingStyle` risorse definite nel livello di applicazione [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), con il secondo `Label` istanza il recupero e l'utilizzo di `LabelNormalStyle` risorse definite nel livello di controllo `ResourceDictionary`. Analogamente, il [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) istanza recupera e utilizza il `NormalTextColor` risorse definite nel livello di applicazione `ResourceDictionary`e `MediumBoldText` risorse definite nel livello di controllo `ResourceDictionary`. Ciò comporta l'aspetto illustrato nelle schermate seguenti:

[![](resource-dictionaries-images/screenshots-sml.png "Utilizzo risorse ResourceDictionary")](resource-dictionaries-images/screenshots.png#lightbox "ResourceDictionary risorse")

> [!NOTE]
> Risorse specifiche di una singola pagina non devono essere incluso in un'applicazione livello dizionario risorse, in quanto tali risorse verranno quindi analizzate all'avvio dell'applicazione anziché quando richiesto da una pagina. Per ulteriori informazioni, vedere [ridurre le dimensioni di dizionario risorse dell'applicazione](~/xamarin-forms/deploy-test/performance.md).

## <a name="overriding-resources"></a>Si esegue l'override di risorse

Quando [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) le risorse condividano `x:Key` valori di attributo, le risorse definite inferiore nella gerarchia della visualizzazione avrà la precedenza su quelli definiti più backup. Ad esempio, impostando il `PageBackgroundColor` risorsa `Blue` l'applicazione di livello verrà sottoposto a override da un livello di pagina `PageBackgroundColor` set di risorse a `Yellow`. Analogamente, un livello di pagina `PageBackgroundColor` risorsa verrà sostituita da un livello di controllo `PageBackgroundColor` risorse. L'esempio di codice XAML seguente viene dimostrata la precedenza:

```xaml
<ContentPage ... BackgroundColor="{StaticResource PageBackgroundColor}">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Blue</Color>
            <Color x:Key="NormalTextColor">Yellow</Color>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="0,20,0,0">
        ...
        <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
               Margin="10,20,10,0"
               Style="{StaticResource LabelNormalStyle}" />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked"
                TextColor="{StaticResource NormalTextColor}"
                Margin="0,20,0,0"
                HorizontalOptions="Center"
                Style="{StaticResource MediumBoldText}" />
    </StackLayout>
</ContentPage>
```

Originale `PageBackgroundColor` e `NormalTextColor` istanze, definite a livello di applicazione, vengono sostituite dal `PageBackgroundColor` e `NormalTextColor` istanze definite a livello di pagina. Di conseguenza, il colore di sfondo della pagina diventa blu, e il testo nella pagina diventa giallo, come illustrato nelle schermate seguenti:

[![](resource-dictionaries-images/overridding-screenshots-sml.png "Si esegue l'override delle risorse di ResourceDictionary")](resource-dictionaries-images/overridding-screenshots.png#lightbox "risorse ResourceDictionary viene sottoposto a override")

Si noti tuttavia che la barra di sfondo del [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) è ancora giallo, perché il [ `BarBackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/) proprietà è impostata sul valore del `PageBackgroundColor` risorsa definita nell'applicazione livello [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/).

## <a name="merged-resource-dictionaries"></a>Dizionari risorse uniti

Dizionari risorse sottoposti a merge combinano uno o più [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) istanze in un altro. Questa operazione viene eseguita impostando il `ResourceDictionary.MergedDictionaries` proprietà per uno o più dizionari delle risorse che verranno uniti all'applicazione, una pagina o un livello di controllo `ResourceDictionary`.

> [!IMPORTANT]
> Il [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) tipo dispone anche di un [ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/) proprietà che può essere utilizzato per unire un singolo `ResourceDictionary` in un altro, con il `ResourceDictionary` specificato come valore della `MergedWith`proprietà da unire nell'oggetto corrente `ResourceDictionary` istanza. Quando si uniscono tramite il `MergedWith` proprietà, le risorse nell'oggetto `ResourceDictionary` che condividono `x:Key` i valori con le risorse di attributo di `ResourceDictionary` da unire, verrà sostituito. Tuttavia, il `MergedWith` proprietà verrà rimossa in una futura versione di xamarin. Forms. Pertanto, si consiglia di utilizzare il `MergedDictionaries` proprietà di tipo merge `ResourceDictionary` istanze.

Nell'esempio di codice XAML seguente viene un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) denominato `MyResourceDictionary` che contiene una singola risorsa:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ResourceDictionaryDemo.MyResourceDictionary">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}" TextColor="{StaticResource NormalTextColor}" FontAttributes="Bold" />
                <Label Grid.Column="1" Text="{Binding Age}" TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2" Text="{Binding Location}" TextColor="{StaticResource NormalTextColor}" HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

`MyResourceDictionary` è possibile unire in qualsiasi applicazione, una pagina o un livello di controllo `ResourceDictionary`. L'esempio di codice XAML seguente viene illustrato che l'unione in un livello di pagina `ResourceDictionary` utilizzando il `MergedDictionaries` proprietà:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Quando unite [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) le risorse condividano identici `x:Key` valori di attributo, xamarin. Forms Usa la precedenza di risorse seguenti:

1. Le risorse locali per il dizionario risorse.
1. Le risorse contenute nel dizionario risorse che è stato unito tramite la [ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/) proprietà.
1. Le risorse contenute nei dizionari risorse che sono stati uniti tramite il `MergedDictionaries` insieme, nell'ordine in cui sono elencati il `MergedDictionaries` proprietà.

> [!NOTE]
> La ricerca di dizionari delle risorse può essere un'attività complesse se un'applicazione contiene più dizionari risorse di grandi dimensioni. Pertanto, verificare che ogni pagina in un'applicazione utilizza solo i dizionari delle risorse appropriate per la pagina, per evitare di ricerche non necessarie.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come creare e utilizzare un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)e come unire i dizionari delle risorse. Oggetto `ResourceDictionary` risorse siano definiti in un'unica posizione e riutilizzate in un'applicazione di xamarin. Forms.


## <a name="related-links"></a>Collegamenti correlati

- [Dizionari risorse (esempio)](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
