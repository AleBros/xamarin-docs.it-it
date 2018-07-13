---
title: Introduzione ai modelli di dati di xamarin. Forms
description: I modelli di dati di xamarin. Forms offrono la possibilità di definire la presentazione dei dati nei controlli supportati. Questo articolo fornisce un'introduzione ai modelli di dati, esaminare il motivo per cui sono necessari.
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 129ce7a04b93bfb3cb1b9a1639aee61cd56d09d5
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998917"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Introduzione ai modelli di dati di xamarin. Forms

_I modelli di dati di xamarin. Forms offrono la possibilità di definire la presentazione dei dati nei controlli supportati. Questo articolo fornisce un'introduzione ai modelli di dati, esaminare il motivo per cui sono necessari._

Si consideri un [ `ListView` ](xref:Xamarin.Forms.ListView) che visualizza una raccolta di `Person` oggetti. Esempio di codice seguente illustra la definizione del `Person` classe:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

Il `Person` classe definisce `Name`, `Age`, e `Location` le proprietà, che possono essere impostate quando un `Person` oggetto viene creato. Il [ `ListView` ](xref:Xamarin.Forms.ListView) consente di visualizzare la raccolta di `Person` oggetti, come illustrato nell'esempio di codice XAML seguente:

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

Gli elementi vengono aggiunti al [ `ListView` ](xref:Xamarin.Forms.ListView) in XAML inizializzando il [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà da una matrice di `Person` istanze.

> [!NOTE]
> Si noti che il `x:Array` elemento richiede un `Type` attributo che indica il tipo degli elementi nella matrice.

Pagina c# equivalente è illustrata nell'esempio di codice seguente, che inizializza la [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà a un `List` di `Person` istanze:

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

Il [ `ListView` ](xref:Xamarin.Forms.ListView) chiamate `ToString` quando si visualizzano gli oggetti nella raccolta. Poiché non esiste alcuna `Person.ToString` esegue l'override, `ToString` restituisce il nome del tipo di ogni oggetto, come illustrato negli screenshot seguenti:

![](introduction-images/no-data-template.png "ListView senza un modello di dati")

Il `Person` oggetto può eseguire l'override di `ToString` metodo per visualizzare dati significativi, come illustrato nell'esempio di codice seguente:

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

Il risultato è il [ `ListView` ](xref:Xamarin.Forms.ListView) visualizzando il `Person.Name` valore della proprietà per ogni oggetto nella raccolta, come illustrato negli screenshot seguenti:

![](introduction-images/override-tostring.png "ListView con un modello di dati")

Il `Person.ToString` sostituzione potrebbe restituire una stringa formattata costituito il `Name`, `Age`, e `Location` proprietà. Tuttavia, questo approccio offre solo un controllo limitato sull'aspetto di ogni elemento di dati. Per una maggiore flessibilità, un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) possono essere creati che definisce l'aspetto dei dati.

## <a name="creating-a-datatemplate"></a>Creazione di un DataTemplate

Oggetto [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) viene usato per specificare l'aspetto dei dati e Usa in genere l'associazione dati per visualizzare i dati. Lo scenario di utilizzo comune è la visualizzazione di dati da una raccolta di oggetti in un [ `ListView` ](xref:Xamarin.Forms.ListView). Ad esempio, quando un `ListView` è associato a una raccolta di `Person` oggetti, il `ListView.ItemTemplate` verrà impostata su un `DataTemplate` che definisce l'aspetto della ognuno `Person` dell'oggetto nel `ListView`. Il `DataTemplate` conterrà gli elementi che associano i valori delle proprietà della ognuno `Person` oggetto. Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

Oggetto [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) può essere utilizzato come valore per le proprietà seguenti:

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), che viene ereditata dagli [ `ListView` ](xref:Xamarin.Forms.ListView).
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1), che viene ereditata dagli [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage), [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage), e [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage).

> [!NOTE]
> Si noti che sebbene il [ `TableView` ](xref:Xamarin.Forms.TableView) Usa [ `Cell` ](xref:Xamarin.Forms.Cell) oggetti, non viene utilizzato un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Infatti, le associazioni dati sono sempre impostate direttamente su `Cell` oggetti.

Oggetto [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) che viene inserito come figlio diretto delle proprietà elencate in precedenza è nota come un' *modello inline*. In alternativa, un `DataTemplate` può essere definita come una risorsa a livello di controllo, a livello di pagina o a livello di applicazione. Scegliere dove definire un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) impatti dove può essere utilizzato:

- Oggetto [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definito il controllo a livello può essere applicato solo al controllo.
- Oggetto [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definiti nella pagina di livello è applicabile a più controlli validi nella pagina.
- Oggetto [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definiti a livello di applicazione possono essere applicati a valido controlli in tutta l'applicazione.

I modelli di dati inferiori nella gerarchia di visualizzazione hanno la precedenza rispetto a quelle definite superiore backup quando condividono `x:Key` attributi. Ad esempio, un modello di dati a livello di applicazione eseguirà l'override di un modello di dati a livello di pagina e un modello di dati a livello di pagina verrà sostituito da un modello di dati a livello di controllo o un modello di dati inline.


## <a name="related-links"></a>Collegamenti correlati

- [Aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelli di dati (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
