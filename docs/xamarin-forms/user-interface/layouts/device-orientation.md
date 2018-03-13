---
title: Orientamento del dispositivo
description: Comprendere il layout di applicazioni che aspetto in orientamento verticale e orizzontale.
ms.topic: article
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: 8b266640bb0e1aa2bc584197e5fd7cbf4ab48e88
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="device-orientation"></a>Orientamento del dispositivo

È importante considerare come verrà usato l'applicazione e come l'orientamento orizzontale può essere incorporata per migliorare l'esperienza utente. Layout di singoli possono essere progettate per supportare più orientamenti e migliore utilizzo dello spazio disponibile. A livello di applicazione, la rotazione può essere disabilitata o abilitata.

In questo articolo verrà illustrata la creazione di applicazioni che sfruttano le funzionalità orientamento del dispositivo e include le sezioni seguenti:

- **[Controllo dell'orientamento](#Controlling_Orientation)**  &ndash; sapere controllare l'orientamento a livello di app in ciascuna piattaforma.
- **[Reazione alle modifiche nell'orientamento](#Reacting_to_Changes_in_Orientation)**  &ndash; imparare ricevere una notifica di e reagire a, cambia l'orientamento.
- **[Layout reattivo](#Responsive_Layout)**  &ndash; informazioni su come creare layout di lavoro automaticamente tra gli orientamenti orizzontale e verticale.

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>Controllare l'orientamento della

Quando si usa xamarin. Forms, il metodo supportato per il controllo dell'orientamento del dispositivo è di utilizzare le impostazioni per ogni singolo progetto.

> [!NOTE]
> A partire da xamarin. Forms 1.5.0 è presente un bug che impedisce personalizzato basato su renderer tenta di controllare l'orientamento esito negativo. Vedere [questa discussione](https://forums.xamarin.com/discussion/46653/forcing-landscape-for-a-single-page-in-ios#latest)questa discussione nei forum di Xamarin per ulteriori informazioni.

### <a name="ios"></a>iOS

In iOS, l'orientamento del dispositivo è configurato per applicazioni che utilizzano il **Info. plist** file. Questo file includerà le impostazioni di orientamento per iPhone e iPod, nonché le impostazioni per iPad se l'applicazione sono inclusi come destinazione. Di seguito sono istruzioni specifiche per l'IDE. Utilizzare le opzioni di IDE all'inizio di questo documento per selezionare le istruzioni che si desidera vedere:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

In Visual Studio, aprire il progetto iOS e **Info. plist**. Il file verrà aperto in un pannello di configurazione, a partire dalla scheda le informazioni sulla distribuzione iPhone:

![iPhone le informazioni sulla distribuzione in Visual Studio](device-orientation-images/orientation-vs-iphone.png)

Per configurare l'orientamento iPad, selezionare il **iPad le informazioni sulla distribuzione** scheda nella parte superiore sinistra del riquadro, quindi selezionare dagli orientamenti disponibili:

![Orientamenti di dispositivi supportati in Visual Studio](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

In Visual Studio per Mac, aprire il progetto iOS e **Info. plist**. Sotto il **applicazione** scheda sezioni saranno disponibili per impostare l'orientamento:

![le informazioni sulla distribuzione in Visual Studio per Mac iPhone](device-orientation-images/orientation-xam-ui.png)

Se si desidera modificare i valori usando un'interfaccia editor chiave-valore, selezionare il **origine**> scheda nella parte inferiore dello schermo:

![Orientamenti di dispositivo è supportata in Visual Studio per Mac](device-orientation-images/orientation-xam-source.png)

-----


### <a name="android"></a>Android

Per controllare l'orientamento in Android, aprire **Mainactivity** e impostare l'orientamento utilizzando l'attributo decorando la `MainActivity` classe:

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin supporta diverse opzioni per specificare l'orientamento:

- **Orizzontale** &ndash; forza l'orientamento dell'applicazione in orizzontale, indipendentemente dal fatto di dati del sensore.
- **Verticale** &ndash; forza l'orientamento dell'applicazione in verticale, indipendentemente dal fatto di dati del sensore.
- **Utente** &ndash; , l'applicazione verrà visualizzato con orientamento preferito dell'utente.
- **Dietro** &ndash; determina l'orientamento dell'applicazione corrispondere all'orientamento del [attività](https://developer.xamarin.com/api/type/Android.App.Activity/) sottostante.
- **Sensore** &ndash; determina l'orientamento dell'applicazione venga determinata dal sensore, anche se l'utente abbia disabilitato la rotazione automatica.
- **SensorLandscape** &ndash; fa sì che l'applicazione da utilizzare con orientamento orizzontale durante l'utilizzo di dati del sensore di modificare la direzione verso la schermata (in modo che la schermata non è considerata dall'alto in basso).
- **SensorPortrait** &ndash; fa sì che l'applicazione da utilizzare con orientamento verticale durante l'utilizzo di dati del sensore di modificare la direzione verso la schermata (in modo che la schermata non è considerata dall'alto in basso).
- **ReverseLandscape** &ndash; fa sì che l'applicazione da utilizzare con orientamento orizzontale, rivolta nella direzione opposta di solito, in modo che vengono visualizzati "dall'alto in basso."
- **ReversePortrait** &ndash; fa sì che l'applicazione da utilizzare con orientamento verticale, rivolta nella direzione opposta di solito, in modo che vengono visualizzati "dall'alto in basso."
- **FullSensor** &ndash; causa dell'applicazione si basano su dati del sensore di selezionare l'orientamento corretto (non più di 4 possibili).
- **FullUser** &ndash; fa sì che l'applicazione per utilizzare le preferenze dell'utente orientamento. Se la rotazione automatica è abilitata, è possono utilizzare tutti gli 4 orientamenti.
- **UserLandscape** &ndash;  _\[non supportato\]_  fa sì che l'applicazione da utilizzare con orientamento orizzontale, a meno che l'utente abbia la rotazione automatica abilitata, nel qual caso utilizzerà il sensore per determinare l'orientamento. Questa opzione interromperà la compilazione.
- **UserPortrait** &ndash;  _\[non supportato\]_  fa sì che l'applicazione da utilizzare con orientamento verticale, a meno che l'utente abbia la rotazione automatica abilitata, nel qual caso utilizzerà il sensore per determinare l'orientamento. Questa opzione interromperà la compilazione.
- **Bloccato** &ndash;  _\[non supportato\]_  fa sì che l'applicazione per l'utilizzo dell'orientamento dello schermo, l'elemento è all'avvio, senza risposta alle modifiche nel dispositivo del fisico orientamento. Questa opzione interromperà la compilazione.

Si noti che le API di Android native offrono un grande controllo sulle modalità di gestione dell'orientamento, incluse le opzioni che in modo esplicito in contraddizione con l'utente espresso preferenze.

### <a name="windows-phone"></a>Windows Phone

In Windows Phone RT, gli orientamenti supportati sono impostati <span class="UIItem">package. appxmanifest</span> file. Aprendo il manifesto verrà visualizzato un pannello di configurazione in cui è possibile selezionare gli orientamenti supportati:

![](device-orientation-images/vs-winrt-config.png "Editor di Visual package. appxmanifest")

In Windows Phone 8 (Silverlight), gli orientamenti supportati sono impostati nel codice il <span class="UIItem">MainPage.xaml.cs</span> file. Nel modello di progetto predefinito, il valore è già impostato con la riga di codice seguente:

```csharp
SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;
```

Per specificare le opzioni di orientamento in Windows Phone, sostituirlo con il codice per attivare gli orientamenti desiderato:

```csharp
SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;
SupportedOrientations = SupportedPageOrientation.Portrait; // portrait only
SupportedOrientations = SupportedPageOrientation.Landscape; // landscape only
```

Si noti che Windows Phone supporta viste orizzontale in entrambi (come illustrato da verticale) gli orientamenti da sinistra a destra e a destra a sinistra. Non è possibile specificare che viene utilizzato.

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>Reazione alle modifiche di orientamento

Xamarin. Forms non offre tutti gli eventi nativi per la notifica dell'app di modifiche di orientamento in codice condiviso. Tuttavia, il `SizeChanged` evento del `Page` viene generato quando la larghezza o altezza il `Page` modifiche. Quando la larghezza del `Page` è maggiore rispetto all'altezza, il dispositivo è in modalità orizzontale. Per ulteriori informazioni, vedere [visualizzare un'immagine basata su orientamento dello schermo](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/screen-orientation/).

> [!NOTE]
> È un pacchetto NuGet gratuito, esistente per la ricezione di notifiche di modifiche di orientamento nel codice condiviso. Vedere il [repository GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) per ulteriori informazioni.

In alternativa, è possibile eseguire l'override di [ `OnSizeAllocated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnSizeAllocated(System.Double,System.Double)/) metodo su un `Page`, l'inserimento di qualsiasi layout modifica qui la logica. Il `OnSizeAllocated` metodo viene chiamato ogni volta che un `Page` viene allocata una nuova dimensione che si verifica il dispositivo viene ruotato whenver. Si noti che l'implementazione di base di `OnSizeAllocated` esegue le funzioni di layout importante, pertanto è importante chiamare l'implementazione di base nell'override:

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

Se non si esegue questa operazione comporterà una pagina non funzionante.

Si noti che il `OnSizeAllocated` metodo può essere chiamato più volte quando un dispositivo viene ruotato. Modifica il layout di ogni volta che è dispendiosa delle risorse e può causare lo sfarfallio. È consigliabile usare una variabile di istanza all'interno della pagina per rilevare se l'orientamento è in orizzontale o verticale e ridisegnare solo quando viene apportata una modifica:

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

Una volta che è stata rilevata una modifica in orientamento del dispositivo, si desidera aggiungere o rimuovere visualizzazioni aggiuntive a/da interfaccia utente per rispondere alla modifica dello spazio disponibile. Si consideri ad esempio la calcolatrice incorporata in ogni piattaforma verticale:

![](device-orientation-images/calculator-portrait.png "Applicazione Calculator in formato verticale")

e orizzontale:

![](device-orientation-images/calculator-landscape.png "Applicazione Calculator in formato orizzontale")

Si noti che le app sfruttano lo spazio disponibile tramite l'aggiunta di ulteriori funzionalità in orizzontale.

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>Layout reattivo

È possibile per le interfacce di progettazione utilizzando il layout predefinito, in modo che viene completata la transizione normalmente quando il dispositivo viene ruotato. Quando si progettano le interfacce che continueranno a essere interessante per la risposta alle modifiche nell'orientamento considerare le regole generali seguenti:

- **Prestare attenzione a rapporti** &ndash; modifiche orientamento possono causare problemi quando vengono apportati alcuni presupposti per quanto riguarda i rapporti. Ad esempio, una vista che avrebbe una notevole quantità di spazio di 1/3 dello spazio verticale dello schermo in formato verticale potrà non rientrare nel 1/3 di spazio verticale in orizzontale.
- **Prestare attenzione ai valori assoluti** &ndash; potrebbero non essere accettabili valori assoluto (pixel) che ha senso verticale in orizzontale. Quando sono necessari i valori assoluti, utilizzare layout nidificati per isolare l'impatto. Ad esempio, sarebbe ragionevole utilizzare valori assoluti un `TableView` `ItemTemplate` quando il modello di elemento con un'altezza di uniform garantita.

Le regole precedenti si applicano anche quando l'implementazione di interfacce per più dimensioni dello schermo e sono in genere considerati procedure consigliate. Il resto di questa Guida verrà descritti esempi specifici di layout reattivo utilizzando tutti i layout primari in xamarin. Forms.

> [!NOTE]
> Per maggiore chiarezza, nelle sezioni seguenti viene illustrato come implementare layout risponde con un solo tipo di `Layout` alla volta. In pratica, è spesso più semplice combinare `Layout`s per ottenere un layout desiderato utilizzando le più semplici e più intuitiva `Layout` per ogni componente.

### <a name="stacklayout"></a>StackLayout

Si consideri la seguente applicazione, visualizzata in formato verticale:

![](device-orientation-images/photo-stack-portrait.png "Applicazione di foto in formato verticale")

e orizzontale:

![](device-orientation-images/photo-stack-landscape.png "Applicazione di foto in formato orizzontale")

Che viene eseguita con il codice XAML seguente:

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

Alcuni c# viene utilizzata per modificare l'orientamento di `outerStack` in base all'orientamento del dispositivo:

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

- `outerStack` viene regolato per presentare i controlli e l'immagine come uno stack orizzontale o verticale a seconda di orientamento, meglio in modo da sfruttare lo spazio disponibile.


### <a name="absolutelayout"></a>AbsoluteLayout

Si consideri la seguente applicazione, visualizzata in formato verticale:

![](device-orientation-images/photo-abs-portrait.png "Applicazione di foto in formato verticale")

e orizzontale:

![](device-orientation-images/photo-abs-landscape.png "Applicazione di foto in formato orizzontale")

Che viene eseguita con il codice XAML seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImage="deer.jpg">
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

- A causa della modalità layout di pagina i, non è necessario per il codice procedurale per introdurre una velocità di risposta.
- Il `ScrollView` viene utilizzato per consentire l'etichetta sia visibile anche quando l'altezza dello schermo è inferiore alla somma delle altezze fisse dei pulsanti e l'immagine.


### <a name="relativelayout"></a>RelativeLayout

Si consideri la seguente applicazione, visualizzata in formato verticale:

![](device-orientation-images/photo-rel-portrait.png "Applicazione di foto in formato verticale")

e orizzontale:

![](device-orientation-images/photo-rel-landscape.png "Applicazione di foto in formato orizzontale")

Che viene eseguita con il codice XAML seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImage="deer.jpg">
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

- A causa della modalità layout di pagina i, non è necessario per il codice procedurale per introdurre una velocità di risposta.
- Il `ScrollView` viene utilizzato per consentire l'etichetta sia visibile anche quando l'altezza dello schermo è inferiore alla somma delle altezze fisse dei pulsanti e l'immagine.

### <a name="grid"></a>Grid

Si consideri la seguente applicazione, visualizzata in formato verticale:

![](device-orientation-images/photo-grid-portrait.png "Applicazione di foto in formato verticale")

e orizzontale:

![](device-orientation-images/photo-grid-landscape.png "Applicazione di foto in formato orizzontale")

Che viene eseguita con il codice XAML seguente:

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

Con il seguente codice procedura per gestire le modifiche di rotazione:

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

- A causa della modalità layout di pagina i, è disponibile un metodo per modificare la posizione della griglia dei controlli.


## <a name="related-links"></a>Collegamenti correlati

- [Layout (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Esempio BusinessTumble (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
- [Layout reattivo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ResponsiveLayout)
- [Visualizzare un'immagine basata su orientamento dello schermo](https://developer.xamarin.comhttps://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/screen-orientation/)
