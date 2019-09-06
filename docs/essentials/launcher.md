---
title: 'Xamarin.Essentials: Utilità di avvio'
description: La classe Launcher in Xamarin.Essentials consente a un'applicazione di aprire un URI dal sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: c1c9fc4ce7e7a1dc4ab9573d29407f081e2ad8a1
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199842"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Utilità di avvio

La classe **Launcher** consente a un'applicazione di aprire un URI dal sistema. Questa funzionalità viene usata spesso per il deep linking negli schemi URI personalizzati di un'altra applicazione. Se lo scopo è aprire il browser su un sito Web specifico, vedere l'API **[Browser](open-browser.md)** .

## <a name="get-started"></a>Attività iniziali

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

Questa operazione può essere combinata in una singola chiamata con `TryOpenAsync`, che controlla se il parametro può essere aperto e in caso affermativo lo apre.

```csharp
public class LauncherTest
{
    public async Task<bool> OpenRideShareAsync()
    {
        return await Launcher.TryOpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

## <a name="files"></a>File

Questa funzionalità consente a un'app di richiedere l'apertura e la visualizzazione di un file ad altre app. Xamarin.Essentials rileverà automaticamente il tipo di file (MIME) e richiederà l'apertura del file.

Di seguito è riportato un esempio di scrittura di testo su disco e di richiesta di apertura:

```csharp
var fn = "File.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Launcher.OpenAsync(new OpenFileRequest
{
    File = new ReadOnlyFile(file)
});
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
