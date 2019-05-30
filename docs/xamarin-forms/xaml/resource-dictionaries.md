---
title: Dizionari risorse
description: Risorse XAML sono oggetti che possono essere condivisa e usati nuovamente in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: ba0a76b44f5942db5528e9f5d11a1d2c3f027c40
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354034"
---
# <a name="resource-dictionaries"></a>Dizionari risorse

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/XAML/ResourceDictionaries/)

_Risorse XAML sono definizioni di oggetti che possono essere condivisa e usati nuovamente in un'applicazione xamarin. Forms._

Queste risorse vengono archiviati in un dizionario risorse. Questo articolo descrive come creare e utilizzare un dizionario risorse e come i dizionari risorse di tipo merge.

## <a name="overview"></a>Panoramica

Oggetto [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) è un repository per le risorse utilizzate da un'applicazione xamarin. Forms. Le risorse tipiche che vengono archiviate in un `ResourceDictionary` includono [stili](~/xamarin-forms/user-interface/styles/index.md), [modelli di controllo](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [modelli di data](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), colori e dei convertitori di tipi.

In XAML, le risorse che vengono archiviati in un `ResourceDictionary` possono essere recuperate e applicato agli elementi usando il `StaticResource` estensione di markup. In c#, risorse possono anche essere definite un `ResourceDictionary` recuperato e quindi applicata agli elementi con un indicizzatore basato su stringa. Tuttavia, c'è molto vantaggiosa all'uso di un `ResourceDictionary` nel linguaggio c#, come oggetti condivisi possono semplicemente essere archiviati come campi o proprietà e si accede direttamente senza dover prima recuperarli da un dizionario.

## <a name="creating-and-consuming-a-resourcedictionary"></a>Creazione e l'utilizzo di un oggetto ResourceDictionary

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

Il primo [ `Label` ](xref:Xamarin.Forms.Label) istanza recupera e utilizza il `LabelPageHeadingStyle` risorse definite nel livello di applicazione `ResourceDictionary`, con il secondo `Label` istanza il recupero e l'utilizzo di `LabelNormalStyle`risorse definite nel livello di controllo `ResourceDictionary`. Allo stesso modo, il [ `Button` ](xref:Xamarin.Forms.Button) istanza recupera e utilizza il `NormalTextColor` risorse definite nel livello di applicazione `ResourceDictionary`e la `MediumBoldText` risorse definite nel livello di controllo `ResourceDictionary`. Ciò comporta l'aspetto illustrato negli screenshot seguenti:

[![](resource-dictionaries-images/screenshots-sml.png "Utilizzo delle risorse di ResourceDictionary")](resource-dictionaries-images/screenshots.png#lightbox "utilizzano risorse di ResourceDictionary")

> [!NOTE]
> Le risorse specifiche per una singola pagina non devono essere incluso in un'applicazione a livello dizionario risorse, di conseguenza le risorse vengano analizzate all'avvio dell'applicazione anziché quando richiesto da una pagina. Per altre informazioni, vedere [ridurre le dimensioni del dizionario risorse dell'applicazione](~/xamarin-forms/deploy-test/performance.md).

## <a name="overriding-resources"></a>Si esegue l'override delle risorse

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

