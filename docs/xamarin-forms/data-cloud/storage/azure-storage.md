---
title: L'archiviazione e l'accesso ai dati nell'archiviazione di Azure
description: Archiviazione di Azure è una soluzione di archiviazione cloud scalabili che può essere utilizzata per archiviare dati strutturati e non strutturati. In questo articolo viene illustrato come utilizzare xamarin. Forms per archiviare dati di testo e binari in archiviazione di Azure e come accedere ai dati.
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 63afeec81eff350b034e8dd3a13da52801937826
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="storing-and-accessing-data-in-azure-storage"></a>L'archiviazione e l'accesso ai dati nell'archiviazione di Azure

_Archiviazione di Azure è una soluzione di archiviazione cloud scalabili che può essere utilizzata per archiviare dati strutturati e non strutturati. In questo articolo viene illustrato come utilizzare xamarin. Forms per archiviare dati di testo e binari in archiviazione di Azure e come accedere ai dati._

## <a name="overview"></a>Panoramica

Archiviazione di Azure offre quattro servizi di archiviazione:

- Archiviazione BLOB. Un blob può essere testo o dati binari, ad esempio backup, le macchine virtuali, i file multimediali o documenti.
- Archiviazione tabelle è un archivio chiave-attributo NoSQL.
- L'archiviazione delle code è un servizio di messaggistica per la comunicazione tra servizi cloud e l'elaborazione del flusso di lavoro.
- Archiviazione di file consente l'archiviazione condivisa usando il protocollo SMB.

Esistono due tipi di account di archiviazione:

- Un account di archiviazione generico fornisce l'accesso ai servizi di archiviazione di Azure da un singolo account.
- Un account di archiviazione Blob è un account di archiviazione specializzata per l'archiviazione BLOB. Il tipo di account è consigliato quando è sufficiente archiviare dati blob.

In questo articolo e accompagna l'applicazione di esempio viene illustrato il caricamento di file immagine e testo per l'archiviazione blob e scaricarli. Inoltre, viene inoltre illustrato il recupero di un elenco di file dall'archiviazione blob e l'eliminazione di file.

