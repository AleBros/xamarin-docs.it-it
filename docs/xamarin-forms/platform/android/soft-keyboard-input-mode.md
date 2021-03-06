---
title: Modalità di input tastiera soft in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma Android che imposta la modalità operativa per un'area di input della tastiera soft.
ms.prod: xamarin
ms.assetid: AFCDC92F-F61E-42F6-904B-50B5C4949970
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 835f75b473fe966b5e92e7cb599f7675a7a459dd
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655665"
---
# <a name="soft-keyboard-input-mode-on-android"></a>Modalità di input tastiera soft in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android viene utilizzata per impostare la modalità operativa per un'area di input della tastiera soft e viene utilizzata in XAML impostando [`Application.WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) la proprietà associata su un valore [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) dell'enumerazione:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

Il `Application.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [`Application.UseWindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)) metodo, nel [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usato per impostare la modalità operativa l'area di input di tastiera, con la [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumerazione che fornisce due valori: [`Pan`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) e [`Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize). Il `Pan` valore viene utilizzato il [`AdjustPan`](xref:Android.Views.SoftInput.AdjustPan) opzione rettifica, che non può essere ridimensionato la finestra quando un controllo di input ha lo stato attivo. Il contenuto della finestra, invece, venga visualizzati in panoramica, in modo che lo stato attivo corrente non è nascosto dalla tastiera. Il `Resize` valore viene utilizzato il [`AdjustResize`](xref:Android.Views.SoftInput.AdjustResize) opzione rettifica, che si ridimensiona la finestra quando un controllo di input è attivo, per liberare spazio per la tastiera.

Il risultato è che la tastiera area modalità operativa può essere impostata quando un controllo di input ha lo stato attivo di input:

[![](soft-keyboard-input-mode-images/pan-resize.png "Tasti di tipo \"soft\" operativa modalità specifiche della piattaforma")](soft-keyboard-input-mode-images/pan-resize-large.png#lightbox "tastiera funziona in modalità specifiche della piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
