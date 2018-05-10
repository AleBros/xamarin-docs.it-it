---
title: Blocco schermo Xamarin.Essentials
description: La classe ScreenLock può richiedere per evitare che la schermata proficua quando l'applicazione è in esecuzione.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7175362dcb7f85746ea85447936d7fe3e2fd026b
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
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
            ScreenLock.RequestRelease;
    }
}
```

## <a name="api"></a>API

- [Schermata di codice sorgente di blocco](https://github.com/xamarin/Essentials/tree/master/Essentials/ScreenLock)
- [Documentazione dell'API di blocco dello schermo](xref:Xamarin.Essentials.ScreenLock)
