---
title: Barra schede TabbedPage traslucida in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che imposta la modalità di traslucidità della barra delle schede in un TabbedPage.
ms.prod: xamarin
ms.assetid: 9581AE81-9557-47AD-8B07-25A1AC5F055B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 55fda0be2e260c5aa4a34ab2dcc1ac3cac33b92a
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2020
ms.locfileid: "80070296"
---
# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>Barra schede TabbedPage traslucida in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS viene utilizzata per impostare la modalità di traslucidità della barra scheda in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Viene utilizzato in XAML impostando la proprietà `TabbedPage.TranslucencyMode` associabile su un valore di enumerazione `TranslucencyMode`:

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

Il metodo `TabbedPage.On<iOS>` specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il metodo `TabbedPage.SetTranslucencyMode`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , viene usato per impostare la modalità di traslucidità della barra della scheda in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) specificando uno dei seguenti valori di enumerazione `TranslucencyMode`:

- `Default`, che imposta la barra delle schede sulla relativa modalità di traslucidità predefinita. Questo è il valore predefinito per la proprietà `TabbedPage.TranslucencyMode`.
- `Translucent`, che imposta la barra delle schede come traslucida.
- `Opaque`, che imposta la barra delle schede come opaca.

Inoltre, è possibile usare il metodo `GetTranslucencyMode` per recuperare il valore corrente dell'enumerazione `TranslucencyMode` applicata al [`TabbedPage`](xref:Xamarin.Forms.TabbedPage).

Il risultato è che è possibile impostare la modalità di traslucidità della barra scheda in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

![Screenshot delle barre delle schede trasparenti e opache in iOS](tabbedpage-translucent-tabbar-images/translucencymodes.png "Barre delle schede trasparenti e opache")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