[![](resource-dictionaries-images/overridding-screenshots-sml.png "Si esegue l'override delle risorse di ResourceDictionary")](resource-dictionaries-images/overridding-screenshots.png#lightbox "eseguendo l'override delle risorse di ResourceDictionary")

Si noti tuttavia che la barra in background del [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) è ancora giallo, perché il [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) proprietà è impostata sul valore del `PageBackgroundColor` risorse definite nell'applicazione livello `ResourceDictionary`.

Ecco un altro modo per pensare `ResourceDictionary` precedenza: Quando il parser XAML rileva un `StaticResource`, la ricerca di una chiave corrispondente da passano la struttura ad albero visuale, usando la prima corrispondenza trovata. Se questa ricerca termina in corrispondenza della pagina e la chiave non è ancora stata trovata, Cerca il parser XAML il `ResourceDictionary` collegato al `App` oggetto. Se la chiave non viene trovata, viene generata un'eccezione.

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

Questo approccio presenta tuttavia alcune limitazioni: Il `Resources` proprietà del `ContentPage` fa riferimento a questo sola `ResourceDictionary`. Nella maggior parte dei casi, si desidera che la possibilità di includere altri `ResourceDictionary` istanze e forse altre risorse oltre.

Questa attività richiede dizionari risorse uniti.

## <a name="merged-resource-dictionaries"></a>Dizionari risorse uniti

Dizionari risorse uniti combinano uno o più `ResourceDictionary` istanze in un altro `ResourceDictionary`. È possibile farlo in un file XAML impostando il [ `MergedDictionaries` ](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) proprietà da uno o più dizionari risorse che verranno uniti all'applicazione, pagina o a livello di controllo `ResourceDictionary`.

> [!IMPORTANT]
> `ResourceDictionary` definisce anche un [ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith) proprietà. Non usare questa proprietà. si è stato deprecato a partire da xamarin. Forms 3.0.

Un'istanza di `MyResourceDictionary` possono essere uniti in qualsiasi applicazione, la pagina o a livello di controllo `ResourceDictionary`. Esempio di codice XAML seguente mostra l'unione in un livello di pagina `ResourceDictionary` utilizzando il `MergedDictionaries` proprietà:

```xaml
<ContentPage ...>
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

Markup mostra solo un'istanza di `MyResourceDictionary` viene aggiunto al `ResourceDictionary` ma è anche possibile fare riferimento altre `ResourceDictionary` alle istanze del `MergedDictionary` i tag di elemento di proprietà e altre risorse all'esterno di tali tag:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary.MergedDictionaries>

                <!-- Add more resource dictionaries here -->

                <local:MyResourceDictionary />

                <!-- Add more resource dictionaries here -->

            </ResourceDictionary.MergedDictionaries>

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Può esistere un solo `MergedDictionaries` sezione un `ResourceDictionary`, ma è possibile inserire tante `ResourceDictionary` desiderato di istanze in questa posizione.

Durante l'unione [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) condividono di risorse identici `x:Key` valori di attributo, xamarin. Forms Usa la precedenza di risorsa seguente:

1. Le risorse locali per il dizionario risorse.
1. Le risorse contenute nel dizionario risorse unito tramite deprecate [ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith) proprietà.
1. Le risorse contenute nei dizionari risorse uniti tramite il `MergedDictionaries` insieme, in ordine inverso sono elencati nel `MergedDictionaries` proprietà.

> [!NOTE]
> La ricerca nei dizionari risorse può essere un'attività con calcoli complessi se un'applicazione contiene più i dizionari di risorse di grandi dimensioni. Pertanto, per evitare la ricerca non necessari, è necessario assicurarsi che ogni pagina in un'applicazione usa solo i dizionari risorse uniti appropriati alla pagina.

## <a name="merging-dictionaries-in-xamarinforms-30"></a>Unione di dizionari in xamarin. Forms 3.0

A partire da xamarin. Forms 3.0, il processo consistente nell'unire [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) istanze è diventato un po' più semplice e più flessibile. Il `MergedDictionaries` i tag di elemento di proprietà non sono più necessari. È invece aggiungere al dizionario risorse un'altra `ResourceDictionary` tag con il nuovo [ `Source` ](xref:Xamarin.Forms.ResourceDictionary.Source) proprietà è impostata per il nome del file del file XAML con le risorse:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary Source="MyResourceDictionary.xaml" />

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Poiché xamarin. Forms 3.0 crea automaticamente un'istanza di `ResourceDictionary`, questi due outer `ResourceDictionary` tag non sono più necessari:

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

Questa nuova sintassi viene _non_ creare un'istanza di `MyResourceDictionary` classe. Al contrario, fa riferimento il file XAML. Per questa ragione il file code-behind (**MyResourceDictionary.xaml.cs**) non è più necessario. È anche possibile rimuovere il `x:Class` attributo di tag radice del **MyResourceDictionary.xaml** file.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare e utilizzare un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e come unire i dizionari risorse. Oggetto `ResourceDictionary` consente alle risorse di essere definito in un'unica posizione e usata nuovamente in un'applicazione xamarin. Forms.

## <a name="related-links"></a>Collegamenti correlati

- [Dizionari risorse (esempio)](https://developer.xamarin.com/samples/xamarin-forms/XAML/ResourceDictionaries/)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]

