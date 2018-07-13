---
title: Pagine modali xamarin. Forms
description: Xamarin.Forms dispone di supporto per le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata. Questo articolo viene illustrato come spostarsi tra le pagine modali.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 44aee8500c7de2ae56b59049368d6025ec49cc5e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994828"
---
# <a name="xamarinforms-modal-pages"></a>Pagine modali xamarin. Forms

_Xamarin. Forms fornisce supporto per le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata. Questo articolo viene illustrato come spostarsi tra le pagine modali._

Questo articolo tratta gli argomenti seguenti:

- [Navigazione](#Performing_Navigation) : il push di pagine in modale dello stack, pagine pop dallo stack modale, disabilitare il pulsante Indietro e animazione di transizioni di pagina.
- [Passaggio di dati durante gli spostamenti](#Passing_Data_when_Navigating) – il passaggio di dati tramite un costruttore della pagina e un `BindingContext`.

## <a name="overview"></a>Panoramica

Una pagina modale può essere uno qualsiasi dei [pagina](~/xamarin-forms/user-interface/controls/pages.md) tipi supportati da xamarin. Forms. Per visualizzare una pagina modale l'applicazione esegue il push nello stack di modale, in cui diventa la pagina attiva, come illustrato nel diagramma seguente:

![](modal-images/pushing.png "Eseguire il push di una pagina modale nello stack")

Per tornare alla pagina precedente l'applicazione preleva la pagina corrente dallo stack modale e la nuova pagina in primo piano diventa la pagina attiva, come illustrato nel diagramma seguente:

![](modal-images/popping.png "Visualizzazione di una pagina dallo Stack modale")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Navigazione

I metodi di navigazione modale sono esposti dalla proprietà [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) in qualsiasi tipo derivato [`Page`](xref:Xamarin.Forms.Page). Questi metodi offrono la possibilità [push pagine modali](#Pushing_Pages_to_the_Modal_Stack) stack modale, e [pop pagine modali](#Popping_Pages_from_the_Modal_Stack) dallo stack modale.

Il [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) proprietà espone anche una [ `ModalStack` ](xref:Xamarin.Forms.INavigation.ModalStack) proprietà da cui è possibile ottenere le pagine modali nello stack di modale. Tuttavia non esiste nessun concetto di gestione modale dello stack o di prelievo e attivazione della pagina radice nella navigazione modale. Queste operazioni non sono supportate in tutti i casi sulle piattaforme sottostanti.

> [!NOTE]
> Per la navigazione modale nelle pagine non è necessaria un'istanza di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>Eseguire il push di pagine modali nello stack

Per passare al `ModalPage` è necessario richiamare il [ `PushModalAsync` ](xref:Xamarin.Forms.INavigation.PushModalAsync*) metodo sul [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) proprietà della pagina corrente, come illustrato nell'esempio di codice seguente:

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    ...
    await Navigation.PushModalAsync (detailPage);
  }
}
```

In questo modo, il `ModalPage` fornita l'istanza da inserire nello stack di modale, in cui diventa la pagina attiva, che è stato selezionato un elemento nel [ `ListView` ](xref:Xamarin.Forms.ListView) sul `MainPage` istanza. Il `ModalPage` istanza è illustrata negli screenshot seguenti:

![](modal-images/modalpage.png "Esempio di pagina modale")

Quando [ `PushModalAsync` ](xref:Xamarin.Forms.INavigation.PushModalAsync*) viene richiamato, si verificano gli eventi seguenti:

- La chiamata di pagina `PushModalAsync` ha relativi [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) override richiamato, a condizione che Android non è la piattaforma sottostante.
- Nella pagina a cui si accede sono relativi [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) override richiamato.
- Il `PushAsync` al completamento dell'attività.

Tuttavia, l'ordine preciso che si verificano questi eventi è dipendente dalla piattaforma. Per altre informazioni, vedere [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro di xamarin. Forms Petzolds.

> [!NOTE]
> Le chiamate al [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) e [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) sostituzioni non possono essere considerate come indicazioni garantite navigazione tra le pagine. Ad esempio, in iOS il `OnDisappearing` override viene chiamato nella pagina attiva quando l'applicazione viene terminata.

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>Pagine pop dallo Stack modale

La pagina attiva può essere prelevata dallo stack modale premendo la *nuovamente* pulsante sul dispositivo, indipendentemente se si tratta di un pulsante fisico nel dispositivo o un pulsante sullo schermo.

Per tornare a livello di codice alla pagina originale, l'istanza `ModalPage` deve chiamare il metodo [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync), come illustrato nell'esempio di codice seguente:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

In questo modo, il `ModalPage` istanza da rimuovere dallo stack modale, con la nuova pagina in primo piano diventa la pagina attiva. Quando [ `PopModalAsync` ](xref:Xamarin.Forms.INavigation.PopModalAsync) viene richiamato, si verificano gli eventi seguenti:

- Chiamare il metodo di pagina `PopModalAsync` ha relativi [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) override richiamato.
- La pagina viene quindi restituita contiene relativi [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) override richiamato, a condizione che Android non è la piattaforma sottostante.
- Il `PopModalAsync` restituisce di attività.

Tuttavia, l'ordine preciso che si verificano questi eventi è dipendente dalla piattaforma. Per altre informazioni, vedere [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro di xamarin. Forms Petzolds.

### <a name="disabling-the-back-button"></a>Disabilitare il pulsante Indietro

In Android, l'utente sempre possibile tornare alla pagina precedente, premere standard *nuovamente* pulsante sul dispositivo. Se la pagina modale richiede agli utenti il completamento di un'attività indipendente prima di lasciare la pagina, l'applicazione è necessario disabilitare la *nuovamente* pulsante. Ciò può essere effettuata eseguendo l'override di [ `Page.OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed) metodo sulla pagina modale. Per altre informazioni, vedere [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro di xamarin. Forms Petzolds.

### <a name="animating-page-transitions"></a>Animazione di transizioni di pagina

Il [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) proprietà di ogni pagina fornisce inoltre sottoposto a override push e pop metodi che includono un `boolean` parametro che controlla se deve essere visualizzata un'animazione di pagina durante la navigazione, come illustrato nel codice seguente esempio:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushModalAsync (new DetailPage (), false);
}

