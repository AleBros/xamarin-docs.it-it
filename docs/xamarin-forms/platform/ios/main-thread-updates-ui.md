---
title: Aggiornamenti principali del controllo thread in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la piattaforma iOS specifica che consente l'esecuzione di aggiornamenti del layout e del rendering del controllo nel thread principale.
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: d55ef4a97d5d4f320bf152ba05c86aff82eb2f1e
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200122"
---
# <a name="main-thread-control-updates-on-ios"></a>Aggiornamenti principali del controllo thread in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS consente di eseguire il layout di controllo e gli aggiornamenti del rendering sul thread principale, anziché essere eseguiti in un thread in background. Si dovrebbe essere necessario raramente, ma in alcuni casi potrebbe impedire arresti anomali del sistema. Relativo utilizzate in XAML, impostando il `Application.HandleControlUpdatesOnMainThread` la proprietà associabile per `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

Il `Application.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Application.SetHandleControlUpdatesOnMainThread` metodo, nelle [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se il layout dei controlli e gli aggiornamenti di rendering vengono eseguiti nel thread principale, invece di viene eseguita su un thread in background. Inoltre, il `Application.GetHandleControlUpdatesOnMainThread` metodo può essere utilizzato da restituire se il layout dei controlli e gli aggiornamenti di rendering sono eseguiti sul thread principale.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
