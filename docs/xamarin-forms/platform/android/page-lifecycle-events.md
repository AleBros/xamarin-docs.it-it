---
title: Eventi del ciclo di vita della pagina in Android
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la piattaforma Android specifica che disabilita gli eventi della pagina di scomparsa e di visualizzazione per la sospensione e la ripresa dell'applicazione, rispettivamente.
ms.prod: xamarin
ms.assetid: F6E3759C-D347-407A-91A2-CF9B3B7D4CBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 76724ff17613fcebe35cb68518a1c932eee8aad7
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128726"
---
# <a name="page-lifecycle-events-on-android"></a>Eventi del ciclo di vita della pagina in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android viene usata per disabilitare [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) rispettivamente gli eventi della pagina e in caso di [`Appearing`](xref:Xamarin.Forms.Page.Appearing) sospensione e ripresa dell'applicazione, per le applicazioni che usano AppCompat. Inoltre, include la possibilità di controllare se la tastiera soft viene visualizzata al riavvio, se è stata visualizzata durante la pausa, a condizione che la modalità operativa della tastiera soft sia impostata su [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) .

> [!NOTE]
> Si noti che questi eventi sono abilitati per impostazione predefinita per mantenere il comportamento esistente per le applicazioni che si basano sugli eventi. La disabilitazione di questi eventi rende il ciclo di eventi AppCompat corrispondente al ciclo di eventi pre-AppCompat.

Questa specifica della piattaforma può essere utilizzata in XAML impostando le [`Application.SendDisappearingEventOnPause`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty) [`Application.SendAppearingEventOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty) proprietà associate, e sui [`Application.ShouldPreserveKeyboardOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) `boolean` valori:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

Il `Application.Current.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. [ `Application.SendDisappearingEventOnPause` ] (Xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. Application. SendDisappearingEventOnPause ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Il metodo Application}, System. Boolean)), nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) spazio dei nomi, viene usato per abilitare o disabilitare la generazione dell' [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) evento di pagina quando l'applicazione entra in background. [ `Application.SendAppearingEventOnResume` ] (Xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. Application. SendAppearingEventOnResume ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Application}, System. Boolean)) il metodo viene usato per abilitare o disabilitare la generazione dell' [`Appearing`](xref:Xamarin.Forms.Page.Appearing) evento di pagina quando l'applicazione riprende dallo sfondo. [ `Application.ShouldPreserveKeyboardOnResume` ] (Xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. Application. ShouldPreserveKeyboardOnResume ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Il metodo Application}, System. Boolean)) viene usato per controllare se la tastiera soft viene visualizzata al riavvio, se è stata visualizzata durante la pausa, a condizione che la modalità operativa della tastiera soft sia impostata su [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) .

Il risultato è che gli [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) eventi della pagina e non verranno attivati durante la sospensione e la ripresa dell'applicazione rispettivamente e che, se la tastiera soft è stata visualizzata quando l'applicazione è stata sospesa, verrà visualizzata anche quando l'applicazione riprende:

[![](page-lifecycle-events-images/keyboard-on-resume.png "Lifecycle Events Platform-Specific")](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "Lifecycle Events Platform-Specific")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
