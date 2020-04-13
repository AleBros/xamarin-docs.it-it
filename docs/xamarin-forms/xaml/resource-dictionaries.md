---
title: Dizionari risorse Xamarin.Forms
description: Le risorse XAML Xamarin.Forms sono oggetti che possono essere condivisi e riutilizzati in un'applicazione Xamarin.Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
ms.custom: video
ms.openlocfilehash: 8dd3c7f36ddd436a812927816a1326dbb7c48341
ms.sourcegitcommit: ee9e48e2ec643915f42a6c1641077970ae20cb17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80523239"
---
# <a name="xamarinforms-resource-dictionaries"></a>Dizionari risorse Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

A [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) è un archivio per le risorse utilizzate da un'applicazione Xamarin.Forms. Risorse tipiche archiviate `ResourceDictionary` in [stili](~/xamarin-forms/user-interface/styles/index.md)di inclusione , [modelli](~/xamarin-forms/app-fundamentals/templates/control-template.md)di controllo , [modelli di dati,](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)colori e convertitori.

In XAML, le risorse [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) archiviate in un oggetto possono `StaticResource` essere `DynamicResource` referenziate e applicate agli elementi usando l'estensione di markup o . Nel linguaggio C, le risorse `ResourceDictionary` possono anche essere definite in un oggetto e quindi viene fatto riferimento e applicate agli elementi utilizzando un indicizzatore basato su stringa. Tuttavia, non c'è `ResourceDictionary` alcun vantaggio per l'utilizzo di un in C , come gli oggetti condivisi possono essere archiviati come campi o proprietà e accessibile direttamente senza dover prima recuperarli da un dizionario.

## <a name="create-resources-in-xaml"></a>Creare risorse in XAMLCreate resources in XAML

Ogni [`VisualElement`](xref:Xamarin.Forms.VisualElement) oggetto derivato ha [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) proprietà, che è un che può contenere risorse. Analogamente, [`Application`](xref:Xamarin.Forms.Application) un oggetto [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) derivato ha [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) una proprietà, che è un che può contenere risorse.

Un'applicazione Xamarin.Forms contiene solo [`Application`](xref:Xamarin.Forms.Application)una classe che deriva da , [`VisualElement`](xref:Xamarin.Forms.VisualElement)ma spesso utilizza molte classi che derivano da , tra cui pagine, layout e controlli . La proprietà `Resources` di ognuno di [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) questi oggetti può essere impostata su un contenitore di risorse. Scelta della posizione `ResourceDictionary` in cui inserire un determinato impatto su cui è possibile utilizzare le risorse:Choosing where to put a particular impacts where the resources can be used:

- Le risorse `ResourceDictionary` in un oggetto collegato [`Button`](xref:Xamarin.Forms.Button) a [`Label`](xref:Xamarin.Forms.Label) una visualizzazione, ad esempio o che possono essere applicate solo a quel particolare oggetto.
- Le risorse `ResourceDictionary` in un layout [`StackLayout`](xref:Xamarin.Forms.StackLayout) allegato a un layout, ad esempio o [`Grid`](xref:Xamarin.Forms.Grid) possono essere applicate al layout e a tutti gli elementi figlio di tale layout.
- Le risorse `ResourceDictionary` in un oggetto definito a livello di pagina possono essere applicate alla pagina e a tutti i relativi elementi figlio.
- Le risorse `ResourceDictionary` in un oggetto definito a livello di applicazione possono essere applicate in tutta l'applicazione.

Ad eccezione degli stili impliciti, ogni risorsa nel dizionario risorse `x:Key` deve avere una chiave di stringa univoca definita con l'attributo .

