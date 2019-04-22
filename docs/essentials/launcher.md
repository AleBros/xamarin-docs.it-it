---
title: 'Xamarin.Essentials: Utilità di avvio'
description: La classe Launcher in Xamarin.Essentials consente a un'applicazione di aprire un URI dal sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 26ab3687e9bcfecbd003ddd0a97f3e46a0cd92b2
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870053"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Utilità di avvio

La classe **Launcher** consente a un'applicazione di aprire un URI dal sistema. Questa funzionalità viene usata spesso per il deep linking negli schemi URI personalizzati di un'altra applicazione. Se lo scopo è aprire il browser su un sito Web specifico, vedere l'API **[Browser](open-browser.md)**.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-launcher"></a>Uso dell'utilità di avvio

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per usare la funzionalità Launcher, chiamare il metodo `OpenAsync` e passare un valore `string` o `Uri` da aprire. Facoltativamente, il metodo `CanOpenAsync` può essere usato per verificare se lo schema URI può essere gestito da un'applicazione nel dispositivo.

```csharp
public class LauncherTest
{
    public async Task OpenRideShareAsync()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

L'attività restituita da `CanOpenAsync` viene completata immediatamente.

# <a name="iostabios"></a>[iOS](#tab/ios)

Se l'applicazione di destinazione in questo dispositivo non è mai stata aperta da `OpenAsync` dall'applicazione in precedenza, iOS richiederà all'utente una volta di consentire all'app di aprirla.

L'attività restituita da `CanOpenAsync` viene completata immediatamente.

Altre informazioni sull'implementazione di iOS sono disponibili [qui](xref:UIKit.UIApplication.CanOpenUrl*)

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Non esistono differenze per questa piattaforma.

-----

## <a name="api"></a>API

- [Codice sorgente di Launcher](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentazione dell'API Launcher](xref:Xamarin.Essentials.Launcher)
