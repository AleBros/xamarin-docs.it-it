---
title: FlexLayout di xamarin. Forms
description: Usare FlexLayout per la sovrapposizione o il wrapping di una raccolta di visualizzazioni figlio.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 187befd88c115133a92aa90a711438e7754518d5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648798"
---
# <a name="the-xamarinforms-flexlayout"></a>FlexLayout di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_Usare FlexLayout per la sovrapposizione o il wrapping di una raccolta di visualizzazioni figlio._

Xamarin. Forms [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout) è stata introdotta in xamarin. Forms versione 3.0. È basata su foglio di stile CSS [flessibile Box Layout Module](http://www.w3.org/TR/css-flexbox-1/), noto come _flex layout_ oppure _flex-finestra_, pertanto chiamato perché include numerose opzioni flessibili per disporre gli elementi figlio all'interno del layout.

`FlexLayout` è simile a xamarin. Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md) in quanto è possibile disporre gli elementi figlio orizzontalmente e verticalmente in uno stack. Tuttavia, il `FlexLayout` è inoltre in grado di ritorno a capo i relativi elementi figlio se sono presenti troppi regolare in una singola riga o colonna e ha inoltre numerose opzioni per l'orientamento dell'allineamento e adattarsi alle diverse dimensioni dello schermo.

`FlexLayout` deriva da [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) ed eredita un [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) vlastnosti typu `IList<View>`.

