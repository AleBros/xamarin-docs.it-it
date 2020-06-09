---
Titolo: "parte 2. Sintassi XAML essenziale "Description:" in questo articolo vengono illustrate le funzionalità di sintassi XAML essenziali degli elementi proprietà e delle proprietà associate.
ms. prod: Novell MS. AssetID: 4022F1DC-3802-4635-A553-688ABD3F0D5A ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/25/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="part-2-essential-xaml-syntax"></a>Parte 2. Sintassi XAML essenziale

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML è progettato principalmente per la creazione di istanze e l'inizializzazione di oggetti. Spesso, tuttavia, le proprietà devono essere impostate su oggetti complessi che non possono essere facilmente rappresentati come stringhe XML e talvolta le proprietà definite da una classe devono essere impostate su una classe figlio. Queste due esigenze richiedono le funzionalità di sintassi XAML essenziali degli elementi proprietà e delle proprietà associate._

## <a name="property-elements"></a>Elementi Property

In XAML le proprietà delle classi vengono in genere impostate come attributi XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Tuttavia, esiste una modalità alternativa per impostare una proprietà in XAML. Per provare questa alternativa con `TextColor` , eliminare prima l' `TextColor` impostazione esistente:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Aprire il tag di elemento vuoto `Label` separandoli con i tag di inizio e di fine:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

All'interno di questi tag aggiungere i tag di inizio e di fine costituiti dal nome della classe e da un nome di proprietà separati da un punto:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

Impostare il valore della proprietà come contenuto di questi nuovi tag, come segue:

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

Questi due modi per specificare la `TextColor` proprietà sono equivalenti dal punto di vista funzionale, ma non usano i due modi per la stessa proprietà, in quanto ciò consentirebbe di impostare la proprietà due volte e potrebbe essere ambigua.

Con questa nuova sintassi è possibile introdurre una terminologia pratica:

- `Label`è un *elemento oggetto*. Si tratta di un Xamarin.Forms oggetto espresso come elemento XML.
- `Text`, `VerticalOptions` `FontAttributes` e `FontSize` sono attributi della *Proprietà*. Si tratta Xamarin.Forms di proprietà espresse come attributi XML.
- Nel frammento finale `TextColor` è diventato un *elemento Property*. Si tratta di una Xamarin.Forms proprietà che è ora un elemento XML.

La definizione degli elementi proprietà potrebbe sembrare inizialmente una violazione della sintassi XML, ma non lo è. Il periodo non ha un significato speciale in XML. Per un decodificatore XML, `Label.TextColor` è semplicemente un normale elemento figlio.

In XAML, tuttavia, questa sintassi è molto speciale. Una delle regole per gli elementi Property è che non è possibile visualizzare nient'altro nel `Label.TextColor` tag. Il valore della proprietà è sempre definito come contenuto tra i tag di inizio e di fine dell'elemento proprietà.

È possibile utilizzare la sintassi dell'elemento proprietà in più di una proprietà:

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

In alternativa, è possibile usare la sintassi dell'elemento proprietà per tutte le proprietà:

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

Inizialmente, la sintassi degli elementi di proprietà potrebbe sembrare una sostituzione non necessaria a lungo termine per qualcosa di relativamente semplice e in questi esempi è certamente il caso.

Tuttavia, la sintassi dell'elemento proprietà diventa essenziale quando il valore di una proprietà è troppo complesso per essere espresso come stringa semplice. All'interno dei tag dell'elemento Property è possibile creare un'istanza di un altro oggetto e impostarne le proprietà. Ad esempio, è possibile impostare in modo esplicito una proprietà, ad esempio, `VerticalOptions` su un `LayoutOptions` valore con le impostazioni delle proprietà:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Un altro esempio: `Grid` dispone di due proprietà denominate `RowDefinitions` e `ColumnDefinitions` . Queste due proprietà sono di tipo `RowDefinitionCollection` e `ColumnDefinitionCollection` , che sono raccolte di `RowDefinition` `ColumnDefinition` oggetti e. Per impostare queste raccolte, è necessario usare la sintassi dell'elemento Property.

