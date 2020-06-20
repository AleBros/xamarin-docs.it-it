---
title: Orientamento del dispositivo
description: Questo articolo illustra come eseguire il layout Xamarin.Forms delle applicazioni che hanno un aspetto straordinario negli orientamenti verticale e orizzontale.
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0b1a47d4dcc92fca4d280708a2cbbe9374c17da8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573295"
---
# <a name="device-orientation"></a>Orientamento del dispositivo

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)

È importante considerare la modalità di utilizzo dell'applicazione e il modo in cui è possibile incorporare l'orientamento orizzontale per migliorare l'esperienza utente. I singoli layout possono essere progettati per supportare più orientamenti e utilizzare al meglio lo spazio disponibile. A livello di applicazione, la rotazione può essere disabilitata o abilitata.

## <a name="controlling-orientation"></a>Controllo dell'orientamento

Quando Xamarin.Forms si usa, il metodo supportato per il controllo dell'orientamento del dispositivo consiste nell'usare le impostazioni per ogni singolo progetto.

### <a name="ios"></a>iOS

In iOS, l'orientamento del dispositivo è configurato per le applicazioni che usano il file **info. plist** . Usare le opzioni IDE nella parte superiore di questo documento per selezionare le istruzioni che si desidera visualizzare:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

In Visual Studio aprire il progetto iOS e aprire **info. plist**. Il file si aprirà in un pannello di configurazione, a partire dalla scheda informazioni sulla distribuzione iPhone:

