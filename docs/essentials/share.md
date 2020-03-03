---
title: 'Xamarin.Essentials: Condividi'
description: La classe Share in Xamarin.Essentials consente a un'applicazione di condividere dati, ad esempio testo e collegamenti Web, con altre applicazioni nel dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.custom: video
ms.openlocfilehash: f964967dce0dbb7e49d52a7f865b0fe8a9957bbe
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545207"
---
# <a name="xamarinessentials-share"></a>Xamarin.Essentials: Condividi

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

## <a name="presentation-location"></a>Posizione della presentazione

Quando si richiede una condivisione in iPadOS è possibile presentarla in un controllo popup. È possibile specificare la posizione usando la proprietà `PresentationSourceBounds`:

```csharp
await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file),
    PresentationSourceBounds = DeviceInfo.Platform== DevicePlatform.iOS && DeviceInfo.Idiom == DeviceIdiom.Tablet
                            ? new System.Drawing.Rectangle(0, 20, 0, 0)
                            : System.Drawing.Rectangle.Empty
});
```

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="android"></a>[Android](#tab/android)

- La proprietà `Subject` viene usata per l'oggetto desiderato di un messaggio.

# <a name="ios"></a>[iOS](#tab/ios)

- `Subject` non viene usata.
- `Title` non viene usata.

# <a name="uwp"></a>[UWP](#tab/uwp)

- Se non impostata, `Title` userà il nome dell'applicazione per impostazione predefinita.
- `Subject` non viene usata.

-----

## <a name="api"></a>API

- [Codice sorgente di Share](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Share)
- [Documentazione dell'API Share](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
