---
title: Xamarin.Forms Shell
description: Questo articolo spiega come usare Xamarin.Forms Shell, che riduce la complessità delle applicazioni Xamarin.Forms offrendo le principali funzionalità dell'interfaccia utente richieste dalla maggior parte delle applicazioni.
ms.prod: xamarin
ms.assetid: 1A674212-72DB-4AA4-B626-A4EC135AD1A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2018
ms.openlocfilehash: 422311c766584cbd27d0ab0c42adee042e9aac3e
ms.sourcegitcommit: 408b78dd6eded4696469e316af7922a5991f2211
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53246295"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

![Anteprima](~/media/shared/preview.png)

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/Microsoft/TailwindTraders-Mobile)

Xamarin.Forms Shell è un contenitore per applicazioni, che rende disponibili le principali funzionalità dell'interfaccia utente richieste dalla maggior parte delle applicazioni, consentendo all'utente di concentrarsi sul carico di lavoro essenziale dell'applicazione. Le applicazioni Shell includono le funzionalità seguenti:

- Un'unica posizione per descrivere la struttura visiva di un'applicazione.
- Un'interfaccia utente comune per la navigazione.
- Un servizio di navigazione con deep linking.
- Un gestore integrato per la ricerca.

Questa funzionalità riduce la complessità delle applicazioni, aumentando la produttività degli sviluppatori. Inoltre, Shell viene scritto tenendo presenti la velocità di rendering e il consumo di memoria.

> [!IMPORTANT]
> Le applicazioni Android e iOS esistenti possono adottare Shell usufruendo immediatamente dei miglioramenti in termini di navigazione, prestazioni ed estendibilità.

Shell offre un'interfaccia utente di navigazione vincolante, basata su schede e riquadri a comparsa. Il livello superiore di navigazione in un'applicazione Shell è un riquadro a comparsa:

![Riquadro a comparsa](shell-images/flyout.png "Riquadro a comparsa")

Il livello di navigazione successivo è la barra di schede inferiore:

![Schede inferiori](shell-images/bottom-tabs-full.png "Schede inferiori")

Quando il riquadro a comparsa non è aperto la barra di schede inferiore viene considerata il livello di navigazione superiore

All'interno di ogni scheda inferiore il livello di navigazione successivo è la barra di schede superiore, dove ogni scheda è un oggetto `ContentPage`:

![Schede superiori](shell-images/top-tabs-full.png "Schede superiori")

All'interno di ogni `ContentPage` è possibile aggiungere e rimuovere istanze aggiuntive `ContentPage` allo e dallo stack di navigazione.

## <a name="bootstrapping-a-shell-application"></a>Bootstrap di un'applicazione Shell

Il bootstrap di un'applicazione Shell viene eseguito impostando la proprietà `MainPage` della classe `App` su un'istanza di un file Shell:

```csharp
namespace TailwindTraders.Mobile
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();

            Forms.SetFlags("Shell_Experimental");
            MainPage = new TheShell();
        }
    }
}
```

La classe `TheShell` è un file XAML che descrive la struttura visiva dell'applicazione.

> [!IMPORTANT]
> Shell è attualmente in fase di sperimentazione e può essere usato solo aggiungendo `Forms.SetFlags("Shell_Experimental");` alla classe `App`, prima di creare l'istanza di `Shell`, o al progetto di piattaforma, prima di richiamare il metodo `Forms.Init`.

## <a name="shell-file-hierarchy"></a>Gerarchia dei file di Shell

Un file di Shell è costituito da tre elementi gerarchici:

- `ShellItem`. Uno o più elementi nel riquadro a comparsa. Ogni `ShellItem` è figlio di un elemento `Shell`.
- `ShellSection`. Contenuto raggruppato, in cui si può navigare dalle schede inferiori. Ogni `ShellSection` è figlio di un elemento `ShellItem`.
- `ShellContent`. Le istanze di `ContentPage` nell'applicazione, in cui si può navigare dalle schede superiori. Ogni `ShellContent` è figlio di un elemento `ShellSection`.

Nessuno di questi elementi rappresenta un'interfaccia utente, ma piuttosto l'organizzazione della struttura visiva dell'applicazione. Shell accetta questi elementi e genera l'interfaccia utente di navigazione per il contenuto.

Il codice XAML seguente mostra un esempio semplice di file Shell:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Mobile.Features.Shell"
       x:Class="TailwindTraders.Mobile.Features.Shell.TheShell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    </ShellItem>
