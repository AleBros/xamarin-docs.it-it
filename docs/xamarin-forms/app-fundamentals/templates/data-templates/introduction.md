---
title: Introduzione ai modelli di dati Xamarin.Forms
description: I modelli di dati Xamarin.Forms consentono di definire la presentazione dei dati nei controlli supportati. Questo articolo offre un'introduzione ai modelli di dati e spiega perché sono necessari.
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 10bba38de1dc8908ad853d5e4ca2bb845b4ac8c6
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70771272"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Introduzione ai modelli di dati Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_I modelli di dati Xamarin.Forms offrono la possibilità di definire la presentazione dei dati nei controlli supportati. Questo articolo fornisce un'introduzione ai modelli di dati, esaminando il motivo per cui sono necessari._

Si consideri un [`ListView`](xref:Xamarin.Forms.ListView) in cui viene visualizzata una raccolta di oggetti `Person`. L'esempio di codice seguente illustra la definizione della classe `Person`:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

La classe `Person` definisce le proprietà `Name`, `Age` e `Location`, che possono essere impostate quando viene creato un oggetto `Person`. L'elemento [`ListView`](xref:Xamarin.Forms.ListView) viene usato per visualizzare la raccolta di oggetti `Person`, come illustrato nell'esempio di codice XAML seguente:

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
                    <local:Person Name="John" Age="37" Location="USA" />
                    <local:Person Name="Tom" Age="42" Location="UK" />
                    <local:Person Name="Lucas" Age="29" Location="Germany" />
                    <local:Person Name="Tariq" Age="39" Location="UK" />
                    <local:Person Name="Jane" Age="30" Location="USA" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

Gli elementi vengono aggiunti all'oggetto [`ListView`](xref:Xamarin.Forms.ListView) in XAML inizializzando la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) da una matrice di istanze `Person`.

> [!NOTE]
> Si noti che l'elemento `x:Array` richiede un attributo `Type` che indica il tipo degli elementi nella matrice.

La pagina C# equivalente è illustrata nell'esempio di codice seguente, che inizializza la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) su un `List` di istanze `Person`:

```csharp
public WithoutDataTemplatePageCS()
{
    ...
    var people = new List<Person>
    {
        new Person { Name = "Steve", Age = 21, Location = "USA" },
        new Person { Name = "John", Age = 37, Location = "USA" },
        new Person { Name = "Tom", Age = 42, Location = "UK" },
        new Person { Name = "Lucas", Age = 29, Location = "Germany" },
        new Person { Name = "Tariq", Age = 39, Location = "UK" },
        new Person { Name = "Jane", Age = 30, Location = "USA" }
    };

    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children = {
            ...
            new ListView { ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
        }
    };
}
```

L'elemento [`ListView`](xref:Xamarin.Forms.ListView) chiama `ToString` durante la visualizzazione degli oggetti nella raccolta. Poiché non esiste alcuna sostituzione di `Person.ToString`, `ToString` restituisce il nome del tipo di ogni oggetto, come illustrato negli screenshot seguenti:

![](introduction-images/no-data-template.png "ListView without a Data Template")

L'oggetto `Person` può eseguire l'override del metodo `ToString` per visualizzare dati significativi, come illustrato nell'esempio di codice seguente:

```csharp
public class Person
{
  ...
  public override string ToString ()
  {
    return Name;
  }
}
```

Il risultato è l'elemento [`ListView`](xref:Xamarin.Forms.ListView) che visualizza il valore della proprietà `Person.Name` per ogni oggetto nella raccolta, come illustrato negli screenshot seguenti:

![](introduction-images/override-tostring.png "ListView with a Data Template")

La sostituzione di `Person.ToString` potrebbe restituire una stringa formattata costituita dalle proprietà `Name`, `Age` e `Location`. Questo approccio offre tuttavia solo un controllo limitato sull'aspetto di ogni elemento di dati. Per una maggiore flessibilità, è possibile creare un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che definisce l'aspetto dei dati.

## <a name="creating-a-datatemplate"></a>Creazione di un oggetto DataTemplate

Un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene usato per specificare l'aspetto dei dati e usa in genere il data binding per visualizzare i dati. Lo scenario di utilizzo comune è durante la visualizzazione di dati da una raccolta di oggetti in un elemento [`ListView`](xref:Xamarin.Forms.ListView). Ad esempio, quando un elemento `ListView` è associato a una raccolta di oggetti `Person`, la proprietà `ListView.ItemTemplate` verrà impostata su un elemento `DataTemplate` che definisce l'aspetto di ogni oggetto `Person` in `ListView`. L'oggetto `DataTemplate` conterrà gli elementi che associano i valori delle proprietà di ogni oggetto `Person`. Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

Un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) può essere usato come valore per le proprietà seguenti:

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), che viene ereditata da [`ListView`](xref:Xamarin.Forms.ListView).
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1), che viene ereditata da [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage).

> [!NOTE]
> Si noti che, sebbene [`TableView`](xref:Xamarin.Forms.TableView) usi gli oggetti [`Cell`](xref:Xamarin.Forms.Cell), non fa uso di un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). I data binding sono infatti sempre impostati direttamente su oggetti `Cell`.

Un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che viene inserito come figlio diretto delle proprietà elencate in precedenza è detto *modello inline*. In alternativa, un oggetto `DataTemplate` può essere definito come una risorsa a livello di controllo, a livello di pagina o a livello di applicazione. La scelta della posizione in cui definire un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ha effetto sulla posizione in cui può essere usato:

- Un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definito a livello di controllo può essere applicato solo al controllo.
- Un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definito a livello di pagina può essere applicato a più controlli validi nella pagina.
- Un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definito a livello di applicazione può essere applicato a controlli validi in tutta l'applicazione.

I modelli di dati inferiori nella gerarchia di visualizzazione hanno la precedenza rispetto a quelli definiti a livello superiore quando condividono attributi `x:Key`. Ad esempio, un modello di dati a livello di applicazione verrà sostituito da un modello di dati a livello di pagina e un modello di dati a livello di pagina verrà sostituito da un modello di dati a livello di controllo o da un modello di dati inline.

## <a name="related-links"></a>Collegamenti correlati

- [Aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelli di dati (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
