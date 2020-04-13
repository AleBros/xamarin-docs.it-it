---
title: Riquadro a comparsa della shell Xamarin.Forms
description: Il riquadro a comparsa è il menu radice per un'applicazione shell ed è accessibile attraverso un'icona o tramite scorrimento rapido dal lato dello schermo. Il riquadro a comparsa è costituito da un'intestazione facoltativa, elementi del riquadro a comparsa e voci di menu facoltative.
ms.prod: xamarin
ms.assetid: FEDE51EB-577E-4B3E-9890-B7C1A5E52516
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2019
ms.openlocfilehash: 4049b3bdfdd6077dcfa151df9553722e63def0ba
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305171"
---
# <a name="xamarinforms-shell-flyout"></a>Riquadro a comparsa della shell Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Il riquadro a comparsa è il menu radice per un'applicazione shell ed è accessibile attraverso un'icona o tramite scorrimento rapido dal lato dello schermo. Il riquadro a comparsa è costituito da un'intestazione facoltativa, elementi del riquadro a comparsa e voci di menu facoltative:

![Screenshot di un riquadro a comparsa annotato shell](flyout-images/flyout-annotated.png "Riquadro a comparsa annotato")

Se necessario, il colore di sfondo del [`Color`](xref:Xamarin.Forms.Color) riquadro `Shell.FlyoutBackgroundColor` a comparsa può essere impostato su un tramite la proprietà bindable. Questa proprietà può anche essere impostata da un foglio di stile CSS (Cascading Style Sheet). Per altre informazioni, vedere [Xamarin.Forms Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell Xamarin.Forms).

## <a name="flyout-icon"></a>Icona del riquadro a comparsa

Per impostazione predefinita, le applicazioni shell hanno un'icona hamburger che è possibile premere per aprire il riquadro a comparsa. Questa icona può essere modificata impostando la `Shell.FlyoutIcon` [`ImageSource`](xref:Xamarin.Forms.ImageSource)proprietà associabile, di tipo , su un'icona appropriata:

```xaml
<Shell ...
       FlyoutIcon="flyouticon.png">
    ...       
</Shell>
```

## <a name="flyout-behavior"></a>Comportamento del riquadro a comparsa

È possibile accedere al riquadro a comparsa attraverso l'icona hamburger o tramite scorrimento rapido dal lato dello schermo. Questo comportamento può essere tuttavia modificato impostando la proprietà associata `Shell.FlyoutBehavior` su uno dei membri dell'enumerazione `FlyoutBehavior`:

- `Disabled`: indica che il riquadro a comparsa non può essere aperto dall'utente.
- `Flyout`: indica che il riquadro a comparsa può essere aperto e chiuso dall'utente. Questo è il valore predefinito per la proprietà `FlyoutBehavior`.
- `Locked`: indica che il riquadro a comparsa non può essere chiuso dall'utente e che non si sovrappone al contenuto.

L'esempio seguente mostra come disabilitare il riquadro a comparsa:

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

> [!NOTE]
> La proprietà associata `FlyoutBehavior` può essere impostata su `Shell`, `FlyoutItem`, `ShellContent` e sugli oggetti della pagina, per sostituire il comportamento predefinito del riquadro a comparsa.

Il riquadro a comparsa può inoltre essere aperto e chiuso a livello di codice impostando la proprietà associabile `Shell.FlyoutIsPresented` su un valore `boolean` che indica se il riquadro a comparsa è attualmente visibile:

```csharp
Shell.Current.FlyoutIsPresented = false;
```

## <a name="flyout-header"></a>Intestazione del riquadro a comparsa

L'intestazione del riquadro a comparsa è il contenuto che facoltativamente viene visualizzato nella parte superiore del riquadro e il cui aspetto viene definito da un elemento `object` che può essere impostato tramite il valore della proprietà `Shell.FlyoutHeader`:

```xaml
<Shell.FlyoutHeader>
    <controls:FlyoutHeader />
</Shell.FlyoutHeader>
```

Il tipo `FlyoutHeader` è illustrato nell'esempio seguente:

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Xaminals.Controls.FlyoutHeader"
             HeightRequest="200">
    <Grid BackgroundColor="Black">
        <Image Aspect="AspectFill"
               Source="xamarinstore.jpg"
               Opacity="0.6" />
        <Label Text="Animals"
               TextColor="White"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentView>