async void OnDismissButtonClicked (object sender, EventArgs args)
{
  // Page appearance not animated
  await Navigation.PopModalAsync (false);
}
```

Impostando il `boolean` parametro per `false` disabilita l'animazione di transizione di pagina, durante l'impostazione del parametro su `true` consente l'animazione di transizione di pagina, a condizione che è supportata dalla piattaforma sottostante. Tuttavia, i metodi push e pull che non dispongono di questo parametro attiva l'animazione per impostazione predefinita.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passaggio di dati durante la navigazione

In alcuni casi è necessario per una pagina passare i dati in un'altra pagina durante la navigazione. Due tecniche per questa operazione sono passando i dati tramite un costruttore della pagina e impostando la nuova pagina [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) ai dati. Ogni ora parleremo a sua volta.

### <a name="passing-data-through-a-page-constructor"></a>Passaggio di dati tramite un costruttore di pagina

La tecnica più semplice per passare dati a un'altra pagina durante la navigazione è tramite un parametro di costruttore di pagina, illustrata nell'esempio di codice seguente:

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

Questo codice crea un `MainPage` istanza, passando la data e ora correnti nel formato ISO8601.

Il `MainPage` istanza riceve i dati tramite un parametro di costruttore, come illustrato nell'esempio di codice seguente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

I dati vengono quindi visualizzati nella pagina impostando il [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) proprietà.

### <a name="passing-data-through-a-bindingcontext"></a>Passaggio di dati tramite un oggetto BindingContext

Un approccio alternativo per passare dati a un'altra pagina durante l'esplorazione consiste nell'impostare la nuova pagina [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) ai dati, come illustrato nell'esempio di codice seguente:

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    detailPage.BindingContext = e.SelectedItem as Contact;
    listView.SelectedItem = null;
    await Navigation.PushModalAsync (detailPage);
  }
}
```

Questo codice imposta la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del `DetailPage` dell'istanza per il `Contact` istanza e quindi si passa al `DetailPage`.

Il `DetailPage` Usa quindi l'associazione dati per visualizzare il `Contact` istanza dei dati, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ModalNavigation.DetailPage">
    <ContentPage.Padding>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,40,0,0" />
      </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" FontSize="Medium" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
              ...
            <Button x:Name="dismissButton" Text="Dismiss" Clicked="OnDismissButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Esempio di codice seguente viene illustrato come può essere eseguito il data binding in c#:

```csharp
public class DetailPageCS : ContentPage
{
  public DetailPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var dismissButton = new Button { Text = "Dismiss" };
    dismissButton.Clicked += OnDismissButtonClicked;

    Thickness padding;
    switch (Device.RuntimePlatform)
    {
        case Device.iOS:
            padding = new Thickness(0, 40, 0, 0);
            break;
        default:
            padding = new Thickness();
            break;
    }

    Padding = padding;
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
        dismissButton
      }
    };
  }

  async void OnDismissButtonClicked (object sender, EventArgs args)
  {
    await Navigation.PopModalAsync ();
  }
}
```

I dati vengono quindi visualizzati nella pagina da una serie di [ `Label` ](xref:Xamarin.Forms.Label) controlli.

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Nozioni di base sul data binding).

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come spostarsi tra le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.


## <a name="related-links"></a>Collegamenti correlati

- [Navigazione tra le pagine](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Finestra modale (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Modal/)
- [PassingData (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
