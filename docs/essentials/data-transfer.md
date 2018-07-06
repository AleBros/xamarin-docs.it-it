---
title: 'Xamarin.Essentials: Trasferimento dati'
description: La classe DataTransfer in Xamarin.Essentials consente a un'applicazione condividere i dati, ad esempio testo e web collegamenti ad altre applicazioni nel dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c1ed298e1317d0a3f78f4dbd9fc89a2b01c6958c
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855110"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: Trasferimento dati

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **DataTransfer** classe consente a un'applicazione condividere i dati, ad esempio testo e web collegamenti ad altre applicazioni nel dispositivo.

## <a name="using-data-transfer"></a>Utilizzare il trasferimento di dati

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di trasferimento dei dati funziona chiamando il `RequestAsync` metodo con un payload di richiesta di dati che include le informazioni da condividere ad altre applicazioni. È possibile combinare testo e l'Uri e ogni piattaforma gestirà il filtro basato sul contenuto.

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

Interfaccia utente per condividere all'applicazione esterna che viene visualizzato quando viene richiesto:

![Trasferimento dati](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `Subject` proprietà viene utilizzata per l'oggetto desiderato di un messaggio.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` non utilizzato.
* `Title` non utilizzato. 

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

* `Title` sarà il nome dell'applicazione per impostazione predefinita se non impostato.
* `Subject` non utilizzato.

-----

## <a name="api"></a>API

- [Codice sorgente di trasferimento dei dati](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Documentazione dell'API di trasferimento dei dati](xref:Xamarin.Essentials.DataTransfer)
