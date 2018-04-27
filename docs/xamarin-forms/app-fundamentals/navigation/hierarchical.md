---
title: Navigazione gerarchica
description: La classe NavigationPage fornisce un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine, Avanti e indietro, in base alle esigenze. La classe implementa navigazione come uno stack last-in, First-Out (LIFO) degli oggetti di pagina. In questo articolo viene illustrato come utilizzare la classe NavigationPage per spostarsi in una pila di pagine.
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 3fc5b24474230fd2b2477f020ac24cd72996d7b1
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2018
---
# <a name="hierarchical-navigation"></a>Navigazione gerarchica

_La classe NavigationPage fornisce un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine, Avanti e indietro, in base alle esigenze. La classe implementa navigazione come uno stack last-in, First-Out (LIFO) degli oggetti di pagina. In questo articolo viene illustrato come utilizzare la classe NavigationPage per spostarsi in una pila di pagine._

In questo articolo vengono illustrati gli argomenti seguenti:

- [Esecuzione di navigazione](#Performing_Navigation) : creazione di una pagina principale, inserendo pagine allo stack di navigazione, si estraggono pagine dallo stack di navigazione e animazione delle transizioni di pagina.
- [Il passaggio di dati durante la navigazione](#Passing_Data_when_Navigating) : passaggio di dati tramite un costruttore della pagina e un `BindingContext`.
- [Modifica lo stack di navigazione](#Manipulating_the_Navigation_Stack) -modifica lo stack di inserimento o della rimozione delle pagine.

## <a name="overview"></a>Panoramica

Per passare da una pagina a un altro, un'applicazione determinerà una nuova pagina nello stack di navigazione, in cui quest ' ultimo è la pagina attiva, come illustrato nel diagramma seguente:

![](hierarchical-images/pushing.png "Inserimento di una pagina di Stack di navigazione")

Per tornare alla pagina precedente, l'applicazione verrà visualizzata la pagina corrente nello stack di navigazione e la nuova pagina di primo livello diventa la pagina attiva, come illustrato nel diagramma seguente:

![](hierarchical-images/popping.png "Stack di navigazione di una pagina di visualizzazione")

Metodi di navigazione esposte dal [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) proprietà uno [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) tipi derivati. Questi metodi offrono la possibilità di effettuare il push di pagine nello stack di navigazione, alle pagine pop dallo stack di navigazione e per eseguire la modifica dello stack.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Esecuzione di navigazione

Nella navigazione gerarchica la classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) viene usata per navigare in uno stack di oggetti [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). La schermata seguente mostra i componenti principali del `NavigationPage` in ogni piattaforma:

![](hierarchical-images/navigationpage-components.png "Componenti NavigationPage")

Il layout di un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) dipende dalla piattaforma:

- In iOS, una barra di spostamento è presente nella parte superiore della pagina che viene visualizzato un titolo, e che ha un *nuovamente* pulsante Torna alla pagina precedente.
- In Android, è presente nella parte superiore della pagina che consente di visualizzare un titolo, un'icona, una barra di spostamento e un *nuovamente* pulsante Torna alla pagina precedente. L'icona è definito nel `[Activity]` attributo che decora il `MainActivity` classe nel progetto specifico della piattaforma Android.
- Nella piattaforma Windows universale, una barra di spostamento è presente nella parte superiore della pagina che consente di visualizzare un titolo. 

In tutte le piattaforme, il valore di [ `Page.Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) verrà visualizzata come titolo della pagina proprietà.

> [!NOTE]
> È consigliabile un `NavigationPage` deve essere popolato con `ContentPage` solo istanze.

### <a name="creating-the-root-page"></a>Creazione di una pagina principale

La prima pagina aggiunta a uno stack di navigazione è definita pagina *radice* dell'applicazione e il codice seguente illustra come viene eseguita questa operazione:

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

In questo modo il `Page1Xaml` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanza da inserire nello stack di navigazione, in cui diventa la pagina attiva e la pagina principale dell'applicazione. Come illustrato nelle schermate seguenti:

![](hierarchical-images/mainpage.png "Pagina principale dello Stack di navigazione")

> [!NOTE]
> Il [ `RootPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.RootPage/) proprietà di un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) istanza fornisce l'accesso alla prima pagina nello stack di navigazione.

