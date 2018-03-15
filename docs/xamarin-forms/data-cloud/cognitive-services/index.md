---
title: "Aggiunta di funzionalità con servizi cognitivi"
description: "Servizi cognitivi Microsoft sono un set di API e SDK di servizi disponibili per gli sviluppatori per rendere le applicazioni più intelligenti aggiungendo funzionalità quali il riconoscimento facciale, riconoscimento vocale e comprensione del linguaggio. In questo articolo viene fornita un'introduzione all'applicazione di esempio che illustra come richiamare alcune delle API dei servizi Microsoft cognitivi."
ms.topic: article
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: c309fb6936296dc181e499c91770ab8891121e9c
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2018
---
# <a name="adding-intelligence-with-cognitive-services"></a>Aggiunta di funzionalità con servizi cognitivi

_Servizi cognitivi Microsoft sono un set di API e SDK di servizi disponibili per gli sviluppatori per rendere le applicazioni più intelligenti aggiungendo funzionalità quali il riconoscimento facciale, riconoscimento vocale e comprensione del linguaggio. In questo articolo viene fornita un'introduzione all'applicazione di esempio che illustra come richiamare alcune delle API dei servizi Microsoft cognitivi._

## <a name="overview"></a>Panoramica

L'esempio di accompagnamento è un'applicazione di elenco todo che fornisce funzionalità per:

- Consente di visualizzare un elenco di attività.
- Aggiungere e modificare le attività tramite la tastiera, oppure eseguendo il riconoscimento vocale con l'API di riconoscimento vocale Bing. Per ulteriori informazioni sull'esecuzione di riconoscimento vocale, vedere [di riconoscimento vocale utilizzando l'API di riconoscimento vocale Bing](speech-recognition.md).
- Digitare il nome di attività di controllo utilizzando l'API di controllo ortografico Bing. Per ulteriori informazioni, vedere [il controllo ortografico tramite l'API di controllo ortografico Bing](spell-check.md).
- Tradurre attività dall'inglese, tedesco tramite l'API di conversione. Per ulteriori informazioni, vedere [la conversione di testo tramite l'API di conversione](text-translation.md).
- Eliminare le attività.
- Impostare lo stato dell'attività su "OK".
- Frequenza dell'applicazione con riconoscimento emozioni, tramite l'API emozioni. Per ulteriori informazioni, vedere [riconoscimento emozioni tramite l'API emozioni](emotion-recognition.md).

Le attività vengono archiviate in un database locale di SQLite. Per ulteriori informazioni sull'utilizzo di un database locale di SQLite, vedere [utilizzo di un Database locale](~/xamarin-forms/app-fundamentals/databases.md).

Il `TodoListPage` viene visualizzata quando viene avviata l'applicazione. Questa pagina consente di visualizzare un elenco di tutte le attività archiviate nel database locale e consente all'utente di creare una nuova attività o per valutare l'applicazione:

![](images/sample-application-1.png "TodoListPage")

Possono essere creati nuovi elementi facendo clic su di  *+*  pulsante che consente di passare al `TodoItemPage`. Questa pagina inoltre per spostarsi selezionando un'attività:

![](images/sample-application-2.png "TodoItemPage")

Il `TodoItemPage` consente alle attività di essere creato, modificato, ortografia, convertite, salvate ed eliminato. Il riconoscimento vocale consente di creare o modificare un'attività. A questo scopo facendo clic sul pulsante microfono per avviare la registrazione e facendo clic sul pulsante stesso una seconda volta per arrestare la registrazione, che invia la registrazione per l'API di riconoscimento vocale Bing.

Facendo clic sul pulsante smilies sul `TodoListPage` passa al `RateAppPage`, che consente di eseguire il riconoscimento emozioni in un'immagine di un'espressione facciale:

![](images/sample-application-3.png "RateAppPage")

Il `RateAppPage` consente all'utente di richiedere una foto del loro tipo di carattere, viene inviato all'API emozioni con l'emozioni restituita viene visualizzata.

## <a name="understanding-the-application-anatomy"></a>Comprendere l'anatomia di applicazione

Il progetto libreria di classe portabile (PCL) per l'applicazione di esempio è costituito da cinque cartelle principali:

|Cartella|Scopo|
|--- |--- |
|Modelli|Contiene le classi del modello di dati per l'applicazione. Ciò include la `TodoItem` classe, che modella un singolo elemento di dati utilizzati dall'applicazione. La cartella contiene inoltre le classi utilizzate per le risposte JSON modello restituite dall'API dei servizi cognitivi Microsoft diverso.|
|Repository|Contiene il `ITodoItemRepository` interfaccia e `TodoItemRepository` classe utilizzata per eseguire operazioni di database.|
|Servizi|Contiene le interfacce e classi che consentono di accedere a diversi cognitivi servizio API di Microsoft, insieme alle interfacce utilizzate per la `DependencyService` per individuare le classi che implementano le interfacce nei progetti di piattaforma.|
|Utils|Contiene il `Timer` (classe), che viene utilizzato il `AuthenticationService` classe per rinnovare un token di accesso JWT 9 minuti.|
|Visualizzazioni|Contiene le pagine dell'applicazione.|

Il progetto di libreria di classi Portabile contiene anche alcuni file importanti:

|File|Scopo|
|--- |--- |
|Constants.cs|Il `Constants` (classe), che specifica le chiavi API e gli endpoint per le API del servizio Microsoft cognitivi che vengono richiamati. Le costanti di chiave API devono essere aggiornati per accedere alle API servizio cognitivi diversi.|
|App.xaml.cs|Il `App` è responsabile della creazione di un'istanza sia la prima pagina che verrà visualizzata dall'applicazione in ogni piattaforma, e `TodoManager` classe utilizzata per richiamare le operazioni di database.|

### <a name="nuget-packages"></a>Pacchetti NuGet

L'applicazione di esempio utilizza i pacchetti NuGet seguenti:

- `Microsoft.Net.Http` : fornisce la `HttpClient` classe per eseguire le richieste su HTTP.
- `Newtonsoft.Json` : fornisce un framework JSON per .NET.
- `Microsoft.ProjectOxford.Emotion` : una libreria client per l'accesso all'API emozioni.
- `PCLStorage` : fornisce un set di API dei / o dei file locale multipiattaforma.
- `sqlite-net-pcl` : fornisce l'archiviazione del database SQLite.
- `Xam.Plugin.Media` : consente di acquisire foto multipiattaforma e API di prelievo.

Inoltre, questi pacchetti NuGet inoltre installare le proprie dipendenze.

### <a name="modeling-the-data"></a>Modellazione dei dati

Applicazione di esempio utilizza la `TodoItem` classe per modellare i dati visualizzati e archiviati nel database locale di SQLite. L'esempio di codice seguente visualizza la classe `TodoItem`:

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

Il `ID` proprietà viene utilizzata per identificare in modo univoco ogni `TodoItem` istanza ed è decorato con attributi di SQLite che rendono la proprietà di una chiave primaria con incremento automatico del database.

### <a name="invoking-database-operations"></a>Richiamo di operazioni di Database

Il `TodoItemRepository` classe implementa le operazioni di database e un'istanza della classe sono accessibili tramite il `App.TodoManager` proprietà. La `TodoItemRepository` classe fornisce i metodi seguenti per richiamare le operazioni di database:

- **GetAllItemsAsync** – recupera tutti gli elementi del database locale di SQLite.
- **GetItemAsync** – recupera un elemento specificato dal database locale di SQLite.
- **SaveItemAsync** : crea o aggiorna una voce nel database locale di SQLite.
- **DeleteItemAsync** -Elimina l'elemento specificato dal database locale di SQLite.

### <a name="platform-project-implementations"></a>Implementazioni di progetto della piattaforma

Il `Services` cartella nel progetto libreria di classi Portabile contiene il `IFileHelper` e `IAudioRecorderService` le interfacce utilizzate per la `DependencyService` per individuare le classi che implementano le interfacce nei progetti di piattaforma.

Il `IFileHelper` viene implementata mediante la `FileHelper` classe in ogni progetto della piattaforma. Questa classe è costituita da un singolo metodo, `GetLocalFilePath`, che restituisce un percorso file locale per archiviare il database di SQLite.

Il `IAudioRecorderService` viene implementata mediante la `AudioRecorderService` classe in ogni progetto della piattaforma. Questa classe è costituita `StartRecording`, `StopRecording`e i metodi, utilizzano le API della piattaforma per registrare l'audio dal microfono del dispositivo e salvarlo come file wav di supporto. In iOS, il `AudioRecorderService` utilizza il `AVFoundation` API per registrare l'audio. In Android, il `AudioRecordService` utilizza il `AudioRecord` API per registrare l'audio. Nel Windows piattaforma UWP (Universal), il `AudioRecorderService` utilizza il `AudioGraph` API per registrare l'audio.

### <a name="invoking-cognitive-services"></a>Richiamare i servizi cognitivi

L'applicazione di esempio richiama servizi cognitivi Microsoft seguenti:

- API di sintesi vocale Bing. Per ulteriori informazioni, vedere [di riconoscimento vocale utilizzando l'API di riconoscimento vocale Bing](speech-recognition.md).
- Controllo ortografico Bing API. Per ulteriori informazioni, vedere [il controllo ortografico tramite l'API di controllo ortografico Bing](spell-check.md).
- Tradurre API. Per ulteriori informazioni, vedere [la conversione di testo tramite l'API di conversione](text-translation.md).
- API emozioni. Per ulteriori informazioni, vedere [riconoscimento emozioni tramite l'API emozioni](emotion-recognition.md).


## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di Microsoft servizi cognitivi](https://www.microsoft.com/cognitive-services/documentation)
- [Servizi cognitivi TODO (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
