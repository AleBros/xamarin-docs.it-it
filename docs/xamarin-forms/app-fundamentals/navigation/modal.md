---
title: Pagine modali Xamarin.Forms
description: Xamarin.Forms dispone di supporto per le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata. Questo articolo illustra come spostarsi tra pagine modali.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 7a4c67f067b73873c3d1de4499abda2703217ddf
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760830"
---
# <a name="xamarinforms-modal-pages"></a>Pagine modali Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_Xamarin.Forms supporta le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata. Questo articolo illustra come spostarsi tra pagine modali._

Nell'articolo vengono trattati gli argomenti seguenti:

- [Eseguire spostamenti](#Performing_Navigation) - Push delle pagine nello stack modale, prelievo delle pagine dallo stack modale, disabilitazione del pulsante Indietro e animazione delle transizioni di pagina.
- [Passaggio di dati durante gli spostamenti](#Passing_Data_when_Navigating) - Passaggio di dati tramite un costruttore di pagina e un `BindingContext`.

## <a name="overview"></a>Panoramica

Una pagina modale può essere di uno qualsiasi dei tipi [Page](~/xamarin-forms/user-interface/controls/pages.md) supportati da Xamarin.Forms. Per visualizzare una pagina modale, l'applicazione ne eseguirà il push nello stack modale, in cui diventerà la pagina attiva, come illustrato nel diagramma seguente:

![](modal-images/pushing.png "Push di una pagina nello stack modale")

Per tornare alla pagina precedente, l'applicazione preleverà la pagina corrente dallo stack modale e la nuova pagina in primo piano diventa la pagina attiva, come illustrato nel diagramma seguente:

![](modal-images/popping.png "Prelievo di una pagina dallo stack modale")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Esecuzione degli spostamenti

I metodi di navigazione modale sono esposti dalla proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) in qualsiasi tipo derivato [`Page`](xref:Xamarin.Forms.Page). Questi metodi offrono la possibilità di eseguire il [push delle pagine modali](#Pushing_Pages_to_the_Modal_Stack) nello stack modale e di [prelevare le pagine modali](#Popping_Pages_from_the_Modal_Stack) dallo stack modale.

La proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) espone anche una proprietà [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) dalla quale è possibile ottenere le pagine modali nello stack modale. Tuttavia non esiste nessun concetto di gestione modale dello stack o di prelievo e attivazione della pagina radice nella navigazione modale. Queste operazioni non sono supportate in tutti i casi sulle piattaforme sottostanti.

> [!NOTE]
> Per la navigazione modale nelle pagine non è necessaria un'istanza di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>Push delle pagine nello stack modale

Per passare a `ModalPage`, è necessario richiamare il metodo [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) sulla proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) della pagina corrente, come illustrato nell'esempio di codice seguente:

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

In questo modo viene attivato il push dell'istanza di `ModalPage` nello stack modale, in cui diventa la pagina attiva, a condizione che sia stato selezionato un elemento nel controllo [`ListView`](xref:Xamarin.Forms.ListView) per l'istanza di `MainPage`. L'istanza di `ModalPage` è illustrata negli screenshot seguenti:

![](modal-images/modalpage.png "Esempio di pagina modale")

Quando si richiama [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*), si verificano gli eventi seguenti:

- Per la pagina che chiama `PushModalAsync` viene richiamato l'override di [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing), a condizione che la piattaforma sottostante non sia Android.
- Per la pagina di destinazione dello spostamento viene richiamato l'override di [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing).
- L'attività `PushAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per altre informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del manuale di Xamarin.Forms redatto da Charles Petzold.

> [!NOTE]
> Le chiamate degli override di [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) e [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) non possono essere considerate come indicazioni garantite dell'avvenuto spostamento tra le pagine. Ad esempio, in iOS l'override di `OnDisappearing` viene chiamato per la pagina attiva quando l'applicazione viene terminata.

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>Prelievo delle pagine dallo stack modale

La pagina attiva può essere prelevata dallo stack modale premendo il pulsante *Indietro* del dispositivo, indipendentemente dal fatto che sia un pulsante fisico del dispositivo o un pulsante su schermo.

