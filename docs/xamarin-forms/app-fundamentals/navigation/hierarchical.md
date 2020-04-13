---
title: Navigazione gerarchica
description: Questo articolo illustra come usare la classe NavigationPage per eseguire la navigazione in uno stack di pagine LIFO (Last-In, First-Out).
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 11ad1fb18d1263eb77ef037350a3633510934c42
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305115"
---
# <a name="hierarchical-navigation"></a>Navigazione gerarchica

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)

_Il NavigationPage classe fornisce un'esperienza di spostamento gerarchico in cui l'utente è in grado di spostarsi tra le pagine, avanti e indietro, come desiderato. La classe implementa la navigazione come stack LIFO (Last-In, First-Out) di oggetti Page. In questo articolo viene illustrato come utilizzare il NavigationPage classe per eseguire la navigazione in uno stack di pagine._

Per passare da una pagina all'altra, un'applicazione esegue il push di una nuova pagina nello stack di navigazione, in cui diventa la pagina attiva, come illustrato nel diagramma seguente:

![](hierarchical-images/pushing.png "Pushing a Page to the Navigation Stack")

Per tornare alla pagina precedente, l'applicazione preleva la pagina corrente dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva, come illustrato nel diagramma seguente:

![](hierarchical-images/popping.png "Popping a Page from the Navigation Stack")

I metodi di [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) navigazione vengono [`Page`](xref:Xamarin.Forms.Page) esposti dalla proprietà in qualsiasi tipo derivato. Questi metodi consentono di eseguire il push di pagine nello stack di navigazione, di prelevare pagine dallo stack di navigazione e di eseguire la manipolazione dello stack.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Esecuzione degli spostamenti

Nella navigazione gerarchica, la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe viene [`ContentPage`](xref:Xamarin.Forms.ContentPage) utilizzata per spostarsi all'interno di uno stack di oggetti. Gli screenshot seguenti illustrano i componenti principali della `NavigationPage` in ogni piattaforma:

![](hierarchical-images/navigationpage-components.png "NavigationPage Components")

Il layout [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) di un dipende dalla piattaforma:

- In iOS è presente una barra di spostamento nella parte superiore della pagina che include un titolo e un pulsante *Indietro* che consente di tornare alla pagina precedente.
- In Android è presente una barra di spostamento nella parte superiore della pagina che include un titolo, un'icona e un pulsante *Indietro* che consente di tornare alla pagina precedente. L'icona è definita nell'attributo `[Activity]` che decora la classe `MainActivity` nel progetto specifico della piattaforma Android.
- Nella piattaforma UWP (Universal Windows Platform) è presente una barra di spostamento nella parte superiore della pagina che visualizza un titolo.

Su tutte le piattaforme, [`Page.Title`](xref:Xamarin.Forms.Page.Title) il valore della proprietà verrà visualizzato come titolo della pagina.

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

Di conseguenza l'istanza `Page1Xaml` [`ContentPage`](xref:Xamarin.Forms.ContentPage) viene inserita tramite push nello stack di navigazione, in cui diventa la pagina attiva e la pagina radice dell'applicazione. Ciò viene illustrato negli screenshot seguenti:

![](hierarchical-images/mainpage.png "Root Page of Navigation Stack")

> [!NOTE]
> La [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) proprietà [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) di un'istanza fornisce l'accesso alla prima pagina nello stack di navigazione.

### <a name="pushing-pages-to-the-navigation-stack"></a>Eseguire il push delle pagine nello stack di navigazione

