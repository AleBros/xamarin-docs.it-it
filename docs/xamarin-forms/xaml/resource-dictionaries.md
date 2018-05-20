---
title: Dizionari delle risorse
description: Risorse XAML sono oggetti che possono essere condivisi e riutilizzati in un'applicazione di xamarin. Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/07/2018
ms.openlocfilehash: 47cca2f726b0af396ea1eb287cfa4e1f1bf19724
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2018
---
# <a name="resource-dictionaries"></a>Dizionari delle risorse

_Risorse XAML sono definizioni di oggetti che possono essere condivisi e riutilizzate in un'applicazione di xamarin. Forms._

Queste risorse vengono archiviati in un dizionario risorse. In questo articolo viene descritto come creare e utilizzare un dizionario risorse e come unire i dizionari delle risorse.

## <a name="overview"></a>Panoramica

Oggetto [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) è un repository per le risorse utilizzate da un'applicazione di xamarin. Forms. Le risorse che vengono archiviate in un `ResourceDictionary` includono [stili](~/xamarin-forms/user-interface/styles/index.md), [modelli di controllo](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [modelli di data](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), colori e i convertitori di tipi.

In XAML, le risorse che vengono archiviati in un `ResourceDictionary` possono essere recuperate e applicata agli elementi tramite il `StaticResource` estensione di markup. Nel linguaggio c#, le risorse possono essere definite anche in un `ResourceDictionary` e quindi recuperati e applicato agli elementi tramite un indicizzatore basato su stringa. Tuttavia, è molto vantaggiosa all'utilizzo di un `ResourceDictionary` nel linguaggio c#, perché gli oggetti condivisi possono semplicemente essere archiviati come campi o proprietà e accede direttamente senza dover prima recuperarli da un dizionario.

## <a name="creating-and-consuming-a-resourcedictionary"></a>Creazione e utilizzo di un oggetto ResourceDictionary

Le risorse vengono definite un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) vale a dire quindi impostato su uno dei seguenti `Resources` proprietà:

- Il [ `Resources` ](xref:Xamarin.Forms.Application.Resources) proprietà di qualsiasi classe che deriva da [`Application`](xref:Xamarin.Forms.Application)
- Il [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) proprietà di qualsiasi classe che deriva da ['VisualElement'](xref:Xamarin.Forms.Application)

Un programma xamarin. Forms contiene solo una classe che deriva da `Application` , ma spesso si avvalgono di molte classi che derivano da `VisualElement`, inclusi i controlli, layout e pagine. Uno di questi oggetti possono avere relativa `Resources` impostata su un `ResourceDictionary`. Scelta posizione in cui inserire un particolare `ResourceDictionary` impatto in cui le risorse possono essere usate:

- Le risorse in un `ResourceDictionary` che è associata a una visualizzazione, ad esempio `Button` o `Label` può essere applicato solo a tale oggetto specifico, in modo non è molto utile.
- Le risorse in un `ResourceDictionary` associata a un layout, ad esempio `StackLayout` o `Grid` può essere applicato a tutti gli elementi figlio di tale layout e il layout.
- Le risorse in un `ResourceDictionary` definiti nella pagina di livello può essere applicato alla pagina e a tutti i relativi figli.
- Le risorse in un `ResourceDictionary` definito l'applicazione di livello può essere applicato in tutta l'applicazione.

Il codice XAML seguente mostra le risorse definite in un livello di applicazione `ResourceDictionary` nella **app** file creato come parte del programma xamarin. Forms standard:

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

Ciò `ResourceDictionary` definisce tre [ `Color` ](xref:Xamarin.Forms.Color) risorse e un [ `Style` ](xref:Xamarin.Forms.Style) risorse. Per ulteriori informazioni sul `App` classe, vedere [classe App](~/xamarin-forms/app-fundamentals/application-class.md).

A partire da 3.0 xamarin. Forms, esplicite `ResourceDictionary` tag non sono necessari. Il `ResourceDictionary` oggetto viene creato automaticamente ed è possibile inserire direttamente tra le risorse di `Resources` i tag di elemento di proprietà:

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

Ogni risorsa ha una chiave specificata utilizzando il `x:Key` attributo, che diventa il dizionario chiave nella `ResourceDictionary`. La chiave viene usata per recuperare una risorsa dal `ResourceDictionary` dal [ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension) estensione di markup, come illustrato nell'esempio di codice XAML seguente che mostra le risorse aggiuntive definite all'interno di `StackLayout`:

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

