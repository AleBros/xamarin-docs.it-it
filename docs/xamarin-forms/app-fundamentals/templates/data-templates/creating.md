---
title: Creazione di un oggetto Xamarin.Forms DataTemplate
description: I modelli di dati possono essere creati inline, in un oggetto ResourceDictionary o da un tipo personalizzato o da un Xamarin.Forms tipo di cella appropriato. Questo articolo esamina ogni tecnica.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3054180a9deb1357357c90db7b7fbdd8058b6773
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84565499"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Creazione di un oggetto Xamarin.Forms DataTemplate

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_I modelli di dati possono essere creati inline, in un oggetto ResourceDictionary o da un tipo personalizzato o da un Xamarin.Forms tipo di cella appropriato. In questo articolo vengono esaminate le singole tecniche._

Uno scenario di utilizzo comune per un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) è la visualizzazione di dati da una raccolta di oggetti in un oggetto [`ListView`](xref:Xamarin.Forms.ListView) . L'aspetto dei dati per ogni cella in [`ListView`](xref:Xamarin.Forms.ListView) può essere gestito impostando la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) proprietà su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Esistono diverse tecniche utilizzabili per eseguire questa operazione:

- [Creazione di un DataTemplate inline](#creating-an-inline-datatemplate).
- [Creazione di un oggetto DataTemplate con un tipo](#creating-a-datatemplate-with-a-type).
- [Creazione di un oggetto DataTemplate come risorsa](#creating-a-datatemplate-as-a-resource).

Indipendentemente dalla tecnica usata, il risultato è che l'aspetto di ogni cella in [`ListView`](xref:Xamarin.Forms.ListView) è definito da un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , come illustrato nelle schermate seguenti:

![](creating-images/data-template-appearance.png "ListView with a DataTemplate")

## <a name="creating-an-inline-datatemplate"></a>Creazione di un DataTemplate inline

La [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) proprietà può essere impostata su un oggetto inline [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Usare un modello inline, ovvero un modello inserito come elemento figlio diretto di una proprietà di controllo appropriato, se non è necessario riutilizzare il modello di dati in un'altra posizione. Gli elementi specificati nel `DataTemplate` definiscono l'aspetto di ogni cella, come illustrato nell'esempio di codice XAML seguente:

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

Il figlio di un elemento inline [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) deve essere di tipo o derivare da [`Cell`](xref:Xamarin.Forms.Cell) . In questo esempio viene usato un [`ViewCell`](xref:Xamarin.Forms.ViewCell) che deriva da `Cell` . Il layout all'interno `ViewCell` di è gestito da un oggetto [`Grid`](xref:Xamarin.Forms.Grid) . `Grid`Contiene tre [`Label`](xref:Xamarin.Forms.Label) istanze di che associano le [`Text`](xref:Xamarin.Forms.Label.Text) proprietà alle proprietà appropriate di ogni `Person` oggetto nella raccolta.

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

In C#, l'inline [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene creato usando un overload del costruttore che specifica un `Func` argomento.

## <a name="creating-a-datatemplate-with-a-type"></a>Creazione di un DataTemplate con un tipo

La [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) proprietà può anche essere impostata su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) creato da un tipo di cella. Il vantaggio di questo approccio è che l'aspetto definito dal tipo di cella può essere riutilizzato da più modelli di dati in tutta l'applicazione. Nel codice XAML seguente viene illustrato un esempio di questo approccio:

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

In questo caso, la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) proprietà viene impostata su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) creato da un tipo personalizzato che definisce l'aspetto della cella. Il tipo personalizzato deve derivare dal tipo [`ViewCell`](xref:Xamarin.Forms.ViewCell) , come illustrato nell'esempio di codice seguente:

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

All'interno [`ViewCell`](xref:Xamarin.Forms.ViewCell) di il layout è gestito da un oggetto [`Grid`](xref:Xamarin.Forms.Grid) . `Grid`Contiene tre [`Label`](xref:Xamarin.Forms.Label) istanze di che associano le [`Text`](xref:Xamarin.Forms.Label.Text) proprietà alle proprietà appropriate di ogni `Person` oggetto nella raccolta.

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

In C#, [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene creato usando un overload del costruttore che specifica il tipo di cella come argomento. Il tipo di cella deve derivare dal tipo [`ViewCell`](xref:Xamarin.Forms.ViewCell) , come illustrato nell'esempio di codice seguente:

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
> Si noti che Xamarin.Forms include anche i tipi di cella che possono essere utilizzati per visualizzare dati semplici nelle [`ListView`](xref:Xamarin.Forms.ListView) celle. Per altre informazioni, vedere [Customizing ListView Cell Appearance](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) (Personalizzazione dell'aspetto delle celle ListView).

## <a name="creating-a-datatemplate-as-a-resource"></a>Creazione di un DataTemplate come risorsa

È anche possibile creare modelli di dati come oggetti riutilizzabili in un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Tale risultato si ottiene assegnando a ogni dichiarazione un valore univoco dell'attributo `x:Key`, che fornisce una chiave descrittiva nell'elemento `ResourceDictionary`, come illustrato nell'esempio di codice XAML seguente:

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

L'oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene assegnato alla [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) proprietà tramite l' `StaticResource` estensione di markup. Si noti che mentre `DataTemplate` è definito nella pagina [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , può anche essere definito a livello di controllo o di applicazione.

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

L'oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene aggiunto all'oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) utilizzando il [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) metodo, che specifica una `Key` stringa utilizzata per fare riferimento a `DataTemplate` quando viene recuperato.

## <a name="summary"></a>Summary

Questo articolo ha illustrato come creare modelli di dati, inline, da un tipo personalizzato o in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Usare un modello inline se non è necessario usare di nuovo il modello di dati altrove. In alternativa, è possibile riusare un modello di dati definendolo come tipo personalizzato o come risorsa a livello di controllo, a livello di pagina o a livello di applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelli di dati (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
