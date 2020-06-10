---
title: " Xamarin.Forms binding relativi" Descrizione: "in questo articolo viene illustrato come creare binding relativi usando l'estensione di markup RelativeSource per impostare l'origine di associazione in relazione alla posizione della destinazione del binding".
ms. prod: Novell MS. AssetID: CC64BB1D-8303-46B1-94B6-4EF2F20317A8 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 12/04/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-relative-bindings"></a>Xamarin.FormsAssociazioni relative

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Le associazioni relative offrono la possibilità di impostare l'origine di associazione in relazione alla posizione della destinazione del binding. Vengono creati con l' `RelativeSource` estensione di markup e impostati come `Source` proprietà di un'espressione di associazione.

L' `RelativeSource` estensione di markup è supportata dalla `RelativeSourceExtension` classe, che definisce le proprietà seguenti:

- `Mode`, di tipo `RelativeBindingSourceMode` , descrive il percorso dell'origine di associazione in relazione alla posizione della destinazione del binding.
- `AncestorLevel`, di tipo `int` , un livello predecessore facoltativo da cercare, quando la `Mode` proprietà è `FindAncestor` . Un `AncestorLevel` di `n` Ignora `n-1` le istanze di `AncestorType` .
- `AncestorType`, di tipo `Type` , il tipo di predecessore da cercare, quando la `Mode` proprietà è `FindAncestor` .

> [!NOTE]
> Il parser XAML consente `RelativeSourceExtension` di abbreviare la classe come `RelativeSource` .

La `Mode` proprietà deve essere impostata su uno dei `RelativeBindingSourceMode` membri dell'enumerazione:

- `TemplatedParent`indica l'elemento a cui viene applicato il modello, in cui è presente l'elemento associato. Per altre informazioni, vedere [eseguire l'associazione a un elemento padre basato su modelli](#bind-to-a-templated-parent).
- `Self`indica l'elemento su cui viene impostata l'associazione, consentendo di associare una proprietà di tale elemento a un'altra proprietà sullo stesso elemento. Per altre informazioni, vedere [eseguire l'associazione a se stessi](#bind-to-self).
- `FindAncestor`indica il predecessore nella struttura ad albero visuale dell'elemento associato. Questa modalità deve essere utilizzata per eseguire il binding a un controllo predecessore rappresentato dalla `AncestorType` Proprietà. Per altre informazioni, vedere [eseguire l'associazione a un predecessore](#bind-to-an-ancestor).
- `FindAncestorBindingContext`indica l'oggetto `BindingContext` del predecessore nella struttura ad albero visuale dell'elemento associato. Questa modalità deve essere utilizzata per l'associazione all'oggetto `BindingContext` di un predecessore rappresentato dalla `AncestorType` Proprietà. Per altre informazioni, vedere [eseguire l'associazione a un predecessore](#bind-to-an-ancestor).

La `Mode` proprietà è la proprietà Content della `RelativeSourceExtension` classe. Pertanto, per le espressioni di markup XAML espresse con parentesi graffe, è possibile eliminare la `Mode=` parte dell'espressione.

Per altre informazioni sulle Xamarin.Forms estensioni di markup, vedere [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md).

## <a name="bind-to-self"></a>Associa a se stesso

La `Self` modalità di associazione relativa viene utilizzata per associare una proprietà di un elemento a un'altra proprietà nello stesso elemento:

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

In questo esempio, [`BoxView`](xref:Xamarin.Forms.BoxView) imposta la `WidthRequest` proprietà su una dimensione fissa e la proprietà viene `HeightRequest` associata alla `WidthRequest` Proprietà. Pertanto, entrambe le proprietà sono uguali e pertanto viene disegnato un quadrato:

[![Screenshot di un'associazione relativa in modalità self-service, in iOS e Android](relative-bindings-images/self-relative-binding.png "Modalità di associazione relativa automatica")](relative-bindings-images/self-relative-binding-large.png#lightbox "Modalità di associazione relativa automatica")

> [!IMPORTANT]
> Quando si associa una proprietà di un elemento a un'altra proprietà nello stesso elemento, le proprietà devono essere dello stesso tipo. In alternativa, è possibile specificare un convertitore nell'associazione per convertire il valore.

Un uso comune di questa modalità di associazione è impostare un oggetto `BindingContext` su una proprietà su se stesso. Il codice seguente illustra un esempio di questo:

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

In questo esempio, la `BindingContext` proprietà della pagina viene impostata sulla `DefaultViewModel` proprietà di se stessa. Questa proprietà viene definita nel file code-behind per la pagina e fornisce un'istanza ViewModel. L'oggetto viene [`ListView`](xref:Xamarin.Forms.ListView) associato alla `Employees` proprietà dell'elemento ViewModel.

## <a name="bind-to-an-ancestor"></a>Associa a un predecessore

Le `FindAncestor` `FindAncestorBindingContext` modalità di associazione e relative vengono utilizzate per l'associazione agli elementi padre, di un determinato tipo, nella struttura ad albero visuale. La `FindAncestor` modalità viene utilizzata per eseguire l'associazione a un elemento padre che deriva dal [`Element`](xref:Xamarin.Forms.Element) tipo. La `FindAncestorBindingContext` modalità viene utilizzata per eseguire l'associazione all'oggetto `BindingContext` di un elemento padre.

> [!WARNING]
> La `AncestorType` proprietà deve essere impostata su un oggetto `Type` quando si utilizzano le `FindAncestor` modalità di `FindAncestorBindingContext` associazione e relative; in caso contrario, `XamlParseException` viene generata un'eccezione.

Se la `Mode` proprietà non è impostata in modo esplicito, impostando la `AncestorType` proprietà su un tipo che deriva da, [`Element`](xref:Xamarin.Forms.Element) la proprietà viene impostata in modo implicito `Mode` su `FindAncestor` . In modo analogo, impostando la `AncestorType` proprietà su un tipo che non deriva da, `Element` la proprietà viene impostata in modo implicito `Mode` su `FindAncestorBindingContext` .

> [!NOTE]
> Le associazioni relative che usano la `FindAncestorBindingContext` modalità verranno riapplicate quando viene `BindingContext` modificato l'oggetto di qualsiasi predecessore.

Il codice XAML seguente mostra un esempio in cui la `Mode` proprietà verrà impostata in modo implicito su `FindAncestorBindingContext` :

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

In questo esempio, la `BindingContext` proprietà della pagina viene impostata sulla `DefaultViewModel` proprietà di se stessa. Questa proprietà viene definita nel file code-behind per la pagina e fornisce un'istanza ViewModel. L'oggetto viene [`ListView`](xref:Xamarin.Forms.ListView) associato alla `Employees` proprietà dell'elemento ViewModel. [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), Che definisce l'aspetto di ogni elemento in `ListView` , contiene un oggetto [`Button`](xref:Xamarin.Forms.Button) . La proprietà del pulsante `Command` è associata a `DeleteEmployeeCommand` nel ViewModel del relativo elemento padre. Toccare un oggetto `Button` Elimina un dipendente:

[![Screenshot di un'associazione relativa in modalità FindAncestor, in iOS e Android](relative-bindings-images/findancestor-relative-binding.png "Modalità di associazione relativa di FindAncestor")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "Modalità di associazione relativa di FindAncestor")

Inoltre, la proprietà facoltativa `AncestorLevel` può essere utile per evitare ambiguità nella ricerca di predecessori negli scenari in cui è probabilmente presente più di un predecessore di quel tipo nella struttura ad albero visuale:

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

In questo esempio, la `Label.Text` proprietà viene associata alla `Text` proprietà del secondo rilevato nel [`Entry`](xref:Xamarin.Forms.Entry) percorso verso l'alto, a partire dall'elemento di destinazione dell'associazione.

> [!NOTE]
> La `AncestorLevel` proprietà deve essere impostata su 1 per trovare il predecessore più vicino all'elemento di destinazione del binding.

## <a name="bind-to-a-templated-parent"></a>Associa a un elemento padre basato su modelli

La `TemplatedParent` modalità di associazione relativa viene utilizzata per eseguire l'associazione dall'interno di un modello di controllo all'istanza dell'oggetto di runtime a cui è applicato il modello (noto come elemento padre basato su modelli). Questa modalità è applicabile solo se l'associazione relativa si trova all'interno di un modello di controllo ed è simile all'impostazione di un oggetto `TemplateBinding` .

Il codice XAML seguente mostra un esempio della `TemplatedParent` modalità di associazione relativa:

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

In questo esempio,, [`Frame`](xref:Xamarin.Forms.Frame) che è l'elemento radice di `ControlTemplate` , è `BindingContext` impostato sull'istanza dell'oggetto di runtime a cui viene applicato il modello. Pertanto, `Frame` e i relativi elementi figlio risolvono le espressioni di associazione in base alle proprietà di ogni `CardView` oggetto:

[![Screenshot di un'associazione relativa in modalità TemplatedParent, in iOS e Android](relative-bindings-images/templatedparent-relative-binding.png "Modalità di associazione relativa di TemplatedParent")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "Modalità di associazione relativa di TemplatedParent")

Per ulteriori informazioni sui modelli di controllo, vedere [ Xamarin.Forms modelli di controllo](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.FormsModelli di controllo](~/xamarin-forms/app-fundamentals/templates/control-template.md)
