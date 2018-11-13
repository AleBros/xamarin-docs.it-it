---
title: Parte 2. Sintassi XAML essenziale
description: Questo articolo illustra le funzionalità della sintassi XAML essenziale degli elementi di proprietà e le proprietà associate.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 985ca3b34b4b85ef234f12fe3f25edd1d1556e23
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563238"
---
# <a name="part-2-essential-xaml-syntax"></a>Parte 2. Sintassi XAML essenziale

_XAML è progettato principalmente per la creazione di istanze e inizializzazione di oggetti. Ma spesso devono essere impostate su oggetti complessi che non possono essere rappresentati facilmente sotto forma di stringhe XML e a volte le proprietà definite da una classe devono essere impostate su una classe figlio. Queste due esigenze richiedono la funzionalità della sintassi XAML essenziale di elementi di proprietà e le proprietà associate._

## <a name="property-elements"></a>Proprietà elementi

In XAML, proprietà delle classi in genere sono impostate come attributi XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Tuttavia, vi è un modo alternativo per impostare una proprietà in XAML. Per provare questa alternativa con `TextColor`, prima di tutto eliminare quello esistente `TextColor` impostazione:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Aprire l'elemento vuoto `Label` tag da separandolo in tag di inizio e fine:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

All'interno di questi tag, aggiungere i tag di inizio e fine costituiti da un nome di proprietà separati da un punto e il nome della classe:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

Impostare il valore della proprietà come contenuto di questi nuovi tag, simile al seguente:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

Questi due modi per specificare il `TextColor` proprietà sono funzionalmente equivalenti, ma non usare i due modi per la stessa proprietà in quanto che sarebbe effettivamente impostando la proprietà due volte e potrebbe essere ambigua.

Con questa nuova sintassi, è possono che vengano introdotti alcuni termini utili:

-  `Label` è un' *elemento oggetto*. È un oggetto di xamarin. Forms espresso come un elemento XML.
-  `Text`, `VerticalOptions`, `FontAttributes` e `FontSize` sono *gli attributi della proprietà*. Sono le proprietà di xamarin. Forms espresse come attributi XML.
-  In questo frammento di codice finale `TextColor` è diventato un *property (elemento)*. Si tratta di una proprietà di xamarin. Forms, ma ora è un elemento XML.


La definizione della proprietà di elementi potrebbero a prima di tutto sembra essere una violazione della sintassi XML, ma non è. Il periodo di ha alcun significato speciale in XML. Per un decodificatore XML, `Label.TextColor` è semplicemente un elemento figlio normale.

In XAML, tuttavia, questa sintassi è molto speciale. Una delle regole per gli elementi di proprietà è che nessun altro elemento può essere visualizzate nel `Label.TextColor` tag. Il valore della proprietà è sempre definito come contenuto tra l'inizio di elemento di proprietà e i tag di fine.

È possibile usare la sintassi dell'elemento di proprietà in più di una proprietà:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center">
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

Oppure è possibile usare la sintassi dell'elemento di proprietà per tutte le proprietà:

```xaml
<Label>
    <Label.Text>
        Hello, XAML!
    </Label.Text>
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
    <Label.VerticalOptions>
        Center
    </Label.VerticalOptions>
</Label>
```

Inizialmente, la sintassi dell'elemento di proprietà può sembrare una sostituzione di operazioni tediose non necessaria per un elemento relativamente semplice e in questi esempi è certamente il caso.

Tuttavia, la sintassi dell'elemento di proprietà diventa essenziale quando il valore di una proprietà è troppo complesso per essere espresso come una stringa semplice. All'interno dei tag di elemento di proprietà è possibile creare un'istanza di un altro oggetto e impostare le relative proprietà. Ad esempio, è possibile impostare esplicitamente una proprietà, ad esempio `VerticalOptions` a un `LayoutOptions` valore con le impostazioni delle proprietà:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Un altro esempio: la `Grid` ha due proprietà denominate `RowDefinitions` e `ColumnDefinitions`. Queste due proprietà sono di tipo `RowDefinitionCollection` e `ColumnDefinitionCollection`, che sono raccolte di `RowDefinition` e `ColumnDefinition` oggetti. È necessario utilizzare la sintassi degli elementi di proprietà per impostare queste raccolte.

In questo caso è l'inizio del file XAML per un `GridDemoPage` (classe), che mostra i tag di elemento di proprietà per il `RowDefinitions` e `ColumnDefinitions` raccolte:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

Si noti che la sintassi abbreviata per la definizione delle celle di ridimensionamento automatico, le celle dei pixel larghezze e altezze e impostazioni a stella.

## <a name="attached-properties"></a>Proprietà associate

Si è appena visto che il `Grid` richiede che gli elementi di proprietà per il `RowDefinitions` e `ColumnDefinitions` raccolte per definire le righe e colonne. Tuttavia, deve anche esserci un modo per i programmatori indicare la riga e colonna in cui ogni figlio le `Grid` si trova.

