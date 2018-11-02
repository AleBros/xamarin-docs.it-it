---
title: 'Xamarin.Essentials: Utilità di avvio'
description: La classe Launcher in Xamarin.Essentials consente a un'applicazione di aprire un URI dal sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 8f5ef8ef97999e9e85944d9fa9d4e57660779a48
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115783"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Launcher

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **Launcher** consente a un'applicazione di aprire un URI dal sistema. Questa funzionalità viene usata spesso per il deep linking negli schemi URI personalizzati di un'altra applicazione. Se lo scopo è aprire il browser su un sito Web specifico, vedere l'API **[Browser](open-browser.md)**.

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

Altre informazioni sull'implementazione di iOS sono disponibili [qui](https://developer.xamarin.com/api/member/UIKit.UIApplication.CanOpenUrl/p/Foundation.NSUrl/)

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Non esistono differenze per questa piattaforma.

-----

## <a name="api"></a>API

- [Codice sorgente di Launcher](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentazione dell'API Launcher](xref:Xamarin.Essentials.Launcher)
