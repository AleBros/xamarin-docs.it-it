---
title: Parte 2. Sintassi XAML essenziale
description: Questo articolo illustra le funzionalità della sintassi XAML essenziale degli elementi di proprietà e le proprietà associate.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: f79a07a04eddeea1441f7938fdef210a37fb920a
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305493"
---
# <a name="part-2-essential-xaml-syntax"></a>Parte 2. Sintassi XAML essenziale

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML è progettato principalmente per la creazione di istanze e l'inizializzazione di oggetti. Spesso, tuttavia, le proprietà devono essere impostate su oggetti complessi che non possono essere facilmente rappresentati come stringhe XML e talvolta le proprietà definite da una classe devono essere impostate su una classe figlio. Queste due esigenze richiedono le funzionalità di sintassi XAML essenziali degli elementi proprietà e delle proprietà associate._

## <a name="property-elements"></a>Proprietà elementi

In XAML, proprietà delle classi in genere sono impostate come attributi XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Tuttavia, vi è un modo alternativo per impostare una proprietà in XAML. Per provare questa alternativa con `TextColor`, eliminare prima l'impostazione di `TextColor` esistente:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Aprire il tag `Label` elemento vuoto separandoli con i tag di inizio e di fine:

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

Questi due modi per specificare la proprietà `TextColor` sono equivalenti dal punto di vista funzionale, ma non utilizzano i due modi per la stessa proprietà, in quanto ciò consentirebbe di impostare la proprietà due volte e potrebbe essere ambigua.

Con questa nuova sintassi, è possono che vengano introdotti alcuni termini utili:

- `Label` è un *elemento oggetto*. È un oggetto di xamarin. Forms espresso come un elemento XML.
- `Text`, `VerticalOptions`, `FontAttributes` e `FontSize` sono *attributi della proprietà*. Sono le proprietà di xamarin. Forms espresse come attributi XML.
- Nel frammento finale `TextColor` è diventato un *elemento Property*. Si tratta di una proprietà di xamarin. Forms, ma ora è un elemento XML.

La definizione della proprietà di elementi potrebbero a prima di tutto sembra essere una violazione della sintassi XML, ma non è. Il periodo di ha alcun significato speciale in XML. Per un decodificatore XML, `Label.TextColor` è semplicemente un normale elemento figlio.

In XAML, tuttavia, questa sintassi è molto speciale. Una delle regole per gli elementi Property è che non è possibile visualizzare nient'altro nel tag `Label.TextColor`. Il valore della proprietà è sempre definito come contenuto tra l'inizio di elemento di proprietà e i tag di fine.

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

Tuttavia, la sintassi dell'elemento di proprietà diventa essenziale quando il valore di una proprietà è troppo complesso per essere espresso come una stringa semplice. All'interno dei tag di elemento di proprietà è possibile creare un'istanza di un altro oggetto e impostare le relative proprietà. Ad esempio, è possibile impostare in modo esplicito una proprietà, ad esempio `VerticalOptions` su un valore `LayoutOptions` con le impostazioni delle proprietà:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Un altro esempio: il `Grid` dispone di due proprietà denominate `RowDefinitions` e `ColumnDefinitions`. Queste due proprietà sono di tipo `RowDefinitionCollection` e `ColumnDefinitionCollection`, ovvero raccolte di oggetti `RowDefinition` e `ColumnDefinition`. È necessario utilizzare la sintassi degli elementi di proprietà per impostare queste raccolte.

Ecco l'inizio del file XAML per una classe `GridDemoPage`, che mostra i tag dell'elemento proprietà per le raccolte `RowDefinitions` e `ColumnDefinitions`:

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

Si è appena notato che la `Grid` richiede elementi Property per le raccolte `RowDefinitions` e `ColumnDefinitions` per definire le righe e le colonne. Tuttavia, è necessario che il programmatore indichi anche la riga e la colonna in cui si trova ogni elemento figlio del `Grid`.

All'interno del tag per ogni elemento figlio della `Grid` si specificano la riga e la colonna di tale elemento figlio usando gli attributi seguenti:

- `Grid.Row`
- `Grid.Column`

I valori predefiniti di questi attributi sono 0. È inoltre possibile indicare se un elemento figlio si estende su più di una riga o colonna con questi attributi:

- `Grid.RowSpan`
- `Grid.ColumnSpan`

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

Le impostazioni `Grid.Row` e `Grid.Column` di 0 non sono necessarie, ma sono generalmente incluse a scopo di chiarezza.

Ecco l'aspetto:

[Layout griglia ![](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

A giudicare esclusivamente dalla sintassi, questi attributi `Grid.Row`, `Grid.Column`, `Grid.RowSpan`e `Grid.ColumnSpan` sembrano essere campi statici o proprietà di `Grid`, ma è abbastanza interessante `Grid` non definisce alcun nome `Row`, `Column`, `RowSpan`o `ColumnSpan`.

Al contrario, `Grid` definisce quattro proprietà associabili denominate `RowProperty`, `ColumnProperty`, `RowSpanProperty`e `ColumnSpanProperty`. Si tratta di tipi speciali di proprietà associabili note come *proprietà associate*. Sono definiti dalla classe `Grid` ma impostati sui figli del `Grid`.

Quando si desidera utilizzare queste proprietà associate nel codice, la classe `Grid` fornisce metodi statici denominati `SetRow`, `GetColumn`e così via. Tuttavia, in XAML queste proprietà associate vengono impostate come attributi negli elementi figlio della `Grid` usando nomi di proprietà semplici.

Le proprietà associate sono sempre riconoscibile nei file XAML come attributi che contiene una classe sia un nome di proprietà separati da un punto. Sono denominate *proprietà associate* perché sono definite da una classe (in questo caso `Grid`) ma collegate ad altri oggetti (in questo caso, gli elementi figlio del `Grid`). Durante il layout, il `Grid` può interrogare i valori di queste proprietà associate per capire dove posizionare ogni elemento figlio.

La classe `AbsoluteLayout` definisce due proprietà associate denominate `LayoutBounds` e `LayoutFlags`. Ecco un modello a scacchi realizzato usando le funzionalità di posizionamento e dimensionamento proporzionali dei `AbsoluteLayout`:

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

[![layout assoluto](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

Per codice simile al seguente, si potrebbero mettere in dubbio le conoscenze dell'uso di XAML. Certamente, la ripetizione e la regolarità del rettangolo `LayoutBounds` suggeriscono che è possibile realizzarla nel codice.

Si tratta di un problema legittimo e non determina alcun problema con bilanciamento del carico l'uso di codice e markup durante la definizione delle interfacce utente. È facile definire alcuni degli oggetti visivi in XAML e quindi usare il costruttore del file code-behind per aggiungere alcuni altri oggetti visivi che potrebbero essere generati meglio nei cicli.

## <a name="content-properties"></a>Proprietà di contenuto

Negli esempi precedenti, gli oggetti `StackLayout`, `Grid`e `AbsoluteLayout` vengono impostati sulla proprietà `Content` del `ContentPage`e gli elementi figlio di questi layout sono effettivamente elementi nella raccolta di `Children`. Tuttavia, queste proprietà `Content` e `Children` non sono presenti nel file XAML.

È certamente possibile includere le proprietà `Content` e `Children` come elementi Property, ad esempio nell'esempio **XamlPlusCode** :

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

La domanda reale è: perché questi elementi di proprietà *non* sono necessari nel file XAML?

Gli elementi definiti in Novell. Forms da usare in XAML possono avere una proprietà contrassegnata nell'attributo `ContentProperty` della classe. Se si cerca la classe `ContentPage` nella documentazione online di Novell. Forms, verrà visualizzato questo attributo:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Ciò significa che i tag dell'elemento proprietà `Content` non sono obbligatori. Si presuppone che qualsiasi contenuto XML visualizzato tra i tag di inizio e di fine `ContentPage` venga assegnato alla proprietà `Content`.

 `StackLayout`, `Grid`, `AbsoluteLayout`e `RelativeLayout` derivano tutti da `Layout<View>`. Se si cerca `Layout<T>` nella documentazione di Novell. Forms, verrà visualizzato un altro attributo `ContentProperty`:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Che consente di aggiungere automaticamente il contenuto del layout alla raccolta `Children` senza tag di elemento proprietà `Children` espliciti.

Altre classi hanno anche `ContentProperty` definizioni di attributo. Ad esempio, la proprietà Content di `Label` è `Text`. Controllare la documentazione dell'API per altri utenti.

## <a name="platform-differences-with-onplatform"></a>Differenze tra le piattaforme con OnPlatform

Nelle applicazioni a pagina singola, è normale impostare la proprietà `Padding` nella pagina per evitare di sovrascrivere la barra di stato di iOS. Nel codice è possibile usare la proprietà `Device.RuntimePlatform` a questo scopo:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

È anche possibile eseguire operazioni simili in XAML usando le classi [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) e [`On`](xref:Xamarin.Forms.On) . Prima includere gli elementi Property per la proprietà `Padding` nella parte superiore della pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

In questi tag includere un tag `OnPlatform`. `OnPlatform` è una classe generica. È necessario specificare l'argomento di tipo generico, in questo caso `Thickness`, che è il tipo di `Padding` proprietà. Fortunatamente, esiste un attributo XAML specifico per definire argomenti generici denominati `x:TypeArguments`. Questo deve corrispondere al tipo della proprietà che si imposta:

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

`OnPlatform` dispone di una proprietà denominata `Platforms` che è un `IList` di oggetti `On`. Usare i tag di elemento di proprietà per la proprietà:

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

Aggiungere ora `On` elementi. Per ognuno di essi, impostare la proprietà `Platform` e la proprietà `Value` su markup per la proprietà `Thickness`:

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

Questo markup può essere semplificato. La proprietà Content di `OnPlatform` è `Platforms`, quindi è possibile rimuovere i tag dell'elemento Property:

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

La proprietà `Platform` di `On` è di tipo `IList<string>`, pertanto è possibile includere più piattaforme se i valori sono uguali:

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

Poiché Android e UWP sono impostati sul valore predefinito di `Padding`, il tag può essere rimosso:

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

Questo è il modo standard per impostare una proprietà di `Padding` dipendente dalla piattaforma in XAML. Se l'impostazione del `Value` non può essere rappresentata da una singola stringa, è possibile definire gli elementi della proprietà:

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

> [!NOTE]
> Il `OnPlatform` estensione di markup può essere usato anche in XAML per personalizzare l'aspetto dell'interfaccia utente in base alla piattaforma. Fornisce le stesse funzionalità delle classi `OnPlatform` e `On`, ma con una rappresentazione più concisa. Per altre informazioni, vedere [estensione di markup onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="summary"></a>Riepilogo

Con gli elementi di proprietà e le proprietà associate, gran parte della sintassi XAML di base è stata stabilita. Tuttavia, talvolta è necessario impostare le proprietà agli oggetti in modo indiretto, ad esempio, da un dizionario risorse. Questo approccio è trattato nella parte successiva, parte [3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introduzione con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Dal data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
