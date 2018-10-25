---
title: Navigazione gerarchica
description: Questo articolo illustra come usare la classe NavigationPage per eseguire l'esplorazione in uno stack last-in, First-Out (LIFO) pagine.
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: a0a58cf05c97221a73cd0784b7859bb9c84cef86
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "38994676"
---
# <a name="hierarchical-navigation"></a>Navigazione gerarchica

_La classe NavigationPage offre un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine avanti e indietro in base alle esigenze. La classe implementa la navigazione come un mazzo last in, First-Out (LIFO) di oggetti della pagina. Questo articolo illustra come usare la classe NavigationPage per eseguire l'esplorazione in uno stack di pagine._

Per passare da una pagina a altra, un'applicazione determinerà una nuova pagina nello stack di navigazione, in cui diventa la pagina attiva, come illustrato nel diagramma seguente:

![](hierarchical-images/pushing.png "Eseguire il push di una pagina allo Stack di navigazione")

Per tornare alla pagina precedente, l'applicazione verrà visualizzata la pagina corrente dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva, come illustrato nel diagramma seguente:

![](hierarchical-images/popping.png "Visualizzazione di una pagina dallo Stack di navigazione")

Metodi di navigazione esposte dal [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) proprietà su qualsiasi [ `Page` ](xref:Xamarin.Forms.Page) i tipi derivati. Questi metodi consentono di eseguire il push di pagine nello stack di navigazione, alle pagine pop dallo stack di navigazione e per la manipolazione dello stack.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Navigazione

Nella navigazione gerarchica la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) viene usata per navigare in uno stack di oggetti [`ContentPage`](xref:Xamarin.Forms.ContentPage). Le schermate seguenti illustrano i componenti principali del `NavigationPage` in ogni piattaforma:

![](hierarchical-images/navigationpage-components.png "Componenti NavigationPage")

Il layout di un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) dipende dalla piattaforma:

- In iOS, una barra di spostamento è presente nella parte superiore della pagina che consente di visualizzare un titolo e che ha un *nuovamente* pulsante Torna alla pagina precedente.
- In Android, è presente nella parte superiore della pagina che consente di visualizzare un titolo, un'icona, una barra di spostamento e un *nuovamente* pulsante Torna alla pagina precedente. L'icona è definito nel `[Activity]` attributo che decora il `MainActivity` classe nel progetto specifico della piattaforma Android.
- Nella piattaforma Windows universale, una barra di spostamento è presente nella parte superiore della pagina che consente di visualizzare un titolo.

In tutte le piattaforme, il valore della [ `Page.Title` ](xref:Xamarin.Forms.Page.Title) proprietà verrà visualizzata come titolo della pagina.

> [!NOTE]
> È consigliabile che una `NavigationPage` deve essere popolato con `ContentPage` solo istanze.

### <a name="creating-the-root-page"></a>Creazione della pagina principale

La prima pagina aggiunta a uno stack di navigazione è definita pagina *radice* dell'applicazione e il codice seguente illustra come viene eseguita questa operazione:

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

In questo modo, il `Page1Xaml` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanza da inserire nello stack di navigazione, in cui diventa la pagina attiva e la pagina principale dell'applicazione. Come illustrato negli screenshot seguenti:

![](hierarchical-images/mainpage.png "Pagina principale dello Stack di navigazione")

> [!NOTE]
> Il [ `RootPage` ](xref:Xamarin.Forms.NavigationPage.RootPage) proprietà di un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) istanza fornisce l'accesso alla prima pagina nello stack di navigazione.

### <a name="pushing-pages-to-the-navigation-stack"></a>Il push di pagine in Stack di navigazione

Per passare a `Page2Xaml`, è necessario richiamare il [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) metodo sul [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) proprietà della pagina corrente, come illustrato nell'esempio di codice seguente:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Di conseguenza l'istanza `Page2Xaml` viene inserita tramite push nello stack di navigazione, in cui diventa la pagina attiva. Come illustrato negli screenshot seguenti:

![](hierarchical-images/secondpage.png "Pagina di cui è stato eseguito il push nello Stack di navigazione")

Quando la [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) metodo viene richiamato, si verificano gli eventi seguenti:

- Chiamare il metodo di pagina `PushAsync` ha relativi [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) override richiamato.
- Nella pagina a cui si accede sono relativi [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) override richiamato.
- Il `PushAsync` al completamento dell'attività.

Tuttavia, l'ordine esatto in cui si verificano questi eventi è dipendente dalla piattaforma. Per altre informazioni, vedere [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro di xamarin. Forms Petzolds.

> [!NOTE]
> Le chiamate al [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) e [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) sostituzioni non possono essere considerate come indicazioni garantite navigazione tra le pagine. Ad esempio, in iOS il `OnDisappearing` override viene chiamato nella pagina attiva quando l'applicazione viene terminata.

### <a name="popping-pages-from-the-navigation-stack"></a>Pagine pop dallo Stack di navigazione

La pagina attiva può essere prelevata dallo stack di navigazione premendo il pulsante *Indietro* (fisico o su schermo) del dispositivo.

