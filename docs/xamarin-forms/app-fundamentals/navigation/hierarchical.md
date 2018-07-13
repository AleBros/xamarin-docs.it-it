---
title: Navigazione gerarchica
description: Questo articolo illustra come usare la classe NavigationPage per eseguire l'esplorazione in uno stack last-in, First-Out (LIFO) pagine.
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: f8f8f9b4e5755e8b1707178fef633321b64e4e94
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994676"
---
# <a name="hierarchical-navigation"></a>Navigazione gerarchica

_La classe NavigationPage offre un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine avanti e indietro in base alle esigenze. La classe implementa la navigazione come un mazzo last in, First-Out (LIFO) di oggetti della pagina. Questo articolo illustra come usare la classe NavigationPage per eseguire l'esplorazione in uno stack di pagine._

Questo articolo tratta gli argomenti seguenti:

- [Navigazione](#Performing_Navigation) : creazione di una pagina radice, il push di pagine in stack di navigazione, si estraggono le pagine dallo stack di navigazione e l'animazione di transizioni di pagina.
- [Passaggio di dati durante gli spostamenti](#Passing_Data_when_Navigating) – il passaggio di dati tramite un costruttore della pagina e un `BindingContext`.
- [Modifica lo stack di navigazione](#Manipulating_the_Navigation_Stack) : la modifica dello stack inserendo o rimozione di alcune pagine.

## <a name="overview"></a>Panoramica

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

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come usare il [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) classe per eseguire l'esplorazione in uno stack di pagine. Questa classe fornisce un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine avanti e indietro in base alle esigenze. La classe implementa la navigazione come stack LIFO (Last-In, First-Out) di oggetti [`Page`](xref:Xamarin.Forms.Page).


## <a name="related-links"></a>Collegamenti correlati

- [Navigazione tra le pagine](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Gerarchico (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [Come creare un segno nel flusso schermata di esempio xamarin. Forms (Video di Xamarin University)](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [Come creare un segno nel flusso di schermata in xamarin. Forms (Video di Xamarin University)](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
