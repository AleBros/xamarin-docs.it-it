---
title: Renderer di classi Base e i controlli nativi
description: Ogni controllo in xamarin. Forms è un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Questo articolo vengono elencati le classi di controlli nativi che implementano ogni pagina xamarin. Forms, layout, visualizzazione e cella renderer.
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2016
ms.openlocfilehash: 513b9b3738b9481444cdad10daa9b11a8441c9dd
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2018
---
# <a name="renderer-base-classes-and-native-controls"></a>Renderer di classi Base e i controlli nativi

_Ogni controllo in xamarin. Forms è un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Questo articolo vengono elencati le classi di controlli nativi che implementano ogni pagina xamarin. Forms, layout, visualizzazione e cella renderer._

Ad eccezione del `MapRenderer` (classe), il renderer specifico della piattaforma sono disponibili negli spazi dei nomi seguenti:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **La piattaforma Windows universale (UWP)** – Xamarin.Forms.Platform.UWP

La `MapRenderer` classe è reperibile in spazi dei nomi seguenti:

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **La piattaforma Windows universale (UWP)** – Xamarin.Forms.Maps.UWP

## <a name="pages"></a>Pages

Nella tabella seguente sono elencate le classi di controllo nativo che implementano ogni xamarin. Forms e i renderer [pagina](~/xamarin-forms/user-interface/controls/pages.md) tipo:

|Pagina|Renderer|iOS|Android|Android (delle applicazioni)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)|PhoneMasterDetailRenderer (iOS-telefono), TabletMasterDetailPageRenderer (iOS: Tablet), MasterDetailRenderer (Android), MasterDetailPageRenderer (Android delle applicazioni), MasterDetailPageRenderer (UWP)|UIViewController (Phone), UISplitViewController (Tablet)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (controllo)|
|[`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)|NavigationRenderer (iOS e Android), NavigationPageRenderer (Android delle applicazioni), NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (controllo)|
|[`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)|TabbedRenderer (iOS e Android), TabbedPageRenderer (Android delle applicazioni), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (Pivot)|
|[`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Layout

Nella tabella seguente sono elencate le classi di controllo nativo che implementano ogni xamarin. Forms e i renderer [Layout](~/xamarin-forms/user-interface/controls/layouts.md) tipo:

|Layout|Renderer|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`Frame`](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/)|FrameRenderer|UIView|ViewGroup|Bordo|
|[`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)|ScrollViewRenderer|UIScrollView|Elemento ScrollView|ScrollViewer|
|[`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)|ViewRenderer|UIView|Visualizza|FrameworkElement|

## <a name="views"></a>Visualizzazioni

Nella tabella seguente sono elencate le classi di controllo nativo che implementano ogni xamarin. Forms e i renderer [vista](~/xamarin-forms/user-interface/controls/views.md) tipo:

|Visualizzazioni|Renderer|iOS|Android|Android (delle applicazioni)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)|BoxRenderer (iOS e Android), BoxViewRenderer (UWP)|UIView|ViewGroup||Rettangolo|
|[`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)|ButtonRenderer|UIButton|Button|AppCompatButton|Button|
|[`CarouselView`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselView/)|CarouselViewRenderer|UIScrollView|RecyclerView||FlipView|
|[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)|ImageRenderer|UIImageView|ImageView||Image|
|[`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`ProgressBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)|SliderRenderer|UISlider|SeekBar||Slider|
|[`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)|StepperRenderer|UIStepper|LinearLayout||Control|
|[`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)|SwitchRenderer|UISwitch|Opzione|SwitchCompat|ToggleSwitch|
|[`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)|WebViewRenderer|UIWebView|WebView||WebView|

## <a name="cells"></a>Celle

Nella tabella seguente sono elencate le classi di controllo nativo che implementano ogni xamarin. Forms e i renderer [cella](~/xamarin-forms/user-interface/controls/cells.md) tipo:

|Celle|Renderer|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/)|EntryCellRenderer|UITableViewCell con un UITextField|LinearLayout TextView e EditText|DataTemplate con una casella di testo|
|[`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/)|SwitchCellRenderer|UITableViewCell con un UISwitch|Opzione|DataTemplate con una griglia contenente un controllo TextBlock e ToggleSwitch|
|[`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/)|TextCellRenderer|UITableViewCell|LinearLayout con due oggetti TextViews|DataTemplate con un elemento StackPanel contenente due TextBlock|
|[`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/)|ImageCellRenderer|UITableViewCell con un UIImage|LinearLayout con due oggetti TextViews e un ImageView|DataTemplate con una griglia contenente un'immagine e due TextBlock|
|[`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Visualizza|DataTemplate con ContentPresenter|

## <a name="summary"></a>Riepilogo

In questo articolo è elencato il renderer e classi di controlli nativi che implementano ogni pagina xamarin. Forms, layout, visualizzazione e cella. Ogni controllo in xamarin. Forms è un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo.

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzato (Video di University Xamarin)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
