---
title: Navigazione gerarchica
description: Questo articolo illustra come usare la classe NavigationPage per eseguire la navigazione in uno stack di pagine LIFO (Last-In, First-Out).
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
ms.openlocfilehash: 984f54698ccdee54c0b8670a50cb1f4432327977
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517271"
---
# <a name="hierarchical-navigation"></a>Navigazione gerarchica

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)

_La classe NavigationPage fornisce un'esperienza di navigazione gerarchica in cui l'utente è in grado di spostarsi tra le pagine, avanti e indietro, a seconda delle esigenze. La classe implementa lo spostamento come uno stack LIFO (Last-in, First-out) di oggetti Page. Questo articolo illustra come usare la classe NavigationPage per eseguire la navigazione in uno stack di pagine._

Per passare da una pagina all'altra, un'applicazione esegue il push di una nuova pagina nello stack di navigazione, in cui diventa la pagina attiva, come illustrato nel diagramma seguente:

![](hierarchical-images/pushing.png "Pushing a Page to the Navigation Stack")

Per tornare alla pagina precedente, l'applicazione preleva la pagina corrente dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva, come illustrato nel diagramma seguente:

![](hierarchical-images/popping.png "Popping a Page from the Navigation Stack")

I [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) metodi di navigazione sono esposti dalla proprietà su [`Page`](xref:Xamarin.Forms.Page) tutti i tipi derivati. Questi metodi consentono di eseguire il push di pagine nello stack di navigazione, di prelevare pagine dallo stack di navigazione e di eseguire la manipolazione dello stack.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Esecuzione degli spostamenti

Nell'esplorazione gerarchica, la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe viene utilizzata per spostarsi tra uno stack di [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetti. Gli screenshot seguenti illustrano i componenti principali della `NavigationPage` in ogni piattaforma:

![](hierarchical-images/navigationpage-components.png "NavigationPage Components")

Il layout di un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) oggetto dipende dalla piattaforma:

- In iOS è presente una barra di spostamento nella parte superiore della pagina che include un titolo e un pulsante *Indietro* che consente di tornare alla pagina precedente.
- In Android è presente una barra di spostamento nella parte superiore della pagina che include un titolo, un'icona e un pulsante *Indietro* che consente di tornare alla pagina precedente. L'icona è definita nell'attributo `[Activity]` che decora la classe `MainActivity` nel progetto specifico della piattaforma Android.
- Nella piattaforma UWP (Universal Windows Platform) è presente una barra di spostamento nella parte superiore della pagina che visualizza un titolo.

In tutte le piattaforme, il valore della [`Page.Title`](xref:Xamarin.Forms.Page.Title) proprietà verrà visualizzato come titolo della pagina. Inoltre, la `IconColor` proprietà può essere impostata su un oggetto [`Color`](xref:Xamarin.Forms.Color) applicato all'icona nella barra di spostamento.

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
> La [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) proprietà di un' [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) istanza di consente di accedere alla prima pagina nello stack di navigazione.

### <a name="pushing-pages-to-the-navigation-stack"></a>Eseguire il push delle pagine nello stack di navigazione

