---
title: Associazioni relative di Xamarin.Forms
description: In questo articolo viene illustrato come creare associazioni relative utilizzando il RelativeSource estensione di markup per impostare l'origine di associazione relativa alla posizione della destinazione di associazione.
ms.prod: xamarin
ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 63ce27fc871da12eabb1baad568af167c860926f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75955806"
---
# <a name="xamarinforms-relative-bindings"></a>Associazioni relative di Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Le associazioni relative consentono di impostare l'origine di associazione rispetto alla posizione della destinazione di associazione. Vengono creati con `RelativeSource` l'estensione di `Source` markup e impostati come proprietà di un'espressione di associazione.

L'estensione `RelativeSource` di markup `RelativeSourceExtension` è supportata dalla classe , che definisce le proprietà seguenti:The markup extension is supported by the class, which defines the following properties:

- `Mode`, di `RelativeBindingSourceMode`tipo , descrive la posizione dell'origine di associazione rispetto alla posizione della destinazione di associazione.
- `AncestorLevel`, di `int`tipo , un livello predecessore `Mode` facoltativo da cercare, quando la proprietà è `FindAncestor`. Un `AncestorLevel` `n` di `n-1` ssaltazioni `AncestorType`istanze del file .
- `AncestorType`, di `Type`tipo , il tipo di `Mode` predecessore `FindAncestor`da cercare, quando la proprietà è .

> [!NOTE]
> Il parser XAML `RelativeSourceExtension` consente alla classe `RelativeSource`di essere abbreviata come .

La `Mode` proprietà deve essere impostata su uno dei membri dell'enumerazione: `RelativeBindingSourceMode`

