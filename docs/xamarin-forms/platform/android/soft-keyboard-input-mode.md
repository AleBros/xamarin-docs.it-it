---
title: Modalità di input tastiera soft in Android
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma Android che imposta la modalità operativa per un'area di input della tastiera soft.
ms.prod: xamarin
ms.assetid: AFCDC92F-F61E-42F6-904B-50B5C4949970
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c62d09c7d7848d9f62c018caa1698bb53a2a39a8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128713"
---
# <a name="soft-keyboard-input-mode-on-android"></a>Modalità di input tastiera soft in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android viene utilizzata per impostare la modalità operativa per un'area di input della tastiera soft e viene utilizzata in XAML impostando la [`Application.WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) proprietà associata su un valore dell' [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumerazione:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

Il `Application.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. [ `Application.UseWindowSoftInputModeAdjust` ] (Xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Application. UseWindowSoftInputModeAdjust ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Applicazione}, Xamarin.Forms . Il metodo PlatformConfiguration. AndroidSpecific. WindowSoftInputModeAdjust), nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene usato per impostare la modalità operativa dell'area di input della tastiera soft, con l' [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumerazione che fornisce due valori: [`Pan`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) e [`Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) . Il `Pan` valore usa l' [`AdjustPan`](xref:Android.Views.SoftInput.AdjustPan) opzione di regolazione, che non ridimensiona la finestra quando un controllo di input ha lo stato attivo. Al contrario, il contenuto della finestra viene stroncato in modo che lo stato attivo corrente non sia nascosto dalla tastiera soft. Il `Resize` valore usa l' [`AdjustResize`](xref:Android.Views.SoftInput.AdjustResize) opzione di regolazione, che ridimensiona la finestra quando un controllo di input ha lo stato attivo, per fare spazio alla tastiera soft.

Il risultato è che è possibile impostare la modalità operativa dell'area di input della tastiera soft quando un controllo di input ha lo stato attivo:

[![](soft-keyboard-input-mode-images/pan-resize.png "Soft Keyboard Operating Mode Platform-Specific")](soft-keyboard-input-mode-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Platform-Specific")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
