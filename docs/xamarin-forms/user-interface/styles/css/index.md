---
title: Applicazione degli stili le app xamarin. Forms usando fogli di stile CSS
description: Xamarin. Forms supporta gli elementi visivi di definizione dello stile utilizzando fogli CSS (Cascading Style).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 811abacff330bf7b6e6240691cb6a15ebbd9d242
ms.sourcegitcommit: daa089d41cfe1ed0456d6de2f8134cf96ae072b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets"></a>Applicazione degli stili le app xamarin. Forms usando fogli di stile CSS

_Xamarin. Forms supporta gli elementi visivi di definizione dello stile utilizzando fogli CSS (Cascading Style)._

Xamarin. Forms 3.0 introduce la possibilità di applicare stili a un'app tramite CSS. Un foglio di stile è costituito da un elenco di regole, con ogni regola composta da uno o più selettori e un blocco di dichiarazione. Un blocco di dichiarazione è costituito da un elenco delle dichiarazioni in parentesi graffe, con ogni dichiarazione composta da una proprietà, i due punti e un valore. Quando sono presenti più dichiarazioni in un blocco, viene inserito un punto e virgola come separatore. Esempio di codice seguente mostra alcuni CSS compatibile con xamarin. Forms:

```css
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

In xamarin. Forms, fogli di stile CSS sono analizzati e valutati in fase di esecuzione, anziché fase di compilazione e fogli di stile vengono nuovamente analizzati in uso.

> [!NOTE]
> Attualmente tutti gli stili che è possibili eseguire con lo stile di XAML non può essere eseguiti con CSS. Tuttavia, gli stili XAML consente di integrare CSS per le proprietà che non sono attualmente supportate dai xamarin. Forms. Per ulteriori informazioni sugli stili XAML, vedere [stile le app xamarin. Forms usando gli stili di XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

Il [MonkeyAppCSS](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/) esempio viene illustrato l'utilizzo di CSS per definire lo stile di una semplice app e viene visualizzato nelle schermate seguenti:

[![Pagina principale MonkeyApp con lo stile CSS](css-images/MonkeyAppMainPage.png "MonkeyApp Main pagina con dello stile CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp Main pagina con dello stile CSS")

[![Pagina dei dettagli MonkeyApp con lo stile CSS](css-images/MonkeyAppDetailPage.png "MonkeyApp pagina dei dettagli con lo stile CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "MonkeyApp pagina dei dettagli con lo stile CSS")

> [!NOTE]
> Non è attualmente possibile definire lo stile il colore di sfondo di un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) mediante un foglio di stile. Pertanto, nell'applicazione di esempio il [ `NavigationPage.BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) viene impostata nel codice.

## <a name="consuming-a-style-sheet"></a>Utilizzo di un foglio di stile

Il processo per l'aggiunta di un foglio di stile a una soluzione è come segue:

1. Aggiungere un file CSS vuoto al progetto di libreria .NET Standard.
1. Impostare l'azione di compilazione del file CSS **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Il caricamento di un foglio di stile

Esistono diversi approcci che può essere usato per caricare un foglio di stile.

### <a name="xaml"></a>XAML

