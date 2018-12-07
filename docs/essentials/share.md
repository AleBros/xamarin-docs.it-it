---
title: 'Xamarin.Essentials: Share'
description: La classe Share in Xamarin.Essentials consente a un'applicazione di condividere dati, ad esempio testo e collegamenti Web, con altre applicazioni nel dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 6fd3fd90d1e2ada225dafdd855f8903688677660
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899374"
---
# <a name="xamarinessentials-share"></a>Xamarin.Essentials: Share

La classe **Share** consente a un'applicazione di condividere dati, ad esempio testo e collegamenti Web, con altre applicazioni nel dispositivo.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-share"></a>Uso di Share

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Share opera chiamando il metodo `RequestAsync` con un payload di richiesta dati che include le informazioni da condividere con le altre applicazioni. È possibile combinare testo e URI e ogni piattaforma gestirà i filtri in base al contenuto.

```csharp

public class ShareTest
{
    public async Task ShareText(string text)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

Interfaccia utente per la condivisione nell'applicazione esterna visualizzata quando viene effettuata la richiesta:

![Condividi](share-images/share.png)

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

* La proprietà `Subject` viene usata per l'oggetto desiderato di un messaggio.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` non viene usata.
* `Title` non viene usata.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* Se non impostata, `Title` userà il nome dell'applicazione per impostazione predefinita.
* `Subject` non viene usata.

-----

## <a name="api"></a>API

- [Codice sorgente di Share](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Share)
- [Documentazione dell'API Share](xref:Xamarin.Essentials.Share)
