---
title: Sintassi XAML per gli Standard (anteprima)
description: Come iniziare a esplorare l'anteprima Standard di XAML in xamarin. Forms
ms.topic: article
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: e53df69fdfd5b5c1fc98b667d4b75d06c16c35dc
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="xaml-standard-preview"></a>Sintassi XAML per gli Standard (anteprima)

![Anteprima](~/media/shared/preview.png)

Seguire questi passaggi per sperimentare XAML Standard in xamarin. Forms:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Scaricare il [anteprima qui il pacchetto NuGet](https://aka.ms/xf-xamlstandard-nuget).
2. Aggiungere il **Xamarin.Forms.Alias** pacchetto NuGet per i progetti libreria di classi Portabile xamarin. Forms, Standard di .NET e della piattaforma.
3. Inizializzare il pacchetto con `Alias.Init()`
4. Aggiungere un `xmlns:a` riferimento `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Utilizzare i tipi in XAML, vedere il [controlli fanno riferimento a](controls.md) per ulteriori informazioni.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Scaricare il [anteprima qui il pacchetto NuGet](https://aka.ms/xf-xamlstandard-nuget).
2. Aggiungere il **Xamarin.Forms.Alias** pacchetto NuGet per i progetti libreria di classi Portabile xamarin. Forms, Standard di .NET e della piattaforma.
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
