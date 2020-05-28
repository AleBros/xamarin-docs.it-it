---
title: Applicazione di stili Xamarin.Forms con Cascading Style Sheets (CSS)
description: Xamarin.Formssupporta gli elementi visivi di stile utilizzando Cascading Style Sheets (CSS).
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3e333fcdca12a6bd2f71a1ecbeeb1fd3d5f5be13
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140140"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Applicazione di stili Xamarin.Forms con Cascading Style Sheets (CSS)

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Novell. Forms supporta gli elementi visivi di stile utilizzando Cascading Style Sheets (CSS)._

Xamarin.Formsè possibile applicare uno stile alle applicazioni usando CSS. Un foglio di stile è costituito da un elenco di regole, in cui ogni regola è costituita da uno o più selettori e da un blocco di dichiarazione. Un blocco di dichiarazione è costituito da un elenco di dichiarazioni racchiuse tra parentesi graffe, con ogni dichiarazione costituita da una proprietà, da due punti e da un valore. Quando in un blocco sono presenti più dichiarazioni, viene inserito un punto e virgola come separatore. Nell'esempio di codice riportato di seguito viene illustrato un Xamarin.Forms CSS conforme:

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

In Xamarin.Forms i fogli di stile CSS vengono analizzati e valutati in fase di esecuzione, anziché in fase di compilazione e i fogli di stile vengono analizzati di nuovo al momento dell'utilizzo.

