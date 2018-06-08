---
title: Pagine modale
description: Xamarin.Forms dispone di supporto per le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata. In questo articolo viene illustrato come passare alle pagine modale.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 30d0371e0eaa31673561ae12c7a46b7a7819a647
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847419"
---
# <a name="modal-pages"></a>Pagine modale

_Xamarin. Forms fornisce supporto per le pagine modale. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata. In questo articolo viene illustrato come passare alle pagine modale._

In questo articolo vengono illustrati gli argomenti seguenti:

- [Esecuzione di navigazione](#Performing_Navigation) : push pagine allo stack modale, POP pagine dallo stack modale, disabilitare il pulsante Indietro e animazione delle transizioni di pagina.
- [Il passaggio di dati durante la navigazione](#Passing_Data_when_Navigating) : passaggio di dati tramite un costruttore della pagina e un `BindingContext`.

## <a name="overview"></a>Panoramica

Una pagina modale può essere uno del [pagina](~/xamarin-forms/user-interface/controls/pages.md) tipi supportati da xamarin. Forms. Per visualizzare una pagina modale l'applicazione verrà spingerla stack modale, in cui quest ' ultimo è la pagina attiva, come illustrato nel diagramma seguente:

![](modal-images/pushing.png "Inserimento di una pagina Stack modale")

Per tornare alla pagina precedente, l'applicazione verrà visualizzata la pagina corrente dallo stack modale e la nuova pagina di primo livello diventa la pagina attiva, come illustrato nel diagramma seguente:

![](modal-images/popping.png "Visualizzazione di una pagina dallo Stack modale")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Esecuzione di navigazione

I metodi di navigazione modale sono esposti dalla proprietà [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) in qualsiasi tipo derivato [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). Questi metodi offrono la possibilità di [push pagine modale](#Pushing_Pages_to_the_Modal_Stack) stack modale e [pop pagine modale](#Popping_Pages_from_the_Modal_Stack) dallo stack modale.

Il [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) proprietà espone inoltre un [ `ModalStack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) proprietà da cui è possibile ottenere le pagine modale nello stack modale. Tuttavia non esiste nessun concetto di gestione modale dello stack o di prelievo e attivazione della pagina radice nella navigazione modale. Queste operazioni non sono supportate in tutti i casi sulle piattaforme sottostanti.

> [!NOTE]
> Per la navigazione modale nelle pagine non è necessaria un'istanza di [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/).

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>Inserimento di pagine Stack modale

Per passare al `ModalPage` è necessario richiamare il [ `PushModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/) metodo il [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) proprietà della pagina corrente, come illustrato nell'esempio di codice seguente:

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

In questo modo il `ModalPage` fornita l'istanza di eseguire il push nello stack modale, in cui diventa la pagina attiva, che è stato selezionato un elemento nel [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) sul `MainPage` istanza. Il `ModalPage` istanza viene visualizzata nelle schermate seguenti:

![](modal-images/modalpage.png "Esempio di pagina modale")

Quando [ `PushModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/) viene richiamato, si verificano gli eventi seguenti:

- La chiamata di pagina `PushModalAsync` è relativo [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) override richiamato, purché la piattaforma sottostante non Android.
- La pagina a cui si accede è relativo [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) override richiamato.
- Il `PushAsync` al completamento dell'attività.

Tuttavia, l'ordine esatto che questi eventi si verificano è dipendente dalla piattaforma. Per ulteriori informazioni, vedere [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) di libro di Petzolds xamarin. Forms.

> [!NOTE]
> Le chiamate al [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) e [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) override non possono essere considerati come indicazioni garantite di navigazione a pagina. Ad esempio, in iOS, il `OnDisappearing` viene chiamato l'override nella pagina attiva alla chiusura dell'applicazione.

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>Pagine pop dallo Stack modale

La pagina attiva può essere estratto dallo stack modale premendo il *nuovamente* pulsante sul dispositivo, indipendentemente se si tratta di un pulsante fisico sul dispositivo o un pulsante visualizzato sullo schermo.

Per tornare a livello di codice alla pagina originale, l'istanza `ModalPage` deve chiamare il metodo [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/), come illustrato nell'esempio di codice seguente:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

In questo modo il `ModalPage` istanza da rimuovere dallo stack modale, con la nuova pagina di livello più alto, diventando la pagina attiva. Quando [ `PopModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/) viene richiamato, si verificano gli eventi seguenti:

- La chiamata di pagina `PopModalAsync` è relativo [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) override richiamato.
- La pagina viene quindi restituita ha il [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) override richiamato, purché la piattaforma sottostante non Android.
- Il `PopModalAsync` attività restituisce.

Tuttavia, l'ordine esatto che questi eventi si verificano è dipendente dalla piattaforma. Per ulteriori informazioni, vedere [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) di libro di Petzolds xamarin. Forms.

### <a name="disabling-the-back-button"></a>Disabilitare il pulsante Indietro

In Android, l'utente sempre possibile tornare alla pagina precedente premendo standard *Indietro* pulsante sul dispositivo. Se la pagina modale richiede all'utente di completare un'attività indipendente prima di uscire dalla pagina, è necessario disabilitare l'applicazione di *nuovamente* pulsante. Questo può essere effettuato eseguendo l'override di [ `Page.OnBackButtonPressed` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnBackButtonPressed/) metodo nella pagina modale. Per ulteriori informazioni vedere [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) di libro di Petzolds xamarin. Forms.

### <a name="animating-page-transitions"></a>Animazione di transizioni di pagina

Il [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) proprietà di ogni pagina fornisce inoltre sottoposto a override push e pop metodi che includono un `boolean` parametro che controlla se visualizzare un'animazione di pagina durante la navigazione, come illustrato nel codice seguente esempio:

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

L'impostazione di `boolean` parametro `false` disabilita l'animazione di transizione di pagina, impostando il parametro `true` consente l'animazione di transizione di pagina, a condizione che è supportata dalla piattaforma sottostante. Tuttavia, i metodi di push e pop che non dispongono di questo parametro abilitano l'animazione per impostazione predefinita.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passaggio di dati durante l'esplorazione

In alcuni casi è necessario per una pagina passare dati a un'altra pagina durante la navigazione. Due tecniche a tale scopo sono passando i dati tramite un costruttore di pagina e impostando la nuova pagina [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) ai dati. Ora ogni verranno descritti in dettaglio.

### <a name="passing-data-through-a-page-constructor"></a>Passaggio di dati tramite un costruttore di pagina

La tecnica più semplice per passare dati a un'altra pagina durante la navigazione avviene tramite un parametro di costruttore di pagina, come illustrato nell'esempio di codice seguente:

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

Questo codice crea un `MainPage` istanza, passando la data e ora correnti nel formato ISO8601.

Il `MainPage` istanza riceve i dati attraverso un parametro di costruttore, come illustrato nell'esempio di codice seguente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

I dati vengono quindi visualizzati nella pagina impostando la [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) proprietà.

### <a name="passing-data-through-a-bindingcontext"></a>Passaggio di dati tramite un oggetto BindingContext

È un approccio alternativo per passare dati a un'altra pagina durante l'esplorazione impostando la nuova pagina [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) ai dati, come illustrato nell'esempio di codice seguente:

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

Questo codice imposta il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del `DetailPage` istanza per il `Contact` istanza e quindi si passa al `DetailPage`.

Il `DetailPage` utilizza quindi l'associazione dati per visualizzare il `Contact` istanza dei dati, come illustrato nell'esempio di codice XAML seguente:

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

Esempio di codice seguente viene illustrato come l'associazione di dati può essere eseguita in c#:

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

I dati vengono quindi visualizzati nella pagina da una serie di [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controlli.

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Nozioni di base sul data binding).

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come spostarsi tra le pagine modale. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.


## <a name="related-links"></a>Collegamenti correlati

- [Navigazione a pagina](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Modal/)
- [PassingData (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
