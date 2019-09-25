---
title: Layout associabili in Novell. Forms
description: I layout associabili consentono alle classi di layout di generare il contenuto mediante associazione a una raccolta di elementi, con l'opzione per impostare l'aspetto di ogni elemento con un oggetto DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: a824c892d21df9264b772bed09a4aef893f3b949
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "68647900"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Layout associabili in Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

I layout associabili consentono a qualsiasi classe di layout che deriva [`Layout<T>`](xref:Xamarin.Forms.Layout`1) dalla classe di generare il contenuto mediante associazione a una raccolta di elementi, con l'opzione per impostare l'aspetto di ogni elemento con [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)un oggetto. I `BindableLayout` layout associabili sono forniti dalla classe, che espone le proprietà associate seguenti:

- `ItemsSource`: specifica la raccolta di `IEnumerable` elementi che devono essere visualizzati dal layout.
- `ItemTemplate`: specifica l' [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto da applicare a ogni elemento nella raccolta di elementi visualizzati dal layout.
- `ItemTemplateSelector`: specifica l' [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) oggetto che verrà usato per scegliere un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto per un elemento in fase di esecuzione.

Queste proprietà possono essere collegate alle [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)classi, [`FlexLayout`](xref:Xamarin.Forms.FlexLayout), [`Grid`](xref:Xamarin.Forms.Grid), [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)e [`StackLayout`](xref:Xamarin.Forms.StackLayout) , che derivano tutte dalla [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe.

> [!NOTE]
> La `ItemTemplate` proprietà ha la precedenza quando vengono `ItemTemplate` impostate `ItemTemplateSelector` entrambe le proprietà e.

La `Layout<T>` classe espone una [`Children`](xref:Xamarin.Forms.Layout`1.Children) raccolta alla quale vengono aggiunti gli elementi figlio di un layout. Quando la `BinableLayout.ItemsSource` proprietà è impostata su una raccolta di elementi e collegata a una [`Layout<T>`](xref:Xamarin.Forms.Layout`1)classe derivata da, ogni elemento della `Layout<T>.Children` raccolta viene aggiunto alla raccolta per la visualizzazione da parte del layout. La `Layout<T>`classe derivata da aggiorna quindi le visualizzazioni figlio quando viene modificata la raccolta sottostante. Per ulteriori informazioni sul ciclo di layout di Novell. Forms, vedere [creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md).

I layout associabili devono essere usati solo quando la raccolta di elementi da visualizzare è di piccole dimensioni e lo scorrimento e la selezione non sono necessari. Sebbene sia possibile fornire lo scorrimento eseguendo il wrapping di un layout associabile in un [`ScrollView`](xref:Xamarin.Forms.ScrollView), questa operazione non è consigliata perché i layout associabili non hanno la virtualizzazione dell'interfaccia utente. Quando è necessario lo scorrimento, è necessario utilizzare una visualizzazione scorrevole che includa la virtualizzazione [`CollectionView`](xref:Xamarin.Forms.CollectionView)dell'interfaccia utente, ad esempio [`ListView`](xref:Xamarin.Forms.ListView) o. L'impossibilità di osservare questa raccomandazione può causare problemi di prestazioni.

> [!IMPORTANT]
>Sebbene sia tecnicamente possibile aggiungere un layout associabile a qualsiasi [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe di layout che deriva dalla classe, non è sempre praticabile, in particolare per le [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)classi, [`Grid`](xref:Xamarin.Forms.Grid)e [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Si consideri, ad esempio, lo scenario in cui si desidera visualizzare una [`Grid`](xref:Xamarin.Forms.Grid) raccolta di dati in un oggetto utilizzando un layout associabile, in cui ogni elemento della raccolta è un oggetto che contiene più proprietà. Ogni riga in `Grid` deve visualizzare un oggetto della raccolta, con ogni colonna `Grid` in che visualizza una delle proprietà dell'oggetto. Poiché il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) per il layout associabile può contenere solo un singolo oggetto, è necessario che l'oggetto sia una classe layout contenente più visualizzazioni che ognuna Visualizza una delle proprietà dell'oggetto in una colonna specifica. `Grid` Sebbene questo scenario possa essere realizzato con layout associabili, il `Grid` `Grid` risultato è un elemento padre che contiene un `Grid` elemento figlio per ogni elemento nella raccolta associata, che è un utilizzo estremamente inefficiente e problematico del layout.

## <a name="populating-a-bindable-layout-with-data"></a>Popolamento di un layout associabile con dati

Un layout associabile viene popolato con i dati `ItemsSource` impostando la relativa proprietà su `IEnumerable`qualsiasi raccolta che implementa e associando [`Layout<T>`](xref:Xamarin.Forms.Layout`1)tale layout a una classe derivata da:

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

Il codice C# equivalente è il seguente:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Quando la `BindableLayout.ItemsSource` proprietà associata è impostata su un layout, ma la `BindableLayout.ItemTemplate` proprietà associata non è impostata `IEnumerable` , ogni elemento della raccolta verrà `BindableLayout` visualizzato da un [`Label`](xref:Xamarin.Forms.Label) oggetto creato dalla classe.

## <a name="defining-item-appearance"></a>Definizione dell'aspetto dell'elemento

L'aspetto di ogni elemento nel layout associabile può essere definito impostando la `BindableLayout.ItemTemplate` proprietà associata su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)oggetto:

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding User.TopFollowers}"
             Orientation="Horizontal"
             ...>
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CircleImage Source="{Binding}"
                                  Aspect="AspectFill"
                                  WidthRequest="44"
                                  HeightRequest="44"
                                  ... />
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

Il codice C# equivalente è il seguente:

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

In questo esempio, ogni elemento della `TopFollowers` raccolta verrà visualizzato da una `CircleImage` visualizzazione definita in [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

![Layout associabile a un oggetto DataTemplate](bindable-layouts-images/top-followers.png "Layout associabile con un modello di dati")

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choosing-item-appearance-at-runtime"></a>Scelta dell'aspetto dell'elemento in fase di esecuzione

È possibile scegliere l'aspetto di ogni elemento nel layout associabile in fase di esecuzione, in base al valore dell'elemento, `BindableLayout.ItemTemplateSelector` impostando la proprietà [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)associata su un oggetto:

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

Il codice C# equivalente è il seguente:

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

L' [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) oggetto usato nell'applicazione di esempio è illustrato nell'esempio seguente:

```csharp
public class TechItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinFormsTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return (string)item == "Xamarin.Forms" ? XamarinFormsTemplate : DefaultTemplate;
    }
}
```

La `TechItemTemplateSelector` classe definisce `DefaultTemplate` le `XamarinFormsTemplate` proprietà [e`DataTemplate`](xref:Xamarin.Forms.DataTemplate) impostate su modelli di dati diversi. Il `OnSelectTemplate` metodo`XamarinFormsTemplate`restituisce, che visualizza un elemento in rosso scuro con un cuore accanto, quando l'elemento è uguale a "Novell. Forms". Quando l'elemento non è uguale a "Novell. Forms" `OnSelectTemplate` , il metodo `DefaultTemplate`restituisce, che visualizza un elemento usando il colore predefinito di [`Label`](xref:Xamarin.Forms.Label)un oggetto:

![Layout associabile con DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Layout associabile con un selettore di modello di dati")

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Creating a Novell. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Collegamenti correlati

- [Demo sul layout associabile (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md)
- [Modelli di dati Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creazione di un DataTemplateSelector Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
