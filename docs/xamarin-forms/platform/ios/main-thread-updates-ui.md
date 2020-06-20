---
title: Aggiornamenti principali del controllo thread in iOS
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la piattaforma iOS specifica che consente l'esecuzione di aggiornamenti del layout e del rendering del controllo nel thread principale.
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 005e8216b887b694b33916179ca276cf8091e006
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135980"
---
# <a name="main-thread-control-updates-on-ios"></a>Aggiornamenti principali del controllo thread in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS consente di eseguire il layout di controllo e gli aggiornamenti del rendering sul thread principale, anziché essere eseguiti in un thread in background. Dovrebbe essere raramente necessario, ma in alcuni casi può impedire arresti anomali. Il relativo oggetto utilizzato in XAML impostando la `Application.HandleControlUpdatesOnMainThread` proprietà associabile su `true` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

Il `Application.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `Application.SetHandleControlUpdatesOnMainThread` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per controllare se il layout del controllo e gli aggiornamenti del rendering vengono eseguiti sul thread principale, anziché essere eseguiti in un thread in background. Inoltre, il `Application.GetHandleControlUpdatesOnMainThread` metodo può essere usato per restituire se il layout del controllo e gli aggiornamenti del rendering vengono eseguiti sul thread principale.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
