---
title: Xamarin.EssentialsApri browser
description: La classe browser in Xamarin.Essentials consente a un'applicazione di aprire un collegamento Web nel browser preferito di sistema ottimizzato o nel browser esterno.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 997c6b66b5dba43eb440130f3f58d31a5a274815
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802239"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: Browser

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

Questo metodo restituisce il controllo dopo l'_avvio_ del browser e non necessariamente dopo la _chiusura_ del browser da parte dell'utente.  Il risultato `bool` indica l'esito positivo o negativo dell'avvio.

## <a name="customization"></a>Personalizzazione

Quando si usa il browser preferito del sistema, sono disponibili diverse opzioni di personalizzazione per iOS e Android. Tra queste c'è `TitleMode` (solo Android) e le opzioni dei colori preferiti per la `Toolbar` (iOS e Android) e i `Controls` (solo iOS).

Queste opzioni vengono specificate usando `BrowserLaunchOptions` quando si chiama `OpenAsync`.

```csharp
await Browser.OpenAsync(uri, new BrowserLaunchOptions
                {
                    LaunchMode = BrowserLaunchMode.SystemPreferred,
                    TitleMode = BrowserTitleMode.Show,
                    PreferredToolbarColor = Color.AliceBlue,
                    PreferredControlColor = Color.Violet
                });
```

![Opzioni del browser](images/browser-options.png)

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="android"></a>[Android](#tab/android)

La modalità di avvio determina il modo in cui viene avviato il browser:

## <a name="system-preferred"></a>Preferita dal sistema

Le [schede personalizzate](https://developer.chrome.com/multidevice/android/customtabs) verranno utilizzate per caricare l'URI e per tenere traccia della navigazione.

## <a name="external"></a>Esterno

Verrà usato un `Intent` per richiedere l'apertura dell'URI tramite il browser normale del sistema.

# <a name="ios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Preferita dal sistema

Viene usato [SFSafariViewController](xref:SafariServices.SFSafariViewController) per caricare l'URI e mantenere la consapevolezza di navigazione.

## <a name="external"></a>Esterno

Viene usato il metodo `OpenUrl` standard sull'applicazione principale per avviare il browser predefinito all'esterno dell'applicazione.

# <a name="uwp"></a>[UWP](#tab/uwp)

Verrà sempre avviato il browser predefinito dell'utente indipendentemente da `BrowserLaunchMode`.

--------------

## <a name="api"></a>API

- [Codice sorgente di Browser](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Browser)
- [Documentazione dell'API Browser](xref:Xamarin.Essentials.Browser)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Open-Browser-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