Per passare `Page2Xaml`a , è [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) necessario richiamare il metodo sulla [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà della pagina corrente, come illustrato nell'esempio di codice seguente:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Di conseguenza l'istanza `Page2Xaml` viene inserita tramite push nello stack di navigazione, in cui diventa la pagina attiva. Ciò viene illustrato negli screenshot seguenti:

![](hierarchical-images/secondpage.png "Page Pushed onto Navigation Stack")

Quando [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) viene richiamato il metodo, si verificano i seguenti eventi:

- Il relativo `PushAsync` [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) override richiamato nella chiamata di pagina viene richiamato.
- Il relativo [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) override della pagina a cui ci si sposta viene richiamato.
- L'attività `PushAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per ulteriori informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro Xamarin.Forms di Charles Petzold.

> [!NOTE]
> Le chiamate [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) alle sostituzioni e non possono essere considerate come indicazioni garantite di navigazione tra le pagine. Ad esempio, in iOS l'override di `OnDisappearing` viene chiamato per la pagina attiva quando l'applicazione viene terminata.

### <a name="popping-pages-from-the-navigation-stack"></a>Prelievo di pagine dallo stack di navigazione

La pagina attiva può essere prelevata dallo stack di navigazione premendo il pulsante *Indietro* (fisico o su schermo) del dispositivo.

Per tornare a livello di `Page2Xaml` codice alla [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) pagina originale, l'istanza deve richiamare il metodo, come illustrato nell'esempio di codice seguente:To programmatically return to the original page, the instance must invoke the method, as demonstrated in the following code example:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Di conseguenza l'istanza `Page2Xaml` viene rimossa dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva. Quando [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) viene richiamato il metodo, si verificano i seguenti eventi:

- Il relativo `PopAsync` [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) override richiamato nella chiamata di pagina viene richiamato.
- Il relativo [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) override della pagina restituita viene richiamato.
- L'attività `PopAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per altre informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del manuale di Xamarin.Forms redatto da Charles Petzold.

Oltre [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) ai [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) metodi [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) e , la proprietà [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) di ogni pagina fornisce anche un metodo, illustrato nell'esempio di codice seguente:As as well as and methods, the property of each page also provides a method, which is shown in the following code example:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Questo metodo estrae [`Page`](xref:Xamarin.Forms.Page) tutto tranne la radice dallo stack di navigazione, rendendo quindi la pagina radice dell'applicazione la pagina attiva.

### <a name="animating-page-transitions"></a>Animazione delle transizioni di pagina

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà di ogni pagina fornisce anche metodi `boolean` push e pop sottoposti a override che includono un parametro che controlla se visualizzare un'animazione di pagina durante la navigazione, come illustrato nell'esempio di codice seguente:The property of each page also provides overridden push and pop methods that include a parameter that controls whether to display a page animation during navigation, as shown in the following code example:

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

In alcuni casi, una pagina deve passare dati a un'altra pagina durante gli spostamenti. Due tecniche per eseguire questa operazione sono il passaggio di dati [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) tramite un costruttore di pagina e impostando la nuova pagina per i dati. Di seguito verranno descritti entrambi.

### <a name="passing-data-through-a-page-constructor"></a>Passaggio dei dati tramite un costruttore di pagina

La tecnica più semplice per il passaggio di dati a un'altra pagina durante gli spostamenti è tramite un parametro del costruttore di pagina, come illustrato nell'esempio di codice seguente:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Questo codice `MainPage` crea un'istanza, passando la data e l'ora correnti in [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) formato ISO8601, di cui viene eseguito il wrapping in un'istanza.

L'istanza di `MainPage` riceve i dati tramite un parametro del costruttore, come illustrato nell'esempio di codice seguente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

I dati vengono quindi visualizzati nella [`Label.Text`](xref:Xamarin.Forms.Label.Text) pagina impostando la proprietà, come illustrato nelle schermate seguenti:

![](hierarchical-images/passing-data-constructor.png "Data Passed Through a Page Constructor")

### <a name="passing-data-through-a-bindingcontext"></a>Passaggio dei dati tramite BindingContext

Un approccio alternativo per il passaggio di dati a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) un'altra pagina durante la navigazione consiste nell'impostare la nuova pagina sui dati, come illustrato nell'esempio di codice seguente:An alternative approach for passing data to another page during navigation is by setting the new page's to the data, as shown in the following code example:

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

Questo codice [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) imposta `SecondPage` l'istanza `Contact` sull'istanza, quindi `SecondPage`passa all'oggetto .

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

I dati vengono quindi visualizzati nella [`Label`](xref:Xamarin.Forms.Label) pagina da una serie di controlli, come illustrato nelle schermate seguenti:

![](hierarchical-images/passing-data-bindingcontext.png "Data Passed Through a BindingContext")

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Modifica dello stack di navigazione

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà espone [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) una proprietà da cui è possibile ottenere le pagine nello stack di navigazione. Mentre Xamarin.Forms mantiene l'accesso allo `Navigation` stack [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) di [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) spostamento, la proprietà fornisce i metodi e per modificare lo stack inserendo pagine o rimuovendole.

Il [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) metodo inserisce una pagina specificata nello stack di navigazione prima di una pagina specificata esistente, come illustrato nel diagramma seguente:The method inserts a specified page in the navigation stack before an existing specified page, as shown in the following diagram:

![](hierarchical-images/insert-page-before.png "Inserting a Page in the Navigation Stack")

Il [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) metodo rimuove la pagina specificata dallo stack di navigazione, come illustrato nel diagramma seguente:

![](hierarchical-images/remove-page.png "Removing a Page from the Navigation Stack")

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

A condizione che le credenziali dell'utente siano corrette, l'istanza di `MainPage` viene inserita nello stack di navigazione prima della pagina corrente. Il [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) metodo rimuove quindi la pagina corrente `MainPage` dallo stack di navigazione, con l'istanza che diventa la pagina attiva.

## <a name="displaying-views-in-the-navigation-bar"></a>Visualizzazione delle viste nella barra di spostamento

Qualsiasi Xamarin.Forms [`View`](xref:Xamarin.Forms.View) può essere visualizzato nella [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)barra di navigazione di un oggetto . Questa operazione viene eseguita impostando [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) la `View`proprietà associata su un oggetto . Questa proprietà associata può [`Page`](xref:Xamarin.Forms.Page)essere impostata `Page` su qualsiasi `NavigationPage`, `NavigationPage` e quando l'oggetto viene inserito in un oggetto , il testamento rispetterà il valore della proprietà .

