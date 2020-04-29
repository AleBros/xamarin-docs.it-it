---
title: Layout associabili in Novell. Forms
description: I layout associabili consentono alle classi di layout di generare il contenuto mediante associazione a una raccolta di elementi, con l'opzione per impostare l'aspetto di ogni elemento con un oggetto DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/09/2020
ms.openlocfilehash: d084d910786c24a4b0333ecbc3e893cfe144d404
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517192"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Layout associabili in Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

I layout associabili consentono a qualsiasi classe di layout che deriva [`Layout<T>`](xref:Xamarin.Forms.Layout`1) dalla classe di generare il contenuto mediante associazione a una raccolta di elementi, con l'opzione per impostare l'aspetto di ogni elemento con [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)un oggetto. I `BindableLayout` layout associabili sono forniti dalla classe, che espone le proprietà associate seguenti:

- `ItemsSource`: specifica la raccolta di `IEnumerable` elementi che devono essere visualizzati dal layout.
- `ItemTemplate`: specifica l' [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto da applicare a ogni elemento nella raccolta di elementi visualizzati dal layout.
- `ItemTemplateSelector`: specifica l' [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) oggetto che verrà usato per scegliere un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto per un elemento in fase di esecuzione.

> [!NOTE]
> La `ItemTemplate` proprietà ha la precedenza quando vengono `ItemTemplate` impostate `ItemTemplateSelector` entrambe le proprietà e.

Inoltre, la `BindableLayout` classe espone le proprietà associabili seguenti:

- `EmptyView`: specifica la `string` `ItemsSource` vista o che verrà visualizzata quando la proprietà è `null`o quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Il valore predefinito è `null`.
- `EmptyViewTemplate`: specifica l' [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che verrà visualizzato quando la `ItemsSource` proprietà è `null`o quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Il valore predefinito è `null`.

> [!NOTE]
> La `EmptyViewTemplate` proprietà ha la precedenza quando vengono `EmptyView` impostate `EmptyViewTemplate` entrambe le proprietà e.

Tutte queste proprietà possono essere collegate alle [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)classi, [`FlexLayout`](xref:Xamarin.Forms.FlexLayout), [`Grid`](xref:Xamarin.Forms.Grid), [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)e [`StackLayout`](xref:Xamarin.Forms.StackLayout) , che derivano tutte dalla [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe.

La `Layout<T>` classe espone una [`Children`](xref:Xamarin.Forms.Layout`1.Children) raccolta alla quale vengono aggiunti gli elementi figlio di un layout. Quando la `BinableLayout.ItemsSource` proprietà è impostata su una raccolta di elementi e collegata a una [`Layout<T>`](xref:Xamarin.Forms.Layout`1)classe derivata da, ogni elemento della raccolta viene aggiunto alla `Layout<T>.Children` raccolta per la visualizzazione da parte del layout. La `Layout<T>`classe derivata da aggiorna quindi le visualizzazioni figlio quando viene modificata la raccolta sottostante. Per ulteriori informazioni sul ciclo di layout di Novell. Forms, vedere [creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md).

I layout associabili devono essere usati solo quando la raccolta di elementi da visualizzare è di piccole dimensioni e lo scorrimento e la selezione non sono necessari. Sebbene sia possibile fornire lo scorrimento eseguendo il wrapping di un layout associabile in un [`ScrollView`](xref:Xamarin.Forms.ScrollView), questa operazione non è consigliata perché i layout associabili non hanno la virtualizzazione dell'interfaccia utente. Quando è necessario lo scorrimento, è necessario utilizzare una visualizzazione scorrevole che includa la virtualizzazione dell'interfaccia utente, ad esempio [`ListView`](xref:Xamarin.Forms.ListView) o [`CollectionView`](xref:Xamarin.Forms.CollectionView). L'impossibilità di osservare questa raccomandazione può causare problemi di prestazioni.

> [!IMPORTANT]
> Sebbene sia [`Layout<T>`](xref:Xamarin.Forms.Layout`1) tecnicamente possibile aggiungere un layout associabile a qualsiasi classe di layout che deriva dalla classe, non è sempre praticabile, in particolare per le [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)classi, [`Grid`](xref:Xamarin.Forms.Grid)e. [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) Si consideri, ad esempio, lo scenario in cui si desidera visualizzare una [`Grid`](xref:Xamarin.Forms.Grid) raccolta di dati in un oggetto utilizzando un layout associabile, in cui ogni elemento della raccolta è un oggetto che contiene più proprietà. Ogni riga in `Grid` deve visualizzare un oggetto della raccolta, con ogni colonna in che `Grid` Visualizza una delle proprietà dell'oggetto. Poiché il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) per il layout associabile può contenere solo un singolo oggetto, è necessario che l'oggetto sia una classe layout contenente più visualizzazioni che ognuna Visualizza una delle proprietà dell'oggetto in una colonna specifica `Grid` . Sebbene questo scenario possa essere realizzato con layout associabili, il `Grid` `Grid` `Grid` risultato è un elemento padre che contiene un elemento figlio per ogni elemento nella raccolta associata, che è un utilizzo estremamente inefficiente e problematico del layout.

