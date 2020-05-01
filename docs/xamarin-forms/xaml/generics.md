---
title: Generics in Novell. Forms XAML
description: Novell. Forms XAML fornisce supporto per l'utilizzo di tipi CLR generici specificando i vincoli generici come argomenti di tipo.
ms.prod: xamarin
ms.assetid: 97B73048-4F90-41AD-AB48-8EB804C4998B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/28/2020
ms.openlocfilehash: 9cda08a3bab0e25db2315c9795721e25d47d2429
ms.sourcegitcommit: 154a3e7aec775327565bb54eda1a610976af1d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82624709"
---
# <a name="generics-in-xamarinforms-xaml"></a>Generics in Novell. Forms XAML

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-generics/)

Novell. Forms XAML fornisce supporto per l'utilizzo di tipi CLR generici specificando i vincoli generici come argomenti di tipo. Questo supporto viene fornito dalla `x:TypeArguments` direttiva, che passa gli argomenti di tipo vincolante di un oggetto generico al costruttore del tipo generico.

> [!IMPORTANT]
> La definizione di classi generiche nel codice XAML Novell. `x:TypeArguments` Forms, con la direttiva, non è supportata.

Gli argomenti di tipo vengono specificati come stringa e sono in genere preceduti, ad `sys:String` esempio `sys:Int32`e. Il prefisso è necessario perché i tipi tipici di vincoli CLR generici provengono da librerie di cui non è stato eseguito il mapping allo spazio dei nomi Novell. Forms predefinito. Tuttavia, i tipi incorporati XAML 2009, ad esempio `x:String` e `x:Int32`, possono essere specificati anche come argomenti di tipo, `x` dove è lo spazio dei nomi del linguaggio XAML per XAML 2009. Per ulteriori informazioni sui tipi predefiniti XAML 2009, vedere [primitive del linguaggio xaml 2009](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives).

È possibile specificare più argomenti di tipo utilizzando un delimitatore di virgola. Inoltre, se un vincolo generico usa tipi generici, gli argomenti del tipo di vincolo annidato devono essere racchiusi tra parentesi.

> [!NOTE]
> L' `x:Type` estensione di markup fornisce un riferimento a un tipo CLR per un tipo generico e ha una funzione simile `typeof` all'operatore in C#. Per altre informazioni, vedere [x:Type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

## <a name="single-primitive-type-argument"></a>Singolo argomento di tipo primitivo

È possibile specificare un singolo argomento di tipo primitivo come argomento di stringa con prefisso `x:TypeArguments` usando la direttiva:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="x:String">
                <x:String>Baboon</x:String>
                <x:String>Capuchin Monkey</x:String>
                <x:String>Blue Monkey</x:String>
                <x:String>Squirrel Monkey</x:String>
                <x:String>Golden Lion Tamarin</x:String>
                <x:String>Howler Monkey</x:String>
                <x:String>Japanese Macaque</x:String>
            </scg:List>
        </CollectionView.ItemsSource>
    </CollectionView>
</ContentPage>
```

In questo esempio, `System.Collections.Generic` viene definito come spazio `scg` dei nomi XAML. La `CollectionView.ItemsSource` proprietà è impostata su un `List<T>` oggetto di cui viene creata un' `string` istanza con un argomento di tipo, usando il `x:String` tipo incorporato XAML 2009. La `List<string>` raccolta viene inizializzata con `string` più elementi.

In alternativa, è possibile creare un'istanza `List<T>` della raccolta con il tipo CLR `String` in modo analogo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="sys:String">
                <sys:String>Baboon</sys:String>
                <sys:String>Capuchin Monkey</sys:String>
                <sys:String>Blue Monkey</sys:String>
                <sys:String>Squirrel Monkey</sys:String>
                <sys:String>Golden Lion Tamarin</sys:String>
                <sys:String>Howler Monkey</sys:String>
                <sys:String>Japanese Macaque</sys:String>
            </scg:List>
        </CollectionView.ItemsSource>
    </CollectionView>
</ContentPage>
```