```

Il risultato è l'intestazione del riquadro a comparsa seguente:

![Screenshot dell'intestazione del riquadro a comparsa](flyout-images/flyout-header.png "Intestazione del riquadro a comparsa")

In alternativa, è possibile definire l'aspetto `Shell.FlyoutHeaderTemplate` dell'intestazione del riquadro a comparsa impostando la proprietà su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<Shell.FlyoutHeaderTemplate>
    <DataTemplate>
        <Grid BackgroundColor="Black"
              HeightRequest="200">
            <Image Aspect="AspectFill"
                   Source="xamarinstore.jpg"
                   Opacity="0.6" />
            <Label Text="Animals"
                   TextColor="White"
                   FontAttributes="Bold"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center" />
        </Grid>            
    </DataTemplate>
</Shell.FlyoutHeaderTemplate>
```

Per impostazione predefinita, l'intestazione del riquadro a comparsa è fissa nel riquadro mentre il contenuto sottostante scorre se sono presenti elementi a sufficienza. Questo comportamento, tuttavia, può essere modificato impostando la proprietà associabile `Shell.FlyoutHeaderBehavior` su uno dei membri dell'enumerazione `FlyoutHeaderBehavior`:

- `Default`: indica che verrà usato il comportamento predefinito per la piattaforma. Questo è il valore predefinito per la proprietà `FlyoutHeaderBehavior`.
- `Fixed`: indica che l'intestazione del riquadro a comparsa rimane sempre visibile e non cambia.
- `Scroll`: indica che l'intestazione del riquadro a comparsa scorre uscendo dalla visualizzazione quando l'utente scorre gli elementi.
- `CollapseOnScroll`: indica che l'intestazione del riquadro a comparsa viene compressa nel solo titolo quando l'utente scorre gli elementi.

L'esempio seguente mostra come comprimere l'intestazione del riquadro a comparsa quando l'utente scorre:

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

## <a name="flyout-background-image"></a>Immagine di sfondo del riquadro a comparsa

Il riquadro a comparsa può avere un'immagine di sfondo facoltativa, visualizzata sotto l'intestazione e dietro gli elementi e le voci di menu. L'immagine di sfondo può `FlyoutBackgroundImage` essere specificata impostando [`ImageSource`](xref:Xamarin.Forms.ImageSource)la proprietà associabile, di tipo , su un file, una risorsa incorporata, un URI o un flusso.

Le proporzioni dell'immagine in background `FlyoutBackgroundImageAspect` possono essere configurate [`Aspect`](xref:Xamarin.Forms.Aspect)impostando la `Aspect` proprietà associabile, di tipo , su uno dei membri dell'enumerazione:

- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)- ritaglia l'immagine in modo che riempia l'area di visualizzazione mantenendo le proporzioni.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)- caselle delle lettere dell'immagine, se necessario, in modo che l'immagine si adatti all'area di visualizzazione, con uno spazio vuoto aggiunto in alto / in basso o lati a seconda che l'immagine sia larga o alta.
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)- allunga l'immagine per riempire completamente ed esattamente l'area di visualizzazione. Ciò può comportare la distorsione dell'immagine.

Per impostazione predefinita, la proprietà `FlyoutBackgroundImageAspect` verrà impostata su `AspectFit`.

L'esempio seguente mostra l'impostazione di queste proprietà:

```xaml
<Shell ...
       FlyoutBackgroundImage="photo.jpg"
       FlyoutBackgroundImageAspect="AspectFill">
    ...
</Shell>
```

Nel riquadro a comparsa viene quindi visualizzata un'immagine di sfondo:

![Screenshot di un'immagine di sfondo a comparsa](flyout-images/flyout-backgroundimage.png "Immagine di sfondo del riquadro a comparsa")

## <a name="flyout-items"></a>Elementi del riquadro a comparsa

Quando il modello di spostamento per un'applicazione include un riquadro a comparsa, l'oggetto `Shell` sottoclassato deve contenere uno o più oggetti `FlyoutItem`, con ogni oggetto `FlyoutItem` che rappresenta un elemento nel riquadro a comparsa. Ogni oggetto `FlyoutItem` deve essere un figlio dell'oggetto `Shell`.

L'esempio seguente crea un riquadro a comparsa contenente un'intestazione e due elementi:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <FlyoutItem Title="Cats"
                Icon="cat.png">
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
    <FlyoutItem Title="Dogs"
                Icon="dog.png">
        <Tab>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
</Shell>
```

In questo esempio, è possibile accedervi solo tramite elementi a comparsa:In this example, each [`ContentPage`](xref:Xamarin.Forms.ContentPage) can only be accessed through flyout items:

[![Screenshot di un'app Shell a due pagine con elementi del riquadro a comparsa, in iOS e Android](flyout-images/two-page-app-flyout.png "App Shell a due pagine con elementi del riquadro a comparsa")](flyout-images/two-page-app-flyout-large.png#lightbox "App Shell a due pagine con elementi del riquadro a comparsa")

> [!NOTE]
> Quando l'intestazione del riquadro a comparsa non è presente, gli elementi del riquadro a comparsa vengono visualizzati nella parte superiore del riquadro. In caso contrario, vengono visualizzati sotto l'intestazione.

La shell include operatori di conversione implicita che consentono di semplificare la gerarchia visiva della shell, senza introdurre visualizzazioni aggiuntive nella struttura ad albero visuale. Ciò è possibile perché un oggetto `Shell` sottoclassato può contenere solo oggetti `FlyoutItem` o un oggetto `TabBar`, che possono contenere solo oggetti `Tab`, che possono contenere solo oggetti `ShellContent`. Questi operatori di conversione implicita possono essere usati per rimuovere gli oggetti `FlyoutItem`, `Tab` e `ShellContent` nell'esempio precedente:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <views:CatsPage IconImageSource="cat.png" />
    <views:DogsPage IconImageSource="dog.png" />
</Shell>
```

Questa conversione implicita [`ContentPage`](xref:Xamarin.Forms.ContentPage) esegue `ShellContent` automaticamente il wrapping `Tab` di ogni oggetto `FlyoutItem` in oggetti, di cui viene eseguito il wrapping in oggetti, di cui viene eseguito il wrapping in oggetti.

> [!IMPORTANT]
> In un'applicazione [`ContentPage`](xref:Xamarin.Forms.ContentPage) Shell, ogni elemento `ShellContent` figlio di un oggetto viene creato durante l'avvio dell'applicazione. L'aggiunta di ulteriori oggetti `ShellContent` usando questo approccio comporta la creazione di pagine aggiuntive durante l'avvio dell'applicazione e ciò può portare a un'esperienza di avvio insoddisfacente. La shell, tuttavia, è anche in grado di creare pagine su richiesta, in risposta alla navigazione. Per altre informazioni, vedere [Caricamento efficiente delle pagine](tabs.md#efficient-page-loading) nella guida [Schede della shell Xamarin.Forms](tabs.md).

### <a name="flyoutitem-class"></a>Classe FlyoutItem

La classe `FlyoutItem` include le proprietà seguenti che controllano l'aspetto e il comportamento degli elementi del riquadro a comparsa:

- `FlyoutDisplayOptions`, di tipo `FlyoutDisplayOptions`, che definisce come vengono visualizzati l'elemento e i relativi elementi figlio nel riquadro a comparsa. Il valore predefinito è `AsSingleItem`.
- `CurrentItem`, di tipo `Tab`, ovvero l'elemento selezionato.
- `Items`, di tipo `IList<Tab>`, che definisce tutte le schede all'interno di un oggetto `FlyoutItem`.
- `FlyoutIcon`, di tipo `ImageSource`, ovvero l'icona da usare per l'elemento. Se questa proprietà non è impostata, viene eseguito il fallback all'uso del valore della proprietà `Icon`.
- `Icon`, di tipo `ImageSource`, che definisce l'icona da visualizzare nelle parti del riquadro che non costituiscono il riquadro a comparsa.
- `IsChecked`, di tipo `boolean`, che definisce se l'elemento è attualmente evidenziato nel riquadro a comparsa.
- `IsEnabled`, di tipo `boolean`, che definisce se l'elemento è selezionabile nel riquadro.
- `IsTabStop`, di tipo `bool`, che indica se un oggetto `FlyoutItem` è incluso nella navigazione tramite tabulazione. Il valore predefinito è `true` e quando questo valore è `false` l'oggetto `FlyoutItem` viene ignorato dall'infrastruttura di navigazione tramite tabulazione, indipendentemente dall'impostazione di `TabIndex`.
- `TabIndex`, di tipo `int`, che indica l'ordine in cui gli oggetti `FlyoutItem` ricevono lo stato attivo quando l'utente si sposta tra gli elementi premendo TAB. Il valore predefinito della proprietà è 0.
- `Title`, di tipo `string`, ovvero il titolo da visualizzare nell'interfaccia utente.
- `Route`, di tipo `string`, ovvero la stringa usata per indirizzare l'elemento.

Tutte queste proprietà, `Route` ad eccezione [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) della proprietà, sono supportate da oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> Tutti gli oggetti `FlyoutItem` in un oggetto Shell sottoclassato vengono aggiunti alla raccolta `Shell.Items`, che definisce l'elenco di elementi che verranno visualizzati nel riquadro a comparsa.

La classe `FlyoutItem` espone inoltre i metodi sottoponibili a override seguenti:

- `OnTabIndexPropertyChanged`, chiamato ogni volta che la proprietà `TabIndex` cambia.
- `OnTabStopPropertyChanged`, chiamato ogni volta che la proprietà `IsTabStop` cambia.
- `TabIndexDefaultValueCreator`, restituisce un valore `int` e viene chiamato per impostare il valore predefinito della proprietà `TabIndex`.
- `TabStopDefaultValueCreator`, restituisce un valore `bool` e viene chiamato per impostare il valore predefinito della proprietà `TabStop`.

## <a name="flyout-vertical-scroll"></a>Scorrimento verticale nel riquadro a comparsa

Per impostazione predefinita, è possibile scorrere un riquadro a comparsa verticalmente quando gli elementi del riquadro a comparsa non rientrano nel riquadro a comparsa. Questo comportamento può essere `Shell.FlyoutVerticalScrollMode` modificato impostando la proprietà `ScrollMode` associabile su uno dei membri dell'enumerazione:This behavior can be changed by setting the bindable property to one of the enumeration members:

- `Disabled`– indica che lo scorrimento verticale sarà disabilitato.
- `Enabled`– indica che lo scorrimento verticale sarà abilitato.
- `Auto`: indica che lo scorrimento verticale sarà abilitato se gli elementi del riquadro a comparsa non rientrano nel riquadro a comparsa. Questo è il valore predefinito per la proprietà `Shell.FlyoutVerticalScrollMode`.

L'esempio seguente mostra come disabilitare lo scorrimento verticale:

```xaml
<Shell ...
       FlyoutVerticalScrollMode="Disabled"
    ...
</Shell>
```

## <a name="flyout-display-options"></a>Opzioni di visualizzazione del riquadro a comparsa

L'enumerazione `FlyoutDisplayOptions` definisce i membri seguenti:

- `AsSingleItem`, indica che l'elemento sarà visibile come singolo elemento.
- `AsMultipleItems`, indica che l'elemento e i relativi elementi figlio saranno visibili nel riquadro a comparsa come gruppo di elementi.

Impostando la proprietà `FlyoutItem.FlyoutDisplayOptions` su `AsMultipleItems`, verrà creato un elemento del riquadro a comparsa per ogni oggetto `Tab` in un oggetto `FlyoutItem`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       FlyoutHeaderBehavior="CollapseOnScroll"
       x:Class="Xaminals.AppShell">

    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>

    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png"
                          ContentTemplate="{DataTemplate views:CatsPage}" />
            <ShellContent Title="Dogs"
                          Icon="dog.png"
                          ContentTemplate="{DataTemplate views:DogsPage}" />
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png"
                      ContentTemplate="{DataTemplate views:MonkeysPage}" />
        <ShellContent Title="Elephants"
                      Icon="elephant.png"
                      ContentTemplate="{DataTemplate views:ElephantsPage}" />  
        <ShellContent Title="Bears"
                      Icon="bear.png"
                      ContentTemplate="{DataTemplate views:BearsPage}" />
    </FlyoutItem>

    <ShellContent Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />    
</Shell>
```

In questo esempio vengono creati elementi del riquadro a comparsa per l'oggetto `Tab`, che è un figlio dell'oggetto `FlyoutItem`, e per gli oggetti `ShellContent` che sono figli dell'oggetto `FlyoutItem`. Ciò si verifica perché viene eseguito automaticamente il wrapping di ogni oggetto `ShellContent`, che è un figlio dell'oggetto `FlyoutItem`, in un oggetto `Tab`. Viene inoltre creato un elemento del riquadro a comparsa per l'oggetto `ShellContent` finale, di cui viene eseguito il wrapping in un oggetto `Tab` e quindi in un oggetto `FlyoutItem`.

Il risultato sono gli elementi del riquadro a comparsa seguenti:

[![Screenshot del riquadro a comparsa contenente oggetti FlyoutItem, in iOS e Android](flyout-images/flyout-reduced.png "Riquadro a comparsa della shell contenente oggetti FlyoutItem")](flyout-images/flyout-reduced-large.png#lightbox "Riquadro a comparsa della shell contenente oggetti FlyoutItem")

## <a name="define-flyoutitem-appearance"></a>Definire l'aspetto di un oggetto FlyoutItem

L'aspetto `FlyoutItem` di ciascuno può essere `Shell.ItemTemplate` personalizzato impostando [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)la proprietà associata su un :

```xaml
<Shell ...>
    ...
    <Shell.ItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding FlyoutIcon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Title}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.ItemTemplate>