Per tornare a livello di codice alla pagina originale, l'istanza `ModalPage` deve chiamare il metodo [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync), come illustrato nell'esempio di codice seguente:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Di conseguenza, l'istanza di `ModalPage` viene rimossa dallo stack modale e la nuova pagina in primo piano diventa la pagina attiva. Quando si richiama [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync), si verificano gli eventi seguenti:

- Per la pagina che chiama `PopModalAsync` viene richiamato l'override di [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing).
- Per la pagina a cui si ritorna viene richiamato l'override di [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing), a condizione che la piattaforma sottostante non sia Android.
- L'attività `PopModalAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per altre informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del manuale di Xamarin.Forms redatto da Charles Petzold.

### <a name="disabling-the-back-button"></a>Disabilitazione del pulsante Indietro

In Android, l'utente può sempre tornare alla pagina precedente premendo il pulsante *Indietro* standard sul dispositivo. Se la pagina modale richiede all'utente di completare un'attività indipendente prima di lasciare la pagina, l'applicazione deve disabilitare il pulsante *Indietro*. Questa operazione può essere effettuata eseguendo l'override del metodo [`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) nella pagina modale. Per altre informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del manuale di Xamarin.Forms redatto da Charles Petzold.

### <a name="animating-page-transitions"></a>Animazione delle transizioni di pagina

La proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) di ogni pagina offre anche metodi di push e di prelievo sottoposti a override, che includono un parametro `boolean` che controlla la visualizzazione di un'animazione di pagina durante gli spostamenti, come illustrato nell'esempio di codice seguente:

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

L'impostazione del parametro `boolean` su `false` disabilita l'animazione per la transizione di pagina, mentre l'impostazione del parametro su `true` abilita l'animazione per la transizione di pagina, a condizione che sia supportata dalla piattaforma sottostante. Tuttavia, i metodi di push e prelievo senza questo parametro abilitano l'animazione per impostazione predefinita.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passaggio di dati durante gli spostamenti

In alcuni casi, una pagina deve passare dati a un'altra pagina durante gli spostamenti. Due tecniche per questa operazione sono il passaggio dei dati tramite un costruttore di pagina e l'impostazione di [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della nuova pagina sui dati. Di seguito verranno descritti entrambi.

### <a name="passing-data-through-a-page-constructor"></a>Passaggio dei dati tramite un costruttore di pagina

La tecnica più semplice per il passaggio di dati a un'altra pagina durante gli spostamenti è tramite un parametro del costruttore di pagina, come illustrato nell'esempio di codice seguente:

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

Questo codice crea un'istanza di `MainPage`, passando la data e l'ora correnti in formato ISO8601.

L'istanza di `MainPage` riceve i dati tramite un parametro del costruttore, come illustrato nell'esempio di codice seguente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

I dati vengono quindi visualizzati nella pagina impostando la proprietà [`Label.Text`](xref:Xamarin.Forms.Label.Text).

### <a name="passing-data-through-a-bindingcontext"></a>Passaggio dei dati tramite BindingContext

Un approccio alternativo per passare dati a un'altra pagina durante gli spostamenti consiste nell'impostare [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della nuova pagina sui dati, come illustrato nell'esempio di codice seguente:

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

Questo codice imposta [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) dell'istanza di `DetailPage` sull'istanza di `Contact` e quindi passa a `DetailPage`.

`DetailPage` usa quindi il data binding per visualizzare i dati dell'istanza di `Contact`, come illustrato nell'esempio di codice XAML seguente:

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

L'esempio di codice seguente illustra come è possibile implementare il data binding in C#:

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

I dati vengono quindi visualizzati nella pagina tramite una serie di controlli [`Label`](xref:Xamarin.Forms.Label).

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Nozioni di base sul data binding).

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come spostarsi tra pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.

## <a name="related-links"></a>Collegamenti correlati

- [Page Navigation](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) (Spostamenti tra le pagine)
- [Modal (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal) (Esempio di pagina modale)
- [Passing Data (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata) (Esempio di passaggio dei dati)
