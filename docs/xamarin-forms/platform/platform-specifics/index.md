---
title: Funzionalità specifiche della piattaforma
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti.
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: 284cce95b4b91d10c6516fa2880a48437340db49
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "38997299"
---
# <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

_Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti._

Le funzionalità specifiche della piattaforma seguenti viene fornita per le visualizzazioni, pagine e layout di xamarin. Forms:

|iOS|Android|WINDOWS|
|--- |--- |--- |
|[VisualElement.BlurEffect](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#blur)|[VisualElement.Elevation](~/xamarin-forms/platform/platform-specifics/consuming/android.md#elevation)|[VisualElement.AccessKey, VisualElement.AccessKeyPlacement, VisualElement.AccessKeyHorizontalOffset e VisualElement.AccessKeyVerticalOffset](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#visualelement-accesskeys)|
|[VisualElement.IsLegacyColorModeEnabled](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#legacy-color-mode)|[VisualElement.IsLegacyColorModeEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#legacy-color-mode)|[VisualElement.IsLegacyColorModeEnabled](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#legacy-color-mode)|
|[VisualElement.IsShadowEnabled](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#drop-shadow)|

Le funzionalità specifiche della piattaforma seguenti viene fornita per le visualizzazioni di xamarin. Forms:

|iOS|Android|WINDOWS|
|--- |--- |--- |
|[Entry.AdjustsFontSizeToFitWidth](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#adjust_font_size)|[Button.UseDefaultPadding e Button.UseDefaultShadow](~/xamarin-forms/platform/platform-specifics/consuming/android.md#button-padding-shadow)|[InputView.DetectReadingOrderFromContent, Label.DetectReadingOrderFromContent](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#inputview-readingorder)|
|[Entry.CursorColor](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#entry-cursorcolor)|[Entry.ImeOptions](~/xamarin-forms/platform/platform-specifics/consuming/android.md#entry-imeoptions)|[ListView.SelectionMode](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#listview-selectionmode)|
|[ListView.SeparatorStyle](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#listview-separatorstyle)|[ListView.IsFastScrollEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#fastscroll)|[SearchBar.IsSpellCheckEnabled](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#searchbar-spellcheck)|
|[Picker.UpdateMode](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode)|[WebView.MixedContentMode](~/xamarin-forms/platform/platform-specifics/consuming/android.md#webview-mixed-content)|[WebView.IsJavaScriptAlertEnabled](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#webview-javascript-alert)|
|[Slider.UpdateOnTap](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#slider-updateontap)|

Le funzionalità specifiche della piattaforma seguenti viene fornita per le pagine di xamarin. Forms:

|iOS|Android|WINDOWS|
|--- |--- |--- |
|[NavigationPage.HideSeparatorBar](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#navigationpage-hideseparatorbar)|[NavigationPage.BarHeight](~/xamarin-forms/platform/platform-specifics/consuming/android.md#navigationpage-barheight)|[MasterDetailPage.CollapsedPaneWidth e MasterDetailPage.CollapseStyle](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#collapsable_navigation_bar)|
|[NavigationPage.IsNavigationBarTranslucent](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#translucent_navigation_bar)|[TabbedPage.IsSmoothScrollEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-transition-animations)|[Page.ToolbarPlacement](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#toolbar_placement)|
|[NavigationPage.StatusBarTextColorMode](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#status_bar_color_mode)|[TabbedPage.IsSwipePagingEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#enable_swipe_paging)|[TabbedPage.HeaderIconsEnabled e TabbedPage.HeaderIconsSize](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#tabbedpage-icons)|
|[NavigationPage.PrefersLargeTitles](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#large_title)|[TabbedPage.ToolbarPlacement TabbedPage.BarItemColor e TabbedPage.BarSelectedItemColor](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar)|
|[Page.ModalPresentationStyle](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#modal-page-presentation-style)|
|[Page.PrefersStatusBarHidden e Page.PreferredStatusBarUpdateAnimation](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#set_status_bar_visibility)|
|[Page.UseSafeArea](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#safe_area_layout)|

Le funzionalità specifiche della piattaforma seguenti viene fornita per il layout di xamarin. Forms:

|iOS|
|--- |
|[ScrollView.ShouldDelayContentTouches](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#delay_content_touches)|

Le funzionalità specifiche della piattaforma seguenti viene fornita per xamarin. Forms [ `Application` ](xref:Xamarin.Forms.Application) classe:

|iOS|Android|
|--- |--- |
|[Application.PanGestureRecognizerShouldRecognizeSimultaneously](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#simultaneous-pan-gesture)|[Application.WindowSoftInputModeAdjust](~/xamarin-forms/platform/platform-specifics/consuming/android.md#soft_input_mode)|
||[Application.SendDisappearingEventOnPause Application.SendAppearingEventOnResume e Application.ShouldPreserveKeyboardOnResume](~/xamarin-forms/platform/platform-specifics/consuming/android.md#disable_lifecycle_events)|

## <a name="consuming-platform-specifics"></a>Utilizzo di funzionalità specifiche della piattaforma

Il processo per l'utilizzo di una specifica della piattaforma tramite XAML o tramite il codice fluent API è come segue:

1. Aggiungere un `xmlns` dichiarazione o `using` direttiva per il [ `Xamarin.Forms.PlatformConfiguration` ](xref:Xamarin.Forms.PlatformConfiguration) dello spazio dei nomi.
1. Aggiungere un `xmlns` dichiarazione o `using` direttiva dello spazio dei nomi che contiene le funzionalità specifiche della piattaforma:
    1. In iOS, questo è il [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi.
    1. In Android, questo è il [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi. Per Android AppCompat, questo è il [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) dello spazio dei nomi.
    1. Nella piattaforma Windows universale, questo è il [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi.
1. Applicare le specifiche della piattaforma da XAML o dal codice con il `On<T>` API fluent. Il valore di `T` può essere il [ `iOS` ](xref:Xamarin.Forms.PlatformConfiguration.iOS), [ `Android` ](xref:Xamarin.Forms.PlatformConfiguration.Android), o [ `Windows` ](xref:Xamarin.Forms.PlatformConfiguration.Windows) tipi dal [ `Xamarin.Forms.PlatformConfiguration` ](xref:Xamarin.Forms.PlatformConfiguration) dello spazio dei nomi.

> [!NOTE]
> Si noti che il tentativo di utilizzare una specifica della piattaforma su una piattaforma in cui non è disponibile non comporterà un errore. Al contrario, il codice verrà eseguito senza la specifica della piattaforma in corso l'applicazione.

Funzionalità specifiche della piattaforma utilizzati tramite il `On<T>` codice fluent return API [ `IPlatformElementConfiguration` ](xref:Xamarin.Forms.IPlatformElementConfiguration`2) oggetti. In tal modo funzionalità specifiche della piattaforma più venga richiamato sullo stesso oggetto con metodo di propagazione.

Per altre informazioni sulle funzionalità specifiche della piattaforma, vedere [funzionalità specifiche della piattaforma Consuming](~/xamarin-forms/platform/platform-specifics/consuming/index.md) e [funzionalità specifiche della piattaforma creazione](~/xamarin-forms/platform/platform-specifics/creating.md).

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/consuming/index.md)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