</Shell>
```

Questo esempio visualizza il titolo di ogni oggetto `FlyoutItem` in corsivo:

[![Screenshot degli oggetti FlyoutItem basati su modelli, in iOS e Android](flyout-images/flyoutitem-templated.png "Oggetti FlyoutItem basati su shell")](flyout-images/flyoutitem-templated-large.png#lightbox "Oggetti FlyoutItem basati su shell")


Poiché `Shell.ItemTemplate` è una proprietà associata, è `FlyoutItem` possibile associare modelli diversi a oggetti specifici.

> [!NOTE]
> Shell fornisce `Title` `FlyoutIcon` le proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) e `ItemTemplate`al .


### <a name="default-template-for-flyoutitems-and-menuitems"></a>Modello predefinito per FlyoutItems e MenuItems
Shell utilizza internamente il modello seguente per l'implementazione predefinita. Questo è un ottimo punto di partenza se tutto quello che vuoi fare è fare piccole modifiche ai layout esistenti. Vengono inoltre illustrate le funzionalità di Gestione stato di visualizzazione degli elementi del riquadro a comparsa. Questo stesso modello può essere utilizzato anche per MenuItems

```xaml
<DataTemplate x:Key="FlyoutTemplates">
    <Grid HeightRequest="{x:OnPlatform Android=50}">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroupList>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal">
                    </VisualState>
                    <VisualState x:Name="Selected">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="#F2F2F2" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateGroupList>
        </VisualStateManager.VisualStateGroups>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="{x:OnPlatform Android=54, iOS=50}"></ColumnDefinition>
            <ColumnDefinition Width="*"></ColumnDefinition>
        </Grid.ColumnDefinitions>
        <Image Source="{Binding FlyoutIcon}"
            VerticalOptions="Center"
            HorizontalOptions="Center"
            HeightRequest="{x:OnPlatform Android=24, iOS=22}"
            WidthRequest="{x:OnPlatform Android=24, iOS=22}">
        </Image>
        <Label VerticalOptions="Center"
                Text="{Binding Title}"
                FontSize="{x:OnPlatform Android=14, iOS=Small}"
                FontAttributes="Bold" Grid.Column="1">
            <Label.TextColor>
                <OnPlatform x:TypeArguments="Color">
                    <OnPlatform.Platforms>
                        <On Platform="Android" Value="#D2000000" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.TextColor>
            <Label.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="Android" Value="20, 0, 0, 0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.Margin>
            <Label.FontFamily>
                <OnPlatform x:TypeArguments="x:String">
                    <OnPlatform.Platforms>
                        <On Platform="Android" Value="sans-serif-medium" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.FontFamily>
        </Label>
    </Grid>
