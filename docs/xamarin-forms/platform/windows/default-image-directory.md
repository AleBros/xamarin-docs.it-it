---
title: Directory immagine predefinita in Windows
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare l'oggetto specifico della piattaforma Windows che definisce la directory nel progetto da cui verranno caricati gli asset di immagine.
ms.prod: xamarin
ms.assetid: 537A032B-74DD-4D43-864E-7D7113286D0D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 52197b980726936f4368ef1e4507ea671c9e70b1
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291726"
---
# <a name="default-image-directory-on-windows"></a>Directory immagine predefinita in Windows

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo piattaforma UWP (Universal Windows Platform) specifico della piattaforma definisce la directory nel progetto da cui verranno caricati gli asset di immagine. Viene utilizzato in XAML impostando il `Application.ImageDirectory` su un `string` che rappresenta la directory del progetto che contiene gli asset di immagine:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
             ...
             windows:Application.ImageDirectory="Assets">
    ...
</Application>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
Application.Current.On<Windows>().SetImageDirectory("Assets");
```

Il metodo `Application.On<Windows>` specifica che questa specifica della piattaforma verrà eseguita solo nel piattaforma UWP (Universal Windows Platform). Il metodo `Application.SetImageDirectory`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) , viene usato per specificare la directory del progetto da cui verranno caricate le immagini. Inoltre, è possibile usare il metodo `GetImageDirectory` per restituire una `string` che rappresenta la directory del progetto che contiene le risorse dell'immagine dell'applicazione.

Il risultato è che tutte le immagini utilizzate in un'applicazione verranno caricate dalla directory del progetto specificata.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
