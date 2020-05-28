---
title: Xamarin.FormsPagine
description: Xamarin.FormsLe pagine rappresentano le schermate delle applicazioni per dispositivi mobili multipiattaforma. Questo articolo elenca le pagine incluse in Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c576186dcfd598cb4fcfecd6d36edf04f73eee64
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132821"
---
# <a name="xamarinforms-pages"></a>Xamarin.FormsPagine

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Le pagine Novell. Forms rappresentano le schermate delle applicazioni per dispositivi mobili multipiattaforma._

Tutti i tipi di pagina descritti di seguito derivano dalla Xamarin.Forms [`Page`](xref:Xamarin.Forms.Page) classe. Questi elementi visivi occupano tutti o la maggior parte dello schermo. Un `Page` oggetto rappresenta un `ViewController` in iOS e un oggetto `Page` nel piattaforma UWP (Universal Windows Platform). In Android ogni pagina occupa lo schermo come `Activity` , ma le Xamarin.Forms pagine *non* sono `Activity` oggetti.

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Pagine

Xamarin.Formssupporta i tipi di pagina seguenti:

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage)è il tipo di pagina più semplice e più comune. Impostare la [`Content`](xref:Xamarin.Forms.ContentPage.Content) proprietà su un singolo [`View`](views.md) oggetto, che è più spesso un, [`Layout`](layouts.md) ad esempio [`StackLayout`](layouts.md#stackLayout) , [`Grid`](layouts.md#grid) o [`ScrollView`](layouts.md#scrollView) .<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ContentPage) | [![Esempio di ContentPage](pages-images/ContentPage.png "Esempio di ContentPage")](pages-images/ContentPage-Large.png#lightbox "Esempio di ContentPage")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Un oggetto [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gestisce due riquadri di informazioni. Impostare la [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) proprietà su una pagina in genere visualizzando un elenco o un menu. Impostare la [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) proprietà su una pagina che mostra un elemento selezionato dalla pagina master. La [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) proprietà determina se la pagina master o di dettaglio è visibile.<br /><br />[Documentazione API](xref:Xamarin.Forms.MasterDetailPage)  /  [Guida](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) di | [![Esempio di MasterDetailPage](pages-images/MasterDetailPage.png "Esempio di MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Esempio di MasterDetailPage")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)Gestisce la navigazione tra le altre pagine usando un'architettura basata su stack. Quando si usa l'esplorazione delle pagine nell'applicazione, un'istanza del home page deve essere passata al costruttore di un `NavigationPage` oggetto.<br /><br />[Documentazione API](xref:Xamarin.Forms.NavigationPage)  /  [Guida](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  /  di [Esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)e [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Esempio di NavigationPage](pages-images/NavigationPage.png "Esempio di NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Esempio di NavigationPage")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) con [code = behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)deriva dalla [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) classe astratta e consente la navigazione tra le pagine figlio utilizzando le schede. Impostare la [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) proprietà su una raccolta di pagine oppure impostare la [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) proprietà su una raccolta di oggetti dati e la [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) proprietà su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che descrive il modo in cui ogni oggetto deve essere rappresentato visivamente.<br /><br />[Documentazione API](xref:Xamarin.Forms.TabbedPage)  /  [Guida](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  /  di [Esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Esempio di TabbedPage](pages-images/TabbedPage.png "Esempio di TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Esempio di TabbedPage")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)deriva dalla [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) classe astratta e consente lo spostamento tra le pagine figlio attraverso la sfioramento del dito. Impostare la [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) proprietà su una raccolta di [`ContentPage`](#contentPage) oggetti oppure impostare la [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) proprietà su una raccolta di oggetti dati e la [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) proprietà su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che descrive il modo in cui ogni oggetto deve essere rappresentato visivamente.<br /><br />[Documentazione API](xref:Xamarin.Forms.CarouselPage)  /  [Guida](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  /  di [Esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Esempio di CarouselPage](pages-images/CarouselPage.png "Esempio di CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Esempio di CarouselPage")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)Visualizza il contenuto a schermo intero con un modello di controllo ed è la classe di base per [`ContentPage`](#contentPage) .<br /><br />[Documentazione API](xref:Xamarin.Forms.TemplatedPage)  /  [Guida](~/xamarin-forms/app-fundamentals/templates/control-template.md) di | [![Esempio di TemplatedPage](pages-images/TemplatedPage.png "Esempio di TemplatedPage")](pages-images/TemplatedPage.png "Esempio di TemplatedPage") |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.FormsEsempio FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsCampioni](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentazione API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
