---
title: Xamarin.Forms Cells
description: "È possibile aggiungere controlli ListView e TableViews celle xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 509ecc509754bba544115c140e619f634bd64eae
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms Cells

_È possibile aggiungere controlli ListView e TableViews celle xamarin. Forms._

<style>.tableimg {larghezza massima: nessuna! importanti;}</style>

## <a name="cells"></a>Celle

Una cella è un elemento specifico utilizzato per gli elementi in una tabella e viene descritto come ogni elemento in un elenco deve essere disegnato. Cella deriva da [ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/), da cui anche deriva VisualElement. Tuttavia la cella non è un elemento visivo, vengono descritte solo un modello per la creazione di un elemento visivo. [`Cell`](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) fornisce funzionalità e classe di base per tutte le celle di xamarin. Forms. Le celle sono progettati per essere aggiunti a elementi [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) o [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) controlli.

Per informazioni su come usare e personalizzare celle, consultare il [ListView](~/xamarin-forms/user-interface/listview/index.md) e [TableView](~/xamarin-forms/user-interface/tableview.md) documentazione.

<table align="center" border="1" cellpadding="1" cellspacing="1">
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
  </thead>
  <tbody>
    <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/">EntryCell</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a> con un'etichetta e un campo di immissione di testo singola riga.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EntryDemoPage.cs"><img src="cells-images/EntryCell.png" title="Esempio EntryCell" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/">SwitchCell</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a> con un'etichetta e un'opzione di attivazione/disattivazione.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SwitchCellDemoPage.cs"><img src="cells-images/SwitchCell.png" title="Esempio SwitchCell" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">TextCell</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a> con il testo primario e secondario.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TextCellDemoPage.cs"><img src="cells-images/TextCell.png" title="Esempio TextCell" class="tableimg">
    </a></td>
  </tr>
      <tr>
    <td>
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/">ImageCell</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">testo cella</a> che include anche un'immagine.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ImageCellDemoPage.cs"><img src="cells-images/ImageCell.png" title="Esempio ImageCell" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Collegamenti correlati

- [Introduction to Xamarin.Forms (Introduzione a Xamarin.Forms)](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Raccolta di xamarin. Forms (esempio)](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentazione di xamarin. Forms API](https://developer.xamarin.com/api/namespace/Xamarin.Forms/)
