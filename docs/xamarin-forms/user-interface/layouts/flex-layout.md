---
title: Xamarin.FormsFlexLayout
description: Usare FlexLayout per lo stack o il wrapping di una raccolta di visualizzazioni figlio.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 518bf97be3dd9d906d574b15434b0bfa67612516
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84570687"
---
# <a name="the-xamarinforms-flexlayout"></a>Xamarin.FormsFlexLayout

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_Usare FlexLayout per lo stack o il wrapping di una raccolta di visualizzazioni figlio._

Xamarin.Forms [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) È una novità della Xamarin.Forms versione 3,0. Si basa sul [modulo CSS flexible box layout](https://www.w3.org/TR/css-flexbox-1/), comunemente noto come _Flex layout_ o _Flex-box_, quindi chiamato perché include molte opzioni flessibili per disporre gli elementi figlio all'interno del layout.

`FlexLayout`è simile a Xamarin.Forms [`StackLayout`](~/xamarin-forms/user-interface/layouts/stacklayout.md) in quanto può disporre gli elementi figlio orizzontalmente e verticalmente in uno stack. Tuttavia, `FlexLayout` è anche in grado di eseguire il wrapping dei relativi elementi figlio se sono presenti troppi per adattarsi a una singola riga o colonna, oltre a numerose opzioni per l'orientamento, l'allineamento e l'adattamento a diverse dimensioni dello schermo.

`FlexLayout`deriva da [`Layout<View>`](xref:Xamarin.Forms.Layout`1) ed eredita una [`Children`](xref:Xamarin.Forms.Layout`1.Children) proprietà di tipo `IList<View>` .

`FlexLayout`definisce sei proprietà associabili pubbliche e cinque proprietà associabili associate che influiscono sulle dimensioni, l'orientamento e l'allineamento degli elementi figlio. Se non si ha familiarità con le proprietà associabili associate, vedere l'articolo **[proprietà associate](~/xamarin-forms/xaml/attached-properties.md)**. Queste proprietà sono descritte in dettaglio nelle sezioni seguenti sulle **[proprietà associabili in dettaglio](#the-bindable-properties-in-detail)** e sulle **[proprietà associabili associate in modo dettagliato](#the-attached-bindable-properties-in-detail)**. Tuttavia, questo articolo inizia con una sezione di alcuni **[scenari di utilizzo comuni](#common-usage-scenarios)** di `FlexLayout` che descrive molte di queste proprietà in modo più informale. Alla fine dell'articolo, si vedrà come combinare i `FlexLayout` [fogli di stile CSS](~/xamarin-forms/user-interface/styles/css/index.md).

## <a name="common-usage-scenarios"></a>Scenari di utilizzo comuni

Il programma di esempio **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** contiene diverse pagine che illustrano alcuni usi comuni di `FlexLayout` e consente di sperimentare le relative proprietà.

### <a name="using-flexlayout-for-a-simple-stack"></a>Uso di FlexLayout per un semplice stack

Nella pagina **stack semplice** viene illustrato come `FlexLayout` sostituire un oggetto, `StackLayout` ma con markup più semplice. Tutto questo esempio è definito nella pagina XAML. `FlexLayout`Contiene quattro elementi figlio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.SimpleStackPage"
             Title="Simple Stack">

    <FlexLayout Direction="Column"
                AlignItems="Center"
                JustifyContent="SpaceEvenly">

        <Label Text="FlexLayout in Action"
               FontSize="Large" />

        <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />

        <Button Text="Do-Nothing Button" />

        <Label Text="Another Label" />
    </FlexLayout>
</ContentPage>
```

Di seguito è illustrata la pagina in esecuzione in iOS, Android e i piattaforma UWP (Universal Windows Platform):

[![Pagina semplice dello stack](flex-layout-images/SimpleStack.png "Pagina semplice dello stack")](flex-layout-images/SimpleStack-Large.png#lightbox)

Tre proprietà di `FlexLayout` vengono visualizzate nel file **SimpleStackPage. XAML** :

- La [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) proprietà è impostata su un valore dell' [`FlexDirection`](xref:Xamarin.Forms.FlexDirection) enumerazione. Il valore predefinito è `Row`. L'impostazione della proprietà su `Column` fa sì che gli elementi figlio dell'oggetto `FlexLayout` vengano disposti in una singola colonna di elementi.

    Quando gli elementi in un oggetto `FlexLayout` sono disposti in una colonna, `FlexLayout` viene definito un _asse principale_ verticale e un _asse incrociato_orizzontale.

- La [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) proprietà è di tipo [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) e specifica il modo in cui gli elementi sono allineati sull'asse incrociato. L' `Center` opzione fa sì che ogni elemento sia centrato orizzontalmente.

    Se si utilizza un `StackLayout` anziché un oggetto `FlexLayout` per questa attività, è necessario incentrare tutti gli elementi assegnando la `HorizontalOptions` proprietà di ogni elemento a `Center` . La `HorizontalOptions` proprietà non funziona per gli elementi figlio di un oggetto `FlexLayout` , ma la singola `AlignItems` proprietà esegue lo stesso obiettivo. Se necessario, è possibile usare la `AlignSelf` proprietà associabile associata per eseguire l'override della `AlignItems` proprietà per i singoli elementi:

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    Con questa modifica, questa `Label` viene posizionata sul bordo sinistro di `FlexLayout` quando l'ordine di lettura è da sinistra a destra.

- La [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) proprietà è di tipo [`FlexJustify`](xref:Xamarin.Forms.FlexJustify) e specifica il modo in cui gli elementi vengono disposti sull'asse principale. L' `SpaceEvenly` opzione consente di allocare lo stesso spazio verticale rimanente tra tutti gli elementi e sopra il primo elemento e sotto l'ultimo elemento.

    Se si utilizza un oggetto `StackLayout` , è necessario assegnare la `VerticalOptions` proprietà di ogni elemento a `CenterAndExpand` per ottenere un effetto simile. Tuttavia `CenterAndExpand` , l'opzione consente di allocare due volte lo spazio tra ogni elemento e prima del primo elemento e dopo l'ultimo elemento. È possibile simulare l' `CenterAndExpand` opzione di impostando `VerticalOptions` la `JustifyContent` proprietà di `FlexLayout` su `SpaceAround` .

Queste `FlexLayout` proprietà vengono descritte più dettagliatamente nella sezione **[delle proprietà associabili in dettaglio di](#the-bindable-properties-in-detail)** seguito.

### <a name="using-flexlayout-for-wrapping-items"></a>Uso di FlexLayout per il wrapping di elementi

Nella pagina di **wrapping delle foto** dell'esempio **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** viene illustrato come `FlexLayout` è possibile eseguire il wrapping dei figli a righe o colonne aggiuntive. Il file XAML crea un'istanza `FlexLayout` di e assegna due proprietà:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.PhotoWrappingPage"
             Title="Photo Wrapping">
    <Grid>
        <ScrollView>
            <FlexLayout x:Name="flexLayout"
                        Wrap="Wrap"
                        JustifyContent="SpaceAround" />
        </ScrollView>

        <ActivityIndicator x:Name="activityIndicator"
                           IsRunning="True"
                           VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

La `Direction` proprietà di questo oggetto `FlexLayout` non è impostata, quindi presenta l'impostazione predefinita `Row` , ovvero gli elementi figlio sono disposti in righe e l'asse principale è orizzontale.

La [`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap) proprietà è di un tipo di enumerazione [`FlexWrap`](xref:Xamarin.Forms.FlexWrap) . Se è presente un numero eccessivo di elementi da inserire in una riga, questa impostazione della proprietà determina il wrapping degli elementi nella riga successiva.