## <a name="populate-a-bindable-layout-with-data"></a>Popolamento di un layout associabile con dati

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

Quando la `BindableLayout.ItemsSource` proprietà associata è impostata su un layout, ma la `BindableLayout.ItemTemplate` proprietà associata non è impostata, ogni elemento della `IEnumerable` raccolta verrà visualizzato da un [`Label`](xref:Xamarin.Forms.Label) oggetto creato dalla `BindableLayout` classe.

## <a name="define-item-appearance"></a>Definisci aspetto elemento

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

In questo esempio, ogni elemento della `TopFollowers` raccolta verrà visualizzato da una `CircleImage` visualizzazione definita in: [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)

![Layout associabile a un oggetto DataTemplate](bindable-layouts-images/top-followers.png "Layout associabile con un modello di dati")

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Scegli aspetto elemento in fase di esecuzione

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

La `TechItemTemplateSelector` classe definisce `DefaultTemplate` le `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà e impostate su modelli di dati diversi. Il `OnSelectTemplate` metodo restituisce `XamarinFormsTemplate`, che visualizza un elemento in rosso scuro con un cuore accanto, quando l'elemento è uguale a "Novell. Forms". Quando l'elemento non è uguale a "Novell. Forms" `OnSelectTemplate` , il metodo `DefaultTemplate`restituisce, che visualizza un elemento usando il colore predefinito di [`Label`](xref:Xamarin.Forms.Label)un oggetto:

![Layout associabile con DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Layout associabile con un selettore di modello di dati")

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Creating a Novell. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Visualizza una stringa quando i dati non sono disponibili

La `EmptyView` proprietà può essere impostata su una stringa che verrà [`Label`](xref:Xamarin.Forms.Label) visualizzata da un oggetto quando la `ItemsSource` proprietà è `null`o quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Il codice XAML seguente mostra un esempio di questo scenario:

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}"
             BindableLayout.EmptyView="No achievements">
    ...
</StackLayout>
```

Il risultato è che, quando la raccolta associata ai `null`dati è, viene visualizzata la `EmptyView` stringa impostata come valore della proprietà:

