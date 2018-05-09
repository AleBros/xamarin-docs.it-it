---
title: Il FlexLayout xamarin. Forms
description: Utilizzare FlexLayout per la sovrapposizione o il wrapping di una raccolta di visualizzazioni figlio.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 05/01/2018
ms.openlocfilehash: 4aa2ea21c9cf2e9e646465ab7ad4aa0a01de433e
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="the-xamarinforms-flexlayout"></a>Il FlexLayout xamarin. Forms

_Utilizzare FlexLayout per la sovrapposizione o il wrapping di una raccolta di visualizzazioni figlio._

Il xamarin. Forms [ `FlexLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexLayout/) è stata introdotta in xamarin. Forms versione 3.0. È basato sul foglio di stile CSS [modulo di Layout casella flessibile](http://www.w3.org/TR/css-flexbox-1/), comunemente noti come _flex layout_ o _flex-box_, cosiddetti perché include numerose opzioni flessibili per disporre gli elementi figlio all'interno del layout.

`FlexLayout` è simile a di xamarin. Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md) in quanto è possibile disporre gli elementi figlio orizzontalmente e verticalmente in uno stack. Tuttavia, il `FlexLayout` è inoltre in grado di disposizione testo per gli elementi figlio se sono presenti troppi per essere contenuta in una singola riga o colonna, e dispone anche di numerose opzioni per l'orientamento allineamento e adattamento per diverse dimensioni dello schermo.

`FlexLayout` deriva da [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) ed eredita un [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) proprietà di tipo `IList<View>`.

`FlexLayout` Definisce proprietà associabile pubbliche sei e cinque proprietà associabile associate. (Se non si ha familiarità con le proprietà associabili associata, vedere l'articolo  **[proprietà associate](~/xamarin-forms/xaml/attached-properties.md)**.) Tutte queste proprietà sono descritte in dettaglio nelle sezioni riportate di seguito nella **[le proprietà associabili ai sei](#bindable-properties)** e **[i cinque proprietà associabile associate](#attached-properties)**. Tuttavia, questo articolo inizia con una sezione in alcune **[applicazioni comuni](#common-applications)** di `FlexLayout` che descrive molte di queste proprietà in modo più informale. Verso la fine dell'articolo, si noterà come combinare `FlexLayout` con [fogli di stile CSS](~/xamarin-forms/user-interface/styles/css/index.md).

<a name="common-applications" />

## <a name="common-applications"></a>Applicazioni comuni

Il **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** programma di esempio contiene più pagine tale illustrare alcuni usi della `FlexLayout` e consente di sperimentare le relative proprietà.

### <a name="using-flexlayout-for-a-simple-stack"></a>Utilizzo di FlexLayout per uno stack semplice

Il **semplice dello Stack** pagina vengono visualizzati come `FlexLayout` puoi sostituire a un `StackLayout` ma con markup più semplice. Tutti gli elementi in questo esempio viene definito nella pagina XAML. Il `FlexLayout` contiene quattro elementi figlio:

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

Di seguito è la pagina eseguiti su iOS, Android e la piattaforma Windows universale:

[![Il semplice dello Stack pagina](flex-layout-images/SimpleStack.png "il semplice dello Stack pagina")](flex-layout-images/SimpleStack-Large.png#lightbox)

Tre proprietà di `FlexLayout` vengono visualizzati nel **SimpleStackPage.xaml** file:

- Il [ `Direction` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.Direction/) è impostata su un valore di [ `FlexDirection` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexDirection/) enumerazione. Il valore predefinito è `Row`. Impostare la proprietà su `Column` fa sì che gli elementi figlio del `FlexLayout` devono essere disposti in una singola colonna di elementi.

    Quando gli elementi in un `FlexLayout` vengono disposte in una colonna, il `FlexLayout` presenta una verticale _asse principale_ e orizzontale _incrociato asse_.

- Il [ `AlignItems` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.AlignItems/) proprietà è di tipo [ `FlexAlignItems` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexAlignItems/) e specifica come gli elementi vengono allineate lungo l'asse incrociato. Il `Center` opzione fa sì che ogni elemento da allineare al centro orizzontalmente.

    Se si utilizza un `StackLayout` anziché da un `FlexLayout` per questa attività, è necessario allineare al centro tutti gli elementi assegnando il `HorizontalOptions` proprietà di ogni elemento da `Center`. Il `HorizontalOptions` proprietà non funziona per gli elementi figlio di un `FlexLayout`, ma questo singolo `AlignItems` proprietà consente di raggiungere lo stesso obiettivo. Se è necessario, è possibile usare il `AlignSelf` collegato proprietà associabile per eseguire l'override di `AlignItems` proprietà per i singoli elementi:

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    Questa modifica, il presente `Label` è posizionato il bordo sinistro del `FlexLayout` quando l'ordine di lettura è da sinistra a destra.

- Il [ `JustifyContent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.JustifyContent/) proprietà è di tipo [ `FlexJustify` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexJustify/)e specifica come vengono disposti sull'asse principale. Il `SpaceEvenly` opzione alloca tutte spazio verticale rimasto equamente tra tutti gli elementi e sopra il primo elemento e sotto l'ultimo elemento.

    Se si utilizza un `StackLayout`, è necessario assegnare il `VerticalOptions` proprietà di ogni elemento da `CenterAndExpand` per ottenere un effetto simile. Ma la `CenterAndExpand` opzione potrebbe allocare due volte come quantità di spazio tra ogni elemento rispetto a prima del primo e dopo l'ultimo elemento. È possibile simulare il `CenterAndExpand` opzione di `VerticalOptions` impostando il `JustifyContent` proprietà del `FlexLayout` a `SpaceAround`.