Si noti che `FlexLayout` è un elemento figlio di un oggetto `ScrollView` . Se sono presenti troppe righe da adattare alla pagina, l'oggetto `ScrollView` ha una proprietà predefinita `Orientation` `Vertical` e consente lo scorrimento verticale.

La `JustifyContent` Proprietà alloca spazio residuo sull'asse principale (asse orizzontale) in modo che ogni elemento sia racchiuso dalla stessa quantità di spazio vuoto.

Il file code-behind accede a una raccolta di foto di esempio e le aggiunge alla `Children` raccolta di `FlexLayout` :

```csharp
public partial class PhotoWrappingPage : ContentPage
{
    // Class for deserializing JSON list of sample bitmaps
    [DataContract]
    class ImageList
    {
        [DataMember(Name = "photos")]
        public List<string> Photos = null;
    }

    public PhotoWrappingPage ()
    {
        InitializeComponent ();

        LoadBitmapCollection();
    }

    async void LoadBitmapCollection()
    {
        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json");
                byte[] data = await webClient.DownloadDataTaskAsync(uri);

                // Convert to a Stream object
                using (Stream stream = new MemoryStream(data))
                {
                    // Deserialize the JSON into an ImageList object
                    var jsonSerializer = new DataContractJsonSerializer(typeof(ImageList));
                    ImageList imageList = (ImageList)jsonSerializer.ReadObject(stream);

                    // Create an Image object for each bitmap
                    foreach (string filepath in imageList.Photos)
                    {
                        Image image = new Image
                        {
                            Source = ImageSource.FromUri(new Uri(filepath))
                        };
                        flexLayout.Children.Add(image);
                    }
                }
            }
            catch
            {
                flexLayout.Children.Add(new Label
                {
                    Text = "Cannot access list of bitmap files"
                });
            }
        }

        activityIndicator.IsRunning = false;
        activityIndicator.IsVisible = false;
    }
}
```