L'esempio seguente, tratto dall'[esempio di visualizzazione del titolo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview), mostra come impostare la proprietà associata [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) da XAML:

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

In questo [`Slider`](xref:Xamarin.Forms.Slider) modo viene visualizzata la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)barra di navigazione della scheda :

[![Dispositivo di scorrimento TitleView](hierarchical-images/titleview-small.png "Dispositivo di scorrimento TitleView")](hierarchical-images/titleview-large.png#lightbox "Dispositivo di scorrimento TitleView")

> [!IMPORTANT]
> Molte visualizzazioni non verranno visualizzate nella barra di spostamento [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) a [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) meno che le dimensioni della visualizzazione non siano specificate con le proprietà e . In alternativa, è possibile eseguire [`StackLayout`](xref:Xamarin.Forms.StackLayout) il [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) wrapping della vista in a con le proprietà e impostate sui valori appropriati.

Si noti [`Layout`](xref:Xamarin.Forms.Layout) che, poiché [`View`](xref:Xamarin.Forms.View) la [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) classe deriva dalla classe , la proprietà associata può essere impostata per visualizzare una classe di layout che contiene più visualizzazioni. In iOS e nella piattaforma UWP (Universal Windows Platform) non è possibile modificare l'altezza della barra di navigazione, pertanto verrà effettuato il ritaglio se la vista visualizzata nella barra di spostamento sarà superiore alle dimensioni predefinite della barra di spostamento. Tuttavia, in Android, l'altezza della barra [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) di navigazione `double` può essere modificata impostando la proprietà associabile su un che rappresenta la nuova altezza. Per altre informazioni, vedere [Setting the Navigation Bar Height on a NavigationPage](~/xamarin-forms/platform/android/navigationpage-bar-height.md) (Impostazione dell'altezza della barra di spostamento su una NavigationPage).

In alternativa, può essere suggerita una barra di spostamento estesa inserendo alcuni contenuti nella barra di spostamento e altri in una vista nella parte superiore del contenuto della pagina, da associare ai colori della barra di navigazione. Inoltre, in iOS la linea di separazione e l'ombreggiatura che si [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) trovare `true`nella parte inferiore della barra di spostamento possono essere rimosse impostando la proprietà associabile su . Per altre informazioni, vedere [Hiding the Navigation Bar Separator on a NavigationPage](~/xamarin-forms/platform/ios/navigation-bar-separator.md) (Nascondere il separatore della barra di spostamento su una NavigationPage).

> [!NOTE]
> Le [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty) [`Title`](xref:Xamarin.Forms.Page.Title)proprietà [`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty), [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) , e possono definire tutti i valori che occupano spazio sulla barra di spostamento. Mentre le dimensioni della barra di spostamento variano a seconda della piattaforma e delle dimensioni dello schermo, l'impostazione di tutte queste proprietà provocherà conflitti a causa dello spazio limitato disponibile. Invece di tentare di usare una combinazione di queste proprietà, è possibile ottenere un design migliore della barra di spostamento desiderata impostando solo la proprietà `TitleView`.

### <a name="limitations"></a>Limitazioni

Ci sono una serie di limitazioni da [`View`](xref:Xamarin.Forms.View) tenere presente quando [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)si visualizza un nella barra di navigazione di un :

- In iOS, le viste poste nella barra di spostamento di una `NavigationPage` vengono visualizzate in una posizione diversa a seconda se sono abilitati i titoli di grandi dimensioni. Per altre informazioni sull'abilitazione di titoli di grandi dimensioni, vedere [Displaying Large Titles](~/xamarin-forms/platform/ios/page-large-title.md) (Visualizzazione di titoli di grandi dimensioni).
- In Android, il posizionamento delle viste nella barra di spostamento di una `NavigationPage` può essere eseguito solo nelle app che usano la compatibilità delle app.
- Non è consigliabile posizionare viste grandi [`ListView`](xref:Xamarin.Forms.ListView) e [`TableView`](xref:Xamarin.Forms.TableView)complesse, ad esempio `NavigationPage`e , nella barra di navigazione di un oggetto .

## <a name="related-links"></a>Collegamenti correlati

- [Navigazione tra le pagine](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierarchical (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical) (Esempio gerarchico)
- [Passing Data (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata) (Esempio di passaggio dei dati)
- [LoginFlow (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow) (Esempio di LoginFlow)
- [TitleView (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview) (Esempio di TitleView)
- [How to Create a Sign In Screen Flow in Xamarin.Forms video](https://www.youtube.com/watch?v=qKQ7pyyG1fo) (Come creare un flusso della schermata di accesso in Xamarin.Forms - video)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
