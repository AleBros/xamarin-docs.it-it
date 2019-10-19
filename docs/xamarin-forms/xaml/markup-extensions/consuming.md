---
title: Utilizzo di estensioni di markup XAML
description: Questo articolo illustra come usare le estensioni di markup XAML di Novell. Forms per migliorare la potenza e la flessibilità di XAML consentendo l'impostazione degli attributi degli elementi da diverse origini.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 03aaf471479a5113aade6bd3f34034afadfb538c
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "69887899"
---
# <a name="consuming-xaml-markup-extensions"></a>Utilizzo di estensioni di markup XAML

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Le estensioni di markup XAML consentono di migliorare la potenza e la flessibilità di XAML consentendo l'impostazione degli attributi degli elementi da diverse origini. Diverse estensioni di markup XAML fanno parte della specifica XAML 2009. Questi vengono visualizzati nei file XAML con il prefisso dello spazio dei nomi `x` personalizzato e sono comunemente definiti con questo prefisso. Questo articolo illustra le estensioni di markup seguenti:

- [`x:Static`](#static) : fare riferimento a proprietà statiche, campi o membri di enumerazione.
- [`x:Reference`](#reference) : fanno riferimento agli elementi denominati nella pagina.
- [`x:Type`](#type) : impostare un attributo su un oggetto `System.Type`.
- [`x:Array`](#array) : costruire una matrice di oggetti di un determinato tipo.
- [`x:Null`](#null) : impostare un attributo su un valore `null`.
- [`OnPlatform`](#onplatform) : personalizzare l'aspetto dell'interfaccia utente in base alla piattaforma.
- [`OnIdiom`](#onidiom) : personalizzare l'aspetto dell'interfaccia utente in base all'idioma del dispositivo in cui è in esecuzione l'applicazione.
- [`DataTemplate`](#datatemplate-markup-extension) : converte un tipo in un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).
- [`FontImage`](#fontimage-markup-extension) -Visualizza un'icona del tipo di carattere in qualsiasi visualizzazione in cui è possibile visualizzare una `ImageSource`.

Le estensioni di markup XAML aggiuntive sono state supportate in passato da altre implementazioni XAML e sono supportate anche da Novell. Forms. Queste informazioni sono descritte in modo più completo negli altri articoli:

- `StaticResource` &ndash; oggetti di riferimento da un dizionario risorse, come descritto nell'articolo [**dizionari risorse**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource` &ndash; rispondono alle modifiche apportate agli oggetti in un dizionario risorse, come descritto nell'articolo [**stili dinamici**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding` &ndash; stabilire un collegamento tra le proprietà di due oggetti, come descritto nell'articolo [**Data Binding**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding` &ndash; esegue data binding da un modello di controllo, come illustrato nell'articolo relativo all' [**associazione da un modello di controllo**](~/xamarin-forms/app-fundamentals/templates/control-templates/template-binding.md).

Il layout [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) usa l'estensione di markup personalizzata [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression). Questa estensione di markup è descritta nell'articolo [**sul relativelayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>Estensione del markup x:Static

L'estensione di markup `x:Static` è supportata dalla classe [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension) . La classe dispone di una singola proprietà denominata [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) di tipo `string` impostato sul nome di una costante pubblica, di una proprietà statica, di un campo statico o di un membro di enumerazione.

Un modo comune per usare `x:Static` consiste nel definire prima una classe con alcune costanti o variabili statiche, ad esempio questa piccola classe `AppConstants` nel programma [**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) :

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

La pagina **demo di x:static** illustra diversi modi per usare l'estensione di markup `x:Static`. L'approccio più dettagliato crea un'istanza della classe `StaticExtension` tra `Label.FontSize` tag elemento proprietà:

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

Il parser XAML consente inoltre di abbreviare la classe `StaticExtension` come `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Questa operazione può essere ulteriormente semplificata, ma la modifica introduce una nuova sintassi: consiste nell'inserire la classe `StaticExtension` e l'impostazione del membro tra parentesi graffe. L'espressione risultante viene impostata direttamente sull'attributo `FontSize`:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Si noti *che non ci sono* virgolette tra parentesi graffe. La proprietà `Member` di `StaticExtension` non è più un attributo XML. È invece parte dell'espressione per l'estensione di markup.

Così come è possibile abbreviare `x:StaticExtension` per `x:Static` quando viene usato come elemento oggetto, è anche possibile abbreviare l'espressione nell'espressione tra parentesi graffe:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

La classe `StaticExtension` dispone di un attributo `ContentProperty` che fa riferimento alla proprietà `Member`, che contrassegna questa proprietà come proprietà di contenuto predefinita della classe. Per le estensioni di markup XAML espresse con parentesi graffe, è possibile eliminare la `Member=` parte dell'espressione:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Questa è la forma più comune dell'estensione di markup `x:Static`.

La pagina **Demo statica** contiene altri due esempi. Il tag radice del file XAML contiene una dichiarazione dello spazio dei nomi XML per lo spazio dei nomi .NET `System`:

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

In questo modo è possibile impostare la dimensione del tipo di carattere `Label` sul campo statico `Math.PI`. Il risultato è un testo piuttosto piccolo, quindi la proprietà `Scale` è impostata su `Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

Nell'esempio finale viene visualizzato il valore `Device.RuntimePlatform`. La proprietà statica `Environment.NewLine` viene utilizzata per inserire un carattere di nuova riga tra i due oggetti `Span`:

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

L'estensione di markup `x:Reference` è supportata dalla classe [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) . La classe dispone di una singola proprietà denominata [`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) di tipo `string` impostato sul nome di un elemento nella pagina a cui è stato assegnato un nome con `x:Name`. Questa `Name` proprietà è la proprietà Content di `ReferenceExtension`, pertanto non è necessario `Name=` quando `x:Reference` viene visualizzato tra parentesi graffe.

L'estensione di markup `x:Reference` viene utilizzata esclusivamente con i data binding, descritti in modo più dettagliato nell' [**associazione dati**](~/xamarin-forms/app-fundamentals/data-binding/index.md)dell'articolo.

La pagina **demo di x:Reference** Mostra due usi di `x:Reference` con i data binding, il primo in cui viene usato per impostare la proprietà `Source` dell'oggetto `Binding` e il secondo in cui viene usato per impostare la proprietà `BindingContext` per due associazioni dati :

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

Entrambe le espressioni `x:Reference` utilizzano la versione abbreviata del nome della classe `ReferenceExtension` ed eliminano la `Name=` parte dell'espressione. Nel primo esempio, l'estensione di markup `x:Reference` è incorporata nell'estensione di markup `Binding`. Si noti che le impostazioni `Source` e `StringFormat` sono separate da virgole. Ecco il programma in esecuzione:

[![Demo di x:Reference](consuming-images/referencedemo-small.png "Demo di x:Reference")](consuming-images/referencedemo-large.png#lightbox "Demo di x:Reference")

<a name="type" />

## <a name="xtype-markup-extension"></a>Estensione del markup x:Type

L'estensione di markup `x:Type` è l'equivalente XAML della C# parola chiave [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/) . È supportato dalla classe [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension) , che definisce una proprietà denominata [`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) di tipo `string` che è impostata su un nome di classe o di struttura. L'estensione di markup `x:Type` restituisce l'oggetto [`System.Type`](xref:System.Type) della classe o della struttura. `TypeName` è la proprietà Content di `TypeExtension`, pertanto `TypeName=` non è obbligatorio quando `x:Type` viene visualizzato con parentesi graffe.

In Novell. Forms sono disponibili diverse proprietà con argomenti di tipo `Type`. Gli esempi includono la proprietà [`TargetType`](xref:Xamarin.Forms.Style.TargetType) di `Style` e l'attributo [x:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) usato per specificare gli argomenti nelle classi generiche. Tuttavia, il parser XAML esegue automaticamente l'operazione di `typeof` e l'estensione di markup `x:Type` non viene utilizzata in questi casi.

Un'unica posizione in cui *è necessario `x:Type` è con* l'estensione di markup `x:Array`, descritta nella [sezione successiva](#array).

L'estensione di markup `x:Type` è utile anche quando si crea un menu in cui ogni voce di menu corrisponde a un oggetto di un determinato tipo. È possibile associare un oggetto `Type` a ogni voce di menu, quindi creare un'istanza dell'oggetto quando la voce di menu è selezionata.

Questo è il modo in cui il menu di navigazione `MainPage` nel programma **estensioni di markup** funziona. Il file **MainPage. XAML** contiene un `TableView` con ogni `TextCell` corrispondente a una determinata pagina del programma:

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

Ogni proprietà `CommandParameter` è impostata su un'estensione di markup `x:Type` che fa riferimento a una delle altre pagine. La proprietà `Command` è associata a una proprietà denominata `NavigateCommand`. Questa proprietà viene definita nel file code-behind `MainPage`:

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

La proprietà `NavigateCommand` è un oggetto `Command` che implementa un comando Execute con un argomento di tipo `Type` &mdash; il valore di `CommandParameter`. Il metodo usa `Activator.CreateInstance` per creare un'istanza della pagina e quindi passa a essa. Il costruttore termina impostando il `BindingContext` della pagina su se stesso, consentendo al `Binding` `Command` di funzionare. Per altri dettagli su questo tipo di codice, vedere l'articolo relativo al [**Data Binding**](~/xamarin-forms/app-fundamentals/data-binding/index.md) e in particolare l'articolo sui [**comandi**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) .

La pagina **demo di x:Type** usa una tecnica simile per creare un'istanza di elementi Novell. Forms e aggiungerli a una `StackLayout`. Il file XAML è inizialmente costituito da tre `Button` elementi con le relative proprietà `Command` impostate su un `Binding` e le proprietà `CommandParameter` impostate su tipi di tre visualizzazioni Novell. Forms:

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

Il file code-behind definisce e inizializza la proprietà `CreateCommand`:

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

Il metodo eseguito quando viene premuto un `Button` crea una nuova istanza dell'argomento, ne imposta la proprietà `VerticalOptions` e la aggiunge al `StackLayout`. I tre elementi `Button` quindi condividono la pagina con le visualizzazioni create dinamicamente:

[![Demo di x:Type](consuming-images/typedemo-small.png "Demo di x:Type")](consuming-images/typedemo-large.png#lightbox "Demo di x:Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>Estensione del markup x:Array

L'estensione di markup `x:Array` consente di definire una matrice nel markup. È supportato dalla classe [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension) , che definisce due proprietà:

- `Type` di tipo `Type`, che indica il tipo degli elementi nella matrice.
- `Items` di tipo `IList`, ovvero una raccolta di elementi stessi. Si tratta della proprietà Content di `ArrayExtension`.

L'estensione di markup `x:Array` non viene mai visualizzata tra parentesi graffe. Al contrario, `x:Array` tag di inizio e di fine delimitano l'elenco di elementi. Impostare la proprietà `Type` su un'estensione di markup `x:Type`.

La pagina **demo di x:Array** illustra come usare `x:Array` per aggiungere elementi a una `ListView` impostando la proprietà `ItemsSource` su una matrice:

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

Il `ViewCell` crea una semplice `BoxView` per ogni voce di colore:

[![Demo di x:Array](consuming-images/arraydemo-small.png "Demo di x:Array")](consuming-images/arraydemo-large.png#lightbox "Demo di x:Array")

Esistono diversi modi per specificare i singoli elementi di `Color` in questa matrice. È possibile usare un'estensione di markup `x:Static`:

```xaml
<x:Static Member="Color.Blue" />
```

In alternativa, è possibile usare `StaticResource` per recuperare un colore da un dizionario risorse:

```xaml
<StaticResource Key="myColor" />
```

Alla fine di questo articolo verrà visualizzata un'estensione di markup XAML personalizzata che crea anche un nuovo valore di colore:

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

Quando si definiscono matrici di tipi comuni come stringhe o numeri, usare i tag elencati nell'articolo [**passare gli argomenti del costruttore**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) per delimitare i valori.

<a name="null" />

## <a name="xnull-markup-extension"></a>Estensione del markup x:Null

L'estensione di markup `x:Null` è supportata dalla classe [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension) . Non dispone di proprietà ed è semplicemente l'equivalente XAML della C# parola chiave [`null`](/dotnet/csharp/language-reference/keywords/null/) .

Il `x:Null` estensione di markup è raramente necessario e viene usato raramente, ma se si trova una necessità, si sarà contenti che esista.

La pagina **demo di x:null** illustra uno scenario in cui `x:Null` potrebbe essere utile. Si supponga di definire un `Style` implicito per `Label` che includa una `Setter` che imposta la proprietà `FontFamily` su un nome di famiglia dipendente dalla piattaforma:

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

Si scopre quindi che per uno degli elementi `Label` si desidera che tutte le impostazioni delle proprietà nel `Style` implicito eccetto il `FontFamily` che si desidera sia il valore predefinito. È possibile definire un altro `Style` a tale scopo, ma un approccio più semplice consiste semplicemente nell'impostare la proprietà `FontFamily` della `Label` particolare su `x:Null`, come illustrato nella `Label` centrale.

Ecco il programma in esecuzione:

[![Demo di x:Null](consuming-images/nulldemo-small.png "Demo di x:Null")](consuming-images/nulldemo-large.png#lightbox "Demo di x:Null")

Si noti che quattro degli elementi `Label` hanno un tipo di carattere serif, ma al centro `Label` è associato il tipo di carattere sans-serif predefinito.

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>Estensione di markup onplatform

Il `OnPlatform` estensione di markup consente di personalizzare l'aspetto dell'interfaccia utente in base alla piattaforma. Fornisce le stesse funzionalità delle classi [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) e [`On`](xref:Xamarin.Forms.On) , ma con una rappresentazione più concisa.

L'estensione di markup `OnPlatform` è supportata dalla classe [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) , che definisce le proprietà seguenti:

- `Default` di tipo `object`, che è possibile impostare su un valore predefinito da applicare alle proprietà che rappresentano le piattaforme.
- `Android` di tipo `object`, che è possibile impostare su un valore da applicare in Android.
- `GTK` di tipo `object`, che è possibile impostare su un valore da applicare alle piattaforme GTK.
- `iOS` di tipo `object`, che è possibile impostare su un valore da applicare in iOS.
- `macOS` di tipo `object`, che è possibile impostare su un valore da applicare in macOS.
- `Tizen` di tipo `object`, che è possibile impostare su un valore da applicare alla piattaforma Tizen.
- `UWP` di tipo `object`, che è possibile impostare su un valore da applicare al piattaforma UWP (Universal Windows Platform).
- `WPF` di tipo `object`, che è possibile impostare su un valore da applicare alla piattaforma di Windows Presentation Foundation.
- `Converter` di tipo `IValueConverter`, che è stato impostato su un'implementazione di `IValueConverter`.
- `ConverterParameter` di tipo `object`, che è stato impostato su un valore da passare all'implementazione di `IValueConverter`.

> [!NOTE]
> Il parser XAML consente di abbreviare la classe [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) come `OnPlatform`.

La proprietà `Default` è la proprietà Content di `OnPlatformExtension`. Pertanto, per le espressioni di markup XAML espresse con parentesi graffe, è possibile eliminare la `Default=` parte dell'espressione purché sia il primo argomento.

> [!IMPORTANT]
> Il parser XAML prevede che i valori del tipo corretto verranno forniti alle proprietà che utilizzano l'estensione di markup `OnPlatform`. Se è necessaria la conversione del tipo, l'estensione di markup `OnPlatform` tenterà di eseguirla usando i convertitori predefiniti forniti da Novell. Forms. Tuttavia, esistono alcune conversioni di tipi che non possono essere eseguite dai convertitori predefiniti e in questi casi è necessario impostare la proprietà `Converter` su un'implementazione di `IValueConverter`.

La pagina **demo di onplatform** Mostra come usare l'estensione di markup `OnPlatform`:

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

In questo esempio, tutte e tre le espressioni `OnPlatform` utilizzano la versione abbreviata del nome della classe `OnPlatformExtension`. Le tre `OnPlatform` estensioni di markup impostano le proprietà [`Color`](xref:Xamarin.Forms.BoxView.Color), [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) del [`BoxView`](xref:Xamarin.Forms.BoxView) su valori diversi in iOS, Android e UWP. Le estensioni di markup forniscono anche valori predefiniti per queste proprietà sulle piattaforme non specificate, eliminando al tempo stesso la `Default=` parte dell'espressione. Si noti che le proprietà dell'estensione di markup impostate sono separate da virgole.

Ecco il programma in esecuzione:

[![Demo su piattaforma](consuming-images/onplatformdemo-small.png "Demo su piattaforma")](consuming-images/onplatformdemo-large.png#lightbox "Demo su piattaforma")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>Estensione di markup onidiom

L'estensione di markup `OnIdiom` consente di personalizzare l'aspetto dell'interfaccia utente in base all'idioma del dispositivo in cui è in esecuzione l'applicazione. È supportato dalla classe [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) , che definisce le proprietà seguenti:

- `Default` di tipo `object`, che è possibile impostare su un valore predefinito da applicare alle proprietà che rappresentano gli idiomi dei dispositivi.
- `Phone` di tipo `object`, che è possibile impostare su un valore da applicare ai telefoni.
- `Tablet` di tipo `object`, che è possibile impostare su un valore da applicare ai tablet.
- `Desktop` di tipo `object`, che è possibile impostare su un valore da applicare sulle piattaforme desktop.
- `TV` di tipo `object`, che è possibile impostare su un valore da applicare sulle piattaforme TV.
- `Watch` di tipo `object`, che è stato impostato su un valore da applicare sulle piattaforme Watch.
- `Converter` di tipo `IValueConverter`, che è stato impostato su un'implementazione di `IValueConverter`.
- `ConverterParameter` di tipo `object`, che è stato impostato su un valore da passare all'implementazione di `IValueConverter`.

> [!NOTE]
> Il parser XAML consente di abbreviare la classe [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) come `OnIdiom`.

La proprietà `Default` è la proprietà Content di `OnIdiomExtension`. Pertanto, per le espressioni di markup XAML espresse con parentesi graffe, è possibile eliminare la `Default=` parte dell'espressione purché sia il primo argomento.

> [!IMPORTANT]
> Il parser XAML prevede che i valori del tipo corretto verranno forniti alle proprietà che utilizzano l'estensione di markup `OnIdiom`. Se è necessaria la conversione del tipo, l'estensione di markup `OnIdiom` tenterà di eseguirla usando i convertitori predefiniti forniti da Novell. Forms. Tuttavia, esistono alcune conversioni di tipi che non possono essere eseguite dai convertitori predefiniti e in questi casi è necessario impostare la proprietà `Converter` su un'implementazione di `IValueConverter`.

La pagina **demo Onidioma** Mostra come usare l'estensione di markup `OnIdiom`:

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

In questo esempio, tutte e tre le espressioni `OnIdiom` utilizzano la versione abbreviata del nome della classe `OnIdiomExtension`. Le tre `OnIdiom` estensioni di markup impostano le proprietà [`Color`](xref:Xamarin.Forms.BoxView.Color), [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) del [`BoxView`](xref:Xamarin.Forms.BoxView) su valori diversi sugli idiomi Phone, tablet e desktop. Le estensioni di markup forniscono anche valori predefiniti per queste proprietà sugli idiomi non specificati, eliminando al tempo stesso la `Default=` parte dell'espressione. Si noti che le proprietà dell'estensione di markup impostate sono separate da virgole.

Ecco il programma in esecuzione:

[![Demo onidioma](consuming-images/onidiomdemo-small.png "Demo onidioma")](consuming-images/onidiomdemo-large.png#lightbox "Demo onidioma")

## <a name="datatemplate-markup-extension"></a>Estensione di markup DataTemplate

L'estensione di markup `DataTemplate` consente di convertire un tipo in un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). È supportato dalla classe `DataTemplateExtension`, che definisce una proprietà `TypeName`, di tipo `string`, impostata sul nome del tipo da convertire in un `DataTemplate`. La proprietà `TypeName` è la proprietà Content di `DataTemplateExtension`. Pertanto, per le espressioni di markup XAML espresse con parentesi graffe, è possibile eliminare la `TypeName=` parte dell'espressione.

> [!NOTE]
> Il parser XAML consente di abbreviare la classe `DataTemplateExtension` come `DataTemplate`.

Un utilizzo tipico di questa estensione di markup si trova in un'applicazione shell, come illustrato nell'esempio seguente:

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

In questo esempio `MonkeysPage` viene convertito da un [`ContentPage`](xref:Xamarin.Forms.ContentPage) a un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), che viene impostato come valore della proprietà `ShellContent.ContentTemplate`. In questo modo si garantisce che `MonkeysPage` venga creato solo quando si verifica la navigazione alla pagina, anziché all'avvio dell'applicazione.

Per ulteriori informazioni sulle applicazioni shell, vedere [Novell. Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="fontimage-markup-extension"></a>Estensione di markup FontImage

L'estensione di markup `FontImage` consente di visualizzare un'icona del tipo di carattere in qualsiasi visualizzazione in grado di visualizzare una `ImageSource`. Fornisce le stesse funzionalità della classe `FontImageSource`, ma con una rappresentazione più concisa.

L'estensione di markup `FontImage` è supportata dalla classe `FontImageExtension`, che definisce le proprietà seguenti:

- `FontFamily` di tipo `string`, la famiglia di caratteri a cui appartiene l'icona del tipo di carattere.
- `Glyph` di tipo `string`, il valore del carattere Unicode dell'icona del tipo di carattere.
- `Color` di tipo `Color`, il colore da utilizzare quando si visualizza l'icona del tipo di carattere.
- `Size` di tipo `double`, ovvero le dimensioni, in unità indipendenti dal dispositivo, dell'icona del tipo di carattere di cui è stato eseguito il rendering.

> [!NOTE]
> Il parser XAML consente di abbreviare la classe `FontImageExtension` come `FontImage`.

La proprietà `Glyph` è la proprietà Content di `FontImageExtension`. Pertanto, per le espressioni di markup XAML espresse con parentesi graffe, è possibile eliminare la `Glyph=` parte dell'espressione purché sia il primo argomento.

La pagina **demo di FontImage** illustra come usare l'estensione di markup `FontImage`:

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

In questo esempio viene usata la versione abbreviata del nome della classe `FontImageExtension` per visualizzare un'icona di XBox, dalla famiglia di caratteri Ionicons, in una [`Image`](xref:Xamarin.Forms.Image). L'espressione usa anche l'estensione di markup `OnPlatform` per specificare valori di proprietà `FontFamily` diversi in iOS e Android. Inoltre, il `Glyph=` parte dell'espressione viene eliminato e le proprietà dell'estensione di markup impostate sono separate da virgole. Si noti che, mentre il carattere Unicode per l'icona è `\uf30c`, deve essere preceduto da un carattere di escape in XAML e pertanto viene `&#xf30c;`.

Ecco il programma in esecuzione:

[![Screenshot dell'estensione di markup FontImage](consuming-images/fontimagedemo.png "Demo di FontImage")](consuming-images/fontimagedemo-large.png#lightbox "Demo di FontImage")

Per informazioni sulla visualizzazione delle icone dei tipi di carattere specificando i dati dell'icona del tipo di carattere in un oggetto `FontImageSource`, vedere [visualizzare le icone dei tipi](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)

## <a name="define-your-own-markup-extensions"></a>Definire le proprie estensioni di markup

Se è stata rilevata una necessità di un'estensione di markup XAML non disponibile in Novell. Forms, è possibile [crearne una personalizzata](creating.md).

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capitolo delle estensioni di markup XAML dal libro Novell. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Shell Novell. Forms](~/xamarin-forms/app-fundamentals/shell/index.md).
