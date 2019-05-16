---
title: Navigazione gerarchica
description: Questo articolo illustra come usare la classe NavigationPage per eseguire la navigazione in uno stack di pagine LIFO (Last-In, First-Out).
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 01bb3d19b5e79a6ce9942447b5ddd4afb04574cc
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978613"
---
# <a name="hierarchical-navigation"></a>Navigazione gerarchica

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)

_La classe NavigationPage offre un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine avanti e indietro in base alle esigenze. La classe implementa la navigazione come stack LIFO (Last-In, First-Out) di oggetti Pagina. Questo articolo illustra come usare la classe NavigationPage per eseguire la navigazione in uno stack di pagine._

Per passare da una pagina all'altra, un'applicazione esegue il push di una nuova pagina nello stack di navigazione, in cui diventa la pagina attiva, come illustrato nel diagramma seguente:

![](hierarchical-images/pushing.png "Eseguire il push di una pagina nello stack di navigazione")

Per tornare alla pagina precedente, l'applicazione preleva la pagina corrente dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva, come illustrato nel diagramma seguente:

![](hierarchical-images/popping.png "Prelievo di una pagina dallo stack di navigazione")

I metodi di navigazione sono esposti dalla proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) in qualsiasi tipo di [`Page`](xref:Xamarin.Forms.Page) derivata. Questi metodi consentono di eseguire il push di pagine nello stack di navigazione, di prelevare pagine dallo stack di navigazione e di eseguire la manipolazione dello stack.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Esecuzione degli spostamenti

Nella navigazione gerarchica la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) viene usata per navigare in uno stack di oggetti [`ContentPage`](xref:Xamarin.Forms.ContentPage). Gli screenshot seguenti illustrano i componenti principali della `NavigationPage` in ogni piattaforma:

![](hierarchical-images/navigationpage-components.png "Componenti della NavigationPage")

Il layout di una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) dipende dalla piattaforma:

- In iOS è presente una barra di spostamento nella parte superiore della pagina che include un titolo e un pulsante *Indietro* che consente di tornare alla pagina precedente.
- In Android è presente una barra di spostamento nella parte superiore della pagina che include un titolo, un'icona e un pulsante *Indietro* che consente di tornare alla pagina precedente. L'icona è definita nell'attributo `[Activity]` che decora la classe `MainActivity` nel progetto specifico della piattaforma Android.
- Nella piattaforma UWP (Universal Windows Platform) è presente una barra di spostamento nella parte superiore della pagina che visualizza un titolo.

In tutte le piattaforme, il valore della proprietà [`Page.Title`](xref:Xamarin.Forms.Page.Title) verrà visualizzato come titolo della pagina.

> [!NOTE]
> È consigliabile popolare una `NavigationPage` solo con istanze di `ContentPage`.

### <a name="creating-the-root-page"></a>Creazione della pagina radice

La prima pagina aggiunta a uno stack di navigazione è definita pagina *radice* dell'applicazione e il codice seguente illustra come viene eseguita questa operazione:

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

Di conseguenza l'istanza `Page1Xaml` [`ContentPage`](xref:Xamarin.Forms.ContentPage) viene inserita tramite push nello stack di navigazione, in cui diventa la pagina attiva e la pagina radice dell'applicazione. Questo è illustrato negli screenshot seguenti:

![](hierarchical-images/mainpage.png "Pagina radice dello stack di navigazione")

> [!NOTE]
> La proprietà [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) di un'istanza [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) fornisce l'accesso alla prima pagina nello stack di navigazione.

### <a name="pushing-pages-to-the-navigation-stack"></a>Eseguire il push delle pagine nello stack di navigazione

Per passare a `Page2Xaml` è necessario chiamare il metodo [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) sulla proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) della pagina corrente, come illustrato nell'esempio di codice seguente:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Di conseguenza l'istanza `Page2Xaml` viene inserita tramite push nello stack di navigazione, in cui diventa la pagina attiva. Questo è illustrato negli screenshot seguenti:

![](hierarchical-images/secondpage.png "Pagina di cui è stato eseguito il push nello stack di navigazione")

Quando si richiama il metodo [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*), si verificano gli eventi seguenti:

- Per la pagina che chiama `PushAsync` viene richiamato l'override di [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing).
- Per la pagina di destinazione dello spostamento viene richiamato l'override di [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing).
- L'attività `PushAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per altre informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del manuale di Xamarin.Forms redatto da Charles Petzold.

