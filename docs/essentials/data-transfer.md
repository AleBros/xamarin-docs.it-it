---
title: 'Xamarin.Essentials: Data Transfer'
description: La classe DataTransfer in Xamarin.Essentials consente a un'applicazione di condividere dati, ad esempio testo e collegamenti Web, con altre applicazioni nel dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 179d4327aa768e7aa2c81dbbffd694d078327400
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674834"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: Data Transfer

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **DataTransfer** consente a un'applicazione di condividere dati, ad esempio testo e collegamenti Web, con altre applicazioni nel dispositivo.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-data-transfer"></a>Uso del trasferimento dei dati

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Data Transfer opera chiamando il metodo `RequestAsync` con un payload di richiesta dati che include le informazioni da condividere con le altre applicazioni. È possibile combinare testo e URI e ogni piattaforma gestirà i filtri in base al contenuto.

```csharp

public class DataTransferTest
{
    public async Task ShareText(string text)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

Interfaccia utente per la condivisione nell'applicazione esterna visualizzata quando viene effettuata la richiesta:

![Trasferimento dati](data-transfer-images/data-transfer.png)

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

- [Codice sorgente di Data Transfer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Documentazione dell'API Data Transfer](xref:Xamarin.Essentials.DataTransfer)
