---
title: Archiviare e accedere ai dati in archiviazione di Azure daXamarin.Forms
description: Archiviazione di Azure è una soluzione di archiviazione cloud scalabile che può essere usata per archiviare dati non strutturati e strutturati. Questo articolo illustra come usare Xamarin.Forms per archiviare dati di testo e binari in archiviazione di Azure e come accedere ai dati.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4df14ef4d3eb72b92e4201e57103780801ca2d2f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84131027"
---
# <a name="store-and-access-data-in-azure-storage-from-xamarinforms"></a>Archiviare e accedere ai dati in archiviazione di Azure daXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)

_Archiviazione di Azure è una soluzione di archiviazione cloud scalabile che può essere usata per archiviare dati non strutturati e strutturati. Questo articolo illustra come usare Xamarin.Forms per archiviare dati di testo e binari in archiviazione di Azure e come accedere ai dati._

Archiviazione di Azure fornisce quattro servizi di archiviazione:

- Archiviazione BLOB. Un BLOB può essere costituito da dati di testo o binari, ad esempio backup, macchine virtuali, file multimediali o documenti.
- L'archiviazione tabelle è un archivio chiave-attributo NoSQL.
- L'archiviazione code è un servizio di messaggistica per l'elaborazione e la comunicazione del flusso di lavoro tra i servizi cloud.
- Archiviazione file offre l'archiviazione condivisa usando il protocollo SMB.

Sono disponibili due tipi di account di archiviazione:

- Un account di archiviazione per utilizzo generico consente di accedere ai servizi di archiviazione di Azure da un singolo account.
- Un account di archiviazione BLOB è un account di archiviazione specializzato per l'archiviazione dei BLOB. Questo tipo di account è consigliato quando è necessario archiviare solo i dati BLOB.

Questo articolo e l'applicazione di esempio associata illustrano il caricamento di file di immagine e di testo nell'archivio BLOB e il loro download. Viene inoltre illustrato il recupero di un elenco di file dall'archiviazione BLOB e l'eliminazione di file.

