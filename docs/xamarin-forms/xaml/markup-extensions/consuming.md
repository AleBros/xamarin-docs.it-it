---
title: Utilizzo di estensioni di markup XAML
description: Questo articolo illustra come usare le estensioni di markup XAML di Novell. Forms per migliorare la potenza e la flessibilità di XAML consentendo l'impostazione degli attributi degli elementi da diverse origini.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/21/2020
ms.openlocfilehash: 7f46bc84543a1838241923ab91809bd01c706f44
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517101"
---
# <a name="consuming-xaml-markup-extensions"></a>Utilizzo di estensioni di markup XAML

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Le estensioni di markup XAML consentono di migliorare la potenza e la flessibilità di XAML consentendo l'impostazione degli attributi degli elementi da diverse origini. Diverse estensioni di markup XAML fanno parte della specifica XAML 2009. Questi vengono visualizzati nei file XAML con il prefisso `x` dello spazio dei nomi personalizzato e sono comunemente definiti con questo prefisso. Questo articolo illustra le estensioni di markup seguenti:

- [`x:Static`](#static): riferimento a proprietà statiche, campi o membri di enumerazione.
- [`x:Reference`](#reference): fanno riferimento agli elementi denominati nella pagina.
- [`x:Type`](#type): impostare un attributo su un `System.Type` oggetto.
- [`x:Array`](#array): costruire una matrice di oggetti di un determinato tipo.
- [`x:Null`](#null): impostare un attributo su un `null` valore.
- [`OnPlatform`](#onplatform): personalizzare l'aspetto dell'interfaccia utente in base alla piattaforma.
- [`OnIdiom`](#onidiom): personalizzare l'aspetto dell'interfaccia utente in base all'idioma del dispositivo in cui è in esecuzione l'applicazione.
- [`DataTemplate`](#datatemplate-markup-extension): converte un tipo in un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)oggetto.
- [`FontImage`](#fontimage-markup-extension): Visualizza un'icona del tipo di carattere in qualsiasi visualizzazione in `ImageSource`cui è possibile visualizzare un oggetto.
- [`OnAppTheme`](#onapptheme-markup-extension): usa una risorsa basata sul tema corrente del sistema.

Le estensioni di markup XAML aggiuntive sono state supportate in passato da altre implementazioni XAML e sono supportate anche da Novell. Forms. Queste informazioni sono descritte in modo più completo negli altri articoli:

- `StaticResource`-riferimento a oggetti da un dizionario risorse, come descritto nell'articolo [**dizionari risorse**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource`-rispondere alle modifiche apportate agli oggetti in un dizionario risorse, come descritto nell'articolo [**stili dinamici**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding`-stabilire un collegamento tra le proprietà di due oggetti, come descritto nell'articolo [**Data Binding**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding`: esegue data binding da un modello di controllo, come illustrato nell'articolo [**modelli di controllo Novell. Forms**](~/xamarin-forms/app-fundamentals/templates/control-template.md).
- `RelativeSource`: imposta l'origine dell'associazione in relazione alla posizione della destinazione del binding, come illustrato nell'articolo [associazioni relative](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

Il [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) layout usa l'estensione [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)di markup personalizzata. Questa estensione di markup è descritta nell'articolo [**sul relativelayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>estensione di markup x:Static

L' `x:Static` estensione di markup è supportata dalla [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension) classe. La classe dispone di una singola proprietà [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) denominata di `string` tipo impostata sul nome di una costante pubblica, di una proprietà statica, di un campo statico o di un membro di enumerazione.

Un modo comune per usare `x:Static` consiste nel definire prima una classe con alcune costanti o variabili statiche, ad esempio questa classe piccola `AppConstants` nel programma [**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) :

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

La pagina **demo di x:static** illustra diversi modi per usare `x:Static` l'estensione di markup. L'approccio più dettagliato crea un'istanza della `StaticExtension` classe tra `Label.FontSize` i tag dell'elemento proprietà:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.StaticDemoPage"
             Title="x:Static Demo">
    <StackLayout Margin="10, 0">
        <Label Text="Label No. 1">
            <Label.FontSize>
                <x:StaticExtension Member="local:AppConstants.NormalFontSize" />
            </Label.FontSize>
        </Label>

        ···

    </StackLayout>
</ContentPage>
```

Il parser XAML consente inoltre di `StaticExtension` abbreviare la classe come `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Questa operazione può essere ulteriormente semplificata, ma la modifica introduce una nuova sintassi: consiste nell'inserire la `StaticExtension` classe e l'impostazione del membro tra parentesi graffe. L'espressione risultante viene impostata direttamente sull' `FontSize` attributo:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Si noti *che non ci sono* virgolette tra parentesi graffe. La `Member` proprietà di `StaticExtension` non è più un attributo XML. È invece parte dell'espressione per l'estensione di markup.

Come è possibile `x:StaticExtension` abbreviare `x:Static` quando si usa come elemento oggetto, è anche possibile abbreviare l'espressione nell'espressione tra parentesi graffe:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

La `StaticExtension` classe dispone di `ContentProperty` un attributo che fa `Member`riferimento alla proprietà, che contrassegna questa proprietà come proprietà di contenuto predefinita della classe. Per le estensioni di markup XAML espresse con parentesi graffe, è possibile eliminare `Member=` la parte dell'espressione:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Si tratta della forma più comune dell'estensione `x:Static` di markup.

La pagina **Demo statica** contiene altri due esempi. Il tag radice del file XAML contiene una dichiarazione dello spazio dei nomi XML per `System` lo spazio dei nomi .NET:

```xaml
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

In questo modo `Label` è possibile impostare la dimensione del carattere sul campo `Math.PI`statico. Il risultato è un testo piuttosto piccolo, quindi `Scale` la proprietà è impostata `Math.E`su:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

Nell'esempio finale viene visualizzato `Device.RuntimePlatform` il valore. La `Environment.NewLine` proprietà statica viene utilizzata per inserire un carattere di nuova riga tra i due `Span` oggetti:

```xaml
<Label HorizontalTextAlignment="Center"
       FontSize="{x:Static local:AppConstants.NormalFontSize}">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Runtime Platform: " />
            <Span Text="{x:Static sys:Environment.NewLine}" />
            <Span Text="{x:Static Device.RuntimePlatform}" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Di seguito è riportato l'esempio in esecuzione:

[![Demo di x:Static](consuming-images/staticdemo-small.png "Demo di x:Static")](consuming-images/staticdemo-large.png#lightbox "Demo di x:Static")

<a name="reference" />

## <a name="xreference-markup-extension"></a>estensione di markup x:Reference

L' `x:Reference` estensione di markup è supportata dalla [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) classe. La classe dispone di una singola proprietà [`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) denominata di `string` tipo impostata sul nome di un elemento nella pagina a cui è stato assegnato un nome `x:Name`. Questa `Name` proprietà è la proprietà Content di `ReferenceExtension`, pertanto `Name=` non è obbligatorio quando `x:Reference` viene visualizzata tra parentesi graffe.

L' `x:Reference` estensione di markup viene utilizzata esclusivamente con i data binding, descritti in modo più dettagliato nell'articolo [**Data Binding**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

La **pagina demo di x:Reference** Mostra due utilizzi `x:Reference` di con data binding, il primo in cui viene usato per impostare la `Source` proprietà dell' `Binding` oggetto e il secondo in cui viene usato per impostare la `BindingContext` proprietà per due data binding:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ReferenceDemoPage"
             x:Name="page"
             Title="x:Reference Demo">

    <StackLayout Margin="10, 0">

        <Label Text="{Binding Source={x:Reference page},
                              StringFormat='The type of this page is {0}'}"
               FontSize="18"
               VerticalOptions="CenterAndExpand"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="Center" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='{0:F0}&#x00B0; rotation'}"
               Rotation="{Binding Value}"
               FontSize="24"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

Entrambe `x:Reference` le espressioni utilizzano la versione abbreviata `ReferenceExtension` del nome della classe ed `Name=` eliminano la parte dell'espressione. Nel primo esempio, l' `x:Reference` estensione di markup è incorporata nell' `Binding` estensione di markup. Si noti che `Source` le `StringFormat` impostazioni e sono separate da virgole. Ecco il programma in esecuzione:

[![Demo di x:Reference](consuming-images/referencedemo-small.png "Demo di x:Reference")](consuming-images/referencedemo-large.png#lightbox "Demo di x:Reference")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type (estensione di markup)

L' `x:Type` estensione di markup è l'equivalente XAML della parola [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/) chiave C#. È supportato dalla [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension) classe, che definisce una proprietà denominata [`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) di tipo `string` impostata su un nome di classe o di struttura. L' `x:Type` estensione di markup restituisce [`System.Type`](xref:System.Type) l'oggetto della classe o della struttura. `TypeName`è la proprietà di contenuto `TypeExtension`di, `TypeName=` pertanto non è obbligatorio `x:Type` quando viene visualizzato con parentesi graffe.

In Novell. Forms sono disponibili diverse proprietà con argomenti di tipo `Type`. Gli esempi includono [`TargetType`](xref:Xamarin.Forms.Style.TargetType) la proprietà `Style`di e l'attributo [x:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) usato per specificare gli argomenti nelle classi generiche. Tuttavia, il parser XAML esegue l' `typeof` operazione automaticamente e l' `x:Type` estensione di markup non viene utilizzata in questi casi.

Un'unica posizione `x:Type` in cui *è* necessario è `x:Array` con l'estensione di markup, descritta nella [sezione successiva](#array).

L' `x:Type` estensione di markup è utile anche quando si crea un menu in cui ogni voce di menu corrisponde a un oggetto di un determinato tipo. È possibile associare un `Type` oggetto a ogni voce di menu, quindi creare un'istanza dell'oggetto quando viene selezionata la voce di menu.

Ecco come funziona il menu di navigazione `MainPage` in nel programma **estensioni di markup** . Il file **MainPage. XAML** contiene un `TableView` oggetto con `TextCell` ognuno corrispondente a una determinata pagina del programma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.MainPage"
             Title="Markup Extensions"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection>
                <TextCell Text="x:Static Demo"
                          Detail="Access constants or statics"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:StaticDemoPage}" />

                <TextCell Text="x:Reference Demo"
                          Detail="Reference named elements on the page"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ReferenceDemoPage}" />

                <TextCell Text="x:Type Demo"
                          Detail="Associate a Button with a Type"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:TypeDemoPage}" />

                <TextCell Text="x:Array Demo"
                          Detail="Use an array to fill a ListView"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ArrayDemoPage}" />

                ···                          

        </TableRoot>
    </TableView>
</ContentPage>
```

Di seguito è illustrata la pagina principale di apertura nelle **estensioni di markup**:

[![Pagina principale](consuming-images/mainpage-small.png "Pagina principale")](consuming-images/mainpage-large.png#lightbox "Pagina principale")

Ogni `CommandParameter` proprietà è impostata su un' `x:Type` estensione di markup che fa riferimento a una delle altre pagine. La `Command` proprietà è associata a una proprietà denominata `NavigateCommand`. Questa proprietà viene definita nel file `MainPage` code-behind:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

La `NavigateCommand` proprietà è un `Command` oggetto che implementa un comando Execute con un argomento di tipo `Type` &mdash; il cui valore `CommandParameter`è. Il metodo usa `Activator.CreateInstance` per creare un'istanza della pagina e quindi passa a essa. Il costruttore termina impostando la `BindingContext` della pagina su se stessa, consentendo `Binding` `Command` a di funzionare. Per altri dettagli su questo tipo di codice, vedere l'articolo relativo al [**Data Binding**](~/xamarin-forms/app-fundamentals/data-binding/index.md) e in particolare l'articolo sui [**comandi**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) .

La pagina **demo di x:Type** usa una tecnica simile per creare un'istanza di elementi Novell. Forms e aggiungerli a un. `StackLayout` Il file XAML è inizialmente costituito `Button` da tre elementi `Command` con le rispettive `Binding` proprietà impostate `CommandParameter` su un oggetto e le proprietà impostate su tipi di tre visualizzazioni Novell. Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.TypeDemoPage"
             Title="x:Type Demo">

    <StackLayout x:Name="stackLayout"
                 Padding="10, 0">

        <Button Text="Create a Slider"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Slider}" />

        <Button Text="Create a Stepper"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Stepper}" />

        <Button Text="Create a Switch"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Switch}" />
    </StackLayout>
</ContentPage>
```

Il file code-behind definisce e inizializza la `CreateCommand` proprietà:

```csharp
public partial class TypeDemoPage : ContentPage
{
    public TypeDemoPage()
    {
        InitializeComponent();

        CreateCommand = new Command<Type>((Type viewType) =>
        {
            View view = (View)Activator.CreateInstance(viewType);
            view.VerticalOptions = LayoutOptions.CenterAndExpand;
            stackLayout.Children.Add(view);
        });

        BindingContext = this;
    }

    public ICommand CreateCommand { private set; get; }
}
```

Il metodo eseguito quando viene premuto un `Button` oggetto crea una nuova istanza dell'argomento, ne imposta la `VerticalOptions` proprietà e la `StackLayout`aggiunge a. I tre `Button` elementi condividono quindi la pagina con visualizzazioni create dinamicamente:

[![Demo di x:Type](consuming-images/typedemo-small.png "Demo di x:Type")](consuming-images/typedemo-large.png#lightbox "Demo di x:Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array (estensione di markup)

L' `x:Array` estensione di markup consente di definire una matrice nel markup. È supportato dalla [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension) classe, che definisce due proprietà:

- `Type`di tipo `Type`, che indica il tipo degli elementi nella matrice.
- `Items`di tipo `IList`, che è una raccolta di elementi stessi. Questa è la proprietà di contenuto `ArrayExtension`di.

L' `x:Array` estensione di markup non viene mai visualizzata tra parentesi graffe. I tag `x:Array` di inizio e di fine delimitano invece l'elenco di elementi. Impostare la `Type` proprietà su un' `x:Type` estensione di markup.

La **pagina demo di x:Array** illustra come usare `x:Array` per aggiungere elementi a un `ListView` oggetto impostando `ItemsSource` la proprietà su una matrice:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ArrayDemoPage"
             Title="x:Array Demo Page">
    <ListView Margin="10">
        <ListView.ItemsSource>
            <x:Array Type="{x:Type Color}">
                <Color>Aqua</Color>
                <Color>Black</Color>
                <Color>Blue</Color>
                <Color>Fuchsia</Color>
                <Color>Gray</Color>
                <Color>Green</Color>
                <Color>Lime</Color>
                <Color>Maroon</Color>
                <Color>Navy</Color>
                <Color>Olive</Color>
                <Color>Pink</Color>
                <Color>Purple</Color>
                <Color>Red</Color>
                <Color>Silver</Color>
                <Color>Teal</Color>
                <Color>White</Color>
                <Color>Yellow</Color>
            </x:Array>
        </ListView.ItemsSource>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <BoxView Color="{Binding}"
                             Margin="3" />    
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>        
```

Tramite `ViewCell` viene creato un `BoxView` oggetto semplice per ogni voce di colore:

[![Demo di x:Array](consuming-images/arraydemo-small.png "Demo di x:Array")](consuming-images/arraydemo-large.png#lightbox "Demo di x:Array")

Esistono diversi modi per specificare i singoli `Color` elementi in questa matrice. È possibile usare un' `x:Static` estensione di markup:

```xaml
<x:Static Member="Color.Blue" />
```

In alternativa, è possibile `StaticResource` usare per recuperare un colore da un dizionario risorse:

```xaml
<StaticResource Key="myColor" />
```

Alla fine di questo articolo verrà visualizzata un'estensione di markup XAML personalizzata che crea anche un nuovo valore di colore:

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

Quando si definiscono matrici di tipi comuni come stringhe o numeri, usare i tag elencati nell'articolo [**passare gli argomenti del costruttore**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) per delimitare i valori.

<a name="null" />

## <a name="xnull-markup-extension"></a>estensione di markup x:Null

L' `x:Null` estensione di markup è supportata dalla [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension) classe. Non dispone di proprietà ed è semplicemente l'equivalente XAML della parola chiave [`null`](/dotnet/csharp/language-reference/keywords/null/) C#.

L' `x:Null` estensione di markup è raramente necessaria e viene usata raramente, ma se si trova una necessità, si sarà contenti che esista.

La pagina **demo di x:null** illustra uno scenario in `x:Null` cui potrebbe essere utile. Si supponga di definire un oggetto `Style` implicito per `Label` che `Setter` includa un `FontFamily` oggetto che imposta la proprietà su un nome di famiglia dipendente dalla piattaforma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.NullDemoPage"
             Title="x:Null Demo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="48" />
                <Setter Property="FontFamily">
                    <Setter.Value>
                        <OnPlatform x:TypeArguments="x:String">
                            <On Platform="iOS" Value="Times New Roman" />
                            <On Platform="Android" Value="serif" />
                            <On Platform="UWP" Value="Times New Roman" />
                        </OnPlatform>
                    </Setter.Value>
                </Setter>
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ContentPage.Content>
        <StackLayout Padding="10, 0">
            <Label Text="Text 1" />
            <Label Text="Text 2" />

            <Label Text="Text 3"
                   FontFamily="{x:Null}" />

            <Label Text="Text 4" />
            <Label Text="Text 5" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>   
```

Quindi si scopre che per uno degli `Label` elementi si desidera che tutte le impostazioni delle proprietà nell'oggetto implicito `Style` , ad `FontFamily`eccezione di, che si desidera essere il valore predefinito. È possibile definire un `Style` altro a tale scopo, ma un approccio più semplice consiste semplicemente nell' `FontFamily` impostare la proprietà del `Label` particolare `x:Null`su, come illustrato in centro `Label`.

Ecco il programma in esecuzione:

[![Demo di x:Null](consuming-images/nulldemo-small.png "Demo di x:Null")](consuming-images/nulldemo-large.png#lightbox "Demo di x:Null")

Si noti che quattro degli `Label` elementi hanno un tipo di carattere serif, ma `Label` il centro ha il tipo di carattere sans-serif predefinito.

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>Estensione di markup onplatform

L'estensione di markup `OnPlatform` consente di personalizzare l'aspetto dell'interfaccia utente per ogni piattaforma. Fornisce le stesse funzionalità delle [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classi e [`On`](xref:Xamarin.Forms.On) , ma con una rappresentazione più concisa.

L' `OnPlatform` estensione di markup è supportata dalla [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) classe, che definisce le proprietà seguenti:

- `Default`di tipo `object`, che viene impostato su un valore predefinito da applicare alle proprietà che rappresentano le piattaforme.
- `Android`di tipo `object`, impostato su un valore da applicare in Android.
- `GTK`di tipo `object`, impostato su un valore da applicare sulle piattaforme GTK.
- `iOS`di tipo `object`, impostato su un valore da applicare in iOS.
- `macOS`di tipo `object`, impostato su un valore da applicare in MacOS.
- `Tizen`di tipo `object`, impostato su un valore da applicare alla piattaforma Tizen.
- `UWP`di tipo `object`, che è possibile impostare su un valore da applicare nel piattaforma UWP (Universal Windows Platform).
- `WPF`di tipo `object`, impostato su un valore da applicare alla piattaforma Windows Presentation Foundation.
- `Converter`di tipo `IValueConverter`, che può essere impostato su un' `IValueConverter` implementazione di.
- `ConverterParameter`di tipo `object`, che può essere impostato su un valore da passare all' `IValueConverter` implementazione.

> [!NOTE]
> Il parser XAML consente di [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) abbreviare la classe come `OnPlatform`.

La `Default` proprietà è la proprietà di contenuto `OnPlatformExtension`di. Pertanto, per le espressioni di markup XAML espresse con parentesi graffe, è possibile eliminare `Default=` la parte dell'espressione purché sia il primo argomento. Se la `Default` proprietà non è impostata, verrà utilizzato per impostazione [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) predefinita il valore della proprietà, purché l'estensione di markup sia [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)destinata a un oggetto.

> [!IMPORTANT]
> Il parser XAML prevede che i valori del tipo corretto verranno forniti alle proprietà che utilizzano l' `OnPlatform` estensione di markup. Se è necessaria la conversione del tipo `OnPlatform` , l'estensione di markup tenterà di eseguirla usando i convertitori predefiniti forniti da Novell. Forms. Tuttavia, esistono alcune conversioni di tipi che non possono essere eseguite dai convertitori predefiniti e in questi casi la `Converter` proprietà deve essere impostata su un' `IValueConverter` implementazione di.

La pagina **demo di onplatform** Mostra come usare l' `OnPlatform` estensione di markup:

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

In questo esempio, tutte e `OnPlatform` tre le espressioni usano la versione abbreviata del nome della `OnPlatformExtension` classe. Le tre `OnPlatform` [`Color`](xref:Xamarin.Forms.BoxView.Color)estensioni di markup impostano le proprietà, [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) di [`BoxView`](xref:Xamarin.Forms.BoxView) su valori diversi in iOS, Android e UWP. Le estensioni di markup forniscono anche valori predefiniti per queste proprietà nelle piattaforme non specificate, eliminando la `Default=` parte dell'espressione. Si noti che le proprietà dell'estensione di markup impostate sono separate da virgole.

Ecco il programma in esecuzione:

[![Demo su piattaforma](consuming-images/onplatformdemo-small.png "Demo su piattaforma")](consuming-images/onplatformdemo-large.png#lightbox "Demo su piattaforma")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>Estensione di markup onidiom

L' `OnIdiom` estensione di markup consente di personalizzare l'aspetto dell'interfaccia utente in base all'idioma del dispositivo in cui è in esecuzione l'applicazione. È supportato dalla [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) classe, che definisce le proprietà seguenti:

- `Default`di tipo `object`, che viene impostato su un valore predefinito da applicare alle proprietà che rappresentano gli idiomi dei dispositivi.
- `Phone`di tipo `object`, impostato su un valore da applicare ai telefoni.
- `Tablet`di tipo `object`, impostato su un valore da applicare ai tablet.
- `Desktop`di tipo `object`, impostato su un valore da applicare sulle piattaforme desktop.
- `TV`di tipo `object`, impostato su un valore da applicare sulle piattaforme TV.
- `Watch`di tipo `object`, impostato su un valore da applicare sulle piattaforme di controllo.
- `Converter`di tipo `IValueConverter`, che può essere impostato su un' `IValueConverter` implementazione di.
- `ConverterParameter`di tipo `object`, che può essere impostato su un valore da passare all' `IValueConverter` implementazione.

> [!NOTE]
> Il parser XAML consente di [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) abbreviare la classe come `OnIdiom`.

La `Default` proprietà è la proprietà di contenuto `OnIdiomExtension`di. Pertanto, per le espressioni di markup XAML espresse con parentesi graffe, è possibile eliminare `Default=` la parte dell'espressione purché sia il primo argomento.

> [!IMPORTANT]
> Il parser XAML prevede che i valori del tipo corretto verranno forniti alle proprietà che utilizzano l' `OnIdiom` estensione di markup. Se è necessaria la conversione del tipo `OnIdiom` , l'estensione di markup tenterà di eseguirla usando i convertitori predefiniti forniti da Novell. Forms. Tuttavia, esistono alcune conversioni di tipi che non possono essere eseguite dai convertitori predefiniti e in questi casi la `Converter` proprietà deve essere impostata su un' `IValueConverter` implementazione di.

La pagina **demo Onidioma** Mostra come usare l' `OnIdiom` estensione di markup:

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

In questo esempio, tutte e `OnIdiom` tre le espressioni usano la versione abbreviata del nome della `OnIdiomExtension` classe. Le tre `OnIdiom` [`Color`](xref:Xamarin.Forms.BoxView.Color)estensioni di markup impostano le proprietà, [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) di [`BoxView`](xref:Xamarin.Forms.BoxView) su valori diversi sugli idiomi telefono, tablet e desktop. Le estensioni di markup forniscono anche valori predefiniti per queste proprietà sugli idiomi non specificati, eliminando al tempo stesso `Default=` la parte dell'espressione. Si noti che le proprietà dell'estensione di markup impostate sono separate da virgole.

Ecco il programma in esecuzione:

[![Demo onidioma](consuming-images/onidiomdemo-small.png "Demo onidioma")](consuming-images/onidiomdemo-large.png#lightbox "Demo onidioma")

## <a name="datatemplate-markup-extension"></a>Estensione di markup DataTemplate

L' `DataTemplate` estensione di markup consente di convertire un tipo in un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)oggetto. `DataTemplateExtension` È supportato dalla classe, che definisce `TypeName` una proprietà di tipo `string`, impostata sul nome del tipo da convertire in un oggetto. `DataTemplate` La `TypeName` proprietà è la proprietà di contenuto `DataTemplateExtension`di. Pertanto, per le espressioni di markup XAML espresse con parentesi graffe, è possibile eliminare `TypeName=` la parte dell'espressione.

> [!NOTE]
> Il parser XAML consente di `DataTemplateExtension` abbreviare la classe come `DataTemplate`.

Un utilizzo tipico di questa estensione di markup si trova in un'applicazione shell, come illustrato nell'esempio seguente:

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

In questo esempio, `MonkeysPage` viene convertito da un [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)un oggetto, che viene impostato come valore della `ShellContent.ContentTemplate` proprietà. Ciò garantisce che `MonkeysPage` venga creato solo quando si verifica la navigazione alla pagina, anziché all'avvio dell'applicazione.

Per ulteriori informazioni sulle applicazioni shell, vedere [Novell. Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="fontimage-markup-extension"></a>Estensione del markup FontImage

L' `FontImage` estensione di markup consente di visualizzare un'icona del tipo di carattere in qualsiasi visualizzazione in `ImageSource`grado di visualizzare. Fornisce la stessa funzionalità della `FontImageSource` classe, ma con una rappresentazione più concisa.

L' `FontImage` estensione di markup è supportata dalla `FontImageExtension` classe, che definisce le proprietà seguenti:

- `FontFamily`di tipo `string`, la famiglia di caratteri a cui appartiene l'icona del tipo di carattere.
- `Glyph`di tipo `string`, il valore del carattere Unicode dell'icona del tipo di carattere.
- `Color`di tipo [`Color`](xref:Xamarin.Forms.Color), il colore da utilizzare quando si visualizza l'icona del tipo di carattere.
- `Size`di tipo `double`, la dimensione, in unità indipendenti dal dispositivo, dell'icona del tipo di carattere di cui è stato eseguito il rendering. Il valore predefinito è 30. Inoltre, questa proprietà può essere impostata su una dimensione del tipo di carattere denominata.

> [!NOTE]
> Il parser XAML consente di `FontImageExtension` abbreviare la classe come `FontImage`.

La `Glyph` proprietà è la proprietà di contenuto `FontImageExtension`di. Pertanto, per le espressioni di markup XAML espresse con parentesi graffe, è possibile eliminare `Glyph=` la parte dell'espressione purché sia il primo argomento.

La pagina **demo di FontImage** illustra come usare l' `FontImage` estensione di markup:

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

In questo esempio, la versione abbreviata del `FontImageExtension` nome della classe viene usata per visualizzare un'icona di Xbox, dalla famiglia di caratteri Ionicons, [`Image`](xref:Xamarin.Forms.Image)in un oggetto. L'espressione usa anche l' `OnPlatform` estensione di markup per specificare `FontFamily` valori di proprietà diversi in iOS e Android. Inoltre, la `Glyph=` parte dell'espressione viene eliminata e le proprietà dell'estensione di markup impostate sono separate da virgole. Si noti che, mentre il carattere Unicode per l' `\uf30c`icona è, deve essere preceduto da un carattere di escape `&#xf30c;`in XAML e quindi diventa.

Ecco il programma in esecuzione:

[![Screenshot dell'estensione di markup FontImage](consuming-images/fontimagedemo.png "Demo di FontImage")](consuming-images/fontimagedemo-large.png#lightbox "Demo di FontImage")

Per informazioni sulla visualizzazione delle icone dei tipi di carattere specificando i dati `FontImageSource` dell'icona del tipo di carattere in un oggetto, vedere [visualizzare le icone del carattere](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons).

## <a name="onapptheme-markup-extension"></a>Estensione di markup OnAppTheme

L' `OnAppTheme` estensione di markup consente di specificare una risorsa da utilizzare, ad esempio un'immagine o un colore, in base al tema corrente del sistema. Fornisce la stessa funzionalità della `OnAppTheme<T>` classe, ma con una rappresentazione più concisa.

> [!IMPORTANT]
> L' `OnAppTheme` estensione di markup presenta requisiti minimi per il sistema operativo. Per altre informazioni, vedere [rispondere alle modifiche apportate al tema del sistema nelle applicazioni Novell. Forms](~/xamarin-forms/user-interface/theming/system-theme-changes.md).

L' `OnAppTheme` estensione di markup è supportata dalla `OnAppThemeExtension` classe, che definisce le proprietà seguenti:

- `Default`, di tipo `object`, impostato sulla risorsa da utilizzare per impostazione predefinita.
- `Light`, di tipo `object`, impostato sulla risorsa da usare quando il dispositivo usa il tema chiaro.
- `Dark`, di tipo `object`, impostato sulla risorsa da usare quando il dispositivo usa il tema scuro.
- `Value`, di tipo `object`, che restituisce la risorsa attualmente utilizzata dall'estensione di markup.
- `Converter`di tipo `IValueConverter`, che può essere impostato su un' `IValueConverter` implementazione di.
- `ConverterParameter`di tipo `object`, che può essere impostato su un valore da passare all' `IValueConverter` implementazione.

> [!NOTE]
> Il parser XAML consente di `OnAppThemeExtension` abbreviare la classe come `OnAppTheme`.

La `Default` proprietà è la proprietà di contenuto `OnAppThemeExtension`di. Pertanto, per le espressioni di markup XAML espresse con parentesi graffe, è possibile eliminare `Default=` la parte dell'espressione purché sia il primo argomento.

La pagina **demo di OnAppTheme** illustra come usare l' `OnAppTheme` estensione di markup:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.OnAppThemeDemoPage"
             Title="OnAppTheme Demo">
    <ContentPage.Resources>

        <Style x:Key="labelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{OnAppTheme Black, Light=Blue, Dark=Teal}" />
        </Style>

    </ContentPage.Resources>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{OnAppTheme Light=Green, Dark=Red}" />
        <Label Text="This text is black by default, blue in light mode, and teal in dark mode."
               Style="{DynamicResource labelStyle}" />
    </StackLayout>
</ContentPage>
```

In questo esempio, il colore del testo del primo [`Label`](xref:Xamarin.Forms.Label) viene impostato su verde quando il dispositivo usa il tema chiaro e viene impostato su rosso quando il dispositivo usa il tema scuro. Il secondo `Label` ha la [`TextColor`](xref:Xamarin.Forms.Label.TextColor) proprietà impostata tramite [`Style`](xref:Xamarin.Forms.Style). In `Style` questo modo, il colore del `Label` testo di viene impostato su nero per impostazione predefinita, su blu quando il dispositivo usa il tema chiaro e su verde acqua quando il dispositivo usa il tema scuro.

> [!NOTE]
> Un [`Style`](xref:Xamarin.Forms.Style) oggetto che utilizza l' `OnAppTheme` estensione di markup deve essere applicato a un controllo con `DynamicResource` l'estensione di markup, in modo che l'interfaccia utente dell'applicazione venga aggiornata quando il tema del sistema viene modificato.

Ecco il programma in esecuzione:

![Demo di OnAppTheme](consuming-images/onappthemedemo.png "Demo di OnAppTheme")

## <a name="define-markup-extensions"></a>Definire le estensioni di markup

Se è stata rilevata una necessità di un'estensione di markup XAML non disponibile in Novell. Forms, è possibile [crearne una personalizzata](creating.md).

## <a name="related-links"></a>Link correlati

- [Estensioni di markup (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capitolo delle estensioni di markup XAML dal libro Novell. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Shell di Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Rispondere alle modifiche del tema di sistema nelle applicazioni Novell. Forms](~/xamarin-forms/user-interface/theming/system-theme-changes.md)
