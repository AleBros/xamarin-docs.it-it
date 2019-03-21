---
title: Compressione dei file in xamarin. IOS
description: Questo documento descrive come usare l'API libcompression in xamarin. IOS. Vengono illustrati deflazionando, ciò fa aumentare erroneamente, e i diversi algoritmi è supportato.
ms.prod: xamarin
ms.assetid: 94D05DAB-01E8-4C62-9CEF-9D6417EEA8EB
ms.technology: xamarin-ios
author: mandel-macaque
ms.author: mandel
ms.date: 03/04/2019
ms.openlocfilehash: f7a1df65047fd8040dd40e9f7f057d6bfe6dea61
ms.sourcegitcommit: 4c97f5d73be7eb2da153a85183be4258b6b11ca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58290926"
---
# <a name="file-compression-in-xamarinios"></a>Compressione dei file in xamarin. IOS

Xamarin App destinate a iOS 9.0 o macOS 10.11 (e versioni successive) possono usare la _Framework di compressione_ per comprimere (codifica) e decomprimere (decodifica) dei dati. Xamarin. IOS fornisce questo framework seguendo l'API di Stream. Il framework di compressione consente agli sviluppatori di interagire con la compressione e decompressione dei dati come se fossero flussi normali senza la necessità di usare callback o delegati.

Il framework di compressione offre supporto per gli algoritmi seguenti:

* LZ4
* LZ4 non elaborati
* Lzfse
* Lzma
* Zlib

Usando il framework di compressione consente agli sviluppatori di eseguire le operazioni di compressione senza librerie di terze parti o pacchetti NuGet. Ciò riduce le dipendenze esterne e assicura che le operazioni di compressione sono supportate in tutte le piattaforme (purché soddisfino i requisiti minimi del sistema operativo).

## <a name="general-file-decompression"></a>Decompressione di file generici

Il framework di compressione Usa un flusso di API in xamarin. IOS e xamarin. Mac. Questa API significa che per comprimere i dati, lo sviluppatore può utilizzare i modelli normali usati in altre API dei / o all'interno di .NET. L'esempio seguente illustra come decomprimere i dati con il framework di compressione, che è simile all'API nel `System.IO.Compression.DeflateStream` API:

```csharp
// sample zlib data
static byte [] compressed_data = { 0xf3, 0x48, 0xcd, 0xc9, 0xc9, 0xe7, 0x02, 0x00 };

using (var backing = new MemoryStream (compressed_data)) // compress data to read
using (var decompressing = new CompressionStream (backing, CompressionMode.Decompress, CompressionAlgorithm.Zlib)) // create decompression stream with the correct algorithm
using (var reader = new StreamReader (decompressing))
{
    // perform the required stream operations
    Console.WriteLine (reader.ReadLine ());
}
```

Il `CompressionStream` implementa la `IDisposable` interfaccia, come altro `System.IO.Streams`, in modo che gli sviluppatori devono assicurarsi che le risorse vengono liberate quando non sono più necessari.

## <a name="general-file-compression"></a>Compressione di file generici

L'API di compressione consente inoltre agli sviluppatori di comprimere i dati seguendo la stessa API. I dati possono essere compressi tramite uno degli algoritmi forniti indicati nella `CompressionAlgorithm` enumeratore.

```csharp
// sample method that copies the data from the source stream to the destination stream
static void CopyStream (Stream src, Stream dest)
{
    byte[] array = new byte[1024];
    int bytes_read;
    bytes_read = src.Read (array, 0, 1024);
    while (bytes_read != 0) {
        dest.Write (array, 0, bytes_read);
        bytes_read = src.Read (array, 0, 1024);
    }
}

static void CompressExample ()
{
    // create some sample data to compress
    byte [] data = new byte[100000];
    for (int i = 0; i < 100000; i++) {
        data[i] = (byte) i;
    }

    using (var dataStream = new MemoryStream (data)) // stream that contains the data to compress
    using (var backing = new MemoryStream ()) // stream in which the compress data will be written
    using (var compressing = new CompressionStream (backing, CompressionMode.Compress, CompressionAlgorithm.Zlib, true))
    {
        // copy the data to the compressing stream
        CopyStream (dataStream, compressing);
        // at this point, the CompressionStream contains all the data from the dataStream but compressed
        // using the zlib algorithm
    }
```

## <a name="async-support"></a>Supporto di Async

Il `CompressionStream` supporta tutte le operazioni asincrone che sono supportate dal `System.IO.DeflateStream`, il che significa che gli sviluppatori possono utilizzare la parola chiave async per eseguire le operazioni di compressione/decompressione senza bloccare il thread dell'interfaccia utente.