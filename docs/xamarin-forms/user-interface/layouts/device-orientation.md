---
title: Orientamento del dispositivo
description: Questo articolo illustra come le applicazioni xamarin. Forms di layout che aspetto accattivante nell'orientamento verticale e orizzontale.
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: a57775776b32f34d2c9b976a22cc92cc22f3c879
ms.sourcegitcommit: 771e65583e978ff2b9c652b953a21b0bbb10a5d1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58782500"
---
# <a name="device-orientation"></a>Orientamento del dispositivo

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ResponsiveLayout)

È importante da considerare come verrà usato l'applicazione e come incorporarli con orientamento orizzontale per migliorare l'esperienza utente. Layout delle singole possono essere progettate per contenere vari orientamenti e migliore utilizzo dello spazio disponibile. A livello di applicazione, la rotazione può essere disabilitata o abilitata.

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>Controllo dell'orientamento

Quando si usa xamarin. Forms, il metodo supportato per il controllo dell'orientamento del dispositivo consiste nell'utilizzare le impostazioni per ogni singolo progetto.

### <a name="ios"></a>iOS

In iOS, l'orientamento del dispositivo è configurato per applicazioni che usano il **Info. plist** file. Questo file includerà le impostazioni di orientamento per iPhone e iPod, nonché le impostazioni per iPad se l'app include come destinazione. Di seguito sono istruzioni specifiche per il tuo IDE. Usare le opzioni dell'IDE all'inizio di questo documento per selezionare le istruzioni che si desidera vedere:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In Visual Studio, aprire il progetto iOS e aprire **Info. plist**. Il file verrà aperto un pannello di configurazione, iniziando dalla scheda informazioni sulla distribuzione iPhone:

![informazioni sulla distribuzione in Visual Studio iPhone](device-orientation-images/orientation-vs-iphone.png)

Per configurare l'orientamento di iPad, selezionare la **informazioni sulla distribuzione iPad** scheda nella parte superiore sinistra del pannello, quindi selezionare dagli orientamenti disponibili:

![Orientamenti dispositivo supportati in Visual Studio](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In Visual Studio per Mac, aprire il progetto iOS e aprire **Info. plist**. Sotto il **applicazione** scheda sezioni saranno disponibili per impostare l'orientamento:

![informazioni sulla distribuzione in Visual Studio per Mac iPhone](device-orientation-images/orientation-xam-ui.png)

Se si desidera modificare i valori usando un'interfaccia editor chiave-valore, selezionare la **origine**> scheda nella parte inferiore della schermata:

![Orientamenti del dispositivo è supportato in Visual Studio per Mac](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

Per controllare l'orientamento in Android, aprire **MainActivity.cs** e impostare l'orientamento usando l'attributo decorando la `MainActivity` classe:

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin. Android supporta diverse opzioni per specificare l'orientamento:

- **Panorama applicativo** &ndash; forza l'orientamento dell'applicazione in orizzontale, indipendentemente dal fatto i dati del sensore.
- **Verticale** &ndash; forza l'orientamento dell'applicazione in verticale, indipendentemente dal fatto i dati del sensore.
- **Utente** &ndash; , l'applicazione verrà visualizzato con orientamento preferito dell'utente.
- **Dietro** &ndash; provoca l'orientamento dell'applicazione sia lo stesso come l'orientamento del [attività](https://developer.xamarin.com/api/type/Android.App.Activity/) dietro di essa.
- **Sensore** &ndash; determina l'orientamento dell'applicazione deve essere determinato dal sensore, anche se l'utente ha disabilitato la rotazione automatica.
- **SensorLandscape** &ndash; fa sì che l'applicazione da usare con orientamento orizzontale quando si usa i dati dei sensori per modificare la direzione è rivolta verso la schermata (in modo che la schermata non viene considerata dall'alto in basso).
- **SensorPortrait** &ndash; fa sì che l'applicazione da usare con orientamento verticale quando si usa i dati dei sensori per modificare la direzione è rivolta verso la schermata (in modo che la schermata non viene considerata dall'alto in basso).
- **ReverseLandscape** &ndash; fa sì che l'applicazione da usare con orientamento orizzontale, rivolta nella direzione opposta dalla normale, in modo tale da visualizzato "dall'alto in basso."
- **ReversePortrait** &ndash; fa sì che l'applicazione a usare l'orientamento verticale, rivolta nella direzione opposta dalla normale, in modo tale da visualizzato "dall'alto in basso."
- **FullSensor** &ndash; fa sì che l'applicazione si affidano i dati del sensore per selezionare l'orientamento corretto (non il 4 possibili).
- **FullUser** &ndash; fa sì che l'applicazione a usare le preferenze di orientamento dell'utente. Se la rotazione automatica è abilitata, quindi tutti gli 4 orientamenti sono utilizzabile.
- **UserLandscape** &ndash; _\[non supportate\]_ fa sì che l'applicazione da usare con orientamento orizzontale, a meno che l'utente abbia la rotazione automatica abilitata, nel qual caso userà il sensori per determinare l'orientamento. Questa opzione interromperà la compilazione.
- **UserPortrait** &ndash; _\[non supportate\]_ fa sì che l'applicazione a usare l'orientamento verticale, a meno che l'utente abbia la rotazione automatica abilitata, nel qual caso userà il sensori per determinare l'orientamento. Questa opzione interromperà la compilazione.
- **Bloccato** &ndash; _\[non supportate\]_ fa sì che l'applicazione per l'uso dell'orientamento dello schermo, qualsiasi elemento è al momento del lancio, senza rispondere alle modifiche nel dispositivo di fisica orientamento. Questa opzione interromperà la compilazione.