Il codice XAML seguente mostra `ResourceDictionary` le risorse definite in un livello di applicazione nel file **App.xaml:The following** XAML shows resources defined in an application level in the App.xaml file:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.App">
    <Application.Resources>

        <Thickness x:Key="PageMargin">20</Thickness>

        <!-- Colors -->
        <Color x:Key="AppBackgroundColor">AliceBlue</Color>
        <Color x:Key="NavigationBarColor">#1976D2</Color>
        <Color x:Key="NavigationBarTextColor">White</Color>
        <Color x:Key="NormalTextColor">Black</Color>

        <!-- Implicit styles -->
        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{StaticResource NavigationBarColor}" />
            <Setter Property="BarTextColor"
                    Value="{StaticResource NavigationBarTextColor}" />
        </Style>

        <Style TargetType="{x:Type ContentPage}"
               ApplyToDerivedTypes="True">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>

    </Application.Resources>
</Application>
```

In questo esempio, il dizionario risorse definisce una [`Thickness`](xref:Xamarin.Forms.Thickness) risorsa, più [`Color`](xref:Xamarin.Forms.Color) risorse e due risorse implicite. [`Style`](xref:Xamarin.Forms.Style) Per ulteriori informazioni `App` sulla classe , vedere Classe app [Xamarin.Forms](~/xamarin-forms/app-fundamentals/application-class.md).

> [!NOTE]
> È anche valido inserire tutte le `ResourceDictionary` risorse tra tag espliciti. Tuttavia, poiché Xamarin.Forms `ResourceDictionary` 3.0 i tag non sono necessari. Al contrario, l'oggetto `ResourceDictionary` viene creato automaticamente ed `Resources` è possibile inserire le risorse direttamente tra i tag dell'elemento proprietà.

## <a name="consume-resources-in-xaml"></a>Usare le risorse in XAMLConsume resources in XAML

Ogni risorsa dispone di una `x:Key` chiave specificata utilizzando l'attributo , che diventa la chiave del dizionario nell'oggetto `ResourceDictionary`. La chiave viene utilizzata per [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) fare [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) riferimento [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) a una risorsa dall'estensione con o di markup.

L'estensione `StaticResource` di markup `DynamicResource` è simile all'estensione di markup in quanto entrambi usano una chiave del dizionario per fare riferimento a un valore da un dizionario risorse. Tuttavia, `StaticResource` mentre l'estensione di markup `DynamicResource` esegue una singola ricerca nel dizionario, l'estensione di markup mantiene un collegamento alla chiave del dizionario. Pertanto, se la voce del dizionario associata alla chiave viene sostituita, la modifica viene applicata all'elemento visivo. Ciò consente di apportare modifiche alle risorse di runtime in un'applicazione. Per ulteriori informazioni sulle estensioni di markup, consultate [Estensioni di markup XAML.](~/xamarin-forms/xaml/markup-extensions/index.md)

L'esempio XAML seguente mostra come usare le risorse [`StackLayout`](xref:Xamarin.Forms.StackLayout)e definisce anche risorse aggiuntive in un:The following XAML example shows how to consume resources, and also defines additional resources in a :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.HomePage"
             Title="Home Page">
    <StackLayout Margin="{StaticResource PageMargin}">
        <StackLayout.Resources>
            <!-- Implicit style -->
            <Style TargetType="Button">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
            </Style>
        </StackLayout.Resources>

        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries." />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked" />
    </StackLayout>
</ContentPage>
```