Ecco il programma in esecuzione, a scorrimento progressivo dall'alto verso il basso:

[![Pagina di wrapping della foto](flex-layout-images/PhotoWrapping.png "Pagina di wrapping della foto")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>Layout di pagina con FlexLayout

È presente un layout standard in progettazione Web denominato [_Santo Graal_](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) perché è un formato di layout molto auspicabile, ma spesso difficile da realizzare con la perfezione. Il layout è costituito da un'intestazione nella parte superiore della pagina e da un piè di pagina nella parte inferiore della pagina. Occupare il centro della pagina è il contenuto principale, ma spesso con un menu a colonne a sinistra del contenuto e informazioni supplementari (talvolta denominate area _da parte_ ) a destra. [La sezione 5.4.1 della specifica CSS per il layout della casella flessibile](https://www.w3.org/TR/css-flexbox-1/#order-accessibility) descrive il modo in cui il layout del Santo Graal può essere realizzato con una casella Flex.

La pagina del **layout del Santo Graal** dell'esempio **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** illustra una semplice implementazione di questo layout usando una `FlexLayout` nidificata in un'altra. Poiché questa pagina è progettata per un telefono in modalità verticale, le aree a sinistra e a destra dell'area di contenuto sono solo di 50 pixel di larghezza:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.HolyGrailLayoutPage"
             Title="Holy Grail Layout">

    <FlexLayout Direction="Column">

        <!-- Header -->
        <Label Text="HEADER"
               FontSize="Large"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center" />

        <!-- Body -->
        <FlexLayout FlexLayout.Grow="1">

            <!-- Content -->
            <Label Text="CONTENT"
                   FontSize="Large"
                   BackgroundColor="Gray"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center"
                   FlexLayout.Grow="1" />

            <!-- Navigation items-->
            <BoxView FlexLayout.Basis="50"
                     FlexLayout.Order="-1"
                     Color="Blue" />

            <!-- Aside items -->
            <BoxView FlexLayout.Basis="50"
                     Color="Green" />

        </FlexLayout>

        <!-- Footer -->
        <Label Text="FOOTER"
               FontSize="Large"
               BackgroundColor="Pink"
               HorizontalTextAlignment="Center" />
    </FlexLayout>
</ContentPage>
```

In esecuzione:

[![Pagina di layout del Santo Graal](flex-layout-images/HolyGrailLayout.png "Pagina di layout del Santo Graal")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

Viene eseguito il rendering delle aree di navigazione e di riserva con un oggetto `BoxView` a sinistra e a destra.

Il primo `FlexLayout` nel file XAML dispone di un asse principale verticale e contiene tre elementi figlio disposti in una colonna. Si tratta dell'intestazione, del corpo della pagina e del piè di pagina. L'oggetto annidato `FlexLayout` ha un asse principale orizzontale con tre elementi figlio disposti in una riga.

In questo programma sono illustrate tre proprietà associabili associate:

- La `Order` proprietà associabile associata viene impostata sul primo oggetto `BoxView` . Questa proprietà è di tipo integer e il valore predefinito è 0. È possibile utilizzare questa proprietà per modificare l'ordine di layout. In genere, gli sviluppatori preferiscono che il contenuto della pagina venga visualizzato nel markup prima degli elementi di navigazione e degli elementi da parte. Se si imposta la `Order` proprietà sulla prima proprietà su `BoxView` un valore minore di quello degli altri elementi di pari livello, questa verrà visualizzata come primo elemento nella riga. Analogamente, è possibile verificare che un elemento venga visualizzato per ultimo impostando la `Order` proprietà su un valore maggiore di quello dei relativi elementi di pari livello.

- La `Basis` proprietà associabile associata è impostata sui due `BoxView` elementi per fornire una larghezza di 50 pixel. Questa proprietà è di tipo `FlexBasis` , una struttura che definisce una proprietà statica di tipo `FlexBasis` denominato `Auto` , che è l'impostazione predefinita. È possibile usare `Basis` per specificare una dimensione in pixel o una percentuale che indica la quantità di spazio occupata dall'elemento sull'asse principale. Viene chiamato _base_ perché specifica le dimensioni di un elemento che è la base di tutto il layout successivo.

- La `Grow` proprietà viene impostata nell'oggetto annidato `Layout` e nell'oggetto `Label` figlio che rappresenta il contenuto. Questa proprietà è di tipo `float` e il valore predefinito è 0. Quando è impostato su un valore positivo, tutto lo spazio rimanente lungo l'asse principale viene allocato a tale elemento e a elementi di pari livello con i valori positivi di `Grow` . Lo spazio viene allocato proporzionalmente ai valori, in modo analogo alla specifica Star in un oggetto `Grid` .

    La prima `Grow` proprietà associata viene impostata sull'oggetto annidato `FlexLayout` , a indicare che questa operazione deve `FlexLayout` occupare tutto lo spazio verticale inutilizzato all'interno dell'oggetto esterno `FlexLayout` . La seconda `Grow` proprietà associata è impostata sull'oggetto `Label` che rappresenta il contenuto, a indicare che questo contenuto deve occupare tutto lo spazio orizzontale inutilizzato all'interno dell'oggetto interno `FlexLayout` .

    È inoltre disponibile una `Shrink` proprietà associabile simile che è possibile utilizzare quando le dimensioni degli elementi figlio superano le dimensioni dell'oggetto `FlexLayout` , ma non si desidera eseguire il wrapping.

### <a name="catalog-items-with-flexlayout"></a>Elementi del catalogo con FlexLayout

La pagina degli **elementi del catalogo** nell'esempio **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** è simile a quella dell' [esempio 1 nella sezione 1,1 della specifica CSS Flex layout box](https://www.w3.org//TR/css-flexbox-1/#overview) , ad eccezione del fatto che visualizza una serie di immagini scorrevoli orizzontalmente e le descrizioni di tre scimmie:

[![Pagina elementi del catalogo](flex-layout-images/CatalogItems.png "Pagina elementi del catalogo")](flex-layout-images/CatalogItems-Large.png#lightbox)

Ognuna delle tre scimmie è un oggetto `FlexLayout` contenuto in un oggetto a `Frame` cui sono assegnati un'altezza e una larghezza esplicita e che è anche un elemento figlio di un valore più grande `FlexLayout` . In questo file XAML, la maggior parte delle proprietà degli `FlexLayout` elementi figlio è specificata negli stili, tutti tranne uno di questi è uno stile implicito:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CatalogItemsPage"
             Title="Catalog Items">
    <ContentPage.Resources>
        <Style TargetType="Frame">
            <Setter Property="BackgroundColor" Value="LightYellow" />
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="Margin" Value="10" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="Margin" Value="0, 4" />
        </Style>

        <Style x:Key="headerLabel" TargetType="Label">
            <Setter Property="Margin" Value="0, 8" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="Blue" />
        </Style>

        <Style TargetType="Image">
            <Setter Property="FlexLayout.Order" Value="-1" />
            <Setter Property="FlexLayout.AlignSelf" Value="Center" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="White" />
            <Setter Property="BackgroundColor" Value="Green" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}"
                           WidthRequest="240"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Lo stile implicito per `Image` include le impostazioni di due proprietà associabili associate `Flexlayout` :

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

Con l' `Order` impostazione &ndash; 1, l' `Image` elemento viene visualizzato per primo in ognuna delle visualizzazioni annidate `FlexLayout` indipendentemente dalla relativa posizione all'interno della raccolta Children. La `AlignSelf` proprietà di `Center` fa `Image` in modo che l'oggetto sia centrato all'interno di `FlexLayout` . In questo modo viene eseguito l'override dell'impostazione della `AlignItems` proprietà, che ha un valore predefinito `Stretch` , che indica che gli `Label` `Button` elementi figlio e vengono estesi alla larghezza completa di `FlexLayout` .

All'interno di ognuna delle tre `FlexLayout` Visualizzazioni, uno spazio vuoto `Label` precede `Button` , ma è `Grow` impostato su 1. Ciò significa che tutto lo spazio verticale aggiuntivo viene allocato a questo campo vuoto `Label` , il che esegue il push `Button` in basso.

## <a name="the-bindable-properties-in-detail"></a>Informazioni dettagliate sulle proprietà associabili

Ora che sono state visualizzate alcune applicazioni comuni di `FlexLayout` , le proprietà di `FlexLayout` possono essere esplorate in modo più dettagliato.
`FlexLayout`definisce sei proprietà associabili impostate su `FlexLayout` se stesso, nel codice o in XAML, per controllare l'orientamento e l'allineamento. Una di queste proprietà, [`Position`](xref:Xamarin.Forms.FlexLayout.Position) , non è trattata in questo articolo.

È possibile sperimentare le cinque proprietà associabili rimanenti usando la pagina **esperimento** dell'esempio **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** . Questa pagina consente di aggiungere o rimuovere elementi figlio da un oggetto `FlexLayout` e di impostare le combinazioni delle cinque proprietà associabili. Tutti gli elementi figlio di `FlexLayout` sono `Label` viste di diversi colori e dimensioni, con la `Text` proprietà impostata su un numero corrispondente alla relativa posizione nella `Children` raccolta.

All'avvio del programma, cinque `Picker` visualizzazioni visualizzano i valori predefiniti di queste cinque `FlexLayout` Proprietà. Nella parte `FlexLayout` inferiore della schermata sono contenuti tre elementi figlio:

[![Pagina dell'esperimento: predefinita](flex-layout-images/ExperimentDefault.png "Pagina dell'esperimento-impostazione predefinita")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Ogni `Label` vista ha uno sfondo grigio che mostra lo spazio allocato all'interno di `Label` `FlexLayout` . Lo sfondo di `FlexLayout` è Alice blu. Occupa l'intera area inferiore della pagina eccetto un piccolo margine a sinistra e a destra.

### <a name="the-direction-property"></a>Proprietà Direction

La [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) proprietà è di tipo [`FlexDirection`](xref:Xamarin.Forms.FlexDirection) , un'enumerazione con quattro membri:

- `Column`
- `ColumnReverse`(o "column-reverse" in XAML)
- `Row`, il valore predefinito
- `RowReverse`(o "row-reverse" in XAML)

In XAML è possibile specificare il valore di questa proprietà usando i nomi dei membri di enumerazione in lettere minuscole, maiuscole o miste oppure è possibile usare due stringhe aggiuntive visualizzate tra parentesi che corrispondono agli indicatori CSS. (Le stringhe "column-reverse" e "row-reverse" sono definite nella [`FlexDirectionTypeConverter`](xref:Xamarin.Forms.FlexDirectionTypeConverter) classe utilizzata dal parser XAML).

Di seguito è illustrata la pagina dell' **esperimento** , da sinistra a destra, direzione, direzione `Row` e direzione `Column` `ColumnReverse` :

[![Pagina dell'esperimento: direzione](flex-layout-images/ExperimentDirection.png "Direzione della pagina dell'esperimento")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Si noti che per le `Reverse` Opzioni, gli elementi iniziano a destra o in basso.

### <a name="the-wrap-property"></a>Proprietà Wrap

La [`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap) proprietà è di tipo [`FlexWrap`](xref:Xamarin.Forms.FlexWrap) , un'enumerazione con tre membri:

