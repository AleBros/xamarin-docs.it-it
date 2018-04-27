---
title: Parte 2. Sintassi XAML essenziali
description: XAML è progettato principalmente per la creazione e inizializzazione di oggetti. Spesso, devono essere impostate su oggetti complessi che non possono facilmente essere rappresentati come stringhe XML, ma in alcuni casi è necessario impostare le proprietà definite da una classe in una classe figlio. Queste due esigenze richiedono le funzionalità essenziali della sintassi XAML di elementi di proprietà e le proprietà associate.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: d0129ec9872d8e5270ed8f0072cff0035d4f5255
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2018
---
# <a name="part-2-essential-xaml-syntax"></a>Parte 2. Sintassi XAML essenziali

_XAML è progettato principalmente per la creazione e inizializzazione di oggetti. Spesso, devono essere impostate su oggetti complessi che non possono facilmente essere rappresentati come stringhe XML, ma in alcuni casi è necessario impostare le proprietà definite da una classe in una classe figlio. Queste due esigenze richiedono le funzionalità essenziali della sintassi XAML di elementi di proprietà e le proprietà associate._

## <a name="property-elements"></a>Elementi proprietà

In XAML, le proprietà delle classi vengono normalmente definite come attributi XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Tuttavia, è un modo alternativo per impostare una proprietà in XAML. Per provare questa alternativa con `TextColor`, prima di eliminare quello esistente `TextColor` impostazione:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Apre un elemento vuoto `Label` tag in base che lo separa in tag di inizio e fine:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

All'interno di tali tag, aggiungere i tag di inizio e fine costituita il nome della classe e un nome di proprietà separati da un punto:

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

Questi due modi per specificare il `TextColor` proprietà sono funzionalmente equivalenti, ma non utilizzare due modalità per la stessa proprietà perché che verrebbe in modo efficace impostando la proprietà due volte e potrebbe essere ambigua.

Con questa nuova sintassi, è possono che vengano introdotti alcuni termini utili:

-  `Label` è un *elemento oggetto*. È un oggetto di xamarin. Forms espresso come un elemento XML.
-  `Text`, `VerticalOptions`, `FontAttributes` e `FontSize` sono *attributi della proprietà*. Sono proprietà di xamarin. Forms espressa come attributi XML.
-  In questo frammento di codice finale, `TextColor` è diventato un *elemento proprietà*. È una proprietà di xamarin. Forms, ma ora è un elemento XML.


La definizione di proprietà di elementi potrebbero sembrare innanzitutto come una violazione della sintassi XML, ma non. Il periodo di hanno alcun significato speciale in XML. A un decodificatore XML, `Label.TextColor` è semplicemente un elemento figlio normale.

In XAML, tuttavia, questa sintassi è molto speciale. Una delle regole per gli elementi di proprietà è che può essere inclusi nessun altro elemento di `Label.TextColor` tag. Il valore della proprietà è sempre definito come contenuto tra i tag di fine e l'inizio elemento proprietà.

È possibile utilizzare la sintassi dell'elemento di proprietà in più di una proprietà:

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

In alternativa, è possibile usare la sintassi dell'elemento di proprietà per tutte le proprietà:

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

Inizialmente, la sintassi dell'elemento di proprietà potrebbe sembrare una sostituzione di operazioni tediose non necessaria per un elemento relativamente semplice e in questi esempi è certamente il caso.

Tuttavia, la sintassi dell'elemento di proprietà diventa essenziale quando il valore di una proprietà è troppo complesso per essere espresso come una stringa semplice. All'interno dei tag di elemento di proprietà è possibile creare un'istanza di un altro oggetto e impostare le relative proprietà. Ad esempio, è possibile in modo esplicito impostare una proprietà, ad esempio `VerticalOptions` per un `LayoutOptions` valore con le impostazioni delle proprietà:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Un altro esempio: il `Grid` ha due proprietà denominate `RowDefinitions` e `ColumnDefinitions`. Queste due proprietà sono di tipo `RowDefinitionCollection` e `ColumnDefinitionCollection`, che sono raccolte di `RowDefinition` e `ColumnDefinition` oggetti. È necessario utilizzare la sintassi degli elementi di proprietà per impostare queste raccolte.