Il primo [ `Label` ](xref:Xamarin.Forms.Label) istanza recupera e utilizza il `LabelPageHeadingStyle` risorse definite nel livello di applicazione `ResourceDictionary`, con il secondo `Label` istanza il recupero e l'utilizzo di `LabelNormalStyle`risorse definite nel livello di controllo `ResourceDictionary`. Analogamente, il [ `Button` ](xref:Xamarin.Forms.Button) istanza recupera e utilizza il `NormalTextColor` risorse definite nel livello di applicazione `ResourceDictionary`e `MediumBoldText` risorse definite nel livello di controllo `ResourceDictionary`. Ciò comporta l'aspetto illustrato nelle schermate seguenti:

[![](resource-dictionaries-images/screenshots-sml.png "Utilizzo risorse ResourceDictionary")](resource-dictionaries-images/screenshots.png#lightbox "ResourceDictionary risorse")

> [!NOTE]
> Risorse specifiche di una singola pagina non devono essere incluso in un'applicazione livello dizionario risorse, in quanto tali risorse verranno quindi analizzate all'avvio dell'applicazione anziché quando richiesto da una pagina. Per ulteriori informazioni, vedere [ridurre le dimensioni di dizionario risorse dell'applicazione](~/xamarin-forms/deploy-test/performance.md).

## <a name="overriding-resources"></a>Si esegue l'override di risorse

Quando si `ResourceDictionary` le risorse condividano `x:Key` valori di attributo, le risorse definite inferiore nella gerarchia della visualizzazione avrà la precedenza rispetto a quelli definiti su un valore maggiore di. Ad esempio, impostando il `PageBackgroundColor` risorsa `Blue` l'applicazione di livello verrà sottoposto a override da un livello di pagina `PageBackgroundColor` set di risorse a `Yellow`. Analogamente, un livello di pagina `PageBackgroundColor` risorsa verrà sostituita da un livello di controllo `PageBackgroundColor` risorse. L'esempio di codice XAML seguente viene dimostrata la precedenza:

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

Originale `PageBackgroundColor` e `NormalTextColor` istanze, definite a livello di applicazione, vengono sostituite dal `PageBackgroundColor` e `NormalTextColor` istanze definite a livello di pagina. Di conseguenza, il colore di sfondo della pagina diventa blu, e il testo nella pagina diventa giallo, come illustrato nelle schermate seguenti:

