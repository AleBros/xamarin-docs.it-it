---
title: Renderer di classi Base e controlli nativi
description: Ogni controllo di xamarin. Forms dispone di un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Questo articolo elenca le classi di controllo nativo che implementano ogni pagina xamarin. Forms, layout, visualizzazione e cella e il renderer.
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 56df2f7e6b83ddd4a5780506471cbd32a3aced40
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171950"
---
# <a name="renderer-base-classes-and-native-controls"></a>Renderer di classi Base e controlli nativi

_Ogni controllo di xamarin. Forms dispone di un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Questo articolo elenca le classi di controllo nativo che implementano ogni pagina xamarin. Forms, layout, visualizzazione e cella e il renderer._

Ad eccezione del `MapRenderer` (classe), il renderer specifico della piattaforma è reperibile negli spazi dei nomi seguenti:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **Universal Windows Platform (UWP)** – Xamarin.Forms.Platform.UWP

Il `MapRenderer` classe sono reperibili negli spazi dei nomi seguenti:

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **Universal Windows Platform (UWP)** – Xamarin.Forms.Maps.UWP

## <a name="pages"></a>Pages

La tabella seguente elenca le classi di controllo nativo che implementano ogni xamarin. Forms e renderer [pagina](~/xamarin-forms/user-interface/controls/pages.md) tipo:

|Pagina|Renderer|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS-telefono), TabletMasterDetailPageRenderer (iOS, Tablet), MasterDetailRenderer (Android), MasterDetailPageRenderer (AppCompat Android), MasterDetailPageRenderer (UWP)|UIViewController (Phone), UISplitViewController (Tablet)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (controllo personalizzato)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS e Android), NavigationPageRenderer (AppCompat Android), NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (controllo personalizzato)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS e Android), TabbedPageRenderer (AppCompat Android), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (PowerPivot)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Layout

La tabella seguente elenca le classi di controllo nativo che implementano ogni xamarin. Forms e renderer [Layout](~/xamarin-forms/user-interface/controls/layouts.md) tipo:

|Layout|Renderer|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|ViewGroup|Bordo|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|Visualizza|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|Visualizza|FrameworkElement|

## <a name="views"></a>Visualizzazioni

La tabella seguente elenca le classi di controllo nativo che implementano ogni xamarin. Forms e renderer [vista](~/xamarin-forms/user-interface/controls/views.md) tipo:

|Visualizzazioni|Renderer|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS e Android), BoxViewRenderer (UWP)|UIView|ViewGroup||Rettangolo|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|Button|AppCompatButton|Button|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Image|
|`ImageButton`|ImageButtonRenderer|UIButton||AppCompatImageButton|Button|
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||Slider|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||Control|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Opzione|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WebViewRenderer|UIWebView|WebView||WebView|

## <a name="cells"></a>Celle

La tabella seguente elenca le classi di controllo nativo che implementano ogni xamarin. Forms e renderer [cella](~/xamarin-forms/user-interface/controls/cells.md) tipo:

|Celle|Renderer|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|UITableViewCell con un UITextField|LinearLayout con TextView ed EditText|DataTemplate con una casella di testo|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|UITableViewCell con un UISwitch|Opzione|Con una griglia contenente un TextBlock e il controllo ToggleSwitch DataTemplate|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|LinearLayout con due oggetti TextViews|DataTemplate con un elemento StackPanel contenente due TextBlock|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|UITableViewCell con un UIImage|LinearLayout con due oggetti TextViews e un ImageView|DataTemplate con una griglia che contiene un'immagine e due TextBlock|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Visualizza|DataTemplate con ContentPresenter|

## <a name="summary"></a>Riepilogo

Questo articolo è elencato il renderer e classi di controlli nativi che implementano ogni pagina xamarin. Forms, layout, visualizzazione e celle. Ogni controllo di xamarin. Forms dispone di un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo.

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati (Video di Xamarin University)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
