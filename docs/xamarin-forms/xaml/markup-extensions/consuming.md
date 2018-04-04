---
title: Utilizzo di estensioni di Markup XAML
description: Utilizzare le estensioni di Markup XAML disponibili in xamarin. Forms
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: 25eada483e8bd2ce95cb3101dfe873ea38b283ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="consuming-xaml-markup-extensions"></a>Utilizzo di estensioni di Markup XAML

Le estensioni di markup XAML consentono di migliorare la potenza e flessibilità di XAML, consentendo di attributi dell'elemento da impostare da una varietà di origini. Diverse estensioni di markup XAML fanno parte della specifica di XAML 2009. Questi elementi vengono visualizzati nei file XAML con il sistema `x` prefisso dello spazio dei nomi e sono in genere definiti con questo prefisso. Questi elementi sono descritti nelle sezioni seguenti:

- [`x:Static`](#static) &ndash; Fare riferimento a proprietà statiche, campi o membri di enumerazione.
- [`x:Reference`](#reference) &ndash; riferimento denominato gli elementi della pagina.
- [`x:Type`](#type) &ndash; impostare un attributo un `System.Type` oggetto.
- [`x:Array`](#array) &ndash; costruire una matrice di oggetti di un determinato tipo.
- [`x:Null`](#null) &ndash; impostare un attributo un `null` valore.

Tre altre estensioni di markup XAML in passato sono supportati nelle altre implementazioni di XAML e sono supportate anche da xamarin. Forms. Questi sono descritti in dettaglio in altri articoli:

- `StaticResource` &ndash; Fare riferimento a oggetti da un dizionario risorse, come descritto nell'articolo [ **dizionari risorse**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource` &ndash; rispondere alle modifiche agli oggetti in un dizionario risorse, come descritto nell'articolo [ **stili dinamici**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding` &ndash; stabilire un collegamento tra le proprietà di due oggetti, come descritto nell'articolo [ **Data Binding**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding` &ndash; esegue l'associazione dati da un modello di controllo, come descritto nell'articolo [**associazione di un modello di controllo**] Guide/xamarin-forms/applicazione-nozioni di base/modelli/controllo-modelli /-associazione a modello / /)

Il [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/) layout si avvalgono dell'estensione di markup personalizzata [ `ConstraintExpression` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ConstraintExpression/). Questa estensione di markup è descritto nell'articolo [ **RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>Estensione del markup x:Static

Il `x:Static` estensione di markup è supportato dal [ `StaticExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) classe. La classe dispone di una singola proprietà denominata [ `Member` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.StaticExtension.Member/) di tipo `string` che si imposta il nome di una costante pubblica, una proprietà statica, un campo statico o membro di enumerazione.

Un modo comune per utilizzare `x:Static` consiste nel definire una classe con alcune costanti o variabili statiche, ad esempio questo piccolo `AppConstants` classe il [ **MarkupExtensions** ](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/) programma:

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

Il **X:Static Demo** pagina illustra diversi modi per utilizzare il `x:Static` estensione di markup. L'approccio più dettagliato crea un'istanza di `StaticExtension` classe tra `Label.FontSize` tag di elemento di proprietà:

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

Il parser XAML consente inoltre di `StaticExtension` classe deve essere abbreviato come `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Questo può essere ulteriormente semplificato, ma la modifica introduce alcune nuova sintassi: È costituito da inserimento la `StaticExtension` classe e il membro impostazione tra parentesi graffe. L'espressione risulta viene impostata direttamente il `FontSize` attributo:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Si noti che esistono *non* virgolette doppie all'interno delle parentesi graffe. Il `Member` proprietà `StaticExtension` non è più un attributo XML. È invece parte dell'espressione per l'estensione di markup.

Analogamente a come è possibile abbreviare `x:StaticExtension` a `x:Static` quando si utilizza come elemento oggetto, è possibile anche abbreviare nell'espressione tra parentesi graffe:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

Il `StaticExtension` classe dispone di un `ContentProperty` attributo che fa riferimento la proprietà `Member`, che indica che questa proprietà come proprietà di contenuto predefinito della classe. Per le estensioni di markup XAML espresse con parentesi graffe, è possibile eliminare il `Member=` fa parte dell'espressione:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Questo è il formato più comune per il `x:Static` estensione di markup.

Il **Demo statico** pagina contiene due altri esempi. Il tag radice del file XAML contiene una dichiarazione dello spazio dei nomi XML per .NET `System` dello spazio dei nomi:

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

In questo modo il `Label` dimensioni del carattere da impostare per il campo statico `Math.PI`. Che risulta piuttosto piccolo testo, pertanto la `Scale` è impostata su `Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

Nell'esempio finale viene visualizzato il `Device.RuntimePlatform` valore. Il `Environment.NewLine` proprietà statica viene utilizzato per inserire un carattere di nuova riga tra le due `Span` oggetti:

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

Di seguito è riportato l'esempio in esecuzione in tutti e tre le piattaforme:

[![x:Static Demo](consuming-images/staticdemo-small.png "x:Static Demo")](consuming-images/staticdemo-large.png#lightbox "x:Static Demo")

<a name="reference" />

## <a name="xreference-markup-extension"></a>estensione di markup x:Reference

Il `x:Reference` estensione di markup è supportato dal [ `ReferenceExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/) classe. La classe dispone di una singola proprietà denominata [ `Name` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.ReferenceExtension.Name/) di tipo `string` impostare il nome di un elemento nella pagina di cui è stato assegnato un nome con `x:Name`. Questo `Name` proprietà è la proprietà content del `ReferenceExtension`, pertanto `Name=` è obbligatorio quando `x:Reference` viene visualizzato tra parentesi graffe.

Il `x:Reference` estensione di markup viene utilizzata esclusivamente con le associazioni dati, che sono descritte in dettaglio nell'articolo [ **Data Binding**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Il **X:Reference Demo** pagina mostra due usi di `x:Reference` con le associazioni di dati, il primo in cui viene utilizzato per impostare il `Source` proprietà del `Binding` oggetto e il secondo in cui viene utilizzato per impostare il `BindingContext` proprietà di dati di due associazioni:

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

Entrambi `x:Reference` espressioni utilizzano la versione abbreviata del `ReferenceExtension` nome della classe ed eliminare il `Name=` fa parte dell'espressione. Nel primo esempio, il `x:Reference` estensione di markup è incorporato nel `Binding` estensione di markup. Si noti che il `Source` e `StringFormat` impostazioni sono separate da virgole. Di seguito è riportato il programma in esecuzione in tutti e tre le piattaforme:

[![X:Reference Demo](consuming-images/referencedemo-small.png "Demo X:Reference")](consuming-images/referencedemo-large.png#lightbox "X:Reference Demo")

<a name="type" />

## <a name="xtype-markup-extension"></a>Estensione del markup x:Type

Il `x:Type` estensione di markup è l'equivalente XAML del linguaggio c# [ `typeof` ](/dotnet/csharp/language-reference/keywords/typeof/) (parola chiave). È supportato dal [ `TypeExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/) (classe), che definisce una proprietà denominata [ `TypeName` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.TypeExtension.TypeName/) di tipo `string` che è impostato su un nome di classe o struttura. Il `x:Type` restituisce estensione di markup di [ `System.Type` ](https://developer.xamarin.com/api/type/System.Type/) oggetto di tale classe o struttura. `TypeName` è la proprietà content del `TypeExtension`, pertanto `TypeName=` è obbligatorio quando `x:Type` viene visualizzato tra parentesi graffe.

All'interno di xamarin. Forms, esistono diverse proprietà che dispongono di argomenti di tipo `Type`. Gli esempi includono la [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) proprietà di `Style`e [X:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) attributo utilizzato per specificare gli argomenti in classi generiche. Tuttavia, il parser XAML esegue il `typeof` operazione automaticamente e `x:Type` estensione di markup non viene usato in questi casi.

Un'unica posizione in cui `x:Type` *è* obbligatorio è con il `x:Array` estensione di markup, come descritto nel [nella sezione successiva](#array).

Il `x:Type` estensione di markup è utile anche quando la creazione di un menu in cui ogni voce di menu corrisponde a un oggetto di un determinato tipo. È possibile associare un `Type` con ogni voce di menu e quindi creare un'istanza dell'oggetto quando è selezionata la voce di menu.

Si tratta come menu di navigazione `MainPage` nel **le estensioni di Markup** funzionamento del programma. Il **MainPage. XAML** file contiene un `TableView` con ogni `TextCell` corrispondente a una determinata pagina del programma:

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

Di seguito è la pagina principale di apertura **le estensioni di Markup**:

[![Main pagina](consuming-images/mainpage-small.png "Main pagina")](consuming-images/mainpage-large.png#lightbox "Main pagina")

Ogni `CommandParameter` è impostata su un `x:Type` estensione di markup che fa riferimento a una delle altre pagine. Il `Command` proprietà è associata a una proprietà denominata `NavigateCommand`. Questa proprietà è definita nel `MainPage` file code-behind:

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

Il `NavigateCommand` proprietà è un `Command` oggetto che implementa un comando di esecuzione con un argomento di tipo `Type` &mdash; il valore di `CommandParameter`. Il metodo utilizza `Activator.CreateInstance` per creare un'istanza della pagina e quindi si passa a esso. Il costruttore conclude impostando il `BindingContext` della pagina a se stessa, che consente il `Binding` in `Command` a funzionare. Vedere il [ **Data Binding** ](~/xamarin-forms/app-fundamentals/data-binding/index.md) articolo e in particolare il [ **Commanding** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) articolo per informazioni dettagliate su questo tipo di codice.

Il **X:Type Demo** pagina utilizza una tecnica simile per creare istanze di elementi di xamarin. Forms e aggiungerli a un `StackLayout`. Il file XAML inizialmente costituito da tre `Button` gli elementi con i relativi `Command` impostate in modo che un `Binding` e `CommandParameter` impostate sui tipi di xamarin. Forms e tre le visualizzazioni:

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

Il metodo che viene eseguita quando un `Button` viene premuto crea una nuova istanza dell'argomento, imposta relativo `VerticalOptions` proprietà e lo aggiunge al `StackLayout`. I tre `Button` elementi condividono quindi la pagina con le viste create in modo dinamico:

[![X:Type Demo](consuming-images/typedemo-small.png "X:Type Demo")](consuming-images/typedemo-large.png#lightbox "X:Type Demo")

<a name="array" />

## <a name="xarray-markup-extension"></a>Estensione del markup x:Array

Il `x:Array` estensione di markup consente di definire una matrice nel markup. È supportato dal [ `ArrayExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/) (classe), che definisce due proprietà:

- `Type` di tipo `Type`, che indica il tipo di elementi nella matrice.
- `Items` di tipo `IList`, che è una raccolta di elementi stessi. Questa è la proprietà content del `ArrayExtension`.

Il `x:Array` estensione di markup stesso non viene mai visualizzato tra parentesi graffe. In alternativa, `x:Array` tag di inizio e fine delimitare l'elenco di elementi. Impostare il `Type` proprietà per un `x:Type` estensione di markup.

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

Il `ViewCell` creato un semplice `BoxView` per ogni voce di colore:

[![X:Array Demo](consuming-images/arraydemo-small.png "X:Array Demo")](consuming-images/arraydemo-large.png#lightbox "X:Array Demo")

Esistono diversi modi per specificare i singoli `Color` elementi di questa matrice. È possibile utilizzare un `x:Static` estensione di markup:

```xaml
<x:Static Member="Color.Blue" />
```

In alternativa, utilizzare `StaticResource` per recuperare un colore da un dizionario risorse:

```xaml
<StaticResource Key="myColor" />
```

Verso la fine di questo articolo, si otterrà un'estensione di markup XAML personalizzata che crea un nuovo valore di colore:

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

Quando si definiscono le matrici di tipi comuni come stringhe o numeri, utilizzare i tag elencati nella [ **passando gli argomenti del costruttore** ](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) articolo per delimitare i valori.

<a name="null" />

## <a name="xnull-markup-extension"></a>Estensione del markup x:Null

Il `x:Null` estensione di markup è supportato dal [ `NullExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/) classe. Dispone di alcuna proprietà ed è semplicemente l'equivalente XAML del linguaggio c# [ `null` ](/dotnet/csharp/language-reference/keywords/null/) (parola chiave).

Il `x:Null` estensione di markup è raramente necessaria e utilizzate raramente, ma se si trova un necessaria, sarà valeva che è presente.

Il **x: Null Demo** pagina viene illustrato uno scenario quando `x:Null` potrebbe essere opportuno. Si supponga di definisce implicita `Style` per `Label` che include un `Setter` che imposta il `FontFamily` proprietà a un nome di famiglia dipendente dalla piattaforma:

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

Quindi si scopre che per uno del `Label` elementi, si desidera che tutte le impostazioni delle proprietà nell'operatore implicito `Style` tranne che per il `FontFamily`, che si desidera rendere il valore predefinito. È possibile definire un altro `Style` per tale scopo, ma un approccio più semplice consiste semplicemente nell'impostazione di `FontFamily` proprietà dello specifico `Label` a `x:Null`, come illustrato nel centro `Label`.

Di seguito è riportato il programma in esecuzione su tre piattaforme:

[![Demo di x: Null](consuming-images/nulldemo-small.png "x: Null Demo")](consuming-images/nulldemo-large.png#lightbox "Demo x: Null")

Si noti che quattro del `Label` elementi hanno un tipo di carattere serif, ma il centro `Label` ha il tipo di carattere sans-serif predefinito.

## <a name="define-your-own-markup-extensions"></a>Definire estensioni di Markup

Se è stato riscontrato necessità di un'estensione di markup XAML che non è disponibile in xamarin. Forms, è possibile [crearne di proprie](creating.md).


## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup (esempio)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capitolo di estensioni di markup XAML di libro xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