[![](resource-dictionaries-images/overridding-screenshots-sml.png "Si esegue l'override delle risorse di ResourceDictionary")](resource-dictionaries-images/overridding-screenshots.png#lightbox "risorse ResourceDictionary viene sottoposto a override")

Si noti tuttavia che la barra in background del [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) è ancora giallo, perché il [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) proprietà è impostata sul valore della `PageBackgroundColor` risorsa definita nell'applicazione livello `ResourceDictionary`.

Ecco un altro modo per considerare `ResourceDictionary` precedenza: quando XAML il parser rileva un `StaticResource`, alla ricerca di una chiave corrispondente da passano la struttura ad albero visuale, utilizzando la prima corrispondenza trova. Se questa ricerca termina in corrispondenza della pagina e la chiave non è ancora stata trovata, il parser XAML cerca il `ResourceDictionary` collegata la `App` oggetto. Se la chiave non viene trovata, viene generata un'eccezione.

## <a name="stand-alone-resource-dictionaries"></a>Dizionari delle risorse autonomo

Una classe derivata da `ResourceDictionary` può anche essere in un file autonomo separato. (Più precisamente, una classe derivata da `ResourceDictionary` richiede in genere un _coppia_ dei file perché le risorse vengono definite in un file XAML, ma un file code-behind con un `InitializeComponent` è anche necessaria una chiamata.) Il file risultante può quindi essere condivisa tra applicazioni.

Per creare un file di questo tipo, aggiungere un nuovo **visualizzazione contenuto** oppure **pagina contenuto** elemento al progetto (ma non un **visualizzazione contenuto** oppure **pagina contenuto** con solo un file c#). Sia il file XAML e file c#, modificare il nome della classe di base da `ContentView` oppure `ContentPage` a `ResourceDictionary`. Nel file XAML, il nome della classe base è l'elemento di livello principale.

Nell'esempio XAML seguente viene illustrata una [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) denominato `MyResourceDictionary`:

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

Ciò `ResourceDictionary` contiene una singola risorsa, ovvero un oggetto di tipo `DataTemplate`.

È possibile creare un'istanza `MyResourceDictionary` mettendola tra una coppia di `Resources` elemento proprietà tag, ad esempio, un `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Un'istanza di `MyResourceDictionary` è impostato il `Resources` proprietà del `ContentPage` oggetto.

Tuttavia, questo approccio presenta alcune limitazioni: il `Resources` proprietà del `ContentPage` fa riferimento a questa sola `ResourceDictionary`. Nella maggior parte dei casi, si desidera applicare l'opzione di inclusione altri `ResourceDictionary` istanze e forse altre risorse anche.

Questa attività richiede dizionari risorse sottoposti a merge.

## <a name="merged-resource-dictionaries"></a>Dizionari risorse uniti

I dizionari risorse sottoposti a merge combinano uno o più `ResourceDictionary` le istanze in un altro `ResourceDictionary`. È possibile farlo in un file XAML impostando i [ `MergedDictionaries` ](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) proprietà da uno o più dizionari risorse che vengono unite all'applicazione, una pagina o un livello di controllo `ResourceDictionary`.

> [!IMPORTANT]
> `ResourceDictionary` definisce inoltre un [ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith) proprietà. Non utilizzare questa proprietà. ed è stato deprecato a partire da 3.0 xamarin. Forms.

E l'istanza del `MyResourceDictionary` possono essere uniti in qualsiasi applicazione, una pagina o un livello di controllo `ResourceDictionary`. L'esempio di codice XAML seguente viene illustrato che l'unione in un livello di pagina `ResourceDictionary` utilizzando il `MergedDictionaries` proprietà:

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

Tale markup mostra solo un'istanza di `MyResourceDictionary` viene pertanto aggiunta al `ResourceDictionary` ma si può fare riferimento anche altri `ResourceDictionary` istanze all'interno di `MergedDictionary` i tag di elemento di proprietà e altre risorse all'esterno di tali tag:

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

Può essere presente solo una `MergedDictionaries` sezione un `ResourceDictionary`, ma è possibile inserire tante `ResourceDictionary` istanze esiste come si desidera.

Quando unite [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) le risorse condividano identici `x:Key` valori di attributo, xamarin. Forms Usa la precedenza di risorse seguenti:

1. Le risorse locali per il dizionario risorse.
1. Le risorse contenute nel dizionario risorse che è stato unito tramite deprecate [ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith) proprietà.
1. Le risorse contenute nei dizionari risorse che sono stati uniti tramite il `MergedDictionaries` insieme, nell'ordine in cui sono elencati il `MergedDictionaries` proprietà.

> [!NOTE]
> La ricerca di dizionari delle risorse può essere un'attività complesse se un'applicazione contiene più dizionari risorse di grandi dimensioni. Pertanto, per evitare di ricerche non necessarie, è necessario assicurarsi che ogni pagina in un'applicazione utilizza solo i dizionari delle risorse appropriate per la pagina.

## <a name="merging-dictionaries-in-xamarinforms-30"></a>L'unione di dizionari in xamarin. Forms 3.0

A partire da xamarin. Forms 3.0, il processo di unione [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) istanze è diventata più semplice e più flessibile. Il `MergedDictionaries` i tag di elemento di proprietà non sono più necessari. Al contrario, aggiungere al dizionario risorse un'altra `ResourceDictionary` tag con il nuovo [ `Source` ](xref:Xamarin.Forms.ResourceDictionary.Source) impostata per il nome del file del file XAML con le risorse:

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

Questa nuova sintassi _non_ creare un'istanza di `MyResourceDictionary` classe. Invece, fa riferimento a file con estensione XAML. Per questa ragione il file code-behind (**MyResourceDictionary.xaml.cs**) non è più necessario. È inoltre possibile rimuovere il `x:Class` attributo dal tag radice del **MyResourceDictionary.xaml** file.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come creare e utilizzare un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e come unire i dizionari delle risorse. Oggetto `ResourceDictionary` risorse siano definiti in un'unica posizione e riutilizzate in un'applicazione di xamarin. Forms.

## <a name="related-links"></a>Collegamenti correlati

- [Dizionari risorse (esempio)](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
