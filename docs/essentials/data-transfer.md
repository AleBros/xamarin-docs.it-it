---
title: Trasferimento dati Xamarin.Essentials
description: La classe DataTransfer consente a un'applicazione di condivisione di dati, ad esempio testo e web collegamenti ad altre applicazioni nel dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b03ec1330aff1210350adf2600c63d7d84bc1125
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-data-transfer"></a>Trasferimento dati Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **DataTransfer** classe consente a un'applicazione di condivisione di dati, ad esempio testo e web collegamenti ad altre applicazioni nel dispositivo.

## <a name="using-data-transfer"></a>Uso di trasferimento di dati

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di trasferimento dei dati funziona chiamando il `RequestAsync` metodo con un payload di richiesta di dati che include informazioni da condividere ad altre applicazioni. Testo e l'Uri può essere combinato e ogni piattaforma gestirà il filtro basato sul contenuto.

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

Interfaccia utente di condividere all'applicazione esterna che viene visualizzata quando viene richiesto:

![Trasferimento dati](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>Differenze di piattaforma

| Piattaforma | Differenza |
| --- | --- |
| Android | Proprietà Subject viene utilizzata per l'oggetto desiderato di un messaggio. |
| iOS | Oggetto non utilizzato. |
| iOS | Titolo non utilizzato. |
| UWP | Titolo verrà nome dell'applicazione per impostazione predefinita se non impostato. |
| UWP | Oggetto non utilizzato. |

## <a name="api"></a>API

- [Codice sorgente di trasferimento dei dati](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Documentazione dell'API di trasferimento dei dati](xref:Xamarin.Essentials.DataTransfer)
