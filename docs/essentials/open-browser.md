---
title: Apri Xamarin.Essentials Browser
description: La classe di Browser consente a un'applicazione aprire un collegamento web nel browser preferito sistema ottimizzato o browser esterno.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2a95d0b89b78ce8b0ddfdc94d86c284a6f8b2bbe
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2018
---
# <a name="xamarinessentials-browser"></a>Browser Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Browser** classe consente a un'applicazione aprire un collegamento web nel browser preferito sistema ottimizzato o browser esterno.

## <a name="using-browser"></a>Tramite Browser

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità del Browser funziona chiamando il `OpenAsync` metodo con il `Uri` e `BrowserLaunchType`.

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchType.SystemPreferred);
    }
}
```

## <a name="platform-implementation-specifics"></a>Informazioni specifiche sull'implementazione della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Il tipo di avvio determina come viene avviato nel browser:

## <a name="system-preferred"></a>Sistema preferito

[Chrome personalizzato schede](https://developer.chrome.com/multidevice/android/customtabs) verrà tentato di utilizzare l'Uri di caricare e mantenere la consapevolezza di navigazione.

## <a name="external"></a>Altre informazioni

Un `Intent` verrà utilizzato per richiedere l'Uri da aprire tramite il browser normale sistemi.

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Sistema preferito

[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) consente di caricare l'Uri e mantenere la consapevolezza di navigazione.

## <a name="external"></a>Altre informazioni

Lo standard `OpenUrl` nell'applicazione principale utilizzato per avviare il browser predefinito all'esterno dell'applicazione.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Il browser dell'utente predefinito verrà sempre avviato indipendentemente dal valore di `BrowserLaunchType`.

--------------

## <a name="api"></a>API

- [Codice di origine del browser](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Documentazione del browser API](xref:Xamarin.Essentials.Browser)
