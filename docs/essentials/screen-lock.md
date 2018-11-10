---
title: 'Xamarin.Essentials: Screen Lock'
description: Questo documento descrive la classe ScreenLock in Xamarin.Essentials, che può richiedere di evitare che lo schermo entri in modalità di sospensione quando l'applicazione è in esecuzione.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3bf8c949650cf9f039a5a516366a90e717dc944b
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675315"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials: Screen Lock

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **ScreenLock** può richiedere di evitare che lo schermo entri in modalità di sospensione quando l'applicazione è in esecuzione.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-screenlock"></a>Uso di ScreenLock

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di blocco dello schermo opera chiamando i metodi `RequestActive` e `RequestRelease` per evitare la disattivazione dello schermo.

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

- [Codice sorgente di Screen Lock](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Documentazione dell'API Screen Lock](xref:Xamarin.Essentials.ScreenLock)