Di seguito è l'inizio del file XAML per un `GridDemoPage` (classe), che mostra i tag di elemento di proprietà per il `RowDefinitions` e `ColumnDefinitions` raccolte:

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

Si noti la sintassi abbreviata per la definizione di celle ridimensionamento automatico, le celle di larghezza in pixel e l'altezza delle impostazioni a stella.

## <a name="attached-properties"></a>Proprietà associate

Abbiamo già visto che il `Grid` richiede che gli elementi di proprietà per il `RowDefinitions` e `ColumnDefinitions` raccolte per definire le righe e colonne. Tuttavia, è inoltre necessario un modo per i programmatori indicare la riga e colonna in cui ogni elemento figlio del `Grid` risiede.

All'interno del tag di ogni figlio di `Grid` si specifica la riga e la colonna di tale elemento figlio utilizzando i seguenti attributi:

-  `Grid.Row`
-  `Grid.Column`

I valori predefiniti di questi attributi sono di 0. È inoltre possibile indicare se un elemento figlio si estende su più di una riga o colonna con questi attributi:

-  `Grid.RowSpan`
-  `Grid.ColumnSpan`

Questi due attributi presentano valori predefiniti di 1.

Di seguito è riportato il file GridDemoPage.xaml completo:

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

Il `Grid.Row` e `Grid.Column` impostazioni pari a 0 non sono obbligatori ma in genere incluse per maggiore chiarezza.

Ecco l'aspetto in tutte e tre le piattaforme:

[![](essential-xaml-syntax-images/griddemo.png "Layout di griglia")](essential-xaml-syntax-images/griddemo-large.png#lightbox "Layout di griglia")

Giudicare esclusivamente dalla sintassi, questi `Grid.Row`, `Grid.Column`, `Grid.RowSpan`, e `Grid.ColumnSpan` gli attributi vengono visualizzati come campi statici o le proprietà di `Grid`, ma è interessante, `Grid` non definisce alcun elemento denominato `Row`, `Column`, `RowSpan`, o `ColumnSpan`.

In alternativa, `Grid` definisce quattro proprietà associabile denominata `RowProperty`, `ColumnProperty`, `RowSpanProperty`, e `ColumnSpanProperty`. Questi sono tipi speciali di proprietà associabile, noto come *le proprietà associate*. Vengono definiti dal `Grid` classe ma impostare negli elementi figlio del `Grid`.

Quando si desidera utilizzare queste proprietà nel codice, associate il `Grid` classe fornisce metodi statici denominati `SetRow`, `GetColumn`e così via. Ma in XAML, le proprietà associate vengono impostate come attributi in elementi figlio del `Grid` utilizzando nomi di proprietà semplici.

Le proprietà associate sono sempre riconoscibile nei file XAML come attributi che contiene una classe sia un nome di proprietà separati da un punto. Vengono chiamati *le proprietà associate* perché vengono definiti da una classe (in questo caso, `Grid`) ma collegato ad altri oggetti (in questo caso, i figli la `Grid`). Durante il layout di `Grid` può interrogare i valori di queste proprietà associate per conoscere la posizione di ogni elemento figlio.

Il `AbsoluteLayout` classe definisce le proprietà associate due denominate `LayoutBounds` e `LayoutFlags`. Ecco un motivo a scacchi realizzato tramite il posizionamento proporzionale e funzionalità di ridimensionamento di `AbsoluteLayout`:

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

E in questo caso è:

[![](essential-xaml-syntax-images/absolutedemo-large.png "Layout assoluto")](essential-xaml-syntax-images/absolutedemo-large.png#lightbox "Layout assoluto")

Per simile al seguente, si potrebbero domanda le conoscenze della tramite XAML. Ovviamente la ripetizione e regolarità del `LayoutBounds` rettangolo suggerisce potrebbe essere realizzato meglio in codice.

È certamente un problema legittimo e nessun problema con bilanciamento del carico l'uso di codice e markup, quando si definiscono le interfacce utente. È facile definire alcuni degli oggetti visivi in XAML e quindi utilizzare il costruttore del file code-behind per aggiungere alcune più oggetti visivi che potrebbero essere meglio generati nei cicli.

## <a name="content-properties"></a>Proprietà di contenuto

Negli esempi precedenti, il `StackLayout`, `Grid`, e `AbsoluteLayout` oggetti vengono impostati il `Content` proprietà del `ContentPage`, e gli elementi figlio di questi layout sono elementi effettivamente il `Children` insieme. Ma queste `Content` e `Children` non sono nel file XAML.

È certamente possibile includere il `Content` e `Children` proprietà come elementi di proprietà, ad esempio nel **XamlPlusCode** esempio:

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

La domanda reale è: perché sono questi elementi proprietà *non* necessarie nel file XAML?

Gli elementi definiti in xamarin. Forms per l'utilizzo in XAML possono avere una proprietà contrassegnata nel `ContentProperty` attributo della classe. Se ricerca la `ContentPage` classe xamarin. Forms nella documentazione in linea, verrà visualizzato questo attributo:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Ciò significa che il `Content` i tag di elemento di proprietà non sono obbligatori. Qualsiasi contenuto XML che viene visualizzato tra l'inizio e fine `ContentPage` tag si presuppone che per è possibile assegnare il `Content` proprietà.

 `StackLayout`, `Grid`, `AbsoluteLayout`, e `RelativeLayout` derivano tutte dalla `Layout<View>`, e se la ricerca di `Layout<T>` nella documentazione di xamarin. Forms, si noterà un altro `ContentProperty` attributo:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Che consente contenuto del layout di aggiungere automaticamente il `Children` insieme senza esplicita `Children` tag di elemento di proprietà.

Altre classi hanno `ContentProperty` le definizioni di attributo. Ad esempio, la proprietà content del `Label` è `Text`. Controllare la documentazione dell'API di altri utenti.

## <a name="platform-differences-with-onplatform"></a>Differenze di piattaforma con OnPlatform

In applicazioni a pagina singola, è comune per impostare il `Padding` proprietà nella pagina per evitare di sovrascrivere la barra di stato di iOS. Nel codice, è possibile utilizzare il `Device.RuntimePlatform` proprietà per questo scopo:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

È inoltre possibile eseguire un comportamento simile in XAML tramite il `OnPlatform` e `On` classi. Per prima cosa includere elementi di proprietà per il `Padding` proprietà nella parte superiore della pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

All'interno di tali tag, includere un `OnPlatform` tag. `OnPlatform` è una classe generica. È necessario specificare l'argomento di tipo generico, in questo caso, `Thickness`, ovvero il tipo di `Padding` proprietà. Fortunatamente, è un attributo XAML in modo specifico per definire argomenti generici chiamati `x:TypeArguments`. Questo deve corrispondere al tipo della proprietà a cui che si sta impostando:

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

`OnPlatform` contiene una proprietà denominata `Platforms` vale a dire un `IList` di `On` oggetti. Utilizzare il tag di elemento di proprietà per la proprietà:

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

A questo punto aggiungere `On` elementi. Per ogni onem impostare il `Platform` proprietà e `Value` proprietà per il markup per il `Thickness` proprietà:

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

Questo codice può essere semplificato. La proprietà content del `OnPlatform` è `Platforms`, pertanto è possibile rimuovere i tag di elemento di proprietà:

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

Il `Platform` proprietà di `On` è di tipo `IList<string>`, pertanto se i valori sono uguali, è possibile includere più piattaforme:

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

Android e UWP sono impostate sul valore predefinito di `Padding`, che può essere rimosso tag:

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

Questo è il modo standard per impostare un dipendente dalla piattaforma `Padding` proprietà in XAML. Se il `Value` impostazione non può essere rappresentata da un'unica stringa, è possibile definire elementi di proprietà per tale:

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

Con gli elementi di proprietà e le proprietà associate, gran parte della sintassi XAML base è stata stabilita. Tuttavia, talvolta è necessario impostare le proprietà per oggetti in un modo indiretto, ad esempio, da un dizionario risorse. Questo approccio viene descritta nella sezione successiva, parte [3. Le estensioni di Markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).



## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introduzione a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Nozioni di base sul data binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Da un'associazione dati a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
