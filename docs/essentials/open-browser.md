---
title: 'Xamarin.Essentials: Apertura browser'
description: La classe Browser in Xamarin.Essentials consente a un'applicazione di aprire un collegamento Web nel browser preferito del sistema ottimizzato o in un browser esterno.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: fe8730ba6bc664269d79c550fb4e0abef7767fe0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765004"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: Browser

La classe **Browser** consente a un'applicazione di aprire un collegamento Web nel browser preferito del sistema ottimizzato o in un browser esterno.

## <a name="get-started"></a>Attività iniziali

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

# <a name="androidtabandroid"></a>[Android](#tab/android)

La modalità di avvio determina il modo in cui viene avviato il browser:

## <a name="system-preferred"></a>Preferita dal sistema

Verrà tentato l'uso delle [schede personalizzate di Chrome](https://developer.chrome.com/multidevice/android/customtabs) per caricare l'URI e per mantenere la consapevolezza di navigazione.

## <a name="external"></a>Altre informazioni

Verrà usato un `Intent` per richiedere l'apertura dell'URI tramite il browser normale del sistema.

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Preferita dal sistema

Viene usato [SFSafariViewController](xref:SafariServices.SFSafariViewController) per caricare l'URI e mantenere la consapevolezza di navigazione.

## <a name="external"></a>Altre informazioni

Viene usato il metodo `OpenUrl` standard sull'applicazione principale per avviare il browser predefinito all'esterno dell'applicazione.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Verrà sempre avviato il browser predefinito dell'utente indipendentemente da `BrowserLaunchMode`.

--------------

## <a name="api"></a>API

- [Codice sorgente di Browser](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Documentazione dell'API Browser](xref:Xamarin.Essentials.Browser)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Open-Browser-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
