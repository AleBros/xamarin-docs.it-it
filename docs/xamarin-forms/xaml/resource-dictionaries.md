---
title: Xamarin.Formsdizionari risorse
description: Xamarin.FormsLe risorse XAML sono oggetti che possono essere condivisi e riutilizzati in un' Xamarin.Forms applicazione.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: a1c7cfd4a0f3549b11ac51dc13b40da552f6b758
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139412"
---
# <a name="xamarinforms-resource-dictionaries"></a>Xamarin.Formsdizionari risorse

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) è un repository per le risorse utilizzate da un' Xamarin.Forms applicazione. Le risorse tipiche archiviate in un `ResourceDictionary` includono [stili](~/xamarin-forms/user-interface/styles/index.md), [modelli di controllo](~/xamarin-forms/app-fundamentals/templates/control-template.md), [modelli di dati](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), colori e convertitori.

In XAML, è possibile fare riferimento alle risorse archiviate in un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e applicarle agli elementi usando `StaticResource` l' `DynamicResource` estensione di markup o. In C# è anche possibile definire le risorse in un oggetto `ResourceDictionary` e quindi farvi riferimento e applicarle agli elementi usando un indicizzatore basato su stringa. Tuttavia, l'uso di un in C# è leggermente vantaggioso `ResourceDictionary` , perché gli oggetti condivisi possono essere archiviati come campi o proprietà ed è possibile accedervi direttamente senza dover prima recuperarli da un dizionario.

## <a name="create-resources-in-xaml"></a>Creare risorse in XAML

Ogni [`VisualElement`](xref:Xamarin.Forms.VisualElement) oggetto derivato ha una [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) proprietà, che [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) può contenere risorse. Analogamente, un [`Application`](xref:Xamarin.Forms.Application) oggetto derivato ha una [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) proprietà, che [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) può contenere risorse.

Un' Xamarin.Forms applicazione contiene solo classi che derivano da [`Application`](xref:Xamarin.Forms.Application) , ma spesso usa molte classi che derivano da [`VisualElement`](xref:Xamarin.Forms.VisualElement) , incluse le pagine, i layout e i controlli. Uno di questi oggetti può avere la `Resources` proprietà impostata su un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contenente le risorse. Scelta della posizione in cui inserire un particolare `ResourceDictionary` effetto in cui è possibile usare le risorse:

- Le risorse in un `ResourceDictionary` oggetto collegato a una vista, ad esempio [`Button`](xref:Xamarin.Forms.Button) o, [`Label`](xref:Xamarin.Forms.Label) possono essere applicate solo a tale oggetto specifico.
- Le risorse in un oggetto `ResourceDictionary` collegato a un layout, ad esempio [`StackLayout`](xref:Xamarin.Forms.StackLayout) o, [`Grid`](xref:Xamarin.Forms.Grid) possono essere applicate al layout e a tutti gli elementi figlio di tale layout.
- Le risorse in un oggetto `ResourceDictionary` definito a livello di pagina possono essere applicate alla pagina e a tutti i relativi elementi figlio.
- Le risorse in un oggetto `ResourceDictionary` definito a livello di applicazione possono essere applicate all'interno dell'applicazione.

Fatta eccezione per gli stili impliciti, ogni risorsa nel dizionario risorse deve avere una chiave di stringa univoca definita con l' `x:Key` attributo.

Il codice XAML seguente mostra le risorse definite a livello di applicazione `ResourceDictionary` nel file **app. XAML** :

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

In questo esempio il dizionario risorse definisce una [`Thickness`](xref:Xamarin.Forms.Thickness) risorsa, più [`Color`](xref:Xamarin.Forms.Color) risorse e due risorse implicite [`Style`](xref:Xamarin.Forms.Style) . Per ulteriori informazioni sulla `App` classe, vedere [ Xamarin.Forms classe App](~/xamarin-forms/app-fundamentals/application-class.md).

> [!NOTE]
> È anche possibile inserire tutte le risorse tra tag espliciti `ResourceDictionary` . Tuttavia, poiché Xamarin.Forms 3,0 i `ResourceDictionary` tag non sono obbligatori. Al contrario, l' `ResourceDictionary` oggetto viene creato automaticamente ed è possibile inserire le risorse direttamente tra i `Resources` tag dell'elemento proprietà.

