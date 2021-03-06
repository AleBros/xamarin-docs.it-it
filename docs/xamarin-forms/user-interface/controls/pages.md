---
title: Pagine di xamarin. Forms
description: Le pagine di xamarin. Forms rappresentano le schermate dell'applicazione per dispositivi mobili multipiattaforma. Questo articolo elenca le pagine incluse in xamarin. Forms.
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 101b7aa014064e07de69e8c6b12f0ad1000ae0dd
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75955743"
---
# <a name="xamarinforms-pages"></a>Pagine di xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Le pagine di xamarin. Forms rappresentano le schermate dell'applicazione per dispositivi mobili multipiattaforma._

Tutti i tipi di pagina che sono descritte di seguito derivano da xamarin. Forms [ `Page` ](xref:Xamarin.Forms.Page) classe. Questi elementi visivi che occupano tutti o la maggior parte della schermata. Oggetto `Page` oggetto rappresenta un `ViewController` in iOS e un `Page` nella piattaforma Windows universale. In Android, ogni pagina occupa la schermata, ad esempio un `Activity`, tuttavia alle pagine di xamarin. Forms *non* `Activity` oggetti.

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Pages

Xamarin. Forms supporta i seguenti tipi di pagina:

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage) è il tipo più semplice e più comune della pagina. Impostare il [ `Content` ](xref:Xamarin.Forms.ContentPage.Content) proprietà a un singolo [ `View` ](views.md) oggetto, ovvero in genere un [ `Layout` ](layouts.md) come [ `StackLayout` ](layouts.md#stackLayout), [ `Grid` ](layouts.md#grid), o [ `ScrollView` ](layouts.md#scrollView).<br /><br />[Documentazione delle API](xref:Xamarin.Forms.ContentPage) | [![Esempio di ContentPage](pages-images/ContentPage.png "Esempio di ContentPage")](pages-images/ContentPage-Large.png#lightbox "Esempio di ContentPage")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Oggetto [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) gestisce due riquadri di informazioni. Impostare il [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) proprietà a una pagina a livello generale che mostra un elenco o un menu. Impostare il [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) proprietà a una pagina che mostra un elemento selezionato dalla pagina master. Il [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) proprietà determina se la pagina master o di dettaglio è visibile.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.MasterDetailPage) / [Guida](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![Esempio di MasterDetailPage](pages-images/MasterDetailPage.png "Esempio di MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Esempio di MasterDetailPage")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| Il [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) gestisce lo spostamento tra altre pagine che usano un'architettura basata su stack. Quando si usa la navigazione all'interno dell'applicazione, un'istanza della home page deve essere passata al costruttore di una `NavigationPage` oggetto.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.NavigationPage) / [Guida](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [campionare 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata), e [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Esempio di NavigationPage](pages-images/NavigationPage.png "Esempio di NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Esempio di NavigationPage")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) con [codice =-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) deriva dalla classe astratta [ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1) classe e consente la navigazione tra figlio pagine usando le schede. Impostare il [ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children) proprietà a un insieme di pagine o set il [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) proprietà a una raccolta di oggetti di data e il [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) proprietà a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) che descrive come ogni oggetto deve essere rappresentato visivamente.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.TabbedPage) / [Guida](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [campionare 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Esempio di TabbedPage](pages-images/TabbedPage.png "Esempio di TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Esempio di TabbedPage")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) deriva dalla classe astratta [ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1) classe e consente la navigazione tra figlio pagine tramite scorrimento rapido con un dito. Impostare il [ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children) proprietà a una raccolta di [ `ContentPage` ](#contentPage) oggetti o set il [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) proprietà a una raccolta di oggetti dati e il [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) proprietà a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) che descrive come ogni oggetto deve essere rappresentato visivamente.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.CarouselPage) / [Guida](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [campionare 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Esempio di CarouselPage](pages-images/CarouselPage.png "Esempio di CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Esempio di CarouselPage")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) Visualizza il contenuto a schermo intero con un modello di controllo ed è la classe base per [ `ContentPage` ](#contentPage).<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.TemplatedPage) / [Guida](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Esempio di TemplatedPage](pages-images/TemplatedPage.png "Esempio di TemplatedPage")](pages-images/TemplatedPage.png "Esempio di TemplatedPage") |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di xamarin. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentazione per le API di Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
