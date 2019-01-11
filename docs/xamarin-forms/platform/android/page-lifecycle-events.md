---
title: Eventi del ciclo di vita di pagina in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare Android specifiche della piattaforma che disattiva la Disappearing e gli eventi della pagina Appearing aplikaci sospendere e riprendere, rispettivamente.
ms.prod: xamarin
ms.assetid: F6E3759C-D347-407A-91A2-CF9B3B7D4CBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: ab9c404fc9051014fd3a243848290087f43a46d2
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209306"
---
# <a name="page-lifecycle-events-on-android"></a>Eventi del ciclo di vita di pagina in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma Android consente di disabilitare il [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) eventi pagina aplikaci sospendere e riprendere rispettivamente per le applicazioni che usano AppCompat. Inoltre, include la possibilità di controllare se viene visualizzata la tastiera al ripristino, se è stata visualizzata dopo la pausa, condizione che la modalità operativa della tastiera è impostata su [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

> [!NOTE]
> Si noti che questi eventi vengono abilitati per impostazione predefinita per mantenere il comportamento esistente per le applicazioni basate su eventi. La disabilitazione di questi eventi rende il ciclo degli eventi AppCompat corrispondere il ciclo di eventi di pre-AppCompat.

Questo specifico della piattaforma può essere usata in XAML, impostando il [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty), [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty), e [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) proprietà associate a `boolean` valori:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

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

Il `Application.Current.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) dello spazio dei nomi, viene utilizzato per abilitare o disabilitare la generazione dell'evento il [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) evento della pagina, quando l'applicazione entra in background. Il [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) metodo viene utilizzato per abilitare o disabilitare la generazione dell'evento il [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) evento pagina, se l'applicazione si riattiva dallo sfondo. Il [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) viene usato il metodo controllo se viene visualizzata la tastiera al ripristino, se è stata visualizzata dopo la pausa, fornite che la modalità operativa della tastiera temporanea è impostata su [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

Il risultato è che il [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) gli eventi della pagina non verranno generati dopo la pausa dell'applicazione e riprendere rispettivamente, e che, se la tastiera era visualizzato quando l'applicazione è stato sospeso, verrà inoltre visualizzato quando l'applicazione riprende a:

[![](page-lifecycle-events-images/keyboard-on-resume.png "Specifica della piattaforma gli eventi del ciclo di vita")](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "specifiche della piattaforma gli eventi del ciclo di vita")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
