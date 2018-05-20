---
title: Blocco schermo Xamarin.Essentials
description: La classe ScreenLock può richiedere per evitare che la schermata proficua quando l'applicazione è in esecuzione.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0bdf75825d9c6dc594749fe7aa1e133207cfa0fa
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2018
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
        if (ScreenLock.IsActive)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease();
    }
}
```

## <a name="api"></a>API

- [Schermata di codice sorgente di blocco](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Documentazione dell'API di blocco dello schermo](xref:Xamarin.Essentials.ScreenLock)
