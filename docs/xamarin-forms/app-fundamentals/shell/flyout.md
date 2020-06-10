---
title: " Xamarin.Forms Shell riquadro a comparsa": "il riquadro a comparsa è il menu radice per un'applicazione shell ed è accessibile tramite un'icona o scorrendo il dito dal lato dello schermo. Il riquadro a comparsa è costituito da un'intestazione facoltativa, elementi del riquadro a comparsa e voci di menu facoltative.
ms. prod: Novell MS. AssetID: FEDE51EB-577E-4B3E-9890-B7C1A5E52516 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/22/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-shell-flyout"></a>Xamarin.FormsRiquadro a comparsa Shell

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Il riquadro a comparsa è il menu radice per un'applicazione shell ed è accessibile attraverso un'icona o tramite scorrimento rapido dal lato dello schermo. Il riquadro a comparsa è costituito da un'intestazione facoltativa, elementi del riquadro a comparsa e voci di menu facoltative:

![Screenshot di un riquadro a comparsa con annotazioni della shell](flyout-images/flyout-annotated.png "Riquadro a comparsa con annotazioni")

Se necessario, il colore di sfondo del riquadro a comparsa può essere impostato su un oggetto [`Color`](xref:Xamarin.Forms.Color) tramite la `Shell.FlyoutBackgroundColor` proprietà associabile. Questa proprietà può anche essere impostata da un foglio di stile CSS (Cascading Style Sheet). Per altre informazioni, vedere [ Xamarin.Forms proprietà specifiche della shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

## <a name="flyout-icon"></a>Icona del riquadro a comparsa

Per impostazione predefinita, le applicazioni shell hanno un'icona hamburger che è possibile premere per aprire il riquadro a comparsa. Questa icona può essere modificata impostando la `Shell.FlyoutIcon` proprietà associabile, di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , su un'icona appropriata:

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

In alternativa, è possibile definire l'aspetto dell'intestazione del riquadro a comparsa impostando la `Shell.FlyoutHeaderTemplate` proprietà su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

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

Il riquadro a comparsa può avere un'immagine di sfondo facoltativa, visualizzata sotto l'intestazione e dietro gli elementi e le voci di menu. È possibile specificare l'immagine di sfondo impostando la `FlyoutBackgroundImage` proprietà associabile, di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , su un file, una risorsa incorporata, un URI o un flusso.

È possibile configurare le proporzioni dell'immagine di sfondo impostando la `FlyoutBackgroundImageAspect` proprietà associabile, di tipo [`Aspect`](xref:Xamarin.Forms.Aspect) , su uno dei `Aspect` membri dell'enumerazione:

- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): Ritaglia l'immagine in modo da riempire l'area di visualizzazione mantenendo le proporzioni.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)-Letterbox l'immagine, se necessario, in modo che l'immagine si trovi nell'area di visualizzazione, con uno spazio vuoto aggiunto alla parte superiore/inferiore o ai lati a seconda che l'immagine sia larga o alta.
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): estende l'immagine in modo da riempire completamente l'area di visualizzazione. Ciò può comportare la distorsione dell'immagine.

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

