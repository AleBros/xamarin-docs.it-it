---
title: Introduzione a Xamarin.Forms e servizi cognitivi di Azure
description: Questo articolo fornisce un'introduzione a un'applicazione di esempio che illustra come richiamare alcune delle API servizi cognitivi Microsoft.
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 12802abe7b027f4e6d59abd62d2ae0611d71f438
ms.sourcegitcommit: ba83c107c87b015dbcc9db13964fe111a0573dca
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76265191"
---
# <a name="xamarinforms-and-azure-cognitive-services-introduction"></a>Introduzione a Xamarin.Forms e servizi cognitivi di Azure

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_I servizi cognitivi Microsoft sono un set di API, SDK e servizi disponibili agli sviluppatori per rendere le applicazioni più intelligenti aggiungendo funzionalità come il riconoscimento facciale, il riconoscimento vocale e la comprensione del linguaggio. Questo articolo fornisce un'introduzione all'applicazione di esempio che illustra come richiamare alcune API del servizio cognitivo Microsoft._

## <a name="overview"></a>Panoramica di

Esempio di accompagnamento è un'applicazione elenco attività che fornisce funzionalità per:

- Visualizzare un elenco di attività.
- Aggiungere e modificare le attività tramite la tastiera, oppure eseguendo il riconoscimento vocale con l'API traduzione vocale Microsoft.
- Digitare il nome di attività di controllo usando l'API controllo ortografico Bing. Per altre informazioni, vedere [ortografico usando l'API controllo ortografico Bing](spell-check.md).
- Tradurre le attività dall'inglese in tedesco utilizzando l'API Microsoft Translator. Per altre informazioni, vedere [traduzione testuale tramite l'API Microsoft Translator](text-translation.md).
- Eliminare le attività.
- Impostare lo stato dell'attività su "OK".
- Valutare l'applicazione con riconoscimento delle emozioni, tramite l'API viso. Per altre informazioni, vedere [riconoscimento delle emozioni tramite l'API viso](emotion-recognition.md).

> [!WARNING]
> Il Speech API Bing è stato deprecato a favore del servizio riconoscimento vocale di Azure. Per un esempio dedicato al servizio riconoscimento vocale di Azure, vedere [riconoscimento vocale con l'API del servizio vocale](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md).

Le attività vengono archiviate in un database SQLite locale. Per altre informazioni sull'uso di un database SQLite locale, vedere [utilizzo di un Database locale](~/xamarin-forms/data-cloud/data/databases.md).

Il `TodoListPage` viene visualizzata quando viene avviata l'applicazione. Questa pagina viene visualizzato un elenco di tutte le attività archiviati nel database locale e consente all'utente per creare una nuova attività o di frequenza dell'applicazione:

![](introduction-images/sample-application-1.png "TodoListPage")

Possono essere creati nuovi elementi facendo clic sui *+* pulsante che consente di passare al `TodoItemPage`. Questa pagina anche per spostarsi selezionando un'attività:

![](introduction-images/sample-application-2.png "TodoItemPage")

Il `TodoItemPage` consente alle attività di essere creato, modificato, ortografia, convertite, salvate ed eliminate. Riconoscimento vocale è utilizzabile per creare o modificare un'attività. Questo risultato viene ottenuto premendo il pulsante del microfono per avviare la registrazione e premendo il pulsante stesso una seconda volta per arrestare la registrazione, che invia la registrazione per l'API di riconoscimento vocale Bing.

Facendo clic sul pulsante smilies sul `TodoListPage` passa al `RateAppPage`, che consente di eseguire il riconoscimento delle emozioni su un'immagine di un'espressione del viso:

![](introduction-images/sample-application-3.png "RateAppPage")

Il `RateAppPage` consente all'utente di scattare una foto della loro visi, che viene inviata all'API viso con le emozioni restituito viene visualizzato.

## <a name="understand-the-application-anatomy"></a>Comprendere l'anatomia dell'applicazione

Il progetto di codice condiviso per l'applicazione di esempio è costituito da cinque cartelle principali:

|Folder|Scopo|
|--- |--- |
|Modelli|Contiene le classi di modello di dati per l'applicazione. Ciò include la `TodoItem` (classe), che modella un singolo elemento di dati usati dall'applicazione. La cartella contiene inoltre le classi usate per modellare le risposte JSON restituite da diverse API servizi cognitivi Microsoft.|
|Repository|Contiene il `ITodoItemRepository` interfaccia e `TodoItemRepository` classi che consentono di eseguire operazioni di database.|
|Servizi|Contiene le interfacce e classi che consentono di accedere a diversi Microsoft le API servizi cognitivi, insieme alle interfacce utilizzate dal `DependencyService` classe per individuare le classi che implementano le interfacce nei progetti di piattaforma.|
|Utils|Contiene il `Timer` (classe), che viene usato dal `AuthenticationService` classe per rinnovare un token di accesso JWT ogni 9 minuti.|
|Visualizzazioni|Contiene le pagine dell'applicazione.|

Il progetto di codice condiviso contiene anche alcuni file importanti:

|File|Scopo|
|--- |--- |
|Constants.cs|Il `Constants` (classe), che specifica gli endpoint e le chiavi API per le API di servizi cognitivi Microsoft che vengono richiamati. Le costanti di chiavi API richiedono l'aggiornamento per accedere alle API di servizi cognitivi diversi.|
|App.xaml.cs|Il `App` classe è responsabile della creazione di un'istanza sia la prima pagina visualizzata dall'applicazione in ogni piattaforma, e il `TodoManager` classe che viene utilizzato per richiamare operazioni del database.|

### <a name="nuget-packages"></a>Pacchetti NuGet

L'applicazione di esempio Usa i pacchetti NuGet seguenti:

- `Newtonsoft.Json` : fornisce un framework JSON per .NET.
- `PCLStorage` : fornisce un set di file locale multipiattaforma con le API dei / o.
- `sqlite-net-pcl` – Consente di memorizzare database SQLite.
- `Xam.Plugin.Media` – Consente di acquisire foto multipiattaforma e il prelievo di API.

Inoltre, questi pacchetti NuGet installano anche le proprie dipendenze.

### <a name="model-the-data"></a>Modellare i dati

L'applicazione di esempio Usa il `TodoItem` classe per modellare i dati visualizzati e archiviati nel database SQLite locale. L'esempio di codice seguente visualizza la classe `TodoItem`:

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

Il `ID` proprietà viene utilizzata per identificare in modo univoco ogni `TodoItem` di istanza ed è decorato con attributi di SQLite che rendono la proprietà di una chiave primaria con incremento automatico nel database.

### <a name="invoke-database-operations"></a>Richiama operazioni del database

Il `TodoItemRepository` classe implementa le operazioni di database e un'istanza della classe sono accessibili tramite il `App.TodoManager` proprietà. Il `TodoItemRepository` classe fornisce i metodi seguenti per richiamare le operazioni di database:

- **GetAllItemsAsync** -recupera tutti gli elementi dal database SQLite locale.
- **GetItemAsync** : recupera un elemento specificato dal database SQLite locale.
- **SaveItemAsync** : crea o aggiorna un elemento nel database SQLite locale.
- **DeleteItemAsync** – Elimina l'elemento specificato dal database SQLite locale.

### <a name="platform-project-implementations"></a>Implementazioni del progetto di piattaforma

La cartella `Services` nel progetto di codice condiviso contiene le interfacce `IFileHelper` e `IAudioRecorderService` utilizzate dalla classe `DependencyService` per individuare le classi che implementano le interfacce nei progetti di piattaforma.

Il `IFileHelper` viene implementata mediante il `FileHelper` classe in ogni progetto della piattaforma. Questa classe è costituito da un singolo metodo, `GetLocalFilePath`, che restituisce un percorso file locale per memorizzare database SQLite.

Il `IAudioRecorderService` viene implementata mediante il `AudioRecorderService` classe in ogni progetto della piattaforma. Questa classe è costituita `StartRecording`, `StopRecording`e il supporto di metodi che usano le API della piattaforma di registrare l'audio dal microfono del dispositivo e archiviano come un file wav. In iOS, il `AudioRecorderService` utilizza il `AVFoundation` API per registrare l'audio. In Android, il `AudioRecordService` utilizza il `AudioRecord` API per registrare l'audio. In Universal Windows Platform (UWP), il `AudioRecorderService` utilizza il `AudioGraph` API per registrare l'audio.

### <a name="invoke-cognitive-services"></a>Richiama Servizi cognitivi

L'applicazione di esempio richiama i servizi cognitivi Microsoft seguenti:

- API traduzione vocale di Microsoft. Per altre informazioni, vedere [riconoscimento vocale usando l'API traduzione vocale Microsoft](speech-recognition.md).
- API del controllo ortografico Bing. Per altre informazioni, vedere [ortografico usando l'API controllo ortografico Bing](spell-check.md).
- API traduzione. Per altre informazioni, vedere [traduzione testuale tramite l'API Microsoft Translator](text-translation.md).
- API viso. Per altre informazioni, vedere [riconoscimento delle emozioni tramite l'API viso](emotion-recognition.md).

## <a name="related-links"></a>Collegamenti correlati

- [Riconoscimento vocale con l'API del servizio riconoscimento vocale](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md)
- [Documentazione dei servizi cognitivi Microsoft](https://www.microsoft.com/cognitive-services/documentation)
- [Servizi cognitivi TODO (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
