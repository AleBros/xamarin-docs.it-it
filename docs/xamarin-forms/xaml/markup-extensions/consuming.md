---
title: Utilizzo di estensioni di Markup XAML
description: Questo articolo illustra come usare le estensioni di markup XAML di xamarin. Forms per migliorare la potenza e flessibilità di XAML, consentendo di attributi dell'elemento da impostare da diverse origini.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: a630d7c2acb95b7551c9f5f870078a0efcfc075c
ms.sourcegitcommit: ecdc031e9e26bbbf9572885531ee1f2e623203f5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39393672"
---
# <a name="consuming-xaml-markup-extensions"></a>Utilizzo di estensioni di Markup XAML

Le estensioni di markup XAML consentono di migliorare la potenza e flessibilità di XAML, consentendo di attributi dell'elemento da impostare da diverse origini. Diverse estensioni di markup XAML fanno parte della specifica di XAML 2009. Questi elementi vengono visualizzati nei file XAML con la consueta `x` prefisso dello spazio dei nomi e vengono in genere definite con questo prefisso. Questi elementi sono descritti nelle sezioni seguenti:

- [`x:Static`](#static) &ndash; Fare riferimento a proprietà statiche, campi o membri di enumerazione.
- [`x:Reference`](#reference) &ndash; riferimento denominati elementi della pagina.
- [`x:Type`](#type) &ndash; impostare un attributo un `System.Type` oggetto.
- [`x:Array`](#array) &ndash; costruire una matrice di oggetti di un determinato tipo.
- [`x:Null`](#null) &ndash; impostare un attributo un `null` valore.

Altre estensioni di markup XAML in passato sono supportate nelle altre implementazioni di XAML e sono supportate anche da xamarin. Forms. Questi elementi sono descritti più dettagliatamente negli altri articoli:

- `StaticResource` &ndash; Fare riferimento a oggetti da un dizionario risorse, come descritto nell'articolo [ **dizionari risorse**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource` &ndash; rispondere alle modifiche agli oggetti in un dizionario risorse, come descritto nell'articolo [ **stili dinamici**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding` &ndash; stabilire un collegamento tra le proprietà di due oggetti, come descritto nell'articolo [ **Data Binding**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding` &ndash; esegue l'associazione dati da un modello di controllo, come descritto nell'articolo [ **l'associazione da un modello di controllo**](/guides/xamarin-forms/application-fundamentals/templates/control-templates/template-binding/).

Il [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout) layout Usa l'estensione di markup personalizzata [ `ConstraintExpression` ](xref:Xamarin.Forms.ConstraintExpression). Questa estensione di markup è descritto nell'articolo [ **RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>Estensione del markup x:Static

Il `x:Static` estensione di markup è supportato per il [ `StaticExtension` ](xref:Xamarin.Forms.Xaml.StaticExtension) classe. La classe dispone di una singola proprietà denominata [ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member) typu `string` che è impostato sul nome di una costante pubblica, proprietà statica, campo statico o membro di enumerazione.

Un modo comune per utilizzare `x:Static` consiste nel definire innanzitutto una classe con alcune costanti o variabili statiche, ad esempio questo piccolo `AppConstants` classe la [ **MarkupExtensions** ](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/) programma:

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

Il **X:Static Demo** pagina vengono illustrati diversi modi per usare il `x:Static` estensione di markup. L'approccio più dettagliata crea un'istanza di `StaticExtension` classe tra `Label.FontSize` i tag di elemento di proprietà:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
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

Il parser XAML consente anche il `StaticExtension` classe deve essere abbreviato come `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Ciò può essere semplificato ulteriormente, ma la modifica introduce alcune nuova sintassi: È costituito da inserimento di `StaticExtension` classe e il membro impostazione tra parentesi graffe. L'espressione risultante viene impostato direttamente sul `FontSize` attributo:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Si noti che esistono *alcun* virgolette doppie all'interno di parentesi graffe. Il `Member` proprietà di `StaticExtension` non è più un attributo XML. È invece parte dell'espressione per l'estensione di markup.

Così come è possibile abbreviare `x:StaticExtension` a `x:Static` quando si usa, come elemento oggetto, è anche possibile abbreviare, nell'espressione tra parentesi graffe:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

Il `StaticExtension` classe ha un `ContentProperty` attributo fare riferimento alla proprietà `Member`, che indica che questa proprietà come proprietà di contenuto predefinito della classe. Le estensioni di markup XAML espresse con parentesi graffe, è possibile eliminare il `Member=` fa parte dell'espressione:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Si tratta del formato più comune del `x:Static` estensione di markup.

Il **Demo statico** pagina contiene due altri esempi. Il tag radice del file XAML contiene una dichiarazione dello spazio dei nomi XML per .NET `System` dello spazio dei nomi:

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

In questo modo il `Label` dimensione del carattere da impostare per il campo statico `Math.PI`. Testo piuttosto ridotta, verrà generato in modo che il `Scale` è impostata su `Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

L'esempio finale visualizza il `Device.RuntimePlatform` valore. Il `Environment.NewLine` utilizzata per inserire un carattere di nuova riga tra le due proprietà statica `Span` oggetti:

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

Ecco il codice di esempio in esecuzione in tutti e tre le piattaforme:

[![Demo X:Static](consuming-images/staticdemo-small.png "X:Static Demo")](consuming-images/staticdemo-large.png#lightbox "X:Static Demo")

<a name="reference" />

## <a name="xreference-markup-extension"></a>estensione di markup x:Reference

Il `x:Reference` estensione di markup è supportato per il [ `ReferenceExtension` ](xref:Xamarin.Forms.Xaml.ReferenceExtension) classe. La classe dispone di una singola proprietà denominata [ `Name` ](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) typu `string` che è impostato sul nome di un elemento nella pagina in cui è stato assegnato un nome con `x:Name`. Ciò `Name` è la proprietà di contenuto `ReferenceExtension`, quindi `Name=` non è obbligatorio quando `x:Reference` viene visualizzato tra parentesi graffe.

Il `x:Reference` estensione di markup viene utilizzato esclusivamente con le associazioni di dati, che sono descritte più dettagliatamente nell'articolo [ **Data Binding**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Il **X:Reference Demo** pagina mostra due usi di `x:Reference` con le associazioni dati, il primo in cui viene usato per impostare il `Source` proprietà del `Binding` oggetto, mentre la seconda di dove viene usata per impostare il `BindingContext` proprietà per le associazioni due dati:

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

Entrambe `x:Reference` espressioni di usano la versione abbreviata del `ReferenceExtension` nome della classe ed eliminare il `Name=` fa parte dell'espressione. Nel primo esempio, il `x:Reference` estensione di markup è incorporato nel `Binding` estensione di markup. Si noti che il `Source` e `StringFormat` impostazioni sono separate da virgole. Ecco il programma in esecuzione in tutti e tre le piattaforme:

[![Demo X:Reference](consuming-images/referencedemo-small.png "X:Reference Demo")](consuming-images/referencedemo-large.png#lightbox "X:Reference Demo")

<a name="type" />

## <a name="xtype-markup-extension"></a>Estensione del markup x:Type

Il `x:Type` estensione di markup è l'equivalente XAML del linguaggio c# [ `typeof` ](/dotnet/csharp/language-reference/keywords/typeof/) (parola chiave). È supportato dal [ `TypeExtension` ](xref:Xamarin.Forms.Xaml.TypeExtension) (classe), che definisce una proprietà denominata [ `TypeName` ](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) typu `string` che è impostato su un nome di classe o struttura. Il `x:Type` markup estensione restituisce il [ `System.Type` ](xref:System.Type) oggetto di tale classe o struttura. `TypeName` è la proprietà content del `TypeExtension`, pertanto `TypeName=` non è obbligatorio quando `x:Type` viene visualizzato tra parentesi graffe.

All'interno di xamarin. Forms, esistono diverse proprietà che hanno argomenti di tipo `Type`. Gli esempi includono la [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) proprietà della `Style`e il [X:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) attributo utilizzato per specificare gli argomenti in classi generiche. Tuttavia, il parser XAML consente di eseguire la `typeof` operazione automaticamente e il `x:Type` estensione di markup non viene usato in questi casi.

Un'unica posizione in cui `x:Type` *viene* obbligatorio è con il `x:Array` estensione di markup, che è descritti il [nella sezione successiva](#array).

Il `x:Type` estensione di markup è utile anche quando la creazione di un menu in cui ogni voce di menu corrisponde a un oggetto di un determinato tipo. È possibile associare un `Type` oggetti con ogni voce di menu e quindi creare un'istanza dell'oggetto quando la voce di menu è selezionata.

Si tratta di come menu di navigazione di `MainPage` nella **le estensioni di Markup** funzionamento del programma. Il **MainPage. XAML** file contiene una `TableView` con ogni `TextCell` corrispondente a una determinata pagina del programma:

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

Ecco la pagina principale di apertura **estensioni di Markup**:

[![Main pagina](consuming-images/mainpage-small.png "Main pagina")](consuming-images/mainpage-large.png#lightbox "Main pagina")

Ciascuna `CommandParameter` è impostata su un `x:Type` estensione di markup che fa riferimento a una delle altre pagine. Il `Command` proprietà è associata a una proprietà denominata `NavigateCommand`. Questa proprietà è definita nel `MainPage` file code-behind:

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

Il `NavigateCommand` proprietà è una `Command` oggetto che implementa un comando execute con un argomento di tipo `Type` &mdash; hodnotou `CommandParameter`. Il metodo Usa `Activator.CreateInstance` per creare un'istanza della pagina e quindi si passa a esso. Il costruttore termina impostando il `BindingContext` della pagina a se stessa, che consente la `Binding` su `Command` a funzionare. Vedere le [ **Data Binding** ](~/xamarin-forms/app-fundamentals/data-binding/index.md) articolo e in particolare il [ **Commanding** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) per altre informazioni su questo tipo di codice.

Il **X:Type Demo** pagina Usa una tecnica simile per creare istanze di elementi di xamarin. Forms e aggiungerli a un `StackLayout`. Il file XAML inizialmente costituito da tre `Button` elementi con loro `Command` impostate su un `Binding` e il `CommandParameter` impostate sui tipi di tre visualizzazioni di xamarin. Forms:

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

Il file code-behind definisce e inizializza il `CreateCommand` proprietà:

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

Il metodo che viene eseguita quando un `Button` viene premuto crea una nuova istanza dell'argomento, imposta relativi `VerticalOptions` proprietà e lo aggiunge al `StackLayout`. I tre `Button` elementi condividano quindi la pagina con le viste create in modo dinamico:

[![Demo X:Type](consuming-images/typedemo-small.png "X:Type Demo")](consuming-images/typedemo-large.png#lightbox "X:Type Demo")

<a name="array" />

## <a name="xarray-markup-extension"></a>Estensione del markup x:Array

Il `x:Array` estensione di markup consente di definire una matrice nel markup. È supportato per il [ `ArrayExtension` ](xref:Xamarin.Forms.Xaml.ArrayExtension) (classe), che definisce due proprietà:

- `Type` di tipo `Type`, che indica il tipo degli elementi nella matrice.
- `Items` di tipo `IList`, che è una raccolta di elementi stessi. Questa è la proprietà content del `ArrayExtension`.

Il `x:Array` estensione di markup stesso non viene mai visualizzato tra parentesi graffe. Al contrario, `x:Array` tag di inizio e fine delimitare l'elenco di elementi. Impostare il `Type` proprietà su un `x:Type` estensione di markup.

Il **X:Array Demo** pagina viene illustrato come utilizzare `x:Array` per aggiungere elementi a un `ListView` impostando il `ItemsSource` proprietà in una matrice:

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

Il `ViewCell` consente di creare una semplice `BoxView` per ogni voce di colore:

[![Demo X:Array](consuming-images/arraydemo-small.png "X:Array Demo")](consuming-images/arraydemo-large.png#lightbox "X:Array Demo")

Esistono diversi modi per specificare i singoli `Color` elementi nella matrice. È possibile usare un `x:Static` estensione di markup:

```xaml
<x:Static Member="Color.Blue" />
```

In alternativa, è possibile usare `StaticResource` per recuperare un colore da un dizionario risorse:

```xaml
<StaticResource Key="myColor" />
```

Verso la fine di questo articolo, si noterà un'estensione di markup XAML personalizzata che crea anche un nuovo valore di colore:

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

Quando si definiscono le matrici di tipi comuni, ad esempio stringhe o numeri, utilizzare i tag elencati nella [ **passando gli argomenti del costruttore** ](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) articolo per delimitare i valori.

<a name="null" />

## <a name="xnull-markup-extension"></a>Estensione del markup x:Null

Il `x:Null` estensione di markup è supportato per il [ `NullExtension` ](xref:Xamarin.Forms.Xaml.NullExtension) classe. Il non dispone di proprietà ed è semplicemente l'equivalente XAML del linguaggio c# [ `null` ](/dotnet/csharp/language-reference/keywords/null/) (parola chiave).

Il `x:Null` estensione di markup viene raramente richiesti e usati raramente, ma se si trova una necessità per tale, sarà valeva che è presente.

Il **X:null Demo** pagina viene illustrato uno scenario quando `x:Null` potrebbe risultare utile. Si supponga di definisce implicita `Style` per `Label` che include un `Setter` che consente di scegliere il `FontFamily` proprietà a un nome di famiglia dipendente dalla piattaforma:

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

Quindi si scopre che per uno dei `Label` elementi, si desidera che tutte le impostazioni delle proprietà in implicita `Style` ad eccezione del `FontFamily`, che potrebbe essere il valore predefinito. È possibile definire un'altra `Style` per tale scopo, ma un approccio più semplice consiste nell'impostare il `FontFamily` proprietà del particolare `Label` a `x:Null`, come dimostrato nel centro `Label`.

Ecco il programma in esecuzione su tre piattaforme:

[![Demo X:null](consuming-images/nulldemo-small.png "X:null Demo")](consuming-images/nulldemo-large.png#lightbox "Demo x: Null")

Si noti che quattro del `Label` elementi hanno un tipo di carattere serif, ma il centro `Label` ha il tipo di carattere sans-serif predefinito.

## <a name="define-your-own-markup-extensions"></a>Definire le proprie estensioni di Markup

Se avrai avuto necessità di un'estensione di markup XAML che non è disponibile in xamarin. Forms, è possibile [creane di nuove](creating.md).


## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup (esempio)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capitolo di estensioni di markup XAML dal libro di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
