---
title: Eventi del ciclo di vita della pagina in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la piattaforma Android specifica che disabilita gli eventi della pagina di scomparsa e di visualizzazione per la sospensione e la ripresa dell'applicazione, rispettivamente.
ms.prod: xamarin
ms.assetid: F6E3759C-D347-407A-91A2-CF9B3B7D4CBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 1745f137f2eeb04c0894c57bb0e45e5c43be7d0b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649939"
---
# <a name="page-lifecycle-events-on-android"></a>Eventi del ciclo di vita della pagina in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android viene usata per disabilitare rispettivamente [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) gli [`Appearing`](xref:Xamarin.Forms.Page.Appearing) eventi della pagina e in caso di sospensione e ripresa dell'applicazione, per le applicazioni che usano AppCompat. Inoltre, include la possibilità di controllare se viene visualizzata la tastiera al ripristino, se è stata visualizzata dopo la pausa, condizione che la modalità operativa della tastiera è impostata su [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

> [!NOTE]
> Si noti che questi eventi vengono abilitati per impostazione predefinita per mantenere il comportamento esistente per le applicazioni basate su eventi. La disabilitazione di questi eventi rende il ciclo degli eventi AppCompat corrispondere il ciclo di eventi di pre-AppCompat.

Questo specifico della piattaforma può essere usata in XAML, impostando il [`Application.SendDisappearingEventOnPause`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty), [`Application.SendAppearingEventOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty), e [`Application.ShouldPreserveKeyboardOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) proprietà associate a `boolean` valori:

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

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

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

Il `Application.Current.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [`Application.SendDisappearingEventOnPause`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) metodo, nel [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) dello spazio dei nomi, viene utilizzato per abilitare o disabilitare la generazione dell'evento il [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) evento della pagina, quando l'applicazione entra in background. Il [`Application.SendAppearingEventOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) metodo viene utilizzato per abilitare o disabilitare la generazione dell'evento il [`Appearing`](xref:Xamarin.Forms.Page.Appearing) evento pagina, se l'applicazione si riattiva dallo sfondo. Il [`Application.ShouldPreserveKeyboardOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) viene usato il metodo controllo se viene visualizzata la tastiera al ripristino, se è stata visualizzata dopo la pausa, fornite che la modalità operativa della tastiera temporanea è impostata su [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

Il risultato è che il [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) e [`Appearing`](xref:Xamarin.Forms.Page.Appearing) gli eventi della pagina non verranno generati dopo la pausa dell'applicazione e riprendere rispettivamente, e che, se la tastiera era visualizzato quando l'applicazione è stato sospeso, verrà inoltre visualizzato quando l'applicazione riprende a:

[![](page-lifecycle-events-images/keyboard-on-resume.png "Specifica della piattaforma gli eventi del ciclo di vita")](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "specifiche della piattaforma gli eventi del ciclo di vita")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
