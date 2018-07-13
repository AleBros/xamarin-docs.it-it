---
title: Creazione di un DataTemplate xamarin. Forms
description: I modelli di dati possono essere creati inline, in un oggetto ResourceDictionary o da un tipo personalizzato o un tipo di cella di xamarin. Forms appropriato. Questo articolo esamina ogni tecnica.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 63f9bf82bc8e637aced1afa5d5699ac1e8dc3f8c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994615"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Creazione di un DataTemplate xamarin. Forms

_I modelli di dati possono essere creati inline, in un oggetto ResourceDictionary o da un tipo personalizzato o un tipo di cella di xamarin. Forms appropriato. Questo articolo esamina ogni tecnica._

Uno scenario di utilizzo comune per un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) vengono visualizzati i dati da una raccolta di oggetti in un [ `ListView` ](xref:Xamarin.Forms.ListView). L'aspetto dei dati per ogni cella nella [ `ListView` ](xref:Xamarin.Forms.ListView) possono essere gestiti mediante l'impostazione di [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) proprietà a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Esistono diverse tecniche utilizzabili per eseguire questa operazione:

- [Creazione di un Inline DataTemplate](#inline).
- [Creazione di un DataTemplate con un tipo](#type).
- [Creazione di un DataTemplate come risorsa](#resource).

Indipendentemente dalla tecnica viene utilizzata, il risultato è che l'aspetto di ogni cella del [ `ListView` ](xref:Xamarin.Forms.ListView) è definito da una [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), come illustrato negli screenshot seguenti:

![](creating-images/data-template-appearance.png "ListView con DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Creazione di un Inline DataTemplate

Il [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) può essere impostata su un inline [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Usare un modello inline, ovvero una che viene inserito come figlio diretto di una proprietà di controllo appropriato, se non è necessario per riutilizzare il modello di dati in un' posizione. Gli elementi specificati nel `DataTemplate` definiscono l'aspetto di ogni cella, come illustrato nell'esempio di codice XAML seguente:

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

L'elemento figlio di inline [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) deve essere di, o derivare da, digitare [ `ViewCell` ](xref:Xamarin.Forms.ViewCell). Layout all'interno di `ViewCell` gestito qui da un [ `Grid` ](xref:Xamarin.Forms.Grid). Il `Grid` contiene tre [ `Label` ](xref:Xamarin.Forms.Label) istanze che bind loro [ `Text` ](xref:Xamarin.Forms.Label.Text) proprietà per le proprietà appropriate della ognuno `Person` oggetto nella raccolta.

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

In c# inline [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) viene creato utilizzando un overload del costruttore che specifica un `Func` argomento.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Creazione di un DataTemplate con un tipo

Il [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) può anche essere impostata su un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) creata da un tipo di cella. Il vantaggio di questo approccio è che l'aspetto definito dal tipo di cella può essere riutilizzato da più modelli di dati in tutta l'applicazione. Il codice XAML seguente viene illustrato un esempio di questo approccio:

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

In questo caso, il [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) è impostata su un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) che viene creato da un tipo personalizzato che definisce l'aspetto delle celle. Il tipo personalizzato deve derivare dal tipo [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), come illustrato nell'esempio di codice seguente:

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

All'interno di [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), layout è gestito seguito da una [ `Grid` ](xref:Xamarin.Forms.Grid). Il `Grid` contiene tre [ `Label` ](xref:Xamarin.Forms.Label) istanze che bind loro [ `Text` ](xref:Xamarin.Forms.Label.Text) proprietà per le proprietà appropriate della ognuno `Person` oggetto nella raccolta.

Nell'esempio seguente viene illustrato il codice c# equivalente:

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

In c#, il [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) viene creato utilizzando un overload del costruttore che specifica il tipo di cella come argomento. Il tipo di cella deve derivare dal tipo [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), come illustrato nell'esempio di codice seguente:

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
> Si noti che xamarin. Forms include anche i tipi di cella che possono essere utilizzati per visualizzare dati semplici [ `ListView` ](xref:Xamarin.Forms.ListView) celle. Per altre informazioni, vedere [aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Creazione di un DataTemplate come risorsa

I modelli di dati possono essere creati anche come oggetti riutilizzabili in un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Questo risultato viene ottenuto, assegnando un valore univoco a ogni dichiarazione `x:Key` attributo, che fornisce una chiave descrittiva nel `ResourceDictionary`, come illustrato nell'esempio di codice XAML seguente:

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

Il [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) viene assegnato al [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) usando il `StaticResource` estensione di markup. Si noti che, anche se il `DataTemplate` è definito in della pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), può anche essere definito a livello di controllo oppure a livello di applicazione.

Esempio di codice seguente mostra la pagina equivalente nel linguaggio c#:

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

Il [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) viene aggiunto al [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) tramite i [ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) metodo, che specifica un `Key` stringa che viene usato per riferimento di `DataTemplate` quando vengono recuperati.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come creare modelli di dati inline, da un tipo personalizzato, o in un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Un modello inline deve essere utilizzato se non è necessario per riutilizzare il modello di dati in un' posizione. In alternativa, un modello di dati può essere riutilizzato da definirla come un tipo personalizzato, o come una risorsa a livello di controllo, a livello di pagina o a livello di applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelli di dati (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
