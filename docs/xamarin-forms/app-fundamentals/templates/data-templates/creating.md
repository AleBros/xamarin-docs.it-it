---
title: Creazione di un DataTemplate in Xamarin.Forms
description: I modelli di dati possono essere creati inline, in un oggetto ResourceDictionary oppure usando un tipo personalizzato o un tipo di cella Xamarin.Forms appropriato. Questo articolo esamina ogni tecnica.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 399f411acd497b9d55ca81f670556430fe5f5503
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771293"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Creazione di un DataTemplate in Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_I modelli di dati possono essere creati inline, in un ResourceDictionary o da un tipo di cella Xamarin.Forms appropriato o appropriato. Questo articolo esplora ogni tecnica._

Uno scenario di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) utilizzo comune per un oggetto è [`ListView`](xref:Xamarin.Forms.ListView)la visualizzazione di dati da una raccolta di oggetti in un oggetto . L'aspetto dei dati per [`ListView`](xref:Xamarin.Forms.ListView) ogni cella dell'oggetto può essere gestito impostando la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) proprietà su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)oggetto . Esistono diverse tecniche utilizzabili per eseguire questa operazione:

- [Creazione di un DataTemplate inline](#inline).
- [Creazione di un DataTemplate con un tipo](#type).
- [Creazione di un DataTemplate come risorsa](#resource).

Indipendentemente dalla tecnica utilizzata, il risultato è che [`ListView`](xref:Xamarin.Forms.ListView) l'aspetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)di ogni cella nell'oggetto è definito da un oggetto , come illustrato nelle schermate seguenti:

![](creating-images/data-template-appearance.png "ListView with a DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Creazione di un DataTemplate inline

La [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) proprietà può essere impostata su un oggetto inline [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Usare un modello inline, ovvero un modello inserito come elemento figlio diretto di una proprietà di controllo appropriato, se non è necessario riutilizzare il modello di dati in un'altra posizione. Gli elementi specificati nel `DataTemplate` definiscono l'aspetto di ogni cella, come illustrato nell'esempio di codice XAML seguente:

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

L'elemento figlio [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) di un oggetto inline [`Cell`](xref:Xamarin.Forms.Cell)deve essere o derivare dal tipo . In questo [`ViewCell`](xref:Xamarin.Forms.ViewCell)esempio viene utilizzato `Cell`un oggetto , che deriva da . Layout all'interno del `ViewCell` [`Grid`](xref:Xamarin.Forms.Grid)è gestito qui da un . `Grid` L'oggetto [`Label`](xref:Xamarin.Forms.Label) contiene tre [`Text`](xref:Xamarin.Forms.Label.Text) istanze che associano le relative proprietà alle proprietà appropriate di ogni `Person` oggetto nella raccolta.

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

Nel linguaggio C, [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) l'oggetto inline viene `Func` creato utilizzando un overload del costruttore che specifica un argomento.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Creazione di un DataTemplate con un tipo

La [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) proprietà può anche [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) essere impostata su un creato da un tipo di cella. Il vantaggio di questo approccio è che l'aspetto definito dal tipo di cella può essere riutilizzato da più modelli di dati in tutta l'applicazione. Nel codice XAML seguente viene illustrato un esempio di questo approccio:

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

In questo [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) caso, la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà viene impostata su un che viene creato da un tipo personalizzato che definisce l'aspetto della cella. Il tipo personalizzato deve [`ViewCell`](xref:Xamarin.Forms.ViewCell)derivare dal tipo , come illustrato nell'esempio di codice seguente:

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

All'interno del , [`ViewCell`](xref:Xamarin.Forms.ViewCell)il [`Grid`](xref:Xamarin.Forms.Grid)layout è gestito qui da un . `Grid` L'oggetto [`Label`](xref:Xamarin.Forms.Label) contiene tre [`Text`](xref:Xamarin.Forms.Label.Text) istanze che associano le relative proprietà alle proprietà appropriate di ogni `Person` oggetto nella raccolta.

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

Nel linguaggio [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) C, l'oggetto viene creato utilizzando un overload del costruttore che specifica il tipo di cella come argomento. Il tipo di cella [`ViewCell`](xref:Xamarin.Forms.ViewCell)deve derivare dal tipo , come illustrato nell'esempio di codice seguente:

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
> Si noti che Xamarin.Forms include anche i tipi [`ListView`](xref:Xamarin.Forms.ListView) di cella che possono essere utilizzati per visualizzare dati semplici nelle celle. Per altre informazioni, vedere [Customizing ListView Cell Appearance](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) (Personalizzazione dell'aspetto delle celle ListView).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Creazione di un DataTemplate come risorsa

I modelli di dati possono anche [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)essere creati come oggetti riutilizzabili in un file . Tale risultato si ottiene assegnando a ogni dichiarazione un valore univoco dell'attributo `x:Key`, che fornisce una chiave descrittiva nell'elemento `ResourceDictionary`, come illustrato nell'esempio di codice XAML seguente:

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

L'oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) assegnato alla `StaticResource` proprietà utilizzando l'estensione di markup. Si noti `DataTemplate` che mentre l'oggetto è definito nella pagina [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), può anche essere definito a livello di controllo o a livello di applicazione.

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

L'oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) aggiunto [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) al metodo using `Key` , che specifica `DataTemplate` una stringa utilizzata per fare riferimento a quando viene recuperata.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come creare modelli di dati, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)inline, da un tipo personalizzato o in un oggetto . Usare un modello inline se non è necessario usare di nuovo il modello di dati altrove. In alternativa, è possibile riusare un modello di dati definendolo come tipo personalizzato o come risorsa a livello di controllo, a livello di pagina o a livello di applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelli di dati (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
