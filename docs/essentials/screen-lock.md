---
title: 'Xamarin.Essentials: Blocco dello schermo'
description: Questo documento descrive la classe ScreenLock in Xamarin.Essentials, che può richiedere per evitare che la schermata che rientrano in stato di sospensione quando l'applicazione è in esecuzione.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c8110b7abc86fe1d12485579f134997718540e6
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848570"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials: Blocco dello schermo

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **ScreenLock** classe può richiedere per evitare che la schermata che rientrano in stato di sospensione quando l'applicazione è in esecuzione.

## <a name="using-screenlock"></a>Usando ScreenLock

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di blocco dello schermo funziona chiamando il `RequestActive` e `RequestRelease` metodi per richiedere la schermata di disattivazione.

```csharp
public class ScreenLockTest
{
    public void ToggleScreenLock()
    {
        if (!ScreenLock.IsActive)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease();
    }
}
```

## <a name="api"></a>API

- [Schermata di codice sorgente di blocco](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Documentazione dell'API di blocco dello schermo](xref:Xamarin.Essentials.ScreenLock)
