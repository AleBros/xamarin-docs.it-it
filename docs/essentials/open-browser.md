---
title: 'Xamarin.Essentials: Apertura browser'
description: La classe Browser in Xamarin.Essentials consente a un'applicazione di aprire un collegamento Web nel browser preferito del sistema ottimizzato o in un browser esterno.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: ea2a10c11a77fcb2b3ce142d176522ebf0310725
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898876"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: Apertura browser

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
    public async Task<bool> OpenBrowser(Uri uri)
    {
        return await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

Questo metodo restituisce il controllo dopo l'_avvio_ del browser e non necessariamente dopo la _chiusura_ del browser da parte dell'utente.  Il risultato `bool` indica l'esito positivo o negativo dell'avvio.

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
