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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771272"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Introduzione ai modelli di dati Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_I modelli di dati Xamarin.Forms consentono di definire la presentazione dei dati nei controlli supportati. In questo articolo viene fornita un'introduzione ai modelli di dati, esaminando il motivo per cui sono necessari._

Si [`ListView`](xref:Xamarin.Forms.ListView) consideri un `Person` che visualizza una raccolta di oggetti. L'esempio di codice seguente illustra la definizione della classe `Person`:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

La classe `Person` definisce le proprietà `Name`, `Age` e `Location`, che possono essere impostate quando viene creato un oggetto `Person`. L'oggetto viene usato per visualizzare la raccolta di oggetti, come illustrato nell'esempio di codice XAML seguente:The [`ListView`](xref:Xamarin.Forms.ListView) is used to display the collection of `Person` objects, as shown in the following XAML code example:

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

Gli elementi vengono [`ListView`](xref:Xamarin.Forms.ListView) aggiunti all'oggetto [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) in XAML `Person` inizializzando la proprietà da una matrice di istanze.

> [!NOTE]
> Si noti che l'elemento `x:Array` richiede un attributo `Type` che indica il tipo degli elementi nella matrice.

Nell'esempio di codice riportato di seguito viene [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) illustrata `List` la `Person` pagina equivalente di C, che inizializza la proprietà su una delle istanze:

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

Chiamate [`ListView`](xref:Xamarin.Forms.ListView) `ToString` quando si visualizzano gli oggetti nella raccolta. Poiché non esiste alcuna sostituzione di `Person.ToString`, `ToString` restituisce il nome del tipo di ogni oggetto, come illustrato negli screenshot seguenti:

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

Ciò comporta [`ListView`](xref:Xamarin.Forms.ListView) la `Person.Name` visualizzazione del valore della proprietà per ogni oggetto nella raccolta, come illustrato nelle schermate seguenti:This results in the displaying the property value for each object in the collection, as shown in the following screenshots:

![](introduction-images/override-tostring.png "ListView with a Data Template")

La sostituzione di `Person.ToString` potrebbe restituire una stringa formattata costituita dalle proprietà `Name`, `Age` e `Location`. Questo approccio offre tuttavia solo un controllo limitato sull'aspetto di ogni elemento di dati. Per una maggiore [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) flessibilità, è possibile creare un oggetto che definisce l'aspetto dei dati.

## <a name="creating-a-datatemplate"></a>Creazione di un oggetto DataTemplate

Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto viene utilizzato per specificare l'aspetto dei dati e in genere utilizza l'associazione dati per visualizzare i dati. Lo scenario di utilizzo comune è quando si [`ListView`](xref:Xamarin.Forms.ListView)visualizzano dati da una raccolta di oggetti in un oggetto . Ad esempio, quando un elemento `ListView` è associato a una raccolta di oggetti `Person`, la proprietà `ListView.ItemTemplate` verrà impostata su un elemento `DataTemplate` che definisce l'aspetto di ogni oggetto `Person` in `ListView`. L'oggetto `DataTemplate` conterrà gli elementi che associano i valori delle proprietà di ogni oggetto `Person`. Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto può essere utilizzato come valore per le seguenti proprietà:

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), ereditato da [`ListView`](xref:Xamarin.Forms.ListView).
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1), ereditato da [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), e .

> [!NOTE]
> Si noti [`TableView`](xref:Xamarin.Forms.TableView) che, [`Cell`](xref:Xamarin.Forms.Cell) sebbene utilizzi di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)oggetti, non utilizza un oggetto . I data binding sono infatti sempre impostati direttamente su oggetti `Cell`.

Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) elemento posizionato come elemento figlio diretto delle proprietà elencate in precedenza è noto come *modello inline*. In alternativa, un oggetto `DataTemplate` può essere definito come una risorsa a livello di controllo, a livello di pagina o a livello di applicazione. Scelta della posizione [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) in cui definire un impatto nel punto in cui può essere utilizzato:

- Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definito a livello di controllo può essere applicato solo al controllo.
- Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definito a livello di pagina può essere applicato a più controlli validi nella pagina.
- Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definito a livello di applicazione può essere applicato acontrolli validi in tutta l'applicazione.

I modelli di dati inferiori nella gerarchia di visualizzazione hanno la precedenza rispetto a quelli definiti a livello superiore quando condividono attributi `x:Key`. Ad esempio, un modello di dati a livello di applicazione verrà sostituito da un modello di dati a livello di pagina e un modello di dati a livello di pagina verrà sostituito da un modello di dati a livello di controllo o da un modello di dati inline.

## <a name="related-links"></a>Collegamenti correlati

- [Aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelli di dati (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
