---
title: Modalità di Input di tastiera in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare Android specifico della piattaforma che consente di impostare la modalità operativa per un'area di input di tastiera.
ms.prod: xamarin
ms.assetid: AFCDC92F-F61E-42F6-904B-50B5C4949970
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: c1955226f04203ad2e5805c47f35a32281942eab
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209196"
---
# <a name="soft-keyboard-input-mode-on-android"></a>Modalità di Input di tastiera in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma Android consente di impostare la modalità operativa per un'area di input di tastiera e vengono utilizzato in XAML, impostando il [ `Application.WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) proprietà associata a un valore del [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)enumerazione:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

Il `Application.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `Application.UseWindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usato per impostare la modalità operativa l'area di input di tastiera, con la [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumerazione che fornisce due valori: [ `Pan` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) e [ `Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize). Il `Pan` valore viene utilizzato il [ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/) opzione rettifica, che non può essere ridimensionato la finestra quando un controllo di input ha lo stato attivo. Il contenuto della finestra, invece, venga visualizzati in panoramica, in modo che lo stato attivo corrente non è nascosto dalla tastiera. Il `Resize` valore viene utilizzato il [ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/) opzione rettifica, che si ridimensiona la finestra quando un controllo di input è attivo, per liberare spazio per la tastiera.

Il risultato è che la tastiera area modalità operativa può essere impostata quando un controllo di input ha lo stato attivo di input:

[![](soft-keyboard-input-mode-images/pan-resize.png "Tasti di tipo \"soft\" operativa modalità specifiche della piattaforma")](soft-keyboard-input-mode-images/pan-resize-large.png#lightbox "tastiera funziona in modalità specifiche della piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
