---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 69594924f26afff133d8f211199cac44e66254d9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128025"
---
# <a name="page-status-bar-visibility-on-ios"></a>Visibilità della barra di stato della pagina in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS viene utilizzata per impostare la visibilità della barra di stato in un [`Page`](xref:Xamarin.Forms.Page) e include la possibilità di controllare il modo in cui la barra di stato entra o esce da `Page` . Viene utilizzato in XAML impostando la `Page.PrefersStatusBarHidden` proprietà associata su un valore dell' `StatusBarHiddenMode` enumerazione e, facoltativamente, la `Page.PreferredStatusBarUpdateAnimation` proprietà associata a un valore dell' `UIStatusBarAnimation` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

Il `Page.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `Page.SetPrefersStatusBarHidden` metodo, nello `Xamarin.Forms.PlatformConfiguration.iOSSpecific` spazio dei nomi, viene usato per impostare la visibilità della barra di stato su un oggetto [`Page`](xref:Xamarin.Forms.Page) specificando uno dei `StatusBarHiddenMode` valori di enumerazione: `Default` , `True` o `False` . I `StatusBarHiddenMode.True` `StatusBarHiddenMode.False` valori e impostano la visibilità della barra di stato indipendentemente dall'orientamento del dispositivo e il `StatusBarHiddenMode.Default` valore nasconde la barra di stato in un ambiente compattato verticalmente.

Il risultato è che è possibile impostare la visibilità della barra di stato in un oggetto [`Page`](xref:Xamarin.Forms.Page) :

![](page-status-bar-visibility-images/hide-status-bar.png "Status Bar Visibility Platform-Specific")

> [!NOTE]
> In [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , il valore di `StatusBarHiddenMode` enumerazione specificato aggiornerà anche la barra di stato in tutte le pagine figlio. In tutti gli altri [`Page`](xref:Xamarin.Forms.Page) tipi derivati da, il `StatusBarHiddenMode` valore di enumerazione specificato aggiornerà solo la barra di stato nella pagina corrente.

Il `Page.SetPreferredStatusBarUpdateAnimation` metodo viene usato per impostare il modo in cui la barra di stato entra o esce dall'oggetto [`Page`](xref:Xamarin.Forms.Page) specificando uno dei `UIStatusBarAnimation` valori di enumerazione: `None` , `Fade` o `Slide` . Se `Fade` `Slide` viene specificato il valore di enumerazione o, un'animazione di 0,25 secondi viene eseguita quando la barra di stato entra o esce dall'oggetto `Page` .

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
