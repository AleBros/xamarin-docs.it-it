---
title: Xamarin.Forms Layouts
description: Layout di xamarin. Forms vengono utilizzati per creare i controlli dell'interfaccia utente in strutture logiche.
ms.topic: article
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: ecea0f55360fcde7a50c52bb33c45a2c5fff5eeb
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-layouts"></a>Xamarin.Forms Layouts

_Layout di xamarin. Forms vengono utilizzati per creare i controlli dell'interfaccia utente in strutture logiche._

<style>.tableimg {larghezza massima: nessuna! importanti;}</style>

## <a name="layouts"></a>Layout

Il [`Layout`](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout) classe in xamarin. Forms è un sottotipo di visualizzazione, che funge da contenitore per altri layout o visualizzazioni specializzato. In genere contiene la logica necessaria per impostare la posizione e le dimensioni degli elementi figlio nelle applicazioni di xamarin. Forms.

 [ ![](layouts-images/layouts-sml.png "Tipi di Layout di xamarin. Forms")](layouts-images/layouts.png "tipi di Layout di xamarin. Forms")

<br clear="all" />

Xamarin. Forms supporta:

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
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/">ContentPresenter</a>
    </td>
    <td valign="top">
Un gestore di layout per le viste basate su modelli. Utilizzato all'interno di un <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/">ControlTemplate</a> per contrassegnare in cui viene visualizzato il contenuto da presentare.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/Templates/ControlTemplates/SimpleTheme/SimpleTheme/App.xaml"><img src="layouts-images/ContentPresenter.png" title="Esempio di ContentPresenter" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/">ContentView</a>
    </td>
    <td valign="top">
Un elemento con un singolo contenuto. ContentView è molto grande utilità propri. Il suo scopo è fungere da classe base per le visualizzazioni composte definito dall'utente.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ContentViewDemoPage.cs"><img src="layouts-images/ContentView.png" title="Esempio ContentView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/">Frame</a>
    </td>
    <td valign="top">
Un elemento contenente un singolo elemento figlio, con alcune opzioni di framing. Frame prevedono un valore predefinito <a href="https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/">Xamarin.Forms.Layout.Padding</a> di 20.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/FrameDemoPage.cs"><img src="layouts-images/Frame.png" title="Esempio di frame" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a>
    </td>
    <td valign="top">
Richiede un elemento in grado di scorrimento in caso di contenuto.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ScrollViewDemoPage.cs"><img src="layouts-images/ScrollView.png" title="Esempio ScrollView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/">TemplatedView</a>
    </td>
    <td valign="top">
Un elemento che consente di visualizzare il contenuto con un modello di controllo e la classe base per <a href=""/api/type/Xamarin.Forms.ContentView/">ContentView</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/tree/master/Templates/ControlTemplates/"><img src="layouts-images/TemplatedView.png" title="Esempio TemplatedView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/">AbsoluteLayout</a>
    </td>
    <td valign="top">
Posiziona gli elementi figlio in corrispondenza delle posizioni richieste assolute. Utente assegnato Anchor e i limiti definisce la posizione e le dimensioni del controllo.
    </td>
    <td valign="top">
      <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/AbsoluteLayoutDemoPage.cs"><img src="layouts-images/AbsoluteLayout.png" title="Esempio AbsoluteLayout" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/">Grid</a>
    </td>
    <td valign="top">
Un layout contenente viste disposte in righe e colonne.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/GridDemoPage.cs"><img src="layouts-images/Grid.png" title="Esempio di Grid" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td>
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/">RelativeLayout</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/%601">Layout</a> che utilizza <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/">vincolo</a>s al layout nei relativi elementi figlio.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/RelativeLayoutDemoPage.cs"><img src="layouts-images/RelativeLayout.png" title="Esempio RelativeLayout" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/">Layout</a> che posiziona gli elementi figlio in una singola riga che può essere orientata orizzontalmente o verticalmente. Questo layout imposterà il figlio limiti automaticamente durante il ciclo di layout. Limiti assegnati all'utente verrà sovrascritto e pertanto non deve essere impostato su un elemento figlio dall'utente.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/StackLayoutDemoPage.cs"><img src="layouts-images/StackLayout.png" title="Esempio StackLayout" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Collegamenti correlati

- [Introduction to Xamarin.Forms (Introduzione a Xamarin.Forms)](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Raccolta di xamarin. Forms (esempio)](https://developer.xamarin.com/samples/FormsGallery/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Documentazione di xamarin. Forms API](https://developer.xamarin.com/api/namespace/Xamarin.Forms)