`FlexLayout` definisce sei proprietà associabili pubbliche e cinque proprietà associabili associate che interessano le dimensioni, l'orientamento e l'allineamento degli elementi figlio. (Se non si ha familiarità con le proprietà associabili associate, vedere l'articolo  **[le proprietà associate](~/xamarin-forms/xaml/attached-properties.md)** .) Queste proprietà sono descritte in dettaglio nelle sezioni riportate di seguito sul **[le proprietà associabili dettagliatamente](#bindable-properties)** e  **[le proprietà associabili associate in modo dettagliato](#attached-properties)** . Tuttavia, questo articolo inizia con una sezione in alcune **[scenari di utilizzo comuni](#common-scenarios)** di `FlexLayout` molte di queste proprietà che descrive in modo più informale. Verso la fine dell'articolo, scoprirai come combinare `FlexLayout` con [fogli di stile CSS](~/xamarin-forms/user-interface/styles/css/index.md).

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>Scenari di utilizzo comuni

Il **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** programma di esempio è disponibili diverse pagine che illustrano alcuni utilizzi comuni `FlexLayout` e consente di sperimentare con le relative proprietà.

### <a name="using-flexlayout-for-a-simple-stack"></a>Usando FlexLayout per lo stack di semplice

Il **Stack semplice** pagina viene mostrato come `FlexLayout` puoi sostituire a un `StackLayout` ma con markup più semplice. Tutti gli elementi in questo esempio viene definito nella pagina XAML. Il `FlexLayout` contiene quattro elementi figlio:

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

Ecco la pagina in esecuzione in iOS, Android e la piattaforma Windows universale:

[![Il semplice Stack pagina](flex-layout-images/SimpleStack.png "il semplice Stack pagina")](flex-layout-images/SimpleStack-Large.png#lightbox)

Tre proprietà del `FlexLayout` vengono visualizzati nei **SimpleStackPage.xaml** file:

- Il [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) proprietà è impostata su un valore di [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection) enumerazione. Il valore predefinito è `Row`. Impostazione della proprietà su `Column` fa in modo che gli elementi figlio del `FlexLayout` devono essere disposti in una singola colonna di elementi.

    Quando gli elementi in un `FlexLayout` vengono disposte in una colonna, il `FlexLayout` si dice che ha un parametro vertical _asse principale_ e orizzontale _incrociato asse_.

- Il [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) proprietà è di tipo [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems) e specifica come gli elementi sono allineati sull'asse incrociato. Il `Center` opzione fa in modo che ogni elemento da allineare al centro in senso orizzontale.

    Se si usava una `StackLayout` piuttosto che un `FlexLayout` per questa attività, si sarebbero center tutti gli elementi tramite l'assegnazione di `HorizontalOptions` proprietà di ogni elemento per `Center`. Il `HorizontalOptions` proprietà non funziona per gli elementi figlio di un `FlexLayout`, ma il singolo `AlignItems` proprietà svolge lo stesso scopo. Se si desidera, è possibile usare la `AlignSelf` associata la proprietà associabile per eseguire l'override di `AlignItems` proprietà per i singoli elementi:

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    Con tale modifica, il presente `Label` è posizionato in corrispondenza del bordo sinistro di `FlexLayout` quando l'ordine di lettura è da sinistra a destra.

- Il [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) proprietà è di tipo [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)e specifica come gli elementi vengono disposti sull'asse principale. Il `SpaceEvenly` opzione alloca tutti spazio verticale rimasto distribuita equamente tra tutti gli elementi e sopra il primo elemento e sotto l'ultimo elemento.

    Se si usa un' `StackLayout`, è necessario assegnare il `VerticalOptions` proprietà di ogni elemento per `CenterAndExpand` per ottenere un risultato simile. Ma la `CenterAndExpand` opzione allocherà doppio dello spazio tra ogni elemento rispetto a prima del primo elemento e dopo l'ultimo elemento. È possibile riprodurre il `CenterAndExpand` opzione di `VerticalOptions` impostando il `JustifyContent` proprietà della `FlexLayout` a `SpaceAround`.

Questi `FlexLayout` proprietà sono descritte più dettagliatamente nella sezione **[le proprietà associabili dettagliatamente](#bindable-properties)** sotto.

### <a name="using-flexlayout-for-wrapping-items"></a>Usando FlexLayout per il wrapping di elementi

Il **foto Wrapping** pagina della **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** esempio viene illustrato come `FlexLayout` può eseguire il wrapping dei relativi figli righe o colonne aggiuntive. Il file XAML crea un'istanza di `FlexLayout` e assegna le due proprietà di esso:

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

Il `Direction` proprietà di questo `FlexLayout` non è impostata, pertanto l'impostazione predefinita di `Row`, vale a dire che gli elementi figlio vengono disposti in righe e l'asse principale è orizzontale.

Il [ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) proprietà è di tipo di enumerazione [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap). Se sono presenti troppi elementi per adattarsi a una riga, impostazione di questa proprietà fa sì che gli elementi eseguire il wrapping alla riga successiva.

Si noti che il `FlexLayout` è un figlio di un `ScrollView`. Se sono presenti troppe righe per pagina, il `ScrollView` ha un valore predefinito `Orientation` proprietà di `Vertical` e consente lo scorrimento verticale.

Il `JustifyContent` proprietà alloca spazio rimasto sull'asse principale (l'asse orizzontale) in modo che ogni elemento verrà racchiusi tra la stessa quantità di spazio vuoto.

Il file code-behind accede a una raccolta di foto di esempio e li aggiunge al `Children` raccolta del `FlexLayout`:

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

Ecco il programma in esecuzione, progressivamente scorrere dall'alto verso il basso:

[![La pagina di ritorno a capo foto](flex-layout-images/PhotoWrapping.png "la pagina di wrapping delle foto")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>Layout di pagina con FlexLayout

Nella progettazione di web chiamato è un layout standard il [ _Sacro Graal_ ](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) perché è un formato di layout che è molto utile, ma spesso difficile da realizzare con alla perfezione. Il layout è costituito da un'intestazione nella parte superiore della pagina e piè di pagina nella parte inferiore, sia che si estende all'intera larghezza della pagina. Che occupa il centro della pagina è il contenuto principale, ma spesso con un menu a sinistra del contenuto e le informazioni supplementari a colonne (talvolta chiamato un' _riservato_ area) a destra. [Sezione 5.4.1 della specifica del Layout di caselle flessibili CSS](http://www.w3.org/TR/css-flexbox-1/#order-accessibility) viene descritto come il layout Sacro Graal può essere realizzato con una casella flessibile.

Il **Layout Sacro Graal** pagina della **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** illustra un'implementazione semplice di questo layout usando uno `FlexLayout` nidificato in un altro. Poiché questa pagina è progettata per un telefono in modalità verticale, le aree a sinistra e a destra dell'area del contenuto sono solo 50 pixel in larghezza:

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

Qui è in esecuzione:

[![La pagina di Layout Sacro Graal](flex-layout-images/HolyGrailLayout.png "la pagina di Layout Sacro Graal")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

Le aree di navigazione e aside eseguono il rendering di un `BoxView` a sinistra e destra.

Il primo `FlexLayout` nel XAML file dispone di un asse principale verticale e contiene tre membri figlio disposti in una colonna. Si tratta di intestazione, corpo della pagina e piè di pagina. Annidata `FlexLayout` dispone di un asse orizzontale principale con tre membri figlio disposti in una riga.

Tre proprietà associabili associate sono illustrate in questo programma:

- Il `Order` proprietà associabili associata è impostata sul primo `BoxView`. Questa proprietà è di tipo integer e un valore predefinito pari a 0. È possibile usare questa proprietà per modificare l'ordine di layout. In genere gli sviluppatori preferiscono il contenuto della pagina venga visualizzato nel markup prima gli elementi di navigazione e riservato gli elementi. Impostando il `Order` proprietà in corrispondenza del primo `BoxView` su un valore minore di elementi di altri pari livello fa in modo che venga visualizzato come primo elemento nella riga. Analogamente, è possibile garantire che un elemento viene visualizzato per ultimo, impostando il `Order` proprietà su un valore maggiore di elementi di pari livello.

- Il `Basis` proprietà associabili associata è impostata su due `BoxView` elementi per assegnarvi una larghezza pari a 50 pixel. Questa proprietà è di tipo `FlexBasis`, una struttura che definisca una proprietà statica typu `FlexBasis` denominata `Auto`, ovvero l'impostazione predefinita. È possibile usare `Basis` per specificare una dimensione in pixel o percentuale che indica la quantità di spazio occupata l'elemento sull'asse principale. Viene chiamato un _base_ perché specifica una dimensione di elemento che costituisce la base del layout tutte le successive.

- Il `Grow` è impostata su annidata `Layout` e nella `Label` figlio che rappresenta il contenuto. Questa proprietà è di tipo `float` e ha un valore predefinito pari a 0. Se impostato su un valore positivo, tutto lo spazio rimanente lungo l'asse principale viene allocato per tale elemento e nodi di pari livello con i valori positivi di `Grow`. Lo spazio viene allocato in modo proporzionale ai valori, in qualche modo, ad esempio la specifica di stelle in un `Grid`.

    Il primo `Grow` proprietà associata è impostata su annidata `FlexLayout`, che indica che questo `FlexLayout` è in modo che occupi tutto lo spazio verticale non usato all'interno di esterna `FlexLayout`. La seconda `Grow` proprietà associata è impostata sul `Label` che rappresenta il contenuto, che indica che questo contenuto è in modo che occupi tutto lo spazio orizzontale non usato all'interno di interna `FlexLayout`.

    È inoltre disponibile un simile `Shrink` proprietà associabile che è possibile usare quando le dimensioni degli elementi figlio superano le dimensioni di associata la `FlexLayout` ma non si desidera ritorno a capo.

### <a name="catalog-items-with-flexlayout"></a>Elementi del catalogo con FlexLayout

Il **gli elementi del catalogo** pagina il **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** è simile all'esempio [1 riportato nella sezione 1.1 della specifica di CSS Flex Layout finestra](http://www.w3.org/TR/css-flexbox-1/#overview)ad eccezione del fatto che consente di visualizzare una serie di immagini che è possibile scorrere in senso orizzontale e le descrizioni di esistono tre programmatori:

[![Pagina elementi di catalogo](flex-layout-images/CatalogItems.png "elementi catalogo")](flex-layout-images/CatalogItems-Large.png#lightbox)

Ogni i tre esistono programmatori è un `FlexLayout` contenuti in un `Frame` che viene assegnato un esplicita altezza e la larghezza e che è anche un elemento figlio di un maggiore `FlexLayout`. In questo file XAML, la maggior parte delle proprietà del `FlexLayout` gli elementi figlio vengono specificati negli stili, tutte tranne uno dei quali è uno stile implicito:

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

Lo stile implicito per il `Image` include le impostazioni di due proprietà associabili associate di `Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

Il `Order` impostazione di &ndash;1 causa la `Image` elemento da visualizzare prima di tutto in ognuna delle annidata `FlexLayout` visualizzazioni indipendentemente dalla relativa posizione all'interno della raccolta di elementi figlio. Il `AlignSelf` proprietà di `Center` fa sì che il `Image` da allineare al centro all'interno di `FlexLayout`. Questa impostazione sostituisce l'impostazione delle `AlignItems` proprietà, che ha un valore predefinito di `Stretch`, ovvero che il `Label` e `Button` figli vengono allungati fino alla larghezza massima del `FlexLayout`.

All'interno di ognuno dei tre `FlexLayout` viste, uno spazio vuoto `Label` precede la `Button`, ma presenta un `Grow` impostazione pari a 1. Ciò significa che tutto lo spazio extra verticale è allocato a vuoto `Label`, che inserisce in modo efficace il `Button` verso il basso.

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>Le proprietà associabili in dettaglio

Ora che si sono appreso alcune applicazioni comuni di `FlexLayout`, le proprietà di `FlexLayout` possono essere esaminate in maggiore dettaglio.
`FlexLayout` Definisce i sei proprietà associabili che è nastavit il `FlexLayout` stessa, nel codice o XAML, allineamento e orientamento del controllo. (Una di queste proprietà, [ `Position` ](xref:Xamarin.Forms.FlexLayout.Position), non è illustrata in questo articolo.)

È possibile provare con cinque rimanenti proprietà associabili usando il **sperimentare** pagina della **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** esempio. Questa pagina consente di aggiungere o rimuovere gli elementi figlio da un `FlexLayout` e impostare le combinazioni di cinque proprietà associabili. Tutti gli elementi figlio del `FlexLayout` vengono `Label` le viste di diversi colori e dimensioni, con la `Text` proprietà è impostata su un numero corrispondente alla relativa posizione nel `Children` raccolta.

Quando il programma di avvio, cinque `Picker` consente di visualizzare i valori predefiniti di questi cinque `FlexLayout` proprietà. Il `FlexLayout` nella parte inferiore della schermata contiene tre elementi figlio:

[![Pagina dell'esperimento: Impostazione]predefinita(flex-layout-images/ExperimentDefault.png "della pagina dell'esperimento-impostazione predefinita")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Ognuno del `Label` viste presenta uno sfondo grigio che mostra lo spazio allocato a quella `Label` all'interno di `FlexLayout`. Lo sfondo del `FlexLayout` stesso è bianco ghiaccio. Occupa l'intero inferiore della pagina, ad eccezione di un piccolo margine a sinistra e a destra.

<a name="direction" />

### <a name="the-direction-property"></a>La proprietà direzione

Il [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) proprietà è di tipo [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection), un'enumerazione con quattro membri:

- `Column`
- `ColumnReverse` (o "colonna-reverse" in XAML)
- `Row`, il valore predefinito
- `RowReverse` (o "riga-reverse" in XAML)

In XAML, è possibile specificare il valore di questa proprietà con i nomi dei membri di enumerazione in caratteri minuscoli, maiuscoli, o formato misto, oppure è possibile usare due stringhe aggiuntive racchiusa tra parentesi che sono gli stessi indicatori di CSS. (Le stringhe "colonna-reverse" e "riga-reverse" sono definite nel [ `FlexDirectionTypeConverter` ](xref:Xamarin.Forms.FlexDirectionTypeConverter) classe utilizzata dal parser XAML.)

Di seguito è riportato il **esperimento** pagina che mostra (da sinistra a destra), il `Row` direzione `Column` direzione, e `ColumnReverse` direzione:

[![Pagina dell'esperimento: Direzione](flex-layout-images/ExperimentDirection.png "della pagina dell'esperimento")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Si noti che per il `Reverse` opzioni, gli elementi di partono da destra o inferiore.

<a name="wrap" />

### <a name="the-wrap-property"></a>La proprietà a capo automatico

Il [ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) proprietà è di tipo [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap), un'enumerazione con tre membri:

- `NoWrap`, il valore predefinito
- `Wrap`
- `Reverse` (o "a capo-reverse" in XAML)

Da sinistra a destra, visualizzare queste schermate vengono visualizzate le `NoWrap`, `Wrap` e `Reverse` opzioni per gli elementi 12 figlio:

[![Pagina dell'esperimento: Eseguire](flex-layout-images/ExperimentWrap.png "il wrapping della pagina dell'esperimento a capo")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Quando la `Wrap` è impostata su `NoWrap` è vincolato all'asse principale (come in questo programma) e l'asse principale non è sufficientemente grande per rientrare tutti i nodi figlio, o a livello di `FlexLayout` tenti di definire gli elementi più piccoli, come nella schermata di iOS viene illustrato. È possibile controllare il shrinkness degli elementi con il [ `Shrink` ](#shrink) proprietà associabili associata.

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>La proprietà JustifyContent

Il [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) proprietà è di tipo [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify), un'enumerazione con sei membri:

- `Start` (o "flex-start" in XAML), il valore predefinito
- `Center`
- `End` (o "flex-end" in XAML)
- `SpaceBetween` (o "spazio tra" in XAML)
- `SpaceAround` (o "spazio-around" in XAML)
- `SpaceEvenly`

Questa proprietà specifica come gli elementi sono disposti sull'asse principale, ovvero l'asse orizzontale in questo esempio:

[![Pagina dell'esperimento: Giustificare](flex-layout-images/ExperimentJustifyContent.png "il contenuto della pagina dell'esperimento-giustificare il contenuto")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

In tutte le tre schermate, i `Wrap` è impostata su `Wrap`. Il `Start` predefinito viene illustrato nello screenshot di Android precedente. Nella schermata iOS qui viene mostrato il `Center` opzione: tutti gli elementi vengono spostati al centro. Le altre tre opzioni che iniziano con la parola `Space` allocare lo spazio non occupato da elementi. `SpaceBetween` alloca lo spazio in modo uniforme tra gli elementi; `SpaceAround` puts uguale spazio intorno a ogni elemento, mentre `SpaceEvenly` puts lo spazio tra ogni elemento e prima del primo elemento e dopo l'ultimo elemento nella riga è uguale.

<a name="align-items" />

### <a name="the-alignitems-property"></a>La proprietà AlignItems

Il [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) proprietà è di tipo [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems), un'enumerazione con quattro membri:

- `Stretch`, il valore predefinito
- `Center`
- `Start` (o "flex-start" in XAML)
- `End` (o "flex-end" in XAML)

Questa è una delle due proprietà (l'altro che [ `AlignContent` ](#align-content)) che indica come elementi figlio vengono allineate lungo l'asse incrociato. In ogni riga, gli elementi figlio vengono estesi (come illustrato nello screenshot precedente) o allineati all'avvio, center o alla fine di ogni elemento, come illustrato negli screenshot seguenti tre:

[![Pagina dell'esperimento: Allinea elementi](flex-layout-images/ExperimentAlignItems.png "alla pagina dell'esperimento-Allinea elementi")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

Nella schermata iOS, le parti superiori di tutti i nodi figlio sono allineate. Negli screenshot di Android, gli elementi sono centrati verticalmente base figlio più alto. Nella schermata UWP, la sequenza di tutti gli elementi è allineata.

Per un singolo elemento, il `AlignItems` impostazione può essere sostituita con il [ `AlignSelf` ](#align-self) proprietà associabili associata.

<a name="align-content" />

### <a name="the-aligncontent-property"></a>La proprietà AlignContent

Il [ `AlignContent` ](xref:Xamarin.Forms.FlexLayout.AlignContent) proprietà è di tipo [ `FlexAlignContent` ](xref:Xamarin.Forms.FlexAlignContent), un'enumerazione con sette membri:

- `Stretch`, il valore predefinito
- `Center`
- `Start` (o "flex-start" in XAML)
- `End` (o "flex-end" in XAML)
- `SpaceBetween` (o "spazio tra" in XAML)
- `SpaceAround` (o "spazio-around" in XAML)
- `SpaceEvenly`

Ad esempio `AlignItems`, il `AlignContent` proprietà inoltre consente di allineare gli elementi figlio sull'asse incrociato, ma influisce sulle righe o colonne intere:

[![Pagina dell'esperimento: Allinea il]contenuto(flex-layout-images/ExperimentAlignContent.png "della pagina dell'esperimento-allinea il contenuto")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

Nella schermata iOS, entrambe le righe sono in alto. Nella schermata Android in cui si trovano al centro; e nella schermata UWP sono nella parte inferiore. Le righe possono anche essere separate da uno spazio in vari modi:

[![Pagina dell'esperimento:  Allinea il contenuto]2(flex-layout-images/ExperimentAlignContent2.png "la pagina dell'esperimento-allinea il contenuto 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

Il `AlignContent` non ha alcun effetto quando si verifica solo una riga o colonna.

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>Le proprietà associabili associate in modo dettagliato

`FlexLayout` definisce le proprietà associabili associate cinque. Queste proprietà vengono impostate su elementi figlio del `FlexLayout` e si riferiscono solo a quel particolare elemento figlio.

<a name="align-self" />

### <a name="the-alignself-property"></a>La proprietà AlignSelf

Il [ `AlignSelf` ](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) proprietà associabili associata è di tipo [ `FlexAlignSelf` ](xref:Xamarin.Forms.FlexAlignContent), un'enumerazione con cinque membri:

- `Auto`, il valore predefinito
- `Stretch`
- `Center`
- `Start` (o "flex-start" in XAML)
- `End` (o "flex-end" in XAML)

Di qualsiasi singolo figlio di `FlexLayout`, questa proprietà di impostazione delle sostituzioni il [ `AlignItems` ](#align-items) proprietà è impostata sul `FlexLayout` stesso. L'impostazione predefinita `Auto` indica che verranno utilizzati i `AlignItems` impostazione.

Per un `Label` elemento denominato `label` (o esempio), è possibile impostare il `AlignSelf` proprietà nel codice simile al seguente:

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

Si noti che non sono presenti riferimenti per il `FlexLayout` padre del `Label`. In XAML, è impostare la proprietà simile al seguente:

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>La proprietà dell'ordine

Il [ `Order` ](xref:Xamarin.Forms.FlexLayout.OrderProperty) proprietà è di tipo `int`. Il valore predefinito è 0.

Il `Order` proprietà consente di modificare l'ordine che gli elementi figlio del `FlexLayout` vengono disposti. In genere, gli elementi figlio di un `FlexLayout` disposte nello stesso ordine in cui appaiono nella `Children` raccolta. È possibile eseguire l'override di questo ordine impostando il `Order` proprietà associabili associata a un valore intero diverso da zero in uno o più figli. Il `FlexLayout` dispone quindi i relativi elementi figlio in base all'impostazione delle `Order` proprietà per ogni figlio, ma gli elementi figlio con lo stesso `Order` impostazione vengono disposti nell'ordine in cui appaiono nella `Children` raccolta.

### <a name="the-basis-property"></a>La proprietà di base

Il [ `Basis` ](xref:Xamarin.Forms.FlexLayout.BasisProperty) proprietà associabili associata indica la quantità di spazio allocata a un elemento figlio del `FlexLayout` sull'asse principale. Le dimensioni specificate per il `Basis` proprietà corrisponde alle dimensioni lungo l'asse principale dell'elemento padre `FlexLayout`. Pertanto, `Basis` indica la larghezza di un elemento figlio quando gli elementi figlio vengono disposti in righe o l'altezza quando gli elementi figlio vengono disposti in colonne.

Il `Basis` proprietà è di tipo [ `FlexBasis` ](xref:Xamarin.Forms.FlexBasis), una struttura. La dimensione può essere specificata in un'unità di misura indipendenti dal dispositivo o come percentuale delle dimensioni del `FlexLayout`. Il valore predefinito di `Basis` è la proprietà statica `FlexBasis.Auto`, il che significa che l'elemento figlio della richiesta larghezza o altezza viene utilizzato.

Nel codice, è possibile impostare il `Basis` proprietà per un `Label` denominata `label` a 40 device independent unit, simile al seguente:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

Il secondo argomento per il `FlexBasis` è denominato costruttore `isRelative` e indica se la dimensione è relativa (`true`) o assoluto (`false`). L'argomento ha un valore predefinito di `false`, pertanto è anche possibile usare il codice seguente:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Una conversione implicita da `float` a `FlexBasis` è definito, quindi è possibile semplificare ulteriormente:

```csharp
FlexLayout.SetBasis(label, 40);
```

È possibile impostare le dimensioni al 25% del `FlexLayout` padre simile al seguente:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Questo valore frazionario deve essere compreso nell'intervallo da 0 a 1.

In XAML, è possibile usare un numero per una dimensione in unità indipendenti dal dispositivo:

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

In alternativa, è possibile specificare una percentuale nell'intervallo del 0% al 100%:

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

Il **base sperimentare** pagina della **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** esempio consente di sperimentare il `Basis` proprietà. La pagina viene visualizzata una colonna con wrapping di cinque `Label` gli elementi con alternanza di colori di sfondo e primo piano. Due `Slider` elementi consentono di specificare `Basis` i valori per la seconda e la quarta `Label`:

[![Pagina di sperimentare le basi](flex-layout-images/BasisExperiment.png "base sperimentare pagina")](flex-layout-images/BasisExperiment-Large.png#lightbox)

Nella schermata iOS a sinistra mostra i due `Label` elementi viene forniti l'altezza in unità indipendenti dal dispositivo. Visualizza lo schermo Android viene fornito l'altezza delle righe che sono una frazione dell'altezza totale del `FlexLayout`. Se l' `Basis` oggetto è impostato su 100%, l'elemento figlio è l'altezza `FlexLayout`di ed esegue il wrapping nella colonna successiva e occupa l'intera altezza di tale colonna, come illustrato nella schermata UWP: Viene visualizzato come se i cinque elementi figlio fossero disposti in una riga, ma in realtà sono disposti in cinque colonne.

### <a name="the-grow-property"></a>L'aumento delle dimensioni di proprietà

Il [ `Grow` ](xref:Xamarin.Forms.FlexLayout.GrowProperty) proprietà associabili associata è di tipo `int`. Il valore predefinito è 0 e il valore deve essere maggiore o uguale a 0.

Il `Grow` proprietà svolge un ruolo quando il `Wrap` è impostata su `NoWrap` e la riga degli elementi figlio ha una larghezza totale minore di quella del `FlexLayout`, o la colonna di elementi figlio ha un'altezza inferiore rispetto al `FlexLayout`. Il `Grow` proprietà indica come eseguire la ripartizione di spazi vuoti tra gli elementi figlio.

Nel **esperimento di aumento delle dimensioni** pagina, cinque `Label` elementi di colori alternati sono disposti in una colonna e due `Slider` elementi consentono di regolare il `Grow` proprietà del secondo e il quarto `Label`. La schermata iOS all'estrema sinistra mostra il valore predefinito `Grow` proprietà pari a 0:

[![La pagina dell'esperimento Grow](flex-layout-images/GrowExperiment.png "pagina dell'esperimento di aumento")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Se qualsiasi un elemento figlio non viene specificato un valore positivo `Grow` valore, tale elemento figlio occupa tutto lo spazio rimanente, come illustra lo screenshot di Android. Questo può anche essere allocato tra due o più elementi figlio. Nella schermata UWP, il `Grow` proprietà del secondo `Label` è impostata su 0,5, mentre il `Grow` proprietà del quarto `Label` è la 1.5, che consente la quarta `Label` di triplicare dello spazio rimasto come il secondo `Label`.

Come la visualizzazione figlio Usa lo spazio dipende dal tipo specifico di elemento figlio. Per un `Label`, il testo può essere posizionato all'interno di spazio totale del `Label` usando la proprietà `HorizontalTextAlignment` e `VerticalTextAlignment`.

<a name="shrink" />

### <a name="the-shrink-property"></a>La proprietà di compattazione

Il [ `Shrink` ](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) proprietà associabili associata è di tipo `int`. Il valore predefinito è 1 e il valore deve essere maggiore o uguale a 0.

Il `Shrink` proprietà svolge un ruolo quando il `Wrap` è impostata su `NoWrap` e la larghezza di aggregazione di una riga degli elementi figlio è superiore alla larghezza del `FlexLayout`, o l'aggregazione altezza di una singola colonna di elementi figlio è superiore al altezza del `FlexLayout`. In genere il `FlexLayout` visualizzerà questi elementi figlio da constricting le relative dimensioni. Il `Shrink` proprietà può indicare quali elementi figlio sia data priorità nella visualizzazione le relative dimensioni completi.

Il **compattare esperimento** pagina consente di creare un `FlexLayout` con una sola riga di cinque `Label` figli che richiedono più spazio rispetto al `FlexLayout` larghezza. La schermata iOS a sinistra mostra tutte le `Label` elementi con i valori predefiniti pari a 1:

[![Pagina di sperimentare la compattazione](flex-layout-images/ShrinkExperiment.png "la compattazione sperimentare pagina")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

Nello screenshot di Android le `Shrink` valore per il secondo `Label` è impostata su 0 e che `Label` viene visualizzato nella larghezza intera. Inoltre, la quarta `Label` viene assegnato un `Shrink` valore maggiore di uno e è compattato. Lo screenshot UWP illustra entrambe `Label` elementi di cui è stati assegnati un `Shrink` il valore 0 per consentirgli di essere visualizzato alle dimensioni intere, se è possibile.

È possibile impostare entrambe le `Grow` e `Shrink` i valori per consentire situazioni in cui le dimensioni aggregate figlio potrebbero talvolta essere minore di o a volte maggiore delle dimensioni del `FlexLayout`.

## <a name="css-styling-with-flexlayout"></a>Applicazione degli stili CSS con FlexLayout

È possibile usare la [lo stile CSS](~/xamarin-forms/user-interface/styles/css/index.md) funzionalità introdotta con xamarin. Forms 3.0 in relazione a `FlexLayout`. Il **CSS gli elementi del catalogo** pagina della **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** esempio duplica il layout del **gli elementi del catalogo** pagina, ma con un CSS foglio di stile per molti degli stili:

[![Pagina degli elementi del catalogo di CSS](flex-layout-images/CssCatalogItems.png "CSS pagina degli elementi del catalogo")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

Originale **CatalogItemsPage.xaml** file dispone di cinque `Style` definizioni nel relativo `Resources` sezione con 15 `Setter` oggetti. Nel **CssCatalogItemsPage.xaml** file, che è stato ridotto a due `Style` definizioni con solo quattro `Setter` oggetti. Questi stili di integrano il foglio di stile CSS per le proprietà che non supporta attualmente la funzionalità di applicazione di stili CSS di xamarin. Forms:

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

Il foglio di stile CSS fa riferimento la prima riga del `Resources` sezione:

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Si noti anche che due elementi in ognuna delle tre elementi includono `StyleClass` impostazioni:

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

Questi elementi fanno riferimento a selettori nel **CatalogItemsStyles.css** foglio di stile:

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

Diversi `FlexLayout` proprietà associabili collegate fanno riferimento qui. Nel `label.empty` selettore, si noterà il `flex-grow` attributo, che consente di disegnare un oggetto vuoto `Label` per fornire spazio vuoto sopra il `Button`. Il `image` selettore contiene un `order` attributo e un `align-self` attributo, che corrispondono a `FlexLayout` proprietà associabili associate.

Si è visto che è possibile impostare proprietà direttamente sul `FlexLayout` ed è possibile impostare le proprietà associabili associate sugli elementi figlio di un `FlexLayout`. In alternativa, è possibile impostare queste proprietà usando indirettamente tradizionale basato su XAML o stili CSS. È importante conoscere e comprendere queste proprietà. Queste proprietà sono ciò che rende il `FlexLayout` davvero flessibile.

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout con Xamarin.University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Video Novell. Forms 3,0 Flex layout**

## <a name="related-links"></a>Collegamenti correlati

- [FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)