Per altre informazioni su archiviazione di Azure, vedere [Introduzione all'archiviazione](https://azure.microsoft.com/documentation/articles/storage-introduction/).

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

## <a name="introduction-to-blob-storage"></a>Introduzione all'archiviazione BLOB

L'archiviazione BLOB è costituita da tre componenti, illustrati nel diagramma seguente:

![](azure-storage-images/blob-storage.png "Blob Storage Concepts")

L'accesso ad archiviazione di Azure avviene tramite un account di archiviazione. Un account di archiviazione può contenere un numero illimitato di contenitori e un contenitore può archiviare un numero illimitato di BLOB, fino al limite di capacità dell'account di archiviazione.

Un BLOB è un file di qualsiasi tipo e dimensione. Archiviazione di Azure supporta tre tipi di BLOB diversi:

- I BLOB in blocchi sono ottimizzati per lo streaming e l'archiviazione di oggetti cloud e rappresentano una scelta ottimale per l'archiviazione di backup, file multimediali, documenti e così via. I BLOB in blocchi possono avere dimensioni fino a 195Gb.
- I BLOB di Accodamento sono simili ai BLOB in blocchi, ma sono ottimizzati per operazioni di Accodamento, ad esempio la registrazione. I BLOB di accodamento possono avere dimensioni fino a 195Gb.
- I BLOB di pagine sono ottimizzati per operazioni di lettura/scrittura frequenti e vengono in genere usati per archiviare le macchine virtuali e i relativi dischi. I BLOB di pagine possono avere dimensioni fino a 1 TB.

> [!NOTE]
> Si noti che gli account di archiviazione BLOB supportano i BLOB in blocchi e di aggiunta, ma non i BLOB di pagine.

Un BLOB viene caricato in archiviazione di Azure e scaricato da archiviazione di Azure come un flusso di byte. Di conseguenza, i file devono essere convertiti in un flusso di byte prima del caricamento e riconvertiti nella relativa rappresentazione originale dopo il download.

Ogni oggetto archiviato in archiviazione di Azure ha un indirizzo URL univoco. Il nome dell'account di archiviazione costituisce il sottodominio dell'indirizzo e la combinazione di sottodominio e nome di dominio costituisce un *endpoint* per l'account di archiviazione. Ad esempio, se l'account di archiviazione è denominato *mystorageaccount*, l'endpoint BLOB predefinito per l'account di archiviazione è `https://mystorageaccount.blob.core.windows.net` .

L'URL per accedere a un oggetto in un account di archiviazione viene formato aggiungendo la posizione dell'oggetto nell'account di archiviazione all'endpoint. Ad esempio, un indirizzo BLOB avrà il formato `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob` .

## <a name="setup"></a>Configurazione

Il processo per l'integrazione di un account di archiviazione di Azure in un' Xamarin.Forms applicazione è il seguente:

1. Creare un account di archiviazione. Per altre informazioni, vedere [creare un account di archiviazione](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account).
1. Aggiungere la [libreria client di archiviazione di Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) all' Xamarin.Forms applicazione.
1. Configurare la stringa di connessione di archiviazione. Per altre informazioni, vedere [connessione ad archiviazione di Azure](#connecting).
1. Aggiungere `using` direttive per gli `Microsoft.WindowsAzure.Storage` `Microsoft.WindowsAzure.Storage.Blob` spazi dei nomi e alle classi che accedono ad archiviazione di Azure.

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>Connessione ad Archiviazione di Azure

Ogni richiesta effettuata per le risorse dell'account di archiviazione deve essere autenticata. Mentre i BLOB possono essere configurati per supportare l'autenticazione anonima, esistono due approcci principali che un'applicazione può usare per l'autenticazione con un account di archiviazione:

- Chiave condivisa. Questo approccio usa il nome dell'account di archiviazione di Azure e la chiave dell'account per accedere ai servizi di archiviazione. Al momento della creazione, a un account di archiviazione vengono assegnate due chiavi private che possono essere usate per l'autenticazione con chiave condivisa.
- Firma di accesso condiviso. Si tratta di un token che può essere aggiunto a un URL che consente l'accesso delegato a una risorsa di archiviazione, con le autorizzazioni specificate, per il periodo di tempo in cui è valido.

È possibile specificare le stringhe di connessione che includono le informazioni di autenticazione necessarie per accedere alle risorse di archiviazione di Azure da un'applicazione. Inoltre, è possibile configurare una stringa di connessione per connettersi all'emulatore di archiviazione di Azure da Visual Studio.

> [!NOTE]
> Archiviazione di Azure supporta HTTP e HTTPS in una stringa di connessione. È tuttavia consigliabile utilizzare HTTPS.

### <a name="connecting-to-the-azure-storage-emulator"></a>Connessione all'emulatore di archiviazione di Azure

L'emulatore di archiviazione di Azure fornisce un ambiente locale che emula i servizi BLOB, di Accodamento e tabelle di Azure per scopi di sviluppo.

Per connettersi all'emulatore di archiviazione di Azure, è necessario usare la stringa di connessione seguente:

```csharp
UseDevelopmentStorage=true
```

Per altre informazioni sull'emulatore di archiviazione di Azure, vedere [usare l'emulatore di archiviazione di Azure per sviluppo e test](https://azure.microsoft.com/documentation/articles/storage-use-emulator/).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>Connessione ad archiviazione di Azure tramite una chiave condivisa

Per connettersi ad archiviazione di Azure con una chiave condivisa, è necessario usare il formato della stringa di connessione seguente:

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName`deve essere sostituito con il nome dell'account di archiviazione e `myAccountKey` deve essere sostituito con una delle due chiavi di accesso dell'account.

> [!NOTE]
> Quando si usa l'autenticazione con chiave condivisa, il nome dell'account e la chiave dell'account verranno distribuiti a ogni persona che usa l'applicazione, che fornirà l'accesso in lettura/scrittura completo all'account di archiviazione. Quindi, usare l'autenticazione con chiave condivisa solo a scopo di test e non distribuire mai le chiavi ad altri utenti.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>Connessione ad archiviazione di Azure tramite una firma di accesso condiviso

Per connettersi ad archiviazione di Azure con una firma di accesso condiviso, è necessario usare il formato della stringa di connessione seguente:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint`deve essere sostituito con l'URL dell'endpoint BLOB e `mySharedAccessSignature` deve essere sostituito con la firma di accesso condiviso. La firma di accesso condiviso fornisce il protocollo, l'endpoint del servizio e le credenziali per accedere alla risorsa.

> [!NOTE]
> L'autenticazione SAS è consigliata per le applicazioni di produzione. Tuttavia, in un'applicazione di produzione la firma di accesso condiviso deve essere recuperata da un servizio back-end su richiesta, anziché essere aggregata con l'applicazione.

Per altre informazioni sulle firme di accesso condiviso, vedere [uso delle firme di accesso condiviso (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="creating-a-container"></a>Creazione di un contenitore

Il `GetContainer` metodo viene usato per recuperare un riferimento a un contenitore denominato, che può quindi essere usato per recuperare i BLOB dal contenitore o per aggiungere BLOB al contenitore. L'esempio di codice seguente illustra il metodo `GetContainer`:

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

Il `CloudStorageAccount.Parse` metodo analizza una stringa di connessione e restituisce un' `CloudStorageAccount` istanza di che rappresenta l'account di archiviazione. Un' `CloudBlobClient` istanza di, che viene usata per recuperare contenitori e BLOB, viene quindi creata dal `CreateCloudBlobClient` metodo. Il `GetContainerReference` metodo recupera il contenitore specificato come `CloudBlobContainer` istanza di, prima che venga restituito al metodo chiamante. In questo esempio, il nome del contenitore è il `ContainerType` valore di enumerazione, convertito in una stringa di caratteri minuscoli.

> [!NOTE]
> I nomi dei contenitori devono essere minuscoli e devono iniziare con una lettera o un numero. Inoltre, possono contenere solo lettere, numeri e il carattere di trattino e devono avere una lunghezza compresa tra 3 e 63 caratteri.

Il `GetContainer` metodo viene richiamato come segue:

```csharp
var container = GetContainer(containerType);
```

L' `CloudBlobContainer` istanza può quindi essere usata per creare un contenitore, se non esiste già:

```csharp
await container.CreateIfNotExistsAsync();
```

Per impostazione predefinita, un contenitore appena creato è privato. Ciò significa che è necessario specificare una chiave di accesso alle risorse di archiviazione per recuperare i BLOB dal contenitore. Per informazioni sull'esecuzione di BLOB in un contenitore pubblico, vedere [creare un contenitore](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container).

## <a name="uploading-data-to-a-container"></a>Caricamento di dati in un contenitore

Il `UploadFileAsync` metodo viene usato per caricare un flusso di dati byte nell'archivio BLOB e viene illustrato nell'esempio di codice seguente:

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

Dopo il recupero di un riferimento a un contenitore, il metodo crea il contenitore se non esiste già. `Guid`Viene quindi creato un nuovo oggetto per fungere da nome univoco del BLOB e un riferimento a un blocco BLOB viene recuperato come `CloudBlockBlob` istanza. Il flusso di dati viene quindi caricato nel BLOB usando il `UploadFromStreamAsync` metodo, che crea il BLOB, se non esiste già, oppure lo sovrascrive se esiste.

Prima di poter caricare un file nell'archiviazione BLOB usando questo metodo, è necessario prima convertirlo in un flusso di byte. Questa operazione è illustrata nell'esempio di codice seguente:

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

I `text` dati vengono convertiti in una matrice di byte, che viene quindi sottoposta a wrapped come flusso passato al `UploadFileAsync` metodo.

## <a name="downloading-data-from-a-container"></a>Download dei dati da un contenitore

Il `GetFileAsync` metodo viene usato per scaricare i dati BLOB da archiviazione di Azure e viene illustrato nell'esempio di codice seguente:

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

Dopo il recupero di un riferimento al contenitore, il metodo recupera un riferimento al BLOB per i dati archiviati. Se il BLOB esiste, le relative proprietà vengono recuperate dal `FetchAttributesAsync` metodo. Viene creata una matrice di byte con le dimensioni corrette e il BLOB viene scaricato come una matrice di byte che viene restituita al metodo chiamante.

Dopo aver scaricato i dati di Byte BLOB, è necessario convertirli nella relativa rappresentazione originale. Questa operazione è illustrata nell'esempio di codice seguente:

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

La matrice di byte viene recuperata dall'archiviazione di Azure dal `GetFileAsync` metodo, prima che venga riconvertita in una stringa con codifica UTF8.

## <a name="listing-data-in-a-container"></a>Elenco di dati in un contenitore

Il `GetFilesListAsync` metodo viene usato per recuperare un elenco di BLOB archiviati in un contenitore e viene illustrato nell'esempio di codice seguente:

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

Dopo il recupero di un riferimento a un contenitore, il metodo usa il metodo del contenitore `ListBlobsSegmentedAsync` per recuperare i riferimenti ai BLOB all'interno del contenitore. I risultati restituiti dal `ListBlobsSegmentedAsync` metodo vengono enumerati quando l' `BlobContinuationToken` istanza non lo è `null` . Ogni BLOB viene sottomesso a cast dall'oggetto restituito `IListBlobItem` a un oggetto per `CloudBlockBlob` accedere alla `Name` proprietà del BLOB, prima che venga aggiunto un valore alla `allBlobsList` raccolta. Una volta che l' `BlobContinuationToken` istanza è `null` , è stato restituito l'ultimo nome di BLOB e l'esecuzione termina il ciclo.

## <a name="deleting-data-from-a-container"></a>Eliminazione di dati da un contenitore

Il `DeleteFileAsync` metodo viene usato per eliminare un BLOB da un contenitore e viene illustrato nell'esempio di codice seguente:

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

Dopo il recupero di un riferimento a un contenitore, il metodo recupera un riferimento al BLOB per il blob specificato. Il BLOB viene quindi eliminato con il `DeleteIfExistsAsync` metodo.

## <a name="related-links"></a>Collegamenti correlati

- [Archiviazione di Azure (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)
- [Introduzione all'archiviazione](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Come usare l'archiviazione BLOB da Xamarin](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [Uso delle firme di accesso condiviso](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Archiviazione di Windows Azure (NuGet)](https://www.nuget.org/packages/WindowsAzure.Storage/)