## <a name="single-object-type-argument"></a>Argomento di tipo oggetto singolo

Un singolo argomento di tipo di oggetto può essere specificato come argomento di stringa con prefisso `x:TypeArguments` usando la direttiva:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:models="clr-namespace:GenericsDemo.Models"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="models:Monkey">
                <models:Monkey Name="Baboon"
                               Location="Africa and Asia"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg" />
                <models:Monkey Name="Capuchin Monkey"
                               Location="Central and South America"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg" />
                <models:Monkey Name="Blue Monkey"
                               Location="Central and East Africa"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg" />
            </scg:List>
        </CollectionView.ItemsSource>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="Auto" />
                    </Grid.ColumnDefinitions>
                    <Image Grid.RowSpan="2"
                           Source="{Binding ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Name}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Location}"
                           FontAttributes="Italic"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</ContentPage>
```

In questo esempio, `GenericsDemo.Models` viene definito come spazio `models` dei nomi XAML e `System.Collections.Generic` viene definito come spazio `scg` dei nomi XAML. La `CollectionView.ItemsSource` proprietà è impostata su un `List<T>` oggetto di cui viene creata un' `Monkey` istanza con un argomento di tipo. La `List<Monkey>` raccolta viene inizializzata con `Monkey` più elementi e un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che definisce l'aspetto di `Monkey` ogni oggetto viene impostato come `ItemTemplate` di [`CollectionView`](xref:Xamarin.Forms.CollectionView).

## <a name="multiple-type-arguments"></a>Più argomenti di tipo

È possibile specificare più argomenti di tipo come argomenti di stringa con prefisso, delimitati da una virgola `x:TypeArguments` , usando la direttiva. Quando un vincolo generico usa tipi generici, gli argomenti del tipo di vincolo annidato sono racchiusi tra parentesi:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:models="clr-namespace:GenericsDemo.Models"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="scg:KeyValuePair(x:String,models:Monkey)">
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Baboon</x:String>
                        <models:Monkey Location="Africa and Asia"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg" />
                    </x:Arguments>
                </scg:KeyValuePair>
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Capuchin Monkey</x:String>
                        <models:Monkey Location="Central and South America"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg" />   
                    </x:Arguments>
                </scg:KeyValuePair>
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Blue Monkey</x:String>
                        <models:Monkey Location="Central and East Africa"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg" />
                    </x:Arguments>
                </scg:KeyValuePair>
            </scg:List>
        </CollectionView.ItemsSource>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="Auto" />
                    </Grid.ColumnDefinitions>
                    <Image Grid.RowSpan="2"
                           Source="{Binding Value.ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Key}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Value.Location}"
                           FontAttributes="Italic"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</ContentPage    
```

In questo esempio, `GenericsDemo.Models` viene definito come spazio `models` dei nomi XAML e `System.Collections.Generic` viene definito come spazio `scg` dei nomi XAML. La `CollectionView.ItemsSource` proprietà è impostata su un `List<T>` oggetto di cui viene creata un' `KeyValuePair<TKey, TValue>` istanza con un vincolo, con gli argomenti `string` di `Monkey`tipo vincolo interno e. La `List<KeyValuePair<string,Monkey>>` raccolta viene inizializzata con `KeyValuePair` più elementi, usando il costruttore non `KeyValuePair` predefinito e un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che definisce l'aspetto di ogni `Monkey` oggetto viene impostato come `ItemTemplate` di. [`CollectionView`](xref:Xamarin.Forms.CollectionView) Per informazioni sul passaggio di argomenti a un costruttore non predefinito, vedere [passaggio di argomenti del costruttore](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments).

## <a name="related-links"></a>Link correlati

- [Generics in XAML (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-generics/)
- [Primitive del linguaggio XAML 2009](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives)
- [estensione di markup x:Type](~/xamarin-forms/xaml/markup-extensions/consuming.md#type)
- [Passaggio di argomenti del costruttore](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)