[![Screenshot della visualizzazione vuota di una stringa di layout associabile, in iOS e Android](bindable-layouts-images/emptyview-string.png "Visualizzazione vuota della stringa di layout associabile")](bindable-layouts-images/emptyview-string-large.png#lightbox "Visualizzazione vuota della stringa di layout associabile")

## <a name="display-views-when-data-is-unavailable"></a>Visualizzazione delle visualizzazioni quando i dati non sono disponibili

La `EmptyView` `ItemsSource` proprietà può essere impostata su una visualizzazione, che verrà visualizzata quando la proprietà è `null`o quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Può trattarsi di una vista singola o di una vista che contiene più visualizzazioni figlio. Nell'esempio di codice XAML riportato `EmptyView` di seguito viene illustrata la proprietà impostata su una vista che contiene più visualizzazioni figlio:

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyView>
        <StackLayout>
            <Label Text="None."
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
            <Label Text="Try harder and return later?"
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
        </StackLayout>
    </BindableLayout.EmptyView>
    ...
</StackLayout>
```

Il risultato è che, quando la raccolta associata ai `null`dati è [`StackLayout`](xref:Xamarin.Forms.StackLayout) , vengono visualizzate le e le relative visualizzazioni figlio.

[![Screenshot di una visualizzazione vuota del layout associabile con più visualizzazioni, in iOS e Android](bindable-layouts-images/emptyview-views.png "Visualizzazione vuota layout associabile")](bindable-layouts-images/emptyview-views-large.png#lightbox "Visualizzazione vuota layout associabile")

Analogamente, `EmptyViewTemplate` può essere impostato su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)oggetto, che verrà visualizzato quando la `ItemsSource` proprietà è `null`o quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. `DataTemplate` Può contenere una sola visualizzazione o una vista che contiene più visualizzazioni figlio. Inoltre, l'oggetto `BindingContext` di `EmptyViewTemplate` verrà ereditato dall'oggetto `BindingContext` di. `BindableLayout` Nell'esempio di codice XAML riportato `EmptyViewTemplate` di seguito viene illustrata la proprietà impostata su un oggetto `DataTemplate` che contiene una singola visualizzazione:

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyViewTemplate>
        <DataTemplate>
            <Label Text="{Binding Source={x:Reference usernameLabel}, Path=Text, StringFormat='{0} has no achievements.'}" />
        </DataTemplate>
    </BindableLayout.EmptyViewTemplate>
    ...
</StackLayout>
```

Il risultato è che, quando la raccolta associata ai `null`dati è [`Label`](xref:Xamarin.Forms.Label) , viene [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) visualizzato il in:

[![Screenshot di un modello di visualizzazione vuoto di layout associabile, in iOS e Android](bindable-layouts-images/emptyviewtemplate.png "Modello di visualizzazione vuota layout associabile")](bindable-layouts-images/emptyviewtemplate-large.png#lightbox "Modello di visualizzazione vuota layout associabile")

> [!NOTE]
> Impossibile `EmptyViewTemplate` impostare la proprietà tramite un oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector).

## <a name="choose-an-emptyview-at-runtime"></a>Scegliere un EmptyView in fase di esecuzione

Le visualizzazioni che verranno visualizzate come dati `EmptyView` quando non sono disponibili, possono essere definite come [`ContentView`](xref:Xamarin.Forms.ContentView) oggetti in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)oggetto. La `EmptyView` proprietà può quindi essere impostata su un oggetto `ContentView`specifico, in base a una logica di business, in fase di esecuzione. Il codice XAML seguente mostra un esempio di questo scenario:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...    
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No achievements."
                       FontSize="14" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="None."
                       FontAttributes="Italic"
                       FontSize="14" />
                <Label Text="Try harder and return later?"
                       FontAttributes="Italic"
                       FontSize="14" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout>
        ...
        <Switch Toggled="OnEmptyViewSwitchToggled" />

        <StackLayout x:Name="stackLayout"
                     BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
            ...
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Il codice XAML definisce [`ContentView`](xref:Xamarin.Forms.ContentView) due oggetti a livello [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)di pagina, con l' [`Switch`](xref:Xamarin.Forms.Switch) oggetto che controlla `ContentView` quale oggetto verrà impostato come valore `EmptyView` della proprietà. Quando l' `Switch` oggetto viene attivato o disattivato `OnEmptyViewSwitchToggled` , il gestore dell'evento `ToggleEmptyView` esegue il metodo:

```csharp
void ToggleEmptyView(bool isToggled)
{
    object view = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
    BindableLayout.SetEmptyView(stackLayout, view);
}
```

Il `ToggleEmptyView` metodo imposta la `EmptyView` proprietà dell' `stackLayout` oggetto su uno dei due [`ContentView`](xref:Xamarin.Forms.ContentView) oggetti archiviati in [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), in base al valore della [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) proprietà. Quindi, quando la raccolta associata ai dati `null`è, `ContentView` viene visualizzato l'oggetto `EmptyView` impostato come proprietà:

[![Screenshot della scelta di una vista vuota in fase di esecuzione, in iOS e Android](bindable-layouts-images/emptyview-runtime.png "Scelta del runtime di visualizzazione vuota del layout associabile")](bindable-layouts-images/emptyview-runtime-large.png#lightbox "Scelta del runtime di visualizzazione vuota del layout associabile")

## <a name="related-links"></a>Link correlati

- [Demo sul layout associabile (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md)
- [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creazione di un DataTemplateSelector Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