Un foglio di stile può essere caricato e analizzare con il [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe prima di essere aggiunti per il [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) per la pagina:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Il [ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) proprietà specifica il foglio di stile come un URI relativo al percorso del file XAML che lo contiene, o relativo rispetto alla radice del progetto se l'URI inizia con un `/`.

> [!WARNING]
> Il file CSS non verrà caricato se si tratta di azione di compilazione non è impostata su **EmbeddedResource**.

In alternativa, può essere caricato il foglio di stile e analizzare con il [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe per l'incorporamento in un `CDATA` sezione:

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

### <a name="c"></a>C#

Nel linguaggio c#, un foglio di stile può essere caricato come risorsa incorporata e aggiungere la [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) per la pagina:

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        this.Resources.Add(StyleSheet.FromAssemblyResource(
            IntrospectionExtensions.GetTypeInfo(typeof(MyPage)).Assembly,
            "MyProject.Assets.styles.css"));
    }
}
```

Il primo argomento per il `StyleSheet.FromAssemblyResource` metodo è l'assembly contenente il foglio di stile, mentre il secondo argomento è un `string` che rappresenta l'identificatore di risorsa. L'identificatore di risorsa può essere ottenuto dal **proprietà** finestra quando viene selezionato il file CSS.

In alternativa, può essere caricato il foglio di stile da un `StringReader` e aggiungere il [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) per la pagina:

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

L'argomento per il `StyleSheet.FromReader` metodo è il `TextReader` che dispone di leggere il foglio di stile.

## <a name="selecting-elements-and-applying-properties"></a>Selezione di elementi e l'applicazione delle proprietà

CSS utilizza i selettori per determinare quali elementi di destinazione. Stili con corrispondenza selettori vengono applicati in modo consecutivo, in ordine di definizione. Gli stili definiti in un elemento specifico vengano sempre applicati per ultima. Per ulteriori informazioni sui selettori supportati, vedere [selettore riferimento](#selector-reference).

CSS utilizza le proprietà per definire lo stile di un elemento selezionato. Ogni proprietà dispone di un set di valori possibili e alcune proprietà possono influire qualsiasi tipo di elemento, mentre altre si applicano ai gruppi di elementi. Per ulteriori informazioni sulle proprietà supportate, vedere [un riferimento a proprietà](#property-reference).

### <a name="selecting-elements-by-type"></a>Selezione di elementi per tipo

È possibile selezionare elementi nell'albero visuale da tipo con le maiuscole e minuscole `element` selettore:

```css
stacklayout {
    margin: 20;
}
```

Questa selezione identifica qualsiasi [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementi nelle pagine che utilizzano il foglio di stile e imposta i margini su uno spessore uniforme pari a 20.

> [!NOTE]
> Il `element` selettore non identifica le sottoclassi del tipo specificato.

### <a name="selecting-elements-by-base-class"></a>Selezione di elementi dalla classe base

È possibile selezionare elementi in albero elementi visivi dalla classe base con le maiuscole e minuscole `^base` selettore:

```css
^contentpage {
    background-color: lightgray;
}
```

Questa selezione identifica qualsiasi [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) gli elementi che utilizzano il foglio di stile e imposta lo sfondo di colore per `lightgray`.

> [!NOTE]
> Il `^base` selettore è specifico di xamarin. Forms e non fa parte della specifica di CSS.

### <a name="selecting-an-element-by-name"></a>Selezione di un elemento in base al nome

È possibile selezionare singoli elementi nell'albero visuale con la distinzione maiuscole / minuscole `#id` selettore:

```css
#listView {
    background-color: lightgray;
}
```

Questa selezione identifica l'elemento il cui [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) è impostata su `listView`. Tuttavia, se il `StyleId` non è impostata, il selettore eseguirà il fallback utilizzando il `x:Name` dell'elemento. Pertanto, nell'esempio XAML seguente, il `#listView` selettore identificherà il [ `ListView` ](xref:Xamarin.Forms.ListView) cui `x:Name` attributo è impostato su `listView`e imposterà di colore di sfondo su `lightgray`.

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

È possibile selezionare elementi con un attributo di classe specifico con la distinzione maiuscole / minuscole `.class` selettore:

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

Una classe CSS può essere assegnata a un elemento XAML impostando i [ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass) proprietà dell'elemento per il nome della classe CSS. Pertanto, nell'esempio XAML seguente, gli stili definiti il `.detailPageTitle` classe assegnati al primo [ `Label` ](xref:Xamarin.Forms.Label), mentre gli stili definiti mediante il `.detailPageSubtitle` classe assegnati al secondo `Label`.

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

È possibile selezionare gli elementi figlio nell'albero visuale con le maiuscole e minuscole `element element` selettore:

```css
listview image {
    height: 60;
    width: 60;
}
```

Questa selezione identifica qualsiasi [ `Image` ](xref:Xamarin.Forms.Image) gli elementi figlio del [ `ListView` ](xref:Xamarin.Forms.ListView) elementi e imposta l'altezza e larghezza su 60. Pertanto, nell'esempio XAML seguente, il `listview image` selettore identificherà il [ `Image` ](xref:Xamarin.Forms.Image) che è un figlio del [ `ListView` ](xref:Xamarin.Forms.ListView)e imposta l'altezza e larghezza su 60.

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
> Il `element element` selettore non richiede l'elemento figlio di un _diretto_ elemento figlio dell'elemento padre: l'elemento figlio ha un padre diverso. Selezione avviene purché un predecessore è il primo elemento specificato.

