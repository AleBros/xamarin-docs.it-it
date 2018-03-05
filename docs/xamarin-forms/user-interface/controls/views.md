---
title: Viste di xamarin. Forms
description: Viste di xamarin. Forms sono i blocchi predefiniti di interfacce utente per dispositivi mobili multipiattaforma.
ms.topic: article
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: df8c8463b2556035c5369c70cb10dbc3dc6b6743
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-views"></a>Viste di xamarin. Forms

_Viste di xamarin. Forms sono i blocchi predefiniti di interfacce utente per dispositivi mobili multipiattaforma._

<style>.tableimg {larghezza massima: nessuna! importanti;}</style>

## <a name="views"></a>Visualizzazioni

Xamarin. Forms Usa la parola *vista* per fare riferimento agli oggetti visivi, ad esempio pulsanti, etichette o caselle di immissione testo - che possono essere più comunemente noto come controlli del widget.

Questi elementi dell'interfaccia utente vengono in genere sono sottoclassi di [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/).

<br clear="right" />

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
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/">ActivityIndicator</a>
    </td>
    <td valign="top">
Un controllo visual utilizzato per indicare che un elemento è in corso. Questo controllo fornisce un indizio visivo all'utente che si è in corso, senza informazioni di stato di avanzamento.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ActivityIndicatorDemoPage.cs"><img src="views-images/ActivityIndicator.png" title="Esempio ActivityIndicator" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/">BoxView</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> utilizzato per disegnare un rettangolo di colore a tinta unita. Quando si esegue la creazione di prototipi iniziale, BoxView è un sostituto utile per le immagini o gli elementi personalizzati. BoxView dispone di una richiesta di dimensione predefinito di 40 x 40. Se è necessario dimensioni diverse, assegnare il <a href="https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/">VisualElement.WidthRequest</a> e <a href="https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/">VisualElement.HeightRequest</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/BoxViewDemoPage.cs"><img src="views-images/BoxView.png" title="Esempio BoxView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Button/">Button</a>
    </td>
    <td align="center" valign="top">
Un pulsante <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> che reagisce agli eventi di tocco.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ButtonDemoPage.cs"><img src="views-images/Button.png" title="Esempio di pulsante" class="tableimg">
    </a></td>
  </tr>
  <tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/">DatePicker</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> che consente la selezione di date. La rappresentazione visiva di un DatePicker è molto simile a quello di <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">voce</a>, ad eccezione del fatto che un controllo speciale per il prelievo di una data viene visualizzata al posto di una tastiera </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/DatePickerDemoPage.cs"><img src="views-images/DatePicker.png" title="Esempio DatePicker" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/">Editor</a>
    </td>
    <td valign="top">
Un controllo che consente di modificare più righe di testo. Per le voci di riga singola, vedere <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">voce</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EditorDemoPage.cs"><img src="views-images/Editor.png" title="Esempio di editor" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">Voce</a>
    </td>
    <td valign="top">
Un controllo che consente di modificare una singola riga di testo. La voce è una voce singola riga di testo. Si è più adatta per la raccolta di piccole dimensioni parti discrete di informazioni, ad esempio nomi utente e password.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EntryDemoPage.cs"><img src="views-images/Entry.png" title="Esempio di voce" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Image/">Immagine</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> che contiene un'immagine.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Image/">Immagine di API</a>
    <br />
    <a href="~/xamarin-forms/user-interface/images.md">Utilizzo di immagini</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ImageDemoPage.cs">Origine demo</a>
    </td>
    <td>
    <img src="views-images/Image.png" title="Esempio di immagine" class="tableimg">
    </td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Label/">Etichetta</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> che consente di visualizzare il testo in un formato di solo letto. Un'etichetta viene utilizzata per visualizzare gli elementi di testo a riga singola e i blocchi a più righe di testo.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/LabelDemoPage.cs"><img src="views-images/Label.png" title="Esempio di etichetta" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/">ListView</a>
    </td>
    <td valign="top">
Un <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/">l'oggettoVedi il</a> che visualizza una raccolta di dati come un elenco verticale.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/">API di ListView</a>
    <br />
    <a href="~/xamarin-forms/user-interface/listview/index.md">Documentazione di ListView</a>
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ListViewDemoPage.cs"><img src="views-images/ListView.png" title="Esempio di ListView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/">OpenGLView</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> che visualizza il contenuto OpenGL.
    <ul>
      <li>Funziona solo per progetti iOS e Android (Nessun supporto per Windows Phone).
      <li>Richiede un riferimento al <b>OpenTK 1.0</b> assembly in cui i progetti iOS e Android.</li>
      <li>Più adatto per l'utilizzo nei progetti condivisi. Se utilizzata in una PCL un DependencyService sarà anche necessario.</li>
    </ul>
    </td>
    <td>
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/"><img src="views-images/OpenGL.png" title="Esempio OpenGlView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/">Selezione</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> controllo per il prelievo di un elemento in un elenco. La rappresentazione visiva di un controllo selezione è simile a un <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">voce</a>, ma viene visualizzato un controllo selezione al posto di una tastiera.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/PickerDemoPage.cs"><img src="views-images/Picker.png" title="Esempio di selezione" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/">ProgressBar</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> controllo che indica uno stato di avanzamento.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ProgressBarDemoPage.cs"><img src="views-images/ProgressBar.png" title="Classe di esempio ProgressBar ="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/">SearchBar</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> controllo che fornisce una casella di ricerca.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SearchBarDemoPage.cs"><img src="views-images/SearchBar.png" title="Esempio SearchBar" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/">Slider</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> controllo che inserisce un valore lineare.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SliderDemoPage.cs"><img src="views-images/Slider.png" title="Esempio di dispositivo di scorrimento" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/">Gestore di istruzioni</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> controllo che inserisce un valore discreto, vincolato a un intervallo.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/StepperDemoPage.cs"><img src="views-images/Stepper.png" title="Esempio di gestore di istruzioni" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/">commutatore</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> controllo che fornisce un valore di stato attivato o disattivato.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SwitchDemoPage.cs"><img src="views-images/Switch.png" title="Esempio di switch" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/">TableView</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> che contiene righe di <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">cella</a>s.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/">TableView API</a>
    <br />
    <a href="~/xamarin-forms/user-interface/tableview.md">Documentazione TableView</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TableViewFormDemoPage.cs">Origine demo</a>
    </td>
    <td>
    <img src="views-images/TableViewNewest.png" title="Esempio TableView" class="tableimg">
    </td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/">TimePicker</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> controllo che fornisce il prelievo di tempo. La rappresentazione visiva di un TimePicker è molto simile a quello di <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">voce</a>, ad eccezione del fatto che viene visualizzato un controllo speciale per il prelievo di un'ora al posto di una tastiera.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TimePickerDemoPage.cs"><img src="views-images/TimePicker.png" title="Esempio TimePicker" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/">WebView</a>
    </td>
    <td valign="top">
Oggetto <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> che presenta contenuto HTML.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/">API di WebView</a>
    <br />
    <a href="~/xamarin-forms/user-interface/webview.md">Documentazione di WebView</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/WebViewDemoPage.cs">Origine demo</a>
    </td>
    <td>
    <img src="views-images/WebView.png" title="Esempio di WebView" class="tableimg">
    </td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Collegamenti correlati

- [Introduction to Xamarin.Forms (Introduzione a Xamarin.Forms)](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Raccolta di xamarin. Forms (esempio)](https://developer.xamarin.com/samples/FormsGallery/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Documentazione di xamarin. Forms API](https://developer.xamarin.com/api/root/Xamarin.Forms/)
