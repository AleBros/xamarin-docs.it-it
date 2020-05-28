---
title: ''
description: Ogni Xamarin.Forms controllo dispone di un renderer associato per ogni piattaforma che crea un'istanza di un controllo nativo. Questo articolo elenca le classi del renderer e del controllo nativo che implementano ogni Xamarin.Forms pagina, layout, visualizzazione e cella.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a56f05f8ff4eb8ece43a9f4f38a669cfdc85c4be
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135187"
---
# <a name="renderer-base-classes-and-native-controls"></a>Classi di base e controlli nativi del renderer

_Ogni Xamarin.Forms controllo dispone di un renderer associato per ogni piattaforma che crea un'istanza di un controllo nativo. Questo articolo elenca le classi del renderer e del controllo nativo che implementano ogni Xamarin.Forms pagina, layout, visualizzazione e cella._

Ad eccezione della classe `MapRenderer`, i renderer specifici delle varie piattaforme sono disponibili negli spazi dei nomi seguenti:

- **iOS** : Xamarin.Forms . Platform. iOS
- **Android** : Xamarin.Forms . Platform. Android
- **Android (appcompat)** - Xamarin.Forms . Platform. Android. AppCompat
- **Piattaforma UWP (Universal Windows Platform) (UWP)** - Xamarin.Forms . Platform. UWP

La classe `MapRenderer` è disponibile negli spazi dei nomi seguenti:

- **iOS** : Xamarin.Forms . Maps. iOS
- **Android** : Xamarin.Forms . Maps. Android
- **Piattaforma UWP (Universal Windows Platform) (UWP)** - Xamarin.Forms . Maps. UWP

> [!NOTE]
> Per informazioni sulla creazione di renderer personalizzati per le applicazioni shell, vedere [ Xamarin.Forms renderer personalizzati](~/xamarin-forms/app-fundamentals/shell/customrenderers.md)della shell.

## <a name="pages"></a>Pagine

Nella tabella seguente sono elencate le classi del renderer e del controllo nativo che implementano ogni tipo di Xamarin.Forms [pagina](~/xamarin-forms/user-interface/controls/pages.md) :

|Pagina|Renderer|iOS|Android|Android (AppCompat)|Piattaforma UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS - Phone), TabletMasterDetailPageRenderer (iOS - Tablet), MasterDetailRenderer (Android), MasterDetailPageRenderer (Android AppCompat), MasterDetailPageRenderer (UWP)|UIViewController (Phone), UISplitViewController (Tablet)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (controllo personalizzato)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS e Android), NavigationPageRenderer (Android AppCompat), NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (controllo personalizzato)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS e Android), TabbedPageRenderer (Android AppCompat), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (Pivot)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Layout

Nella tabella seguente sono elencate le classi del renderer e del controllo nativo che implementano ogni tipo di Xamarin.Forms [layout](~/xamarin-forms/user-interface/controls/layouts.md) :

|Layout|Renderer|iOS|Android|Piattaforma UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|Visualizzazione|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|Visualizzazione|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|Visualizzazione|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|ViewGroup|Bordo|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|Visualizzazione|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|Visualizzazione|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|Visualizzazione|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|Visualizzazione|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|Visualizzazione|FrameworkElement|

## <a name="views"></a>Viste

Nella tabella seguente sono elencate le classi del renderer e del controllo nativo che implementano ogni tipo di Xamarin.Forms [visualizzazione](~/xamarin-forms/user-interface/controls/views.md) :

|Viste|Renderer|iOS|Android|Android (AppCompat)|Piattaforma UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS e Android), BoxViewRenderer (UWP)|UIView|ViewGroup||Rectangle|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|Pulsante|AppCompatButton|Pulsante|
|[`CarouselView`](xref:Xamarin.Forms.CarouselView)|CarouselViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|`CheckBox`|CheckBoxRenderer|UIButton||AppCompatCheckBox|CheckBox|
|[`CollectionView`](xref:Xamarin.Forms.CollectionView)|CollectionViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Immagine|
|[`ImageButton`](xref:Xamarin.Forms.ImageButton)|ImageButtonRenderer|UIButton||AppCompatImageButton|Pulsante|
|`IndicatorView`|IndicatorViewRenderer|UIPageControl||LinearLayout||
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)|MKMapView|MapView||MapControl|
|[`MediaElement`](xref:Xamarin.Forms.MediaElement)|MediaElementRenderer|UIView||VideoView|MediaElement|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|`RadioButton`|RadioButtonRenderer|UIButton||AppCompatRadioButton|RadioButton|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`RefreshView`](xref:Xamarin.Forms.RefreshView)|RefreshViewRenderer|UIView||SwipeRefreshLayout|RefreshContainer|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||Dispositivo di scorrimento|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||Controllo|
|`SwipeView`|SwipeViewRenderer|UIView||Visualizzazione|SwipeControl|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Opzione|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WkWebViewRenderer (iOS), WebViewRenderer (Android e UWP)|WkWebView|WebView||WebView|

> [!NOTE]
> Il `Expander` controllo viene implementato utilizzando un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) , con l'animazione. Non sono pertanto disponibili renderer di piattaforma.

## <a name="cells"></a>Celle

Nella tabella seguente sono elencate le classi del renderer e del controllo nativo che implementano ogni tipo di Xamarin.Forms [cella](~/xamarin-forms/user-interface/controls/cells.md) :

|Celle|Renderer|iOS|Android|Piattaforma UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|UITableViewCell con UITextField|LinearLayout con TextView ed EditText|DataTemplate con TextBox|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|UITableViewCell con UISwitch|Opzione|DataTemplate con un controllo Grid contenente un controllo TextBlock e ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|LinearLayout con due TextView|DataTemplate con un controllo StackPanel contenente due TextBlock|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|UITableViewCell con UIImage|LinearLayout con due controlli TextView e un controllo ImageView|DataTemplate con un controllo Grid contenente un controllo Image e due controlli TextBlock|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Visualizzazione|DataTemplate con ContentPresenter|

## <a name="summary"></a>Riepilogo

In questo articolo sono elencate le classi del renderer e del controllo nativo che implementano ogni Xamarin.Forms pagina, layout, visualizzazione e cella. Ogni Xamarin.Forms controllo dispone di un renderer associato per ogni piattaforma che crea un'istanza di un controllo nativo.
