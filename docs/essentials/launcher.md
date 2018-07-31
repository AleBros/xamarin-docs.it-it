---
title: Utilità di avvio Xamarin.Essentials
description: La classe di utilità di avvio in Xamarin.Essentials consente a un'applicazione aprire un URI dal sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 3ab820ece127558c1a5ca8b13c05fc4d6f20646e
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353940"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: utilità di avvio

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **utilità di avvio** classe consente a un'applicazione aprire un URI dal sistema. Viene spesso utilizzata quando si completa il collegamento in schemi URI personalizzati di un'altra applicazione. Se si sta cercando di aprire il browser a un sito Web, è consigliabile consultare il **[Browser](open-browser.md)** API.

## <a name="using-launcher"></a>Uso dell'utilità di avvio

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per usare la chiamata di funzionalità di utilità di avvio di `OpenAsync` metodo e passare un `string` o `Uri` aprire. Facoltativamente, il `CanOpenAsync` metodo può essere utilizzato per verificare se lo schema URI può essere gestito da un'applicazione nel dispositivo.

```csharp
public class LauncherTest
{
    public async Task OpenRideShare()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

## <a name="api"></a>API

- [Codice sorgente dell'utilità di avvio](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentazione API dell'utilità di avvio](xref:Xamarin.Essentials.Launcher)
