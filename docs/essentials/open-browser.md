---
title: Apri Xamarin.Essentials Browser
description: La classe di Browser consente a un'applicazione aprire un collegamento web nel browser preferito sistema ottimizzato o browser esterno.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: e8e1a6973e7928032b2aa8669d36325b93671624
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
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
        await Browser.RequestAsync(uri, BrowserLaunchType.SystemPreferred);
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

- [Codice di origine del browser](https://github.com/xamarin/Essentials/tree/master/Essentials/Browser)
- [Documentazione del browser API](xref:Xamarin.Essentials.Browser)
