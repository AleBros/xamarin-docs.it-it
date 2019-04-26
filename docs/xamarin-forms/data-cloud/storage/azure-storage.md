---
title: L'archiviazione e accesso ai dati in archiviazione di Azure
description: Archiviazione di Azure è una soluzione di archiviazione cloud scalabile che può essere utilizzata per archiviare i dati non strutturati e strutturati. Questo articolo illustra come usare xamarin. Forms per archiviare dati di testo e binari in archiviazione di Azure e come accedere ai dati.
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/28/2018
ms.openlocfilehash: 4ecffa0902d186b659e7df07dbcf17053e29c818
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61319941"
---
# <a name="storing-and-accessing-data-in-azure-storage"></a>L'archiviazione e accesso ai dati in archiviazione di Azure

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)

_Archiviazione di Azure è una soluzione di archiviazione cloud scalabile che può essere utilizzata per archiviare i dati non strutturati e strutturati. Questo articolo illustra come usare xamarin. Forms per archiviare dati di testo e binari in archiviazione di Azure e come accedere ai dati._

Archiviazione di Azure offre quattro servizi di archiviazione:

- Archivio BLOB. Un blob può essere testo o dati binari, ad esempio backup, le macchine virtuali, i file multimediali o documenti.
- Archiviazione tabelle è un archivio chiave-attributo NoSQL.
- Archiviazione code è un servizio di messaggistica per l'elaborazione del flusso di lavoro e la comunicazione tra servizi cloud.
- Archiviazione file offre l'archiviazione condivisa usando il protocollo SMB.

Esistono due tipi di account di archiviazione:

- Un account di archiviazione per utilizzo generico fornisce l'accesso ai servizi di archiviazione di Azure da un singolo account.
- Un account di archiviazione Blob è un account di archiviazione specializzato per l'archiviazione BLOB. Questo tipo di account è consigliato quando è sufficiente archiviare dati blob.

Questo articolo e applicazione di esempio di accompagnamento di seguito viene illustrato il caricamento di file immagine e testo per l'archiviazione blob e scaricarli. Inoltre, viene inoltre illustrato il recupero di un elenco di file dall'archiviazione blob e l'eliminazione di file.