### <a name="pushing-pages-to-the-navigation-stack"></a>Inserimento di pagine nello Stack di navigazione

Per passare a `Page2Xaml`, è necessario richiamare il [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) metodo il [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) proprietà della pagina corrente, come illustrato nell'esempio di codice seguente:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Di conseguenza l'istanza `Page2Xaml` viene inserita tramite push nello stack di navigazione, in cui diventa la pagina attiva. Come illustrato nelle schermate seguenti:

![](hierarchical-images/secondpage.png "Pagina inserito nello Stack di navigazione")

Quando il [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) metodo viene richiamato, si verificano gli eventi seguenti:

- La chiamata di pagina `PushAsync` è relativo [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) override richiamato.
- La pagina a cui si accede è relativo [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) override richiamato.
- Il `PushAsync` al completamento dell'attività.

Tuttavia, l'ordine esatto in cui questi eventi si verificano è dipendente dalla piattaforma. Per ulteriori informazioni, vedere [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) di libro di Petzolds xamarin. Forms.

> [!NOTE]
> Le chiamate al [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) e [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) override non possono essere considerati come indicazioni garantite di navigazione a pagina. Ad esempio, in iOS, il `OnDisappearing` viene chiamato l'override nella pagina attiva alla chiusura dell'applicazione.

### <a name="popping-pages-from-the-navigation-stack"></a>Pagine pop dallo Stack di navigazione

La pagina attiva può essere prelevata dallo stack di navigazione premendo il pulsante *Indietro* (fisico o su schermo) del dispositivo.

Per tornare a livello di codice alla pagina originale, l'istanza `Page2Xaml` deve chiamare il metodo [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/), come illustrato nell'esempio di codice seguente:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Di conseguenza l'istanza `Page2Xaml` viene rimossa dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva. Quando il [ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) metodo viene richiamato, si verificano gli eventi seguenti:

- La chiamata di pagina `PopAsync` è relativo [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) override richiamato.
- La pagina viene quindi restituita ha il [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) override richiamato.
- Il `PopAsync` attività restituisce.

Tuttavia, l'ordine esatto in cui questi eventi si verificano è dipendente dalla piattaforma. Per ulteriori informazioni vedere [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) di libro di Petzolds xamarin. Forms.

