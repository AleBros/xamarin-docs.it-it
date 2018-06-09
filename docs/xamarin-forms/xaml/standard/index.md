---
title: Sintassi XAML per gli Standard (anteprima)
description: Questo articolo illustra come iniziare a usare esplorazione l'anteprima Standard XAML in xamarin. Forms.
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 61e0fa2587ce9a8794dbd32ff9de1f13da857342
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245794"
---
# <a name="xaml-standard-preview"></a>Sintassi XAML per gli Standard (anteprima)

![Anteprima](~/media/shared/preview.png)

Seguire questi passaggi per sperimentare XAML Standard in xamarin. Forms:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Scaricare il [anteprima qui il pacchetto NuGet](https://aka.ms/xf-xamlstandard-nuget).
2. Aggiungere il **Xamarin.Forms.Alias** pacchetto NuGet per i progetti di piattaforma e xamarin. Forms .NET Standard.
3. Inizializzare il pacchetto con `Alias.Init()`
4. Aggiungere un `xmlns:a` riferimento `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Utilizzare i tipi in XAML, vedere il [controlli fanno riferimento a](controls.md) per ulteriori informazioni.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Scaricare il [anteprima qui il pacchetto NuGet](https://aka.ms/xf-xamlstandard-nuget).
2. Aggiungere il **Xamarin.Forms.Alias** pacchetto NuGet per i progetti di piattaforma e xamarin. Forms .NET Standard.
3. Inizializzare il pacchetto con `Alias.Init()`
4. Aggiungere un `xmlns:a` riferimento `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Utilizzare i tipi in XAML, vedere il [controlli fanno riferimento a](controls.md) per ulteriori informazioni.

-----

Il codice XAML seguente illustra alcuni controlli XAML Standard viene utilizzati in un xamarin. Forms `ContentPage`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ContentPage 
    xmlns="http://xamarin.com/schemas/2014/forms" 
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" 
    xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"
    x:Class="XAMLStandardSample.ItemsPage" 
    Title="{Binding Title}" x:Name="BrowseItemsPage">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Add" Clicked="AddItem_Clicked" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <a:StackPanel>
            <ListView x:Name="ItemsListView" ItemsSource="{Binding Items}" VerticalOptions="FillAndExpand" HasUnevenRows="true" RefreshCommand="{Binding LoadItemsCommand}" IsPullToRefreshEnabled="true" IsRefreshing="{Binding IsBusy, Mode=OneWay}" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <ViewCell>
                            <StackLayout Padding="10">
                                <a:TextBlock Text="{Binding Text}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemTextStyle}" FontSize="16" />
                                <a:TextBlock Text="{Binding Description}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemDetailTextStyle}" FontSize="13" />
                            </StackLayout>
                        </ViewCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </a:StackPanel>
    </ContentPage.Content>
</ContentPage>
```

> [!NOTE]
> Richiedere xmlns `a:` prefisso sui controlli della sintassi XAML per gli Standard è una limitazione di anteprima corrente.


## <a name="related-links"></a>Collegamenti correlati

- [Anteprima NuGet](https://aka.ms/xf-xamlstandard-nuget)
- [Riferimento per i controlli](controls.md)