> [!NOTE]
> Le chiamate degli override di [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) e [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) non possono essere considerate come indicazioni garantite dell'avvenuto spostamento tra le pagine. Ad esempio, in iOS l'override di `OnDisappearing` viene chiamato per la pagina attiva quando l'applicazione viene terminata.

### <a name="popping-pages-from-the-navigation-stack"></a>Prelievo di pagine dallo stack di navigazione

La pagina attiva può essere prelevata dallo stack di navigazione premendo il pulsante *Indietro* (fisico o su schermo) del dispositivo.

Per tornare a livello di codice alla pagina originale, l'istanza `Page2Xaml` deve chiamare il metodo [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), come illustrato nell'esempio di codice seguente:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Di conseguenza l'istanza `Page2Xaml` viene rimossa dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva. Quando si richiama il metodo [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), si verificano gli eventi seguenti:

- Per la pagina che chiama `PopAsync` viene richiamato l'override di [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing).
- Per la pagina restituita viene richiamato l'override di [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing).
- L'attività `PopAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per altre informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del manuale di Xamarin.Forms redatto da Charles Petzold.

Analogamente ai metodi [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) e [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), la proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) di ogni pagina fornisce anche un metodo [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync), che viene visualizzato nell'esempio di codice seguente:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Questo metodo preleva tutto tranne la radice [`Page`](xref:Xamarin.Forms.Page) dallo stack di navigazione, rendendo pertanto la pagina radice dell'applicazione la pagina attiva.

### <a name="animating-page-transitions"></a>Animazione delle transizioni di pagina

La proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) di ogni pagina offre anche metodi di push e di prelievo sottoposti a override, che includono un parametro `boolean` che controlla la visualizzazione di un'animazione di pagina durante gli spostamenti, come illustrato nell'esempio di codice seguente:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushAsync (new Page2Xaml (), false);
}

async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopAsync (false);
}

async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopToRootAsync (false);
}
```

L'impostazione del parametro `boolean` su `false` disabilita l'animazione per la transizione di pagina, mentre l'impostazione del parametro su `true` abilita l'animazione per la transizione di pagina, a condizione che sia supportata dalla piattaforma sottostante. Tuttavia, i metodi di push e prelievo senza questo parametro abilitano l'animazione per impostazione predefinita.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passaggio di dati durante gli spostamenti

In alcuni casi, una pagina deve passare dati a un'altra pagina durante gli spostamenti. Due tecniche per questa operazione sono il passaggio dei dati tramite un costruttore di pagina e l'impostazione di [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della nuova pagina sui dati. Di seguito verranno descritti entrambi.

### <a name="passing-data-through-a-page-constructor"></a>Passaggio dei dati tramite un costruttore di pagina

La tecnica più semplice per il passaggio di dati a un'altra pagina durante gli spostamenti è tramite un parametro del costruttore di pagina, come illustrato nell'esempio di codice seguente:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Questo codice crea un'istanza di `MainPage`, passando la data e l'ora correnti in formato ISO8601, di cui viene eseguito il wrapping in un'istanza della [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

L'istanza di `MainPage` riceve i dati tramite un parametro del costruttore, come illustrato nell'esempio di codice seguente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

I dati vengono quindi visualizzati nella pagina impostando la proprietà [`Label.Text`](xref:Xamarin.Forms.Label.Text), come illustrato negli screenshot seguenti:

![](hierarchical-images/passing-data-constructor.png "Passaggio dei dati tramite un costruttore di pagina")

### <a name="passing-data-through-a-bindingcontext"></a>Passaggio dei dati tramite BindingContext

Un approccio alternativo per passare dati a un'altra pagina durante gli spostamenti consiste nell'impostare [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della nuova pagina sui dati, come illustrato nell'esempio di codice seguente:

```csharp
async void OnNavigateButtonClicked (object sender, EventArgs e)
{
  var contact = new Contact {
    Name = "Jane Doe",
    Age = 30,
    Occupation = "Developer",
    Country = "USA"
  };

  var secondPage = new SecondPage ();
  secondPage.BindingContext = contact;
  await Navigation.PushAsync (secondPage);
}
```

Questo codice imposta [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) dell'istanza di `SecondPage` sull'istanza di `Contact` e quindi passa a `SecondPage`.

`SecondPage` usa quindi il data binding per visualizzare i dati dell'istanza di `Contact`, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="PassingData.SecondPage"
             Title="Second Page">
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
            ...
            <Button x:Name="navigateButton" Text="Previous Page" Clicked="OnNavigateButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

L'esempio di codice seguente illustra come è possibile implementare il data binding in C#:

```csharp
public class SecondPageCS : ContentPage
{
  public SecondPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var navigateButton = new Button { Text = "Previous Page" };
    navigateButton.Clicked += OnNavigateButtonClicked;

