---
title: Renderer di classi Base e i controlli nativi
description: "Ogni controllo in xamarin. Forms è un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Questo articolo vengono elencati le classi di controlli nativi che implementano ogni pagina xamarin. Forms, layout, visualizzazione e cella renderer."
ms.topic: article
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2016
ms.openlocfilehash: 95518d9b23db68cc972549c730deeea968512444
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="renderer-base-classes-and-native-controls"></a>Renderer di classi Base e i controlli nativi

_Ogni controllo in xamarin. Forms è un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Questo articolo vengono elencati le classi di controlli nativi che implementano ogni pagina xamarin. Forms, layout, visualizzazione e cella renderer._

Ad eccezione del `MapRenderer` (classe), il renderer specifico della piattaforma sono disponibili negli spazi dei nomi seguenti:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **Windows Phone 8** – Xamarin.Forms.Platform.WinPhone
- **WinRT** – Xamarin.Forms.Platform.WinRT
- **La piattaforma Windows universale (UWP)** – Xamarin.Forms.Platform.UWP

La `MapRenderer` classe è reperibile in spazi dei nomi seguenti:

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **Windows Phone 8** – Xamarin.Forms.Maps.WP8
- **WinRT** – Xamarin.Forms.Maps.WinRT
- **La piattaforma Windows universale (UWP)** – Xamarin.Forms.Maps.UWP

## <a name="pages"></a>Pages

Nella tabella seguente sono elencate le classi di controllo nativo che implementano ogni xamarin. Forms e i renderer [pagina](~/xamarin-forms/user-interface/controls/pages.md) tipo:

<table>
 <thead>
   <tr>
     <td><strong>pagina</strong></td>
     <td><strong>Renderer</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Android (delle applicazioni)</strong></td>
     <td><strong>Windows Phone 8 < / strong</td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md">PageRenderer</a></td>
     <td>UIViewController</td>
     <td>ViewGroup</td>
     <td></td>
     <td>Panel</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/">MasterDetailPage</a></td>
     <td><p>PhoneMasterDetailRenderer (iOS-telefono)</p><p>TabletMasterDetailPageRenderer (iOS: Tablet)</p><p>MasterDetailRenderer (Android)</p><p>MasterDetailPageRenderer (Android delle applicazioni)</p><p>MasterDetailRenderer (Windows Phone)</p><p>MasterDetailPageRenderer (WinRT)</p></td>
     <td><p>UIViewController (Phone)</p><p>UISplitViewController (Tablet)</p></td>
     <td>DrawerLayout (v4)</td>
     <td>DrawerLayout (v4)</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement (controllo)</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/">NavigationPage</a></td>
     <td><p>NavigationRenderer (iOS e Android)</p><p>NavigationPageRenderer (Android delle applicazioni)</p><p>NavigationPageRenderer (Windows Phone 8 e WinRT)</p></td>
     <td>UIToolbar</td>
     <td>ViewGroup</td>
     <td>ViewGroup</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement (controllo)</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/">TabbedPage</a></td>
     <td><p>TabbedRenderer (iOS e Android)</p><p>TabbedPageRenderer (Android delle applicazioni)</p><p>TabbedPageRenderer (Windows Phone 8 e WinRT)</p></td>
     <td>UIView</td>
     <td>ViewPager</td>
     <td>ViewPager</td>
     <td>UIElement (Pivot)</td>
     <td>FrameworkElement (Pivot)</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/">TemplatedPage</a></td>
     <td>PageRenderer</td>
     <td>UIViewController</td>
     <td>ViewGroup</td>
     <td></td>
     <td>Panel</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/">CarouselPage<a/></td>
     <td>CarouselPageRenderer</td>
     <td>UIScrollView</td>
     <td>ViewPager</td>
     <td>ViewPager</td>
     <td>UIElement (Panorama)</td>
     <td>FrameworkElement (FlipView)</td>
   </tr>
 </tbody>
</table>

## <a name="layouts"></a>Layout

Nella tabella seguente sono elencate le classi di controllo nativo che implementano ogni xamarin. Forms e i renderer [Layout](~/xamarin-forms/user-interface/controls/layouts.md) tipo:

<table>
 <thead>
   <tr>
     <td><strong>Layout</strong></td>
     <td><strong>Renderer</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Windows Phone 8</strong></td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/">ContentPresenter</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Visualizza</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/">ContentView</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Visualizza</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/">Frame</a></td>
     <td>FrameRenderer</td>
     <td>UIView</td>
     <td>ViewGroup</td>
     <td>Bordo</td>
     <td>Bordo</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a></td>
     <td>ScrollViewRenderer</td>
     <td>UIScrollView</td>
     <td>ScrollView</td>
     <td>ScrollViewer</td>
     <td>ScrollViewer</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/">TemplatedView</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Visualizza</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/">AbsoluteLayout</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Visualizza</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/">Griglia</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Visualizza</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/">RelativeLayout</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Visualizza</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Visualizza</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
 </tbody>
</table>

## <a name="views"></a>Visualizzazioni

Nella tabella seguente sono elencate le classi di controllo nativo che implementano ogni xamarin. Forms e i renderer [vista](~/xamarin-forms/user-interface/controls/views.md) tipo:

<table>
 <thead>
   <tr>
     <td><strong>Visualizzazioni</strong></td>
     <td><strong>Renderer</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Android (delle applicazioni)</strong></td>
     <td><strong>Windows Phone 8</strong></td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/">ActivityIndicator</a></td>
     <td>ActivityIndicatorRenderer</td>
     <td>UIActivityIndicator</td>
     <td>ProgressBar</td>
     <td></td>
     <td>ProgressBar</td>
     <td>ProgressBar</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/">BoxView</a></td>
     <td><p>BoxRenderer (iOS e Android)</p><p>BoxViewRenderer (Windows Phone e WinRT)</p></td>
     <td>UIView</td>
     <td>ViewGroup</td>
     <td></td>
     <td>Rettangolo</td>
     <td>Rettangolo</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Button/">Pulsante</a></td>
     <td>ButtonRenderer</td>
     <td>UIButton</td>
     <td>Button</td>
     <td>AppCompatButton</td>
     <td>Button</td>
     <td>Button</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselView/">CarouselView</a></td>
     <td>CarouselViewRenderer</td>
     <td>UIScrollView</td>
     <td>RecyclerView</td>
     <td></td>
     <td>FlipView</td>
     <td>FlipView</td>
   </tr>   
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/">DatePicker</a></td>
     <td>DatePickerRenderer</td>
     <td>UITextField</td>
     <td>EditText</td>
     <td></td>
     <td>DatePicker</td>
     <td>DatePicker</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/">Editor</a></td>
     <td>EditorRenderer</td>
     <td>UITextView</td>
     <td>EditText</td>
     <td></td>
     <td>TextBox</td>
     <td>TextBox</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">Voce</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/entry.md">EntryRenderer</a></td>
     <td>UITextField</td>
     <td>EditText</td>
     <td></td>
     <td>PhoneTextBox/PasswordBox</td>
     <td>TextBox</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Image/">Immagine</a></td>
     <td>ImageRenderer</td>
     <td>UIImageView</td>
     <td>ImageView</td>
     <td></td>
     <td>Image</td>
     <td>Image</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Label/">Etichetta</a></td>
     <td>LabelRenderer</td>
     <td>UILabel</td>
     <td>TextView</td>
     <td></td>
     <td>TextBlock</td>
     <td>TextBlock</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/">ListView</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/listview.md">ListViewRenderer</a></td>
     <td>UITableView</td>
     <td>ListView</td>
     <td></td>
     <td>LongListSelector</td>
     <td>ListView</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/">mappa</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md">MapRenderer</a></td>
     <td>MKMapView</td>
     <td>MapView</td>
     <td></td>
     <td>Mappa</td>
     <td>MapControl</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/">Selezione</a></td>
     <td>PickerRenderer</td>
     <td>UITextField</td>
     <td>EditText</td>
     <td>EditText</td>
     <td>FrameworkElement</td>
     <td>ComboBox</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/">ProgressBar</a></td>
     <td>ProgressBarRenderer</td>
     <td>UIProgressView</td>
     <td>ProgressBar</td>
     <td></td>
     <td>ProgressBar</td>
     <td>ProgressBar</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/">SearchBar</a></td>
     <td>SearchBarRenderer</td>
     <td>UISearchBar</td>
     <td>SearchView</td>
     <td></td>
     <td>PhoneTextBox</td>
     <td><p>SearchBox (WinRT)</p><p>AutoSuggestBox (UWP)</p></td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/">Dispositivo di scorrimento</a></td>
     <td>SliderRenderer</td>
     <td>UISlider</td>
     <td>SeekBar</td>
     <td></td>
     <td>Slider</td>
     <td>Slider</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/">Gestore di istruzioni</a></td>
     <td>StepperRenderer</td>
     <td>UIStepper</td>
     <td>LinearLayout</td>
     <td></td>
     <td>Due pulsanti e il bordo con un elemento StackPanel</td>
     <td><p>UserControl (WinRT)</p><p>Controllo (UWP)</p></td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/">commutatore</a></td>
     <td>SwitchRenderer</td>
     <td>UISwitch</td>
     <td>Opzione</td>
     <td>SwitchCompat</td>
     <td>Bordo con un ToggleSwitchButton</td>
     <td>ToggleSwitch</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/">TableView</a></td>
     <td>TableViewRenderer</td>
     <td>UITableView</td>
     <td>ListView</td>
     <td></td>
     <td>Griglia con un controllo TextBlock e ListBox</td>
     <td>ListView</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/">TimePicker</a></td>
     <td>TimePickerRenderer</td>
     <td>UITextField</td>
     <td>EditText</td>
     <td></td>
     <td>TimePicker</td>
     <td>TimePicker</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/">WebView</a></td>
     <td>WebViewRenderer</td>
     <td>UIWebView</td>
     <td>WebView</td>
     <td></td>
     <td>WebBrowser</td>
     <td>WebView</td>
   </tr>
 </tbody>
