---
title: Binding relativi di Novell. Forms
description: In questo articolo viene illustrato come creare associazioni relative usando l'estensione di markup RelativeSource per impostare l'origine di associazione in relazione alla posizione della destinazione del binding.
ms.prod: xamarin
ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
ms.openlocfilehash: 08026cd3f3ef7503a92f6c78f1e3e27ad3642d09
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959133"
---
# <a name="xamarinforms-relative-bindings"></a>Binding relativi di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Le associazioni relative offrono la possibilità di impostare l'origine di associazione in relazione alla posizione della destinazione del binding. Vengono creati con l'estensione di markup `RelativeSource` e impostati come proprietà `Source` di un'espressione di associazione.

L'estensione di markup `RelativeSource` è supportata dalla classe `RelativeSourceExtension`, che definisce le proprietà seguenti:

- `Mode`, di tipo `RelativeBindingSourceMode`, descrive il percorso dell'origine di associazione in relazione alla posizione della destinazione del binding.
- `AncestorLevel`, di tipo `int`, un livello predecessore facoltativo da cercare, quando la proprietà `Mode` è `FindAncestor`.
- `AncestorType`, di tipo `Type`, il tipo di predecessore da cercare, quando la proprietà `Mode` è `FindAncestor`.

> [!NOTE]
> Il parser XAML consente di abbreviare la classe `RelativeSourceExtension` come `RelativeSource`.

È necessario impostare la proprietà `Mode` su uno dei membri dell'enumerazione `RelativeBindingSourceMode`:

- `TemplatedParent` indica l'elemento a cui viene applicato il modello, in cui è presente l'elemento associato. Per altre informazioni, vedere [eseguire l'associazione a un elemento padre basato su modelli](#bind-to-a-templated-parent).
- `Self` indica l'elemento su cui viene impostata l'associazione, consentendo di associare una proprietà di tale elemento a un'altra proprietà sullo stesso elemento. Per altre informazioni, vedere [eseguire l'associazione a se stessi](#bind-to-self).
- `FindAncestor` indica il predecessore nella struttura ad albero visuale dell'elemento associato. Questa modalità deve essere utilizzata per eseguire il binding a un controllo predecessore rappresentato dalla proprietà `AncestorType`. Per altre informazioni, vedere [eseguire l'associazione a un predecessore](#bind-to-an-ancestor).
- `FindAncestorBindingContext` indica l'`BindingContext` del predecessore nella struttura ad albero visuale dell'elemento associato. Questa modalità deve essere utilizzata per eseguire il binding alla `BindingContext` di un predecessore rappresentato dalla proprietà `AncestorType`. Per altre informazioni, vedere [eseguire l'associazione a un predecessore](#bind-to-an-ancestor).

La proprietà `Mode` è la proprietà Content della classe `RelativeSourceExtension`. Pertanto, per le espressioni di markup XAML espresse con parentesi graffe, è possibile eliminare la `Mode=` parte dell'espressione.

Per altre informazioni sulle estensioni di markup Novell. Forms, vedere [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md).

## <a name="bind-to-self"></a>Associa a se stesso

La `Self` modalità di associazione relativa viene utilizzata per associare una proprietà di un elemento a un'altra proprietà nello stesso elemento:

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

In questo esempio, il [`BoxView`](xref:Xamarin.Forms.BoxView) imposta la relativa proprietà `WidthRequest` su una dimensione fissa e la proprietà `HeightRequest` viene associata alla proprietà `WidthRequest`. Pertanto, entrambe le proprietà sono uguali e pertanto viene disegnato un quadrato:

[![Screenshot di un'associazione relativa in modalità self-service, in iOS e Android](relative-bindings-images/self-relative-binding.png "Modalità di associazione relativa automatica")](relative-bindings-images/self-relative-binding-large.png#lightbox "Modalità di associazione relativa automatica")

> [!IMPORTANT]
> Quando si associa una proprietà di un elemento a un'altra proprietà nello stesso elemento, le proprietà devono essere dello stesso tipo. In alternativa, è possibile specificare un convertitore nell'associazione per convertire il valore.

Un uso comune di questa modalità di associazione è l'impostazione di un `BindingContext` di un oggetto su una proprietà. Il codice seguente illustra un esempio di questo:

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

In questo esempio, il `BindingContext` della pagina viene impostato sulla proprietà `DefaultViewModel` di se stessa. Questa proprietà viene definita nel file code-behind per la pagina e fornisce un'istanza ViewModel. Il [`ListView`](xref:Xamarin.Forms.ListView) viene associato alla proprietà `Employees` dell'elemento ViewModel.

## <a name="bind-to-an-ancestor"></a>Associa a un predecessore

Le modalità di associazione relative a `FindAncestor` e `FindAncestorBindingContext` vengono utilizzate per l'associazione agli elementi padre, di un determinato tipo, nella struttura ad albero visuale. La modalità `FindAncestor` viene utilizzata per eseguire l'associazione a un elemento padre che deriva dal tipo di [`Element`](xref:Xamarin.Forms.Element) . La modalità `FindAncestorBindingContext` viene utilizzata per eseguire il binding alla `BindingContext` di un elemento padre.

> [!WARNING]
> È necessario impostare la proprietà `AncestorType` su un `Type` quando si utilizzano le modalità di associazione `FindAncestor` e `FindAncestorBindingContext` relative, in caso contrario viene generata un'`XamlParseException`.

Se la proprietà `Mode` non è impostata in modo esplicito, impostando la proprietà `AncestorType` su un tipo che deriva da [`Element`](xref:Xamarin.Forms.Element) la proprietà `Mode` viene impostata in modo implicito su `FindAncestor`. Analogamente, se si imposta la proprietà `AncestorType` su un tipo che non deriva da `Element`, la proprietà `Mode` viene impostata in modo implicito su `FindAncestorBindingContext`.

Il codice XAML seguente mostra un esempio in cui la proprietà `Mode` verrà impostata in modo implicito su `FindAncestorBindingContext`:

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

In questo esempio, il `BindingContext` della pagina viene impostato sulla proprietà `DefaultViewModel` di se stessa. Questa proprietà viene definita nel file code-behind per la pagina e fornisce un'istanza ViewModel. Il [`ListView`](xref:Xamarin.Forms.ListView) viene associato alla proprietà `Employees` dell'elemento ViewModel. Il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), che definisce l'aspetto di ogni elemento nella `ListView`, contiene un [`Button`](xref:Xamarin.Forms.Button). Il `Command` proprietà del pulsante è associato al `DeleteEmployeeCommand` nel ViewModel del relativo elemento padre. Toccando una `Button` viene eliminato un dipendente:

[![Screenshot di un'associazione relativa in modalità FindAncestor, in iOS e Android](relative-bindings-images/findancestor-relative-binding.png "Modalità di associazione relativa di FindAncestor")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "Modalità di associazione relativa di FindAncestor")

Inoltre, la proprietà facoltativa `AncestorLevel` consente di evitare ambiguità nella ricerca di predecessori negli scenari in cui è possibile che esistano più predecessori di quel tipo nella struttura ad albero visuale:

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

In questo esempio, la proprietà `Label.Text` viene associata alla proprietà `Text` della seconda [`Entry`](xref:Xamarin.Forms.Entry) rilevata nel percorso verso l'alto, a partire dall'elemento di destinazione dell'associazione.

> [!NOTE]
> Il `AncestorLevel` proprietà deve essere impostato su 1 per trovare il predecessore più vicino all'elemento di destinazione del binding.

## <a name="bind-to-a-templated-parent"></a>Associa a un elemento padre basato su modelli

La `TemplatedParent` modalità di associazione relativa viene utilizzata per eseguire l'associazione dall'interno di un modello di controllo all'istanza dell'oggetto di runtime a cui è applicato il modello (noto come elemento padre basato su modelli). Questa modalità è applicabile solo se l'associazione relativa si trova all'interno di un modello di controllo ed è simile all'impostazione di un `TemplateBinding`.

Il codice XAML seguente mostra un esempio della modalità di associazione `TemplatedParent` relativa:

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

In questo esempio, il [`Frame`](xref:Xamarin.Forms.Frame), che è l'elemento radice della `ControlTemplate`, ha la `BindingContext` impostata sull'istanza dell'oggetto di runtime a cui viene applicato il modello. Pertanto, il `Frame` e i relativi elementi figlio risolvono le espressioni di associazione in base alle proprietà di ogni oggetto `CardView`:

[![Screenshot di un'associazione relativa in modalità TemplatedParent, in iOS e Android](relative-bindings-images/templatedparent-relative-binding.png "Modalità di associazione relativa di TemplatedParent")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "Modalità di associazione relativa di TemplatedParent")

Per ulteriori informazioni sui modelli di controllo, vedere [Novell. Forms Control Templates](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Modelli di controllo Novell. Forms](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)