![Screenshot di un'immagine di sfondo del riquadro a comparsa](flyout-images/flyout-backgroundimage.png "Immagine di sfondo del riquadro a comparsa")

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

In questo esempio, [`ContentPage`](xref:Xamarin.Forms.ContentPage) è possibile accedere a ogni oggetto solo tramite elementi del riquadro a comparsa:

[![Screenshot di un'app shell a due pagine con elementi a comparsa in iOS e Android](flyout-images/two-page-app-flyout.png "App shell a due pagine con elementi del riquadro a comparsa")](flyout-images/two-page-app-flyout-large.png#lightbox "App shell a due pagine con elementi del riquadro a comparsa")

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

Questa conversione implicita esegue automaticamente il wrapping di ogni [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto negli oggetti `ShellContent` , che sono racchiusi tra oggetti `Tab` , che sono racchiusi tra `FlyoutItem` oggetti.

> [!IMPORTANT]
> In un'applicazione shell ogni [`ContentPage`](xref:Xamarin.Forms.ContentPage) elemento figlio di un `ShellContent` oggetto viene creato durante l'avvio dell'applicazione. L'aggiunta di ulteriori oggetti `ShellContent` usando questo approccio comporta la creazione di pagine aggiuntive durante l'avvio dell'applicazione e ciò può portare a un'esperienza di avvio insoddisfacente. La shell, tuttavia, è anche in grado di creare pagine su richiesta, in risposta alla navigazione. Per ulteriori informazioni, vedere [caricamento di pagine efficienti](tabs.md#efficient-page-loading) nella Guida alle [ Xamarin.Forms schede della shell](tabs.md) .

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

Tutte queste proprietà, ad eccezione della `Route` proprietà, sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> Tutti gli oggetti `FlyoutItem` in un oggetto Shell sottoclassato vengono aggiunti alla raccolta `Shell.Items`, che definisce l'elenco di elementi che verranno visualizzati nel riquadro a comparsa.

La classe `FlyoutItem` espone inoltre i metodi sottoponibili a override seguenti:

- `OnTabIndexPropertyChanged`, chiamato ogni volta che la proprietà `TabIndex` cambia.
- `OnTabStopPropertyChanged`, chiamato ogni volta che la proprietà `IsTabStop` cambia.
- `TabIndexDefaultValueCreator`, restituisce un valore `int` e viene chiamato per impostare il valore predefinito della proprietà `TabIndex`.
- `TabStopDefaultValueCreator`, restituisce un valore `bool` e viene chiamato per impostare il valore predefinito della proprietà `TabStop`.

## <a name="flyout-vertical-scroll"></a>Scorrimento verticale nel riquadro a comparsa

Per impostazione predefinita, è possibile scorrere verticalmente un riquadro a comparsa quando gli elementi del riquadro a comparsa non rientrano nel riquadro a comparsa. Questo comportamento può essere modificato impostando la `Shell.FlyoutVerticalScrollMode` proprietà associabile su uno dei `ScrollMode` membri dell'enumerazione:

- `Disabled`: indica che lo scorrimento verticale sarà disabilitato.
- `Enabled`: indica che verrà abilitato lo scorrimento verticale.
- `Auto`: indica che lo scorrimento verticale verrà abilitato se gli elementi del riquadro a comparsa non si adattano al riquadro a comparsa. Questo è il valore predefinito per la proprietà `Shell.FlyoutVerticalScrollMode`.

Nell'esempio seguente viene illustrato come disabilitare lo scorrimento verticale:

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

[![Screenshot del riquadro a comparsa contenente oggetti FlyoutItem in iOS e Android](flyout-images/flyout-reduced.png "Riquadro a comparsa shell contenente oggetti FlyoutItem")](flyout-images/flyout-reduced-large.png#lightbox "Riquadro a comparsa shell contenente oggetti FlyoutItem")

## <a name="define-flyoutitem-appearance"></a>Definire l'aspetto di un oggetto FlyoutItem

L'aspetto di ogni oggetto `FlyoutItem` può essere personalizzato impostando la `Shell.ItemTemplate` proprietà associata su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

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

[![Screenshot degli oggetti FlyoutItem basati su modelli, in iOS e Android](flyout-images/flyoutitem-templated.png "Oggetti FlyoutItem basati su modelli della shell")](flyout-images/flyoutitem-templated-large.png#lightbox "Oggetti FlyoutItem basati su modelli della shell")

Poiché `Shell.ItemTemplate` è una proprietà associata, è possibile collegare modelli diversi a `FlyoutItem` oggetti specifici.

> [!NOTE]
> Shell fornisce le `Title` `FlyoutIcon` proprietà e alla proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) dell'oggetto `ItemTemplate` .

Inoltre, la shell include tre classi di stile, che vengono applicate automaticamente agli `FlyoutItem` oggetti. Per altre informazioni, vedere [FlyoutItem e le classi di stile MenuItem](#flyoutitem-and-menuitem-style-classes).

### <a name="default-template-for-flyoutitems"></a>Modello predefinito per FlyoutItems

Il valore predefinito [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) usato per ogni `FlyoutItem` è illustrato di seguito:

```xaml
<DataTemplate x:Key="FlyoutTemplate">
    <Grid x:Name="FlyoutItemLayout"
          HeightRequest="{x:OnPlatform Android=50}"
          ColumnSpacing="{x:OnPlatform UWP=0}"
          RowSpacing="{x:OnPlatform UWP=0}">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroupList>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Selected">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor"
                                    Value="{x:OnPlatform Android=#F2F2F2, iOS=#F2F2F2}" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateGroupList>
        </VisualStateManager.VisualStateGroups>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="{x:OnPlatform Android=54, iOS=50, UWP=Auto}" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Image x:Name="FlyoutItemImage"
               Source="{Binding FlyoutIcon}"
               VerticalOptions="Center"
               HorizontalOptions="{x:OnPlatform Default=Center, UWP=Start}"
               HeightRequest="{x:OnPlatform Android=24, iOS=22, UWP=16}"
               WidthRequest="{x:OnPlatform Android=24, iOS=22, UWP=16}">
            <Image.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="UWP"
                            Value="12,0,12,0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Image.Margin>
        </Image>
        <Label x:Name="FlyoutItemLabel"
               Grid.Column="1"
               Text="{Binding Title}"
               FontSize="{x:OnPlatform Android=14, iOS=Small}"
               HorizontalOptions="{x:OnPlatform UWP=Start}"
               HorizontalTextAlignment="{x:OnPlatform UWP=Start}"
               FontAttributes="{x:OnPlatform iOS=Bold}"
               VerticalTextAlignment="Center">
            <Label.TextColor>
                <OnPlatform x:TypeArguments="Color">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="#D2000000" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.TextColor>
            <Label.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="20, 0, 0, 0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.Margin>
            <Label.FontFamily>
                <OnPlatform x:TypeArguments="x:String">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="sans-serif-medium" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.FontFamily>
        </Label>
    </Grid>
</DataTemplate>
```

Questo modello può essere utilizzato come base per apportare modifiche al layout del riquadro a comparsa esistente e Mostra anche gli Stati di visualizzazione implementati per gli elementi del riquadro a comparsa.

Inoltre, gli [`Grid`](xref:Xamarin.Forms.Grid) elementi, [`Image`](xref:Xamarin.Forms.Image) e [`Label`](xref:Xamarin.Forms.Label) hanno tutti i `x:Name` valori e possono quindi essere assegnati al gestore dello stato di visualizzazione. Per altre informazioni, vedere [impostare lo stato su più elementi](~/xamarin-forms/user-interface/visual-state-manager.md#set-state-on-multiple-elements).

> [!NOTE]
> Lo stesso modello può essere utilizzato anche per `MenuItem` gli oggetti.

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

Il codice C# equivalente, dato un `ShellContent` oggetto denominato `aboutItem` , è:

```csharp
CurrentItem = aboutItem;
```

In questo esempio la `CurrentItem` proprietà viene impostata nella classe sottoclasse `Shell` . In alternativa, `CurrentItem` è possibile impostare la proprietà in qualsiasi classe tramite la `Shell.Current` proprietà statica:

```csharp
Shell.Current.CurrentItem = aboutItem;
```

## <a name="menu-items"></a>Voci di menu

Facoltativamente, le voci di menu possono essere aggiunte al riquadro a comparsa e ogni voce di menu è rappresentata da un [`MenuItem`](xref:Xamarin.Forms.MenuItem) oggetto. La posizione degli oggetti `MenuItem` nel riquadro a comparsa dipende dall'ordine di dichiarazione nella gerarchia visiva della shell. Pertanto, qualsiasi oggetto `MenuItem` dichiarato prima degli oggetti `FlyoutItem` verrà visualizzato nella parte superiore del riquadro a comparsa e gli eventuali oggetti `MenuItem` dichiarati dopo gli oggetti `FlyoutItem` verranno visualizzati nella parte inferiore del riquadro a comparsa.

> [!NOTE]
> La `MenuItem` classe ha un [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) evento e una [`Command`](xref:Xamarin.Forms.MenuItem.Command) Proprietà. Gli oggetti `MenuItem` consentono quindi scenari in cui viene eseguita un'azione in risposta al tocco di `MenuItem`. Questi scenari includono la navigazione e l'apertura di un Web browser in una pagina Web specifica.

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

Questo codice aggiunge due [`MenuItem`](xref:Xamarin.Forms.MenuItem) oggetti al riquadro a comparsa, sotto tutti gli elementi del riquadro a comparsa:

[![Screenshot del riquadro a comparsa contenente oggetti MenuItem in iOS e Android](flyout-images/flyout.png "Riquadro a comparsa shell contenente oggetti MenuItem")](flyout-images/flyout-large.png#lightbox "Riquadro a comparsa shell contenente oggetti MenuItem")

Il primo [`MenuItem`](xref:Xamarin.Forms.MenuItem) oggetto esegue un oggetto `ICommand` denominato `RandomPageCommand` , che consente di passare a una pagina casuale nell'applicazione. Il secondo oggetto `MenuItem` esegue un comando `ICommand` denominato `HelpCommand`, che apre l'URL specificato dalla proprietà `CommandParameter` in un Web browser.

> [!NOTE]
> Il [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di ogni `MenuItem` viene ereditato dall'oggetto sottoclassato `Shell` .

## <a name="define-menuitem-appearance"></a>Definire l'aspetto di un oggetto MenuItem

L'aspetto di ogni oggetto `MenuItem` può essere personalizzato impostando la `Shell.MenuItemTemplate` proprietà associata su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

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

[![Screenshot degli oggetti MenuItem basati su modelli, in iOS e Android](flyout-images/menuitem-templated.png "Oggetti MenuItem basati su modelli della shell")](flyout-images/menuitem-templated-large.png#lightbox "Oggetti MenuItem basati su modelli della shell")

> [!NOTE]
> Shell fornisce le [`Text`](xref:Xamarin.Forms.MenuItem.Text) [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) proprietà e alla proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) dell'oggetto `MenuItemTemplate` . È anche possibile usare `Title` al posto di `Text` e `Icon` al posto di `IconImageSource` che consentirà di riutilizzare lo stesso modello per le voci di menu e gli elementi del riquadro a comparsa

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

Questo esempio associa la proprietà `MenuItemTemplate` a livello di shell al primo oggetto `MenuItem` e associa la proprietà `MenuItemTemplate` inline al secondo `MenuItem`.

> [!NOTE]
> `FlyoutItem`Per gli oggetti è inoltre possibile utilizzare il modello predefinito per gli oggetti `MenuItem` . Per ulteriori informazioni, vedere [modello predefinito per FlyoutItems](#default-template-for-flyoutitems).

## <a name="flyoutitem-and-menuitem-style-classes"></a>Classi di stile FlyoutItem e MenuItem

La shell include tre classi di stile, che vengono applicate automaticamente agli `FlyoutItem` `MenuItem` oggetti e. I nomi delle classi di stile sono:

- `FlyoutItemLabelStyle`
- `FlyoutItemImageStyle`
- `FlyoutItemLayoutStyle`

Il codice XAML seguente mostra un esempio di definizione di stili per queste classi di stile:

```xaml
<Style TargetType="Label"
       Class="FlyoutItemLabelStyle">
    <Setter Property="TextColor"
            Value="Black" />
    <Setter Property="HeightRequest"
            Value="100" />
</Style>

<Style TargetType="Image"
       Class="FlyoutItemImageStyle">
    <Setter Property="Aspect"
            Value="Fill" />
</Style>

<Style TargetType="Layout"
       Class="FlyoutItemLayoutStyle"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Teal" />
</Style>
```

Questi stili verranno applicati automaticamente agli `FlyoutItem` oggetti e `MenuItem` , senza che sia necessario impostarne le [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) proprietà sui nomi delle classi di stile.

Inoltre, è possibile definire e applicare classi di stile personalizzate agli `FlyoutItem` `MenuItem` oggetti e. Per ulteriori informazioni sulle classi di stile, vedere [ Xamarin.Forms classi di stile](~/xamarin-forms/user-interface/styles/xaml/style-class.md).

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.FormsClassi di stile](~/xamarin-forms/user-interface/styles/xaml/style-class.md)
- [Xamarin.FormsGestione stato di visualizzazione](~/xamarin-forms/user-interface/visual-state-manager.md)