</table>

## <a name="cells"></a>Celle

Nella tabella seguente sono elencate le classi di controllo nativo che implementano ogni xamarin. Forms e i renderer [cella](~/xamarin-forms/user-interface/controls/cells.md) tipo:

<table>
 <thead>
   <tr>
     <td><strong>Celle</strong></td>
     <td><strong>Renderer</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Windows Phone 8</strong></td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/">EntryCell</a></td>
   <td>EntryCellRenderer</td>
   <td>UITableViewCell con un UITextField</td>
   <td>LinearLayout TextView e EditText</td>
   <td>DataTemplate con una griglia contenente un controllo TextBlock e un PhoneTextBox</td>
   <td>DataTemplate con una casella di testo</td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/">SwitchCell</a></td>
   <td>SwitchCellRenderer</td>
   <td>UITableViewCell con un UISwitch</td>
   <td>Opzione</td>
   <td>DataTemplate con un ToggleSwitch</td>
   <td>DataTemplate con una griglia contenente un controllo TextBlock e ToggleSwitch</td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">TextCell</a></td>
   <td>TextCellRenderer</td>
   <td>UITableViewCell</td>
   <td>LinearLayout con due oggetti TextViews</td>
   <td>DataTemplate con un pulsante contenente un elemento StackPanel con due TextBlock</td>
   <td>DataTemplate con un elemento StackPanel contenente due TextBlock</td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/">ImageCell</a></td>
   <td>ImageCellRenderer</td>
   <td>UITableViewCell con un UIImage</td>
   <td>LinearLayout con due oggetti TextViews e un ImageView</td>
   <td>DataTemplate con un pulsante che contiene una griglia con un'immagine e un elemento StackPanel contenente due TextBlock</td>
   <td>DataTemplate con una griglia contenente un'immagine e due TextBlock</td>  
   </td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/">ViewCell</a></td>
   <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md">ViewCellRenderer</a></td>
   <td>UITableViewCell</td>
   <td>Visualizza</td>
   <td>DataTemplate con ContentPresenter</td>
   <td>DataTemplate con ContentPresenter</td>  
   </td>
 </tr>
 </tbody>
</table>

## <a name="summary"></a>Riepilogo

In questo articolo è elencato il renderer e classi di controlli nativi che implementano ogni pagina xamarin. Forms, layout, visualizzazione e cella. Ogni controllo in xamarin. Forms è un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo.



## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzato (Video di University Xamarin)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
