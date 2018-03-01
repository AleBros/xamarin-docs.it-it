---
title: Specifiche di piattaforma
description: "Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/17/2017
ms.openlocfilehash: e1598241e64e6ed3a77fec3803e7fa4d94ee7433
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="platform-specifics"></a>Specifiche di piattaforma

_Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti._

Le seguenti funzionalità specifiche della piattaforma viene compilata in xamarin. Forms:

<table>
  <thead>
    <tr>
      <td><strong>iOS</strong></td>
      <td><strong>Android</strong></td>
      <td><strong>Windows</strong></td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#blur">VisualElement.BlurEffect</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/android.md#soft_input_mode">Application.WindowSoftInputModeAdjust</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/windows.md#toolbar_placement">Page.ToolbarPlacement</a></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#large_title">NavigationPage.PrefersLargeTitles</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/android.md#fastscroll">ListView.IsFastScrollEnabled</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/windows.md#collapsable_navigation_bar">MasterDetailPage.CollapsedPaneWidth e MasterDetailPage.CollapseStyle</a></td>
    </tr>    
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#safe_area_layout">Page.UseSafeArea</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/android.md#enable_swipe_paging">TabbedPage.IsSwipePagingEnabled</a></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#translucent_navigation_bar">NavigationPage.IsNavigationBarTranslucent</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/android.md#elevation">Elevation.Elevation</a></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#status_bar_color_mode">NavigationPage.StatusBarTextColorMode</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/android.md#disable_lifecycle_events">Application.SendDisappearingEventOnPause Application.SendAppearingEventOnResume e Application.ShouldPreserveKeyboardOnResume</a></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#adjust_font_size">Entry.AdjustsFontSizeToFitWidth</a></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode">Picker.UpdateMode</a></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#set_status_bar_visibility">Page.PrefersStatusBarHidden e Page.PreferredStatusBarUpdateAnimation</a></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#delay_content_touches">ScrollView.ShouldDelayContentTouches</a></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>

Il processo per l'utilizzo di una specifica piattaforma tramite XAML o un codice di Microsoft Office fluent API è come segue:

1. Aggiungere un `xmlns` dichiarazione o `using` direttiva per il [ `Xamarin.Forms.PlatformConfiguration` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/) dello spazio dei nomi.
1. Aggiungere un `xmlns` dichiarazione o `using` direttiva dello spazio dei nomi che contiene le funzionalità specifiche della piattaforma:
    1. In iOS, si tratta di [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) dello spazio dei nomi.
    1. In Android, si tratta di [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) dello spazio dei nomi. Per Android delle applicazioni, si tratta di [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/) dello spazio dei nomi.
    1. Nella piattaforma Windows universale, si tratta di [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/) dello spazio dei nomi.
1. Applicare la specifica della piattaforma da XAML, o da codice con il `On<T>` API fluent. Il valore di `T` può essere il [ `iOS` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOS/), [ `Android` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.Android/), o [ `Windows` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.Windows/) tipi dal [ `Xamarin.Forms.PlatformConfiguration` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/) dello spazio dei nomi.

> [!NOTE]
> Si noti che il tentativo di utilizzare una specifica della piattaforma su una piattaforma in cui non è disponibile non genererà un errore. Al contrario, il codice viene eseguito senza specifiche della piattaforma viene applicato.

Le specifiche di piattaforma utilizzati tramite il `On<T>` fluent codice restituito API [ `IPlatformElementConfiguration` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IPlatformElementConfiguration%3CTPlatform,TElement%3E/) oggetti. In questo modo le specifiche della piattaforma più venga richiamato sullo stesso oggetto con il metodo di propagazione.

Per ulteriori informazioni sulla piattaforma specifiche, vedere [le specifiche della piattaforma utilizzo](~/xamarin-forms/platform/platform-specifics/consuming/index.md) e [le specifiche della piattaforma creazione](~/xamarin-forms/platform/platform-specifics/creating.md).


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di piattaforma specifiche](~/xamarin-forms/platform/platform-specifics/consuming/index.md)
- [Creazione di piattaforma specifiche](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [PlatformConfiguration](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/)
