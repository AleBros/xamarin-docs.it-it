---
title: Applicazione di stili alle app Xamarin.Forms con Cascading Style Sheets (CSS)
description: Xamarin. Forms supporta gli elementi visivi di stile usando Cascading Style Sheets (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
ms.openlocfilehash: 726ebd55b38460ee966113e4ee487327cd42b03d
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912786"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Stile App xamarin. Forms con Cascading Style Sheets (CSS)

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Novell. Forms supporta gli elementi visivi di stile utilizzando Cascading Style Sheets (CSS)._

Applicazioni xamarin. Forms possono essere personalizzati con stili con CSS. Un foglio di stile è costituito da un elenco di regole, con ogni regola costituito da uno o più selettori e un blocco di dichiarazione. Un blocco di dichiarazione è costituito da un elenco di dichiarazioni di parentesi graffe, con ogni dichiarazione costituito da una proprietà, i due punti e un valore. Quando sono presenti più dichiarazioni in un blocco, viene inserito un punto e virgola come separatore. Esempio di codice seguente mostra alcuni CSS conforme a xamarin. Forms:

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

In xamarin. Forms, fogli di stile CSS vengono analizzati e valutati in fase di esecuzione, anziché come fase di compilazione e i fogli di stile vengono nuovamente analizzati in uso.

> [!NOTE]
> Attualmente tutti gli stili che sono possibili eseguire con lo stile di XAML non può essere eseguiti con CSS. Tuttavia, gli stili XAML consente di integrare CSS per le proprietà che non sono attualmente supportate da xamarin. Forms. Per altre informazioni sugli stili XAML, vedere [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

L'esempio [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) illustra l'uso di CSS per applicare uno stile a un'app semplice ed è illustrato nelle schermate seguenti:

[![Pagina principale di MonkeyApp con stile CSS](css-images/MonkeyAppMainPage.png "Pagina principale di MonkeyApp con stile CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "Pagina principale di MonkeyApp con stile CSS")

[![Pagina dei dettagli di MonkeyApp con stile CSS](css-images/MonkeyAppDetailPage.png "Pagina dei dettagli di MonkeyApp con stile CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "Pagina dei dettagli di MonkeyApp con stile CSS")

## <a name="consuming-a-style-sheet"></a>Utilizzo di un foglio di stile

Il processo per l'aggiunta di un foglio di stile a una soluzione è come segue:

1. Aggiungere un file CSS vuoto al progetto della libreria .NET Standard.
1. Impostare l'azione di compilazione del file CSS su **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Il caricamento di un foglio di stile

Esistono diversi approcci che può essere utilizzato per caricare un foglio di stile.

### <a name="xaml"></a>XAML

Un foglio di stile può essere caricato e analizzato con la classe [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) prima di essere aggiunto a una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

La proprietà [`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) specifica il foglio di stile come URI relativo alla posizione del file XAML di inclusione o rispetto alla radice del progetto se l'URI inizia con una `/`.

> [!WARNING]
> Il caricamento del file CSS non verrà eseguito se la relativa azione di compilazione non è impostata su **EmbeddedResource**.

In alternativa, è possibile caricare un foglio di stile e analizzarlo con la classe [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) prima di aggiungerlo a una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), inserendolo in una sezione `CDATA`:

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

In C#è possibile caricare un foglio di stile da un `StringReader` e aggiungerlo a una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary):

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

L'argomento per il metodo `StyleSheet.FromReader` è il `TextReader` che ha letto il foglio di stile.

## <a name="selecting-elements-and-applying-properties"></a>Selezione di elementi e applicando le proprietà

CSS Usa i selettori per determinare quali elementi di destinazione. Stili con corrispondenza selettori vengono applicati in modo consecutivo, nell'ordine di definizione. Gli stili definiti in un elemento specifico vengano sempre applicati per ultima. Per altre informazioni sui [selettori supportati, vedere riferimento al selettore](#selector-reference).

CSS Usa le proprietà per definire lo stile di un elemento selezionato. Ogni proprietà dispone di un set di valori possibili e alcune proprietà possono influire sulle qualsiasi tipo di elemento, mentre altre si applicano ai gruppi di elementi. Per ulteriori informazioni sulle proprietà supportate, vedere [riferimento alle proprietà](#property-reference).

### <a name="selecting-elements-by-type"></a>Selezione di elementi in base al tipo

Gli elementi nella struttura ad albero visuale possono essere selezionati per tipo con il selettore `element` senza distinzione tra maiuscole e minuscole

```css
stacklayout {
    margin: 20;
}
```

Questo selettore identifica tutti gli elementi [`StackLayout`](xref:Xamarin.Forms.StackLayout) nelle pagine che utilizzano il foglio di stile e imposta i relativi margini su uno spessore uniforme pari a 20.

> [!NOTE]
> Il selettore `element` non identifica le sottoclassi del tipo specificato.

### <a name="selecting-elements-by-base-class"></a>Selezione di elementi dalla classe di base

Gli elementi nella struttura ad albero visuale possono essere selezionati dalla classe base con il selettore `^base` senza distinzione tra maiuscole e minuscole

```css
^contentpage {
    background-color: lightgray;
}
```

Questo selettore identifica gli elementi [`ContentPage`](xref:Xamarin.Forms.ContentPage) che utilizzano il foglio di stile e imposta il colore di sfondo su `lightgray`.

> [!NOTE]
> Il selettore `^base` è specifico di Novell. Forms e non fa parte della specifica CSS.

### <a name="selecting-an-element-by-name"></a>Selezione di un elemento in base al nome

È possibile selezionare singoli elementi nella struttura ad albero visuale con il selettore `#id` distinzione maiuscole/minuscole:

```css
#listView {
    background-color: lightgray;
}
```

Questo selettore identifica l'elemento la cui proprietà [`StyleId`](xref:Xamarin.Forms.Element.StyleId) è impostata su `listView`. Tuttavia, se la proprietà `StyleId` non è impostata, il selettore eseguirà il fallback utilizzando il `x:Name` dell'elemento. Nell'esempio XAML seguente, quindi, il selettore `#listView` identificherà l' [`ListView`](xref:Xamarin.Forms.ListView) il cui attributo `x:Name` è impostato su `listView`e imposta il colore di sfondo su `lightgray`.

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

Gli elementi con un attributo di classe specifico possono essere selezionati con il selettore `.class` distinzione maiuscole/minuscole:

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

Una classe CSS può essere assegnata a un elemento XAML impostando la proprietà [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) dell'elemento sul nome della classe CSS. Nell'esempio XAML seguente, quindi, gli stili definiti dalla classe `.detailPageTitle` vengono assegnati alla prima [`Label`](xref:Xamarin.Forms.Label), mentre gli stili definiti dalla classe `.detailPageSubtitle` vengono assegnati al secondo `Label`.

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

Gli elementi figlio nella struttura ad albero visuale possono essere selezionati con il selettore `element element` senza distinzione tra maiuscole e minuscole

```css
listview image {
    height: 60;
    width: 60;
}
```

Questo selettore identifica gli elementi [`Image`](xref:Xamarin.Forms.Image) figli di [`ListView`](xref:Xamarin.Forms.ListView) elementi e ne imposta l'altezza e la larghezza su 60. Nell'esempio XAML seguente, quindi, il selettore `listview image` identificherà il [`Image`](xref:Xamarin.Forms.Image) che è figlio del [`ListView`](xref:Xamarin.Forms.ListView)e ne imposta l'altezza e la larghezza su 60.

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
> Il selettore `element element` non richiede che l'elemento figlio sia un figlio _diretto_ del padre. l'elemento figlio potrebbe avere un padre diverso. Selezione avviene a condizione che un predecessore è il primo elemento specificato.

### <a name="selecting-direct-child-elements"></a>Selezione di elementi figlio diretti

È possibile selezionare elementi figlio diretti nella struttura ad albero visuale con il selettore `element>element` senza distinzione tra maiuscole e minuscole:

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Questo selettore identifica tutti gli elementi di [`Image`](xref:Xamarin.Forms.Image) che sono figli diretti di [`StackLayout`](xref:Xamarin.Forms.StackLayout) elementi e ne imposta l'altezza e la larghezza su 200. Nell'esempio XAML seguente, quindi, il selettore `stacklayout>image` identificherà il [`Image`](xref:Xamarin.Forms.Image) che è un figlio diretto del [`StackLayout`](xref:Xamarin.Forms.StackLayout)e ne imposta l'altezza e la larghezza su 200.

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
> Il selettore di `element>element` richiede che l'elemento figlio sia un figlio _diretto_ del padre.

## <a name="selector-reference"></a>Riferimento del selettore

I selettori CSS seguenti sono supportati da xamarin. Forms:

|Selettore|Esempio|Descrizione|
|---|---|---|
|`.class`|`.header`|Seleziona tutti gli elementi con la proprietà `StyleClass` contenente ' header '. Si noti che questo selettore distinzione maiuscole / minuscole.|
|`#id`|`#email`|Seleziona tutti gli elementi con `StyleId` impostato su `email`. Se `StyleId` non è impostato, eseguire il fallback su `x:Name`. Quando si usa XAML, è preferibile `x:Name` `StyleId`. Si noti che questo selettore distinzione maiuscole / minuscole.|
|`*`|`*`|Seleziona tutti gli elementi.|
|`element`|`label`|Seleziona tutti gli elementi di tipo `Label`, ma non le sottoclassi. Si noti che questo selettore maiuscole e minuscole.|
|`^base`|`^contentpage`|Seleziona tutti gli elementi con `ContentPage` come classe di base, incluso `ContentPage` stesso. Si noti che questo selettore non distingue tra maiuscole e non fa parte della specifica di CSS.|
|`element,element`|`label,button`|Seleziona tutti gli elementi di `Button` e tutti gli elementi di `Label`. Si noti che questo selettore maiuscole e minuscole.|
|`element element`|`stacklayout label`|Seleziona tutti gli elementi di `Label` all'interno di un `StackLayout`. Si noti che questo selettore maiuscole e minuscole.|
|`element>element`|`stacklayout>label`|Seleziona tutti gli elementi di `Label` con `StackLayout` come elemento padre diretto. Si noti che questo selettore maiuscole e minuscole.|
|`element+element`|`label+entry`|Seleziona tutti gli elementi di `Entry` direttamente dopo una `Label`. Si noti che questo selettore maiuscole e minuscole.|
|`element~element`|`label~entry`|Seleziona tutti gli elementi di `Entry` preceduti da un `Label`. Si noti che questo selettore maiuscole e minuscole.|

Stili con corrispondenza selettori vengono applicati in modo consecutivo, nell'ordine di definizione. Gli stili definiti in un elemento specifico vengano sempre applicati per ultima.

> [!TIP]
> I selettori possono essere combinati senza limitazioni, ad esempio `StackLayout>ContentView>label.email`.

I selettori seguenti non sono attualmente supportati:

- `[attribute]`
- `@media` e `@supports`
- `:` e `::`

> [!NOTE]
> Specificità ed esegue l'override di specificità non sono supportati.

## <a name="property-reference"></a>Riferimento proprietà

Le proprietà CSS seguenti sono supportate da Novell. Forms (nella colonna **values** i tipi sono _Italic_, mentre i valori letterali stringa sono `gray`):

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_colore_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_stringa_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_colore_ \| `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_doppio_ \| `initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_doppio_ \| `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_colore_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_doppio_ \| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \| `auto` \| `initial`. Inoltre, è possibile specificare una percentuale nell'intervallo compreso tra 0% e 100% con il segno di `%`.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_float_ \| `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_float_ \| `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_stringa_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_double_\| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_doppio_ \| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|_doppio_ \| `initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|_doppio_ \| `initial` |`line-height: 1.8;`|
|`margin`|`View`|_spessore_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_spessore_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_spessore_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_spessore_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_spessore_ \| `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_doppio_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_doppio_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_doppio_ \| `initial` |`opacity: .3;`|
|`order`|`VisualElement`|_int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_spessore_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_doppio_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _doppio_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _doppio_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _doppio_ \| `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _doppio_ \| `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left` e `right` devono essere evitati negli ambienti da destra a sinistra.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _Double_, _Double_ \| `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_doppio_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` è un valore valido per tutte le proprietà. Cancella il valore (verrà reimpostato sul valore predefinito) che è stato impostato da un altro stile.

Le proprietà seguenti non sono attualmente supportate:

- `all: initial`.
- Proprietà del layout (casella o griglia).
- Proprietà a sintassi abbreviata, ad esempio `font`e `border`.

Inoltre, non esiste alcun valore `inherit` e pertanto l'ereditarietà non è supportata. Non è quindi possibile, ad esempio, impostare la proprietà `font-size` in un layout e prevedere che tutte le istanze di [`Label`](xref:Xamarin.Forms.Label) nel layout ereditino il valore. L'unica eccezione è rappresentata dalla proprietà `direction`, il cui valore predefinito è `inherit`.

La destinazione di `Span` elementi presenta un problema noto che impedisce la destinazione degli stili CSS in base all'elemento e al nome (usando il simbolo di `#`). L'elemento `Span` deriva da `GestureElement`, che non dispone della proprietà `StyleClass` in modo che gli intervalli non supportino la destinazione della classe CSS. Per altre informazioni, vedere [non è possibile applicare lo stile CSS al controllo span](https://github.com/xamarin/Xamarin.Forms/issues/5979).

### <a name="xamarinforms-specific-properties"></a>Proprietà specifiche di Novell. Forms

Sono supportate anche le seguenti proprietà CSS specifiche di Novell. Forms (nella colonna **values** i tipi sono _Italic_, mentre i valori letterali stringa sono `gray`):

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_colore_ \| `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_colore_ \| `initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`|_int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|_colore_ \| `initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|_colore_ \| `initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both` è supportato solo in un `ScrollView`. |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|_testo racchiuso tra virgolette_ \| `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_colore_ \| `initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|_doppio_ \| `initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|_colore_ \| `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|_stringa_ \| `initial` |`-xf-visual: material;`|

### <a name="xamarinforms-shell-specific-properties"></a>Proprietà specifiche della shell Novell. Forms

Sono supportate anche le proprietà CSS specifiche della shell Novell. Forms (nella colonna **valori** , i tipi sono in _corsivo_, mentre i valori letterali stringa sono `gray`):

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_colore_ \| `initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_colore_ \| `initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_colore_ \| `initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_colore_ \| `initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_colore_ \| `initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_colore_ \| `initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_colore_ \| `initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_colore_ \| `initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_colore_ \| `initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_colore_ \| `initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_colore_ \| `initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Colore

Sono supportati i valori `color` seguenti:

- `X11` [colori](https://en.wikipedia.org/wiki/X11_color_names), che corrispondono ai colori CSS, UWP i colori predefiniti e i colori Novell. Forms. Si noti che questi valori di colore sono maiuscole e minuscole.
- colori esadecimali: `#rgb`, `#argb`, `#rrggbb`, `#aarrggbb`
- colori RGB: `rgb(255,0,0)`, `rgb(100%,0%,0%)`. I valori sono nell'intervallo 0-255 o 0% al 100%.
- colori RGBA: `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. Il valore di opacità è compreso nell'intervallo tra 0,0-1,0.
- colori HSL: `hsl(120, 100%, 50%)`. Il valore h è compreso nell'intervallo 0-360, mentre s e l sono nell'intervallo 0% al 100%.
- colori HSLA: `hsla(120, 100%, 50%, .8)`. Il valore di opacità è compreso nell'intervallo tra 0,0-1,0.

### <a name="thickness"></a>Thickness

Sono supportati uno, due, tre o quattro `thickness` valori separati da spazi vuoti:

- Un singolo valore indica uno spessore uniforme.
- Due valori indicano spessore verticale e orizzontale.
- I tre valori indicano dello spessore nella parte inferiore e superiore, quindi orizzontale (a sinistra e a destra).
- Quattro valori indicano superiore, destro, quindi nella parte inferiore e quindi spessore a sinistra.

> [!NOTE]
> I valori di `thickness` CSS sono diversi dai valori di [`Thickness`](xref:Xamarin.Forms.Thickness) XAML. Ad esempio, in XAML un `Thickness` a due valori indica uno spessore orizzontale e verticale, mentre un `Thickness` a quattro valori indica Left, poi top, then right, quindi lo spessore inferiore. Inoltre, i valori di `Thickness` XAML sono delimitati da virgole.

### <a name="namedsize"></a>NamedSize

Sono supportati i valori `namedsize` senza distinzione tra maiuscole e minuscole seguenti:

- `default`
- `micro`
- `small`
- `medium`
- `large`

Il significato esatto di ogni valore `namedsize` è dipendente dalla piattaforma e dipendente dalla visualizzazione.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS in xamarin. Forms con Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Novell. Forms 3,0 video CSS**

## <a name="related-links"></a>Collegamenti correlati

- [MonkeyAppCSS (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
