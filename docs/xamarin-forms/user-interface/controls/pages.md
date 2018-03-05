---
title: Xamarin.Forms Pages
description: Pagine di xamarin. Forms rappresentano le schermate di app per dispositivi mobili multipiattaforma.
ms.topic: article
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 35822dbbb7d5694e7f1f0a3f35f10df404206af9
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-pages"></a>Xamarin.Forms Pages

_Pagine di xamarin. Forms rappresentano le schermate di app per dispositivi mobili multipiattaforma._

<style>.tableimg {larghezza massima: nessuna! importanti;}</style>

## <a name="pages"></a>Pages

Il [ `Page` ](http://iosapi.xamarin.com/?link=T%3aXamarin.Forms.Page) classe è un elemento visivo che occupa la maggior parte o tutti dello schermo e contiene un singolo elemento figlio. Oggetto `Xamarin.Forms.Page` rappresenta una pagina in Windows Phone o un Controller di visualizzazione in iOS. In Android ogni pagina occupa la schermata come un'attività, ma sono pagine di xamarin. Forms *non* le attività.

 [ ![](pages-images/pages-sml.png "Tipi di pagina di xamarin. Forms")](pages-images/pages.png "tipi di pagina di xamarin. Forms")

<br clear="all" />

Xamarin. Forms supporta:

<table align="center" border="1" cellpadding="1" cellspacing="1">
  <tr>
  <thead>
    <th>
      <strong>tipo</strong>
    </th>
    <th>
      <strong>Descrizione</strong>
    </th>
    <th style="min-width:400px">
      <strong>schermata di</strong>
    </th>
  </thead></tr>
  <tbody>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a>
    </td>
    <td align="center" valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a> consente di visualizzare un singolo <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a>, spesso un contenitore, ad esempio un <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a> o <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ContentPageDemoPage.cs"><img src="pages-images/ContentPage.png" title="Esempio ContentPage" class="tableimg">
    </a></td>
  </tr><tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/">MasterDetailPage</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">pagina</a> che gestisce due riquadri di informazioni.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/MasterDetailPageDemoPage.cs"><img src="pages-images/MasterDetailPage.png" title="Esempio MasterDetailPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/">NavigationPage</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">pagina</a> che gestisce la navigazione e l'esperienza di uno stack di altre pagine.  
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/NavigationPageDemoPage.cs"><img src="pages-images/NavigationPage.png" title="Esempio NavigationPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/">TabbedPage</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">pagina</a> le pagine mediante le schede che consente lo spostamento tra gli elementi figlio.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TabbedPageDemoPage.cs"><img src="pages-images/TabbedPage.png" title="Esempio TabbedPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/">TemplatedPage</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">pagina</a> che visualizza il contenuto a schermo intero con un modello di controllo e la classe base per <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a>.
    </td>
    <td valign="top">
    <a href="https://github.com/xamarin/xamarin-forms-samples/tree/master/Templates/ControlTemplates/"><img src="pages-images/TemplatedPage.png" title="Esempio TemplatedPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/">CarouselPage</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">pagina</a> possibilità di scorrere tra le pagine secondarie, ad esempio una raccolta.
    </td>
    <td valign="top">
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CarouselPageDemoPage.cs"><img src="pages-images/CarouselPage.png" title="Esempio CarouselPage" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Collegamenti correlati

- [Introduction to Xamarin.Forms (Introduzione a Xamarin.Forms)](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Raccolta di xamarin. Forms (esempio)](https://developer.xamarin.com/samples/FormsGallery/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Documentazione di xamarin. Forms API](http://iosapi.xamarin.com/?link=N%3aXamarin.Forms)
