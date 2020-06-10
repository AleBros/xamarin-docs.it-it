---
title: " Xamarin.Forms e introduzione ai servizi cognitivi di Azure" Description: "questo articolo fornisce un'introduzione a un'applicazione di esempio che illustra come richiamare alcune API del servizio cognitivo Microsoft".
ms. prod: Novell MS. AssetID: 74121ADB-1322-4C1E-A103-F37257BC7CB0 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 02/08/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-and-azure-cognitive-services-introduction"></a>Xamarin.FormsIntroduzione ai servizi cognitivi di Azure

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_I servizi cognitivi Microsoft sono un set di API, SDK e servizi disponibili agli sviluppatori per rendere le applicazioni più intelligenti aggiungendo funzionalità come il riconoscimento facciale, il riconoscimento vocale e la comprensione del linguaggio. Questo articolo fornisce un'introduzione all'applicazione di esempio che illustra come richiamare alcune API del servizio cognitivo Microsoft._

## <a name="overview"></a>Panoramica

L'esempio associato è un'applicazione elenco attività che fornisce funzionalità per:

- Visualizzare un elenco di attività.
- Aggiungere e modificare le attività tramite la tastiera soft oppure eseguendo il riconoscimento vocale con la Speech API Microsoft.
- Attività di controllo ortografico con l'API Controllo ortografico Bing. Per altre informazioni, vedere [controllo ortografico con l'API controllo ortografico Bing](spell-check.md).
- Tradurre le attività dall'inglese al tedesco con l'API di conversione. Per altre informazioni, vedere [traduzione testuale con l'API Translator](text-translation.md).
- Elimina le attività.
- Impostare lo stato di un'attività su "Done".
- Valutare l'applicazione con il riconoscimento delle emozioni, usando il API Viso. Per altre informazioni, vedere [riconoscimento delle emozioni usando il API viso](emotion-recognition.md).

> [!WARNING]
> Il Speech API Bing è stato deprecato a favore del servizio riconoscimento vocale di Azure. Per un esempio dedicato al servizio riconoscimento vocale di Azure, vedere [riconoscimento vocale con l'API del servizio vocale](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md).

Le attività vengono archiviate in un database SQLite locale. Per ulteriori informazioni sull'utilizzo di un database SQLite locale, vedere [utilizzo di un database locale](~/xamarin-forms/data-cloud/data/databases.md).

`TodoListPage`Viene visualizzato quando viene avviata l'applicazione. In questa pagina viene visualizzato un elenco di tutte le attività archiviate nel database locale, che consente all'utente di creare una nuova attività o di valutare l'applicazione:

![](introduction-images/sample-application-1.png "TodoListPage")

È possibile creare nuovi elementi facendo clic sul *+* pulsante, che consente di passare a `TodoItemPage` . È anche possibile passare a questa pagina selezionando un'attività:

![](introduction-images/sample-application-2.png "TodoItemPage")

`TodoItemPage`Consente la creazione, la modifica, il controllo ortografico, la traduzione, il salvataggio e l'eliminazione delle attività. Il riconoscimento vocale può essere usato per creare o modificare un'attività. Questa operazione viene eseguita premendo il pulsante del microfono per avviare la registrazione e premendo lo stesso pulsante una seconda volta per arrestare la registrazione, che invia la registrazione all'API riconoscimento vocale Bing.

Facendo clic sul pulsante smilies nella `TodoListPage` si passa a `RateAppPage` , che viene usato per eseguire il riconoscimento delle emozioni su un'immagine di un'espressione facciale:

![](introduction-images/sample-application-3.png "RateAppPage")

`RateAppPage`Consente all'utente di scattare una foto della propria faccia, che viene inviata al API viso con la visualizzazione dell'emozione restituita.

## <a name="understand-the-application-anatomy"></a>Comprendere l'anatomia dell'applicazione

Il progetto di codice condiviso per l'applicazione di esempio è costituito da cinque cartelle principali:

|Cartella|Scopo|
|--- |--- |
|Modelli|Contiene le classi del modello di dati per l'applicazione. Ciò include la `TodoItem` classe, che modella un singolo elemento di dati usato dall'applicazione. La cartella include anche le classi usate per modellare le risposte JSON restituite da diverse API del servizio cognitivo Microsoft.|
|Repository|Contiene l' `ITodoItemRepository` interfaccia e la `TodoItemRepository` classe utilizzati per eseguire operazioni di database.|
|Servizi|Contiene le interfacce e le classi utilizzate per accedere a diverse API di servizi cognitivi Microsoft, insieme alle interfacce utilizzate dalla `DependencyService` classe per individuare le classi che implementano le interfacce nei progetti di piattaforma.|
|Utils|Contiene la `Timer` classe utilizzata dalla `AuthenticationService` classe per rinnovare un token di accesso JWT ogni 9 minuti.|
|Viste|Contiene le pagine per l'applicazione.|

Il progetto di codice condiviso contiene anche alcuni file importanti:

|File|Scopo|
|--- |--- |
|Constants.cs|La `Constants` classe, che specifica le chiavi API e gli endpoint per le API del servizio cognitivo Microsoft richiamate. Le costanti chiave API richiedono l'aggiornamento per accedere alle diverse API dei servizi cognitivi.|
|App.xaml.cs|La `App` classe è responsabile della creazione di un'istanza della prima pagina che verrà visualizzata dall'applicazione in ogni piattaforma e della `TodoManager` classe utilizzata per richiamare le operazioni del database.|

### <a name="nuget-packages"></a>Pacchetti NuGet

L'applicazione di esempio usa i pacchetti NuGet seguenti:

- `Newtonsoft.Json`: fornisce un Framework JSON per .NET.
- `PCLStorage`: fornisce un set di API di i/o di file locali multipiattaforma.
- `sqlite-net-pcl`: fornisce l'archiviazione del database SQLite.
- `Xam.Plugin.Media`: fornisce le API per l'acquisizione e la selezione di foto multipiattaforma.

Inoltre, questi pacchetti NuGet installano anche le proprie dipendenze.

### <a name="model-the-data"></a>Modellare i dati

L'applicazione di esempio usa la `TodoItem` classe per modellare i dati che vengono visualizzati e archiviati nel database SQLite locale. L'esempio di codice seguente visualizza la classe `TodoItem`:

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

La `ID` proprietà viene usata per identificare in modo univoco ogni `TodoItem` istanza ed è decorata con attributi SQLite che rendono la proprietà una chiave primaria a incremento automatico nel database.

### <a name="invoke-database-operations"></a>Richiama operazioni del database

La `TodoItemRepository` classe implementa le operazioni di database ed è possibile accedere a un'istanza della classe tramite la `App.TodoManager` Proprietà. La `TodoItemRepository` classe fornisce i metodi seguenti per richiamare le operazioni del database:

- **GetAllItemsAsync** : recupera tutti gli elementi dal database SQLite locale.
- **GetItemAsync** : Recupera un elemento specificato dal database SQLite locale.
- **SaveItemAsync** : crea o aggiorna un elemento nel database SQLite locale.
- **DeleteItemAsync** : Elimina l'elemento specificato dal database SQLite locale.

### <a name="platform-project-implementations"></a>Implementazioni del progetto di piattaforma

La `Services` cartella nel progetto di codice condiviso contiene le `IFileHelper` `IAudioRecorderService` interfacce e usate dalla `DependencyService` classe per individuare le classi che implementano le interfacce nei progetti di piattaforma.

L' `IFileHelper` interfaccia viene implementata dalla `FileHelper` classe in ogni progetto di piattaforma. Questa classe è costituita da un singolo metodo, `GetLocalFilePath` , che restituisce un percorso di file locale per archiviare il database SQLite.

L' `IAudioRecorderService` interfaccia viene implementata dalla `AudioRecorderService` classe in ogni progetto di piattaforma. Questa classe è costituita dai `StartRecording` `StopRecording` metodi di supporto, e che usano le API della piattaforma per registrare l'audio dal microfono del dispositivo e archiviarlo come file WAV. In iOS, `AudioRecorderService` Usa l' `AVFoundation` API per registrare l'audio. In Android, `AudioRecordService` Usa l' `AudioRecord` API per registrare l'audio. Nella piattaforma UWP (Universal Windows Platform) (UWP), `AudioRecorderService` Usa l' `AudioGraph` API per registrare l'audio.

### <a name="invoke-cognitive-services"></a>Richiama Servizi cognitivi

L'applicazione di esempio richiama i servizi cognitivi Microsoft seguenti:

- Microsoft Speech API. Per ulteriori informazioni, vedere [riconoscimento vocale utilizzando Microsoft Speech API](speech-recognition.md).
- API Controllo ortografico Bing. Per altre informazioni, vedere [controllo ortografico con l'API controllo ortografico Bing](spell-check.md).
- Tradurre l'API. Per altre informazioni, vedere [traduzione testuale con l'API Translator](text-translation.md).
- API Viso. Per altre informazioni, vedere [riconoscimento delle emozioni usando il API viso](emotion-recognition.md).

## <a name="related-links"></a>Collegamenti correlati

- [Riconoscimento vocale con l'API del servizio riconoscimento vocale](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md)
- [Documentazione dei servizi cognitivi Microsoft](https://www.microsoft.com/cognitive-services/documentation)
- [Servizi cognitivi todo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