- `NoWrap`, il valore predefinito
- `Wrap`
- `Reverse`(o "ritorno a capo" in XAML)

Da sinistra a destra, queste schermate mostrano `NoWrap` le `Wrap` Opzioni, e `Reverse` per 12 elementi figlio:

[![Pagina dell'esperimento: a capo](flex-layout-images/ExperimentWrap.png "Wrapping della pagina dell'esperimento")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Quando la `Wrap` proprietà è impostata su `NoWrap` e l'asse principale è vincolato (come nel programma) e l'asse principale non è ampio o sufficientemente alto da adattarsi a tutti gli elementi figlio, `FlexLayout` tenta di rendere più piccoli gli elementi, come illustrato nella schermata iOS. È possibile controllare la compattazione degli elementi con la [`Shrink`](#the-shrink-property) proprietà associabile associata.

### <a name="the-justifycontent-property"></a>Proprietà JustifyContent

La [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) proprietà è di tipo [`FlexJustify`](xref:Xamarin.Forms.FlexJustify) , un'enumerazione con sei membri:

- `Start`(o "Flex-Start" in XAML), il valore predefinito
- `Center`
- `End`(o "Flex-end" in XAML)
- `SpaceBetween`(o "spazio tra" in XAML)
- `SpaceAround`(o "space-around" in XAML)
- `SpaceEvenly`

Questa proprietà specifica il modo in cui gli elementi sono spaziati sull'asse principale, ovvero l'asse orizzontale in questo esempio:

[![Pagina dell'esperimento: giustificare il contenuto](flex-layout-images/ExperimentJustifyContent.png "Pagina dell'esperimento-giustificare il contenuto")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

In tutte e tre le schermate la `Wrap` proprietà è impostata su `Wrap` . Il `Start` valore predefinito è illustrato nella schermata precedente di Android. La schermata iOS Mostra l' `Center` opzione: tutti gli elementi vengono spostati al centro. Le altre tre opzioni che iniziano con la parola `Space` allocano lo spazio aggiuntivo non occupato dagli elementi. `SpaceBetween`alloca lo spazio equamente tra gli elementi. `SpaceAround`inserisce lo spazio uguale intorno a ogni elemento, mentre `SpaceEvenly` inserisce lo spazio uguale tra ogni elemento e prima del primo elemento e dopo l'ultimo elemento nella riga.

### <a name="the-alignitems-property"></a>Proprietà AlignItems

La [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) proprietà è di tipo [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) , un'enumerazione con quattro membri:

- `Stretch`, il valore predefinito
- `Center`
- `Start`(o "Flex-Start" in XAML)
- `End`(o "Flex-end" in XAML)

Si tratta di una delle due proprietà (l'altra [`AlignContent`](#the-aligncontent-property) ) che indica la modalità di allineamento degli elementi figlio sull'asse incrociato. All'interno di ogni riga, gli elementi figlio vengono estesi, come illustrato nello screenshot precedente, o allineati all'inizio, al centro o alla fine di ogni elemento, come illustrato nelle tre schermate seguenti:

[![Pagina dell'esperimento: Allinea elementi](flex-layout-images/ExperimentAlignItems.png "Pagina dell'esperimento-Allinea elementi")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

Nello screenshot iOS i vertici di tutti gli elementi figlio sono allineati. Negli screenshot Android gli elementi sono centrati verticalmente in base al figlio più alto. Nello screenshot UWP, i contenuti di tutti gli elementi sono allineati.

Per ogni singolo elemento, l' `AlignItems` impostazione può essere sottoposta a override con la [`AlignSelf`](#the-alignself-property) proprietà associabile associata.

### <a name="the-aligncontent-property"></a>Proprietà AlignContent

La [`AlignContent`](xref:Xamarin.Forms.FlexLayout.AlignContent) proprietà è di tipo [`FlexAlignContent`](xref:Xamarin.Forms.FlexAlignContent) , un'enumerazione con sette membri:

- `Stretch`, il valore predefinito
- `Center`
- `Start`(o "Flex-Start" in XAML)
- `End`(o "Flex-end" in XAML)
- `SpaceBetween`(o "spazio tra" in XAML)
- `SpaceAround`(o "space-around" in XAML)
- `SpaceEvenly`

Analogamente `AlignItems` a, la `AlignContent` Proprietà allinea anche gli elementi figlio sull'asse incrociato, ma interessa intere righe o colonne:

[![Pagina dell'esperimento: allinea contenuto](flex-layout-images/ExperimentAlignContent.png "Pagina dell'esperimento-allinea contenuto")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

Nella schermata iOS, entrambe le righe si trovano nella parte superiore; nello screenshot di Android sono al centro; e nella schermata UWP si trovano nella parte inferiore. Le righe possono anche essere separate da uno spazio in diversi modi:

[![Pagina dell'esperimento: allinea il contenuto 2](flex-layout-images/ExperimentAlignContent2.png "Pagina dell'esperimento-allinea il contenuto 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

`AlignContent`Non produce alcun effetto quando è presente una sola riga o colonna.

## <a name="the-attached-bindable-properties-in-detail"></a>Informazioni dettagliate sulle proprietà associabili associate

`FlexLayout`definisce cinque proprietà associabili associate. Queste proprietà vengono impostate per gli elementi figlio di `FlexLayout` e riguardano solo quel particolare elemento figlio.

### <a name="the-alignself-property"></a>Proprietà AlignSelf

La [`AlignSelf`](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) proprietà associabile associata è di tipo [`FlexAlignSelf`](xref:Xamarin.Forms.FlexAlignContent) , un'enumerazione con cinque membri:

- `Auto`, il valore predefinito
- `Stretch`
- `Center`
- `Start`(o "Flex-Start" in XAML)
- `End`(o "Flex-end" in XAML)

Per ogni singolo elemento figlio di `FlexLayout` , questa impostazione della proprietà esegue l'override della [`AlignItems`](#the-alignitems-property) proprietà impostata su `FlexLayout` . L'impostazione predefinita `Auto` consiste nell'utilizzare l' `AlignItems` impostazione.

Per un `Label` elemento denominato `label` (o esempio), è possibile impostare la `AlignSelf` proprietà nel codice simile al seguente:

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

Si noti che non esiste alcun riferimento all' `FlexLayout` elemento padre di `Label` . In XAML è possibile impostare la proprietà come segue:

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>Proprietà Order

La [`Order`](xref:Xamarin.Forms.FlexLayout.OrderProperty) proprietà è di tipo `int` . Il valore predefinito è 0.

La `Order` proprietà consente di modificare l'ordine in cui sono disposti gli elementi figlio dell'oggetto `FlexLayout` . In genere, gli elementi figlio di un oggetto `FlexLayout` sono disposti nello stesso ordine in cui vengono visualizzati nella `Children` raccolta. È possibile eseguire l'override di questo ordine impostando la `Order` proprietà associabile associata su un valore integer diverso da zero in uno o più elementi figlio. `FlexLayout`Dispone quindi gli elementi figlio in base all'impostazione della `Order` Proprietà in ogni elemento figlio, ma gli elementi figlio con la stessa `Order` impostazione sono disposti nell'ordine in cui sono visualizzati nella `Children` raccolta.

### <a name="the-basis-property"></a>Proprietà base

La [`Basis`](xref:Xamarin.Forms.FlexLayout.BasisProperty) proprietà associabile associata indica la quantità di spazio allocata a un elemento figlio di `FlexLayout` sull'asse principale. La dimensione specificata dalla `Basis` proprietà corrisponde alla dimensione lungo l'asse principale dell'elemento padre `FlexLayout` . Pertanto, `Basis` indica la larghezza di un elemento figlio quando gli elementi figlio sono disposti in righe o l'altezza quando gli elementi figlio sono disposti in colonne.

La `Basis` proprietà è di tipo [`FlexBasis`](xref:Xamarin.Forms.FlexBasis) , una struttura. È possibile specificare le dimensioni in unità indipendenti dal dispositivo o come percentuale delle dimensioni di `FlexLayout` . Il valore predefinito della `Basis` proprietà è la proprietà statica `FlexBasis.Auto` , il che significa che viene utilizzata la larghezza o l'altezza richiesta del figlio.

Nel codice è possibile impostare la `Basis` proprietà per un oggetto `Label` denominato `label` su 40 unità indipendenti dal dispositivo come la seguente:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

Il secondo argomento del `FlexBasis` costruttore è denominato `isRelative` e indica se la dimensione è relativa ( `true` ) o Absolute ( `false` ). Il valore predefinito dell'argomento è `false` , quindi è possibile usare anche il codice seguente:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Una conversione implicita da `float` a `FlexBasis` è definita, pertanto è possibile semplificarla ulteriormente:

```csharp
FlexLayout.SetBasis(label, 40);
```

È possibile impostare le dimensioni sul 25% del `FlexLayout` padre come segue:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Il valore frazionario deve essere compreso tra 0 e 1.

In XAML è possibile usare un numero di dimensioni in unità indipendenti dal dispositivo:

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

In alternativa, è possibile specificare una percentuale nell'intervallo compreso tra 0 e 100%:

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

La pagina dell' **esperimento di base** dell'esempio **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** consente di sperimentare la `Basis` Proprietà. La pagina Visualizza una colonna di cui è stato eseguito il wrapped `Label` con cinque elementi con colori di sfondo e di primo piano alternati. Due `Slider` elementi consentono di specificare `Basis` i valori per il secondo e il quarto `Label` :

[![Pagina dell'esperimento di base](flex-layout-images/BasisExperiment.png "Pagina dell'esperimento di base")](flex-layout-images/BasisExperiment-Large.png#lightbox)

Lo screenshot iOS a sinistra mostra i due `Label` elementi a cui vengono assegnate le altezze in unità indipendenti dal dispositivo. La schermata Android mostra che vengono date le altezze che rappresentano una frazione dell'altezza totale dell'oggetto `FlexLayout` . Se `Basis` è impostato su 100%, l'elemento figlio è l'altezza di `FlexLayout` e viene eseguito il wrapping nella colonna successiva e viene occupata l'intera altezza di tale colonna, come illustrato nella schermata UWP: sembra che i cinque elementi figlio siano disposti in una riga, ma sono effettivamente disposti in cinque colonne.

### <a name="the-grow-property"></a>Proprietà Grow

La [`Grow`](xref:Xamarin.Forms.FlexLayout.GrowProperty) proprietà associabile associata è di tipo `int` . Il valore predefinito è 0 e il valore deve essere maggiore o uguale a 0.

La `Grow` Proprietà svolge un ruolo quando la `Wrap` proprietà è impostata su `NoWrap` e la lunghezza totale della riga di elementi figlio è inferiore alla larghezza di `FlexLayout` oppure la colonna di elementi figlio ha un'altezza più breve rispetto a `FlexLayout` . La `Grow` proprietà indica come ripartire lo spazio rimanente tra gli elementi figlio.

Nella pagina **crescita esperimento** , cinque `Label` elementi di colori alternativi vengono disposti in una colonna e due `Slider` elementi consentono di modificare la `Grow` proprietà del secondo e del quarto `Label` . Lo screenshot iOS all'estrema sinistra mostra le proprietà predefinite `Grow` 0:

[![Pagina di crescita dell'esperimento](flex-layout-images/GrowExperiment.png "Pagina di crescita dell'esperimento")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Se a un figlio viene assegnato un `Grow` valore positivo, l'elemento figlio occupa tutto lo spazio rimanente, come illustrato nella schermata Android. Questo spazio può essere allocato anche tra due o più elementi figlio. Nella schermata UWP la `Grow` proprietà della seconda `Label` è impostata su 0,5, mentre la `Grow` proprietà del quarto `Label` è 1,5, che restituisce il quarto `Label` tre volte la maggior parte dello spazio residuo come secondo `Label` .

Il modo in cui la visualizzazione figlio usa tale spazio dipende dal tipo particolare di elemento figlio. Per un oggetto `Label` , il testo può essere posizionato nello spazio totale del `Label` utilizzando le proprietà `HorizontalTextAlignment` e `VerticalTextAlignment` .

### <a name="the-shrink-property"></a>Proprietà Shrink

La [`Shrink`](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) proprietà associabile associata è di tipo `int` . Il valore predefinito è 1 e il valore deve essere maggiore o uguale a 0.

La `Shrink` Proprietà svolge un ruolo quando la `Wrap` proprietà è impostata su `NoWrap` e la larghezza di aggregazione di una riga di elementi figlio è maggiore della larghezza di `FlexLayout` oppure l'altezza di aggregazione di una singola colonna di elementi figlio è maggiore dell'altezza di `FlexLayout` . Normalmente il visualizzerà `FlexLayout` questi elementi figlio limitando le loro dimensioni. La `Shrink` proprietà può indicare a quali elementi figlio viene assegnata la priorità per la visualizzazione delle dimensioni complete.

La pagina dell' **esperimento di compattazione** crea un oggetto `FlexLayout` con una singola riga di cinque `Label` elementi figlio che richiedono più spazio rispetto alla `FlexLayout` larghezza. Lo screenshot iOS a sinistra Mostra tutti gli `Label` elementi con i valori predefiniti 1:

[![Pagina dell'esperimento di compattazione](flex-layout-images/ShrinkExperiment.png "Pagina dell'esperimento di compattazione")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

Nello screenshot Android, il `Shrink` valore per il secondo `Label` è impostato su 0 e `Label` viene visualizzato nella sua larghezza intera. Al quarto viene inoltre `Label` assegnato un `Shrink` valore maggiore di uno ed è stato compattato. Lo screenshot UWP Mostra che a entrambi `Label` gli elementi viene assegnato un `Shrink` valore pari a 0 per consentirne la visualizzazione delle dimensioni complete, se possibile.

È possibile impostare entrambi i `Grow` `Shrink` valori e in modo da gestire le situazioni in cui le dimensioni figlio aggregate potrebbero a volte essere minori o maggiori delle dimensioni di `FlexLayout` .

## <a name="css-styling-with-flexlayout"></a>Stile CSS con FlexLayout

È possibile usare la funzionalità di [stile CSS](~/xamarin-forms/user-interface/styles/css/index.md) introdotta con Xamarin.Forms 3,0 in connessione a `FlexLayout` . La pagina **elementi del catalogo CSS** dell'esempio **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** Duplica il layout della pagina **elementi del catalogo** , ma con un foglio di stile CSS per molti stili:

[![Pagina elementi del catalogo CSS](flex-layout-images/CssCatalogItems.png "Pagina elementi del catalogo CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

Il file **CatalogItemsPage. XAML** originale presenta cinque `Style` definizioni nella `Resources` sezione con 15 `Setter` oggetti. Nel file **CssCatalogItemsPage. XAML** , che è stato ridotto a due `Style` definizioni con soli quattro `Setter` oggetti. Questi stili integrano il foglio di stile CSS per le proprietà Xamarin.Forms attualmente non supportate dalla funzionalità di stile CSS:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CssCatalogItemsPage"
             Title="CSS Catalog Items">
    <ContentPage.Resources>
        <StyleSheet Source="CatalogItemsStyles.css" />

        <Style TargetType="Frame">
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey" StyleClass="header" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey" StyleClass="header" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey" StyleClass="header" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Si fa riferimento al foglio di stile CSS nella prima riga della `Resources` sezione:

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Si noti anche che due elementi in ognuno dei tre elementi includono `StyleClass` le impostazioni:

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

Si fa riferimento ai selettori nel foglio di stile **CatalogItemsStyles. CSS** :

```css
frame {
    width: 300;
    height: 480;
    background-color: lightyellow;
    margin: 10;
}

label {
    margin: 4 0;
}

label.header {
    margin: 8 0;
    font-size: large;
    color: blue;
}

label.empty {
    flex-grow: 1;
}

image {
    height: 180;
    order: -1;
    align-self: center;
}

button {
    font-size: large;
    color: white;
    background-color: green;
}
```

`FlexLayout`Qui viene fatto riferimento a diverse proprietà associabili associate. Nel `label.empty` selettore verrà visualizzato l' `flex-grow` attributo, che designa un oggetto vuoto `Label` per fornire uno spazio vuoto sopra `Button` . Il `image` selettore contiene un `order` attributo e un `align-self` attributo, entrambi corrispondenti alle `FlexLayout` proprietà associabili associate.

Si è visto che è possibile impostare le proprietà direttamente in `FlexLayout` ed è possibile impostare proprietà associabili associate negli elementi figlio di un oggetto `FlexLayout` . In alternativa, è possibile impostare queste proprietà indirettamente usando gli stili CSS o gli stili CSS tradizionali basati su XAML. È importante conoscere e comprendere queste proprietà. Queste proprietà sono che rendono `FlexLayout` effettivamente flessibile.

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout con Novell. University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin.Formsvideo di layout Flex 3,0**

## <a name="related-links"></a>Collegamenti correlati

- [FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)