Ecco l'inizio del file XAML per una `GridDemoPage` classe, che mostra i tag dell'elemento Property per le `RowDefinitions` `ColumnDefinitions` raccolte e:

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

Si noti la sintassi abbreviata per la definizione di celle a dimensione automatica, celle di larghezze e altezze dei pixel e impostazioni a stella.

## <a name="attached-properties"></a>Proprietà associate

Per `Grid` `RowDefinitions` `ColumnDefinitions` definire le righe e le colonne, è necessario che gli elementi della proprietà siano necessari per le raccolte e. Tuttavia, è necessario che il programmatore indichi anche la riga e la colonna in cui si trova ogni elemento figlio di `Grid` .

All'interno del tag per ogni elemento figlio di `Grid` è possibile specificare la riga e la colonna di tale elemento figlio usando gli attributi seguenti:

- `Grid.Row`
- `Grid.Column`

I valori predefiniti di questi attributi sono 0. È inoltre possibile indicare se un figlio si estende su più di una riga o colonna con questi attributi:

- `Grid.RowSpan`
- `Grid.ColumnSpan`

Questi due attributi hanno valori predefiniti 1.

Ecco il file GridDemoPage. XAML completo:

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

Le `Grid.Row` `Grid.Column` Impostazioni e di 0 non sono necessarie, ma sono in genere incluse per motivi di chiarezza.

Ecco come si presenta:

[![Layout griglia](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

A giudicare esclusivamente dalla sintassi, questi `Grid.Row` attributi,, `Grid.Column` `Grid.RowSpan` e `Grid.ColumnSpan` sembrano essere campi statici o proprietà di `Grid` , ma è interessante notare che `Grid` non definisce elementi denominati `Row` ,, `Column` `RowSpan` o `ColumnSpan` .

Al contrario, `Grid` definisce quattro proprietà associabili denominate `RowProperty` ,, `ColumnProperty` `RowSpanProperty` e `ColumnSpanProperty` . Si tratta di tipi speciali di proprietà associabili note come *proprietà associate*. Sono definiti dalla `Grid` classe ma impostati sui figli di `Grid` .

Quando si desidera utilizzare queste proprietà associate nel codice, la `Grid` classe fornisce metodi statici denominati `SetRow` , `GetColumn` e così via. Tuttavia, in XAML queste proprietà associate vengono impostate come attributi negli elementi figlio di `Grid` utilizzando nomi di proprietà semplici.

Le proprietà associate sono sempre riconoscibili nei file XAML come attributi contenenti una classe e un nome di proprietà separati da un punto. Sono denominate *proprietà associate* perché sono definite da una classe (in questo caso, `Grid` ) ma collegate ad altri oggetti (in questo caso, gli elementi figlio di `Grid` ). Durante il layout, `Grid` può interrogare i valori di queste proprietà associate per capire dove posizionare ogni elemento figlio.

La `AbsoluteLayout` classe definisce due proprietà associate denominate `LayoutBounds` e `LayoutFlags` . Ecco un modello a scacchi realizzato usando le funzionalità di posizionamento e dimensionamento proporzionale di `AbsoluteLayout` :

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

Ecco:

[![Layout assoluto](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

Per qualcosa di simile, è possibile mettere in discussione la saggezza dell'uso di XAML. Certamente, la ripetizione e la regolarità del `LayoutBounds` rettangolo suggeriscono che può essere meglio realizzata nel codice.

Questo è certamente un problema legittimo e non ci sono problemi con il bilanciamento dell'uso di codice e markup durante la definizione delle interfacce utente. È facile definire alcuni degli oggetti visivi in XAML e quindi usare il costruttore del file code-behind per aggiungere altri oggetti visivi che potrebbero essere generati in cicli migliori.

## <a name="content-properties"></a>Proprietà di contenuto

Negli esempi precedenti, gli `StackLayout` oggetti, `Grid` e `AbsoluteLayout` vengono impostati sulla `Content` proprietà di `ContentPage` e gli elementi figlio di questi layout sono effettivamente elementi nella `Children` raccolta. Tuttavia `Content` , queste `Children` proprietà e non sono presenti nel file XAML.

È certamente possibile includere le `Content` `Children` proprietà e come elementi Property, ad esempio nell'esempio **XamlPlusCode** :

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

Gli elementi definiti in Xamarin.Forms per l'utilizzo in XAML possono avere una proprietà contrassegnata nell' `ContentProperty` attributo della classe. Se si cerca la `ContentPage` classe nella Xamarin.Forms documentazione online, verrà visualizzato questo attributo:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Ciò significa che i `Content` tag dell'elemento proprietà non sono obbligatori. Si presuppone che qualsiasi contenuto XML visualizzato tra i tag di inizio e di fine venga `ContentPage` assegnato alla `Content` Proprietà.

 `StackLayout`, `Grid` , `AbsoluteLayout` e `RelativeLayout` derivano tutti da `Layout<View>` . Se si cerca `Layout<T>` nella Xamarin.Forms documentazione, verrà visualizzato un altro `ContentProperty` attributo:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Che consente di aggiungere automaticamente il contenuto del layout alla `Children` raccolta senza `Children` tag di elemento proprietà espliciti.

Altre classi hanno anche `ContentProperty` definizioni di attributo. Ad esempio, la proprietà Content di `Label` è `Text` . Controllare la documentazione dell'API per altri utenti.

## <a name="platform-differences-with-onplatform"></a>Differenze di piattaforma con onplatform

Nelle applicazioni a pagina singola, è normale impostare la `Padding` proprietà nella pagina per evitare di sovrascrivere la barra di stato di iOS. Nel codice è possibile usare la `Device.RuntimePlatform` proprietà a questo scopo:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

È anche possibile eseguire operazioni simili in XAML usando le [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) classi e. Prima includere gli elementi Property per la `Padding` proprietà nella parte superiore della pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

In questi tag includere un `OnPlatform` tag. `OnPlatform`è una classe generica. È necessario specificare l'argomento di tipo generico, in questo caso, `Thickness` , che è il tipo della `Padding` Proprietà. Fortunatamente, esiste un attributo XAML specifico per definire argomenti generici chiamati `x:TypeArguments` . Deve corrispondere al tipo della proprietà che si sta impostando:

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

`OnPlatform`dispone di una proprietà denominata `Platforms` che è un oggetto `IList` di `On` oggetti. Usare i tag dell'elemento proprietà per la proprietà:

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

Aggiungere ora `On` elementi. Per ciascuna di esse, impostare la proprietà `Platform` e la `Value` proprietà su markup per la `Thickness` proprietà:

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

Questo markup può essere semplificato. La proprietà Content di `OnPlatform` è `Platforms` , quindi è possibile rimuovere i tag dell'elemento Property:

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

La `Platform` proprietà di `On` è di tipo `IList<string>` , pertanto è possibile includere più piattaforme se i valori sono uguali:

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

Poiché Android e UWP sono impostati sul valore predefinito, il `Padding` tag può essere rimosso:

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

Questo è il modo standard per impostare una proprietà dipendente dalla piattaforma `Padding` in XAML. Se l' `Value` impostazione non può essere rappresentata da una singola stringa, è possibile definire gli elementi della proprietà:

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
> L' `OnPlatform` estensione di markup può essere usata anche in XAML per personalizzare l'aspetto dell'interfaccia utente in base alla piattaforma. Fornisce le stesse funzionalità delle `OnPlatform` `On` classi e, ma con una rappresentazione più concisa. Per altre informazioni, vedere [estensione di markup onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="summary"></a>Summary

Con gli elementi proprietà e le proprietà associate, è stata stabilita gran parte della sintassi XAML di base. Tuttavia, a volte è necessario impostare le proprietà sugli oggetti in modo indiretto, ad esempio da un dizionario risorse. Questo approccio è trattato nella parte successiva, parte [3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introduzione con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Dal data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