</Shell>
```

> [!NOTE]
> Ogni `ShellItem` può inoltre impostare una proprietà `FlyoutIcon` su un elemento `ImageSource`, che verrà visualizzato a sinistra del titolo.

Questo codice XAML visualizza `HomePage`, perché è il primo elemento del contenuto dichiarato nel file di Shell:

![Home page](shell-images/homepage.png "Home page")

Premendo il pulsante hamburger viene visualizzato il riquadro a comparsa:

![Riquadro a comparsa aperto](shell-images/flyout-initial.png "Riquadro a comparsa aperto")

Il numero di elementi nel riquadro a comparsa può essere aumentato aggiungendo altre istanze di `ShellItem` al file di Shell. Si noti tuttavia che `HomePage` viene creato durante l'avvio dell'applicazione e l'aggiunta di ulteriori istanze di `ShellItem` usando questo approccio causa la creazione di queste pagine anche durante l'avvio dell'applicazione. Per evitare questo inconveniente, impostare la proprietà `ShellContent.ContentTemplate` su `DataTemplate`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Mobile.Features.Shell"
       x:Class="TailwindTraders.Mobile.Features.Shell.TheShell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    <ShellItem>
    <ShellContent Title="Profile"
                  ContentTemplate="{DataTemplate local:ProfilePage}" />
</Shell>
```

Con questo approccio, `ProfilePage` viene creato solo quando l'utente passa all'elemento anziché all'avvio dell'applicazione. Inoltre, si noti che per `ProfilePage`, gli oggetti `ShellItem` e `ShellSection` vengono omessi, con la proprietà `Title` definita nell'istanza di `ShellContent`. Questo approccio conciso richiede meno markup poiché Shell rende disponibili i wrapper logici necessari, senza introdurre viste aggiuntive alla struttura ad albero visuale.

Inoltre, l'aspetto di ogni `ShellItem` può essere personalizzato impostando la proprietà `Shell.ItemTemplate` su un elemento `DataTemplate`:

```xaml
<Shell.ItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Title}" />
        </ContentView>
    </DataTemplate>
</Shell.ItemTemplate>          
```

Questo codice si limita a riposizionare il testo per ogni `ShellItem` nel riquadro a comparsa. Si noti che Shell specifica le proprietà `Title` (e `Icon`) per l'elemento `BindingContext` di `DataTemplate`.

## <a name="flyout"></a>Flyout

Il riquadro a comparsa è il menu radice per l'applicazione ed è costituito da un'intestazione del riquadro e da voci di menu:

![Riquadro a comparsa annotato](shell-images/flyout-annotated.png "Riquadro a comparsa annotato")

### <a name="flyout-behavior"></a>Comportamento del riquadro a comparsa

Per accedere al riquadro a comparsa, usare il pulsante hamburger o lo scorrimento rapido dal lato dello schermo. Tuttavia, questo comportamento può essere modificato impostando la proprietà `Shell.FlyoutBehavior` su uno dei membri dell'enumerazione `FlyoutBehavior`:

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

Impostando la proprietà `FlyoutBehavior` su `Disabled` si nasconde il riquadro a comparsa e questo risulta utile quando è presente un solo `ShellItem`. Gli altri valori validi di `FlyoutBehavior` sono `Flyout` (predefinito) e `Locked`.

### <a name="flyout-header"></a>Intestazione del riquadro a comparsa

L'intestazione del riquadro a comparsa è il contenuto che facoltativamente appare nella parte superiore del riquadro e il cui aspetto viene definito da un elemento `View` che si può impostare usando il valore della proprietà `Shell.FlyoutHeader`:

```xaml
<Shell.FlyoutHeader>
    <local:FlyoutHeader />
</Shell.FlyoutHeader>
```

In alternativa, l'aspetto dell'intestazione del riquadro a comparsa può essere definito impostando la proprietà `Shell.FlyoutHeaderTemplate` su un elemento `DataTemplate`:

```xaml
<Shell.FlyoutHeaderTemplate>
    <DataTemplate>
        <StackLayout HorizontalOptions="Fill"
                     VerticalOptions="Fill"
                     BackgroundColor="White"
                     Padding="16">
            <Label FontSize="Medium"
                   Text="Smart Shopping">
                <Label.Margin>
                    <Thickness Left="8" />
                </Label.Margin>
            </Label>
            <Button Image="photo"
                    Text="By taking a photo">
                <Button.Margin>
                    <Thickness Top="16" />
                </Button.Margin>
            </Button>
            <Button Image="ia"
                    Text="By using AR">
                <Button.Margin>
                    <Thickness Top="8" />
                </Button.Margin>
            </Button>
        </StackLayout>
    </DataTemplate>
</Shell.FlyoutHeaderTemplate>
```

Questo codice XAML genera la seguente intestazione del riquadro a comparsa:

