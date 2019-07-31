---
title: Visibilità della barra di stato della pagina in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che imposta la visibilità della barra di stato in una pagina.
ms.prod: xamarin
ms.assetid: D8BB7C24-A27F-4758-8557-6A81F909ABD9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: a187efa9310fa150ddc884d8b42da5ccb9ecee11
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655852"
---
# <a name="page-status-bar-visibility-on-ios"></a>Visibilità della barra di stato della pagina in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS viene utilizzata per impostare la visibilità della barra di stato in un [`Page`](xref:Xamarin.Forms.Page)e include la possibilità di controllare il modo in cui la barra di stato entra o `Page`esce da. Vengono utilizzati in XAML mediante l'impostazione di `Page.PrefersStatusBarHidden` proprietà associata a un valore del `StatusBarHiddenMode` enumerazione e, facoltativamente, il `Page.PreferredStatusBarUpdateAnimation` proprietà associata a un valore del `UIStatusBarAnimation` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

Il `Page.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Page.SetPrefersStatusBarHidden` metodo, nel `Xamarin.Forms.PlatformConfiguration.iOSSpecific` dello spazio dei nomi, viene usato per impostare la visibilità della barra di stato in un [`Page`](xref:Xamarin.Forms.Page) specificando uno del `StatusBarHiddenMode` valori di enumerazione: `Default`, `True` , o `False`. Il `StatusBarHiddenMode.True` e `StatusBarHiddenMode.False` i valori impostati indipendentemente dall'orientamento del dispositivo, la visibilità della barra di stato e il `StatusBarHiddenMode.Default` valore nasconde la barra di stato in un ambiente verticalmente compact.

Il risultato è che la visibilità della barra di stato in una [`Page`](xref:Xamarin.Forms.Page) può essere impostato:

![](page-status-bar-visibility-images/hide-status-bar.png "Barra di stato visibilità specifico della piattaforma")

> [!NOTE]
> In un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), specificato `StatusBarHiddenMode` valore di enumerazione verrà aggiornato anche la barra di stato di tutte le pagine figlio. In tutti gli altri [`Page`](xref:Xamarin.Forms.Page)-tipi derivati, l'oggetto specificati `StatusBarHiddenMode` valore di enumerazione verrà aggiornati solo la barra di stato nella pagina corrente.

Il `Page.SetPreferredStatusBarUpdateAnimation` metodo viene utilizzato per impostare la modalità barra di stato entra o esce dal [`Page`](xref:Xamarin.Forms.Page) specificando uno dei `UIStatusBarAnimation` valori di enumerazione: `None`, `Fade`, o `Slide`. Se il `Fade` oppure `Slide` viene specificato il valore di enumerazione, una seconda 0,25 animazione viene eseguita come la barra di stato entra o esce dal `Page`.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
