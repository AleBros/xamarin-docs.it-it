---
title: Dizionari di risorse
description: Risorse XAML sono oggetti che possono essere condivisa e usati nuovamente in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2020
ms.custom: video
ms.openlocfilehash: 496251ec9596b9ef76fb34149acca184b5934c37
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2020
ms.locfileid: "80070401"
---
# <a name="resource-dictionaries"></a>Dizionari di risorse

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_Le risorse XAML sono definizioni di oggetti che possono essere condivisi e riutilizzati in un'applicazione Novell. Forms. Questi oggetti risorsa vengono archiviati in un dizionario risorse._

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) è un repository per le risorse usate da un'applicazione Novell. Forms. Le risorse tipiche archiviate in un `ResourceDictionary` includono [stili](~/xamarin-forms/user-interface/styles/index.md), [modelli di controllo](~/xamarin-forms/app-fundamentals/templates/control-template.md), [modelli di dati](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), colori e convertitori.

In XAML, le risorse archiviate in un `ResourceDictionary` possono quindi essere recuperate e applicate agli elementi usando l'estensione di markup `StaticResource`. In C#le risorse possono anche essere definite in una `ResourceDictionary` e quindi recuperate e applicate agli elementi usando un indicizzatore basato su stringa. Tuttavia, l'uso di un `ResourceDictionary` in C#è leggermente vantaggioso, perché gli oggetti condivisi possono essere semplicemente archiviati come campi o proprietà ed è possibile accedervi direttamente senza dover prima recuperarli da un dizionario.

## <a name="create-and-consume-a-resourcedictionary"></a>Creazione e utilizzo di un oggetto ResourceDictionary

Le risorse vengono definite in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) che viene quindi impostato su una delle proprietà `Resources` seguenti:

- Proprietà [`Resources`](xref:Xamarin.Forms.Application.Resources) di qualsiasi classe che deriva da [`Application`](xref:Xamarin.Forms.Application)
- Proprietà [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) di qualsiasi classe che deriva da [`VisualElement`](xref:Xamarin.Forms.Application)

Un programma Novell. Forms contiene solo una classe che deriva da `Application` ma spesso usa molte classi che derivano da `VisualElement`, incluse pagine, layout e controlli. Uno di questi oggetti può avere la proprietà `Resources` impostata su una `ResourceDictionary`. La scelta della posizione in cui inserire un particolare `ResourceDictionary` influisca sulla posizione in cui è possibile usare le risorse:

- Le risorse in una `ResourceDictionary` collegata a una vista, ad esempio `Button` o `Label`, possono essere applicate solo a questo particolare oggetto, quindi questa operazione non è molto utile.
- Le risorse in un `ResourceDictionary` associate a un layout, ad esempio `StackLayout` o `Grid`, possono essere applicate al layout e a tutti gli elementi figlio di tale layout.
- Le risorse in un `ResourceDictionary` definito a livello di pagina possono essere applicate alla pagina e a tutti i relativi elementi figlio.
- Le risorse in un `ResourceDictionary` definito a livello di applicazione possono essere applicate all'interno dell'applicazione.

Il codice XAML seguente mostra le risorse definite in un livello di applicazione `ResourceDictionary` nel file **app. XAML** creato come parte del programma Novell. Forms standard:

```xaml
<Application ...>
    <Application.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Yellow</Color>
            <Color x:Key="HeadingTextColor">Black</Color>
            <Color x:Key="NormalTextColor">Blue</Color>
            <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
                <Setter Property="FontAttributes" Value="Bold" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Questo `ResourceDictionary` definisce tre risorse [`Color`](xref:Xamarin.Forms.Color) e una risorsa [`Style`](xref:Xamarin.Forms.Style) . Per ulteriori informazioni sulla classe `App`, vedere [classe App](~/xamarin-forms/app-fundamentals/application-class.md).

A partire da Novell. Forms 3,0, i tag `ResourceDictionary` espliciti non sono obbligatori. L'oggetto `ResourceDictionary` viene creato automaticamente ed è possibile inserire le risorse direttamente tra i tag dell'elemento proprietà `Resources`:

```xaml
<Application ...>
    <Application.Resources>
        <Color x:Key="PageBackgroundColor">Yellow</Color>
        <Color x:Key="HeadingTextColor">Black</Color>
        <Color x:Key="NormalTextColor">Blue</Color>
        <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
            <Setter Property="FontAttributes" Value="Bold" />
            <Setter Property="HorizontalOptions" Value="Center" />
            <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
        </Style>
    </Application.Resources>