![Intestazione del riquadro a comparsa](shell-images/flyout-header.png "Intestazione del riquadro a comparsa")

Per impostazione predefinita, l'intestazione del riquadro a comparsa è fissa nel riquadro mentre il contenuto sottostante scorre se sono presenti elementi a sufficienza. Tuttavia, questo comportamento può essere modificato impostando la proprietà `Shell.FlyoutHeaderBehavior` su uno dei membri dell'enumerazione `FlyoutHeaderBehavior`:

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

Impostando `FlyoutHeaderBehavior` su `CollapseOnScroll` il riquadro a comparsa si comprime quando avviene lo scorrimento. Altri valori validi di `FlyoutHeaderBehavior` sono `Default`, `Fixed` e `Scroll` (scorrimento con le voci di menu).

## <a name="menu-items"></a>Voci di menu

Il numero di elementi nel riquadro a comparsa può essere aumentato aggiungendo altre istanze di `ShellItem`. Tuttavia, è anche possibile aggiungere istanze di `MenuItem` al riquadro a comparsa. Ciò consente scenari come il passaggio a una pagina identica durante il passaggio di dati attraverso la proprietà `MenuItem.CommandParameter`.

Le istanze di `MenuItem` devono essere aggiunte alla raccolta `Shell.MenuItems`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Views"
       x:Class="TailwindTraders.Shell"
       FlyoutHeaderBehavior="Fixed"
       Title="Tailwind Traders"
       ...>
    <Shell.MenuItems>
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="1"
                  Text="Holiday decorations" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="2"
                  Text="Appliances" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="3"
                  Text="Bathrooms" />
        ...
    </Shell.MenuItems>
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    </ShellItem>    
</Shell>
```

> [!NOTE]
> Ogni `MenuItem` può inoltre impostare una proprietà `Icon` su un elemento `FileImageSource`, che verrà visualizzato a sinistra del testo.

Questo codice XAML genera il seguente riquadro a comparsa:

![Riquadro a comparsa completo](shell-images/flyout-full.png "Riquadro a comparsa completo")

Inoltre, l'aspetto di `MenuItem` può essere personalizzato impostando la proprietà `Shell.MenuItemTemplate` su un elemento `DataTemplate`:

```xaml
<Shell.MenuItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Text}"
                   FontAttributes="Bold" />
        </ContentView>
    </DataTemplate>
</Shell.MenuItemTemplate>
```

Ciò consente di generare istanze di `MenuItem` con il testo visualizzato in grassetto:

![Voci di menu in grassetto](shell-images/menuitems-bold.png "Voci di menu in grassetto")

## <a name="tabs"></a>Schede

Le istanze di `ShellSection` verranno visualizzate come schede inferiori, purché siano presenti più istanze di `ShellSection` in un singolo `ShellItem`:

```xaml
<ShellItem Title="Bottom Tab Sample"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="AR" Icon="ia.png">
        <ShellContent ContentTemplate="{DataTemplate local:ARPage}"/>
    </ShellSection>
    <ShellSection Title="Photo" Icon="photo.png">
        <ShellContent ContentTemplate="{DataTemplate local:PhotoPage}"/>
    </ShellSection>
</ShellItem>
```

In questo esempio le istanze di `ShellSection` verranno sottoposte a rendering come schede inferiori:

![Schede inferiori](shell-images/tabs-bottom.png "Schede inferiori")

Gli elementi `ShellContent` verranno visualizzati come schede superiori, purché siano presenti più istanze di `ShellContent` in un singolo `ShellSection`:

```xaml
<ShellItem Title="Store Home"
           Shell.TitleView="Store Home"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="Browse Product">
        <ShellContent Title="Featured"
                      ContentTemplate="{DataTemplate local:FeaturedPage}" />
        <ShellContent Title="On Sale"
                      ContentTemplate="{DataTemplate local:SalePage}" />
    </ShellSection>
</ShellItem>
```

In questo esempio le istanze di `ShellContent` verranno sottoposte a rendering come schede superiori:

![Schede superiori](shell-images/tabs-top.png "Schede superiori")

Le schede possono essere personalizzate usando gli stili XAML oppure specificando un renderer personalizzato. Ad esempio, di seguito è riportato uno stile XAML che imposta il colore della scheda:

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.ShellTabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.ShellTabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.ShellTabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

## <a name="navigation"></a>Navigazione

Shell include un'esperienza di navigazione basata su URI. Gli URI offrono un'esperienza di navigazione ottimizzata che consente di passare a qualsiasi pagina nell'applicazione senza dover seguire una gerarchia di navigazione specifica. Offre inoltre la possibilità di navigare all'indietro senza dover visitare tutte le pagine dello stack di navigazione.

La navigazione basata su URI viene eseguita con le route, ovvero i segmenti di URI usati per spostarsi all'interno dell'applicazione. Il file Shell deve dichiarare uno schema di route, un host di route e una route:

```xaml
<Shell ...
       Route="tailwindtraders"
       RouteHost="www.microsoft.com"
       RouteScheme="app">
    ...