All'interno del tag per ogni figlio le `Grid` si specifica la riga e la colonna di tale elemento figlio usando gli attributi seguenti:

-  `Grid.Row`
-  `Grid.Column`

I valori predefiniti di questi attributi sono 0. È inoltre possibile indicare se un elemento figlio si estende su più di una riga o colonna con questi attributi:

-  `Grid.RowSpan`
-  `Grid.ColumnSpan`

Questi due attributi hanno valori predefiniti pari a 1.

Ecco il file GridDemoPage.xaml completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>

        <Label Text="Autosized cell"
               Grid.Row="0" Grid.Column="0"
               TextColor="White"
               BackgroundColor="Blue" />

        <BoxView Color="Silver"
                 HeightRequest="0"
                 Grid.Row="0" Grid.Column="1" />

        <BoxView Color="Teal"
                 Grid.Row="1" Grid.Column="0" />

        <Label Text="Leftover space"
               Grid.Row="1" Grid.Column="1"
               TextColor="Purple"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two rows (or more if you want)"
               Grid.Row="0" Grid.Column="2" Grid.RowSpan="2"
               TextColor="Yellow"
               BackgroundColor="Blue"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two columns"
               Grid.Row="2" Grid.Column="0" Grid.ColumnSpan="2"
               TextColor="Blue"
               BackgroundColor="Yellow"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Fixed 100x100"
               Grid.Row="2" Grid.Column="2"
               TextColor="Aqua"
               BackgroundColor="Red"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

    </Grid>
</ContentPage>
```

Il `Grid.Row` e `Grid.Column` impostazioni pari a 0 non sono necessarie, ma sono in genere incluse per maggiore chiarezza.

Ecco l'effetto su tutte e tre le piattaforme:

[![](essential-xaml-syntax-images/griddemo.png "Layout griglia")](essential-xaml-syntax-images/griddemo-large.png#lightbox "Layout griglia")

A giudicare esclusivamente dalla sintassi, questi `Grid.Row`, `Grid.Column`, `Grid.RowSpan`, e `Grid.ColumnSpan` gli attributi sembrano essere di proprietà o campi statici `Grid`, ma è interessante, `Grid` non definisce un valore denominato `Row`, `Column`, `RowSpan`, o `ColumnSpan`.

Al contrario, `Grid` definisce quattro proprietà associabile denominata `RowProperty`, `ColumnProperty`, `RowSpanProperty`, e `ColumnSpanProperty`. Questi sono tipi speciali di proprietà associabili noto come *le proprietà associate*. Sono definiti dal `Grid` classe ma impostato nell'elemento figlio del `Grid`.

Quando si vuole usare queste proprietà nel codice, associate le `Grid` classe fornisce metodi statici denominati `SetRow`, `GetColumn`e così via. Ma in XAML, tali proprietà vengono impostate come attributi in elementi figlio del `Grid` usando nomi di proprietà semplici.

Le proprietà associate sono sempre riconoscibile nei file XAML come attributi che contiene una classe sia un nome di proprietà separati da un punto. Vengono chiamati *le proprietà associate* perché sono definiti da una classe (in questo caso `Grid`) ma collegato ad altri oggetti (in questo caso, gli elementi figlio del `Grid`). Durante il layout, il `Grid` può interrogare i valori di queste proprietà associate a sapere dove posizionare ogni elemento figlio.

Il `AbsoluteLayout` classe definisce due proprietà associata denominata `LayoutBounds` e `LayoutFlags`. Ecco un motivo a scacchi realizzato con il posizionamento proporzionale e le funzionalità di ridimensionamento di `AbsoluteLayout`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.AbsoluteDemoPage"
             Title="Absolute Demo Page">

    <AbsoluteLayout BackgroundColor="#FF8080">
        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

  </AbsoluteLayout>
</ContentPage>
```

Ed è qui:

[![](essential-xaml-syntax-images/absolutedemo-large.png "Layout assoluto")](essential-xaml-syntax-images/absolutedemo-large.png#lightbox "Layout assoluto")

Per codice simile al seguente, si potrebbero mettere in dubbio le conoscenze dell'uso di XAML. Certamente, la ripetizione e sistematicità del `LayoutBounds` rettangolo suggerisce potrà essere realizzato meglio in codice.

Si tratta di un problema legittimo e non determina alcun problema con bilanciamento del carico l'uso di codice e markup durante la definizione delle interfacce utente. È facile definire alcuni degli oggetti visivi in XAML e quindi usare il costruttore del file code-behind per aggiungere alcuni altri oggetti visivi che potrebbero essere generati meglio nei cicli.

## <a name="content-properties"></a>Proprietà di contenuto

Negli esempi precedenti, il `StackLayout`, `Grid`, e `AbsoluteLayout` gli oggetti vengono impostati il `Content` proprietà del `ContentPage`, e gli elementi figlio di questi layout sono effettivamente gli elementi nel `Children` raccolta. Ma questi `Content` e `Children` proprietà sono disponibile una destinazione nel file XAML.

È certamente possibile includere il `Content` e `Children` delle proprietà come elementi di proprietà, ad esempio nel **XamlPlusCode** esempio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout.Children>
                <Slider VerticalOptions="CenterAndExpand"
                        ValueChanged="OnSliderValueChanged" />

                <Label x:Name="valueLabel"
                       Text="A simple Label"
                       FontSize="Large"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand" />

                <Button Text="Click Me!"
                      HorizontalOptions="Center"
                      VerticalOptions="CenterAndExpand"
                      Clicked="OnButtonClicked" />
            </StackLayout.Children>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

La domanda reale è: perché sono questi elementi property *non* obbligatorio nel file XAML?

Gli elementi definiti in xamarin. Forms per l'utilizzo in XAML possono avere una proprietà contrassegnata nella `ContentProperty` attributo della classe. Se cerca di `ContentPage` classe nella documentazione online di xamarin. Forms, si noterà questo attributo:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Ciò significa che il `Content` i tag di elemento di proprietà non sono necessari. Qualsiasi contenuto XML visualizzato tra l'inizio e fine `ContentPage` si presuppone che i tag per è possibile assegnare il `Content` proprietà.

 `StackLayout`, `Grid`, `AbsoluteLayout`, e `RelativeLayout` derivano tutte dalla `Layout<View>`, e se è cercare `Layout<T>` nella documentazione di xamarin. Forms, si noterà un altro `ContentProperty` attributo:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Che consente il contenuto del layout di essere automaticamente aggiunti per il `Children` insieme senza esplicita `Children` i tag di elemento di proprietà.

Altre classi dispongono inoltre `ContentProperty` definizioni dell'attributo. Ad esempio, la proprietà content del `Label` è `Text`. Controllare la documentazione dell'API per altri utenti.

## <a name="platform-differences-with-onplatform"></a>Differenze tra le piattaforme con OnPlatform

Nelle applicazioni a pagina singola, è comune per impostare il `Padding` proprietà nella pagina per evitare di sovrascrivere la barra di stato iOS. Nel codice, è possibile usare il `Device.RuntimePlatform` proprietà per questo scopo:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

È anche possibile eseguire un'operazione simile in XAML usando il `OnPlatform` e `On` classi. Prima di tutto includere gli elementi di proprietà per il `Padding` proprietà nella parte superiore della pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

All'interno di questi tag includono un `OnPlatform` tag. `OnPlatform` è una classe generica. È necessario specificare l'argomento tipo generico, in questo caso `Thickness`, ovvero il tipo di `Padding` proprietà. Fortunatamente, è un attributo XAML in modo specifico per definire argomenti generici chiamati `x:TypeArguments`. Questo deve corrispondere al tipo della proprietà che si imposta:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">

        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`OnPlatform` include una proprietà denominata `Platforms` vale a dire un' `IList` di `On` oggetti. Usare i tag di elemento di proprietà per la proprietà:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>

            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

A questo punto aggiungere `On` elementi. Per ciascuna di esse impostata la `Platform` proprietà e il `Value` proprietà di markup per il `Thickness` proprietà:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>
                <On Platform="iOS" Value="0, 20, 0, 0" />
                <On Platform="Android" Value="0, 0, 0, 0" />
                <On Platform="UWP" Value="0, 0, 0, 0" />
            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Questo markup può essere semplificato. La proprietà content del `OnPlatform` è `Platforms`, in modo che i tag di elemento di proprietà possono essere rimossi:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android" Value="0, 0, 0, 0" />
            <On Platform="UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Il `Platform` proprietà di `On` JE typu `IList<string>`, pertanto se i valori sono uguali, è possibile includere più piattaforme:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android, UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Poiché Android e UWP sono impostate sul valore predefinito di `Padding`, che può essere rimosso tag:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Questo è il modo standard per impostare un dipendente dalla piattaforma `Padding` proprietà in XAML. Se il `Value` impostazione non può essere rappresentata da un'unica stringa, è possibile definire gli elementi di proprietà per tale:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS">
                <On.Value>
                    0, 20, 0, 0
                </On.Value>
            </On>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

## <a name="summary"></a>Riepilogo

Con gli elementi di proprietà e le proprietà associate, gran parte della sintassi XAML di base è stata stabilita. Tuttavia, talvolta è necessario impostare le proprietà agli oggetti in modo indiretto, ad esempio, da un dizionario risorse. Questo approccio è descritto nella prossima parte parte [3. Le estensioni di Markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).



## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introduzione a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Nozioni di base sul data binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Da Data Binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
