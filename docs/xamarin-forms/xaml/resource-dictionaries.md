---
title: Dizionari risorse
description: Risorse XAML sono oggetti che possono essere condivisa e usati nuovamente in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2019
ms.custom: video
ms.openlocfilehash: 7c0fffbe626a740c15d85b1277c5158a5e564a15
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228082"
---
# <a name="resource-dictionaries"></a>Dizionari risorse

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_Le risorse XAML sono definizioni di oggetti che possono essere condivisi e riutilizzati in un'applicazione Novell. Forms. Questi oggetti risorsa vengono archiviati in un dizionario risorse._

Oggetto [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) è un repository per le risorse utilizzate da un'applicazione xamarin. Forms. Le risorse tipiche che vengono archiviate in un `ResourceDictionary` includono [stili](~/xamarin-forms/user-interface/styles/index.md), [modelli di controllo](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [modelli di data](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), colori e dei convertitori di tipi.

In XAML, le risorse che vengono archiviati in un `ResourceDictionary` possono essere recuperate e applicato agli elementi usando il `StaticResource` estensione di markup. In c#, risorse possono anche essere definite un `ResourceDictionary` recuperato e quindi applicata agli elementi con un indicizzatore basato su stringa. Tuttavia, c'è molto vantaggiosa all'uso di un `ResourceDictionary` nel linguaggio c#, come oggetti condivisi possono semplicemente essere archiviati come campi o proprietà e si accede direttamente senza dover prima recuperarli da un dizionario.

## <a name="create-and-consume-a-resourcedictionary"></a>Creazione e utilizzo di un oggetto ResourceDictionary

Le risorse vengono definite un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) vale a dire quindi impostato su uno dei seguenti `Resources` proprietà:

- Il [ `Resources` ](xref:Xamarin.Forms.Application.Resources) proprietà di qualsiasi classe che deriva da [`Application`](xref:Xamarin.Forms.Application)
- Il [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) proprietà di qualsiasi classe che deriva da [`VisualElement`](xref:Xamarin.Forms.Application)

Un programma di xamarin. Forms contiene una sola classe che deriva da `Application` , ma Usa spesso molte classi che derivano da `VisualElement`, inclusi i controlli, layout e pagine. Uno di questi oggetti possono avere relativi `Resources` impostata su un `ResourceDictionary`. Scegliere dove inserire un determinato `ResourceDictionary` impatti dove le risorse possono essere usate:

- Le risorse in un `ResourceDictionary` che è collegato a una visualizzazione, ad esempio `Button` o `Label` può essere applicato solo all'oggetto in questione, in modo che questo non è molto utile.
- Le risorse in un `ResourceDictionary` collegato a un layout, ad esempio `StackLayout` o `Grid` può essere applicato a tutti gli elementi figlio di tale layout e il layout.
- Le risorse in un `ResourceDictionary` definiti nella pagina di livello può essere applicato alla pagina e a tutti i relativi elementi figlio.
- Le risorse in un `ResourceDictionary` definiti nell'applicazione livello può essere applicato in tutta l'applicazione.

il XAML seguente mostra le risorse definite nel livello dell'applicazione `ResourceDictionary` nella **app** file creato come parte del programma di xamarin. Forms standard:

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

Ciò `ResourceDictionary` definisce tre [ `Color` ](xref:Xamarin.Forms.Color) risorse e un [ `Style` ](xref:Xamarin.Forms.Style) risorsa. Per altre informazioni sul `App` classe, vedere [classe App](~/xamarin-forms/app-fundamentals/application-class.md).

A partire da xamarin. Forms 3.0, l'impostazione esplicita `ResourceDictionary` tag non sono necessari. Il `ResourceDictionary` oggetto viene creato automaticamente, e consente di inserire le risorse direttamente tra il `Resources` i tag di elemento di proprietà:

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

Ogni risorsa dispone di una chiave specificata utilizzando il `x:Key` attributo, che diventa lo chiave del dizionario nel `ResourceDictionary`. La chiave viene usata per recuperare una risorsa dal `ResourceDictionary` dal [ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension) estensione di markup, come illustrato nell'esempio di codice XAML seguente che mostra le risorse aggiuntive definite all'interno di `StackLayout`:

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