![Informazioni sulla distribuzione di iPhone in Visual Studio](device-orientation-images/orientation-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

In Visual Studio per Mac aprire il progetto iOS e aprire **info. plist**. Nella scheda **applicazione** le sezioni saranno disponibili per impostare l'orientamento:

![Informazioni sulla distribuzione di iPhone in Visual Studio per Mac](device-orientation-images/orientation-vsmac.png)

Se si preferisce modificare i valori usando un'interfaccia dell'editor chiave-valore, selezionare la scheda> di **origine** nella parte inferiore della schermata:

![Orientamenti dei dispositivi supportati in Visual Studio per Mac](device-orientation-images/orientation-source-vsmac.png)

-----

### <a name="android"></a>Android

Per controllare l'orientamento in Android, aprire **MainActivity.cs** e impostare l'orientamento usando l'attributo che decora la `MainActivity` classe:

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Novell. Android supporta diverse opzioni per specificare l'orientamento:

- **Orizzontale** &ndash; impone l'orientamento dell'applicazione come orizzontale, indipendentemente dai dati del sensore.
- **Verticale** &ndash; impone il verticale dell'orientamento dell'applicazione, indipendentemente dai dati del sensore.
- **Utente** &ndash; di fa in modo che l'applicazione venga presentata utilizzando l'orientamento preferito dell'utente.
- **Dietro** &ndash; determina l'orientamento dell'applicazione in modo che corrisponda all'orientamento dell' [attività](xref:Android.App.Activity) sottostante.
- **Sensore** &ndash; di fa in modo che l'orientamento dell'applicazione venga determinato dal sensore, anche se l'utente ha disabilitato la rotazione automatica.
- **SensorLandscape** &ndash; fa in modo che l'applicazione usi l'orientamento orizzontale quando usa i dati del sensore per modificare la direzione di visualizzazione dello schermo, in modo che lo schermo non venga visualizzato come capovolto.
- **SensorPortrait** &ndash; fa in modo che l'applicazione usi l'orientamento verticale quando si usano i dati del sensore per modificare la direzione di visualizzazione dello schermo, in modo che lo schermo non venga visualizzato come capovolto.
- **ReverseLandscape** &ndash; fa in modo che l'applicazione usi l'orientamento orizzontale, rivolto verso la direzione opposta rispetto al solito, in modo da visualizzare "capovolto".
- **ReversePortrait** &ndash; fa in modo che l'applicazione usi l'orientamento verticale, rivolto alla direzione opposta rispetto al solito, in modo da visualizzare "capovolto".
- **FullSensor** &ndash; fa in modo che l'applicazione si basi sui dati dei sensori per selezionare l'orientamento corretto (fuori dal possibile 4).
- **FullUser** &ndash; consente all'applicazione di utilizzare le preferenze di orientamento dell'utente. Se è abilitata la rotazione automatica, è possibile usare tutti e quattro gli orientamenti.
- **UserLandscape** &ndash; _ \[ Non supportato \] _ fa in modo che l'applicazione usi l'orientamento orizzontale, a meno che l'utente non abbia la rotazione automatica abilitata, nel qual caso utilizzerà il sensore per determinare l'orientamento. Questa opzione consente di interrompere la compilazione.
- **UserPortrait** &ndash; _ \[ Non supportato \] _ fa in modo che l'applicazione usi l'orientamento verticale, a meno che l'utente non abbia la rotazione automatica abilitata, nel qual caso utilizzerà il sensore per determinare l'orientamento. Questa opzione consente di interrompere la compilazione.
- Con **blocco** &ndash; _ \[ Non supportato \] _ fa in modo che l'applicazione usi l'orientamento dello schermo, indipendentemente dal fatto che sia in fase di avvio, senza rispondere alle modifiche nell'orientamento fisico del dispositivo. Questa opzione consente di interrompere la compilazione.

Si noti che le API Android Native forniscono una grande quantità di controllo sulla modalità di gestione dell'orientamento, incluse le opzioni che contraddicono esplicitamente le preferenze espresse dall'utente.

### <a name="universal-windows-platform"></a>Piattaforma Windows universale

Nel piattaforma UWP (Universal Windows Platform) (UWP), gli orientamenti supportati vengono impostati nel file **Package. appxmanifest** . Aprendo il manifesto verrà rivelato un pannello di configurazione in cui è possibile selezionare gli orientamenti supportati.

## <a name="reacting-to-changes-in-orientation"></a>Reazione alle modifiche nell'orientamento

Xamarin.Formsnon offre eventi nativi per notificare all'app le modifiche dell'orientamento nel codice condiviso. Contiene tuttavia [Xamarin.Essentials](~/essentials/index.md) una classe [ `DeviceDisplay` ] che fornisce le notifiche relative alle modifiche dell'orientamento.

Per rilevare gli orientamenti senza Xamarin.Essentials , monitorare l' `SizeChanged` evento dell'oggetto `Page` , che viene attivato quando `Page` viene modificata la larghezza o l'altezza. Quando la larghezza di `Page` è maggiore dell'altezza, il dispositivo è in modalità orizzontale. Per altre informazioni, vedere [visualizzare un'immagine in base all'orientamento dello schermo](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation).

In alternativa, è possibile eseguire l'override del [`OnSizeAllocated`](xref:Xamarin.Forms.Page.OnSizeAllocated*) metodo su un oggetto `Page` , inserendo la logica di modifica del layout. Il `OnSizeAllocated` metodo viene chiamato ogni volta che un oggetto `Page` viene allocato a una nuova dimensione, che viene eseguita ogni volta che il dispositivo viene ruotato. Si noti che l'implementazione di base di `OnSizeAllocated` esegue funzioni di layout importanti, quindi è importante chiamare l'implementazione di base nell'override:

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

La mancata esecuzione di questo passaggio comporterà una pagina non funzionante.

Si noti che il `OnSizeAllocated` metodo può essere chiamato molte volte quando un dispositivo viene ruotato. Modificare il layout ogni volta è uno spreco di risorse e può causare lo sfarfallio. Prendere in considerazione l'uso di una variabile di istanza all'interno della pagina per verificare se l'orientamento è orizzontale o verticale e ricreare solo quando viene apportata una modifica:

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

Una volta rilevata una modifica nell'orientamento del dispositivo, è possibile aggiungere o rimuovere visualizzazioni aggiuntive da e verso l'interfaccia utente per rispondere alla modifica dello spazio disponibile. Si consideri, ad esempio, la calcolatrice incorporata in ogni piattaforma verticale:

![](device-orientation-images/calculator-portrait.png "Calculator Application in Portrait")

e Landscape:

![](device-orientation-images/calculator-landscape.png "Calculator Application in Landscape")

Si noti che le app sfruttano lo spazio disponibile aggiungendo più funzionalità in orizzontale.

## <a name="responsive-layout"></a>Layout reattivo

È possibile progettare interfacce usando i layout incorporati in modo che si trasformano correttamente quando il dispositivo viene ruotato. Quando si progettano interfacce che continueranno a essere accattivanti quando rispondono alle modifiche nell'orientamento, prendere in considerazione le regole generali seguenti:

- **Prestare attenzione ai rapporti** &ndash; le modifiche all'orientamento possono causare problemi quando si apportano determinati presupposti per quanto riguarda i rapporti. Ad esempio, una vista con una grande quantità di spazio in 1/3 dello spazio verticale di una schermata in verticale potrebbe non rientrare in 1/3 dello spazio verticale in orizzontale.
- **Prestare attenzione con i valori assoluti** &ndash; i valori assoluti (pixel) che hanno un significato in verticale potrebbero non essere significativi in orizzontale. Quando sono necessari valori assoluti, utilizzare layout annidati per isolarne l'effetto. Ad esempio, sarebbe ragionevole usare i valori assoluti in un oggetto `TableView` `ItemTemplate` quando il modello di elemento ha un'altezza uniforme garantita.

Le regole sopra riportate si applicano anche quando si implementano interfacce per più dimensioni dello schermo e sono generalmente considerate procedure consigliate. Nella parte restante di questa guida vengono illustrati esempi specifici di layout reattivi con ognuno dei layout primari in Xamarin.Forms .

> [!NOTE]
> Per maggiore chiarezza, nelle sezioni seguenti viene illustrato come implementare layout reattivi utilizzando un solo tipo di `Layout` alla volta. In pratica, è spesso più semplice combinare `Layout` s per ottenere un layout desiderato usando il più semplice o più intuitivo `Layout` per ogni componente.

### <a name="stacklayout"></a>StackLayout

Si consideri la seguente applicazione, visualizzata in verticale:

![](device-orientation-images/photo-stack-portrait.png "Photo Application in Portrait")

e Landscape:

![](device-orientation-images/photo-stack-landscape.png "Photo Application in Landscape")

Questa operazione viene eseguita con il codice XAML seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Il linguaggio C# viene usato per modificare l'orientamento di `outerStack` in base all'orientamento del dispositivo:

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

Tenere presente quanto segue:

- `outerStack`viene regolato per presentare l'immagine e i controlli come uno stack orizzontale o verticale, a seconda dell'orientamento, per sfruttare al meglio lo spazio disponibile.

### <a name="absolutelayout"></a>AbsoluteLayout

Si consideri la seguente applicazione, visualizzata in verticale:

![](device-orientation-images/photo-abs-portrait.png "Photo Application in Portrait")

e Landscape:

![](device-orientation-images/photo-abs-landscape.png "Photo Application in Landscape")

Questa operazione viene eseguita con il codice XAML seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

Tenere presente quanto segue:

- A causa del modo in cui la pagina è stata disposta, non è necessario il codice procedurale per introdurre la velocità di risposta.
- `ScrollView`Viene utilizzato per consentire che l'etichetta sia visibile anche quando l'altezza dello schermo è inferiore alla somma delle altezze fisse dei pulsanti e dell'immagine.

### <a name="relativelayout"></a>RelativeLayout

Si consideri la seguente applicazione, visualizzata in verticale:

![](device-orientation-images/photo-rel-portrait.png "Photo Application in Portrait")

e Landscape:

![](device-orientation-images/photo-rel-landscape.png "Photo Application in Landscape")

Questa operazione viene eseguita con il codice XAML seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

Tenere presente quanto segue:

- A causa del modo in cui la pagina è stata disposta, non è necessario il codice procedurale per introdurre la velocità di risposta.
- `ScrollView`Viene utilizzato per consentire che l'etichetta sia visibile anche quando l'altezza dello schermo è inferiore alla somma delle altezze fisse dei pulsanti e dell'immagine.

### <a name="grid"></a>Grid

Si consideri la seguente applicazione, visualizzata in verticale:

![](device-orientation-images/photo-grid-portrait.png "Photo Application in Portrait")

e Landscape:

![](device-orientation-images/photo-grid-landscape.png "Photo Application in Landscape")

Questa operazione viene eseguita con il codice XAML seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

Insieme al codice procedurale seguente per gestire le modifiche alla rotazione:

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

Tenere presente quanto segue:

- A causa del modo in cui la pagina è stata disposta, è disponibile un metodo per modificare la posizione della griglia dei controlli.

## <a name="related-links"></a>Collegamenti correlati

- [Layout (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Esempio di BusinessTumble (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
- [Layout reattivo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)
- [Visualizzare un'immagine in base all'orientamento dello schermo](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
