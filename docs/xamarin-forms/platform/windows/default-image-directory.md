---
title: Directory immagine predefinita in Windows
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare l'oggetto specifico della piattaforma Windows che definisce la directory nel progetto da cui verranno caricati gli asset di immagine.
ms.prod: xamarin
ms.assetid: 537A032B-74DD-4D43-864E-7D7113286D0D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d5c5e6db8ddcf3cef32bde5c387adc378afd0058
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135612"
---
# <a name="default-image-directory-on-windows"></a>Directory immagine predefinita in Windows

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo piattaforma UWP (Universal Windows Platform) specifico della piattaforma definisce la directory nel progetto da cui verranno caricati gli asset di immagine. Viene utilizzato in XAML impostando `Application.ImageDirectory` su un oggetto `string` che rappresenta la directory del progetto che contiene le risorse dell'immagine:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
             ...
             windows:Application.ImageDirectory="Assets">
    ...
</Application>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
Application.Current.On<Windows>().SetImageDirectory("Assets");
```

Il `Application.On<Windows>` metodo specifica che questa specifica della piattaforma verrà eseguita solo sul piattaforma UWP (Universal Windows Platform). Il `Application.SetImageDirectory` metodo, nello [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) spazio dei nomi, viene usato per specificare la directory del progetto da cui verranno caricate le immagini. Inoltre, il `GetImageDirectory` metodo può essere usato per restituire un oggetto `string` che rappresenta la directory del progetto che contiene le risorse dell'immagine dell'applicazione.

Il risultato è che tutte le immagini utilizzate in un'applicazione verranno caricate dalla directory del progetto specificata.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