Così come [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) e [ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) metodi, il [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) proprietà di ogni pagina fornisce inoltre un [ `PopToRootAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopToRootAsync()/) (metodo), come illustrato nell'esempio di codice seguente:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Questo metodo estrae tutte tranne radice [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) dallo stack di navigazione, rendendo pertanto la pagina principale dell'applicazione la pagina attiva.

### <a name="animating-page-transitions"></a>Animazione di transizioni di pagina

Il [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) proprietà di ogni pagina fornisce inoltre sottoposto a override push e pop metodi che includono un `boolean` parametro che controlla se visualizzare un'animazione di pagina durante la navigazione, come illustrato nel codice seguente esempio:

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

L'impostazione di `boolean` parametro `false` disabilita l'animazione di transizione di pagina, impostando il parametro `true` consente l'animazione di transizione di pagina, a condizione che è supportata dalla piattaforma sottostante. Tuttavia, i metodi di push e pop che non dispongono di questo parametro abilitano l'animazione per impostazione predefinita.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passaggio di dati durante l'esplorazione

In alcuni casi è necessario per una pagina passare dati a un'altra pagina durante la navigazione. Due tecniche per il completamento di questo siano passando i dati tramite un costruttore della pagina e impostando la nuova pagina [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) ai dati. Ora ogni verranno descritti in dettaglio.

### <a name="passing-data-through-a-page-constructor"></a>Passaggio di dati tramite un costruttore di pagina

La tecnica più semplice per passare dati a un'altra pagina durante la navigazione avviene tramite un parametro di costruttore di pagina, come illustrato nell'esempio di codice seguente:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Questo codice crea un `MainPage` dell'istanza, passando la data e ora correnti nel formato ISO8601, che viene eseguito il wrapping un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) istanza.

Il `MainPage` istanza riceve i dati attraverso un parametro di costruttore, come illustrato nell'esempio di codice seguente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

I dati vengono quindi visualizzati nella pagina impostando la [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) proprietà, come illustrato nelle schermate seguenti:

![](hierarchical-images/passing-data-constructor.png "Dati passati tramite un costruttore di pagina")

### <a name="passing-data-through-a-bindingcontext"></a>Passaggio di dati tramite un oggetto BindingContext

È un approccio alternativo per passare dati a un'altra pagina durante l'esplorazione impostando la nuova pagina [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) ai dati, come illustrato nell'esempio di codice seguente:

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

Questo codice imposta il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del `SecondPage` istanza per il `Contact` istanza e quindi si passa al `SecondPage`.

Il `SecondPage` utilizza quindi l'associazione dati per visualizzare il `Contact` istanza dei dati, come illustrato nell'esempio di codice XAML seguente:

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

Esempio di codice seguente viene illustrato come l'associazione di dati può essere eseguita in c#:

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

I dati vengono quindi visualizzati nella pagina da una serie di [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controlli, come illustrato nelle schermate seguenti:

![](hierarchical-images/passing-data-bindingcontext.png "Dati passati tramite un oggetto BindingContext")

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Modifica lo Stack di navigazione

Il [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) proprietà espone un [ `NavigationStack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.NavigationStack/) proprietà da cui è possibile ottenere le pagine nello stack di navigazione. Sebbene xamarin. Forms mantiene l'accesso allo stack di navigazione, il `Navigation` proprietà fornisce il [ `InsertPageBefore` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page)/) e [ `RemovePage` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page)/) metodi per modificare lo stack inserendo pagine o la rimozione.

Il [ `InsertPageBefore` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page)/) metodo inserisce una pagina specificata nello stack di navigazione prima di una pagina specificata esistente, come illustrato nel diagramma seguente:

![](hierarchical-images/insert-page-before.png "Inserimento di una pagina nello Stack di navigazione")

Il [ `RemovePage` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page)/) metodo rimuove la pagina specificata dallo stack di navigazione, come illustrato nel diagramma seguente:

![](hierarchical-images/remove-page.png "Rimozione di una pagina dallo Stack di navigazione")

Questi metodi consentono un'esperienza di navigazione personalizzati, ad esempio la sostituzione di una pagina di accesso con una nuova pagina, dopo un accesso con esito positivo. Esempio di codice seguente illustra questo scenario:

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

Condizione che le credenziali dell'utente siano corrette, la `MainPage` istanza viene inserita nello stack di navigazione prima della pagina corrente. Il [ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) metodo quindi rimuove lo stack di navigazione, la pagina corrente con il `MainPage` istanza diventare la pagina attiva.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare il [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) classe per eseguire una navigazione in uno stack di pagine. Questa classe fornisce un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine, Avanti e indietro, in base alle esigenze. La classe implementa la navigazione come stack LIFO (Last-In, First-Out) di oggetti [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/).


## <a name="related-links"></a>Collegamenti correlati

- [Navigazione a pagina](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Gerarchico (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [Come creare un segno nel flusso schermata nell'esempio di xamarin. Forms (Video di University Xamarin)](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [Come creare un segno nel flusso di schermata in xamarin. Forms (Video di University Xamarin)](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)
