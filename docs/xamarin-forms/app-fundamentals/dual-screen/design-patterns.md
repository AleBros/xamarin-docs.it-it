---
title: Modelli di progettazione per doppio schermo di Xamarin.Forms
description: Questa guida illustra il supporto di Xamarin.Forms di vari modelli di progettazione diversi ottimizzati per i dispositivi con doppio schermo.
ms.prod: xamarin
ms.assetid: 3176d792-6dba-4e00-b463-497c58678ee9
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: c739b32cb45c27bbbc166f898dee964a7cbbbce6
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145525"
---
# <a name="xamarinforms-dual-screen-design-patterns"></a>Modelli di progettazione per doppio schermo di Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)

Questa guida presenta i modelli di progettazione consigliati per i dispositivi con doppio schermo con codice ed esempi utili per la creazione di interfacce in grado di offrire esperienze utente accattivanti e utili.

## <a name="extended-canvas-pattern"></a>Modello con canvas esteso

Il modello con canvas esteso considera entrambi gli schermi come un grande canvas per la visualizzazione di una mappa, un'immagine, un foglio di calcolo o altro contenuto di questo tipo che trae vantaggio dalla distribuzione del contenuto per usare lo spazio al massimo.

![](design-patterns-images/extended-canvas-sample.png "Extended canvas sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:d="http://xamarin.com/schemas/2014/forms/design"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    x:Class="Xamarin.Duo.Forms.Samples.ExtendCanvas">
    <ContentPage.Content>
        <Grid>
            <WebView x:Name="webView" HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
            <SearchBar x:Name="searchBar" Placeholder="Find a place..." BackgroundColor="DarkGray" Opacity="0.8" HorizontalOptions="FillAndExpand" VerticalOptions="Start" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

In questo esempio, il controllo `Grid` e il contenuto interno si espanderanno per utilizzare tutto lo spazio disponibile sullo schermo, sia che vengano visualizzati su un singolo schermo che estesi a entrambi.

## <a name="master-detail-pattern"></a>Modello master-dettagli

Il modello master-dettagli è quello in cui la visualizzazione master, in genere un elenco a sinistra, fornisce contenuto da cui un utente seleziona un elemento per visualizzarne i dettagli a destra.

![](design-patterns-images/master-detail-sample.png "Master detail sample")

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Class="Xamarin.Duo.Forms.Samples.MasterDetail">
    <dualScreen:TwoPaneView MinWideModeWidth="4000" MinTallModeHeight="4000">
        <dualScreen:TwoPaneView.Pane1>
            <local:Master x:Name="masterPage"></local:Master>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:Details x:Name="detailsPage"></local:Details>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

In questo esempio, è possibile usare `TwoPaneView` per impostare un elenco in un riquadro e una visualizzazione dettagli nell'altro.

## <a name="two-page-pattern"></a>Modello a due pagine

Due pagine sono ideali per contenuti che si prestano a un layout con 2 pagine per foglio, ad esempio un lettore di documenti o di note o una lavagna grafica.

![](design-patterns-images/two-page-sample.png "Two page sample")

```xaml
<Grid x:Name="layout">
    <CollectionView x:Name="cv" BackgroundColor="LightGray">
        <CollectionView.ItemsLayout>
            <GridItemsLayout
                SnapPointsAlignment="Start"
                SnapPointsType="MandatorySingle"
                Orientation="Horizontal"
                HorizontalItemSpacing="{Binding Source={x:Reference mainPage}, Path=HingeWidth}" />
        </CollectionView.ItemsLayout>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Frame BackgroundColor="LightGray" Padding="0" Margin="0"
                        WidthRequest="{Binding Source={x:Reference mainPage}, Path=ContentWidth}"
                        HeightRequest="{Binding Source={x:Reference mainPage}, Path=ContentHeight}">
                    <Frame Margin="20" BackgroundColor="White">
                        <Label FontSize="Large" Text="{Binding .}" VerticalTextAlignment="Center" HorizontalTextAlignment="Center" HorizontalOptions="Center" VerticalOptions="Center"></Label>
                    </Frame>
                </Frame>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</Grid>
```

Il controllo [`CollectionView`](xref:Xamarin.Forms.CollectionView), con un layout griglia che si divide in corrispondenza della cerniera, costituisce un modo ideale per fornire questa esperienza su doppio schermo.

## <a name="dual-view-pattern"></a>Modello di visualizzazione doppia

La visualizzazione doppia può sembrare analoga alla visualizzazione "a due pagine", ma la distinzione riguarda lo scenario d'uso per contenuti e utenti. In questo modello si confronta contenuto affiancato, ad esempio per modificare un documento o una foto, per confrontare diversi menù di ristoranti o per risolvere conflitti di merge per file di codice.

![](design-patterns-images/dual-view-sample.png "Dual view sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Class="Xamarin.Duo.Forms.Samples.DualViewListPage">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <CollectionView SelectionMode="Single" x:Name="mapList">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Grid Padding="10,5,10,5">
                            <Frame Visual="Material" BorderColor="LightGray">
                                <StackLayout Padding="5">
                                    <Label FontSize="Title" Text="{Binding Title}"></Label>
                                </StackLayout>
                            </Frame>
                        </Grid>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:DualViewMap x:Name="mapPage"></local:DualViewMap>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="companion-pattern"></a>Modello per contenuto complementare

Il modello per contenuto complementare dimostra come è possibile usare il secondo schermo per fornire un secondo livello di contenuto correlato alla visualizzazione primaria, ad esempio nel caso di un'app di disegno, di un gioco o di una modifica multimediale.

![](design-patterns-images/companion-pane-sample.png "Companion pane sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples" xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualscreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Name="mainPage"
    x:Class="Xamarin.Duo.Forms.Samples.CompanionPane"
    BackgroundColor="LightGray"
    Visual="Material">
    <dualscreen:TwoPaneView x:Name="twoPaneView" MinWideModeWidth="4000" MinTallModeHeight="4000">
        <dualscreen:TwoPaneView.Pane1>
            <CarouselView x:Name="cv" BackgroundColor="LightGray" IsScrollAnimated="False" >
                <CarouselView.ItemTemplate>
                    <DataTemplate>
                        <Frame BackgroundColor="LightGray" Padding="0" Margin="0"
                           WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Width}"
                           HeightRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Height}">
                            <Frame Margin="20" BackgroundColor="White">
                                <Label FontSize="Large" Text="{Binding ., StringFormat='Slide Content {0}'}" VerticalTextAlignment="Center" HorizontalTextAlignment="Center" HorizontalOptions="Center" VerticalOptions="Center"></Label>
                            </Frame>
                        </Frame>
                    </DataTemplate>
                </CarouselView.ItemTemplate>
            </CarouselView>
        </dualscreen:TwoPaneView.Pane1>
        <dualscreen:TwoPaneView.Pane2>
            <CollectionView x:Name="indicators"
            SelectionMode="Single"
            Margin="20, 20, 20, 20"
            BackgroundColor="LightGray"
            WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}"
            ItemsSource="{Binding Source={x:Reference cv}, Path=ItemsSource}">
                <CollectionView.Resources>
                    <ResourceDictionary>
                        <Style TargetType="Frame">
                            <Setter Property="VisualStateManager.VisualStateGroups">
                                <VisualStateGroupList>
                                    <VisualStateGroup x:Name="CommonStates">
                                        <VisualState x:Name="Normal">
                                            <VisualState.Setters>
                                                <Setter Property="Padding" Value="0"></Setter>
                                            </VisualState.Setters>
                                        </VisualState>
                                        <VisualState x:Name="Selected">
                                            <VisualState.Setters>
                                                <Setter Property="BorderColor" Value="Green" />
                                                <Setter Property="Padding" Value="1"></Setter>
                                            </VisualState.Setters>
                                        </VisualState>
                                    </VisualStateGroup>
                                </VisualStateGroupList>
                            </Setter>
                        </Style>
                    </ResourceDictionary>
                </CollectionView.Resources>
                <CollectionView.ItemsLayout>
                    <LinearItemsLayout Orientation="Vertical" ItemSpacing="10"></LinearItemsLayout>
                </CollectionView.ItemsLayout>
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Frame WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}" CornerRadius="10" HeightRequest="60" BackgroundColor="White" Margin="0">
                            <StackLayout HorizontalOptions="Fill" VerticalOptions="Fill"  Orientation="Horizontal">
                                <Label FontSize="Micro" Padding="20,0,20,0" VerticalTextAlignment="Center" WidthRequest="140" Text="{Binding ., StringFormat='Slide Content {0}'}"></Label>
                                <Label FontSize="Small" Padding="20,0,20,0" VerticalTextAlignment="Center" HorizontalOptions="FillAndExpand" BackgroundColor="DarkGray"  Grid.Column="1" Text="{Binding ., StringFormat='Slide {0}'}"></Label>
                            </StackLayout>
                        </Frame>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualscreen:TwoPaneView.Pane2>
    </dualscreen:TwoPaneView>
</ContentPage>
```

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di doppio schermo (GitHub)](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)
- [Creare app per dispositivi con doppio schermo](index.md)