Per passare a `Page2Xaml`, è necessario richiamare il [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) metodo sulla [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà della pagina corrente, come illustrato nell'esempio di codice seguente:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Di conseguenza l'istanza `Page2Xaml` viene inserita tramite push nello stack di navigazione, in cui diventa la pagina attiva. Ciò viene illustrato negli screenshot seguenti:

![](hierarchical-images/secondpage.png "Page Pushed onto Navigation Stack")

Quando viene [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) richiamato il metodo, si verificano gli eventi seguenti:

- Per la chiamata `PushAsync` della pagina [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) è stato richiamato il relativo override.
- Viene richiamata la [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) sostituzione della pagina a cui è stato eseguito lo spostamento.
- L'attività `PushAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per ulteriori informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro Novell. Forms di Charles Petzold.

> [!NOTE]
> Le chiamate a [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) e [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) le sostituzioni non possono essere considerate come indicazioni garantite della navigazione tra le pagine. Ad esempio, in iOS l'override di `OnDisappearing` viene chiamato per la pagina attiva quando l'applicazione viene terminata.

### <a name="popping-pages-from-the-navigation-stack"></a>Prelievo di pagine dallo stack di navigazione

La pagina attiva può essere prelevata dallo stack di navigazione premendo il pulsante *Indietro* (fisico o su schermo) del dispositivo.

Per tornare a livello di codice alla pagina originale, `Page2Xaml` l'istanza deve richiamare [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) il metodo, come illustrato nell'esempio di codice seguente:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Di conseguenza l'istanza `Page2Xaml` viene rimossa dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva. Quando viene [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) richiamato il metodo, si verificano gli eventi seguenti:

- Per la chiamata `PopAsync` della pagina [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) è stato richiamato il relativo override.
- Viene richiamata la [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) sostituzione della pagina a cui viene restituito.
- L'attività `PopAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per altre informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del manuale di Xamarin.Forms redatto da Charles Petzold.

Oltre ai metodi [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) e [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) , la [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà di ogni pagina fornisce anche un [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) metodo, illustrato nell'esempio di codice seguente:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Questo metodo estrae tutto tranne la [`Page`](xref:Xamarin.Forms.Page) radice dallo stack di navigazione, rendendo quindi la pagina radice dell'applicazione la pagina attiva.

### <a name="animating-page-transitions"></a>Animazione delle transizioni di pagina

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà di ogni pagina fornisce anche metodi push e pop sottoposti a override che `boolean` includono un parametro che controlla se visualizzare un'animazione di pagina durante la navigazione, come illustrato nell'esempio di codice seguente:

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

In alcuni casi, una pagina deve passare dati a un'altra pagina durante gli spostamenti. Due tecniche per eseguire questa operazione comportano il passaggio dei dati tramite un costruttore di pagina e l'impostazione [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della nuova pagina sui dati. Di seguito verranno descritti entrambi.

### <a name="passing-data-through-a-page-constructor"></a>Passaggio dei dati tramite un costruttore di pagina

La tecnica più semplice per il passaggio di dati a un'altra pagina durante gli spostamenti è tramite un parametro del costruttore di pagina, come illustrato nell'esempio di codice seguente:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Questo codice crea un' `MainPage` istanza di, passando la data e l'ora correnti nel formato ISO8601, che viene racchiuso [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) in un'istanza.

L'istanza di `MainPage` riceve i dati tramite un parametro del costruttore, come illustrato nell'esempio di codice seguente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

I dati vengono quindi visualizzati nella pagina impostando la [`Label.Text`](xref:Xamarin.Forms.Label.Text) proprietà, come illustrato nelle schermate seguenti:

![](hierarchical-images/passing-data-constructor.png "Data Passed Through a Page Constructor")

### <a name="passing-data-through-a-bindingcontext"></a>Passaggio dei dati tramite BindingContext

Un approccio alternativo per passare i dati a un'altra pagina durante la navigazione consiste nell'impostare la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) nuova pagina sui dati, come illustrato nell'esempio di codice seguente:

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

Questo codice imposta l' [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) oggetto dell' `SecondPage` istanza sull' `Contact` istanza di, quindi passa a `SecondPage`.

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

I dati vengono quindi visualizzati nella pagina da una serie di [`Label`](xref:Xamarin.Forms.Label) controlli, come illustrato nelle schermate seguenti:

![](hierarchical-images/passing-data-bindingcontext.png "Data Passed Through a BindingContext")

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Modifica dello stack di navigazione

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà espone una [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) proprietà dalla quale è possibile ottenere le pagine nello stack di navigazione. Mentre Novell. Forms mantiene l'accesso allo stack di navigazione `Navigation` , la proprietà [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) fornisce [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) i metodi e per la modifica dello stack inserendo le pagine o rimuovendo le pagine.

Il [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) metodo inserisce una pagina specificata nello stack di navigazione prima di una pagina specificata esistente, come illustrato nel diagramma seguente:

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

A condizione che le credenziali dell'utente siano corrette, l'istanza di `MainPage` viene inserita nello stack di navigazione prima della pagina corrente. Il [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) metodo rimuove quindi la pagina corrente dallo stack di navigazione, con l' `MainPage` istanza che diventa la pagina attiva.

## <a name="displaying-views-in-the-navigation-bar"></a>Visualizzazione delle viste nella barra di spostamento

Qualsiasi Novell. Forms [`View`](xref:Xamarin.Forms.View) può essere visualizzato nella barra di navigazione di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)un oggetto. Questa operazione viene eseguita impostando [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) la proprietà associata su `View`un oggetto. Questa proprietà associata può essere impostata su qualsiasi [`Page`](xref:Xamarin.Forms.Page)oggetto e, quando `Page` viene eseguito il push `NavigationPage`su un `NavigationPage` oggetto, il valore della proprietà verrà rispettato da.

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

Ciò comporta la visualizzazione [`Slider`](xref:Xamarin.Forms.Slider) di un oggetto nella barra di spostamento in [`NavigationPage`](xref:Xamarin.Forms.NavigationPage):

[![Dispositivo di scorrimento TitleView](hierarchical-images/titleview-small.png "Dispositivo di scorrimento TitleView")](hierarchical-images/titleview-large.png#lightbox "Dispositivo di scorrimento TitleView")

> [!IMPORTANT]
> Molte visualizzazioni non verranno visualizzate nella barra di spostamento, a meno che le dimensioni della vista non siano [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) specificate [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) con le proprietà e. In alternativa, è possibile eseguire il wrapper della vista [`StackLayout`](xref:Xamarin.Forms.StackLayout) in un [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) oggetto [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) con le proprietà e impostate sui valori appropriati.

Si noti che, poiché [`Layout`](xref:Xamarin.Forms.Layout) la classe deriva dalla [`View`](xref:Xamarin.Forms.View) classe, la [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) proprietà associata può essere impostata in modo da visualizzare una classe layout contenente più viste. In iOS e nella piattaforma UWP (Universal Windows Platform) non è possibile modificare l'altezza della barra di navigazione, pertanto verrà effettuato il ritaglio se la vista visualizzata nella barra di spostamento sarà superiore alle dimensioni predefinite della barra di spostamento. Tuttavia, in Android, l'altezza della barra di spostamento può essere modificata impostando la [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) proprietà associabile su `double` un oggetto che rappresenta la nuova altezza. Per altre informazioni, vedere [Setting the Navigation Bar Height on a NavigationPage](~/xamarin-forms/platform/android/navigationpage-bar-height.md) (Impostazione dell'altezza della barra di spostamento su una NavigationPage).

In alternativa, può essere suggerita una barra di spostamento estesa inserendo alcuni contenuti nella barra di spostamento e altri in una vista nella parte superiore del contenuto della pagina, da associare ai colori della barra di navigazione. Inoltre, in iOS la linea di separazione e l'ombreggiatura che si trova nella parte inferiore della barra di spostamento possono essere rimossi [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) impostando la `true`proprietà associabile su. Per altre informazioni, vedere [Hiding the Navigation Bar Separator on a NavigationPage](~/xamarin-forms/platform/ios/navigation-bar-separator.md) (Nascondere il separatore della barra di spostamento su una NavigationPage).

> [!NOTE]
> Le [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty)proprietà [`Title`](xref:Xamarin.Forms.Page.Title), [`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty), e [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) possono definire valori che occupano spazio sulla barra di spostamento. Mentre le dimensioni della barra di spostamento variano a seconda della piattaforma e delle dimensioni dello schermo, l'impostazione di tutte queste proprietà provocherà conflitti a causa dello spazio limitato disponibile. Invece di tentare di usare una combinazione di queste proprietà, è possibile ottenere un design migliore della barra di spostamento desiderata impostando solo la proprietà `TitleView`.

### <a name="limitations"></a>Limitazioni

Quando si visualizza un oggetto [`View`](xref:Xamarin.Forms.View) nella barra di navigazione di un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), è necessario tenere presenti alcune limitazioni:

- In iOS, le viste poste nella barra di spostamento di una `NavigationPage` vengono visualizzate in una posizione diversa a seconda se sono abilitati i titoli di grandi dimensioni. Per altre informazioni sull'abilitazione di titoli di grandi dimensioni, vedere [Displaying Large Titles](~/xamarin-forms/platform/ios/page-large-title.md) (Visualizzazione di titoli di grandi dimensioni).
- In Android, il posizionamento delle viste nella barra di spostamento di una `NavigationPage` può essere eseguito solo nelle app che usano la compatibilità delle app.
- Non è consigliabile inserire visualizzazioni grandi e complesse, ad esempio [`ListView`](xref:Xamarin.Forms.ListView) e [`TableView`](xref:Xamarin.Forms.TableView), nella barra di navigazione di un oggetto. `NavigationPage`

## <a name="related-links"></a>Collegamenti correlati

- [Navigazione tra pagine](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierarchical (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical) (Esempio gerarchico)
- [Passing Data (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata) (Esempio di passaggio dei dati)
- [LoginFlow (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow) (Esempio di LoginFlow)
- [TitleView (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview) (Esempio di TitleView)
- [How to Create a Sign In Screen Flow in Xamarin.Forms video](https://www.youtube.com/watch?v=qKQ7pyyG1fo) (Come creare un flusso della schermata di accesso in Xamarin.Forms - video)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
