---
title: Creazione di un DataTemplateSelector xamarin. Forms
description: Questo articolo illustra come creare e utilizzare un DataTemplateSelector, che consente di scegliere un DataTemplate in fase di esecuzione in base al valore di una proprietà con associazione a dati.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: a72777c7e51e96a8e123ecd85ad0aa24fc60fc6c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994517"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Creazione di un DataTemplateSelector xamarin. Forms

_Un DataTemplateSelector è utilizzabile per scegliere un DataTemplate in fase di esecuzione in base al valore di una proprietà con associazione a dati. In questo modo, più DataTemplate da applicare allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici. Questo articolo illustra come creare e utilizzare un DataTemplateSelector._

Un selettore di modello di dati consente gli scenari, ad esempio un [ `ListView` ](xref:Xamarin.Forms.ListView) associazione a una raccolta di oggetti, in cui l'aspetto di ogni oggetto nel `ListView` selezionabili in fase di esecuzione dal selettore del modello dati restituzione di un determinata [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate).

## <a name="creating-a-datatemplateselector"></a>Creazione di un DataTemplateSelector

Un selettore di modello di dati viene implementato tramite la creazione di una classe che eredita da [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector). Il `OnSelectTemplate` è quindi sottoposto a override per restituire un particolare [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), come illustrato nell'esempio di codice seguente:

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

Il `OnSelectTemplate` metodo restituisce il modello appropriato in base al valore della `DateOfBirth` proprietà. Il modello da restituire è il valore della `ValidTemplate` proprietà o il `InvalidTemplate` proprietà, che vengono impostate quando si utilizzano il `PersonDataTemplateSelector`.

Un'istanza della classe di selettore del modello di dati possa quindi essere assegnata alle proprietà dei controlli di xamarin. Forms, ad esempio [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1). Per un elenco di proprietà valide, vedere [creazione di un DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitazioni

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) istanze presentano le limitazioni seguenti:

- Il `DataTemplateSelector` sottoclasse deve sempre restituire lo stesso modello per gli stessi dati, se sottoposti a query più volte.
- Il `DataTemplateSelector` sottoclasse non deve restituire un altro `DataTemplateSelector` sottoclasse.
- Il `DataTemplateSelector` sottoclasse non deve restituire nuove istanze di un `DataTemplate` per ogni chiamata. Al contrario, la stessa istanza deve essere restituita. In caso contrario, verrà creata una perdita di memoria e disabiliterà la virtualizzazione.
- In Android, può essere presente non più di 20 modelli di dati diversi per ogni `ListView`.

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

Questo livello di pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) vengono definiti due [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) istanze e un `PersonDataTemplateSelector` istanza. Il `PersonDataTemplateSelector` set dell'istanza relativo `ValidTemplate` e `InvalidTemplate` delle proprietà appropriata `DataTemplate` istanze usando il `StaticResource` estensione di markup. Si noti che mentre le risorse definite della pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), può anche essere definiti a livello di controllo oppure a livello di applicazione.

Il `PersonDataTemplateSelector` istanza viene consumata assegnando alla VM per il [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) proprietà, come illustrato nell'esempio di codice seguente:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

In fase di esecuzione, il [ `ListView` ](xref:Xamarin.Forms.ListView) chiama il `PersonDataTemplateSelector.OnSelectTemplate` metodo per ognuno degli elementi nella raccolta sottostante, con la chiamata si passa l'oggetto dati come il `item` parametro. Il [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) restituito dal metodo viene quindi applicato a tale oggetto.

Le schermate seguenti illustrano il risultato del [ `ListView` ](xref:Xamarin.Forms.ListView) applicando il `PersonDataTemplateSelector` a ogni oggetto nella raccolta sottostante:

![](selector-images/data-template-selector.png "ListView con un selettore di modello di dati")

Eventuali `Person` oggetto avente un `DateOfBirth` proprietà valore maggiore o uguale al 1980 viene visualizzato in verde, con gli oggetti rimanenti viene visualizzati in rosso.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Utilizzo di un DataTemplateSelector in C&num;

In c#, il `PersonDataTemplateSelector` può essere creata e assegnata per il [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) proprietà, come illustrato nell'esempio di codice seguente:

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

Il `PersonDataTemplateSelector` istanza set relativo `ValidTemplate` e `InvalidTemplate` delle proprietà appropriata [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) istanze create dal `SetupDataTemplates` (metodo). In fase di esecuzione, il [ `ListView` ](xref:Xamarin.Forms.ListView) chiama il `PersonDataTemplateSelector.OnSelectTemplate` metodo per ognuno degli elementi nella raccolta sottostante, con la chiamata si passa l'oggetto dati come il `item` parametro. Il `DataTemplate` restituito dal metodo viene quindi applicato a tale oggetto.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare e utilizzare un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector). Oggetto `DataTemplateSelector` può essere utilizzato per scegliere una [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) in fase di esecuzione in base al valore di una proprietà con associazione a dati. In questo modo, più `DataTemplate` istanze da applicare allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici.


## <a name="related-links"></a>Collegamenti correlati

- [Selettore di modello di dati (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