</DataTemplate>
```

## <a name="flyoutitem-tab-order"></a>Ordine di tabulazione degli oggetti FlyoutItem

Per impostazione predefinita, l'ordine di tabulazione degli oggetti `FlyoutItem` è lo stesso in cui sono elencati in XAML o vengono aggiunti a livello di codice a una raccolta figlio. Questo ordine corrisponde all'ordine in base a cui ci si sposterà tra gli oggetti `FlyoutItem` tramite tastiera e spesso l'ordine predefinito è l'ordine ottimale.

L'ordine di tabulazione predefinito può essere modificato impostando la proprietà `FlyoutItem.TabIndex`, che indica l'ordine in cui gli oggetti `FlyoutItem` ricevono lo stato attivo quando l'utente si sposta tra gli elementi premendo TAB. Il valore predefinito della proprietà è 0, e può essere impostata su qualsiasi valore `int`.

Le regole seguenti si applicano per l'uso dell'ordine di tabulazione predefinito o per l'impostazione della proprietà `TabIndex`:

- Gli oggetti `FlyoutItem` con `TabIndex` equivalente a 0 vengono aggiunti all'ordine di tabulazione in base all'ordine di dichiarazione in XAML o nelle raccolte figlio.
- Gli oggetti `FlyoutItem` con `TabIndex` maggiore di 0 vengono aggiunti all'ordine di tabulazione in base al rispettivo valore di `TabIndex`.
- Gli oggetti `FlyoutItem` con `TabIndex` minore di 0 vengono aggiunti all'ordine di tabulazione e vengono visualizzati prima di qualsiasi valore zero.
- I conflitti per `TabIndex` vengono risolti in base all'ordine di dichiarazione.

Dopo aver definito un ordine di tabulazione, premendo TAB lo stato attivo viene spostato in sequenza tra gli oggetti `FlyoutItem` in ordine crescente di `TabIndex`, ritornando all'inizio dopo aver raggiunto l'oggetto finale.

Oltre a impostare l'ordine di tabulazione degli oggetti `FlyoutItem`, può essere necessario escludere alcuni oggetti da tale ordine. A tale scopo, è possibile usare la proprietà `FlyoutItem.IsTabStop`, che indica se un oggetto `FlyoutItem` è incluso nella navigazione tramite tabulazione. Il valore predefinito è `true` e quando questo valore è `false` l'oggetto `FlyoutItem` viene ignorato dall'infrastruttura di navigazione tramite tabulazione, indipendentemente dall'impostazione di `TabIndex`.

## <a name="set-the-current-flyoutitem"></a>Impostare l'oggetto FlyoutItem corrente

La classe `Shell` ha una proprietà associabile denominata `CurrentItem`, di tipo `FlyoutItem`, che rappresenta l'oggetto `FlyoutItem` attualmente selezionato. Quando un'applicazione shell viene eseguita per la prima volta, questa proprietà viene impostata sul primo oggetto `FlyoutItem` nell'oggetto `Shell` sottoclassato. La proprietà può tuttavia essere impostata su un altro oggetto `FlyoutItem`, come illustrato nell'esempio seguente:

```xaml
<Shell ...
       CurrentItem="{x:Reference aboutItem}">
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        ...
    </FlyoutItem>
    <ShellContent x:Name="aboutItem"
                  Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />
</Shell>
```

Questo codice imposta l'oggetto `ShellContent` denominato `aboutItem` come proprietà `CurrentItem` e, di conseguenza, l'oggetto viene visualizzato. In questo esempio viene usata una conversione implicita per eseguire il wrapping dell'oggetto `ShellContent` in un oggetto `Tab`, di cui viene eseguito il wrapping in un oggetto `FlyoutItem`.

Il codice C# equivalente è il seguente:

```csharp
Shell.Current.CurrentItem = aboutItem;
```

## <a name="menu-items"></a>Voci di menu

Le voci di menu possono essere aggiunte facoltativamente al [`MenuItem`](xref:Xamarin.Forms.MenuItem) riquadro a comparsa e ogni voce di menu è rappresentata da un oggetto. La posizione degli oggetti `MenuItem` nel riquadro a comparsa dipende dall'ordine di dichiarazione nella gerarchia visiva della shell. Pertanto, qualsiasi oggetto `MenuItem` dichiarato prima degli oggetti `FlyoutItem` verrà visualizzato nella parte superiore del riquadro a comparsa e gli eventuali oggetti `MenuItem` dichiarati dopo gli oggetti `FlyoutItem` verranno visualizzati nella parte inferiore del riquadro a comparsa.

> [!NOTE]
> La `MenuItem` classe [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) dispone di [`Command`](xref:Xamarin.Forms.MenuItem.Command) un evento e di una proprietà. Gli oggetti `MenuItem` consentono quindi scenari in cui viene eseguita un'azione in risposta al tocco di `MenuItem`. Questi scenari includono la navigazione e l'apertura di un Web browser in una pagina Web specifica.

[`MenuItem`](xref:Xamarin.Forms.MenuItem)gli oggetti possono essere aggiunti al riquadro a comparsa come illustrato nell'esempio seguente:

```xaml
<Shell ...>
    ...            
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />    
</Shell>
```

Questo codice [`MenuItem`](xref:Xamarin.Forms.MenuItem) aggiunge due oggetti al riquadro a comparsa, sotto tutti gli elementi riquadro a comparsa:This code adds two objects to the flyout, sotto all flyout items:

[![Screenshot del riquadro a comparsa contenente oggetti MenuItem, iOS e Android](flyout-images/flyout.png "Riquadro a comparsa della shell contenente oggetti MenuItem")](flyout-images/flyout-large.png#lightbox "Riquadro a comparsa della shell contenente oggetti MenuItem")

Il [`MenuItem`](xref:Xamarin.Forms.MenuItem) primo oggetto `ICommand` esegue `RandomPageCommand`un oggetto denominato , che consente di passare a una pagina casuale nell'applicazione. Il secondo oggetto `MenuItem` esegue un comando `ICommand` denominato `HelpCommand`, che apre l'URL specificato dalla proprietà `CommandParameter` in un Web browser.

> [!NOTE]
> [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) L'oggetto `MenuItem` viene ereditato dall'oggetto sottoclassato. `Shell`

## <a name="define-menuitem-appearance"></a>Definire l'aspetto di un oggetto MenuItem

L'aspetto `MenuItem` di ciascuno può essere `Shell.MenuItemTemplate` personalizzato impostando [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)la proprietà associata su un :

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding Icon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Text}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.MenuItemTemplate>
    ...
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />  
</Shell>
```

