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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771250"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Creazione di un DataTemplateSelector Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)

_Oggetto DataTemplateSelector può essere utilizzato per scegliere un DataTemplate in fase di esecuzione in base al valore di una proprietà con associazione a dati. In questo modo più DataTemplates da applicare allo stesso tipo di oggetto, per personalizzare l'aspetto di particolari oggetti. In questo articolo viene illustrato come creare e utilizzare un DataTemplateSelector.This article demonstrates how to create and consume a DataTemplateSelector._

Un selettore di modello di [`ListView`](xref:Xamarin.Forms.ListView) dati consente scenari quali un'associazione a `ListView` una raccolta di oggetti, in cui l'aspetto di ogni oggetto in può essere scelto in fase di esecuzione dal selettore del modello di dati che restituisce un particolare [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)oggetto .

## <a name="creating-a-datatemplateselector"></a>Creazione di un DataTemplateSelector

Un selettore del modello di dati viene [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)implementato creando una classe che eredita da . Il `OnSelectTemplate` metodo viene quindi sottoposto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)a override per restituire un particolare , come illustrato nell'esempio di codice seguente:

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

Un'istanza della classe del selettore del modello di dati può quindi [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)essere assegnata alle proprietà del controllo Xamarin.Forms, ad esempio . Per un elenco delle proprietà valide, vedere [Creazione di un oggetto DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitazioni

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)le istanze presentano le seguenti limitazioni:

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

Questo livello [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pagina definisce due istanze e un'istanza. `PersonDataTemplateSelector` L'istanza di `PersonDataTemplateSelector` imposta le relative proprietà `ValidTemplate` e `InvalidTemplate` sulle istanze di `DataTemplate` appropriate usando l'estensione di markup `StaticResource`. Si noti che, sebbene le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)risorse siano definite nel , possono anche essere definite a livello di controllo o a livello di applicazione.

L'istanza `PersonDataTemplateSelector` viene utilizzata assegnandola [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) alla proprietà , come illustrato nell'esempio di codice seguente:The instance is consumed by assigning it to the property, as shown in the following code example:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

In fase [`ListView`](xref:Xamarin.Forms.ListView) di `PersonDataTemplateSelector.OnSelectTemplate` esecuzione, il metodo chiama il metodo per ognuno degli `item` elementi nella raccolta sottostante, con la chiamata che passa l'oggetto dati come parametro. L'oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) restituito dal metodo viene quindi applicato a tale oggetto.

Le schermate seguenti mostrano [`ListView`](xref:Xamarin.Forms.ListView) il `PersonDataTemplateSelector` risultato dell'applicazione di a ogni oggetto nella raccolta sottostante:

![](selector-images/data-template-selector.png "ListView with a Data Template Selector")

Qualsiasi oggetto `Person` con un valore della proprietà `DateOfBirth` maggiore o uguale a 1980 viene visualizzato in verde, mentre gli oggetti rimanenti vengono visualizzati in rosso.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Utilizzo di un DataTemplateSelector in C&num;

Nel linguaggio `PersonDataTemplateSelector` C, è possibile creare [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) un'istanza di l'oggetto e assegnarlo alla proprietà, come illustrato nell'esempio di codice seguente:In C', the can be instantiated and assigned to the property, as shown in the following code example:

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

L'istanza `PersonDataTemplateSelector` `ValidTemplate` imposta `InvalidTemplate` le proprietà [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) e per `SetupDataTemplates` le istanze appropriate create dal metodo . In fase [`ListView`](xref:Xamarin.Forms.ListView) di `PersonDataTemplateSelector.OnSelectTemplate` esecuzione, il metodo chiama il metodo per ognuno degli `item` elementi nella raccolta sottostante, con la chiamata che passa l'oggetto dati come parametro. Il `DataTemplate` restituito dal metodo viene quindi applicato a tale oggetto.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)come creare e utilizzare un oggetto . Un `DataTemplateSelector` oggetto può essere [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) utilizzato per scegliere un in fase di esecuzione in base al valore di una proprietà con associazione a dati. Questo consente di applicare più istanze di `DataTemplate` allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici.

## <a name="related-links"></a>Collegamenti correlati

- [Data Template Selector (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector) (Esempio di selettore del modello di dati)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
