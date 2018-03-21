---
title: Xamarin.Forms Pages
description: Pagine di xamarin. Forms rappresentano le schermate di applicazioni per dispositivi mobili multipiattaforma.
ms.topic: article
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 5f979d2dbb894107d8d606ec1f41de44c294cdd3
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2018
---
# <a name="xamarinforms-pages"></a>Xamarin.Forms Pages

_Pagine di xamarin. Forms rappresentano le schermate di applicazioni per dispositivi mobili multipiattaforma._

Tutti i tipi di pagina sono descritte di seguito derivano da di xamarin. Forms [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) classe. Questi elementi visivi che occupano tutte o la maggior parte della schermata. Oggetto `Page` oggetto rappresenta un `ViewController` in iOS e un `Page` nella piattaforma Windows universale. In Android, ogni pagina occupa la schermata come una `Activity`, ma sono pagine di xamarin. Forms *non* `Activity` oggetti.

[ ![](pages-images/pages-sml.png "Tipi di pagina di xamarin. Forms")](pages-images/pages.png#lightbox "tipi di pagina di xamarin. Forms")

## <a name="pages"></a>Pages

Xamarin. Forms supporta i seguenti tipi di pagina:

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     | 
| --- | --- | 
| [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) è il tipo più semplice e più comune della pagina. Impostare il [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentPage.Content/) proprietà a un singolo [ `View` ](views.md) oggetto, che è spesso un [ `Layout` ](layouts.md) , ad esempio [ `StackLayout` ](layouts.md#stackLayout), [ `Grid` ](layouts.md#grid), o [ `ScrollView` ](layouts.md#scrollView).<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) | [![Esempio ContentPage](pages-images/ContentPage.png "esempio ContentPage")](pages-images/ContentPage-Large.png#lightbox "ContentPage esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     | 
| --- | --- | 
| Oggetto [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) gestisce due riquadri di informazioni. Impostare il [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) proprietà a una pagina in genere che mostra un elenco o un menu. Impostare il [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) proprietà da una pagina in cui un elemento selezionato dalla pagina master. Il [ `IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/) proprietà determina se la pagina master o di dettaglio è visibile.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) / [Guida](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/) | [![Esempio MasterDetailPage](pages-images/MasterDetailPage.png "MasterDetailPage esempio")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) con [codice](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     | 
| --- | --- | 
| Il [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) gestisce lo spostamento tra le altre pagine utilizzando un'architettura basata su stack. Quando si utilizza la navigazione all'interno dell'applicazione, un'istanza della home page deve essere passata al costruttore di un `NavigationPage` oggetto.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) / [Guida](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [1 di esempio](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/), [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/), e [3](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)  | [![Esempio NavigationPage](pages-images/NavigationPage.png "NavigationPage esempio")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) con [codice = dietro](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     | 
| --- | --- | 
| [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) deriva dalla classe astratta [ `MultiPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/) classe e consente la navigazione tra figlio pagine con schede. Impostare il [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/) proprietà a una raccolta di pagine o set di [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/) proprietà a una raccolta di oggetti dati e il [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/) proprietà da un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) che descrive la modalità con cui ogni oggetto è per essere rappresentato visivamente.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) / [Guida](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [1 di esempio](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/) e [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage) | [![Esempio TabbedPage](pages-images/TabbedPage.png "TabbedPage esempio")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     | 
| --- | --- | 
| [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) deriva dalla classe astratta [ `MultiPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/) classe e consente la navigazione tra figlio pagine attraverso il passaggio di un dito. Impostare il [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/) proprietà a una raccolta di [ `ContentPage` ](#contentPage) oggetti o set di [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/) proprietà a una raccolta di oggetti dati e il [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/) proprietà per un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) che descrive la modalità con cui ogni oggetto è per essere rappresentato visivamente.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) / [Guida](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [1 di esempio](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/) e [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/) | [![Esempio CarouselPage](pages-images/CarouselPage.png "CarouselPage esempio")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     | 
| --- | --- | 
| [`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/) Visualizza il contenuto a schermo intero con un modello di controllo ed è la classe base per [ `ContentPage` ](#contentPage).<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/) / [Guida](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Esempio TemplatedPage](pages-images/TemplatedPage.png "TemplatedPage esempio")](pages-images/TemplatedPage.png "TemplatedPage esempio") |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Introduction to Xamarin.Forms (Introduzione a Xamarin.Forms)](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Esempio FormsGallery xamarin. Forms](https://developer.xamarin.com/samples/FormsGallery/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentazione di xamarin. Forms API](https://developer.xamarin.com/api/root/Xamarin.Forms/)