Per altre informazioni sull'archiviazione di Azure, vedere [Introduzione all'archiviazione](https://azure.microsoft.com/documentation/articles/storage-introduction/).

## <a name="introduction-to-blob-storage"></a>Introduzione all'archiviazione Blob

Archiviazione BLOB è costituita da tre componenti, che vengono visualizzati nel diagramma seguente:

![](azure-storage-images/blob-storage.png "Concetti relativi all'archiviazione BLOB")

Tutti gli accessi ad archiviazione di Azure consiste nell'utilizzare un account di archiviazione. Un account di archiviazione può contenere un numero illimitato di contenitori, e un contenitore può archiviare un numero illimitato di BLOB, fino al limite di capacità dell'account di archiviazione.

Un blob è un file di qualsiasi tipo e dimensione. Archiviazione di Azure supporta tre diversi tipi di blob:

- I BLOB in blocchi sono ottimizzati per lo streaming e l'archiviazione di oggetti cloud e sono un'ottima scelta per l'archiviazione dei backup, i file multimediali, documenti e così via. I BLOB in blocchi possono essere di dimensioni fino a 195Gb.
- Accodare BLOB sono simili ai BLOB in blocchi, ma sono ottimizzati per operazioni di accodamento, ad esempio la registrazione. Aggiungere i BLOB possono essere di dimensioni fino a 195Gb.
- I BLOB di pagine sono ottimizzati per le operazioni di lettura/scrittura frequenti e vengono in genere usati per archiviare le macchine virtuali e i relativi dischi. I BLOB di pagine possono essere fino a 1Tb di dimensioni.

> [!NOTE]
> Si noti che gli account di archiviazione blob supportano blocco e accodare BLOB, ma non i BLOB di pagine.

Un blob viene caricato in archiviazione di Azure e scaricato da archiviazione di Azure, come un flusso di byte. Di conseguenza, i file devono essere convertiti in un flusso di byte prima del caricamento e convertito nuovamente alla relativa rappresentazione originale dopo il download.

Tutti gli oggetti archiviati in archiviazione di Azure ha un indirizzo URL univoco. Il nome di account di archiviazione costituisce il sottodominio dell'indirizzo e la combinazione dei formati di nome di dominio e sottodominio un' *endpoint* dell'account di archiviazione. Ad esempio, se l'account di archiviazione è denominato *mystorageaccount*, l'endpoint di blob predefinito per l'account di archiviazione è `https://mystorageaccount.blob.core.windows.net`.

L'URL per l'accesso a un oggetto in un account di archiviazione viene formato aggiungendo la posizione dell'oggetto nell'account di archiviazione per l'endpoint. Ad esempio, un indirizzo blob avrà il formato `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob`.

## <a name="setup"></a>Configurazione

Il processo per l'integrazione di un account di archiviazione di Azure in un'applicazione xamarin. Forms è come segue:

1. Creare un account di archiviazione. Per altre informazioni, vedere [creare un account di archiviazione](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account).
1. Aggiungere il [Azure Storage Client Library](https://www.nuget.org/packages/WindowsAzure.Storage/) all'applicazione xamarin. Forms.
1. Configurare la stringa di connessione di archiviazione. Per altre informazioni, vedere [ci si connette ad archiviazione di Azure](#connecting).
1. Aggiungere `using` direttive per il `Microsoft.WindowsAzure.Storage` e `Microsoft.WindowsAzure.Storage.Blob` spazi dei nomi per le classi che avrà accesso archiviazione di Azure.

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>La connessione ad archiviazione di Azure

Ogni richiesta effettuata per le risorse di account di archiviazione deve essere autenticata. Mentre i BLOB possono essere configurati per supportare l'autenticazione anonima, esistono due approcci principali che un'applicazione può utilizzare per l'autenticazione con un account di archiviazione:

- Chiave condivisa. Questo approccio Usa l'archiviazione di Azure account nome e la chiave per accedere ai servizi di archiviazione. Un account di archiviazione è assegnato due chiavi private durante la creazione che può essere utilizzato per l'autenticazione chiave condivisa.
- Firma di accesso condiviso. Si tratta di un token che può essere aggiunto a un URL che consente l'accesso delegato a una risorsa di archiviazione, con le autorizzazioni specificate e per il periodo di tempo in cui è valido.

È possibile specificare le stringhe di connessione che includono le informazioni di autenticazione necessarie per accedere alle risorse di archiviazione di Azure da un'applicazione. Inoltre, una stringa di connessione può essere configurata per connettersi all'emulatore di archiviazione di Azure da Visual Studio.

> [!NOTE]
> Archiviazione di Azure supporta HTTP e HTTPS in una stringa di connessione. Tuttavia, è consigliabile usare HTTPS.

### <a name="connecting-to-the-azure-storage-emulator"></a>La connessione all'emulatore di archiviazione di Azure

L'emulatore di archiviazione di Azure fornisce un ambiente locale che emula il blob di Azure, di accodamento e servizi di tabelle per scopi di sviluppo.

Usare la seguente stringa di connessione per connettersi all'emulatore di archiviazione di Azure:

```csharp
UseDevelopmentStorage=true
```

Per altre informazioni sull'emulatore di archiviazione di Azure, vedere [usare l'emulatore di archiviazione di Azure per sviluppo e test](https://azure.microsoft.com/documentation/articles/storage-use-emulator/).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>La connessione ad archiviazione di Azure usando una chiave condivisa

Il seguente formato di stringa di connessione deve essere utilizzato per connettersi ad archiviazione di Azure con una chiave condivisa:

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` deve essere sostituito con il nome dell'account di archiviazione, e `myAccountKey` deve essere sostituito con una delle due chiavi di accesso dell'account.

> [!NOTE]
> Quando utilizzo condiviso l'autenticazione con chiave, il nome dell'account e chiave dell'account verrà distribuito a ogni persona che usa l'applicazione, in modo da fornire accesso completo in lettura/scrittura all'account di archiviazione. Pertanto, utilizzare l'autenticazione chiave condivisa solo a scopo di test e non distribuire mai le chiavi di altri utenti.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>La connessione ad archiviazione di Azure usando una firma di accesso condiviso

Il seguente formato di stringa di connessione deve essere utilizzato per connettersi ad archiviazione di Azure con firma di accesso condiviso:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` deve essere sostituito con l'URL dell'endpoint blob, e `mySharedAccessSignature` deve essere sostituito con la firma di accesso condiviso. La firma di accesso condiviso fornisce il protocollo, l'endpoint del servizio e le credenziali per accedere alla risorsa.

> [!NOTE]
> L'autenticazione di firma di accesso condiviso è consigliato per le applicazioni di produzione. Tuttavia, in un'applicazione di produzione la firma di accesso condiviso deve essere recuperato da un back-end del servizio on demand, anziché essere unite in bundle con l'applicazione.

Per altre informazioni sulle firme di accesso condiviso, vedere [usando le firme di accesso condiviso (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="creating-a-container"></a>Creazione di un contenitore

Il `GetContainer` metodo viene utilizzato per recuperare un riferimento a un contenitore denominato, che quindi può essere utilizzato per recuperare i BLOB dal contenitore o per aggiungere i BLOB nel contenitore. Nell'esempio di codice riportato di seguito viene illustrato il `GetContainer` metodo:

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

Il `CloudStorageAccount.Parse` metodo analizza una stringa di connessione e restituisce un `CloudStorageAccount` istanza che rappresenta l'account di archiviazione. Oggetto `CloudBlobClient` istanza, che consente di recuperare contenitori e BLOB, viene quindi creato eseguendo la `CreateCloudBlobClient` (metodo). Il `GetContainerReference` che consente di recuperare il contenitore specificato come un `CloudBlobContainer` dell'istanza, prima che venga restituito al metodo di chiamata. In questo esempio, il nome del contenitore è il `ContainerType` valore di enumerazione, convertito in stringa di caratteri minuscoli.

> [!NOTE]
> I nomi dei contenitori devono essere minuscole e deve iniziare con una lettera o un numero. Inoltre, può contenere solo lettere, numeri e trattini e deve essere compresa tra 3 e 63 caratteri.

Il `GetContainer` metodo viene richiamato come indicato di seguito:

```csharp
var container = GetContainer(containerType);
```

Il `CloudBlobContainer` istanza è quindi utilizzabile per creare un contenitore, se non esiste già:

```csharp
await container.CreateIfNotExistsAsync();
```

Per impostazione predefinita, un nuovo contenitore è privato. Ciò significa che è necessario specificare una chiave di accesso di archiviazione per recuperare i BLOB dal contenitore. Per informazioni su come rendere i BLOB all'interno di un contenitore pubblico, vedere [creare un contenitore](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container).

## <a name="uploading-data-to-a-container"></a>Caricamento dei dati in un contenitore

Il `UploadFileAsync` metodo viene usato per caricare un flusso di byte di dati nell'archiviazione BLOB e viene illustrato nell'esempio di codice seguente:

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

Dopo aver recuperato un riferimento al contenitore, il metodo crea il contenitore se non esiste già. Una nuova `Guid` viene quindi creato per agire come un nome blob univoco, e viene recuperato un riferimento al blocco di blob come un `CloudBlockBlob` istanza. Il flusso di dati viene quindi caricato per il blob usando il `UploadFromStreamAsync` metodo, che crea il blob se non esiste o lo sovrascrive se esiste.

Prima di essere caricato un file BLOB di archiviazione con questo metodo, deve prima essere convertito in un flusso di byte. Ciò è dimostrato nell'esempio di codice seguente:

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

Il `text` dei dati viene convertiti in una matrice di byte, che viene quindi eseguito il wrapping come un flusso che viene passato al `UploadFileAsync` (metodo).

## <a name="downloading-data-from-a-container"></a>Download dei dati da un contenitore

Il `GetFileAsync` metodo viene usato per scaricare i dati blob da archiviazione di Azure e viene illustrato nell'esempio di codice seguente:

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

Dopo aver recuperato un riferimento al contenitore, il metodo recupera un riferimento al blob per i dati archiviati. Se il blob esiste, le relative proprietà vengono recuperate dal `FetchAttributesAsync` (metodo). Viene creata una matrice di byte di dimensione corretta e il blob viene scaricato come una matrice di byte che vengono restituita al metodo di chiamata.

Dopo aver scaricato i dati di byte del blob, devono essere convertito nella relativa rappresentazione in forma originale. Ciò è dimostrato nell'esempio di codice seguente:

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

Matrice di byte viene recuperata da archiviazione di Azure per il `GetFileAsync` (metodo), prima che venga convertito nuovamente in un UTF8 in una stringa codificata.

## <a name="listing-data-in-a-container"></a>Elenca i dati in un contenitore

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

Dopo aver recuperato un riferimento al contenitore, il metodo Usa il contenitore `ListBlobsSegmentedAsync` metodo per recuperare i riferimenti ai BLOB all'interno del contenitore. I risultati restituiti dai `ListBlobsSegmentedAsync` metodo vengono enumerati mentre la `BlobContinuationToken` istanza non è `null`. Viene eseguito il cast di ogni blob restituiti `IListBlobItem` a un `CloudBlockBlob` Access ordine il `Name` proprietà del blob, prima che venga valore viene aggiunta al `allBlobsList` raccolta. Una volta il `BlobContinuationToken` istanza è `null`, è stato restituito l'ultimo nome di blob e l'esecuzione esce dal ciclo.

## <a name="deleting-data-from-a-container"></a>L'eliminazione dei dati da un contenitore

Il `DeleteFileAsync` metodo viene usato per eliminare un blob da un contenitore e viene illustrato nell'esempio di codice seguente:

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

Dopo aver recuperato un riferimento al contenitore, il metodo recupera un riferimento al blob per il blob specificato. Il blob viene quindi eliminato con il `DeleteIfExistsAsync` (metodo).

## <a name="related-links"></a>Collegamenti correlati

- [Archiviazione di Azure (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)
- [Introduzione all'archiviazione](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Come usare archiviazione Blob da Xamarin](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [Uso delle firme di accesso condiviso (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Windows Azure Storage (NuGet)](https://www.nuget.org/packages/WindowsAzure.Storage/)
