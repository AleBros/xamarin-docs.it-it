---
title: Xamarin.FormsPagine modali
description: Xamarin.Formsfornisce supporto per le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata. Questo articolo illustra come spostarsi tra pagine modali.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aecab26efaed9815ec6916877b5f42297821582c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84569608"
---
# <a name="xamarinforms-modal-pages"></a>Xamarin.FormsPagine modali

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_Novell. Forms fornisce il supporto per le pagine modali. Una pagina modale consiglia agli utenti di completare un'attività autonoma che non può essere spostata da finché l'attività non viene completata o annullata. Questo articolo illustra come passare alle pagine modali._

Questo articolo tratta gli argomenti seguenti:

- [Eseguire spostamenti](#performing-navigation) - Push delle pagine nello stack modale, prelievo delle pagine dallo stack modale, disabilitazione del pulsante Indietro e animazione delle transizioni di pagina.
- [Passaggio di dati durante gli spostamenti](#passing-data-when-navigating) - Passaggio di dati tramite un costruttore di pagina e un `BindingContext`.

## <a name="overview"></a>Panoramica

Una pagina modale può essere qualsiasi tipo di [pagina](~/xamarin-forms/user-interface/controls/pages.md) supportata da Xamarin.Forms . Per visualizzare una pagina modale, l'applicazione ne eseguirà il push nello stack modale, in cui diventerà la pagina attiva, come illustrato nel diagramma seguente:

![](modal-images/pushing.png "Pushing a Page to the Modal Stack")

Per tornare alla pagina precedente, l'applicazione preleverà la pagina corrente dallo stack modale e la nuova pagina in primo piano diventa la pagina attiva, come illustrato nel diagramma seguente:

![](modal-images/popping.png "Popping a Page from the Modal Stack")

## <a name="performing-navigation"></a>Esecuzione degli spostamenti

I metodi di navigazione modale sono esposti dalla [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà su tutti i [`Page`](xref:Xamarin.Forms.Page) tipi derivati. Questi metodi offrono la possibilità di eseguire il [push delle pagine modali](#pushing-pages-to-the-modal-stack) nello stack modale e di [prelevare le pagine modali](#popping-pages-from-the-modal-stack) dallo stack modale.

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà espone anche una [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) proprietà dalla quale è possibile ottenere le pagine modali nello stack modale. Tuttavia non esiste nessun concetto di gestione modale dello stack o di prelievo e attivazione della pagina radice nella navigazione modale. Queste operazioni non sono supportate in tutti i casi sulle piattaforme sottostanti.

> [!NOTE]
> Un' [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) istanza non è necessaria per eseguire la navigazione modale della pagina.

### <a name="pushing-pages-to-the-modal-stack"></a>Push delle pagine nello stack modale

Per passare a, `ModalPage` è necessario richiamare il [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) Metodo sulla [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà della pagina corrente, come illustrato nell'esempio di codice seguente:

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

In questo modo l' `ModalPage` istanza viene inserita nello stack modale, in cui diventa la pagina attiva, purché sia stato selezionato un elemento in nell' [`ListView`](xref:Xamarin.Forms.ListView) `MainPage` istanza. L'istanza di `ModalPage` è illustrata negli screenshot seguenti:

![](modal-images/modalpage.png "Modal Page Example")

Quando [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) viene richiamato, si verificano gli eventi seguenti:

- Per la chiamata della pagina `PushModalAsync` è stato [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) richiamato il relativo override, purché la piattaforma sottostante non sia Android.
- Viene richiamata la sostituzione della pagina a cui è stato eseguito lo spostamento [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) .
- L'attività `PushAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per ulteriori informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro di Charles Petzold Xamarin.Forms .

> [!NOTE]
> Le chiamate a [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) e le [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) sostituzioni non possono essere considerate come indicazioni garantite della navigazione tra le pagine. Ad esempio, in iOS l'override di `OnDisappearing` viene chiamato per la pagina attiva quando l'applicazione viene terminata.

### <a name="popping-pages-from-the-modal-stack"></a>Prelievo delle pagine dallo stack modale

La pagina attiva può essere prelevata dallo stack modale premendo il pulsante *Indietro* del dispositivo, indipendentemente dal fatto che sia un pulsante fisico del dispositivo o un pulsante su schermo.

Per tornare a livello di codice alla pagina originale, l' `ModalPage` istanza deve richiamare il [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) metodo, come illustrato nell'esempio di codice seguente:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Di conseguenza, l'istanza di `ModalPage` viene rimossa dallo stack modale e la nuova pagina in primo piano diventa la pagina attiva. Quando [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) viene richiamato, si verificano gli eventi seguenti:

- Per la chiamata della pagina `PopModalAsync` è stato [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) richiamato il relativo override.
- Viene richiamata la sostituzione della pagina a cui è stato [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) richiamato, purché la piattaforma sottostante non sia Android.
- L'attività `PopModalAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per ulteriori informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro di Charles Petzold Xamarin.Forms .

### <a name="disabling-the-back-button"></a>Disabilitazione del pulsante Indietro

In Android, l'utente può sempre tornare alla pagina precedente premendo il pulsante *Indietro* standard sul dispositivo. Se la pagina modale richiede all'utente di completare un'attività indipendente prima di lasciare la pagina, l'applicazione deve disabilitare il pulsante *Indietro*. Questa operazione può essere eseguita eseguendo l'override del [`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) metodo nella pagina modale. Per ulteriori informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro di Charles Petzold Xamarin.Forms .

### <a name="animating-page-transitions"></a>Animazione delle transizioni di pagina

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà di ogni pagina fornisce anche metodi push e pop sottoposti a override che includono un `boolean` parametro che controlla se visualizzare un'animazione di pagina durante la navigazione, come illustrato nell'esempio di codice seguente:

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

## <a name="passing-data-when-navigating"></a>Passaggio di dati durante gli spostamenti

In alcuni casi, una pagina deve passare dati a un'altra pagina durante gli spostamenti. Due tecniche per eseguire questa operazione sono il passaggio dei dati tramite un costruttore di pagina e l'impostazione della nuova pagina sui [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) dati. Di seguito verranno descritti entrambi.

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

I dati vengono quindi visualizzati nella pagina impostando la [`Label.Text`](xref:Xamarin.Forms.Label.Text) Proprietà.

### <a name="passing-data-through-a-bindingcontext"></a>Passaggio dei dati tramite BindingContext

Un approccio alternativo per passare i dati a un'altra pagina durante la navigazione consiste nell'impostare la nuova pagina sui [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) dati, come illustrato nell'esempio di codice seguente:

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

Questo codice imposta l'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) dell' `DetailPage` istanza sull' `Contact` istanza di, quindi passa a `DetailPage` .

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

I dati vengono quindi visualizzati nella pagina da una serie di [`Label`](xref:Xamarin.Forms.Label) controlli.

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Nozioni di base sul data binding).

## <a name="summary"></a>Summary

In questo articolo è stato illustrato come spostarsi tra pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.

## <a name="related-links"></a>Collegamenti correlati

- [Navigazione tra pagine](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal) (Esempio di pagina modale)
- [Passing Data (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata) (Esempio di passaggio dei dati)
