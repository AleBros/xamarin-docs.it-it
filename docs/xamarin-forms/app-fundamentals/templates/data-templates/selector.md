---
title: Creazione di un Xamarin.Forms DataTemplateSelector
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 74650eb2c52f1da9d0c539b711784896267ed183
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135954"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Creazione di un Xamarin.Forms DataTemplateSelector

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)

_Un DataTemplateSelector può essere usato per scegliere un DataTemplate in fase di esecuzione in base al valore di una proprietà con associazione a dati. In questo modo è possibile applicare più DataTemplate allo stesso tipo di oggetto per personalizzare l'aspetto di determinati oggetti. Questo articolo illustra come creare e utilizzare un DataTemplateSelector._

Un selettore di modello di dati consente scenari quali un' [`ListView`](xref:Xamarin.Forms.ListView) associazione a una raccolta di oggetti, in cui l'aspetto di ogni oggetto in `ListView` può essere scelto in fase di esecuzione dal selettore del modello di dati che restituisce un particolare [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

## <a name="creating-a-datatemplateselector"></a>Creazione di un DataTemplateSelector

Un selettore di modello di dati viene implementato creando una classe che eredita da [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) . `OnSelectTemplate`Viene quindi eseguito l'override del metodo per restituire un particolare [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , come illustrato nell'esempio di codice seguente:

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

È quindi possibile assegnare un'istanza della classe Selector del modello di dati per Xamarin.Forms controllare proprietà quali [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) . Per un elenco delle proprietà valide, vedere [Creazione di un oggetto DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitazioni

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)le istanze presentano le limitazioni seguenti:

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

Questo livello di pagina [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) definisce due [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) istanze e un' `PersonDataTemplateSelector` istanza. L'istanza di `PersonDataTemplateSelector` imposta le relative proprietà `ValidTemplate` e `InvalidTemplate` sulle istanze di `DataTemplate` appropriate usando l'estensione di markup `StaticResource`. Si noti che mentre le risorse sono definite nell'oggetto della pagina [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , possono essere definite anche a livello di controllo o di applicazione.

L' `PersonDataTemplateSelector` istanza viene utilizzata assegnando questo oggetto alla [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) proprietà, come illustrato nell'esempio di codice seguente:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

In fase di esecuzione, [`ListView`](xref:Xamarin.Forms.ListView) chiama il `PersonDataTemplateSelector.OnSelectTemplate` metodo per ogni elemento della raccolta sottostante, con la chiamata che passa l'oggetto dati come `item` parametro. L' [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto restituito dal metodo viene quindi applicato a tale oggetto.

Gli screenshot seguenti mostrano il risultato dell'applicazione di [`ListView`](xref:Xamarin.Forms.ListView) `PersonDataTemplateSelector` a ogni oggetto nella raccolta sottostante:

![](selector-images/data-template-selector.png "ListView with a Data Template Selector")

Qualsiasi oggetto `Person` con un valore della proprietà `DateOfBirth` maggiore o uguale a 1980 viene visualizzato in verde, mentre gli oggetti rimanenti vengono visualizzati in rosso.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Utilizzo di un DataTemplateSelector in C&num;

In C# `PersonDataTemplateSelector` è possibile creare un'istanza di e assegnarla alla [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) proprietà, come illustrato nell'esempio di codice seguente:

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

L' `PersonDataTemplateSelector` istanza imposta le `ValidTemplate` proprietà e sulle `InvalidTemplate` istanze appropriate [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) create dal `SetupDataTemplates` metodo. In fase di esecuzione, [`ListView`](xref:Xamarin.Forms.ListView) chiama il `PersonDataTemplateSelector.OnSelectTemplate` metodo per ogni elemento della raccolta sottostante, con la chiamata che passa l'oggetto dati come `item` parametro. Il `DataTemplate` restituito dal metodo viene quindi applicato a tale oggetto.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare e utilizzare un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) . Un oggetto `DataTemplateSelector` può essere usato per scegliere un oggetto in fase di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) esecuzione in base al valore di una proprietà con associazione a dati. Questo consente di applicare più istanze di `DataTemplate` allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici.

## <a name="related-links"></a>Collegamenti correlati

- [Data Template Selector (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector) (Esempio di selettore del modello di dati)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
