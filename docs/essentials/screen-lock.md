---
title: 'Xamarin.Essentials: Blocco schermo'
description: Questo documento descrive la classe ScreenLock in Xamarin.Essentials, che può richiedere per evitare che la schermata proficua quando l'applicazione è in esecuzione.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c8110b7abc86fe1d12485579f134997718540e6
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782908"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials: Blocco schermo

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