</Application>
```

Ogni risorsa ha una chiave specificata usando l'attributo `x:Key`, che diventa la chiave del dizionario nel `ResourceDictionary`. La chiave viene usata per recuperare una risorsa dal `ResourceDictionary` dall'estensione di markup [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) , come illustrato nell'esempio di codice XAML seguente che Mostra risorse aggiuntive definite all'interno del `StackLayout`:

```xaml
<StackLayout Margin="0,20,0,0">
  <StackLayout.Resources>
    <ResourceDictionary>
      <Style x:Key="LabelNormalStyle" TargetType="Label">
        <Setter Property="TextColor" Value="{StaticResource NormalTextColor}" />
      </Style>
      <Style x:Key="MediumBoldText" TargetType="Button">
        <Setter Property="FontSize" Value="Medium" />
        <Setter Property="FontAttributes" Value="Bold" />
      </Style>
    </ResourceDictionary>
  </StackLayout.Resources>
  <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
    <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
           Margin="10,20,10,0"
           Style="{StaticResource LabelNormalStyle}" />
    <Button Text="Navigate"
            Clicked="OnNavigateButtonClicked"
            TextColor="{StaticResource NormalTextColor}"
            Margin="0,20,0,0"
            HorizontalOptions="Center"
            Style="{StaticResource MediumBoldText}" />
</StackLayout>
```

La prima istanza di [`Label`](xref:Xamarin.Forms.Label) recupera e utilizza la risorsa `LabelPageHeadingStyle` definita a livello di applicazione `ResourceDictionary`, con la seconda istanza di `Label` che recupera e utilizza la risorsa `LabelNormalStyle` definita nel `ResourceDictionary`a livello di controllo. Analogamente, l'istanza di [`Button`](xref:Xamarin.Forms.Button) recupera e utilizza la risorsa `NormalTextColor` definita a livello di applicazione `ResourceDictionary`e la risorsa `MediumBoldText` definita nel `ResourceDictionary`a livello di controllo. Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![utilizzo di risorse ResourceDictionary](resource-dictionaries-images/screenshots-sml.png)](resource-dictionaries-images/screenshots.png#lightbox)

> [!NOTE]
> Le risorse specifiche per una singola pagina non devono essere incluso in un'applicazione a livello dizionario risorse, di conseguenza le risorse vengano analizzate all'avvio dell'applicazione anziché quando richiesto da una pagina. Per altre informazioni, vedere [ridurre le dimensioni del dizionario risorse dell'applicazione](~/xamarin-forms/deploy-test/performance.md).

## <a name="override-resources"></a>Sostituisci risorse

Quando `ResourceDictionary` risorse condividono `x:Key` valori di attributo, le risorse definite in basso nella gerarchia di visualizzazione avranno la precedenza su quelle definite più in alto. Ad esempio, l'impostazione della risorsa `PageBackgroundColor` su `Blue` a livello di applicazione verrà sostituita da un livello di pagina `PageBackgroundColor` set di risorse su `Yellow`. Analogamente, una risorsa `PageBackgroundColor` a livello di pagina verrà sostituita da un livello di controllo `PageBackgroundColor` risorsa. Esempio di codice XAML seguente viene dimostrata questa precedenza:

```xaml
<ContentPage ... BackgroundColor="{StaticResource PageBackgroundColor}">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Blue</Color>
            <Color x:Key="NormalTextColor">Yellow</Color>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="0,20,0,0">
        ...
        <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
               Margin="10,20,10,0"
               Style="{StaticResource LabelNormalStyle}" />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked"
                TextColor="{StaticResource NormalTextColor}"
                Margin="0,20,0,0"
                HorizontalOptions="Center"
                Style="{StaticResource MediumBoldText}" />
    </StackLayout>
</ContentPage>
```

Le istanze di `PageBackgroundColor` e di `NormalTextColor` originali, definite a livello di applicazione, vengono sottoposte a override dalle istanze di `PageBackgroundColor` e `NormalTextColor` definite a livello di pagina. Di conseguenza, il colore di sfondo pagina diventa blu e il testo nella pagina diventa giallo, come illustrato negli screenshot seguenti:

[![l'override delle risorse ResourceDictionary](resource-dictionaries-images/overridding-screenshots-sml.png)](resource-dictionaries-images/overridding-screenshots.png#lightbox)

Si noti tuttavia che la barra di sfondo del [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) è ancora gialla, perché la proprietà [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) è impostata sul valore della risorsa `PageBackgroundColor` definita a livello di applicazione `ResourceDictionary`.

Ecco un altro modo per considerare la precedenza `ResourceDictionary`: quando il parser XAML rileva una `StaticResource`, Cerca una chiave corrispondente passando attraverso la struttura ad albero visuale, usando la prima corrispondenza trovata. Se questa ricerca termina in corrispondenza della pagina e la chiave non è ancora stata trovata, il parser XAML cerca nel `ResourceDictionary` collegato all'oggetto `App`. Se la chiave non viene trovata, viene generata un'eccezione.

## <a name="stand-alone-resource-dictionaries"></a>Dizionari risorse autonome

Una classe derivata da `ResourceDictionary` può anche trovarsi in un file autonomo separato. Il file risultante può quindi essere condiviso tra le applicazioni.

Per creare un file di questo tipo, aggiungere una nuova **visualizzazione contenuto** o elemento della **pagina contenuto** al progetto (ma non una **visualizzazione contenuto** o una **pagina contenuto** con solo un C# file). Nel file e C# nel file XAML modificare il nome della classe di base da `ContentView` o da `ContentPage` a `ResourceDictionary`. Nel file XAML, il nome della classe di base è l'elemento di primo livello.

Nell'esempio di codice XAML seguente viene illustrato un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) denominato `MyResourceDictionary`:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ResourceDictionaryDemo.MyResourceDictionary">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}" TextColor="{StaticResource NormalTextColor}" FontAttributes="Bold" />
                <Label Grid.Column="1" Text="{Binding Age}" TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2" Text="{Binding Location}" TextColor="{StaticResource NormalTextColor}" HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

Questo `ResourceDictionary` contiene una sola risorsa, ovvero un oggetto di tipo `DataTemplate`.

È possibile creare un'istanza di `MyResourceDictionary` inserendola tra una coppia di `Resources` tag elemento proprietà, ad esempio in un `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Un'istanza di `MyResourceDictionary` viene impostata sulla proprietà `Resources` dell'oggetto `ContentPage`.

