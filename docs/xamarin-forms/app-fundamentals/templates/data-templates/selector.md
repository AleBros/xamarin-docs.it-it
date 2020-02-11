---
title: Creazione di un DataTemplateSelector Xamarin.Forms
description: Questo articolo illustra come creare e utilizzare un DataTemplateSelector, che consente di scegliere un DataTemplate in fase di esecuzione in base al valore di una proprietà associata a dati.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e9a17bff9bd0a23d59faf7602544b25c7ec05a86
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771250"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Creazione di un DataTemplateSelector Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)

_È possibile usare un DataTemplateSelector per scegliere un DataTemplate in fase di esecuzione in base al valore di una proprietà associata a dati. Questo consente di applicare più DataTemplate allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici. Questo articolo illustra come creare e utilizzare un DataTemplateSelector._

Un selettore di modello di dati rende possibili scenari come l'associazione di un controllo [`ListView`](xref:Xamarin.Forms.ListView) a una raccolta di oggetti, in cui l'aspetto di ogni oggetto nel controllo `ListView` può essere scelto in fase di esecuzione dal selettore del modello di dati con la restituzione di un particolare [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

## <a name="creating-a-datatemplateselector"></a>Creazione di un DataTemplateSelector

Un selettore del modello di dati viene implementato tramite la creazione di una classe che eredita da [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector). Il metodo `OnSelectTemplate` è quindi sottoposto a override per restituire un particolare [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), come illustrato nell'esempio di codice seguente:

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

Il metodo `OnSelectTemplate` restituisce il modello appropriato in base al valore della proprietà `DateOfBirth`. Il modello da restituire è il valore della proprietà `ValidTemplate` o della proprietà `InvalidTemplate`, impostata quando si utilizza `PersonDataTemplateSelector`.

Un'istanza della classe del selettore del modello di dati può quindi essere assegnata alle proprietà dei controlli di Xamarin.Forms, come [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1). Per un elenco delle proprietà valide, vedere [Creazione di un oggetto DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitazioni

Le istanze di [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) presentano le limitazioni seguenti:

- La sottoclasse `DataTemplateSelector` deve sempre restituire lo stesso modello per gli stessi dati, se sottoposti a query più volte.
- La sottoclasse `DataTemplateSelector` non deve restituire un'altra sottoclasse `DataTemplateSelector`.
- La sottoclasse `DataTemplateSelector` non deve restituire nuove istanze di un `DataTemplate` per ogni chiamata. Al contrario, deve essere restituita la stessa istanza. In caso contrario, si creerà una perdita di memoria e la virtualizzazione verrà disabilitata.
- In Android, non possono esistere più di 20 modelli di dati diversi per ogni `ListView`.

## <a name="consuming-a-datatemplateselector-in-xaml"></a>Utilizzo di un DataTemplateSelector in XAML

In XAML, è possibile creare un'istanza di `PersonDataTemplateSelector` dichiarandolo come risorsa, come illustrato nell'esempio di codice seguente:

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

Questo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a livello di pagina definisce due istanze di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) e un'istanza di `PersonDataTemplateSelector`. L'istanza di `PersonDataTemplateSelector` imposta le relative proprietà `ValidTemplate` e `InvalidTemplate` sulle istanze di `DataTemplate` appropriate usando l'estensione di markup `StaticResource`. Si noti che anche se le risorse sono definite nel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) della pagina, possono essere definite anche a livello di controllo o di applicazione.

L'istanza di `PersonDataTemplateSelector` viene utilizzata assegnandola alla proprietà [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), come illustrato nell'esempio di codice seguente:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

In fase di esecuzione, [`ListView`](xref:Xamarin.Forms.ListView) chiama il metodo `PersonDataTemplateSelector.OnSelectTemplate` per ognuno degli elementi nella raccolta sottostante, con il passaggio dell'oggetto dati come parametro `item` nella chiamata. Il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) restituito dal metodo viene quindi applicato a tale oggetto.

Gli screenshot seguenti illustrano il risultato dell'applicazione del `PersonDataTemplateSelector` da parte del controllo [`ListView`](xref:Xamarin.Forms.ListView) a ogni oggetto nella raccolta sottostante:

![](selector-images/data-template-selector.png "ListView con un selettore del modello di dati")

Qualsiasi oggetto `Person` con un valore della proprietà `DateOfBirth` maggiore o uguale a 1980 viene visualizzato in verde, mentre gli oggetti rimanenti vengono visualizzati in rosso.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Utilizzo di un DataTemplateSelector in C&num;

In C#, è possibile creare un'istanza di `PersonDataTemplateSelector` e assegnarlo alla proprietà [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), come illustrato nell'esempio di codice seguente:

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

L'istanza di `PersonDataTemplateSelector` imposta le proprietà `ValidTemplate` e `InvalidTemplate` sulle istanze di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) appropriate create dal metodo `SetupDataTemplates`. In fase di esecuzione, [`ListView`](xref:Xamarin.Forms.ListView) chiama il metodo `PersonDataTemplateSelector.OnSelectTemplate` per ognuno degli elementi nella raccolta sottostante, con il passaggio dell'oggetto dati come parametro `item` nella chiamata. Il `DataTemplate` restituito dal metodo viene quindi applicato a tale oggetto.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come creare e utilizzare un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector). È possibile usare un `DataTemplateSelector` per scegliere un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) in fase di esecuzione in base al valore di una proprietà associata ai dati. Questo consente di applicare più istanze di `DataTemplate` allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici.

## <a name="related-links"></a>Collegamenti correlati

- [Data Template Selector (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector) (Esempio di selettore del modello di dati)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
