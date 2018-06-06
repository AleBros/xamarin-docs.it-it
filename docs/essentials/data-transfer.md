---
title: 'Xamarin.Essentials: Trasferimento dati'
description: La classe DataTransfer in Xamarin.Essentials consente a un'applicazione condividere i dati, ad esempio testo e web collegamenti ad altre applicazioni nel dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 69d429b1cdbbbd6dbb53e3cefa89695666494ba7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782385"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: Trasferimento dati

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
