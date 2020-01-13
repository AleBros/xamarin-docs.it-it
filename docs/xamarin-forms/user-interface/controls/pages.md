---
title: Pagine di Xamarin.Forms
description: Le pagine Xamarin.Forms rappresentano le schermate delle applicazioni per dispositivi mobili multipiattaforma. Questo articolo elenca le pagine incluse in Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 278256f75d94fe47510ae4d15f12a3ff3a6a2b19
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "69976452"
---
# <a name="xamarinforms-pages"></a>Pagine di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Le pagine Xamarin.Forms rappresentano le schermate delle applicazioni per dispositivi mobili multipiattaforma._

Tutti i tipi di pagina descritti di seguito derivano dalla classe Xamarin.Forms [`Page`](xref:Xamarin.Forms.Page) . Questi elementi visivi occupano tutti o la maggior parte dello schermo. Un oggetto `Page` rappresenta una `ViewController` in iOS e un `Page` nel piattaforma UWP (Universal Windows Platform). In Android ogni pagina occupa lo schermo come un `Activity`, ma le pagine Xamarin.Forms *non* sono `Activity` oggetti.

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Pages

Xamarin.Forms supporta i tipi di pagina seguenti:

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage) è il tipo di pagina più semplice e più comune. Impostare la proprietà [`Content`](xref:Xamarin.Forms.ContentPage.Content) su un singolo oggetto [`View`](views.md) , che è spesso un [`Layout`](layouts.md) , ad esempio [`StackLayout`](layouts.md#stackLayout), [`Grid`](layouts.md#grid)o [1](layouts.md#scrollView).<br /><br />[Documentazione delle API](xref:Xamarin.Forms.ContentPage) | [![Esempio di ContentPage](pages-images/ContentPage.png "Esempio di ContentPage")](pages-images/ContentPage-Large.png#lightbox "Esempio di ContentPage")<br />codice per questa pagina  / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gestisce due riquadri di informazioni. Impostare la proprietà [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) su una pagina in genere visualizzando un elenco o un menu. Imposta la proprietà [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) su una pagina che mostra un elemento selezionato dalla pagina master. La proprietà [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) determina se la pagina master o di dettaglio è visibile.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.MasterDetailPage)  / [Guida](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![Esempio di MasterDetailPage](pages-images/MasterDetailPage.png "Esempio di MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Esempio di MasterDetailPage")<br />codice per questa pagina  / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| Il [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) gestisce la navigazione tra le altre pagine usando un'architettura basata su stack. Quando si usa l'esplorazione delle pagine nell'applicazione, un'istanza del home page deve essere passata al costruttore di un oggetto `NavigationPage`.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.NavigationPage)  / [Guida](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  / [esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)e [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Esempio di NavigationPage](pages-images/NavigationPage.png "Esempio di NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Esempio di NavigationPage")<br />codice per questa pagina  / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) con [code = behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) deriva dalla classe abstract [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) e consente la navigazione tra le pagine figlio utilizzando le schede. Impostare la proprietà [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) su una raccolta di pagine oppure impostare la proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) su una raccolta di oggetti dati e la proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che descrive il modo in cui ogni oggetto deve essere rappresentato visivamente.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.TabbedPage)  / [Guida](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  / [esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Esempio di TabbedPage](pages-images/TabbedPage.png "Esempio di TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Esempio di TabbedPage")<br />codice per questa pagina  / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) deriva dalla classe astratta [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) e consente di spostarsi tra le pagine figlio attraverso la sfioramento del dito. Impostare la proprietà [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) su una raccolta di oggetti [`ContentPage`](#contentPage) oppure impostare la proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) su una raccolta di oggetti dati e la proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che descrive la modalità di visualizzazione di ciascun oggetto rappresentato.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.CarouselPage)  / [Guida](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  / [esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Esempio di CarouselPage](pages-images/CarouselPage.png "Esempio di CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Esempio di CarouselPage")<br />codice per questa pagina  / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) Visualizza il contenuto a schermo intero con un modello di controllo ed è la classe di base per [`ContentPage`](#contentPage).<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.TemplatedPage)  / [Guida](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Esempio di TemplatedPage](pages-images/TemplatedPage.png "Esempio di TemplatedPage")](pages-images/TemplatedPage.png "Esempio di TemplatedPage") |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentazione per le API di Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
