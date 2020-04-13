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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760830"
---
# <a name="xamarinforms-modal-pages"></a>Pagine modali Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_Xamarin.Forms fornisce il supporto per le pagine modali. Una pagina modale incoraggia gli utenti a completare un'attività indipendente da cui non è possibile spostarsi fino a quando l'attività non viene completata o annullata. In questo articolo viene illustrato come passare alle pagine modali._

Questo articolo tratta gli argomenti seguenti:

- [Eseguire spostamenti](#Performing_Navigation) - Push delle pagine nello stack modale, prelievo delle pagine dallo stack modale, disabilitazione del pulsante Indietro e animazione delle transizioni di pagina.
- [Passaggio di dati durante gli spostamenti](#Passing_Data_when_Navigating) - Passaggio di dati tramite un costruttore di pagina e un `BindingContext`.

## <a name="overview"></a>Panoramica

Una pagina modale può essere di uno qualsiasi dei tipi [Page](~/xamarin-forms/user-interface/controls/pages.md) supportati da Xamarin.Forms. Per visualizzare una pagina modale, l'applicazione ne eseguirà il push nello stack modale, in cui diventerà la pagina attiva, come illustrato nel diagramma seguente:

![](modal-images/pushing.png "Pushing a Page to the Modal Stack")

Per tornare alla pagina precedente, l'applicazione preleverà la pagina corrente dallo stack modale e la nuova pagina in primo piano diventa la pagina attiva, come illustrato nel diagramma seguente:

![](modal-images/popping.png "Popping a Page from the Modal Stack")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Esecuzione degli spostamenti

I metodi di navigazione [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) modali [`Page`](xref:Xamarin.Forms.Page) vengono esposti dalla proprietà in qualsiasi tipo derivato. Questi metodi offrono la possibilità di eseguire il [push delle pagine modali](#Pushing_Pages_to_the_Modal_Stack) nello stack modale e di [prelevare le pagine modali](#Popping_Pages_from_the_Modal_Stack) dallo stack modale.

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà espone [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) anche una proprietà da cui è possibile ottenere le pagine modali nello stack modale. Tuttavia non esiste nessun concetto di gestione modale dello stack o di prelievo e attivazione della pagina radice nella navigazione modale. Queste operazioni non sono supportate in tutti i casi sulle piattaforme sottostanti.

> [!NOTE]
> Un'istanza [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) non è necessaria per l'esecuzione dello spostamento tra le pagine modale.

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>Push delle pagine nello stack modale

Per passare `ModalPage` all'oggetto è [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) necessario richiamare il metodo sulla [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà della pagina corrente, come illustrato nell'esempio di codice seguente:

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

In questo `ModalPage` modo l'istanza da inserire nello stack modale, dove diventa la pagina [`ListView`](xref:Xamarin.Forms.ListView) attiva, a condizione che sia stato selezionato un elemento nella nell'istanza. `MainPage` L'istanza di `ModalPage` è illustrata negli screenshot seguenti:

![](modal-images/modalpage.png "Modal Page Example")

Quando [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) viene richiamato, si verificano i seguenti eventi:

- La chiamata `PushModalAsync` di [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) pagina ha il proprio override richiamato, purché la piattaforma sottostante non è Android.The page calling has its override invoked, provided that the underlying platform isn't Android.
- Il relativo [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) override della pagina a cui ci si sposta viene richiamato.
- L'attività `PushAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per ulteriori informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro Xamarin.Forms di Charles Petzold.

> [!NOTE]
> Le chiamate [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) alle sostituzioni e non possono essere considerate come indicazioni garantite di navigazione tra le pagine. Ad esempio, in iOS l'override di `OnDisappearing` viene chiamato per la pagina attiva quando l'applicazione viene terminata.

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>Prelievo delle pagine dallo stack modale

La pagina attiva può essere prelevata dallo stack modale premendo il pulsante *Indietro* del dispositivo, indipendentemente dal fatto che sia un pulsante fisico del dispositivo o un pulsante su schermo.

Per tornare a livello di `ModalPage` codice alla [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) pagina originale, l'istanza deve richiamare il metodo, come illustrato nell'esempio di codice seguente:To programmatically return to the original page, the instance must invoke the method, as demonstrated in the following code example:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Di conseguenza, l'istanza di `ModalPage` viene rimossa dallo stack modale e la nuova pagina in primo piano diventa la pagina attiva. Quando [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) viene richiamato, si verificano i seguenti eventi:

- Il relativo `PopModalAsync` [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) override richiamato nella chiamata di pagina viene richiamato.
- La pagina restituita a [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) ha il suo override richiamato, purché la piattaforma sottostante non è Android.The page being returned to has its override invoked, provided that the underlying platform isn't Android.
- L'attività `PopModalAsync` viene completata.

Tuttavia, l'ordine preciso in cui si verificano questi eventi è dipendente dalla piattaforma. Per ulteriori informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro Xamarin.Forms di Charles Petzold.

### <a name="disabling-the-back-button"></a>Disabilitazione del pulsante Indietro

In Android, l'utente può sempre tornare alla pagina precedente premendo il pulsante *Indietro* standard sul dispositivo. Se la pagina modale richiede all'utente di completare un'attività indipendente prima di lasciare la pagina, l'applicazione deve disabilitare il pulsante *Indietro*. Questa operazione può essere eseguita eseguendo l'override del [`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) metodo nella pagina modale. Per altre informazioni, vedere il [capitolo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del manuale di Xamarin.Forms redatto da Charles Petzold.

### <a name="animating-page-transitions"></a>Animazione delle transizioni di pagina

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà di ogni pagina fornisce anche metodi `boolean` push e pop sottoposti a override che includono un parametro che controlla se visualizzare un'animazione di pagina durante la navigazione, come illustrato nell'esempio di codice seguente:The property of each page also provides overridden push and pop methods that include a parameter that controls whether to display a page animation during navigation, as shown in the following code example:

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

In alcuni casi, una pagina deve passare dati a un'altra pagina durante gli spostamenti. Due tecniche per eseguire questa operazione sono passando i dati attraverso un [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) costruttore di pagina e impostando la nuova pagina per i dati. Di seguito verranno descritti entrambi.

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

I dati vengono quindi visualizzati nella [`Label.Text`](xref:Xamarin.Forms.Label.Text) pagina impostando la proprietà .

### <a name="passing-data-through-a-bindingcontext"></a>Passaggio dei dati tramite BindingContext

Un approccio alternativo per il passaggio di dati a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) un'altra pagina durante la navigazione consiste nell'impostare la nuova pagina sui dati, come illustrato nell'esempio di codice seguente:An alternative approach for passing data to another page during navigation is by setting the new page's to the data, as shown in the following code example:

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

Questo codice [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) imposta `DetailPage` l'istanza `Contact` sull'istanza, quindi `DetailPage`passa all'oggetto .

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

I dati vengono quindi visualizzati nella [`Label`](xref:Xamarin.Forms.Label) pagina da una serie di controlli.

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Nozioni di base sul data binding).

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come spostarsi tra pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.

## <a name="related-links"></a>Collegamenti correlati

- [Navigazione tra le pagine](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal) (Esempio di pagina modale)
- [Passing Data (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata) (Esempio di passaggio dei dati)