## <a name="consume-resources-in-xaml"></a>Utilizzare le risorse in XAML

Ogni risorsa ha una chiave specificata usando l' `x:Key` attributo, che diventa la relativa chiave del dizionario in `ResourceDictionary` . La chiave viene usata per fare riferimento a una risorsa da [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) con [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) l' [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) estensione di markup o.

L' `StaticResource` estensione di markup è simile all' `DynamicResource` estensione di markup in quanto entrambi usano una chiave del dizionario per fare riferimento a un valore da un dizionario risorse. Tuttavia, mentre l' `StaticResource` estensione di markup esegue una singola ricerca nel dizionario, l' `DynamicResource` estensione di markup mantiene un collegamento alla chiave del dizionario. Pertanto, se la voce del dizionario associata alla chiave viene sostituita, la modifica viene applicata all'elemento visivo. Ciò consente di apportare modifiche alle risorse di runtime in un'applicazione. Per altre informazioni sulle estensioni di markup, vedere [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md).

Nell'esempio di codice XAML riportato di seguito viene illustrato come utilizzare le risorse e vengono definite anche risorse aggiuntive in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

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

In questo esempio, l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto utilizza lo stile implicito definito nel dizionario risorse a livello di applicazione. L' [`StackLayout`](xref:Xamarin.Forms.StackLayout) oggetto utilizza la `PageMargin` risorsa definita nel dizionario risorse a livello di applicazione, mentre l' [`Button`](xref:Xamarin.Forms.Button) oggetto utilizza lo stile implicito definito nel [`StackLayout`](xref:Xamarin.Forms.StackLayout) dizionario risorse. Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![Utilizzo di risorse ResourceDictionary](resource-dictionaries-images/consuming.png "Utilizzo di risorse ResourceDictionary")](resource-dictionaries-images/consuming-large.png#lightbox "Utilizzo di risorse ResourceDictionary")

> [!IMPORTANT]
> Le risorse specifiche di una singola pagina non devono essere incluse in un dizionario risorse a livello di applicazione, in quanto tali risorse verranno analizzate all'avvio dell'applicazione anziché quando richiesto da una pagina. Per altre informazioni, vedere [ridurre le dimensioni del dizionario risorse dell'applicazione](~/xamarin-forms/deploy-test/performance.md).

## <a name="resource-lookup-behavior"></a>Comportamento di ricerca delle risorse

Il processo di ricerca seguente si verifica quando viene fatto riferimento a una risorsa con l' [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) estensione di markup o:

- La chiave richiesta viene verificata nel dizionario risorse, se esistente, per l'elemento che imposta la proprietà. Se la chiave richiesta viene trovata, il relativo valore viene restituito e il processo di ricerca termina.
- Se non viene trovata una corrispondenza, il processo di ricerca esegue la ricerca verso l'alto nella struttura ad albero visuale, controllando il dizionario risorse di ogni elemento padre. Se la chiave richiesta viene trovata, il relativo valore viene restituito e il processo di ricerca termina. In caso contrario, il processo continua verso l'alto fino a quando non viene raggiunto l'elemento radice.
- Se non viene trovata una corrispondenza nell'elemento radice, viene esaminato il dizionario risorse a livello di applicazione.
- Se non viene trovata alcuna corrispondenza, `XamlParseException` viene generata un'eccezione.

Pertanto, quando il parser XAML rileva un' `StaticResource` `DynamicResource` estensione di markup o, Cerca una chiave corrispondente passando attraverso la struttura ad albero visuale, usando la prima corrispondenza trovata. Se questa ricerca termina in corrispondenza della pagina e la chiave non è ancora stata trovata, il parser XAML cerca nell' `ResourceDictionary` oggetto collegato all' `App` oggetto. Se la chiave non viene ancora trovata, viene generata un'eccezione.

## <a name="override-resources"></a>Sostituisci risorse

Quando le risorse condividono chiavi, le risorse definite più in basso nella struttura ad albero visuale avranno la precedenza rispetto a quelle definite più in alto. Ad esempio, l'impostazione di una `AppBackgroundColor` risorsa su `AliceBlue` a livello di applicazione verrà sostituita da una risorsa a livello di pagina `AppBackgroundColor` impostata su `Teal` . Analogamente, una risorsa a livello `AppBackgroundColor` di pagina verrà sottoposta a override da una risorsa a livello di controllo `AppBackgroundColor` .

## <a name="stand-alone-resource-dictionaries"></a>Dizionari risorse autonome

Una classe derivata da [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) può inoltre trovarsi in un file autonomo separato. Il file risultante può quindi essere condiviso tra le applicazioni.

Per creare un file di questo tipo, aggiungere una nuova **visualizzazione contenuto** o elemento della **pagina contenuto** al progetto (ma non una **visualizzazione contenuto** o una **pagina contenuto** con solo un file C#). Eliminare il file code-behind e nel file XAML modificare il nome della classe di base da [`ContentView`](xref:Xamarin.Forms.ContentView) o [`ContentPage`](xref:Xamarin.Forms.ContentPage) a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Rimuovere inoltre l' `x:Class` attributo dal tag radice del file.

Nell'esempio di codice XAML seguente viene illustrato un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) denominato **ResourceDictionary. XAML**:

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

In questo esempio, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contiene una sola risorsa, che è un oggetto di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . È possibile utilizzare il file **ResourceDictionary. XAML** unendolo in un altro dizionario risorse.

## <a name="merged-resource-dictionaries"></a>Dizionari risorse uniti

I dizionari risorse Uniti combinano uno o più [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) oggetti in un altro `ResourceDictionary` .

### <a name="merge-local-resource-dictionaries"></a>Unisci dizionari risorse locali

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)È possibile unire un file locale in un altro creando `ResourceDictionary` un `ResourceDictionary` oggetto la cui [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) proprietà è impostata sul nome file del file XAML con le risorse:

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

Questa sintassi non crea un'istanza della `MyResourceDictionary` classe. Al contrario, fa riferimento al file XAML. Per questo motivo, quando si imposta la [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) proprietà, non è necessario un file code-behind e l' `x:Class` attributo può essere rimosso dal tag radice del file **ResourceDictionary. XAML** .

> [!IMPORTANT]
> La [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) proprietà può essere impostata solo da XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Unisci dizionari risorse da altri assembly

È anche possibile unire un oggetto a un altro aggiungendolo alla [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `ResourceDictionary` [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) proprietà di `ResourceDictionary` . Questa tecnica consente di unire i dizionari risorse, indipendentemente dall'assembly in cui risiedono. L'Unione di dizionari risorse da assembly esterni richiede [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) che un'azione di compilazione sia impostata su **EmbeddedResource**, disponga di un file code-behind e definisca l' `x:Class` attributo nel tag radice del file.

> [!WARNING]
> La [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) classe definisce anche una [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) Proprietà. Tuttavia, questa proprietà è stata deprecata e non deve più essere utilizzata.

L'esempio di codice seguente illustra l'aggiunta di due dizionari risorse alla [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) raccolta di un livello di pagina [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

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

In questo esempio, un dizionario risorse dello stesso assembly e un dizionario risorse da un assembly esterno vengono uniti nel dizionario risorse a livello di pagina. Inoltre, è possibile aggiungere altri `ResourceDictionary` oggetti all'interno dei [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) tag dell'elemento proprietà e altre risorse esterne a tali tag.

> [!IMPORTANT]
> Può essere presente un solo `MergedDictionaries` tag di elemento Property in un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , ma è possibile inserire tutti `ResourceDictionary` gli oggetti in tale oggetto come richiesto.

Quando [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) le risorse unite condividono `x:Key` valori di attributo identici, Xamarin.Forms Usa la precedenza delle risorse seguenti:

1. Risorse locali del dizionario risorse.
1. Le risorse contenute nei dizionari risorse che sono state unite tramite la `MergedDictionaries` raccolta, nell'ordine inverso sono elencate nella `MergedDictionaries` Proprietà.

> [!NOTE]
> La ricerca di dizionari risorse può essere un'attività a elevato utilizzo di calcolo se un'applicazione contiene più dizionari risorse di grandi dimensioni. Pertanto, per evitare una ricerca superflua, è necessario assicurarsi che ogni pagina in un'applicazione utilizzi solo dizionari risorse appropriati per la pagina.

## <a name="related-links"></a>Collegamenti correlati

- [Dizionari risorse (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.FormsStili](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