Il primo [ `Label` ](xref:Xamarin.Forms.Label) istanza recupera e utilizza il `LabelPageHeadingStyle` risorse definite nel livello di applicazione `ResourceDictionary`, con il secondo `Label` istanza il recupero e l'utilizzo di `LabelNormalStyle`risorse definite nel livello di controllo `ResourceDictionary`. Allo stesso modo, il [ `Button` ](xref:Xamarin.Forms.Button) istanza recupera e utilizza il `NormalTextColor` risorse definite nel livello di applicazione `ResourceDictionary`e la `MediumBoldText` risorse definite nel livello di controllo `ResourceDictionary`. Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![Utilizzo di risorse ResourceDictionary](resource-dictionaries-images/screenshots-sml.png)](resource-dictionaries-images/screenshots.png#lightbox)

> [!NOTE]
> Le risorse specifiche per una singola pagina non devono essere incluso in un'applicazione a livello dizionario risorse, di conseguenza le risorse vengano analizzate all'avvio dell'applicazione anziché quando richiesto da una pagina. Per altre informazioni, vedere [ridurre le dimensioni del dizionario risorse dell'applicazione](~/xamarin-forms/deploy-test/performance.md).

## <a name="override-resources"></a>Sostituisci risorse

Quando `ResourceDictionary` condividono risorse `x:Key` valori degli attributi, le risorse definite più basso nella gerarchia di visualizzazione avrà la precedenza rispetto a quelli definiti più backup. Ad esempio, impostando il `PageBackgroundColor` risorsa `Blue` nell'applicazione livello eseguirà l'override di un livello di pagina `PageBackgroundColor` set di risorse a `Yellow`. Analogamente, un livello di pagina `PageBackgroundColor` risorsa eseguirà l'override di un livello di controllo `PageBackgroundColor` risorsa. Esempio di codice XAML seguente viene dimostrata questa precedenza:

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

Originale `PageBackgroundColor` e `NormalTextColor` sostituiscono le istanze, definite a livello di applicazione, il `PageBackgroundColor` e `NormalTextColor` istanze definite a livello di pagina. Di conseguenza, il colore di sfondo pagina diventa blu e il testo nella pagina diventa giallo, come illustrato negli screenshot seguenti:

[![Override di risorse ResourceDictionary](resource-dictionaries-images/overridding-screenshots-sml.png)](resource-dictionaries-images/overridding-screenshots.png#lightbox)

Si noti tuttavia che la barra in background del [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) è ancora giallo, perché il [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) proprietà è impostata sul valore del `PageBackgroundColor` risorse definite nell'applicazione livello `ResourceDictionary`.

Ecco un altro modo per considerare `ResourceDictionary` la precedenza: Quando il parser XAML rileva un oggetto `StaticResource`, Cerca una chiave corrispondente passando attraverso la struttura ad albero visuale, usando la prima corrispondenza trovata. Se questa ricerca termina in corrispondenza della pagina e la chiave non è ancora stata trovata, Cerca il parser XAML il `ResourceDictionary` collegato al `App` oggetto. Se la chiave non viene trovata, viene generata un'eccezione.

## <a name="stand-alone-resource-dictionaries"></a>Dizionari risorse autonome

Una classe derivata da `ResourceDictionary` può anche essere in un file autonomo separato. (Più precisamente, una classe derivata da `ResourceDictionary` richiede in genere una _coppia_ dei file in quanto le risorse vengono definite in un file XAML, ma un file code-behind con un `InitializeComponent` è anche necessario chiamare.) Il file risultante può quindi essere condiviso tra le applicazioni.

Per creare un file di questo tipo, aggiungere una nuova **visualizzazione contenuto** o **pagina contenuto** elemento al progetto (ma non una **visualizzazione contenuto** oppure **pagina contenuto** con solo un file c#). File XAML sia dal file di c#, modificare il nome della classe di base da `ContentView` oppure `ContentPage` a `ResourceDictionary`. Nel file XAML, il nome della classe di base è l'elemento di primo livello.

L'esempio XAML seguente viene illustrato un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) denominato `MyResourceDictionary`:

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

Ciò `ResourceDictionary` contiene una sola risorsa, ovvero un oggetto di tipo `DataTemplate`.

È possibile creare un'istanza `MyResourceDictionary` posizionandola tra una coppia di `Resources` elemento proprietà tag, ad esempio, in un `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Un'istanza di `MyResourceDictionary` è impostato sul `Resources` proprietà del `ContentPage` oggetto.

Tuttavia, questo approccio presenta alcune limitazioni: La `Resources` proprietà `ResourceDictionary`di fa riferimento solo a questa. `ContentPage` Nella maggior parte dei casi, si desidera che la possibilità di includere altri `ResourceDictionary` istanze e forse altre risorse oltre.

Questa attività richiede dizionari risorse uniti.

## <a name="merged-resource-dictionaries"></a>Dizionari risorse Uniti

I dizionari risorse Uniti combinano uno [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) o più oggetti `ResourceDictionary`in un altro.

### <a name="merge-local-resource-dictionaries"></a>Unisci dizionari risorse locali

È possibile [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) eseguire il merge di un `ResourceDictionary` oggetto locale in [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) un altro impostando la proprietà sul nome file del file XAML con le risorse:

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

Questa sintassi non crea un'istanza della `MyResourceDictionary` classe. Al contrario, fa riferimento il file XAML. Per questo motivo, quando si imposta [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) la proprietà, il file code-behind (**MyResourceDictionary.XAML.cs**) non è obbligatorio e `x:Class` l'attributo può essere rimosso dal tag radice del file **ResourceDictionary. XAML** . Inoltre, quando si uniscono dizionari risorse usando questo approccio, Novell. Forms creerà automaticamente un'istanza [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)di, di conseguenza `ResourceDictionary` i tag esterni non sono obbligatori.

> [!IMPORTANT]
> La [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) proprietà può essere impostata solo da XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Unisci dizionari risorse da altri assembly

È [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `ResourceDictionary` anchepossibile`ResourceDictionary`unire un oggetto a un altro aggiungendolo alla [proprietàdi.`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) Questa tecnica consente di unire i dizionari risorse, indipendentemente dall'assembly in cui risiedono.

> [!IMPORTANT]
> La [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) classe definisce anche una [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) proprietà. Tuttavia, questa proprietà è stata deprecata e non deve più essere utilizzata.

L'esempio di codice seguente `MyResourceDictionary` viene aggiunto [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) alla raccolta di un livello [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)di pagina:

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

In questo esempio viene illustrata `MyResourceDictionary`un'istanza di, che risiede nello stesso assembly, che viene aggiunto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a. Inoltre, è anche possibile aggiungere dizionari risorse da altri assembly, altri `ResourceDictionary` oggetti [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) all'interno dei tag elemento proprietà e altre risorse esterne a tali tag:

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

> [!IMPORTANT]
> Può essere presente un solo `MergedDictionaries` tag di elemento Property in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)oggetto, ma è possibile inserire il `ResourceDictionary` numero di oggetti desiderato.

Durante l'unione [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) condividono di risorse identici `x:Key` valori di attributo, xamarin. Forms Usa la precedenza di risorsa seguente:

1. Le risorse locali per il dizionario risorse.
1. Le risorse contenute nei dizionari risorse uniti tramite il `MergedDictionaries` insieme, in ordine inverso sono elencati nel `MergedDictionaries` proprietà.

> [!NOTE]
> La ricerca nei dizionari risorse può essere un'attività con calcoli complessi se un'applicazione contiene più i dizionari di risorse di grandi dimensioni. Pertanto, per evitare la ricerca non necessari, è necessario assicurarsi che ogni pagina in un'applicazione usa solo i dizionari risorse uniti appropriati alla pagina.

## <a name="related-links"></a>Collegamenti correlati

- [Dizionari risorse (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Video correlati

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
