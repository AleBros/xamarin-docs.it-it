---
title: Creazione di un DataTemplate in Xamarin.Forms
description: I modelli di dati possono essere creati inline, in un oggetto ResourceDictionary oppure usando un tipo personalizzato o un tipo di cella Xamarin.Forms appropriato. Questo articolo esamina ogni tecnica.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: dee45f9788360326ddb393305e9a52627264a671
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926284"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Creazione di un DataTemplate in Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/Templates/DataTemplates/)

_I modelli di dati possono essere creati inline, in un oggetto ResourceDictionary oppure usando un tipo personalizzato o un tipo di cella Xamarin.Forms appropriato. Questo articolo esamina ogni tecnica._

Uno scenario di utilizzo comune per un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) è la visualizzazione di dati da una raccolta di oggetti in un elemento [`ListView`](xref:Xamarin.Forms.ListView). L'aspetto dei dati per ogni cella dell'elemento [`ListView`](xref:Xamarin.Forms.ListView) può essere gestito mediante l'impostazione della proprietà [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Esistono diverse tecniche utilizzabili per eseguire questa operazione:

- [Creazione di un DataTemplate inline](#inline).
- [Creazione di un DataTemplate con un tipo](#type).
- [Creazione di un DataTemplate come risorsa](#resource).

Indipendentemente dalla tecnica che viene utilizzata, il risultato è che l'aspetto di ogni cella dell'elemento [`ListView`](xref:Xamarin.Forms.ListView) è definito da un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), come illustrato negli screenshot seguenti:

![](creating-images/data-template-appearance.png "ListView con un DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Creazione di un DataTemplate inline

La proprietà [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) può essere impostata su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) inline. Usare un modello inline, ovvero un modello inserito come elemento figlio diretto di una proprietà di controllo appropriato, se non è necessario riutilizzare il modello di dati in un'altra posizione. Gli elementi specificati nel `DataTemplate` definiscono l'aspetto di ogni cella, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ListView Margin="0,20,0,0">
    <ListView.ItemsSource>
        <x:Array Type="{x:Type local:Person}">
            <local:Person Name="Steve" Age="21" Location="USA" />
            <local:Person Name="John" Age="37" Location="USA" />
            <local:Person Name="Tom" Age="42" Location="UK" />
            <local:Person Name="Lucas" Age="29" Location="Germany" />
            <local:Person Name="Tariq" Age="39" Location="UK" />
            <local:Person Name="Jane" Age="30" Location="USA" />
        </x:Array>
    </ListView.ItemsSource>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Label Text="{Binding Name}" FontAttributes="Bold" />
                    <Label Grid.Column="1" Text="{Binding Age}" />
                    <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

L'elemento figlio di un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) inline deve essere di tipo [`Cell`](xref:Xamarin.Forms.Cell) o derivare da questo tipo. Questo esempio usa un elemento [`ViewCell`](xref:Xamarin.Forms.ViewCell), che deriva da `Cell`. Il layout all'interno dell'elemento `ViewCell` è gestito in questo caso da un elemento [`Grid`](xref:Xamarin.Forms.Grid). L'elemento `Grid` contiene tre istanze di [`Label`](xref:Xamarin.Forms.Label) che associano le loro proprietà [`Text`](xref:Xamarin.Forms.Label.Text) alle proprietà appropriate di ogni oggetto `Person` della raccolta.

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
public class WithDataTemplatePageCS : ContentPage
{
    public WithDataTemplatePageCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        var personDataTemplate = new DataTemplate(() =>
        {
            var grid = new Grid();
            ...
            var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
            var ageLabel = new Label();
            var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

            nameLabel.SetBinding(Label.TextProperty, "Name");
            ageLabel.SetBinding(Label.TextProperty, "Age");
            locationLabel.SetBinding(Label.TextProperty, "Location");

            grid.Children.Add(nameLabel);
            grid.Children.Add(ageLabel, 1, 0);
            grid.Children.Add(locationLabel, 2, 0);

            return new ViewCell { View = grid };
        });

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemsSource = people, ItemTemplate = personDataTemplate, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

In C# il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) inline viene creato utilizzando un overload del costruttore che specifica un argomento `Func`.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Creazione di un DataTemplate con un tipo

La proprietà [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) può anche essere impostata su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) creato da un tipo di cella. Il vantaggio di questo approccio è che l'aspetto definito dal tipo di cella può essere riutilizzato da più modelli di dati in tutta l'applicazione. Nel codice XAML seguente viene illustrato un esempio di questo approccio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <local:PersonCell />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

In questo caso, la proprietà [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) è impostata su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) creato da un tipo personalizzato che definisce l'aspetto delle celle. Il tipo personalizzato deve derivare dal tipo [`ViewCell`](xref:Xamarin.Forms.ViewCell), come illustrato nell'esempio di codice seguente:

```xaml
<ViewCell xmlns="http://xamarin.com/schemas/2014/forms"
          xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
          x:Class="DataTemplates.PersonCell">
     <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.2*" />
            <ColumnDefinition Width="0.3*" />
        </Grid.ColumnDefinitions>
        <Label Text="{Binding Name}" FontAttributes="Bold" />
        <Label Grid.Column="1" Text="{Binding Age}" />
        <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
    </Grid>
</ViewCell>
```

Il layout all'interno dell'elemento [`ViewCell`](xref:Xamarin.Forms.ViewCell) è gestito in questo caso da un elemento [`Grid`](xref:Xamarin.Forms.Grid). L'elemento `Grid` contiene tre istanze di [`Label`](xref:Xamarin.Forms.Label) che associano le loro proprietà [`Text`](xref:Xamarin.Forms.Label.Text) alle proprietà appropriate di ogni oggetto `Person` della raccolta.

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
public class WithDataTemplatePageFromTypeCS : ContentPage
{
    public WithDataTemplatePageFromTypeCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemTemplate = new DataTemplate(typeof(PersonCellCS)), ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

In C# il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene creato utilizzando un overload del costruttore che specifica il tipo di cella sotto forma di argomento. Il tipo di cella deve derivare dal tipo [`ViewCell`](xref:Xamarin.Forms.ViewCell), come illustrato nell'esempio di codice seguente:

```csharp
public class PersonCellCS : ViewCell
{
    public PersonCellCS()
    {
        var grid = new Grid();
        ...
        var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        var ageLabel = new Label();
        var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

        nameLabel.SetBinding(Label.TextProperty, "Name");
        ageLabel.SetBinding(Label.TextProperty, "Age");
        locationLabel.SetBinding(Label.TextProperty, "Location");

        grid.Children.Add(nameLabel);
        grid.Children.Add(ageLabel, 1, 0);
        grid.Children.Add(locationLabel, 2, 0);

        View = grid;
    }
}
```

> [!NOTE]
> Si noti che Xamarin.Forms include anche tipi di cella che possono essere utilizzati per visualizzare dati semplici in celle [`ListView`](xref:Xamarin.Forms.ListView). Per altre informazioni, vedere [Customizing ListView Cell Appearance](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) (Personalizzazione dell'aspetto delle celle ListView).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Creazione di un DataTemplate come risorsa

I modelli di dati possono essere creati anche come oggetti riutilizzabili in un elemento [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Tale risultato si ottiene assegnando a ogni dichiarazione un valore univoco dell'attributo `x:Key`, che fornisce una chiave descrittiva nell'elemento `ResourceDictionary`, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="personTemplate">
                 <ViewCell>
                    <Grid>
                        ...
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        ...
        <ListView ItemTemplate="{StaticResource personTemplate}" Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

Il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene assegnato alla proprietà [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) usando l'estensione di markup `StaticResource`. Si noti che anche se il `DataTemplate` è definito nell'elemento [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) della pagina, è possibile definirlo anche a livello di controllo o di applicazione.

L'esempio di codice seguente illustra la pagina equivalente in C#:

```csharp
public class WithDataTemplatePageCS : ContentPage
{
  public WithDataTemplatePageCS ()
  {
    ...
    var personDataTemplate = new DataTemplate (() => {
      var grid = new Grid ();
      ...
      return new ViewCell { View = grid };
    });

    Resources = new ResourceDictionary ();
    Resources.Add ("personTemplate", personDataTemplate);

    Content = new StackLayout {
      Margin = new Thickness(20),
      Children = {
        ...
        new ListView { ItemTemplate = (DataTemplate)Resources ["personTemplate"], ItemsSource = people };
      }
    };
  }
}
```

Il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene aggiunto all'elemento [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) tramite il metodo [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)), che specifica una stringa `Key` usata per creare un riferimento al `DataTemplate` quando viene recuperato.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come creare modelli di dati inline da un tipo personalizzato o in un elemento [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Usare un modello inline se non è necessario usare di nuovo il modello di dati altrove. In alternativa, è possibile riusare un modello di dati definendolo come tipo personalizzato o come risorsa a livello di controllo, a livello di pagina o a livello di applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelli di dati (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Templates/DataTemplates/)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
