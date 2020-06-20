---
title: Layout associabili inXamarin.Forms
description: I layout associabili consentono alle classi di layout di generare il contenuto mediante associazione a una raccolta di elementi, con l'opzione per impostare l'aspetto di ogni elemento con un oggetto DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/09/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9d0497c0c0593b54f69bac84307976c4050e9c95
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138242"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Layout associabili inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

I layout associabili consentono a qualsiasi classe di layout che deriva dalla [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe di generare il contenuto mediante associazione a una raccolta di elementi, con l'opzione per impostare l'aspetto di ogni elemento con un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . I layout associabili sono forniti dalla `BindableLayout` classe, che espone le proprietà associate seguenti:

- `ItemsSource`: specifica la raccolta di `IEnumerable` elementi che devono essere visualizzati dal layout.
- `ItemTemplate`: specifica l'oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) da applicare a ogni elemento nella raccolta di elementi visualizzati dal layout.
- `ItemTemplateSelector`: specifica l'oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) che verrà usato per scegliere un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto per un elemento in fase di esecuzione.

> [!NOTE]
> La `ItemTemplate` proprietà ha la precedenza quando `ItemTemplate` vengono impostate entrambe le `ItemTemplateSelector` proprietà e.

Inoltre, la `BindableLayout` classe espone le proprietà associabili seguenti:

- `EmptyView`: specifica la `string` vista o che verrà visualizzata quando la `ItemsSource` proprietà è o `null` quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Il valore predefinito è `null`.
- `EmptyViewTemplate`: specifica l'oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che verrà visualizzato quando la `ItemsSource` proprietà è o `null` quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Il valore predefinito è `null`.

> [!NOTE]
> La `EmptyViewTemplate` proprietà ha la precedenza quando `EmptyView` vengono impostate entrambe le `EmptyViewTemplate` proprietà e.

Tutte queste proprietà possono essere collegate alle [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) classi,, [`Grid`](xref:Xamarin.Forms.Grid) , [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) e [`StackLayout`](xref:Xamarin.Forms.StackLayout) , che derivano tutte dalla [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe.

La `Layout<T>` classe espone una [`Children`](xref:Xamarin.Forms.Layout`1.Children) raccolta alla quale vengono aggiunti gli elementi figlio di un layout. Quando la `BinableLayout.ItemsSource` proprietà è impostata su una raccolta di elementi e collegata a una [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe derivata da, ogni elemento della raccolta viene aggiunto alla `Layout<T>.Children` raccolta per la visualizzazione da parte del layout. La `Layout<T>` classe derivata da aggiorna quindi le visualizzazioni figlio quando viene modificata la raccolta sottostante. Per ulteriori informazioni sul Xamarin.Forms ciclo di layout, vedere [creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md).

I layout associabili devono essere usati solo quando la raccolta di elementi da visualizzare è di piccole dimensioni e lo scorrimento e la selezione non sono necessari. Sebbene sia possibile fornire lo scorrimento eseguendo il wrapping di un layout associabile in un [`ScrollView`](xref:Xamarin.Forms.ScrollView) , questa operazione non è consigliata perché i layout associabili non hanno la virtualizzazione dell'interfaccia utente. Quando è necessario lo scorrimento, è necessario utilizzare una visualizzazione scorrevole che includa la virtualizzazione dell'interfaccia utente, ad esempio [`ListView`](xref:Xamarin.Forms.ListView) o [`CollectionView`](xref:Xamarin.Forms.CollectionView) . L'impossibilità di osservare questa raccomandazione può causare problemi di prestazioni.

> [!IMPORTANT]
> Sebbene sia tecnicamente possibile aggiungere un layout associabile a qualsiasi classe di layout che deriva dalla [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe, non è sempre praticabile, in particolare per le [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) [`Grid`](xref:Xamarin.Forms.Grid) classi, e [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Si consideri, ad esempio, lo scenario in cui si desidera visualizzare una raccolta di dati in un [`Grid`](xref:Xamarin.Forms.Grid) oggetto utilizzando un layout associabile, in cui ogni elemento della raccolta è un oggetto che contiene più proprietà. Ogni riga in `Grid` deve visualizzare un oggetto della raccolta, con ogni colonna in che `Grid` Visualizza una delle proprietà dell'oggetto. Poiché il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) per il layout associabile può contenere solo un singolo oggetto, è necessario che l'oggetto sia una classe layout contenente più visualizzazioni che ognuna Visualizza una delle proprietà dell'oggetto in una `Grid` colonna specifica. Sebbene questo scenario possa essere realizzato con layout associabili, il risultato è un elemento padre che `Grid` contiene un `Grid` elemento figlio per ogni elemento nella raccolta associata, che è un utilizzo estremamente inefficiente e problematico del `Grid` layout.

## <a name="populate-a-bindable-layout-with-data"></a>Popolamento di un layout associabile con dati

Un layout associabile viene popolato con i dati impostando la relativa `ItemsSource` proprietà su qualsiasi raccolta che implementa `IEnumerable` e associando tale layout a una [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe derivata da:

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

Il codice C# equivalente è il seguente:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Quando la `BindableLayout.ItemsSource` proprietà associata è impostata su un layout, ma la `BindableLayout.ItemTemplate` proprietà associata non è impostata, ogni elemento della `IEnumerable` raccolta verrà visualizzato da un oggetto [`Label`](xref:Xamarin.Forms.Label) creato dalla `BindableLayout` classe.

## <a name="define-item-appearance"></a>Definisci aspetto elemento

L'aspetto di ogni elemento nel layout associabile può essere definito impostando la `BindableLayout.ItemTemplate` proprietà associata su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

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

In questo esempio, ogni elemento della `TopFollowers` raccolta verrà visualizzato da una `CircleImage` visualizzazione definita in [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

![Layout associabile a un oggetto DataTemplate](bindable-layouts-images/top-followers.png "Layout associabile con un modello di dati")

Per ulteriori informazioni sui modelli di dati, vedere [ Xamarin.Forms modelli di dati](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Scegli aspetto elemento in fase di esecuzione

È possibile scegliere l'aspetto di ogni elemento nel layout associabile in fase di esecuzione, in base al valore dell'elemento, impostando la `BindableLayout.ItemTemplateSelector` proprietà associata su un oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

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

L'oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) usato nell'applicazione di esempio è illustrato nell'esempio seguente:

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

La `TechItemTemplateSelector` classe definisce `DefaultTemplate` le `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà e impostate su modelli di dati diversi. Il `OnSelectTemplate` metodo restituisce `XamarinFormsTemplate` , che visualizza un elemento in rosso scuro con un cuore accanto, quando l'elemento è uguale a " Xamarin.Forms ". Quando l'elemento non è uguale a " Xamarin.Forms ", il `OnSelectTemplate` metodo restituisce `DefaultTemplate` , che visualizza un elemento usando il colore predefinito di un oggetto [`Label`](xref:Xamarin.Forms.Label) :

![Layout associabile con DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Layout associabile con un selettore di modello di dati")

Per ulteriori informazioni sui selettori di modelli di dati, vedere [creazione di un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Visualizza una stringa quando i dati non sono disponibili

La `EmptyView` proprietà può essere impostata su una stringa che verrà visualizzata da un oggetto [`Label`](xref:Xamarin.Forms.Label) quando la `ItemsSource` proprietà è o `null` quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Il codice XAML seguente mostra un esempio di questo scenario:

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}"
             BindableLayout.EmptyView="No achievements">
    ...
</StackLayout>
```

Il risultato è che, quando la raccolta associata ai dati è `null` , viene visualizzata la stringa impostata come `EmptyView` valore della proprietà:

[![Screenshot della visualizzazione vuota di una stringa di layout associabile, in iOS e Android](bindable-layouts-images/emptyview-string.png "Visualizzazione vuota della stringa di layout associabile")](bindable-layouts-images/emptyview-string-large.png#lightbox "Visualizzazione vuota della stringa di layout associabile")

## <a name="display-views-when-data-is-unavailable"></a>Visualizzazione delle visualizzazioni quando i dati non sono disponibili

La `EmptyView` proprietà può essere impostata su una visualizzazione, che verrà visualizzata quando la `ItemsSource` proprietà è o `null` quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Può trattarsi di una vista singola o di una vista che contiene più visualizzazioni figlio. Nell'esempio di codice XAML riportato di seguito viene illustrata la `EmptyView` proprietà impostata su una vista che contiene più visualizzazioni figlio:

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

Il risultato è che, quando la raccolta associata ai dati è `null` , [`StackLayout`](xref:Xamarin.Forms.StackLayout) vengono visualizzate le e le relative visualizzazioni figlio.

[![Screenshot di una visualizzazione vuota del layout associabile con più visualizzazioni, in iOS e Android](bindable-layouts-images/emptyview-views.png "Visualizzazione vuota layout associabile")](bindable-layouts-images/emptyview-views-large.png#lightbox "Visualizzazione vuota layout associabile")

Analogamente, `EmptyViewTemplate` può essere impostato su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , che verrà visualizzato quando la `ItemsSource` proprietà è o `null` quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. `DataTemplate`Può contenere una sola visualizzazione o una vista che contiene più visualizzazioni figlio. Inoltre, l'oggetto `BindingContext` di `EmptyViewTemplate` verrà ereditato dall'oggetto `BindingContext` di `BindableLayout` . Nell'esempio di codice XAML riportato di seguito viene illustrata la `EmptyViewTemplate` proprietà impostata su un oggetto `DataTemplate` che contiene una singola visualizzazione:

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

Il risultato è che, quando la raccolta associata ai dati è `null` , viene [`Label`](xref:Xamarin.Forms.Label) visualizzato il in [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

[![Screenshot di un modello di visualizzazione vuoto di layout associabile, in iOS e Android](bindable-layouts-images/emptyviewtemplate.png "Modello di visualizzazione vuota layout associabile")](bindable-layouts-images/emptyviewtemplate-large.png#lightbox "Modello di visualizzazione vuota layout associabile")

> [!NOTE]
> `EmptyViewTemplate`Impossibile impostare la proprietà tramite un oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) .

## <a name="choose-an-emptyview-at-runtime"></a>Scegliere un EmptyView in fase di esecuzione

Le visualizzazioni che verranno visualizzate come `EmptyView` dati quando non sono disponibili, possono essere definite come [`ContentView`](xref:Xamarin.Forms.ContentView) oggetti in un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . La `EmptyView` proprietà può quindi essere impostata su un oggetto specifico `ContentView` , in base a una logica di business, in fase di esecuzione. Il codice XAML seguente mostra un esempio di questo scenario:

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

Il codice XAML definisce due [`ContentView`](xref:Xamarin.Forms.ContentView) oggetti a livello di pagina [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , con l' [`Switch`](xref:Xamarin.Forms.Switch) oggetto che controlla quale `ContentView` oggetto verrà impostato come `EmptyView` valore della proprietà. Quando l'oggetto `Switch` viene attivato o disattivato, il `OnEmptyViewSwitchToggled` gestore dell'evento esegue il `ToggleEmptyView` Metodo:

```csharp
void ToggleEmptyView(bool isToggled)
{
    object view = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
    BindableLayout.SetEmptyView(stackLayout, view);
}
```

Il `ToggleEmptyView` metodo imposta la `EmptyView` proprietà dell' `stackLayout` oggetto su uno dei due [`ContentView`](xref:Xamarin.Forms.ContentView) oggetti archiviati in [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , in base al valore della [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) Proprietà. Quindi, quando la raccolta associata ai dati è `null` , `ContentView` viene visualizzato l'oggetto impostato come `EmptyView` proprietà:

[![Screenshot della scelta di una vista vuota in fase di esecuzione, in iOS e Android](bindable-layouts-images/emptyview-runtime.png "Scelta del runtime di visualizzazione vuota del layout associabile")](bindable-layouts-images/emptyview-runtime-large.png#lightbox "Scelta del runtime di visualizzazione vuota del layout associabile")

## <a name="related-links"></a>Collegamenti correlati

- [Demo sul layout associabile (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin.FormsModelli di dati](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creazione di un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
