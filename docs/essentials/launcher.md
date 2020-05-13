---
title: 'Xamarin.Essentials: Utilità di avvio'
description: La classe Launcher in Xamarin.Essentials consente a un'applicazione di aprire un URI dal sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: f84d3bf379562c8ddd96a32dfb9f15b03b0a6827
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149793"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Launcher

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

### <a name="additional-platform-setup"></a>Configurazione aggiuntiva della piattaforma

# <a name="android"></a>[Android](#tab/android)

Nessuna configurazione aggiuntiva.

# <a name="ios"></a>[iOS](#tab/ios)

In iOS 9 e versioni successive Apple applica gli schemi per cui un'applicazione può eseguire query. Per specificare quali schemi si desidera utilizzare, è necessario specificare `LSApplicationQueriesSchemes` nel `Info.plist` file.

```
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>lyft</string>  
    <string>fb</string>
</array>
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Nessuna configurazione aggiuntiva.

-----

## <a name="files"></a>File

Questa funzionalità consente a un'app di richiedere l'apertura e la visualizzazione di un file ad altre app. Xamarin.Essentials rileverà automaticamente il tipo di file (MIME) e richiederà l'apertura del file.

Di seguito è riportato un esempio di scrittura di testo su disco e richiesta di apertura:

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

# <a name="android"></a>[Android](#tab/android)

L'attività restituita da `CanOpenAsync` viene completata immediatamente.

# <a name="ios"></a>[iOS](#tab/ios)

Se l'applicazione di destinazione in questo dispositivo non è mai stata aperta da `OpenAsync` dall'applicazione in precedenza, iOS richiederà all'utente una volta di consentire all'app di aprirla.

L'attività restituita da `CanOpenAsync` viene completata immediatamente.

Altre informazioni sull'implementazione di iOS sono disponibili [qui](xref:UIKit.UIApplication.CanOpenUrl*)

# <a name="uwp"></a>[UWP](#tab/uwp)

Non esistono differenze per questa piattaforma.

-----

## <a name="api"></a>API

- [Codice sorgente di Launcher](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentazione dell'API Launcher](xref:Xamarin.Essentials.Launcher)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Launcher-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