### <a name="selecting-direct-child-elements"></a>Selezione di elementi figlio diretti

Indirizzare gli elementi figlio nell'albero visuale selezionabile con le maiuscole e minuscole `element>element` selettore:

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Questa selezione identifica qualsiasi [ `Image` ](xref:Xamarin.Forms.Image) gli elementi che sono elementi figlio diretti del [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementi e imposta l'altezza e larghezza su 200. Pertanto, nell'esempio XAML seguente, il `stacklayout>image` selettore identificherà il [ `Image` ](xref:Xamarin.Forms.Image) che è un figlio diretto della [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)e imposta l'altezza e larghezza su 200.

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
> Il `element>element` selettore richiede che l'elemento figlio è un _diretto_ figlio dell'elemento padre.

## <a name="selector-reference"></a>Riferimento selettore

I selettori CSS seguenti sono supportati da xamarin. Forms:

|Selector|Esempio|Descrizione|
|---|---|---|
|`.class`|`.header`|Seleziona tutti gli elementi con il `StyleClass` proprietà contenente 'intestazione'. Si noti che questa selezione viene fatta distinzione tra maiuscole e minuscole.|
|`#id`|`#email`|Seleziona tutti gli elementi `StyleId` impostato su `email`. Se `StyleId` non è impostata, il fallback a `x:Name`. Quando si usa XAML `x:Name` è preferibile `StyleId`. Si noti che questa selezione viene fatta distinzione tra maiuscole e minuscole.|
|`*`|`*`|Seleziona tutti gli elementi.|
|`element`|`label`|Seleziona tutti gli elementi di tipo `Label`, ma non delle classi Sub. Si noti che questa selezione viene fatta distinzione tra maiuscole e minuscole.|
|`^base`|`^contentpage`|Seleziona tutti gli elementi `ContentPage` come classe base, tra cui `ContentPage` se stesso. Si noti che questa selezione viene fatta distinzione tra maiuscole e minuscole e non fa parte della specifica di CSS.|
|`element,element`|`label,button`|Seleziona tutti gli elementi `Button` elementi e i `Label` elementi. Si noti che questa selezione viene fatta distinzione tra maiuscole e minuscole.|
|`element element`|`stacklayout label`|Seleziona tutti gli elementi `Label` gli elementi all'interno di un `StackLayout`. Si noti che questa selezione viene fatta distinzione tra maiuscole e minuscole.|
|`element>element`|`stacklayout>label`|Seleziona tutti gli elementi `Label` gli elementi con `StackLayout` come elemento padre diretto. Si noti che questa selezione viene fatta distinzione tra maiuscole e minuscole.|
|`element+element`|`label+entry`|Seleziona tutti gli elementi `Entry` elementi direttamente dopo un `Label`. Si noti che questa selezione viene fatta distinzione tra maiuscole e minuscole.|
|`element~element`|`label~entry`|Seleziona tutti gli elementi `Entry` elementi preceduto da un `Label`. Si noti che questa selezione viene fatta distinzione tra maiuscole e minuscole.|

Stili con corrispondenza selettori vengono applicati in modo consecutivo, in ordine di definizione. Gli stili definiti in un elemento specifico vengano sempre applicati per ultima.

> [!TIP]
> I selettori possono essere combinati in via esemplificativa, ad esempio `StackLayout>ContentView>label.email`.

I selettori seguenti non sono attualmente supportati:

- `[attribute]`
- `@media` e `@supports`
- `:` e `::`

> [!NOTE]
> Specificità ed esegue l'override di specificità non sono supportati.

## <a name="property-reference"></a>Riferimento a proprietà

Le proprietà CSS seguenti sono supportate da xamarin. Forms (nelle **valori** colonna, i tipi sono _corsivo_, mentre i valori letterali stringa sono `gray`):

|Proprietà|Si applica a|Valori|Esempio|
|---|---|---|---|
|`background-color`|`VisualElement`|_Colore_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_Stringa_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`|_Colore_ \| `initial`|`border-color: #9acd32;`|
|`border-width`|`Button`|_Doppia_ \| `initial` |`border-width: .5;`|
|`color`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`|_Colore_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_Stringa_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_Double_ \| _namedsize_  \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_Doppia_ \| `initial` |`min-height: 250;`|
|`margin`|`View`|_Spessore_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_Spessore_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_Spessore_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_Spessore_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_Spessore_ \| `initial` |`margin-bottom: 6;`|
|`min-height`|`VisualElement`|_Doppia_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_Doppia_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_Doppia_ \| `initial` |`opacity: .3;`|
|`padding`|`Layout`, `Page`|_Spessore_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Layout`, `Page`|_Doppia_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Layout`, `Page`| _Doppia_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Layout`, `Page`| _Doppia_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Layout`, `Page`| _Doppia_ \| `initial` |`padding-bottom: 6;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `right` \| `center` \| `start` \| `end` \| `initial`. `left` e `right` devono essere evitati negli ambienti da destra a sinistra.| `text-align: right;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial `|`visibility: hidden;`|
|`width`|`VisualElement`|_Doppia_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` è un valore valido per tutte le proprietà. Cancella il valore (viene reimpostato sul valore predefinito) che è stato impostato da un altro stile.

Le proprietà seguenti non sono attualmente supportate:

- `all: initial`.
- Proprietà del layout (casella o griglia).
- Proprietà a sintassi abbreviata, ad esempio `font`, e `border`.

Inoltre, è presente alcun `inherit` ereditarietà valore e pertanto non è supportato. Pertanto non è possibile, ad esempio, impostare il `font-size` proprietà in un layout e prevede che tutti i [ `Label` ](xref:Xamarin.Forms.Label) istanze nel layout di ereditare il valore. L'unica eccezione è il `direction` proprietà, che ha un valore predefinito di `inherit`.

### <a name="color"></a>Colore

Nell'esempio `color` valori supportati:

- `X11` [colori](https://en.wikipedia.org/wiki/X11_color_names/), che corrisponde a colori CSS, i colori predefiniti UWP e i colori di xamarin. Forms. Si noti che questi valori di colore sono distinzione tra maiuscole e minuscole.
- HEX colori: `#rgb`, `#argb`, `#rrggbb`, `#aarrggbb`
- i colori RGB: `rgb(255,0,0)`, `rgb(100%,0%,0%)`. I valori sono nell'intervallo tra 0 e 255 o 0-100%.
- colori RGBA: `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. Il valore di opacità è compreso nell'intervallo 0,0-1,0.
- colori HSL: `hsl(120, 100%, 50%)`. Il valore h è compreso nell'intervallo 0-360, mentre s e l sono nell'intervallo 0% - 100%.
- colori HSLA: `hsla(120, 100%, 50%, .8)`. Il valore di opacità è compreso nell'intervallo 0,0-1,0.

### <a name="thickness"></a>Thickness

Uno, due, tre o quattro `thickness` sono supportati i valori, separati da spazi vuoti:

- Un singolo valore indica uno spessore uniforme.
- Due valori indicano spessore verticale quindi orizzontale.
- I tre valori indicano spessore inferiore e superiore, quindi orizzontale (sinistro e destro).
- Quattro valori indicano superiore, destro, inferiore e quindi spessore a sinistra.

> [!NOTE]
> CSS `thickness` valori diversi da XAML [ `Thickness` ](/api/type/Xamarin.Forms.Thickness/) valori. Ad esempio, in XAML, un valore di due `Thickness` indica lo spessore orizzontale quindi verticale, mentre un valore di quattro `Thickness` indica sinistro, superiore e quindi a destra, quindi basso spessore. Inoltre, XAML `Thickness` i valori sono delimitati da virgole.

### <a name="namedsize"></a>NamedSize

Distinzione tra maiuscole e minuscole seguenti `namedsize` valori supportati:

- `default`
- `micro`
- `small`
- `medium`
- `large`

Il significato esatto di ogni `namedsize` valore è dipendente dalla piattaforma e dipendenti dalla vista.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS in xamarin. Forms con Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin. Forms 3.0 CSS, da [University Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Collegamenti correlati

- [MonkeyAppCSS (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)
- [App xamarin. Forms usando sintassi XAML per gli stili di stile](~/xamarin-forms/user-interface/styles/xaml/index.md)