> [!NOTE]
> Attualmente, non è possibile eseguire tutte le operazioni di stile con lo stile XAML con CSS. Tuttavia, gli stili XAML possono essere utilizzati per integrare CSS per le proprietà attualmente non supportate da Xamarin.Forms . Per altre informazioni sugli stili XAML, vedere [applicazione di stili Xamarin.Forms con stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

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

Un foglio di stile può essere caricato e analizzato con la [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe prima di essere aggiunto a un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

La [`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) proprietà specifica il foglio di stile come URI relativo alla posizione del file XAML di inclusione o rispetto alla radice del progetto se l'URI inizia con un oggetto `/` .

> [!WARNING]
> Il caricamento del file CSS non verrà eseguito se la relativa azione di compilazione non è impostata su **EmbeddedResource**.

In alternativa, è possibile caricare un foglio di stile e analizzarlo con la [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe, prima di aggiungerlo a un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , inserendolo in una `CDATA` sezione:

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

In C# è possibile caricare un foglio di stile da un oggetto `StringReader` e aggiungerlo a un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

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

Gli elementi nella struttura ad albero visuale possono essere selezionati per tipo con il selettore senza distinzione tra maiuscole e minuscole `element`

```css
stacklayout {
    margin: 20;
}
```

Questo selettore identifica tutti [`StackLayout`](xref:Xamarin.Forms.StackLayout) gli elementi nelle pagine che utilizzano il foglio di stile e imposta i relativi margini su uno spessore uniforme pari a 20.

> [!NOTE]
> Il `element` selettore non identifica le sottoclassi del tipo specificato.

### <a name="selecting-elements-by-base-class"></a>Selezione di elementi per classe di base

Gli elementi nella struttura ad albero visuale possono essere selezionati dalla classe base con il selettore senza distinzione tra maiuscole e minuscole `^base`

```css
^contentpage {
    background-color: lightgray;
}
```

Questo selettore identifica tutti [`ContentPage`](xref:Xamarin.Forms.ContentPage) gli elementi che utilizzano il foglio di stile e imposta il colore di sfondo su `lightgray` .

> [!NOTE]
> Il `^base` selettore è specifico di Xamarin.Forms e non fa parte della specifica CSS.

### <a name="selecting-an-element-by-name"></a>Selezione di un elemento in base al nome

È possibile selezionare singoli elementi nella struttura ad albero visuale con il selettore distinzione maiuscole/minuscole `#id` :

```css
#listView {
    background-color: lightgray;
}
```

Questo selettore identifica l'elemento la cui [`StyleId`](xref:Xamarin.Forms.Element.StyleId) proprietà è impostata su `listView` . Tuttavia, se la `StyleId` proprietà non è impostata, il selettore eseguirà il fallback utilizzando l'oggetto `x:Name` dell'elemento. Nell'esempio XAML seguente, quindi, il `#listView` selettore identificherà il [`ListView`](xref:Xamarin.Forms.ListView) cui `x:Name` attributo è impostato su `listView` e imposta il colore di sfondo su `lightgray` .

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

Gli elementi con un attributo di classe specifico possono essere selezionati con il selettore di maiuscole e minuscole `.class` :

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

Una classe CSS può essere assegnata a un elemento XAML impostando la [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) proprietà dell'elemento sul nome della classe CSS. Nell'esempio XAML seguente, quindi, gli stili definiti dalla `.detailPageTitle` classe vengono assegnati al primo [`Label`](xref:Xamarin.Forms.Label) , mentre gli stili definiti dalla `.detailPageSubtitle` classe sono assegnati al secondo `Label` .

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

È possibile selezionare elementi figlio nella struttura ad albero visuale con il selettore senza distinzione tra maiuscole e minuscole `element element`

```css
listview image {
    height: 60;
    width: 60;
}
```

Questo selettore identifica tutti [`Image`](xref:Xamarin.Forms.Image) gli elementi figlio di [`ListView`](xref:Xamarin.Forms.ListView) elementi e ne imposta l'altezza e la larghezza su 60. Nell'esempio XAML seguente, quindi, il `listview image` selettore identifica l' [`Image`](xref:Xamarin.Forms.Image) elemento figlio di [`ListView`](xref:Xamarin.Forms.ListView) e ne imposta l'altezza e la larghezza su 60.

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

È possibile selezionare elementi figlio diretti nella struttura ad albero visuale con il selettore senza distinzione tra maiuscole e minuscole `element>element` :

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Questo selettore identifica tutti [`Image`](xref:Xamarin.Forms.Image) gli elementi che sono elementi figlio diretti di [`StackLayout`](xref:Xamarin.Forms.StackLayout) elementi e imposta l'altezza e la larghezza su 200. Nell'esempio XAML seguente, quindi, il `stacklayout>image` selettore identificherà l'oggetto [`Image`](xref:Xamarin.Forms.Image) che corrisponde a un figlio diretto di [`StackLayout`](xref:Xamarin.Forms.StackLayout) e ne imposta l'altezza e la larghezza su 200.

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

I selettori CSS seguenti sono supportati da Xamarin.Forms :

|Selettore|Esempio|Descrizione|
|---|---|---|
|`.class`|`.header`|Seleziona tutti gli elementi con la `StyleClass` proprietà che contiene "header". Si noti che questo selettore distingue tra maiuscole e minuscole|
|`#id`|`#email`|Seleziona tutti gli elementi con `StyleId` impostato su `email` . Se `StyleId` non è impostato, effettuare il fallback a `x:Name` . Quando si usa XAML, `x:Name` è preferibile rispetto a `StyleId` . Si noti che questo selettore distingue tra maiuscole e minuscole|
|`*`|`*`|Seleziona tutti gli elementi.|
|`element`|`label`|Seleziona tutti gli elementi di tipo `Label` , ma non le sottoclassi. Si noti che questo selettore non distingue tra maiuscole e minuscole.|
|`^base`|`^contentpage`|Seleziona tutti gli elementi con `ContentPage` come classe di base, incluso `ContentPage` se stesso. Si noti che questo selettore non fa distinzione tra maiuscole e minuscole e non fa parte della specifica CSS.|
|`element,element`|`label,button`|Seleziona tutti `Button` gli elementi e tutti gli `Label` elementi. Si noti che questo selettore non distingue tra maiuscole e minuscole.|
|`element element`|`stacklayout label`|Seleziona tutti `Label` gli elementi all'interno di un oggetto `StackLayout` . Si noti che questo selettore non distingue tra maiuscole e minuscole.|
|`element>element`|`stacklayout>label`|Seleziona tutti `Label` gli elementi con `StackLayout` come elemento padre diretto. Si noti che questo selettore non distingue tra maiuscole e minuscole.|
|`element+element`|`label+entry`|Seleziona tutti `Entry` gli elementi direttamente dopo un oggetto `Label` . Si noti che questo selettore non distingue tra maiuscole e minuscole.|
|`element~element`|`label~entry`|Seleziona tutti `Entry` gli elementi preceduti da un oggetto `Label` . Si noti che questo selettore non distingue tra maiuscole e minuscole.|

Gli stili con selettori corrispondenti vengono applicati consecutivamente nell'ordine delle definizioni. Gli stili definiti per un elemento specifico vengono sempre applicati per ultimi.

> [!TIP]
> I selettori possono essere combinati senza limitazioni, ad esempio `StackLayout>ContentView>label.email` .

I selettori seguenti non sono attualmente supportati:

- `[attribute]`
- `@media` e `@supports`
- `:` e `::`

> [!NOTE]
> La specificità e le sostituzioni di specificità non sono supportate.

## <a name="property-reference"></a>Informazioni di riferimento sulle proprietà

Le proprietà CSS seguenti sono supportate da Xamarin.Forms (nella colonna **valori** i tipi sono in _corsivo_, mentre i valori letterali stringa sono `gray` ):

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_colore_ \|`initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_string_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_colore_ \|`initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_valore Double_ \|`initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_valore Double_ \|`initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_colore_ \|`initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_valore Double_ \|`initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \| `auto` \| `initial`. Inoltre, è possibile specificare una percentuale nell'intervallo compreso tra 0% e 100% e il `%` segno.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_float_ \|`initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_float_ \|`initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_string_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_valore Double_ \| _namedsize_ \|  `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_valore Double_ \|`initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|_valore Double_ \|`initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|_valore Double_ \|`initial` |`line-height: 1.8;`|
|`margin`|`View`|_spessore_ \|`initial` |`margin: 6 12;`|
|`margin-left`|`View`|_spessore_ \|`initial` |`margin-left: 3;`|
|`margin-top`|`View`|_spessore_ \|`initial` |`margin-top: 2;`|
|`margin-right`|`View`|_spessore_ \|`initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_spessore_ \|`initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_INT_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_valore Double_ \|`initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_valore Double_ \|`initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_valore Double_ \|`initial` |`opacity: .3;`|
|`order`|`VisualElement`|_INT_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_spessore_ \|`initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_valore Double_ \|`initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _valore Double_ \|`initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _valore Double_ \|`initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _valore Double_ \|`initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _valore Double_ \|`initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left`e `right` devono essere evitati negli ambienti da destra a sinistra.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _Double_, _Double_ \|`initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_valore Double_ \|`initial`|`min-width: 320;`|

> [!NOTE]
> `initial`è un valore valido per tutte le proprietà. Cancella il valore (Reimposta impostazione predefinita) impostato da un altro stile.

Le proprietà seguenti non sono attualmente supportate:

- `all: initial`.
- Proprietà layout (casella o griglia).
- Proprietà a sintassi abbreviata, ad esempio `font` , e `border` .

Inoltre, non esiste alcun `inherit` valore e pertanto l'ereditarietà non è supportata. Non è quindi possibile, ad esempio, impostare la `font-size` proprietà su un layout e prevedere che tutte le [`Label`](xref:Xamarin.Forms.Label) istanze nel layout ereditino il valore. L'unica eccezione è la `direction` proprietà, il cui valore predefinito è `inherit` .

`Span`Gli elementi di destinazione hanno un problema noto che impedisce a spans di essere la destinazione degli stili CSS in base all'elemento e al nome (usando il `#` simbolo). L' `Span` elemento deriva da `GestureElement` , che non ha la proprietà in `StyleClass` modo che gli intervalli non supportino la definizione della classe CSS. Per altre informazioni, vedere [non è possibile applicare lo stile CSS al controllo span](https://github.com/xamarin/Xamarin.Forms/issues/5979).

### <a name="xamarinforms-specific-properties"></a>Xamarin.FormsProprietà specifiche

Xamarin.FormsSono supportate anche le seguenti proprietà CSS specifiche, nella colonna **valori** , i tipi sono in _corsivo_, mentre i valori letterali stringa sono `gray` :

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_colore_ \|`initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_colore_ \|`initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`, `SearchBar`|_INT_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|_colore_ \|`initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|_colore_ \|`initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both`è supportato solo in un oggetto `ScrollView` . |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|testo racchiuso _tra virgolette_ \|`initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_colore_ \|`initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|_valore Double_ \|`initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|_colore_ \|`initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|_string_ \| `initial` |`-xf-visual: material;`|

### <a name="xamarinforms-shell-specific-properties"></a>Xamarin.FormsProprietà specifiche della shell

Xamarin.FormsSono supportate anche le seguenti proprietà CSS specifiche della shell, nella colonna **valori** , i tipi sono in _corsivo_, mentre i valori letterali stringa sono `gray` :

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

`color`Sono supportati i valori seguenti:

- `X11`[colori](https://en.wikipedia.org/wiki/X11_color_names), che corrispondono ai colori CSS, UWP colori predefiniti e Xamarin.Forms colori. Si noti che questi valori di colore non fanno distinzione tra maiuscole e minuscole.
- colori esadecimali: `#rgb` , `#argb` , `#rrggbb` ,`#aarrggbb`
- colori RGB: `rgb(255,0,0)` , `rgb(100%,0%,0%)` . I valori sono compresi nell'intervallo 0-255 o 0%-100%.
- colori RGBA: `rgba(255, 0, 0, 0.8)` , `rgba(100%, 0%, 0%, 0.8)` . Il valore di opacità è compreso nell'intervallo 0,0-1.0.
- colori HSL: `hsl(120, 100%, 50%)` . Il valore h è compreso nell'intervallo 0-360, mentre s e l sono compresi nell'intervallo 0%-100%.
- colori HSLA: `hsla(120, 100%, 50%, .8)` . Il valore di opacità è compreso nell'intervallo 0,0-1.0.

### <a name="thickness"></a>Thickness

Sono supportati uno, due, tre o quattro `thickness` valori separati da spazi vuoti:

- Un singolo valore indica uno spessore uniforme.
- Due valori indicano lo spessore verticale e orizzontale.
- Tre valori indicano la parte superiore, quindi orizzontale (sinistra e destra), quindi lo spessore inferiore.
- Quattro valori indicano top, then right, then Bottom e then left Thickness.

> [!NOTE]
> `thickness`I valori CSS sono diversi da [`Thickness`](xref:Xamarin.Forms.Thickness) quelli XAML. Ad esempio, in XAML un valore a due valori `Thickness` indica uno spessore orizzontale e verticale, mentre un valore quattro `Thickness` indica Left, quindi top, then right, quindi lo spessore inferiore. Inoltre, `Thickness` i valori XAML sono delimitati da virgole.

### <a name="namedsize"></a>NamedSize

Sono supportati i valori senza distinzione tra maiuscole e minuscole seguenti `namedsize` :

- `default`
- `micro`
- `small`
- `medium`
- `large`

Il significato esatto di ogni `namedsize` valore è dipendente dalla piattaforma e dipendente dalla visualizzazione.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS in Xamarin.Forms con Novell. University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin.Formsvideo di 3,0 CSS**

## <a name="related-links"></a>Collegamenti correlati

- [MonkeyAppCSS (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin.FormsApplicazione di stili alle app usando gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
