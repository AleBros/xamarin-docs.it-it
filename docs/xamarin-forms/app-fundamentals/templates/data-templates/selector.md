---
title: Creazione di un DataTemplateSelector
description: Un DataTemplateSelector consente di scegliere un modello di dati in fase di esecuzione in base al valore di una proprietà con associazione a dati. In questo modo più DataTemplate da applicare allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici. In questo articolo viene illustrato come creare e utilizzare un DataTemplateSelector.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: d87b9f3cf47e3b3efa42ad53cfba91bac1d07d4f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-datatemplateselector"></a>Creazione di un DataTemplateSelector

_Un DataTemplateSelector consente di scegliere un modello di dati in fase di esecuzione in base al valore di una proprietà con associazione a dati. In questo modo più DataTemplate da applicare allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici. In questo articolo viene illustrato come creare e utilizzare un DataTemplateSelector._

Un selettore di modello di dati consente gli scenari, ad esempio un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) associazione a una raccolta di oggetti, in cui l'aspetto di ogni oggetto nella `ListView` è possibile accedere in fase di esecuzione dal selettore del modello di dati restituisce un particolare [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/).

## <a name="creating-a-datatemplateselector"></a>Creazione di un DataTemplateSelector

Un selettore di modello di dati è implementato mediante la creazione di una classe che eredita da [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/). Il `OnSelectTemplate` è quindi l'override di metodo per restituire un particolare [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), come illustrato nell'esempio di codice seguente:

```csharp
public class PersonDataTemplateSelector : DataTemplateSelector
{
  public DataTemplate ValidTemplate { get; set; }
  public DataTemplate InvalidTemplate { get; set; }

  protected override DataTemplate OnSelectTemplate (object item, BindableObject container)
  {
    return ((Person)item).DateOfBirth.Year >= 1980 ? ValidTemplate : InvalidTemplate;
  }
}
```

Il `OnSelectTemplate` il modello appropriato in base al valore del metodo viene restituito il `DateOfBirth` proprietà. Il modello da restituire è il valore della `ValidTemplate` proprietà o `InvalidTemplate` proprietà, che vengono impostate quando si utilizzano il `PersonDataTemplateSelector`.

Un'istanza della classe di selettore di modello di dati può quindi essere assegnata alle proprietà del controllo di xamarin. Forms, ad esempio [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/). Per un elenco di proprietà valide, vedere [la creazione di un elemento DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitazioni

[`DataTemplateSelector`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) istanze presentano le limitazioni seguenti:

- Il `DataTemplateSelector` sottoclasse deve sempre restituire lo stesso modello per gli stessi dati, se richiesto più volte.
- Il `DataTemplateSelector` sottoclasse non deve restituire un altro `DataTemplateSelector` sottoclasse.
- Il `DataTemplateSelector` sottoclasse non deve restituire nuove istanze di un `DataTemplate` per ogni chiamata. Al contrario, la stessa istanza deve essere restituita. In caso contrario, verrà creata una perdita di memoria e verrà disattivata la virtualizzazione.
- In Android, può essere non più di 20 modelli di dati diversi per `ListView`.

## <a name="consuming-a-datatemplateselector-in-xaml"></a>Utilizzo di un DataTemplateSelector in XAML

In XAML, il `PersonDataTemplateSelector` può essere creata un'istanza, dichiararla come una risorsa, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Selector;assembly=Selector" x:Class="Selector.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="validPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <DataTemplate x:Key="invalidPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <local:PersonDataTemplateSelector x:Key="personDataTemplateSelector"
                ValidTemplate="{StaticResource validPersonTemplate}"
                InvalidTemplate="{StaticResource invalidPersonTemplate}" />
        </ResourceDictionary>
    </ContentPage.Resources>
  ...
</ContentPage>
```

Questo livello di pagina [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definisce due [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) istanze e un `PersonDataTemplateSelector` istanza. Il `PersonDataTemplateSelector` istanza set relativo `ValidTemplate` e `InvalidTemplate` proprietà appropriati `DataTemplate` istanze utilizzando il `StaticResource` estensione di markup. Si noti che, mentre le risorse, definiti nella pagina [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), può anche essere definiti a livello di applicazione o il livello di controllo.

Il `PersonDataTemplateSelector` istanza viene utilizzata tramite l'assegnazione per il [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) proprietà, come illustrato nell'esempio di codice seguente:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

In fase di esecuzione, il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) chiamate di `PersonDataTemplateSelector.OnSelectTemplate` metodo per ogni elemento nella raccolta sottostante, con la chiamata si passa l'oggetto dati come il `item` parametro. Il [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) restituito dal metodo viene quindi applicato all'oggetto.

La schermata seguente mostra il risultato del [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) l'applicazione di `PersonDataTemplateSelector` a ogni oggetto nella raccolta sottostante:

![](selector-images/data-template-selector.png "ListView con un selettore di modello di dati")

Qualsiasi `Person` oggetto che ha un `DateOfBirth` maggiore o uguale a 1980 valore della proprietà viene visualizzato in verde, con gli oggetti rimanenti viene visualizzati in rosso.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Utilizzo di un DataTemplateSelector in C&num;

In c#, la `PersonDataTemplateSelector` può essere creata un'istanza e assegnato al [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) proprietà, come illustrato nell'esempio di codice seguente:

```csharp
public class HomePageCS : ContentPage
{
  DataTemplate validTemplate;
  DataTemplate invalidTemplate;

  public HomePageCS ()
  {
    ...
    SetupDataTemplates ();
    var listView = new ListView {
      ItemsSource = people,
      ItemTemplate = new PersonDataTemplateSelector {
        ValidTemplate = validTemplate,
        InvalidTemplate = invalidTemplate }
    };

    Content = new StackLayout {
      Margin = new Thickness (20),
      Children = {
        ...
        listView
      }
    };
  }
  ...  
}
```

Il `PersonDataTemplateSelector` istanza set relativo `ValidTemplate` e `InvalidTemplate` proprietà appropriati [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) istanze create dal `SetupDataTemplates` metodo. In fase di esecuzione, il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) chiamate di `PersonDataTemplateSelector.OnSelectTemplate` metodo per ogni elemento nella raccolta sottostante, con la chiamata si passa l'oggetto dati come il `item` parametro. Il `DataTemplate` restituito dal metodo viene quindi applicato all'oggetto.

## <a name="summary"></a>Riepilogo

In questo articolo ha illustrato come creare e utilizzare un [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/). Oggetto `DataTemplateSelector` consente di scegliere un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) in fase di esecuzione in base al valore di una proprietà con associazione a dati. In questo modo, più `DataTemplate` istanze da applicare allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici.


## <a name="related-links"></a>Collegamenti correlati

- [Selezione di modelli di dati (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)