Per ulteriori informazioni sull'archiviazione di Azure, vedere [Introduzione all'archiviazione](https://azure.microsoft.com/documentation/articles/storage-introduction/).

## <a name="introduction-to-blob-storage"></a>Introduzione all'archiviazione Blob

Archiviazione BLOB è costituito da tre componenti, che vengono visualizzati nel diagramma seguente:

![](azure-storage-images/blob-storage.png "Concetti di archiviazione BLOB")

Tutti gli accessi al servizio di archiviazione Azure sono tramite un account di archiviazione. Un account di archiviazione può contenere un numero illimitato di contenitori e un contenitore è possibile archiviare un numero illimitato di BLOB, fino al limite di capacità dell'account di archiviazione.

Un blob è un file di qualsiasi tipo e dimensioni. Archiviazione di Azure supporta tre tipi diversi di blob:

- BLOB in blocchi sono ottimizzati per il flusso e l'archiviazione di oggetti del cloud e rappresentano una buona scelta per l'archiviazione dei backup, i file multimediali, documenti e così via. BLOB in blocchi possono essere di dimensioni fino a 195Gb.
- Aggiungere i BLOB sono simili ai BLOB in blocchi, ma sono ottimizzati per le operazioni, ad esempio la registrazione di Accodamento. Aggiungere BLOB possono essere di dimensioni fino a 195Gb.
- BLOB di pagine sono ottimizzati per operazioni di lettura/scrittura frequenti e vengono in genere utilizzati per archiviare le macchine virtuali e dei dischi. BLOB di pagine possono essere di dimensioni fino a 1Tb.

> [!NOTE]
> Si noti che l'account di archiviazione blob supportano blocco e aggiungere oggetti BLOB, ma non i BLOB di pagine.

Un blob viene caricato in archiviazione di Azure e scaricato dall'archiviazione di Azure, come un flusso di byte. Di conseguenza, i file devono essere convertiti in un flusso di byte prima di caricare e back convertito nella relativa rappresentazione originale dopo il download.

Ogni oggetto che viene archiviato in archiviazione di Azure ha un indirizzo URL univoco. Il nome di account di archiviazione costituisce il sottodominio di tale indirizzo e la combinazione di moduli di nome di sottodominio e dominio un *endpoint* per l'account di archiviazione. Ad esempio, se l'account di archiviazione denominato *mystorageaccount*, l'endpoint blob predefinito per l'account di archiviazione è `https://mystorageaccount.blob.core.windows.net`.

L'URL per l'accesso a un oggetto in un account di archiviazione viene creato aggiungendo il percorso dell'oggetto nell'account di archiviazione per l'endpoint. Ad esempio, un indirizzo di blob avrà il formato `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob`.

## <a name="setup"></a>Configurazione

Il processo per l'integrazione di un account di archiviazione di Azure in un'applicazione di xamarin. Forms è come segue:

1. Creare un account di archiviazione. Per ulteriori informazioni, vedere [creare un account di archiviazione](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account).
1. Aggiungere il [Azure Storage Client Library](https://www.nuget.org/packages/WindowsAzure.Storage/) all'applicazione di xamarin. Forms.
1. Configurare la stringa di connessione di archiviazione. Per ulteriori informazioni, vedere [la connessione al servizio di archiviazione Azure](#connecting).
1. Aggiungere `using` direttive per il `Microsoft.WindowsAzure.Storage` e `Microsoft.WindowsAzure.Storage.Blob` gli spazi dei nomi per le classi che accederà ad archiviazione di Azure.

> [!NOTE]
> Mentre in questo esempio viene utilizzato un progetto di accesso condiviso, la libreria Client di archiviazione di Azure ora supporta anche utilizzata da un progetto libreria di classe portabile (PCL).

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>Connessione all'archiviazione di Azure

Ogni richiesta effettuata per le risorse di account di archiviazione deve essere autenticata. Mentre i BLOB possono essere configurati per supportare l'autenticazione anonima, esistono due approcci principali, che un'applicazione può utilizzare per l'autenticazione con un account di archiviazione:

- Chiave condivisa. Questo approccio utilizza l'archiviazione di Azure account nome account e la chiave ai servizi di archiviazione di accesso. Un account di archiviazione viene assegnato a due chiavi private durante la creazione che può essere utilizzato per l'autenticazione chiave condivisa.
- Firma di accesso condiviso. Si tratta di un token che può essere aggiunto a un URL che consente l'accesso delegato a una risorsa di archiviazione, con le autorizzazioni specifica per il periodo di tempo in cui è valido.

È possibile specificare le stringhe di connessione che includono le informazioni di autenticazione necessarie per accedere alle risorse di archiviazione di Azure da un'applicazione. Inoltre, una stringa di connessione può essere configurata per connettersi all'emulatore di archiviazione di Azure da Visual Studio.

> [!NOTE]
> Archiviazione di Azure supporta HTTP e HTTPS in una stringa di connessione. Tuttavia, è consigliabile utilizzare HTTPS.

### <a name="connecting-to-the-azure-storage-emulator"></a>Connessione all'emulatore di archiviazione di Azure

L'emulatore di archiviazione di Azure offre un ambiente locale che emula il blob di Azure, code e servizi di tabella per scopi di sviluppo.

La seguente stringa di connessione deve essere usata per connettersi all'emulatore di archiviazione di Azure:

```csharp
UseDevelopmentStorage=true
```

Per ulteriori informazioni sull'emulatore di archiviazione di Azure, vedere [Usa l'emulatore di archiviazione di Azure per sviluppo e test](https://azure.microsoft.com/documentation/articles/storage-use-emulator/).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>Connessione all'archiviazione di Azure mediante una chiave condivisa

Il seguente formato di stringa di connessione deve essere utilizzato per connettersi ad archiviazione di Azure con una chiave condivisa:

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` deve essere sostituito con il nome dell'account di archiviazione, e `myAccountKey` deve essere sostituito con uno dei due chiavi di accesso account.

> [!NOTE]
> Utilizza condivisa quando l'autenticazione con chiave, il nome dell'account e la chiave dell'account verrà distribuita a ogni persona che utilizza l'applicazione, che fornirà l'accesso in lettura/scrittura all'account di archiviazione. Pertanto, utilizzare l'autenticazione chiave condivisa solo a scopo di test e non distribuire mai le chiavi di altri utenti.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>Connessione all'archiviazione di Azure utilizzando una firma di accesso condiviso

Il seguente formato di stringa di connessione deve essere utilizzato per connettersi ad archiviazione di Azure con una firma di accesso condiviso:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` deve essere sostituito con l'URL dell'endpoint, blob e `mySharedAccessSignature` deve essere sostituito con la firma di accesso condiviso. La firma di accesso condiviso fornisce le credenziali per accedere alla risorsa, il protocollo e l'endpoint del servizio.

> [!NOTE]
> L'autenticazione SAS è consigliato per applicazioni di produzione. Tuttavia, in un'applicazione di produzione, le associazioni di sicurezza devono essere recuperate da un servizio di back-end su richiesta, anziché essere fornito con l'applicazione.

Per ulteriori informazioni sulle firme di accesso condiviso, vedere [tramite firme di accesso condiviso (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="creating-a-container"></a>Creazione di un contenitore

Il `GetContainer` metodo viene utilizzato per recuperare un riferimento a un contenitore denominato, che può quindi essere usato per recuperare i BLOB dal contenitore o per aggiungere il contenitore di BLOB. Nell'esempio di codice riportato di seguito viene illustrato il `GetContainer` metodo:

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

Il `CloudStorageAccount.Parse` metodo analizza una stringa di connessione e restituisce un `CloudStorageAccount` istanza che rappresenta l'account di archiviazione. A `CloudBlobClient` istanza, che viene utilizzato per recuperare i contenitori e BLOB, viene quindi creato eseguendo la `CreateCloudBlobClient` metodo. Il `GetContainerReference` che consente di recuperare il contenitore specificato come un `CloudBlobContainer` dell'istanza, prima che venga restituito al metodo di chiamata. In questo esempio, il nome del contenitore è il `ContainerType` valore di enumerazione, convertito in una stringa in caratteri minuscoli.

> [!NOTE]
> Nomi di contenitore devono essere minuscoli e devono iniziare con una lettera o un numero. Inoltre, può contenere solo lettere, numeri e trattini e deve essere compresa tra 3 e 63 caratteri.

Il `GetContainer` metodo viene richiamato, come segue:

```csharp
var container = GetContainer(containerType);
```

Il `CloudBlobContainer` istanza può quindi essere usata per creare un contenitore, se non esiste già:

```csharp
await container.CreateIfNotExistsAsync();
```

Per impostazione predefinita, un nuovo contenitore è privato. Ciò significa che è necessario specificare una chiave di accesso di archiviazione per recuperare i BLOB dal contenitore. Per informazioni sull'esecuzione di BLOB all'interno di un contenitore di pubblico, vedere [creare un contenitore](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container).

## <a name="uploading-data-to-a-container"></a>Caricamento dei dati di un contenitore

Il `UploadFileAsync` metodo viene utilizzato per caricare un flusso di byte di dati nell'archiviazione BLOB e viene illustrato nell'esempio di codice seguente:

```csharp
public static async Task<string> UploadFileAsync(ContainerType containerType, Stream stream)
{
  var container = GetContainer(containerType);
  await container.CreateIfNotExistsAsync();

  var name = Guid.NewGuid().ToString();
  var fileBlob = container.GetBlockBlobReference(name);
  await fileBlob.UploadFromStreamAsync(stream);

  return name;
}
```

Dopo aver recuperato un riferimento di contenitore, il metodo crea il contenitore se non esiste già. Un nuovo `Guid` viene quindi creato per agire come un nome univoco di blob, e un riferimento di blocchi del blob viene recuperato come un `CloudBlockBlob` istanza. Il flusso di dati viene quindi caricato per il blob utilizzando il `UploadFromStreamAsync` metodo, che crea il blob se non esiste già o lo sovrascrive se esiste.

Prima di un file può essere caricato per l'utilizzo di questo metodo di archiviazione blob, è necessario prima convertirlo in un flusso di byte. Come illustrato nell'esempio di codice seguente:

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

Il `text` dati viene convertiti in una matrice di byte, viene eseguito il wrapping come un flusso che viene passato per il `UploadFileAsync` metodo.

## <a name="downloading-data-from-a-container"></a>Il download dei dati da un contenitore

Il `GetFileAsync` metodo viene utilizzato per scaricare i dati blob da archiviazione di Azure e viene illustrato nell'esempio di codice seguente:

```csharp
public static async Task<byte[]> GetFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);

  var blob = container.GetBlobReference(name);
  if (await blob.ExistsAsync())
  {
    await blob.FetchAttributesAsync();
    byte[] blobBytes = new byte[blob.Properties.Length];

    await blob.DownloadToByteArrayAsync(blobBytes, 0);
    return blobBytes;
  }
  return null;
}
```

Dopo aver recuperato un riferimento di contenitore, il metodo recupera un riferimento di blob per i dati archiviati. Se il blob esiste, le relative proprietà vengono recuperate dal `FetchAttributesAsync` metodo. Viene creata una matrice di byte di dimensioni corrette, e il blob viene scaricato come una matrice di byte restituito al metodo di chiamata.

Dopo aver scaricato i dati di byte del blob, è necessario convertirlo nella relativa rappresentazione originale. Come illustrato nell'esempio di codice seguente:

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

Matrice di byte viene recuperata dall'archiviazione di Azure per il `GetFileAsync` (metodo), prima di essere convertita a un UTF8 in una stringa codificata.

## <a name="listing-data-in-a-container"></a>Elenco di dati in un contenitore

Il `GetFilesListAsync` metodo viene utilizzato per recuperare un elenco di BLOB archiviati in un contenitore e viene illustrato nell'esempio di codice seguente:

```csharp
public static async Task<IList<string>> GetFilesListAsync(ContainerType containerType)
{
  var container = GetContainer(containerType);

  var allBlobsList = new List<string>();
  BlobContinuationToken token = null;

  do
  {
    var result = await container.ListBlobsSegmentedAsync(token);
    if (result.Results.Count() > 0)
    {
      var blobs = result.Results.Cast<CloudBlockBlob>().Select(b => b.Name);
      allBlobsList.AddRange(blobs);
    }
    token = result.ContinuationToken;
  } while (token != null);

  return allBlobsList;
}
```

Dopo aver recuperato un riferimento di contenitore, il metodo utilizza il contenitore `ListBlobsSegmentedAsync` metodo per recuperare i riferimenti ai BLOB all'interno del contenitore. I risultati restituiti dal `ListBlobsSegmentedAsync` metodo vengono enumerate durante la `BlobContinuationToken` istanza non è `null`. Ogni blob viene eseguito il cast da restituito `IListBlobItem` per un `CloudBlockBlob` Access ordine il `Name` viene aggiunta una proprietà del blob, prima del valore di `allBlobsList` insieme. Una volta il `BlobContinuationToken` istanza `null`ha restituito il cognome di blob ed esecuzione di uscire dal ciclo.

## <a name="deleting-data-from-a-container"></a>Eliminazione di dati da un contenitore

Il `DeleteFileAsync` metodo consente di eliminare un blob da un contenitore e viene visualizzato nell'esempio di codice seguente:

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

Dopo aver recuperato un riferimento di contenitore, il metodo recupera un riferimento di blob per il blob specificato. Il blob viene quindi eliminato con il `DeleteIfExistsAsync` metodo.

## <a name="summary"></a>Riepilogo

In questo articolo è illustrato come utilizzare xamarin. Forms per archiviare dati di testo e binari in archiviazione di Azure e come accedere ai dati. Archiviazione di Azure è una soluzione di archiviazione cloud scalabili che può essere utilizzata per l'archiviazione dei dati, strutturati e non.


## <a name="related-links"></a>Collegamenti correlati

- [Archiviazione di Azure (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)
- [Introduzione all'archiviazione](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Come usare l'archiviazione di Blob da Xamarin](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [Utilizzo di firme di accesso condiviso (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Windows Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/)
