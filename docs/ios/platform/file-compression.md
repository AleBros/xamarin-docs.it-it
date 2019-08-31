---
title: Compressione di file in Novell. iOS
description: Questo documento descrive come usare l'API libcompression in Novell. iOS. Vengono illustrati il Deflating, il gonfiaggio e i diversi algoritmi supportati.
ms.prod: xamarin
ms.assetid: 94D05DAB-01E8-4C62-9CEF-9D6417EEA8EB
ms.technology: xamarin-ios
author: mandel-macaque
ms.author: mandel
ms.date: 03/04/2019
ms.openlocfilehash: bcc63aa4e1926f5502d571bf47c83b0c8ea7e429
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199517"
---
# <a name="file-compression-in-xamarinios"></a>Compressione di file in Novell. iOS

Le app Novell destinate a iOS 9,0 o macOS 10,11 (e versioni successive) possono usare il _Framework di compressione_ per comprimere (codificare) e decomprimere (decodificare) i dati. Novell. iOS fornisce questo framework dopo l'API Stream. Il Framework di compressione consente agli sviluppatori di interagire con i dati compressi e decompressi come se fossero flussi normali senza la necessità di utilizzare callback o delegati.

Il Framework di compressione fornisce supporto per gli algoritmi seguenti:

* LZ4
* LZ4 RAW
* Lzfse
* LZMA
* Zlib

L'uso del Framework di compressione consente agli sviluppatori di eseguire operazioni di compressione senza librerie o NuGet di terze parti. In questo modo si riducono le dipendenze esterne e si garantisce che le operazioni di compressione siano supportate in tutte le piattaforme (purché soddisfino i requisiti minimi del sistema operativo).

## <a name="general-file-decompression"></a>Decompressione generale dei file

Il Framework di compressione usa un'API di flusso in Novell. iOS e Novell. Mac. Questa API significa che per comprimere i dati, lo sviluppatore può usare i modelli normali usati in altre API di i/o in .NET. Nell'esempio seguente viene illustrato come decomprimere i dati con il Framework di compressione, che è simile all'API `System.IO.Compression.DeflateStream` disponibile nell'API:

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

Implementa l' `IDisposable` interfaccia, come altre `System.IO.Streams`, per consentire agli sviluppatori di garantire che le risorse vengano liberate una volta che non sono più necessarie. `CompressionStream`

## <a name="general-file-compression"></a>Compressione file generale

L'API di compressione consente inoltre agli sviluppatori di comprimere i dati dopo la stessa API. I dati possono essere compressi utilizzando uno degli algoritmi specificati indicati nell' `CompressionAlgorithm` enumeratore.

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

Supporta tutte le operazioni asincrone supportate `System.IO.DeflateStream`da, il che significa che gli sviluppatori possono usare la parola chiave async per eseguire le operazioni di compressione/decompressione senza bloccare il thread dell'interfaccia utente. `CompressionStream`