Questo esempio associa la proprietà `MenuItemTemplate` a livello di shell a ogni oggetto `MenuItem`, visualizzando il titolo di ogni oggetto `MenuItem` in corsivo:

[![Screenshot degli oggetti MenuItem basati su modelli, in iOS e Android](flyout-images/menuitem-templated.png "Oggetti MenuItem basati su modelli di shell")](flyout-images/menuitem-templated-large.png#lightbox "Oggetti MenuItem basati su modelli di shell")

> [!NOTE]
> Shell fornisce [`Text`](xref:Xamarin.Forms.MenuItem.Text) [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) le proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) e `MenuItemTemplate`al . È inoltre `Title` possibile utilizzare `Text` `Icon` al posto `IconImageSource` di e al posto del quale vi permetterà di riutilizzare lo stesso modello per le voci di menu e flyout elementi

Dato che `Shell.MenuItemTemplate` è una proprietà associata, si possono associare modelli diversi a oggetti `MenuItem` specifici:

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding Icon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Text}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.MenuItemTemplate>
    ...
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              Icon="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell">
        <Shell.MenuItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </Shell.MenuItemTemplate>
    </MenuItem>
</Shell>
```


> [!NOTE]
> Lo stesso modello utilizzato per [gli elementi riquadro](#default-template-for-flyoutitems-and-menuitems) a comparsa può essere utilizzato anche per le voci di menu.

Questo esempio associa la proprietà `MenuItemTemplate` a livello di shell al primo oggetto `MenuItem` e associa la proprietà `MenuItemTemplate` inline al secondo `MenuItem`.

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