Per tornare a livello di codice alla pagina originale, l'istanza `Page2Xaml` deve chiamare il metodo [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), come illustrato nell'esempio di codice seguente:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Di conseguenza l'istanza `Page2Xaml` viene rimossa dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva. Quando la [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) metodo viene richiamato, si verificano gli eventi seguenti:

- Chiamare il metodo di pagina `PopAsync` ha relativi [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) override richiamato.
- La pagina viene quindi restituita contiene relativi [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) override richiamato.
- Il `PopAsync` restituisce di attività.

Tuttavia, l'ordine esatto in cui si verificano questi eventi è dipendente dalla piattaforma. Per altre informazioni, vedere [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro di xamarin. Forms Petzolds.

Nonché [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) e [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) metodi, il [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) proprietà di ogni pagina fornisce anche un [ `PopToRootAsync` ](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) metodo, che viene visualizzato nell'esempio di codice seguente:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Questo metodo Visualizza tutto tranne la radice [ `Page` ](xref:Xamarin.Forms.Page) dallo stack di navigazione, rendendo pertanto la pagina principale dell'applicazione la pagina attiva.

### <a name="animating-page-transitions"></a>Animazione di transizioni di pagina

Il [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) proprietà di ogni pagina fornisce inoltre sottoposto a override push e pop metodi che includono un `boolean` parametro che controlla se deve essere visualizzata un'animazione di pagina durante la navigazione, come illustrato nel codice seguente esempio:

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

Impostando il `boolean` parametro per `false` disabilita l'animazione di transizione di pagina, durante l'impostazione del parametro su `true` consente l'animazione di transizione di pagina, a condizione che è supportata dalla piattaforma sottostante. Tuttavia, i metodi push e pull che non dispongono di questo parametro attiva l'animazione per impostazione predefinita.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passaggio di dati durante la navigazione

In alcuni casi è necessario per una pagina passare i dati in un'altra pagina durante la navigazione. Due tecniche per questa operazione passano dati tramite un costruttore della pagina e impostando la nuova pagina [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) ai dati. Ogni ora parleremo a sua volta.

### <a name="passing-data-through-a-page-constructor"></a>Passaggio di dati tramite un costruttore di pagina

La tecnica più semplice per passare dati a un'altra pagina durante la navigazione è tramite un parametro di costruttore di pagina, illustrata nell'esempio di codice seguente:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Questo codice crea un `MainPage` dell'istanza, il passaggio di data e ora correnti nel formato ISO8601, che viene eseguito il wrapping un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) istanza.

Il `MainPage` istanza riceve i dati tramite un parametro di costruttore, come illustrato nell'esempio di codice seguente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

I dati vengono quindi visualizzati nella pagina impostando il [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) proprietà, come illustrato negli screenshot seguenti:

![](hierarchical-images/passing-data-constructor.png "Dati passati tramite un costruttore di pagina")

### <a name="passing-data-through-a-bindingcontext"></a>Passaggio di dati tramite un oggetto BindingContext

Un approccio alternativo per passare dati a un'altra pagina durante l'esplorazione consiste nell'impostare la nuova pagina [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) ai dati, come illustrato nell'esempio di codice seguente:

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

Questo codice imposta la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del `SecondPage` dell'istanza per il `Contact` istanza e quindi si passa al `SecondPage`.

Il `SecondPage` Usa quindi l'associazione dati per visualizzare il `Contact` istanza dei dati, come illustrato nell'esempio di codice XAML seguente:

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

Esempio di codice seguente viene illustrato come può essere eseguito il data binding in c#:

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

I dati vengono quindi visualizzati nella pagina da una serie di [ `Label` ](xref:Xamarin.Forms.Label) controlli, come illustrato negli screenshot seguenti:

![](hierarchical-images/passing-data-bindingcontext.png "Dati passati tramite un oggetto BindingContext")

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Modifica lo Stack di navigazione

Il [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) espone proprietà una [ `NavigationStack` ](xref:Xamarin.Forms.INavigation.NavigationStack) proprietà da cui è possibile ottenere le pagine nello stack di navigazione. Sebbene xamarin. Forms gestisce l'accesso allo stack di navigazione, la `Navigation` proprietà fornisce il [ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*) e [ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*) metodi per modificare lo stack mediante l'inserimento di le pagine o rimuoverle.

Il [ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*) metodo inserisce una pagina specificata nello stack di navigazione prima di una pagina specificata esistente, come illustrato nel diagramma seguente:

![](hierarchical-images/insert-page-before.png "Inserimento di una pagina nello Stack di navigazione")

Il [ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*) metodo rimuove la pagina specificata dallo stack di navigazione, come illustrato nel diagramma seguente:

![](hierarchical-images/remove-page.png "Rimozione di una pagina dallo Stack di navigazione")

Questi metodi consentono un'esperienza di navigazione personalizzata, ad esempio la sostituzione di una pagina di accesso con una nuova pagina, dopo un accesso con esito positivo. Esempio di codice seguente illustra questo scenario:

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

