---
title: Stile App xamarin. Forms con Cascading Style Sheets (CSS)
description: Xamarin. Forms supporta gli elementi visivi di stile usando Cascading Style Sheets (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: a86d4c6d6737bd6de5e6f115111e072863927fbe
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198836"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Stile App xamarin. Forms con Cascading Style Sheets (CSS)

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Xamarin. Forms supporta gli elementi visivi di stile usando Cascading Style Sheets (CSS)._

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
> Attualmente tutti gli stili che sono possibili eseguire con lo stile di XAML non può essere eseguiti con CSS. Tuttavia, gli stili XAML consente di integrare CSS per le proprietà che non sono attualmente supportate da xamarin. Forms. Per altre informazioni sugli stili XAML, vedere [lo stile delle App xamarin. Forms usando gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

Il [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) illustra l'uso di CSS per applicare uno stile a una semplice app di esempio e viene illustrato negli screenshot seguenti:

[![Pagina principale MonkeyApp con lo stile CSS](css-images/MonkeyAppMainPage.png "MonkeyApp Main pagina con lo stile CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp Main pagina con lo stile CSS")

[![Pagina dei dettagli con lo stile CSS MonkeyApp](css-images/MonkeyAppDetailPage.png "MonkeyApp pagina dei dettagli con lo stile CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "MonkeyApp pagina dei dettagli con lo stile CSS")

## <a name="consuming-a-style-sheet"></a>Utilizzo di un foglio di stile

Il processo per l'aggiunta di un foglio di stile a una soluzione è come segue:

1. Aggiungere un file CSS vuoto al progetto della libreria .NET Standard.
1. Impostare l'azione di compilazione del file CSS **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Il caricamento di un foglio di stile

Esistono diversi approcci che può essere utilizzato per caricare un foglio di stile.

### <a name="xaml"></a>XAML

Un foglio di stile può essere caricato e analizzato con il [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe prima di essere aggiunti a un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

Il [ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) proprietà specifica il foglio di stile come un URI relativo al percorso del file di inclusione XAML o relativo alla radice del progetto se l'URI inizia con un `/`.

> [!WARNING]
> Il file CSS non verrà caricato se l'operazione di compilazione non è impostata su **EmbeddedResource**.

In alternativa, un foglio di stile può essere caricato e analizzato con il [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) (classe), prima di essere aggiunti a un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), per l'incorporamento in un `CDATA` sezione:

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

In C#è possibile caricare un foglio di stile da un `StringReader` oggetto e aggiungerlo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a un oggetto:

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

L'argomento per il `StyleSheet.FromReader` metodo è il `TextReader` che ha letto il foglio di stile.

## <a name="selecting-elements-and-applying-properties"></a>Selezione di elementi e applicando le proprietà

CSS Usa i selettori per determinare quali elementi di destinazione. Stili con corrispondenza selettori vengono applicati in modo consecutivo, nell'ordine di definizione. Gli stili definiti in un elemento specifico vengano sempre applicati per ultima. Per altre informazioni sui selettori supportati, vedere [selettore riferimento](#selector-reference).

CSS Usa le proprietà per definire lo stile di un elemento selezionato. Ogni proprietà dispone di un set di valori possibili e alcune proprietà possono influire sulle qualsiasi tipo di elemento, mentre altre si applicano ai gruppi di elementi. Per altre informazioni sulle proprietà supportate, vedere [riferimento a proprietà](#property-reference).

### <a name="selecting-elements-by-type"></a>Selezione di elementi in base al tipo

Elementi nella struttura visiva possono essere selezionati dal tipo con le maiuscole e minuscole `element` selettore:

```css
stacklayout {
    margin: 20;
}
```

Questo selettore identifica eventuali [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementi nelle pagine che utilizzano il foglio di stile e imposta i margini su uno spessore uniforme pari a 20.

> [!NOTE]
> Il `element` selettore non identifica le sottoclassi del tipo specificato.

### <a name="selecting-elements-by-base-class"></a>Selezione di elementi dalla classe di base

Elementi nella struttura visiva possono essere selezionati da classe di base con le maiuscole e minuscole `^base` selettore:

```css
^contentpage {
    background-color: lightgray;
}
```

Questo selettore identifica eventuali [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) elementi che utilizzano il foglio di stile e imposta lo sfondo di colore per `lightgray`.

> [!NOTE]
> Il `^base` selettore è specifico di xamarin. Forms e non fa parte della specifica di CSS.

### <a name="selecting-an-element-by-name"></a>Selezione di un elemento in base al nome

È possibile selezionare singoli elementi nell'albero visuale con maiuscole / minuscole `#id` selettore:

```css
#listView {
    background-color: lightgray;
}
```

Questo selettore identifica l'elemento il cui [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) è impostata su `listView`. Tuttavia, se il `StyleId` non è impostata, il selettore tornerà all'uso di `x:Name` dell'elemento. Pertanto, nell'esempio XAML seguente, il `#listView` selettore identificherà le [ `ListView` ](xref:Xamarin.Forms.ListView) cui `x:Name` attributo è impostato su `listView`e imposta il colore di sfondo su `lightgray`.

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

Elementi con un attributo di classe specifico possono essere selezionati con la distinzione maiuscole / minuscole `.class` selettore:

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

Una classe CSS può essere assegnata a un elemento XAML impostando il [ `StyleClass` ](xref:Xamarin.Forms.NavigableElement.StyleClass) proprietà dell'elemento per il nome della classe CSS. Pertanto, nell'esempio XAML seguente, gli stili definiti dal `.detailPageTitle` classe vengono assegnati al primo [ `Label` ](xref:Xamarin.Forms.Label), mentre gli stili definiti dal `.detailPageSubtitle` classe vengono assegnati al secondo `Label`.

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

Elementi figlio nella struttura visiva possono essere selezionati con le maiuscole e minuscole `element element` selettore:

```css
listview image {
    height: 60;
    width: 60;
}
```

Questo selettore identifica eventuali [ `Image` ](xref:Xamarin.Forms.Image) elementi figlio del [ `ListView` ](xref:Xamarin.Forms.ListView) elementi e imposta l'altezza e larghezza su 60. Pertanto, nell'esempio XAML seguente, il `listview image` selettore identificherà il [ `Image` ](xref:Xamarin.Forms.Image) che è un elemento figlio il [ `ListView` ](xref:Xamarin.Forms.ListView)e imposta l'altezza e larghezza su 60.

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
> Il `element element` selettore non richiede l'elemento figlio da una _diretta_ elemento figlio dell'elemento padre: l'elemento figlio ha un padre diverso. Selezione avviene a condizione che un predecessore è il primo elemento specificato.

### <a name="selecting-direct-child-elements"></a>Selezione di elementi figlio diretti

Indirizzare gli elementi figlio nella struttura visiva possono essere selezionati con le maiuscole e minuscole `element>element` selettore:

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Questo selettore identifica eventuali [ `Image` ](xref:Xamarin.Forms.Image) gli elementi che sono elementi figlio diretti del [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementi e imposta l'altezza e larghezza su 200. Pertanto, nell'esempio XAML seguente, il `stacklayout>image` selettore identificherà le [ `Image` ](xref:Xamarin.Forms.Image) che è un figlio diretto del [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)e imposta l'altezza e larghezza su 200.

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
> Il `element>element` selettore richiede che l'elemento figlio è una _diretta_ figlio dell'elemento padre.

## <a name="selector-reference"></a>Riferimento del selettore

I selettori CSS seguenti sono supportati da xamarin. Forms:

|Selector|Esempio|Descrizione|
|---|---|---|
|`.class`|`.header`|Seleziona tutti gli elementi di `StyleClass` proprietà che contiene 'header'. Si noti che questo selettore distinzione maiuscole / minuscole.|
|`#id`|`#email`|Seleziona tutti gli elementi `StyleId` impostato su `email`. Se `StyleId` non è impostata, il fallback a `x:Name`. Quando si usa XAML, `x:Name` è preferibile a `StyleId`. Si noti che questo selettore distinzione maiuscole / minuscole.|
|`*`|`*`|Seleziona tutti gli elementi.|
|`element`|`label`|Seleziona tutti gli elementi di `Label`tipo, ma non le sottoclassi. Si noti che questo selettore maiuscole e minuscole.|
|`^base`|`^contentpage`|Seleziona tutti gli elementi `ContentPage` come classe di base, tra cui `ContentPage` stesso. Si noti che questo selettore non distingue tra maiuscole e non fa parte della specifica di CSS.|
|`element,element`|`label,button`|Seleziona tutti gli elementi `Button` elementi e i `Label` elementi. Si noti che questo selettore maiuscole e minuscole.|
|`element element`|`stacklayout label`|Seleziona tutti gli elementi `Label` elementi all'interno di un `StackLayout`. Si noti che questo selettore maiuscole e minuscole.|
|`element>element`|`stacklayout>label`|Seleziona tutti gli elementi `Label` elementi con `StackLayout` come elemento padre diretto. Si noti che questo selettore maiuscole e minuscole.|
|`element+element`|`label+entry`|Seleziona tutti gli elementi `Entry` elementi direttamente dopo un `Label`. Si noti che questo selettore maiuscole e minuscole.|
|`element~element`|`label~entry`|Seleziona tutti gli elementi `Entry` elementi preceduto da un `Label`. Si noti che questo selettore maiuscole e minuscole.|

Stili con corrispondenza selettori vengono applicati in modo consecutivo, nell'ordine di definizione. Gli stili definiti in un elemento specifico vengano sempre applicati per ultima.

> [!TIP]
> I selettori possono essere combinati in via esemplificativa, ad esempio `StackLayout>ContentView>label.email`.

I selettori seguenti non sono attualmente supportati:

- `[attribute]`
- `@media` e `@supports`
- `:` e `::`

> [!NOTE]
> Specificità ed esegue l'override di specificità non sono supportati.

## <a name="property-reference"></a>Riferimento alla proprietà

Le proprietà CSS seguenti sono supportate da xamarin. Forms (nelle **valori** sono tipi di colonna _corsivo_, mentre i valori letterali stringa sono `gray`):

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_Colore_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_Stringa_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_Colore_ \| `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_Valore Double_ \| `initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_Valore Double_ \| `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_Colore_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_Valore Double_ \| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \| `auto` \| `initial`. Inoltre, può essere specificata una percentuale nell'intervallo 0% al 100% con il `%` sign.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_Float_ \| `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_Float_ \| `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_Stringa_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_doppie_ \| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_Valore Double_ \| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`line-height`|`Label`, `Span`|_Valore Double_ \| `initial` |`line-height: 1.8;`|
|`margin`|`View`|_Spessore_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_Spessore_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_Spessore_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_Spessore_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_Spessore_ \| `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_Int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_Valore Double_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_Valore Double_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_Valore Double_ \| `initial` |`opacity: .3;`|
|`order`|`VisualElement`|_Int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_Spessore_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_Valore Double_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _Valore Double_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _Valore Double_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _Valore Double_ \| `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _Valore Double_ \| `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left` e `right` devono essere evitate negli ambienti di destra a sinistra.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _doppie_, _double_ \| `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_Valore Double_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` è un valore valido per tutte le proprietà. Cancella il valore (verrà reimpostato sul valore predefinito) che è stato impostato da un altro stile.

Le proprietà seguenti non sono attualmente supportate:

- `all: initial`.
- Proprietà del layout (casella o griglia).
- Proprietà a sintassi abbreviata, ad esempio `font`, e `border`.

Inoltre, vi è alcun `inherit` ereditarietà valore e pertanto non è supportato. Pertanto non è possibile, ad esempio, impostare il `font-size` proprietà in un layout e attendere che tutti i [ `Label` ](xref:Xamarin.Forms.Label) istanze nel layout per ereditare il valore. L'unica eccezione è il `direction` proprietà, che ha un valore predefinito di `inherit`.

### <a name="xamarinforms-specific-properties"></a>Proprietà specifiche di Novell. Forms

Sono supportate anche le seguenti proprietà CSS specifiche xamarin. Forms (nel **i valori** sono tipi di colonna _corsivo_, mentre i valori letterali stringa sono `gray`):

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|_testo tra virgolette_ \| `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_Colore_ \| `initial` |`-xf-placeholder-color: green;`|
|`-xf-max-length`|`Entry`, `Editor`|_Int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_Colore_ \| `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_Colore_ \| `initial` |`-xf-bar-text-color: gray`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both` è supportato solo in un `ScrollView`. |`-xf-orientation: horizontal;`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-min-track-color`|`Slider`|_Colore_ \| `initial` |`-xf-min-track-color: yellow;`|
|`-xf-max-track-color`|`Slider`|_Colore_ \| `initial` |`-xf-max-track-color: red;`|
|`-xf-thumb-color`|`Slider`|_Colore_ \| `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-spacing`|`StackLayout`|_Valore Double_ \| `initial` |`-xf-spacing: 8;`|

### <a name="xamarinforms-shell-specific-properties"></a>Proprietà specifiche della shell Novell. Forms

Sono supportate anche le proprietà CSS specifiche della shell Novell. Forms (nella colonna **valori** , i tipi sono in _corsivo_, mentre i valori `gray`letterali stringa sono):

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_Colore_ \| `initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_Colore_ \| `initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_Colore_ \| `initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_Colore_ \| `initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_Colore_ \| `initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_Colore_ \| `initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_Colore_ \| `initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_Colore_ \| `initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_Colore_ \| `initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_Colore_ \| `initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_Colore_ \| `initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Colore

Nell'esempio `color` sono supportati i valori:

- `X11` [i colori](https://en.wikipedia.org/wiki/X11_color_names/), che corrisponde al colori CSS, i colori predefiniti UWP e xamarin. Forms colori. Si noti che questi valori di colore sono maiuscole e minuscole.
- HEX colori: `#rgb`, `#argb`, `#rrggbb`, `#aarrggbb`
- i colori RGB: `rgb(255,0,0)`, `rgb(100%,0%,0%)`. I valori sono nell'intervallo 0-255 o 0% al 100%.
- i colori RGBA: `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. Il valore di opacità è compreso nell'intervallo tra 0,0-1,0.
- i colori HSL: `hsl(120, 100%, 50%)`. Il valore h è compreso nell'intervallo 0-360, mentre s e l sono nell'intervallo 0% al 100%.
- i colori HSLA: `hsla(120, 100%, 50%, .8)`. Il valore di opacità è compreso nell'intervallo tra 0,0-1,0.

### <a name="thickness"></a>Thickness

Una, due, tre o quattro `thickness` sono supportati i valori, separati da spazi vuoti:

- Un singolo valore indica uno spessore uniforme.
- Due valori indicano spessore verticale e orizzontale.
- I tre valori indicano dello spessore nella parte inferiore e superiore, quindi orizzontale (a sinistra e a destra).
- Quattro valori indicano superiore, destro, quindi nella parte inferiore e quindi spessore a sinistra.

> [!NOTE]
> CSS `thickness` i valori sono diversi da XAML [ `Thickness` ](xref:Xamarin.Forms.Thickness) valori. Ad esempio, in XAML, un valore di due `Thickness` indica lo spessore orizzontale e verticale, mentre un valore di quattro `Thickness` indica a sinistra, superiore e quindi a destra, quindi bottom spessore. Inoltre, XAML `Thickness` i valori sono delimitati da virgole.

### <a name="namedsize"></a>NamedSize

Maiuscole e minuscole seguenti `namedsize` sono supportati i valori:

- `default`
- `micro`
- `small`
- `medium`
- `large`

Il significato esatto di ogni `namedsize` valore è dipendente dalla piattaforma e dipendenti dalla vista.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS in xamarin. Forms con Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Novell. Forms 3,0 video CSS**

## <a name="related-links"></a>Collegamenti correlati

- [MonkeyAppCSS (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