</Shell>
```

Combinati tra loro, i valori della proprietà `RouteScheme`, `RouteHost` e `Route` formano l'URI principale di `app://www.microsoft.com/tailwindtraders`.

Ogni elemento nel file Shell può anche definire una proprietà di route che può essere usata nella navigazione a livello di codice.

Nel costruttore del file Shell, o in qualsiasi altra posizione eseguita prima che venga richiamata una route, è possibile registrare in modo esplicito altre route per tutte le pagine che non sono rappresentate da un elemento Shell (ad esempio le istanze di `MenuItem`):

```csharp
Routing.RegisterRoute("productcategory", typeof(ProductCategoryPage));
```

### <a name="implementing-navigation"></a>Implementazione della navigazione

Le voci di menu espongono una proprietà `Command` che può essere usata per implementare la navigazione:

```csharp
public ICommand ProductTypeCommand { get; } = new Command<string>(NavigateToProductType);

static void NavigateToProductType(string typeId)
{
  (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"app:///tailwindtraders/productcategory?id={typeId}", true);
}
```

Per richiamare la navigazione, è necessario ottenere un riferimento all'applicazione `Shell`, attraverso la proprietà `MainPage` della classe `Application`. Quindi, la navigazione può essere richiamata chiamando il metodo `GoToAsync`, passando un URI valido come argomento. Il metodo `GoToAsync` consente di navigare usando un oggetto `ShellNavigationState`, che verrà creato da un elemento `string` o `Uri`.

### <a name="passing-data"></a>Passaggio di dati

I dati possono essere passati tra le pagine usando i parametri della stringa di query. Shell imposta i valori dei parametri della stringa di query su `ContentPage` o ViewModel quando si aggiungono gli attributi della proprietà query alla classe:

```csharp
[QueryProperty("TypeID", "id")]
public partial class ProductCategoryPage : ContentPage
{
    private string _typeId;

    public ProductCategoryPage()
    {
        InitializeComponent();

        BindingContext = new ProductCategoryViewModel();
    }

    public string TypeID
    {
        get => _typeId;
        set => MyLabel.Text = value;
    }
}
```

L'attributo `QueryProperty` specifica che `TypeID` riceverà i dati passati nel parametro della stringa di query `id` dall'URI nella chiamata al metodo `GoToAsync`.

### <a name="intercepting-navigation"></a>Intercettazione della navigazione

Shell offre la possibilità di effettuare l'hook nel ciclo di navigazione prima che venga completato e dopo che è stato completato. L'operazione può essere eseguita registrando gestori degli eventi per gli eventi `Navigating` e `Navigated`, rispettivamente:

```xaml
<Shell ...
       Navigating="OnShellNavigating">
    ...
</Shell>
```

```csharp
void OnShellNavigating(object sender, ShellNavigatingEventArgs e)
{
  if (...)
  {
    e.Cancel(); // Cancel the navigation
  }
}
```

La classe `ShellNavigatingEventArgs` specifica le seguenti proprietà:

| Proprietà | Tipo | Description |
|---|---|---|
| Corrente | `ShellNavigationState` | URI della pagina corrente. |
| Origine | `ShellNavigationState` | URI che rappresenta il punto in cui ha origine la navigazione. |
| destinazione | `ShellNavigationSource`  | URI che rappresenta la destinazione della navigazione. |
| CanCancel  | `bool` | Valore che indica se è possibile annullare la navigazione. |
| Annullato  | `bool` | Valore che indica se la navigazione è stata annullata. |

Inoltre, la classe `ShellNavigatingEventArgs` specifica un metodo `Cancel`.

La classe `ShellNavigatedEventArgs` specifica le seguenti proprietà:

| Proprietà | Tipo | Description |
|---|---|---|
| Corrente | `ShellNavigationState` | URI della pagina corrente. |
| Precedente| `ShellNavigationState` | URI della pagina precedente. |
| Origine  | `ShellNavigationSource` | URI che rappresenta il punto in cui ha origine la navigazione. |

Inoltre, Shell offre un metodo `OnBackButtonPressed` sottoponibile a override che può essere usato per intercettare la pressione del pulsante Indietro.

## <a name="related-links"></a>Collegamenti correlati

- [Tailwind Traders (esempio)](https://github.com/Microsoft/TailwindTraders-Mobile)