Condizione che le credenziali dell'utente siano corrette, la `MainPage` istanza viene inserita nello stack di navigazione prima della pagina corrente. Il [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) metodo rimuove quindi la pagina corrente dallo stack di navigazione, con la `MainPage` istanza diventa la pagina attiva.

## <a name="displaying-views-in-the-navigation-bar"></a>Visualizzazione delle viste nella barra di spostamento

Qualsiasi xamarin. Forms [ `View` ](xref:Xamarin.Forms.View) può essere visualizzato nella barra di spostamento di un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Questa operazione viene eseguita impostando il [ `NavigationPage.TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) proprietà associata una `View`. Questa proprietà associata può essere impostata su uno [ `Page` ](xref:Xamarin.Forms.Page)e quando il `Page` viene inserito un `NavigationPage`, il `NavigationPage` rispetterà il valore della proprietà.

L'esempio seguente, tratto dal [esempio di visualizzazione del titolo](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/), viene illustrato come impostare il [ `NavigationPage.TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) proprietà associata da XAML:

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

Ecco l'equivalente C# code:

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

Il risultato è un [ `Slider` ](xref:Xamarin.Forms.Slider) visualizzati nella barra di navigazione sul [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage):

[![Dispositivo di scorrimento TitleView](hierarchical-images/titleview-small.png "TitleView dispositivo di scorrimento")](hierarchical-images/titleview-large.png#lightbox "TitleView dispositivo di scorrimento")

> [!IMPORTANT]
> Molte visualizzazioni non sarà più visualizzato nella barra di spostamento, a meno che la dimensione della vista è specificata con il [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) proprietà. In alternativa, la vista può essere eseguito il wrapping in un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) con il [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) impostate sui valori appropriati.

Si noti che, in quanto il [ `Layout` ](xref:Xamarin.Forms.Layout) deriva dalla classe la [ `View` ](xref:Xamarin.Forms.View) (classe), il [ `TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) proprietà associata può essere impostata per visualizzare un layout classe che contiene più visualizzazioni. In iOS e Universal Windows Platform (UWP), non è possibile modificare l'altezza della barra di spostamento e quindi, ritaglio si verificherà se la visualizzazione contenuta nella barra di spostamento è più grande delle dimensioni predefinite della barra di spostamento. Tuttavia, in Android, l'altezza della barra di spostamento può essere modificato impostando il [ `NavigationPage.BarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) la proprietà associabile per una `double` che rappresenta la nuova altezza. Per altre informazioni, vedere [impostando l'altezza della barra di spostamento su un NavigationPage](~/xamarin-forms/platform/platform-specifics/consuming/android.md#navigationpage-barheight).

In alternativa, una barra di spostamento estesa può essere suggerita inserendo alcuni contenuti nella barra di spostamento e alcuni in una visualizzazione nella parte superiore del contenuto della pagina che si colore corrispondono alla barra di navigazione. Inoltre, in iOS la linea di separazione e sullo shadow che nella parte inferiore della barra di spostamento può essere rimossa mediante l'impostazione di [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) la proprietà associabile per `true`. Per altre informazioni, vedere [nascondendo il separatore barra di navigazione in un NavigationPage](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#navigationpage-hideseparatorbar).

> [!NOTE]
> Il [ `BackButtonTitle` ](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty), [ `Title` ](xref:Xamarin.Forms.Page.Title), [ `TitleIcon` ](xref:Xamarin.Forms.NavigationPage.TitleIconProperty), e [ `TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) proprietà possono definire valori che occupano spazio nella barra di spostamento. Anche se le dimensioni di barra di navigazione varia in base alle dimensioni dello schermo e della piattaforma, impostando tutte queste proprietà comporterà i conflitti a causa di spazio limitato disponibile. Invece di provare a usare una combinazione di queste proprietà, è probabile che è possibile ottenere una migliore la progettazione di barra di spostamento desiderato solo impostando il `TitleView` proprietà.

### <a name="limitations"></a>Limitazioni

Esistono alcune limitazioni da tenere presenti quando si visualizza un' [ `View` ](xref:Xamarin.Forms.View) nella barra di spostamento di un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage):

- In iOS, posizionare le viste nella barra di spostamento di un `NavigationPage` vengono visualizzati in una posizione diversa a seconda se sono abilitati i titoli di grandi dimensioni. Per altre informazioni sull'abilitazione di titoli di grandi dimensioni, vedere [visualizzazione di titoli di grandi dimensioni](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#large_title).
- In Android, posizionare le visualizzazioni nella barra di spostamento di un `NavigationPage` può essere eseguita solo nelle App che usano compatibilità delle app.
- Non è consigliabile posizionare le viste di grandi e complesse, ad esempio [ `ListView` ](xref:Xamarin.Forms.ListView) e [ `TableView` ](xref:Xamarin.Forms.TableView), nella barra di spostamento di un `NavigationPage`.

## <a name="related-links"></a>Collegamenti correlati

- [Navigazione tra le pagine](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Gerarchico (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [TitleView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/)
- [Come creare un segno nel flusso schermata di esempio xamarin. Forms (Video di Xamarin University)](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [Come creare un segno nel flusso di schermata in xamarin. Forms (Video di Xamarin University)](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
