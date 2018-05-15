---
title: Blocco schermo Xamarin.Essentials
description: La classe ScreenLock può richiedere per evitare che la schermata proficua quando l'applicazione è in esecuzione.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: f7e6a3d6933ed1fce7522fdbb8102e5100bd1589
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-screen-lock"></a>Blocco schermo Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **ScreenLock** (classe) può richiedere per evitare che la schermata proficua quando l'applicazione è in esecuzione.

## <a name="using-screenlock"></a>Utilizzo ScreenLock

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di blocco schermo funziona chiamando il `RequestActive` e `RequestRelease` metodi per richiedere la schermata di disattivazione.

```csharp
public class ScreenLockTest
{
    public void ToggleScreenLock()
    {
        if (ScreenLock.IsMonitoring)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease();
    }
}
```

## <a name="api"></a>API

- [Schermata di codice sorgente di blocco](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Documentazione dell'API di blocco dello schermo](xref:Xamarin.Essentials.ScreenLock)