Si noti che le API di Android native fornire un grande controllo sulle modalità di gestione dell'orientamento, incluse le opzioni che siano in contraddizione dell'utente in modo esplicito tra espresso preferenze.

### <a name="universal-windows-platform"></a>Piattaforma Windows universale

In Universal Windows Platform (UWP), gli orientamenti supportati sono impostati **package. appxmanifest** file. Aprendo il manifesto verrà visualizzato un pannello di configurazione in cui è possibile selezionare gli orientamenti supportati.

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>Reazione alle modifiche apportate orientamento

Xamarin. Forms non offre tutti gli eventi nativi per inviare la notifica delle modifiche apportate orientamento nel codice condiviso l'app. Tuttavia, il `SizeChanged` eventi del `Page` viene attivato quando la larghezza o altezza del `Page` le modifiche. Quando la larghezza del `Page` è superiore all'altezza, il dispositivo è in modalità orizzontale. Per altre informazioni, vedere [visualizzare un'immagine di base dell'orientamento dello schermo](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation).

> [!NOTE]
> È un pacchetto NuGet esistente, gratuito per la ricezione di notifiche delle modifiche apportate orientamento nel codice condiviso. Vedere le [repository GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) per altre informazioni.

In alternativa, è possibile eseguire l'override di [ `OnSizeAllocated` ](xref:Xamarin.Forms.Page.OnSizeAllocated*) metodo su un `Page`, inserimento di qualsiasi layout modificare qui la logica. Il `OnSizeAllocated` viene chiamato ogni volta che un `Page` viene allocata una nuova dimensione, che si verifica ogni volta che il dispositivo viene ruotato. Si noti che l'implementazione di base di `OnSizeAllocated` esegue funzioni layout importante, pertanto è importante chiamare l'implementazione di base nell'override:

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

Errore di eseguire questo passaggio comporterà una pagina non funzionante.

Si noti che il `OnSizeAllocated` metodo può essere chiamato più volte quando un dispositivo viene ruotato. La modifica del layout ogni volta che sarebbe uno spreco di risorse e può causare lo sfarfallio. È consigliabile usare una variabile di istanza all'interno della pagina per rilevare se l'orientamento è in orizzontale o verticale e riemettere solo quando viene apportata una modifica:

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

Dopo che è stata rilevata una modifica nell'orientamento del dispositivo, è possibile aggiungere o rimuovere visualizzazioni aggiuntive da e verso l'interfaccia utente per rispondere alla modifica nello spazio disponibile. Si consideri, ad esempio, la calcolatrice incorporata in ogni piattaforma in formato verticale:

![](device-orientation-images/calculator-portrait.png "Applicazione Calculator in formato verticale")

e orizzontale:

![](device-orientation-images/calculator-landscape.png "Applicazione Calculator in formato orizzontale")

Si noti che le app di sfruttano i vantaggi dello spazio disponibile mediante l'aggiunta di ulteriori funzionalità in modalità orizzontale.

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>Layout reattivo

È possibile per le interfacce di progettazione utilizzando il layout predefinito in modo che eseguono la transizione normalmente quando il dispositivo viene ruotato. Quando si progettano le interfacce che continueranno a essere interessante quando si risponde alle modifiche apportate orientamento considerare le regole generali seguenti:

- **Prestare attenzione ai rapporti** &ndash; le modifiche apportate orientamento possono causare problemi quando vengono apportate alcune ipotesi per quanto riguarda i rapporti. Ad esempio, una vista che potrebbe avere una notevole quantità di spazio in 1 o 3 dello spazio verticale di una schermata in formato verticale potrebbe non rientrare nello 1/3 lo spazio verticale nel panorama applicativo.
- **Prestare attenzione con i valori assoluti** &ndash; valori assoluti (pixel) che hanno un significato in formato verticale possono non avere senso in modalità orizzontale. Quando sono necessari i valori assoluti, usare layout annidati per isolare il relativo impatto. Ad esempio, sarebbe ragionevole utilizzati valori assoluti in una `TableView` `ItemTemplate` quando il modello di elemento ha un'altezza uniforme garantita.

Le regole precedenti si applicano anche quando l'implementazione di interfacce per più dimensioni dello schermo e sono in genere considerati procedure consigliate. Il resto di questa guida illustra esempi specifici di layout reattivo utilizzando tutti i layout primari in xamarin. Forms.

> [!NOTE]
> Per maggiore chiarezza, le sezioni seguenti illustrano come implementare il layout reattivo usando solo uno dei tipi `Layout` alla volta. In pratica, è spesso più semplice combinare `Layout`s per ottenere un layout desiderato usando il più semplice o più intuitivo `Layout` per ciascun componente.

### <a name="stacklayout"></a>StackLayout

Si consideri la seguente applicazione, visualizzata in verticale:

![](device-orientation-images/photo-stack-portrait.png "Applicazione di foto in formato verticale")

e orizzontale:

![](device-orientation-images/photo-stack-landscape.png "Applicazione di foto in formato orizzontale")

Che viene eseguita con il XAML seguente:

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

Alcuni c# viene usato per modificare l'orientamento di `outerStack` in base all'orientamento del dispositivo:

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

- `outerStack` viene regolata per presentare l'immagine e i controlli come uno stack orizzontale o verticale a seconda di orientamento, per sfruttare al meglio lo spazio disponibile.


### <a name="absolutelayout"></a>AbsoluteLayout

Si consideri la seguente applicazione, visualizzata in verticale:

![](device-orientation-images/photo-abs-portrait.png "Applicazione di foto in formato verticale")

e orizzontale:

![](device-orientation-images/photo-abs-landscape.png "Applicazione di foto in formato orizzontale")

Che viene eseguita con il XAML seguente:

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

- A causa della modalità che è stato disposto la pagina, non è necessario per il codice procedurale per introdurre la velocità di risposta.
- Il `ScrollView` viene usato per consentire l'etichetta sia visibile anche quando l'altezza dello schermo è inferiore alla somma delle altezze fisse dei pulsanti e l'immagine.


### <a name="relativelayout"></a>RelativeLayout

Si consideri la seguente applicazione, visualizzata in verticale:

![](device-orientation-images/photo-rel-portrait.png "Applicazione di foto in formato verticale")

e orizzontale:

![](device-orientation-images/photo-rel-landscape.png "Applicazione di foto in formato orizzontale")

Che viene eseguita con il XAML seguente:

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

- A causa della modalità che è stato disposto la pagina, non è necessario per il codice procedurale per introdurre la velocità di risposta.
- Il `ScrollView` viene usato per consentire l'etichetta sia visibile anche quando l'altezza dello schermo è inferiore alla somma delle altezze fisse dei pulsanti e l'immagine.

### <a name="grid"></a>Grid

Si consideri la seguente applicazione, visualizzata in verticale:

![](device-orientation-images/photo-grid-portrait.png "Applicazione di foto in formato verticale")

e orizzontale:

![](device-orientation-images/photo-grid-landscape.png "Applicazione di foto in formato orizzontale")

Che viene eseguita con il XAML seguente:

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

Oltre al codice contenente le procedure seguente per gestire le modifiche di rotazione:

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

- A causa della modalità che è stato disposto la pagina, è disponibile un metodo per modificare il posizionamento nella griglia dei controlli.


## <a name="related-links"></a>Collegamenti correlati

- [Layout (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Esempio BusinessTumble (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
- [Layout reattivo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ResponsiveLayout)
- [Visualizzare un'immagine di base dell'orientamento dello schermo](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
