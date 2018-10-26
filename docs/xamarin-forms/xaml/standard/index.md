---
title: XAML Standard (anteprima)
description: Questo articolo illustra come iniziare a usare l'anteprima di Standard di XAML in xamarin. Forms di esplorazione.
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 4e65f31d76d9540bed6110198d7cafaab9fe78f5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114899"
---
# <a name="xaml-standard-preview"></a>XAML Standard (anteprima)

![Anteprima](~/media/shared/preview.png)

Seguire questi passaggi per sperimentare Standard XAML in xamarin. Forms:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Scaricare il [preview qui pacchetto NuGet](https://aka.ms/xf-xamlstandard-nuget).
2. Aggiungere il **Xamarin.Forms.Alias** pacchetto NuGet per i progetti xamarin. Forms .NET Standard e piattaforma.
3. Inizializzare il pacchetto con `Alias.Init()`
4. Aggiungere un `xmlns:a` riferimento `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Usare i tipi in XAML, vedere la [riferimento per i controlli](controls.md) per altre informazioni.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Scaricare il [preview qui pacchetto NuGet](https://aka.ms/xf-xamlstandard-nuget).
2. Aggiungere il **Xamarin.Forms.Alias** pacchetto NuGet per i progetti xamarin. Forms .NET Standard e piattaforma.
3. Inizializzare il pacchetto con `Alias.Init()`
4. Aggiungere un `xmlns:a` riferimento `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Usare i tipi in XAML, vedere la [riferimento per i controlli](controls.md) per altre informazioni.

-----

il XAML seguente illustra alcuni controlli XAML Standard viene utilizzati in un xamarin. Forms `ContentPage`:

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
> Richiedere xmlns `a:` prefisso per i controlli XAML Standard è una limitazione della fase di anteprima corrente.


## <a name="related-links"></a>Collegamenti correlati

- [Anteprima NuGet](https://aka.ms/xf-xamlstandard-nuget)
- [Riferimento per i controlli](controls.md)
