---
title: Novell. Forms CarouselView
description: Per impostazione predefinita, un CarouselView visualizzerà gli elementi in un elenco orizzontale. Tuttavia, ha anche accesso agli stessi layout di CollectionView, incluso un orientamento verticale.
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 5bbaeead524089ea604cfd9a9fd7f3a85d04e724
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908282"
---
# <a name="xamarinforms-carouselview-layouts"></a>Layout CarouselView di Novell. Forms

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)

## <a name="introduction"></a>Introduzione

Gran parte delle funzionalità di layout disponibili per CarouselView ha origine da CollectionView. È possibile fare riferimento alla documentazione di [layout](../collectionview/layout.md) di CollectionView per vedere l'uso di diversi layout.

## <a name="differences-from-collectionview"></a>Differenze rispetto a CollectionView

Per impostazione predefinita, gli elementi in un CarouselView verranno orientati orizzontalmente, così come la tipica funzione di un carosello nelle app.

CarouselView fornisce anche alcune proprietà aggiuntive:

> [!IMPORTANT]
> Le proprietà aggiuntive per CarouselView sono ancora in fase di sviluppo e questo elenco non è ancora completo.

| API | Funzione |
|---|---|---|
| NumberOfSideItems | Imposta il numero di elementi visualizzati a ogni lato dell'elemento corrente. Il valore predefinito è 0.
| PeekAreaInsets | Fornisce un modo per indicare in modo visibile all'utente che il CarouselView dispone di elementi aggiuntivi a cui eseguire lo scorrimento regolando il livello di visibilità adiacente all'elemento corrente.

## <a name="setting-the-number-of-fully-visible-items"></a>Impostazione del numero di elementi completamente visibili

Per impostazione predefinita, il CarouselView Visualizza un elemento interamente nello schermo. Gli utenti possono impostare `NumberOfSideItems` la proprietà in modo da consentire la visualizzazione di più elementi adiacenti all'elemento corrente. Si noti che qualsiasi valore impostato `PeekAreaInsets` su sarà ancora applicabile.

```xaml
<StackLayout Margin="20">
    <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" NumberOfSideItems="1">
        <CarouselView.ItemTemplate>
            <DataTemplate>
                <Frame BorderColor="Black">
                    <Grid>
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <Label Grid.Column="1"
                            Text="{Binding Name}"
                            FontAttributes="Bold"
                            FontSize="14"/>
                        <Label Grid.Row="1"
                            Grid.Column="1"
                            Text="{Binding Location}"
                            FontAttributes="Italic"
                            FontSize="12"
                            VerticalOptions="End" />
                    </Grid>
                </Frame>
            </DataTemplate>
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

[ ![Screenshot di un CarouselView con elementi lato, su](carouselview-images/side-items.png "elementi lato Android CarouselView") ] (carouselview-images/side-items-large.png#lightbox "Elementi lato CarouselView")

## <a name="making-adjacent-items-partially-visible"></a>Creazione di elementi adiacenti parzialmente visibili

Quando si usa un CarouselView nell'app, può essere utile indicare all'utente che il CarouselView funziona in questo modo impostando la `PeekAreaInsets` proprietà su un valore diverso da zero (impostazione predefinita), che li espone parzialmente sullo schermo.

```xaml
<StackLayout Margin="20">
  <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" PeekAreaInsets="100">
      <CarouselView.ItemTemplate>
          <DataTemplate>
              <Frame BorderColor="Black">
                  <Grid>
                      <Grid.RowDefinitions>
                          <RowDefinition Height="Auto" />
                          <RowDefinition Height="Auto" />
                      </Grid.RowDefinitions>
                      <Grid.ColumnDefinitions>
                          <ColumnDefinition Width="Auto" />
                          <ColumnDefinition Width="Auto" />
                      </Grid.ColumnDefinitions>
                      <Label Grid.Column="1"
                          Text="{Binding Name}"
                          FontAttributes="Bold"
                          FontSize="14"/>
                      <Label Grid.Row="1"
                          Grid.Column="1"
                          Text="{Binding Location}"
                          FontAttributes="Italic"
                          FontSize="12"
                          VerticalOptions="End" />
                  </Grid>
              </Frame>
          </DataTemplate>
      </CarouselView.ItemTemplate>
  </CarouselView>
</StackLayout>
```

[ ![Screenshot di un CarouselView con elementi lato, su](carouselview-images/peek-area-insets.png "elementi lato Android CarouselView") ] (carouselview-images/peek-area-insets-large.png#lightbox "Elementi lato CarouselView")

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)
- [Documentazione sul layout di CollectionView](../collectionview/layout.md)
