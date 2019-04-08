---
title: Aggiornamenti di controllo del Thread principale in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare iOS specifiche della piattaforma che consente il layout dei controlli e gli aggiornamenti da eseguire sul thread principale di rendering.
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 56109cc9064de4b995e75ceb967abe4995504660
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209546"
---
# <a name="main-thread-control-updates-on-ios"></a>Aggiornamenti di controllo del Thread principale in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma iOS consente il layout dei controlli e gli aggiornamenti da eseguire sul thread principale, invece di viene eseguita su un thread in background di rendering. Si dovrebbe essere necessario raramente, ma in alcuni casi potrebbe impedire arresti anomali del sistema. Relativo utilizzate in XAML, impostando il `Application.HandleControlUpdatesOnMainThread` la proprietà associabile per `true`:

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

Il `Application.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Application.SetHandleControlUpdatesOnMainThread` metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se il layout dei controlli e gli aggiornamenti di rendering vengono eseguiti nel thread principale, invece di viene eseguita su un thread in background. Inoltre, il `Application.GetHandleControlUpdatesOnMainThread` metodo può essere utilizzato da restituire se il layout dei controlli e gli aggiornamenti di rendering sono eseguiti sul thread principale.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
