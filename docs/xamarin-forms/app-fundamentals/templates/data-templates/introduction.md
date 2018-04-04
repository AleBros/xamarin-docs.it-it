---
title: Introduzione
description: I modelli di xamarin. Forms dati consentono di definire la presentazione dei dati in controlli supportati. In questo articolo viene fornita un'introduzione ai modelli di dati, esaminare il motivo per cui sono necessari.
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 6b71e1cb5f45a580aaf04c267028af1740cb560e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="introduction"></a>Introduzione

_I modelli di xamarin. Forms dati consentono di definire la presentazione dei dati in controlli supportati. In questo articolo viene fornita un'introduzione ai modelli di dati, esaminare il motivo per cui sono necessari._

Si consideri un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) che visualizza una raccolta di `Person` oggetti. Esempio di codice seguente viene illustrata la definizione del `Person` classe:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

Il `Person` classe definisce `Name`, `Age`, e `Location` proprietà, che può essere impostata quando un `Person` viene creato l'oggetto. Il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) viene utilizzato per visualizzare la raccolta di `Person` oggetti, come illustrato nell'esempio di codice XAML seguente:

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

Gli elementi vengono aggiunti per il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) in XAML inizializzando il [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) proprietà da una matrice di `Person` istanze.

> [!NOTE]
> Si noti che il `x:Array` elemento richiede un `Type` attributo che indica il tipo degli elementi nella matrice.

La pagina di equivalenti in c# è illustrata nell'esempio di codice seguente, che inizializza il [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) proprietà per un `List` di `Person` istanze:

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

Il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) chiamate `ToString` quando la visualizzazione degli oggetti nella raccolta. Poiché non esiste alcun `Person.ToString` esegue l'override, `ToString` restituisce il nome del tipo di ogni oggetto, come illustrato nelle schermate seguenti:

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

Di conseguenza, il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) la visualizzazione di `Person.Name` valore della proprietà per ogni oggetto nella raccolta, come illustrato nelle schermate seguenti:

![](introduction-images/override-tostring.png "ListView con un modello di dati")

Il `Person.ToString` sostituzione potrebbe restituire una stringa formattata costituito il `Name`, `Age`, e `Location` proprietà. Tuttavia, questo approccio offre solo un controllo sull'aspetto di ogni elemento di dati limitato. Per maggiore flessibilità, un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) possono essere creati che definisce l'aspetto dei dati.

## <a name="creating-a-datatemplate"></a>Creazione di un elemento DataTemplate

Oggetto [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) viene utilizzata per specificare l'aspetto dei dati e in genere utilizza l'associazione dati per visualizzare i dati. Lo scenario di utilizzo comune è quando si visualizzano dati da una raccolta di oggetti in un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Ad esempio, quando un `ListView` è associato a una raccolta di `Person` oggetti, il `ListView.ItemTemplate` verrà impostata su un `DataTemplate` che definisce l'aspetto di ogni `Person` oggetto di `ListView`. Il `DataTemplate` conterrà gli elementi associati ai valori delle proprietà di ogni `Person` oggetto. Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

Oggetto [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) può essere utilizzato come valore per le proprietà seguenti:

- [`ListView.HeaderTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.HeaderTemplate/)
- [`ListView.FooterTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.FooterTemplate/)
- [`ListView.GroupHeaderTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupHeaderTemplate/)
- [`ItemsView.ItemTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/), che viene ereditata da [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/).
- [`MultiPage.ItemTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/), che viene ereditata da [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/), e [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/).

> [!NOTE]
> Si noti che sebbene il [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) Usa di [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) oggetti, non viene utilizzato un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/). Infatti, le associazioni dati sono sempre impostate direttamente nel `Cell` oggetti.

Oggetto [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) che viene inserito come figlio diretto delle proprietà elencate in precedenza è nota come un *modello inline*. In alternativa, un `DataTemplate` può essere definita come una risorsa a livello di controllo, a livello di pagina o a livello di applicazione. Scelta di come definire un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) impatti dove possono essere usato:

- Oggetto [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) definito a controllo livello può essere applicato solo al controllo.
- Oggetto [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) definito a pagina di livello può essere applicato a più controlli validi nella pagina.
- Oggetto [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) definiti a livello di applicazione possono essere applicate a valido controlli in tutta l'applicazione.

I modelli di dati inferiori nella gerarchia di visualizzazione hanno la precedenza su quelli definiti più alto quando condividono `x:Key` attributi. Ad esempio, un modello di applicazione livello dati verrà sostituito da un modello di dati a livello di pagina e un modello di dati a livello di pagina verrà sostituito da un modello di dati a livello di controllo o un modello di dati inline.


## <a name="related-links"></a>Collegamenti correlati

- [Aspetto delle celle](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelli di dati (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)