In questo esempio, l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) utilizza lo stile implicito definito nel dizionario risorse a livello di applicazione. L'oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) utilizza `PageMargin` la risorsa definita nel dizionario [`Button`](xref:Xamarin.Forms.Button) risorse a livello di [`StackLayout`](xref:Xamarin.Forms.StackLayout) applicazione, mentre l'oggetto utilizza lo stile implicito definito nel dizionario risorse. Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![Utilizzo di risorse ResourceDictionaryConsuming ResourceDictionary Resources](resource-dictionaries-images/consuming.png "Utilizzo delle risorse ResourceDictionaryConsuming ResourceDictionary resources")](resource-dictionaries-images/consuming-large.png#lightbox "Utilizzo delle risorse ResourceDictionaryConsuming ResourceDictionary resources")

> [!IMPORTANT]
> Le risorse specifiche di una singola pagina non devono essere incluse in un dizionario risorse a livello di applicazione, in quanto tali risorse verranno analizzate all'avvio dell'applicazione anziché quando richiesto da una pagina. Per ulteriori informazioni, vedere [Ridurre le dimensioni del dizionario risorse dell'applicazione](~/xamarin-forms/deploy-test/performance.md).

## <a name="resource-lookup-behavior"></a>Comportamento di ricerca delle risorse

Il processo di ricerca seguente si verifica [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) quando si fa riferimento a una risorsa con l'estensione di markup o:The following lookup process occurs when a resource is referenced with the or markup extension:

- La chiave richiesta viene verificata nel dizionario risorse, se esistente, per l'elemento che imposta la proprietà. Se la chiave richiesta viene trovata, viene restituito il relativo valore e il processo di ricerca viene terminato.
- Se non viene trovata una corrispondenza, il processo di ricerca esegue la ricerca nella struttura ad albero visuale verso l'alto, controllando il dizionario risorse di ogni elemento padre. Se la chiave richiesta viene trovata, viene restituito il relativo valore e il processo di ricerca viene terminato. In caso contrario, il processo continua verso l'alto fino a quando non viene raggiunto l'elemento radice.
- Se non viene trovata una corrispondenza nell'elemento radice, viene esaminato il dizionario risorse a livello di applicazione.
- Se una corrispondenza non viene `XamlParseException` ancora trovata, viene generata un'eccezione.

Pertanto, quando il parser `StaticResource` `DynamicResource` XAML rileva un'estensione di markup o, cerca una chiave corrispondente spostandosi verso l'alto nella struttura ad albero visuale, usando la prima corrispondenza trovata. Se la ricerca termina in corrispondenza della pagina e la chiave `ResourceDictionary` non è `App` ancora stata trovata, il parser XAML cerca l'oggetto associato all'oggetto. Se la chiave non viene ancora trovata, viene generata un'eccezione.

## <a name="override-resources"></a>Eseguire l'override delle risorse

Quando le risorse condividono le chiavi, le risorse definite più in basso nella struttura ad albero visuale avranno la precedenza su quelle definite più in alto. Ad esempio, `AppBackgroundColor` l'impostazione di una risorsa `AliceBlue` a livello `AppBackgroundColor` di `Teal`applicazione verrà sottoposta a override da una risorsa a livello di pagina impostata su . Analogamente, una `AppBackgroundColor` risorsa a livello di `AppBackgroundColor` pagina verrà sostituita da una risorsa a livello di controllo.

## <a name="stand-alone-resource-dictionaries"></a>Dizionari risorse autonomi

Una classe [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) derivata da può anche essere in un file autonomo separato. Il file risultante può quindi essere condiviso tra le applicazioni.

Per creare un file di questo tipo, aggiungere un nuovo **elemento Visualizzazione contenuto** o Pagina **contenuto** al progetto, ma non una **visualizzazione contenuto** o una **pagina contenuto** con solo un file C . Eliminare il file code-behind e nel file XAML modificare [`ContentView`](xref:Xamarin.Forms.ContentView) il [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)nome della classe base da o in . Rimuovere inoltre l'attributo `x:Class` dal tag radice del file.

L'esempio XAML [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) seguente mostra un file **MyResourceDictionary.xaml**denominato:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}"
                       TextColor="{StaticResource NormalTextColor}"
                       FontAttributes="Bold" />
                <Label Grid.Column="1"
                       Text="{Binding Age}"
                       TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2"
                       Text="{Binding Location}"
                       TextColor="{StaticResource NormalTextColor}"
                       HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

In questo esempio, l'oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contiene una singola [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)risorsa, ovvero un oggetto di tipo . **MyResourceDictionary.xaml** può essere utilizzato unendolo in un altro dizionario risorse.

## <a name="merged-resource-dictionaries"></a>Dizionari risorse uniti

I dizionari risorse uniti [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) combinano `ResourceDictionary`uno o più oggetti in un altro oggetto .

### <a name="merge-local-resource-dictionaries"></a>Unire i dizionari risorse locali

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) file locale può `ResourceDictionary` essere unito `ResourceDictionary` a [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) un altro creando un oggetto la cui proprietà è impostata sul nome del file XAML con le risorse:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <!-- Add more resources here -->
        <ResourceDictionary Source="MyResourceDictionary.xaml" />
        <!-- Add more resources here -->
    </ContentPage.Resources>
    ...
</ContentPage>
```

Questa sintassi non `MyResourceDictionary` crea un'istanza della classe. Al contrario, fa riferimento al file XAML. Per questo motivo, [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) quando si imposta la proprietà, non `x:Class` è necessario un file code-behind e l'attributo può essere rimosso dal tag radice del file **MyResourceDictionary.xaml.**

> [!IMPORTANT]
> La [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) proprietà può essere impostata solo da XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Unire dizionari risorse da altri assembly

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) oggetto può anche `ResourceDictionary` essere unito [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) in un `ResourceDictionary`altro aggiungendolo alla proprietà dell'oggetto . Questa tecnica consente l'unione di dizionari risorse, indipendentemente dall'assembly in cui risiedono. Per unire dizionari risorse [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) da assembly esterni, è necessario che un'azione di compilazione `x:Class` sia impostata su **EmbeddedResource**, disponga di un file code-behind e definisca l'attributo nel tag radice del file.

> [!WARNING]
> La [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) classe definisce [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) anche una proprietà. Tuttavia, questa proprietà è stata deprecata e non deve più essere utilizzata.

Nell'esempio di codice seguente vengono [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) illustrati due dizionari [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)risorse aggiunti alla raccolta di un livello di pagina:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo"
             xmlns:theme="clr-namespace:MyThemes;assembly=MyThemes">
    <ContentPage.Resources>
        <ResourceDictionary>
            <!-- Add more resources here -->
            <ResourceDictionary.MergedDictionaries>
                <!-- Add more resource dictionaries here -->
                <local:MyResourceDictionary />
                <theme:LightTheme />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
            <!-- Add more resources here -->
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

In questo esempio, un dizionario risorse dello stesso assembly e un dizionario risorse da un assembly esterno vengono uniti nel dizionario risorse a livello di pagina. Inoltre, è anche possibile `ResourceDictionary` aggiungere [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) altri oggetti all'interno dei tag dell'elemento proprietà e altre risorse all'esterno di tali tag.

> [!IMPORTANT]
> In un `MergedDictionaries` [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)oggetto , può essere presente un solo `ResourceDictionary` tag elemento proprietà, ma è possibile inserirvi tutti gli oggetti necessari.

Quando [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) le risorse `x:Key` unite condividono valori di attributo identici, Xamarin.Forms utilizza la seguente precedenza delle risorse:

1. Risorse locali per il dizionario risorse.
1. Le risorse contenute nei dizionari risorse `MergedDictionaries` che sono stati uniti tramite `MergedDictionaries` la raccolta, nell'ordine inverso sono elencate nella proprietà .

> [!NOTE]
> La ricerca di dizionari risorse può essere un'attività che richiede un utilizzo intensivo del calcolo se un'applicazione contiene più dizionari risorse di grandi dimensioni. Pertanto, per evitare ricerche non necessarie, è necessario assicurarsi che ogni pagina in un'applicazione utilizzi solo i dizionari risorse appropriati per la pagina.

## <a name="related-links"></a>Collegamenti correlati

- [Dizionari risorse (esempio)Resource Dictionaries (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Estensioni di markup XAMLXAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Stili di Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