    Content = new StackLayout {
      HorizontalOptions = LayoutOptions.Center,
      VerticalOptions = LayoutOptions.Center,
      Children = {
        new StackLayout {
          Orientation = StackOrientation.Horizontal,
          Children = {
            new Label{ Text = "Name:", FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)), HorizontalOptions = LayoutOptions.FillAndExpand },
            nameLabel
          }
        },
        ...
        navigateButton
      }
    };
  }

  async void OnNavigateButtonClicked (object sender, EventArgs e)
  {
    await Navigation.PopAsync ();
  }
}
```

I dati vengono quindi visualizzati nella pagina tramite una serie di controlli [`Label`](xref:Xamarin.Forms.Label), come illustrato negli screenshot seguenti:

![](hierarchical-images/passing-data-bindingcontext.png "Passaggio dei dati tramite BindingContext")

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Modifica dello stack di navigazione

La proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) espone una proprietà [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) dalla quale è possibile ottenere le pagine nello stack di navigazione. Sebbene Xamarin.Forms mantenga l'accesso allo stack di navigazione, la proprietà `Navigation` fornisce i metodi [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) e [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) per modificare lo stack mediante l'inserimento o la rimozione di pagine.

Il metodo [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) inserisce una pagina specificata nello stack di navigazione prima di una pagina specificata esistente, come illustrato nel diagramma seguente:

![](hierarchical-images/insert-page-before.png "Inserimento di una pagina nello stack di navigazione")

Il metodo [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) rimuove la pagina specificata dallo stack di navigazione, come illustrato nel diagramma seguente:

![](hierarchical-images/remove-page.png "Rimozione di una pagina dallo stack di navigazione")

Questi metodi consentono un'esperienza di navigazione personalizzata, come la sostituzione di una pagina di accesso con una nuova pagina, dopo un accesso con esito positivo. Nell'esempio di codice seguente viene illustrato questo scenario:

```csharp
async void OnLoginButtonClicked (object sender, EventArgs e)
{
  ...
  var isValid = AreCredentialsCorrect (user);
  if (isValid) {
    App.IsUserLoggedIn = true;
    Navigation.InsertPageBefore (new MainPage (), this);
    await Navigation.PopAsync ();
  } else {
    // Login failed
  }
}

```

A condizione che le credenziali dell'utente siano corrette, l'istanza di `MainPage` viene inserita nello stack di navigazione prima della pagina corrente. Il metodo [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) rimuove quindi la pagina corrente dallo stack di navigazione e l'istanza di `MainPage` diventa la pagina attiva.

## <a name="displaying-views-in-the-navigation-bar"></a>Visualizzazione delle viste nella barra di spostamento

Qualsiasi [`View`](xref:Xamarin.Forms.View) Xamarin.Forms può essere visualizzato nella barra di spostamento di una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Questa operazione viene eseguita impostando la proprietà associata [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) su una `View`. Questa proprietà associata può essere impostata su qualunque [`Page`](xref:Xamarin.Forms.Page) e quando viene eseguito il push della `Page` in una `NavigationPage`, `NavigationPage` rispetta il valore della proprietà.

L'esempio seguente, tratto dall'[esempio di visualizzazione del titolo](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/), mostra come impostare la proprietà associata [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) da XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="NavigationPageTitleView.TitleViewPage">
    <NavigationPage.TitleView>
        <Slider HeightRequest="44" WidthRequest="300" />
    </NavigationPage.TitleView>
    ...
</ContentPage>
```

Questo è il codice C# equivalente:

```csharp
public class TitleViewPage : ContentPage
{
    public TitleViewPage()
    {
        var titleView = new Slider { HeightRequest = 44, WidthRequest = 300 };
        NavigationPage.SetTitleView(this, titleView);
        ...
    }
}
```

