---
title: 'Xamarin.Essentials: Condividi'
description: La classe Share in Xamarin.Essentials consente a un'applicazione di condividere dati, ad esempio testo e collegamenti Web, con altre applicazioni nel dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.custom: video
ms.openlocfilehash: b889573c0cefbd692b1e879c1612a143d4bb7c99
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120755"
---
# <a name="xamarinessentials-share"></a>Xamarin.Essentials: Condividi

La classe **Share** consente a un'applicazione di condividere dati, ad esempio testo e collegamenti Web, con altre applicazioni nel dispositivo.

## <a name="get-started"></a>Attività iniziali

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

![Condividi](images/share.png)

## <a name="files"></a>File

Questa funzionalità consente a un'app di condividere file con altre applicazioni nel dispositivo. Xamarin.Essentials rileva automaticamente il tipo di file (MIME) e richiede la condivisione. È possibile che ogni piattaforma supporti solo estensioni di file specifiche.

Ecco un esempio di scrittura di testo su disco e condivisione con altre app:

```csharp
var fn =  "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file)
});
```

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

- La proprietà `Subject` viene usata per l'oggetto desiderato di un messaggio.

# <a name="iostabios"></a>[iOS](#tab/ios)

- `Subject` non viene usata.
- `Title` non viene usata.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- Se non impostata, `Title` userà il nome dell'applicazione per impostazione predefinita.
- `Subject` non viene usata.

-----


## <a name="api"></a>API

- [Codice sorgente di Share](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Share)
- [Documentazione dell'API Share](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
