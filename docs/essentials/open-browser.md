---
title: 'Xamarin.Essentials: Apertura browser'
description: La classe Browser in Xamarin.Essentials consente a un'applicazione di aprire un collegamento Web nel browser preferito del sistema ottimizzato o in un browser esterno.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a68837ac4447dabcf52a1d1b27913adf80b4cbd7
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675393"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: Apertura browser

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **Browser** consente a un'applicazione di aprire un collegamento Web nel browser preferito del sistema ottimizzato o in un browser esterno.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-browser"></a>Uso di Browser

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Browser opera chiamando il metodo `OpenAsync` con `Uri` e `BrowserLaunchMode`.

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

La modalità di avvio determina il modo in cui viene avviato il browser:

## <a name="system-preferred"></a>Preferita dal sistema

Verrà tentato l'uso delle [schede personalizzate di Chrome](https://developer.chrome.com/multidevice/android/customtabs) per caricare l'URI e per mantenere la consapevolezza di navigazione.

## <a name="external"></a>Altre informazioni

Verrà usato un `Intent` per richiedere l'apertura dell'URI tramite il browser normale del sistema.

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Preferita dal sistema

Viene usato [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) per caricare l'URI e mantenere la consapevolezza di navigazione.

## <a name="external"></a>Altre informazioni

Viene usato il metodo `OpenUrl` standard sull'applicazione principale per avviare il browser predefinito all'esterno dell'applicazione.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Verrà sempre avviato il browser predefinito dell'utente indipendentemente da `BrowserLaunchMode`.

--------------

## <a name="api"></a>API

- [Codice sorgente di Browser](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Documentazione dell'API Browser](xref:Xamarin.Essentials.Browser)