Il risultato è la visualizzazione di [`Slider`](xref:Xamarin.Forms.Slider) nella barra di spostamento sulla [`NavigationPage`](xref:Xamarin.Forms.NavigationPage):

[![Dispositivo di scorrimento TitleView](hierarchical-images/titleview-small.png "TitleView dispositivo di scorrimento")](hierarchical-images/titleview-large.png#lightbox "TitleView dispositivo di scorrimento")

> [!IMPORTANT]
> Molte viste non saranno più visualizzate nella barra di spostamento, a meno che non vengano specificate le dimensioni della vista con le proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest). In alternativa, può essere eseguito il wrapping della vista in un [`StackLayout`](xref:Xamarin.Forms.StackLayout) con le proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) impostate sui valori appropriati.

Si noti che, poiché la classe [`Layout`](xref:Xamarin.Forms.Layout) deriva dalla classe [`View`](xref:Xamarin.Forms.View), la proprietà associata [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) può essere impostata per visualizzare una classe di layout contenente viste multiple. In iOS e nella piattaforma UWP (Universal Windows Platform) non è possibile modificare l'altezza della barra di navigazione, pertanto verrà effettuato il ritaglio se la vista visualizzata nella barra di spostamento sarà superiore alle dimensioni predefinite della barra di spostamento. Tuttavia, in Android, l'altezza della barra di spostamento può essere modificata impostando la proprietà associabile [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) per una `double` che rappresenta la nuova altezza. Per altre informazioni, vedere [Setting the Navigation Bar Height on a NavigationPage](~/xamarin-forms/platform/android/navigationpage-bar-height.md) (Impostazione dell'altezza della barra di spostamento su una NavigationPage).

In alternativa, può essere suggerita una barra di spostamento estesa inserendo alcuni contenuti nella barra di spostamento e altri in una vista nella parte superiore del contenuto della pagina, da associare ai colori della barra di navigazione. Inoltre, in iOS la linea di separazione e l'ombreggiatura nella parte inferiore della barra di spostamento possono essere rimosse impostando la proprietà associabile [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) su `true`. Per altre informazioni, vedere [Hiding the Navigation Bar Separator on a NavigationPage](~/xamarin-forms/platform/ios/navigation-bar-separator.md) (Nascondere il separatore della barra di spostamento su una NavigationPage).

> [!NOTE]
> Le proprietà [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty), [`Title`](xref:Xamarin.Forms.Page.Title), [`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty) e [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) possono definire tutte i valori che occupano spazio nella barra di spostamento. Mentre le dimensioni della barra di spostamento variano a seconda della piattaforma e delle dimensioni dello schermo, l'impostazione di tutte queste proprietà provocherà conflitti a causa dello spazio limitato disponibile. Invece di tentare di usare una combinazione di queste proprietà, è possibile ottenere un design migliore della barra di spostamento desiderata impostando solo la proprietà `TitleView`.

### <a name="limitations"></a>Limitazioni

Esistono alcune limitazioni da tenere presenti quando si visualizza una [`View`](xref:Xamarin.Forms.View) nella barra di spostamento di una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage):

- In iOS, le viste poste nella barra di spostamento di una `NavigationPage` vengono visualizzate in una posizione diversa a seconda se sono abilitati i titoli di grandi dimensioni. Per altre informazioni sull'abilitazione di titoli di grandi dimensioni, vedere [Displaying Large Titles](~/xamarin-forms/platform/ios/page-large-title.md) (Visualizzazione di titoli di grandi dimensioni).
- In Android, il posizionamento delle viste nella barra di spostamento di una `NavigationPage` può essere eseguito solo nelle app che usano la compatibilità delle app.
- Non è consigliabile posizionare viste grandi e complesse, come [`ListView`](xref:Xamarin.Forms.ListView) e [`TableView`](xref:Xamarin.Forms.TableView), nella barra di spostamento di una `NavigationPage`.

## <a name="related-links"></a>Collegamenti correlati

- [Page Navigation](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) (Spostamenti tra le pagine)
- [Hierarchical (sample)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/) (Esempio gerarchico)
- [Passing Data (sample)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/) (Esempio di passaggio dei dati)
- [LoginFlow (sample)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/) (Esempio di LoginFlow)
- [TitleView (sample)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/) (Esempio di TitleView)
- [How to Create a Sign In Screen Flow in Xamarin.Forms video](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms) (Come creare un flusso della schermata di accesso in Xamarin.Forms - video)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