- `TemplatedParent`indica l'elemento a cui è applicato il modello, in cui è presente l'elemento associato. Per ulteriori informazioni, consultate [Associare a un elemento padre basato su modelli.](#bind-to-a-templated-parent)
- `Self`indica l'elemento su cui viene impostata l'associazione, consentendo di associare una proprietà di tale elemento a un'altra proprietà nello stesso elemento. Per ulteriori informazioni, vedere [Eseguire l'associazione a se stessi](#bind-to-self).
- `FindAncestor`indica il predecessore nella struttura ad albero visuale dell'elemento associato. Questa modalità deve essere utilizzata per eseguire `AncestorType` l'associazione a un controllo predecessore rappresentato dalla proprietà . Per ulteriori informazioni, vedere [Associare a un predecessore.](#bind-to-an-ancestor)
- `FindAncestorBindingContext`indica il `BindingContext` valore del predecessore nella struttura ad albero visuale dell'elemento associato. Questa modalità deve essere `BindingContext` utilizzata per eseguire `AncestorType` l'associazione all'oggetto di un predecessore rappresentato dalla proprietà . Per ulteriori informazioni, vedere [Associare a un predecessore.](#bind-to-an-ancestor)

La `Mode` proprietà è la `RelativeSourceExtension` proprietà content della classe. Pertanto, per le espressioni di markup XAML espresse `Mode=` con parentesi graffe, è possibile eliminare la parte dell'espressione.

Per ulteriori informazioni sulle estensioni di markup Xamarin.Forms, consultate [Estensioni di markup XAML.](~/xamarin-forms/xaml/markup-extensions/index.md)

## <a name="bind-to-self"></a>Bind a self

La `Self` modalità di associazione relativa viene utilizzata per associare una proprietà di un elemento a un'altra proprietà nello stesso elemento:The relative binding mode is used bind a property of an element to another property on the same element:

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

In questo [`BoxView`](xref:Xamarin.Forms.BoxView) esempio, `WidthRequest` la proprietà imposta la `HeightRequest` proprietà su una `WidthRequest` dimensione fissa e la proprietà viene associata alla proprietà . Pertanto, entrambe le proprietà sono uguali e quindi viene disegnato un quadrato:Therefore, both properties are equal and so a square is drawn:

[![Screenshot di un binding relativo in modalità Self, in iOS e Android](relative-bindings-images/self-relative-binding.png "Modalità di rilegatura auto relativa")](relative-bindings-images/self-relative-binding-large.png#lightbox "Modalità di rilegatura auto relativa")

> [!IMPORTANT]
> Quando si associa una proprietà di un elemento a un'altra proprietà nello stesso elemento, le proprietà devono essere dello stesso tipo. In alternativa, è possibile specificare un convertitore nell'associazione per convertire il valore.

Un utilizzo comune di questa modalità `BindingContext` di associazione è impostato un oggetto su una proprietà su se stesso. Il codice seguente mostra un esempio di questo:The following code shows an example of this:

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

In questo esempio, l'oggetto `BindingContext` della `DefaultViewModel` pagina viene impostato sulla proprietà stessa. Questa proprietà è definita nel file code-behind per la pagina e fornisce un'istanza viewmodel. Il [`ListView`](xref:Xamarin.Forms.ListView) viene associato `Employees` alla proprietà del viewmodel.

## <a name="bind-to-an-ancestor"></a>Eseguire il binding a un predecessoreBind to an ancestor

Le `FindAncestor` `FindAncestorBindingContext` modalità di associazione e relative vengono utilizzate per l'associazione agli elementi padre, di un determinato tipo, nella struttura ad albero visuale. La `FindAncestor` modalità viene utilizzata per eseguire l'associazione a un elemento padre, che deriva dal [`Element`](xref:Xamarin.Forms.Element) tipo. La `FindAncestorBindingContext` modalità viene utilizzata `BindingContext` per eseguire l'associazione a un elemento padre.

> [!WARNING]
> La `AncestorType` proprietà deve essere `Type` impostata `FindAncestor` `FindAncestorBindingContext` su a quando `XamlParseException` si utilizzano le modalità di associazione e relative, in caso contrario viene generata un'eccezione .

Se `Mode` la proprietà non è impostata in modo esplicito, l'impostazione della `AncestorType` proprietà su un tipo che deriva [`Element`](xref:Xamarin.Forms.Element) da imposterà in modo implicito la `Mode` proprietà su `FindAncestor`. Analogamente, `AncestorType` l'impostazione della proprietà `Element` su un tipo `Mode` che `FindAncestorBindingContext`non deriva da imposterà in modo implicito la proprietà su .

> [!NOTE]
> Le associazioni relative `FindAncestorBindingContext` che utilizzano la `BindingContext` modalità verranno riapplicate quando cambiano il predecessore.

Il codice XAML seguente `Mode` mostra un esempio in `FindAncestorBindingContext`cui la proprietà verrà impostata in modo implicito su:

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Label Text="{Binding Fullname}"
                                   VerticalOptions="Center" />
                            <Button Text="Delete"
                                    Command="{Binding Source={RelativeSource AncestorType={x:Type local:PeopleViewModel}}, Path=DeleteEmployeeCommand}"
                                    CommandParameter="{Binding}"
                                    HorizontalOptions="EndAndExpand" />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

In questo esempio, l'oggetto `BindingContext` della `DefaultViewModel` pagina viene impostato sulla proprietà stessa. Questa proprietà è definita nel file code-behind per la pagina e fornisce un'istanza viewmodel. Il [`ListView`](xref:Xamarin.Forms.ListView) viene associato `Employees` alla proprietà del viewmodel. Il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), che definisce l'aspetto `ListView`di [`Button`](xref:Xamarin.Forms.Button)ogni elemento di , contiene un oggetto . La proprietà `Command` del pulsante `DeleteEmployeeCommand` è associata al nel modello di visualizzazione dell'elemento padre. Toccando `Button` un si elimina un dipendente:

[![Screenshot di un'associazione relativa della modalità FindAncestor, in iOS e Android](relative-bindings-images/findancestor-relative-binding.png "Modalità di rilegatura relativa FindAncestor")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "Modalità di rilegatura relativa FindAncestor")

Inoltre, la `AncestorLevel` proprietà facoltativa può aiutare a evitare ambiguità di ricerca predecessore in scenari in cui è possibile che sia presente più di un predecessore di quel tipo nella struttura ad albero visuale:In addition, the optional property can help disambiguate ancestor lookup in scenarios where there is possibly more than one predecessor of that type in the visual tree:

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

In questo esempio, la `Label.Text` proprietà `Text` viene associata alla proprietà del secondo [`Entry`](xref:Xamarin.Forms.Entry) che si è verificato nel percorso verso l'alto, a partire dall'elemento di destinazione dell'associazione.

> [!NOTE]
> La `AncestorLevel` proprietà deve essere impostata su 1 per trovare il predecessore più vicino all'elemento di destinazione dell'associazione.

## <a name="bind-to-a-templated-parent"></a>Eseguire l'associazione a un elemento padre basato su modelliBind to a templated parent

La `TemplatedParent` modalità di associazione relativa viene utilizzata per eseguire l'associazione dall'interno di un modello di controllo all'istanza dell'oggetto runtime a cui viene applicato il modello (noto come padre basato su modelli). Questa modalità è applicabile solo se l'associazione relativa si `TemplateBinding`trova all'interno di un modello di controllo ed è simile all'impostazione di un oggetto .

Il codice XAML seguente `TemplatedParent` mostra un esempio della modalità di associazione relativa:The following XAML shows an example of the relative binding mode:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                   BackgroundColor="{Binding CardColor}"
                   BorderColor="{Binding BorderColor}"
                   ...>
                <Grid>
                    ...
                    <Label Text="{Binding CardTitle}"
                           ... />
                    <BoxView BackgroundColor="{Binding BorderColor}"
                             ... />
                    <Label Text="{Binding CardDescription}"
                           ... />
                </Grid>
            </Frame>
        </ControlTemplate>
    </ContentPage.Resources>
    <StackLayout>        
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
    </StackLayout>
</ContentPage>
```

In questo esempio, l'oggetto [`Frame`](xref:Xamarin.Forms.Frame), `ControlTemplate`che è `BindingContext` l'elemento radice di , ha impostato l'istanza dell'oggetto runtime a cui viene applicato il modello. Pertanto, `Frame` gli elementi figlio e di essi `CardView` risolvono le espressioni di associazione rispetto alle proprietà di ogni oggetto:Therefore, the and its children resolve their binding expressions against the properties of each object:

[![Screenshot di un'associazione relativa in modalità TemplatedParent, in iOS e Android](relative-bindings-images/templatedparent-relative-binding.png "Modalità di associazione relativa TemplatedParent")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "Modalità di associazione relativa TemplatedParent")

Per ulteriori informazioni sui modelli di controllo, vedere Modelli di [controllo Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
- [Estensioni di markup XAMLXAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Modelli di controllo Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md)
