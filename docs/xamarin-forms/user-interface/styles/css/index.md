---
title: Applicazione di stili alle app Xamarin.Forms con Cascading Style Sheets (CSS)
description: Novell. Forms supporta gli elementi visivi di stile utilizzando Cascading Style Sheets (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2020
ms.openlocfilehash: 182bd088ba169dad6bdbbb4315a033925964f64e
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517557"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Applicazione di stili alle app Novell. Forms con Cascading Style Sheets (CSS)

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Novell. Forms supporta gli elementi visivi di stile utilizzando Cascading Style Sheets (CSS)._

Le applicazioni Novell. Forms possono essere con stile CSS. Un foglio di stile è costituito da un elenco di regole, in cui ogni regola è costituita da uno o più selettori e da un blocco di dichiarazione. Un blocco di dichiarazione è costituito da un elenco di dichiarazioni racchiuse tra parentesi graffe, con ogni dichiarazione costituita da una proprietà, da due punti e da un valore. Quando in un blocco sono presenti più dichiarazioni, viene inserito un punto e virgola come separatore. Nell'esempio di codice riportato di seguito viene illustrato un CSS conforme a Novell. Forms:

```css
navigationpage {
    -xf-bar-background-color: lightgray;
}

^contentpage {
    background-color: lightgray;
}

#listView {
    background-color: lightgray;
}

stacklayout {
    margin: 20;
}

.mainPageTitle {
    font-style: bold;
    font-size: medium;
}

.mainPageSubtitle {
    margin-top: 15;
}

.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}

listview image {
    height: 60;
    width: 60;
}

stacklayout>image {
    height: 200;
    width: 200;
}
```

In Novell. Forms, i fogli di stile CSS vengono analizzati e valutati in fase di esecuzione, anziché in fase di compilazione e i fogli di stile vengono analizzati di nuovo durante l'uso.

> [!NOTE]
> Attualmente, non è possibile eseguire tutte le operazioni di stile con lo stile XAML con CSS. Tuttavia, gli stili XAML possono essere utilizzati per integrare CSS per le proprietà attualmente non supportate da Novell. Forms. Per altre informazioni sugli stili XAML, vedere [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

L'esempio [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) illustra l'uso di CSS per applicare uno stile a un'app semplice ed è illustrato nelle schermate seguenti:

[![Pagina principale di MonkeyApp con stile CSS](css-images/MonkeyAppMainPage.png "Pagina principale di MonkeyApp con stile CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "Pagina principale di MonkeyApp con stile CSS")

[![Pagina dei dettagli di MonkeyApp con stile CSS](css-images/MonkeyAppDetailPage.png "Pagina dei dettagli di MonkeyApp con stile CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "Pagina dei dettagli di MonkeyApp con stile CSS")

## <a name="consuming-a-style-sheet"></a>Utilizzo di un foglio di stile

Il processo per l'aggiunta di un foglio di stile a una soluzione è il seguente:

1. Aggiungere un file CSS vuoto al progetto di libreria .NET Standard.
1. Impostare l'azione di compilazione del file CSS su **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Caricamento di un foglio di stile

Sono disponibili diversi approcci che è possibile utilizzare per caricare un foglio di stile.

> [!NOTE]
> Attualmente non è possibile modificare un foglio di stile in fase di esecuzione e fare in modo che il nuovo foglio di stile venga applicato.

### <a name="xaml"></a>XAML

Un foglio di stile può essere caricato e analizzato con [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) la classe prima di essere aggiunto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a un:

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

La [`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) proprietà specifica il foglio di stile come URI relativo alla posizione del file XAML di inclusione o rispetto alla radice del progetto se l'URI inizia con un oggetto `/`.

> [!WARNING]
> Il caricamento del file CSS non verrà eseguito se la relativa azione di compilazione non è impostata su **EmbeddedResource**.

In alternativa, è possibile caricare un foglio di stile e analizzarlo con [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) la classe, prima di aggiungerlo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a un oggetto, inserendolo in `CDATA` una sezione:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet>
            <![CDATA[
            ^contentpage {
                background-color: lightgray;
            }
            ]]>
        </StyleSheet>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Per altre informazioni sui dizionari risorse, vedere [dizionari risorse](~/xamarin-forms/xaml/resource-dictionaries.md).

### <a name="c"></a>C\#

In C# è possibile caricare un foglio di stile da un `StringReader` oggetto e aggiungerlo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a un oggetto:

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        using (var reader = new StringReader("^contentpage { background-color: lightgray; }"))
        {
            this.Resources.Add(StyleSheet.FromReader(reader));
        }
    }
}
```

L'argomento del `StyleSheet.FromReader` metodo è l'oggetto `TextReader` che ha letto il foglio di stile.

## <a name="selecting-elements-and-applying-properties"></a>Selezione di elementi e applicazione di proprietà

CSS usa i selettori per determinare gli elementi di destinazione. Gli stili con selettori corrispondenti vengono applicati consecutivamente nell'ordine delle definizioni. Gli stili definiti per un elemento specifico vengono sempre applicati per ultimi. Per altre informazioni sui [selettori supportati, vedere riferimento al selettore](#selector-reference).

CSS utilizza le proprietà per applicare uno stile a un elemento selezionato. Ogni proprietà dispone di un set di valori possibili e alcune proprietà possono influenzare qualsiasi tipo di elemento, mentre altre si applicano a gruppi di elementi. Per ulteriori informazioni sulle proprietà supportate, vedere [riferimento alle proprietà](#property-reference).

> [!IMPORTANT]
> Le variabili CSS non sono supportate.

### <a name="selecting-elements-by-type"></a>Selezione di elementi per tipo

Gli elementi nella struttura ad albero visuale possono essere selezionati per tipo con il `element` selettore senza distinzione tra maiuscole e minuscole

```css
stacklayout {
    margin: 20;
}
```

Questo selettore [`StackLayout`](xref:Xamarin.Forms.StackLayout) identifica tutti gli elementi nelle pagine che utilizzano il foglio di stile e imposta i relativi margini su uno spessore uniforme pari a 20.

> [!NOTE]
> Il `element` selettore non identifica le sottoclassi del tipo specificato.

### <a name="selecting-elements-by-base-class"></a>Selezione di elementi per classe di base

Gli elementi nella struttura ad albero visuale possono essere selezionati dalla classe base con il `^base` selettore senza distinzione tra maiuscole e minuscole

```css
^contentpage {
    background-color: lightgray;
}
```

Questo selettore [`ContentPage`](xref:Xamarin.Forms.ContentPage) identifica tutti gli elementi che utilizzano il foglio di stile e imposta il `lightgray`colore di sfondo su.

> [!NOTE]
> Il `^base` selettore è specifico di Novell. Forms e non fa parte della specifica CSS.

### <a name="selecting-an-element-by-name"></a>Selezione di un elemento in base al nome

È possibile selezionare singoli elementi nella struttura ad albero visuale con il `#id` selettore distinzione maiuscole/minuscole:

```css
#listView {
    background-color: lightgray;
}
```

Questo selettore identifica l' [`StyleId`](xref:Xamarin.Forms.Element.StyleId) elemento la cui proprietà `listView`è impostata su. Tuttavia, se la `StyleId` proprietà non è impostata, il selettore eseguirà il fallback `x:Name` utilizzando l'oggetto dell'elemento. Nell'esempio XAML seguente, quindi, `#listView` il selettore identificherà il [`ListView`](xref:Xamarin.Forms.ListView) cui `x:Name` attributo è impostato su `listView`e imposta il colore di sfondo su. `lightgray`

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView x:Name="listView" ...>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

### <a name="selecting-elements-with-a-specific-class-attribute"></a>Selezione di elementi con un attributo di classe specifico

Gli elementi con un attributo di classe specifico possono essere selezionati con il `.class` selettore di maiuscole e minuscole:

```css
.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}
```

Una classe CSS può essere assegnata a un elemento XAML impostando [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) la proprietà dell'elemento sul nome della classe CSS. Nell'esempio `.detailPageTitle` XAML seguente, quindi, gli stili definiti dalla classe vengono assegnati al primo [`Label`](xref:Xamarin.Forms.Label), mentre gli stili definiti dalla `.detailPageSubtitle` classe sono assegnati al secondo. `Label`

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            <Label ... StyleClass="detailPageTitle" />
            <Label ... StyleClass="detailPageSubtitle"/>
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

### <a name="selecting-child-elements"></a>Selezione di elementi figlio

È possibile selezionare elementi figlio nella struttura ad albero visuale con il selettore senza distinzione `element element` tra maiuscole e minuscole

```css
listview image {
    height: 60;
    width: 60;
}
```

Questo selettore [`Image`](xref:Xamarin.Forms.Image) identifica tutti gli elementi figlio [`ListView`](xref:Xamarin.Forms.ListView) di elementi e ne imposta l'altezza e la larghezza su 60. Nell'esempio XAML seguente, quindi, il `listview image` selettore identifica l' [`Image`](xref:Xamarin.Forms.Image) elemento figlio di [`ListView`](xref:Xamarin.Forms.ListView)e ne imposta l'altezza e la larghezza su 60.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView ...>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid>
                            ...
                            <Image ... />
                            ...
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> Il `element element` selettore non richiede che l'elemento figlio sia un figlio _diretto_ del padre: l'elemento figlio potrebbe avere un padre diverso. Si verifica una selezione purché un predecessore sia il primo elemento specificato.

### <a name="selecting-direct-child-elements"></a>Selezione di elementi figlio diretti

È possibile selezionare elementi figlio diretti nella struttura ad albero visuale con il selettore senza distinzione `element>element` tra maiuscole e minuscole:

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Questo selettore [`Image`](xref:Xamarin.Forms.Image) identifica tutti gli elementi che sono [`StackLayout`](xref:Xamarin.Forms.StackLayout) elementi figlio diretti di elementi e imposta l'altezza e la larghezza su 200. Nell'esempio XAML seguente, quindi, il `stacklayout>image` selettore identificherà l' [`Image`](xref:Xamarin.Forms.Image) oggetto che corrisponde a un figlio diretto [`StackLayout`](xref:Xamarin.Forms.StackLayout)di e ne imposta l'altezza e la larghezza su 200.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            ...
            <Image ... />
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

> [!NOTE]
> Il `element>element` selettore richiede che l'elemento figlio sia un figlio _diretto_ del padre.

## <a name="selector-reference"></a>Riferimento al selettore

I selettori CSS seguenti sono supportati da Novell. Forms:

|Selettore|Esempio|Descrizione|
|---|---|---|
|`.class`|`.header`|Seleziona tutti gli elementi con `StyleClass` la proprietà che contiene "header". Si noti che questo selettore distingue tra maiuscole e minuscole|
|`#id`|`#email`|Seleziona tutti gli elementi `StyleId` con impostato `email`su. Se `StyleId` non è impostato, effettuare il `x:Name`fallback a. Quando si usa XAML `x:Name` , è preferibile rispetto `StyleId`a. Si noti che questo selettore distingue tra maiuscole e minuscole|
|`*`|`*`|Seleziona tutti gli elementi.|
|`element`|`label`|Seleziona tutti gli elementi di `Label`tipo, ma non le sottoclassi. Si noti che questo selettore non distingue tra maiuscole e minuscole.|
|`^base`|`^contentpage`|Seleziona tutti gli elementi `ContentPage` con come classe di base, `ContentPage` incluso se stesso. Si noti che questo selettore non fa distinzione tra maiuscole e minuscole e non fa parte della specifica CSS.|
|`element,element`|`label,button`|Seleziona tutti `Button` gli elementi e `Label` tutti gli elementi. Si noti che questo selettore non distingue tra maiuscole e minuscole.|
|`element element`|`stacklayout label`|Seleziona tutti `Label` gli elementi all' `StackLayout`interno di un oggetto. Si noti che questo selettore non distingue tra maiuscole e minuscole.|
|`element>element`|`stacklayout>label`|Seleziona tutti `Label` gli elementi `StackLayout` con come elemento padre diretto. Si noti che questo selettore non distingue tra maiuscole e minuscole.|
|`element+element`|`label+entry`|Seleziona tutti `Entry` gli elementi direttamente dopo `Label`un oggetto. Si noti che questo selettore non distingue tra maiuscole e minuscole.|
|`element~element`|`label~entry`|Seleziona tutti `Entry` gli elementi preceduti da `Label`un oggetto. Si noti che questo selettore non distingue tra maiuscole e minuscole.|

Gli stili con selettori corrispondenti vengono applicati consecutivamente nell'ordine delle definizioni. Gli stili definiti per un elemento specifico vengono sempre applicati per ultimi.

> [!TIP]
> I selettori possono essere combinati senza limitazioni `StackLayout>ContentView>label.email`, ad esempio.

I selettori seguenti non sono attualmente supportati:

- `[attribute]`
- `@media` e `@supports`
- `:` e `::`

> [!NOTE]
> La specificità e le sostituzioni di specificità non sono supportate.

## <a name="property-reference"></a>Informazioni di riferimento sulle proprietà

Le proprietà CSS seguenti sono supportate da Novell. Forms (nella colonna **values** i tipi sono _Italic_, mentre i valori letterali stringa sono `gray`):

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_colore_ \|`initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_stringa_ \| di`initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_colore_ \|`initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_double_ \| `initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_double_ \| `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_colore_ \|`initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_double_ \| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \| float `auto` . \| `initial` Inoltre, è possibile specificare una percentuale nell'intervallo compreso tra 0% e 100% e il `%` segno.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_float_ \| `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_float_ \| `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_stringa_ \| di`initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_doppio_ \| _namedsize_ namedsize \|  `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_double_ \| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|_double_ \| `initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|_double_ \| `initial` |`line-height: 1.8;`|
|`margin`|`View`|_spessore_ \|`initial` |`margin: 6 12;`|
|`margin-left`|`View`|_spessore_ \|`initial` |`margin-left: 3;`|
|`margin-top`|`View`|_spessore_ \|`initial` |`margin-top: 2;`|
|`margin-right`|`View`|_spessore_ \|`initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_spessore_ \|`initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_INT_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_double_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_double_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_double_ \| `initial` |`opacity: .3;`|
|`order`|`VisualElement`|_INT_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_spessore_ \|`initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_double_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \| `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _double_ \| `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left`e `right` devono essere evitati negli ambienti da destra a sinistra.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _Double_, _Double_ \|`initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_double_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial`è un valore valido per tutte le proprietà. Cancella il valore (Reimposta impostazione predefinita) impostato da un altro stile.

Le proprietà seguenti non sono attualmente supportate:

- `all: initial`.
- Proprietà layout (casella o griglia).
- Proprietà a sintassi abbreviata, ad esempio `font`, e `border`.

Inoltre, non esiste alcun `inherit` valore e pertanto l'ereditarietà non è supportata. Non è quindi possibile, ad esempio, impostare `font-size` la proprietà su un layout e prevedere che [`Label`](xref:Xamarin.Forms.Label) tutte le istanze nel layout ereditino il valore. L'unica eccezione è la `direction` proprietà, il cui valore predefinito è `inherit`.

`Span` Gli elementi di destinazione hanno un problema noto che impedisce a spans di essere la destinazione degli stili CSS in base all'elemento e `#` al nome (usando il simbolo). L' `Span` elemento deriva da, `GestureElement`che non ha la proprietà in `StyleClass` modo che gli intervalli non supportino la definizione della classe CSS. Per altre informazioni, vedere [non è possibile applicare lo stile CSS al controllo span](https://github.com/xamarin/Xamarin.Forms/issues/5979).

### <a name="xamarinforms-specific-properties"></a>Proprietà specifiche di Novell. Forms

Sono supportate anche le seguenti proprietà CSS specifiche di Novell. Forms (nella colonna **valori** , i tipi sono in _corsivo_, mentre i `gray`valori letterali stringa sono):

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_colore_ \|`initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_colore_ \|`initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`, `SearchBar`|_INT_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|_colore_ \|`initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|_colore_ \|`initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both`è supportato solo in un `ScrollView`oggetto. |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|\| _testo racchiuso tra virgolette_`initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_colore_ \|`initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|_double_ \| `initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|_colore_ \|`initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|_stringa_ \| di`initial` |`-xf-visual: material;`|

### <a name="xamarinforms-shell-specific-properties"></a>Xamarin.Forms Shell specific properties (Proprietà specifiche della shell Xamarin.Forms)

Sono supportate anche le proprietà CSS specifiche della shell Novell. Forms (nella colonna **valori** , i tipi sono in _corsivo_, mentre i valori `gray`letterali stringa sono):

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_colore_ \|`initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_colore_ \|`initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_colore_ \|`initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_colore_ \|`initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_colore_ \|`initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_colore_ \|`initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_colore_ \|`initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_colore_ \|`initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_colore_ \|`initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_colore_ \|`initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_colore_ \|`initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Colore

Sono supportati `color` i valori seguenti:

- `X11`[colori](https://en.wikipedia.org/wiki/X11_color_names), che corrispondono ai colori CSS, UWP i colori predefiniti e i colori Novell. Forms. Si noti che questi valori di colore non fanno distinzione tra maiuscole e minuscole.
- colori esadecimali `#rgb`: `#argb`, `#rrggbb`,,`#aarrggbb`
- colori RGB: `rgb(255,0,0)`, `rgb(100%,0%,0%)`. I valori sono compresi nell'intervallo 0-255 o 0%-100%.
- colori RGBA: `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. Il valore di opacità è compreso nell'intervallo 0,0-1.0.
- colori HSL: `hsl(120, 100%, 50%)`. Il valore h è compreso nell'intervallo 0-360, mentre s e l sono compresi nell'intervallo 0%-100%.
- colori HSLA: `hsla(120, 100%, 50%, .8)`. Il valore di opacità è compreso nell'intervallo 0,0-1.0.

### <a name="thickness"></a>Thickness

Sono supportati uno, due, tre o `thickness` quattro valori separati da spazi vuoti:

- Un singolo valore indica uno spessore uniforme.
- Due valori indicano lo spessore verticale e orizzontale.
- Tre valori indicano la parte superiore, quindi orizzontale (sinistra e destra), quindi lo spessore inferiore.
- Quattro valori indicano top, then right, then Bottom e then left Thickness.

> [!NOTE]
> I `thickness` valori CSS sono diversi [`Thickness`](xref:Xamarin.Forms.Thickness) da quelli XAML. Ad esempio, in XAML un valore a due `Thickness` valori indica uno spessore orizzontale e verticale, mentre un valore `Thickness` quattro indica Left, quindi top, then right, quindi lo spessore inferiore. Inoltre, i valori `Thickness` XAML sono delimitati da virgole.

### <a name="namedsize"></a>NamedSize

Sono supportati i valori senza `namedsize` distinzione tra maiuscole e minuscole seguenti:

- `default`
- `micro`
- `small`
- `medium`
- `large`

Il significato esatto di ogni `namedsize` valore è dipendente dalla piattaforma e dipendente dalla visualizzazione.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS in Novell. Forms con Novell. University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Novell. Forms 3,0 video CSS**

## <a name="related-links"></a>Collegamenti correlati

- [MonkeyAppCSS (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