Tuttavia, questo approccio presenta alcune limitazioni: la proprietà `Resources` della `ContentPage` fa riferimento solo a questa `ResourceDictionary`. Nella maggior parte dei casi, si desidera includere anche altre istanze di `ResourceDictionary` e anche altre risorse.

Questa attività richiede dizionari risorse uniti.

## <a name="merged-resource-dictionaries"></a>Dizionari risorse Uniti

I dizionari risorse Uniti combinano uno o più oggetti [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) in un'altra `ResourceDictionary`.

### <a name="merge-local-resource-dictionaries"></a>Unisci dizionari risorse locali

Una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) locale può essere unita in un'altra `ResourceDictionary` impostando la proprietà [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) sul nome file del file XAML con le risorse:

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

Questa sintassi non crea un'istanza della classe `MyResourceDictionary`. Al contrario, fa riferimento il file XAML. Per questo motivo, quando si imposta la proprietà [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) , non è necessario un file code-behind e l'attributo `x:Class` può essere rimosso dal tag radice del file **ResourceDictionary. XAML** . Inoltre, quando si uniscono dizionari risorse usando questo approccio, Novell. Forms creerà automaticamente un'istanza del [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), di conseguenza i tag di `ResourceDictionary` esterni non sono necessari.

> [!IMPORTANT]
> La proprietà [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) può essere impostata solo da XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Unisci dizionari risorse da altri assembly

È anche possibile unire un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) in un altro `ResourceDictionary` aggiungendolo alla proprietà [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) della `ResourceDictionary`. Questa tecnica consente di unire i dizionari risorse, indipendentemente dall'assembly in cui risiedono.

> [!IMPORTANT]
> La classe [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) definisce anche una proprietà [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) . Tuttavia, questa proprietà è stata deprecata e non deve più essere utilizzata.

Nell'esempio di codice seguente viene illustrato `MyResourceDictionary` essere aggiunto alla raccolta [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) di un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a livello di pagina:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

In questo esempio viene illustrata un'istanza di `MyResourceDictionary`, che risiede nello stesso assembly, che viene aggiunta al [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Inoltre, è anche possibile aggiungere dizionari risorse da altri assembly, altri `ResourceDictionary` oggetti all'interno dei tag dell'elemento proprietà [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) e altre risorse esterne a tali tag:

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

Quando si inserisce un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) in un assembly esterno, assicurarsi che l'azione di compilazione sia impostata su **EmbeddedResource**. Assicurarsi inoltre che disponga di un file code-behind.

> [!IMPORTANT]
> In una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)può essere presente un solo tag `MergedDictionaries` elemento Property-Item, ma è possibile inserire il numero di oggetti `ResourceDictionary` nel modo desiderato.

In caso di merge [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) risorse condividono valori di attributo `x:Key` identici, Novell. Forms usa la precedenza di risorsa seguente:

1. Le risorse locali per il dizionario risorse.
1. Le risorse contenute nei dizionari risorse che sono state unite tramite la raccolta di `MergedDictionaries`, in ordine inverso, sono elencate nella proprietà `MergedDictionaries`.

> [!NOTE]
> La ricerca nei dizionari risorse può essere un'attività con calcoli complessi se un'applicazione contiene più i dizionari di risorse di grandi dimensioni. Pertanto, per evitare la ricerca non necessari, è necessario assicurarsi che ogni pagina in un'applicazione usa solo i dizionari risorse uniti appropriati alla pagina.

## <a name="related-links"></a>Collegamenti correlati

- [Dizionari risorse (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