Questi `FlexLayout` proprietà sono descritte più dettagliatamente nella sezione **[le proprietà associabili ai sei](#bindable-properties)** sotto.

### <a name="using-flexlayout-for-wrapping-items"></a>Utilizzo FlexLayout per eseguire il wrapping elementi

Il **di wrapping delle foto** pagina del **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** esempio viene illustrato come `FlexLayout` possibile eseguire il wrapping dei relativi figli righe o colonne aggiuntive. Il file XAML crea un'istanza di `FlexLayout` e assegna due proprietà:

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

Il `Direction` proprietà di questo `FlexLayout` non è impostata in modo che abbia l'impostazione predefinita `Row`, vale a dire che gli elementi figlio vengono disposti in righe e dell'asse principale è orizzontale.

Il [ `Wrap` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.Wrap/) la proprietà è di un tipo di enumerazione [ `FlexWrap` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexWrap/). Se sono presenti troppi elementi in base a una riga, questa impostazione di proprietà, gli elementi eseguire il wrapping alla riga successiva.

Si noti che il `FlexLayout` è un figlio di un `ScrollView`. Se sono presenti troppe righe per pagina, il `ScrollView` ha un valore predefinito `Orientation` proprietà di `Vertical` e consente di scorrimento verticale.

Il `JustifyContent` proprietà alloca spazio rimasto sull'asse principale (l'asse orizzontale) in modo che ogni elemento è delimitato della stessa quantità di spazio vuoto.

Il file code-behind accede a una raccolta di foto di esempio e li aggiunge ai `Children` insieme il `FlexLayout`:

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
        int imageDimension = Device.RuntimePlatform == Device.iOS ||
                             Device.RuntimePlatform == Device.Android ? 240 : 120;

        string urlSuffix = String.Format("?width={0}&height={0}&mode=max", imageDimension);

        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("http://docs.xamarin.com/demo/stock.json");
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
                            Source = ImageSource.FromUri(new Uri(filepath + urlSuffix))
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

Ecco il programma in esecuzione in tre piattaforme, progressivamente supporta lo scorrimento dall'alto verso il basso:

[![La pagina di ritorno a capo foto](flex-layout-images/PhotoWrapping.png "la pagina di ritorno a capo foto")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="holy-grail-layout-with-flexlayout"></a>Santa Sede Graal layout con FlexLayout

Nella progettazione web chiamato è un layout standard di [ _Graal Santa Sede_ ](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) perché è un formato di layout che è molto utile, ma spesso difficili da comprendere con perfezione. Il layout è costituito da un'intestazione nella parte superiore della pagina e un piè di pagina nella parte inferiore, entrambi si estende fino alla larghezza massima della pagina. Che occupano il centro della pagina corrisponda a quello principale, ma spesso con un menu a sinistra del contenuto e le informazioni supplementari a colonne (detto anche un _riservato_ area) a destra. [Sezione 5.4.1 della specifica del Layout casella flessibile CSS](http://www.w3.org/TR/css-flexbox-1/#order-accessibility) viene descritto come il layout Graal Santa sede può essere realizzato con una casella di flessibilità.

Il **Santa Sede Graal Layout** pagina del **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** esempio illustrata un'implementazione semplice di questo layout mediante uno `FlexLayout` nidificato in un altro. Poiché questa pagina è progettata per un dispositivo phone in modalità verticale, le aree a sinistra e a destra dell'area di contenuto sono solo 50 pixel in larghezza:

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

Qui è in esecuzione su tre piattaforme:

[![La pagina di Layout Graal Santa Sede](flex-layout-images/HolyGrailLayout.png "la pagina di Layout Graal Santa sede")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

Le aree di navigazione e aside eseguono il rendering di un `BoxView` a sinistra e destra.

Il primo `FlexLayout` nel codice XAML file ha un asse principale verticale e contiene tre membri figlio disposti in una colonna. Si tratta l'intestazione, il corpo della pagina e piè di pagina. Nidificata `FlexLayout` dispone di un asse principale orizzontale con tre membri figlio disposti in una riga.

Tre proprietà associabili associate sono illustrate in questo programma:

- Il `Order` proprietà associabile associata è impostata sul primo `BoxView`. Questa proprietà è di tipo integer e un valore predefinito pari a 0. È possibile utilizzare questa proprietà per modificare l'ordine di layout. In genere gli sviluppatori preferiscono il contenuto della pagina da visualizzare nel markup prima gli elementi di navigazione e adattata gli elementi. Impostazione di `Order` proprietà sul primo `BoxView` su un valore minore di elementi di altri pari livello fa sì che venga visualizzato come primo elemento nella riga. Analogamente, è possibile garantire che un elemento viene visualizzato per ultimo impostando il `Order` proprietà su un valore maggiore di elementi di pari livello.

- Il `Basis` proprietà associabile associata è impostata su due `BoxView` elementi nell'associarvi una larghezza di 50 pixel. Questa proprietà è di tipo `FlexBasis`, una struttura che definisce una proprietà statica di tipo `FlexBasis` denominato `Auto`, ovvero l'impostazione predefinita. È possibile utilizzare `Basis` per specificare una dimensione in pixel o percentuale che indica la quantità di spazio occupata dall'elemento sull'asse principale. Viene chiamato un _base_ perché specifica una dimensione di elemento che rappresenta la base del layout tutte le successive.

- Il `Grow` è impostata su nidificata `Layout` e scegliere il `Label` figlio che rappresenta il contenuto. Questa proprietà è di tipo `float` e ha un valore predefinito pari a 0. Se impostato su un valore positivo, tutto lo spazio rimanente lungo l'asse principale viene allocato a tale elemento e a elementi di pari livello con i valori positivi di `Grow`. Lo spazio viene allocato in modo proporzionale i valori, in modo simile specifica di stella in un `Grid`.

    Il primo `Grow` proprietà associata è impostata su nidificata `FlexLayout`, a indicare che questo `FlexLayout` deve occupare tutto lo spazio verticale non utilizzato all'interno di esterna `FlexLayout`. Il secondo `Grow` proprietà associata è impostata sul `Label` che rappresenta il contenuto, che indica che il contenuto deve occupare tutto lo spazio orizzontale inutilizzato all'interno di interna `FlexLayout`.

    È inoltre disponibile un simile `Shrink` proprietà associabile che è possibile utilizzare quando le dimensioni dei figli superano le dimensioni di associata la `FlexLayout` ma non si desidera ritorno a capo.

### <a name="catalog-items-with-flexlayout"></a>Elementi del catalogo con FlexLayout

Il **gli elementi del catalogo** nella pagina di **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** è simile all'esempio [1 riportato nella sezione 1.1 della specifica CSS Flex Layout finestra](http://www.w3.org/TR/css-flexbox-1/#overview)ad eccezione del fatto che consente di visualizzare una serie di immagini scorrevole orizzontalmente e le descrizioni delle tre scimmie:

[![Il catalogo degli elementi](flex-layout-images/CatalogItems.png "il catalogo degli elementi")](flex-layout-images/CatalogItems-Large.png#lightbox)

Ogni tre scimmie è un `FlexLayout` contenuto in un `Frame` che viene assegnato un esplicita altezza e la larghezza, e che è anche un elemento figlio di un maggiore `FlexLayout`. In questo file XAML, la maggior parte delle proprietà del `FlexLayout` elementi figlio vengono specificati negli stili tutti tranne uno dei quali è uno stile implicito:

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

Lo stile implicito per il `Image` include le impostazioni di due proprietà associabile associate di `Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

Il `Order` impostazione di &ndash;1 causa la `Image` elemento da visualizzare prima di tutto in ognuna delle nidificata `FlexLayout` viste indipendentemente dalla relativa posizione all'interno della raccolta di elementi figlio. Il `AlignSelf` proprietà di `Center` fa sì che il `Image` centrato all'interno di `FlexLayout`. Esegue l'override dell'impostazione della `AlignItems` proprietà, che ha un valore predefinito di `Stretch`, ovvero che il `Label` e `Button` gli elementi figlio sono estesa per l'intera larghezza del `FlexLayout`.

All'interno di ognuno dei tre `FlexLayout` viste, uno spazio vuoto `Label` precede il `Button`, ma ha un `Grow` impostazione pari a 1. Ciò significa che tutti i spae aggiuntivi verticale è allocata a questo vuoto `Label`, che inserisce in modo efficace il `Button` verso il basso.

<a name="bindable-properties" />

## <a name="the-six-bindable-properties"></a>Le proprietà associabili ai sei

Ora che si sono visto alcune applicazioni comuni della `FlexLayout`, le proprietà di `FlexLayout` possono essere esplorato in modo più dettagliato. 
`FlexLayout` definisce sei proprietà associabile impostato per il `FlexLayout` stessa, nel codice o XAML, ma la `Position` proprietà non viene descritta in questo articolo.

È possibile provare oggi cinque rimanenti proprietà associabili usando il **sperimentare** pagina del **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** esempio. Questa pagina consente di aggiungere o rimuovere gli elementi figlio da un `FlexLayout` e impostare combinazioni di cinque proprietà associabile. Tutti gli elementi figlio del `FlexLayout` sono `Label` viste di diversi colori e le dimensioni, con il `Text` impostata su un numero corrispondente nella relativa posizione nel `Children` insieme.

Quando il programma di avvio, cinque `Picker` i valori predefiniti di questi cinque visualizzazioni `FlexLayout` proprietà. Il `FlexLayout` nella parte inferiore della schermata contiene tre elementi figlio:

[![La pagina esperimento: Default](flex-layout-images/ExperimentDefault.png "pagina esperimento - Default")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Ognuna del `Label` viste sono presenti uno sfondo grigio che mostra lo spazio allocato a quella `Label` all'interno di `FlexLayout`. Lo sfondo del `FlexLayout` stesso è Alice blu. Occupa l'area intera parte inferiore della pagina, ad eccezione di un piccolo margine a sinistra e a destra.

<a name="direction" />

### <a name="the-direction-property"></a>La proprietà direzione

Il [ `Direction` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.Direction/) proprietà è di tipo [ `FlexDirection` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexDirection/), un'enumerazione con quattro membri:

- `Column`
- `ColumnReverse` (o "colonna in ordine inverso" in XAML)
- `Row`, il valore predefinito
- `RowReverse` (o "righe in ordine inverso" in XAML)

In XAML, è possibile specificare il valore di questa proprietà utilizzando i nomi dei membri di enumerazione in caratteri minuscoli, maiuscoli, o caratteri maiuscoli e minuscoli, oppure è possibile utilizzare due stringhe aggiuntive indicate tra parentesi sono le stesse gli indicatori CSS. (Le stringhe "colonna-reverse" e "riga-reverse" sono definite nel [ `FlexDirectionTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexDirectionTypeConverter/) classe utilizzata dal parser XAML.)

Ecco il **esperimento** pagina visualizzata (da sinistra a destra), la `Row` direzione, `Column` direzione, e `ColumnReverse` direzione:

[![La pagina esperimento: Direzione](flex-layout-images/ExperimentDirection.png "pagina esperimento - direzione")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Si noti che per il `Reverse` opzioni, gli elementi iniziano da destra o a sinistra.

<a name="wrap" />

### <a name="the-wrap-property"></a>La proprietà Wrap

Il [ `Wrap` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.Wrap/) proprietà è di tipo [ `FlexWrap` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexWrap/), un'enumerazione con tre membri:

- `NoWrap`, il valore predefinito
- `Wrap`
- `Reverse` (o "incapsulamento-reverse" in XAML)

Da sinistra a destra, visualizzare queste schermate vengono visualizzate le `NoWrap`, `Wrap` e `Reverse` opzioni per gli elementi 12 figlio:

[![La pagina esperimento: Eseguire il wrapping](flex-layout-images/ExperimentWrap.png "pagina esperimento - Wrap")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Quando il `Wrap` è impostata su `NoWrap` dell'asse principale è vincolato (come in questo programma) e dell'asse principale non è "wide" o aumentare sufficientemente l'altezza in base a tutti gli elementi figlio, il `FlexLayout` tenta di rendere gli elementi più piccoli, come la schermata di iOS viene illustrato. È possibile controllare il shrinkness degli elementi con il [ `Shrink` ](#shrink) proprietà associabile associata.

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>La proprietà JustifyContent

Il [ `JustifyContent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.JustifyContent/) proprietà è di tipo [ `FlexJustify` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexJustify/), un'enumerazione con sei membri:

- `Start` (o "flex-start" in XAML), il valore predefinito
- `Center`
- `End` (o "flex-end" in XAML)
- `SpaceBetween` (o "spazio tra" in XAML)
- `SpaceAround` (o "spazio-around" in XAML)
- `SpaceEvenly`

Questa proprietà specifica come gli elementi sono spaziati sull'asse principale, ovvero l'asse orizzontale in questo esempio:

[![La pagina esperimento: Giustificare contenuto](flex-layout-images/ExperimentJustifyContent.png "pagina esperimento - Justify contenuto")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

In tutti e tre le schermate, la `Wrap` è impostata su `Wrap`. Il `Start` predefinito è illustrato nella schermata di Android precedente. Nella schermata di iOS di seguito viene illustrato il `Center` opzione: tutti gli elementi vengono spostati al centro. Le tre opzioni che iniziano con la parola `Space` allocare lo spazio non occupato da elementi. `SpaceBetween` alloca lo spazio in modo uniforme tra gli elementi; `SpaceAround` inserisce uguale spazio intorno a ogni elemento, mentre `SpaceEvenly` inserisce uguale spazio tra ogni elemento e prima del primo e dopo l'ultimo elemento nella riga.

<a name="align-items" />

### <a name="the-alignitems-property"></a>La proprietà AlignItems

Il [ `AlignItems` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.AlignItems/) proprietà è di tipo [ `FlexAlignItems` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexAlignItems/), un'enumerazione con quattro membri:

- `Stretch`, il valore predefinito
- `Center`
- `Start` (o "flex-start" in XAML)
- `End` (o "flex-end" in XAML)

Si tratta di uno di due proprietà (l'altro che [ `AlignContent` ](#align-content)) che indica come elementi figlio vengono allineate lungo l'asse incrociato. In ogni riga, gli elementi figlio sono estesa (come illustrato nella schermata precedente) o allineati a start, center o fine di ogni elemento, come illustrato nelle tre schermate seguenti:

[![La pagina esperimento: Allineare gli elementi](flex-layout-images/ExperimentAlignItems.png "pagina esperimento - allineare gli elementi")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

Nella schermata iOS sono allineate le parti superiori di tutti gli elementi figlio. Nelle schermate Android, gli elementi sono centrati verticalmente in base a quello più alto. Nella schermata UWP è allineato in basso di tutti gli elementi.

Per un singolo elemento, il `AlignItems` impostazione può essere sostituita con il [ `AlignSelf` ](#align-self) proprietà associabile associata.

<a name="align-content" />

### <a name="the-aligncontent-property"></a>La proprietà AlignContent

Il [ `AlignContent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.AlignContent/) proprietà è di tipo [ `FlexAlignContent` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexAlignContent/), un'enumerazione con sette membri:

- `Stretch`, il valore predefinito
- `Center`
- `Start` (o "flex-start" in XAML)
- `End` (o "flex-end" in XAML)
- `SpaceBetween` (o "spazio tra" in XAML)
- `SpaceAround` (o "spazio-around" in XAML)
- `SpaceEvenly`

Ad esempio `AlignItems`, il `AlignContent` proprietà anche Allinea elementi figlio nell'asse incrociato, ma influisce sulle righe o colonne intere:

[![La pagina esperimento: Allineamento del contenuto](flex-layout-images/ExperimentAlignContent.png "pagina esperimento - Allinea contenuto")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

In screnshot iOS, entrambe le righe sono in alto. Nella schermata Android in cui si trovano al centro; e nella schermata UWP sono nella parte inferiore. Le righe possono anche essere spaziate in vari modi:

[![La pagina esperimento: Allinea contenuto 2](flex-layout-images/ExperimentAlignContent2.png "pagina esperimento - Allinea contenuto 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

Il `AlignContent` non ha alcun effetto quando si verifica solo una riga o colonna.

<a name="attached-properties" />

## <a name="the-five-attached-bindable-properties"></a>I cinque proprietà associabile associate

`FlexLayout` definisce le proprietà associabili associate cinque. Queste proprietà vengono impostate negli elementi figlio del `FlexLayout` e si riferiscono solo a quel particolare figlio.

<a name="align-self" />

### <a name="the-alignself-property"></a>La proprietà AlignSelf

Il [ `AlignSelf` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.AlignSelf/) proprietà associabile associata è di tipo [ `FlexAlignSelf` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlexAlignContent/), un'enumerazione con cinque membri:

- `Auto`, il valore predefinito
- `Stretch`
- `Center`
- `Start` (o "flex-start" in XAML)
- `End` (o "flex-end" in XAML)

Per qualsiasi singolo elemento figlio del `FlexLayout`, questa proprietà di impostazione di sostituzioni di [ `AlignItems` ](#align-items) impostata sul `FlexLayout` stesso. L'impostazione predefinita `Auto` indica l'uso di `AlignItems` impostazione.

Per un `Label` elemento denominato `label` (o esempio), è possibile impostare il `AlignSelf` proprietà nel codice simile al seguente:

```csharp
FlexAlign.SetAlignSelf(label, FlexAlignSelf.Center);
```

Si noti che non esiste alcun riferimento ai `FlexLayout` padre il `Label`. In XAML, impostare la proprietà simile al seguente:

```xaml
<Label ... FlexAlign.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>La proprietà Order

Il [ `Order` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.Order/) proprietà è di tipo `int`. Il valore predefinito è 0.

Il `Order` proprietà consente di modificare l'ordine che gli elementi figlio del `FlexLayout` disposte. In genere, gli elementi figlio di un `FlexLayout` disposte nello stesso ordine in cui appaiono nella `Children` insieme. È possibile eseguire l'override di questo ordine impostando il `Order` proprietà associabile associata a un valore intero diverso da zero in uno o più elementi figlio. Il `FlexLayout` quindi lo dispone gli elementi figlio in base all'impostazione del `Order` proprietà su ogni elemento figlio, ma gli elementi figlio con lo stesso `Order` impostazione sono disposti nell'ordine in cui appaiono nella `Children` insieme.

### <a name="the-basis-property"></a>La proprietà di base

Il [ `Basis` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.Basis/) proprietà associabile associata indica la quantità di spazio allocata a un elemento figlio del `FlexLayout` sull'asse principale. Il completamento di dimensioni per il `Basis` proprietà corrisponde alla dimensione lungo l'asse principale dell'elemento padre `FlexLayout`. In altre parole, `Basis` indica la larghezza di un controllo figlio quando gli elementi figlio vengono disposti in righe o l'altezza quando gli elementi figlio vengono disposti in colonne.

Il `Basis` proprietà è di tipo [ `FlexBasis` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexBasis/), una struttura. Le dimensioni possono essere specificate in unità indipendenti dal dispositivo o come percentuale delle dimensioni del `FlexLayout`. Il valore predefinito di `Basis` è la proprietà statica `FlexBasis.Auto`, il che significa che l'elemento figlio della richiesta larghezza o altezza viene utilizzato.

Nel codice, è possibile impostare il `Basis` proprietà per un `Label` denominata `label` a 40 device independent unit, simile al seguente:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

Il secondo argomento per il `FlexBasis` costruttore è denominato `isRelative` e indica se la dimensione è relativa (`true`) o assoluto (`false`). L'argomento ha un valore predefinito di `false`, pertanto è anche possibile utilizzare il codice seguente:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Una conversione implicita da `float` a `FlexBasis` è definito, pertanto è possibile semplificare ulteriormente:

```csharp
FlexLayout.SetBasis(label, 40);
```

È possibile impostare le dimensioni al 25% del `FlexLayout` padre simile al seguente:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Questo valore frazionario deve essere compreso nell'intervallo tra 0 e 1.

In XAML, è possibile utilizzare un numero per una dimensione in unità indipendenti dal dispositivo:

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

Oppure è possibile specificare una percentuale dell'intervallo di 0% e 100%:

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

Il **base sperimentare** pagina del **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** esempio consente di sperimentare il `Basis` proprietà. La pagina viene visualizzata una colonna di cui effettua il wrapping di cinque `Label` gli elementi con alternanza dei colori di sfondo e primo piano. Due `Slider` elementi consentono di specificare `Basis` i valori per la seconda e la quarta `Label`:

[![La base per la pagina di sperimentare](flex-layout-images/BasisExperiment.png "base sperimentare pagina")](flex-layout-images/BasisExperiment-Large.png#lightbox)

La schermata di iOS a sinistra mostra due `Label` elementi specifico viene assegnati altezze in unità indipendenti dal dispositivo. Schermata di Android Mostra loro viene fornito l'altezza delle righe che sono una frazione dell'altezza del totale del `FlexLayout`. Se il `Basis` è impostato su 100%, l'elemento figlio è l'altezza del `FlexLayout`e a capo nella colonna successiva e occupare l'intera cornice della colonna, come illustrato nella schermata di piattaforma UWP: viene visualizzato come se gli elementi cinque figlio vengono disposte in una riga , ma in realtà sono disposti in cinque colonne.

### <a name="the-grow-property"></a>La proprietà di aumento delle dimensioni

Il [ `Grow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.Grow/) proprietà è di tipo `int`. Il valore predefinito è 0 e il valore deve essere maggiore o uguale a 0.

Il `Grow` proprietà svolge un ruolo quando il `Wrap` è impostata su `NoWrap` e la riga di elementi figlio ha una larghezza totale minore di quella della `FlexLayout`, o la colonna di elementi figlio ha un'altezza inferiore rispetto al `FlexLayout`. Il `Grow` proprietà indica come eseguire la ripartizione di spazi vuoti tra gli elementi figlio.

Nel **esperimento di aumento delle dimensioni** pagina cinque `Label` elementi di colori alternati vengono disposte in una colonna e due `Slider` elementi consentono di regolare il `Grow` proprietà del secondo e il quarto `Label`. La schermata di iOS all'estrema sinistra mostra il valore predefinito `Grow` proprietà pari a 0:

[![La pagina di sperimentazione Grow](flex-layout-images/GrowExperiment.png "pagina esperimento Grow")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Se qualsiasi un elemento figlio viene assegnato un numero positivo `Grow` valore, tale elemento figlio occupa tutto lo spazio rimanente, come illustrato nella schermata di Android. Questo spazio può essere allocato anche tra due o più elementi figlio. Nella schermata UWP, il `Grow` proprietà del secondo `Label` è impostata su 0,5, mentre il `Grow` proprietà del quarto `Label` è 1.5, che fornisce il quarto `Label` tre volte maggiore di spazi vuoti come il secondo `Label`.

Utilizzo della visualizzazione figlio di tale spazio dipende dal tipo specifico dell'elemento figlio. Per un `Label`, il testo può essere posizionato all'interno dello spazio totale del `Label` utilizzando la proprietà `HorizontalTextAlignment` e `VerticalTextAlignment`.

<a name="shrink" />

### <a name="the-shrink-property"></a>La proprietà di compattazione

Il [ `Shrink` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FlexLayout.Shrink/) proprietà è di tipo `int`. Il valore predefinito è 1 e il valore deve essere maggiore o uguale a 0.

Il `Shrink` proprietà svolge un ruolo quando il `Wrap` è impostata su `NoWrap` e la larghezza di aggregazione di una riga di elementi figlio è maggiore della larghezza del `FlexLayout`, o l'altezza di aggregazione di una singola colonna di elementi figlio è maggiore di altezza di `FlexLayout`. In genere il `FlexLayout` visualizzerà queste elementi figlio da constricting le rispettive dimensioni. Il `Shrink` proprietà può indicare quali elementi figlio è prioritari in venga visualizzata nuovamente nelle rispettive dimensioni complete.

Il **compattare esperimento** pagina Crea un `FlexLayout` con una singola riga di cinque `Label` gli oggetti figlio che richiedono più spazio rispetto al `FlexLayout` larghezza. La schermata di iOS a sinistra mostra tutti i `Label` elementi con i valori predefiniti pari a 1:

[![Pagina di sperimentare la compattazione](flex-layout-images/ShrinkExperiment.png "la compattazione sperimentare pagina")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

Nella schermata Android, il `Shrink` valore per il secondo `Label` è impostata su 0, e che `Label` viene visualizzato nella larghezza intera. Inoltre, il quarto `Label` viene assegnato un `Shrink` valore maggiore di uno e è compattato. La schermata UWP Mostra entrambi `Label` elementi specifico viene assegnati un `Shrink` pari a 0 in modo che possano essere visualizzati in alle dimensioni intere, se tale valore è possibile.

È possibile impostare entrambe le `Grow` e `Shrink` i valori per contenere situazioni in cui le dimensioni di aggregazione figlio in alcuni casi potrebbero essere minore o a volte maggiore rispetto alla dimensione del `FlexLayout`.

## <a name="css-styling-with-flexlayout"></a>Applicazione degli stili CSS con FlexLayout

È possibile usare il [dello stile CSS](~/xamarin-forms/user-interface/styles/css/index.md) funzionalità introdotta con xamarin. Forms 3.0 in relazione a `FlexLayout`. Il **gli elementi del catalogo CSS** pagina del **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** esempio coincide con il layout del **gli elementi del catalogo** pagina, ma con un CSS foglio di stile per la maggior parte degli stili:

[![Pagina degli elementi del catalogo di foglio di stile CSS](flex-layout-images/CssCatalogItems.png "pagina degli elementi del catalogo di foglio di stile CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

Originale **CatalogItemsPage.xaml** file dispone di cinque `Style` definizioni nel relativo `Resources` sezione con 15 `Setter` oggetti. Nel **CssCatalogItemsPage.xaml** file, che è stata ridotta a due `Style` definizioni con solo quattro `Setter` oggetti. Questi stili di integrano il foglio di stile CSS per le proprietà che non può gestire la funzionalità di applicazione di stili CSS xamarin. Forms attualmente:

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

Si noti inoltre che due elementi in ciascuno dei tre elementi includono `StyleClass` impostazioni:

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

Diversi `FlexLayout` proprietà associabile associate viene fatto riferimento qui. Nel `label.empty` selettore, verrà visualizzato il `flex-grow` attributo stili vuota `Label` per fornire spazio vuoto sopra il `Button`. Il `image` selettore contiene un `order` attributo e un `align-self` attributo, che corrispondono a `FlexLayout` proprietà associabile associate.

Si è visto che è possibile impostare proprietà direttamente nel `FlexLayout` ed è possibile impostare le proprietà associabili associate sugli elementi figlio di un `FlexLayout`. In alternativa, è possibile impostare queste proprietà indirettamente utilizzando tradizionali basate su XAML o stili CSS. La cosa importante è noto e comprendere queste proprietà. Sono il modo in cui il `FlexLayout` realmente flessibile. 

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout con Xamarin.University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin. Forms 3.0 flessibilità di Layout, [University Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Collegamenti correlati

- [FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)
