---
title: Informazioni sull'esempio
description: In questo argomento fornisce una procedura dettagliata dell'applicazione di esempio xamarin. Forms che illustra come comunicare con i servizi web diversi. Mentre ogni servizio web Usa un'applicazione di esempio separata, sono simili a livello funzionale e condividere classi comuni.
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: bfd7330fa1eee5f80a9043341d9760058d99d48b
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053451"
---
# <a name="understanding-the-sample"></a>Informazioni sull'esempio

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST)

_In questo argomento fornisce una procedura dettagliata dell'applicazione di esempio xamarin. Forms che illustra come comunicare con i servizi web diversi. Mentre ogni servizio web Usa un'applicazione di esempio separata, sono simili a livello funzionale e condividere classi comuni._

L'applicazione di elenco attività di esempio descritta di seguito viene usato per illustrare come accedere ai diversi tipi di back-end del servizio web con xamarin. Forms. Fornisce funzionalità per:

- Visualizzare un elenco di attività.
- Aggiungere, modificare ed eliminare le attività.
- Impostare lo stato dell'attività su "OK".
- Parlare con i campi nome e le note dell'attività.

In tutti i casi, le attività vengono memorizzate in un back-end a cui si accede tramite un servizio web.

Quando viene avviata l'applicazione, viene visualizzata una pagina che elenca tutte le attività recuperate dal servizio web e consente all'utente di creare una nuova attività. Facendo clic su un'attività consente di passare l'applicazione a un'altra pagina in cui l'attività possa essere modificato, salvato, eliminata e parlata. Il risultato è riportato di seguito:

![](walkthrough-images/app-example-1.png "Applicazione TODO - pagina iniziale")
![](walkthrough-images/app-example-2.png "applicazione Todo - seconda pagina")

Ogni argomento di questa guida viene fornito un collegamento di download per un *diversi* versione dell'applicazione in cui viene illustrato un tipo specifico di back-end del servizio web. Scaricare il codice di esempio rilevanti nella pagina relativa allo stile ogni servizio web.

## <a name="understanding-the-application-anatomy"></a>Comprendere la composizione dell'applicazione

Il progetto libreria di classi Portabile per ogni applicazione di esempio è costituito da tre cartelle principali:

|Cartella|Scopo|
|--- |--- |
|Dati|Contiene le classi e interfacce utilizzate per gestire gli elementi di dati e comunicare con il servizio web. Come minimo, inclusi i `TodoItemManager` classe, che viene esposta tramite una proprietà nel `App` classe per richiamare operazioni del servizio web.|
|Modelli|Contiene le classi di modello di dati per l'applicazione. Come minimo, inclusi il `TodoItem` (classe), che modella un singolo elemento di dati usati dall'applicazione. La cartella può includere anche altre classi usate per modellare i dati utente.|
|Visualizzazioni|Contiene le pagine dell'applicazione. Ciò in genere è costituito il `TodoListPage` e `TodoItemPage` classi e altre classi usate per scopi di autenticazione.|

Inoltre, il progetto libreria di classi Portabile per ogni applicazione è costituito da un numero di file importanti:

|File|Scopo|
|--- |--- |
|Constants.cs|Il `Constants` classe, che specifica tutte le costanti usate dall'applicazione per comunicare con il servizio web. Queste costanti richiedono l'aggiornamento per accedere al servizio back-end personale creato in un provider.|
|ITextToSpeech.cs|Il `ITextToSpeech` interfaccia, che specifica che il `Speak` metodo deve essere fornito dalle classi di implementazione.|
|Todo.cs|Il `App` classe responsabile per la creazione di un'istanza sia la prima pagina visualizzata dall'applicazione in ogni piattaforma, e il `TodoItemManager` classe che viene utilizzato per richiamare operazioni del servizio web.|

### <a name="viewing-pages"></a>Visualizzazione delle pagine

La maggior parte delle applicazioni di esempio contiene almeno due pagine:

- **TodoListPage** : questa pagina consente di visualizzare un elenco di `TodoItem` istanze e un'icona di segno di spunta se il `TodoItem.Done` è di proprietà `true`. Facendo clic su un elemento passa al `TodoItemPage`. Inoltre, possono essere creati nuovi elementi facendo clic sui *+* simbolo.
- **TodoItemPage** : questa pagina vengono visualizzati i dettagli per il profilo selezionato `TodoItem`e consente di essere modificati, salvati, eliminate e parlata.

Inoltre, alcune applicazioni di esempio contengono pagine aggiuntive che consentono di gestire il processo di autenticazione utente.

### <a name="modeling-the-data"></a>Modellazione dei dati

Ogni applicazione di esempio Usa il `TodoItem` classe per modellare i dati visualizzati e inviati al servizio web per l'archiviazione. L'esempio di codice seguente visualizza la classe `TodoItem`:

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

Il `ID` proprietà viene utilizzata per identificare in modo univoco ogni `TodoItem` istanza e viene utilizzato da ogni servizio web per identificare i dati per essere aggiornate o eliminate.

### <a name="invoking-web-service-operations"></a>Richiamare operazioni del servizio Web

Operazioni del servizio Web sono accessibili tramite il `TodoItemManager` classe e un'istanza della classe sono accessibili tramite il `App.TodoManager` proprietà. Il `TodoItemManager` classe fornisce i metodi seguenti per richiamare operazioni del servizio web:

- **GetTasksAsync** : questo metodo viene utilizzato per popolare le `ListView` control per il `TodoListPage` con il `TodoItem` istanze recuperati dal servizio web.
- **SaveTaskAsync** : questo metodo viene utilizzato per creare o aggiornare un `TodoItem` istanza dal servizio web.
- **DeleteTaskAsync** : questo metodo viene utilizzato per eliminare un `TodoItem` istanza dal servizio web.

Inoltre, alcune applicazioni di esempio contengono metodi aggiuntivi con il `TodoItemManager` (classe), che vengono usati per gestire il processo di autenticazione utente.

Anziché richiamare direttamente le operazioni del servizio web di `TodoItemManager` metodi di richiamano i metodi in una classe di dipendenti che verranno inseriti nella `TodoItemManager` costruttore. Ad esempio, un'applicazione di esempio inserisce il `RestService` classe la `TodoItemManager` costruttore per fornire l'implementazione che usa le API REST per accedere ai dati.

### <a name="translating-text-to-speech"></a>Traduzione vocale

La maggior parte delle applicazioni di esempio contengono funzionalità di sintesi vocale da leggere a voce i valori del `TodoItem.Name` e `TodoItem.Notes` proprietà. Questa operazione viene eseguita la `OnSpeakActivated` gestore dell'evento nel `TodoItemPage` classe, come illustrato nell'esempio di codice seguente:

```csharp
void OnSpeakActivated (object sender, EventArgs e)
{
    var todoItem = (TodoItem)BindingContext;
    App.Speech.Speak(todoItem.Name + " " + todoItem.Notes);
}
```

Questo metodo richiama semplicemente il `Speak` metodo implementato da una specifica della piattaforma `Speech` classe. Ogni `Speech` classe implementa le `ITextToSpeech` interfaccia, e il codice di avvio specifiche della piattaforma crea un'istanza del `Speech` classe che è possibile accedere tramite il `App.Speech` proprietà.

## <a name="summary"></a>Riepilogo

In questo argomento è illustrata una procedura dettagliata dell'applicazione di esempio xamarin. Forms che viene usato per illustrare come comunicare con i servizi web diversi. Mentre ogni servizio web Usa un'applicazione di esempio separato, sono tutti basati sulla logica dell'interfaccia utente e azienda stessa come descritto in precedenza, solo il web del servizio dati meccanismo di archiviazione è diverso.


## <a name="related-links"></a>Collegamenti correlati

- [Versione ASMX (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX)
- [Versione WCF (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF)
- [Versione REST (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST)
- [Versione di Azure (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure)
