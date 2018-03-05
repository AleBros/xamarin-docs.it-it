---
title: La comprensione dell'esempio
description: In questo argomento fornisce una procedura dettagliata dell'applicazione di esempio xamarin. Forms che illustra come comunicare con servizi web diversi. Mentre ogni servizio web utilizza un'applicazione di esempio distinto, sono simili a livello funzionale e condividono classi comuni.
ms.topic: article
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 6625edc1f661e5f9769de82ec48367e9f900e567
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="understanding-the-sample"></a>La comprensione dell'esempio

_In questo argomento fornisce una procedura dettagliata dell'applicazione di esempio xamarin. Forms che illustra come comunicare con servizi web diversi. Mentre ogni servizio web utilizza un'applicazione di esempio distinto, sono simili a livello funzionale e condividono classi comuni._

L'applicazione elenco attività di esempio descritto di seguito viene utilizzato per illustrare come accedere a tipi diversi di back-end del servizio web con xamarin. Forms. Fornisce funzionalità per:

- Consente di visualizzare un elenco di attività.
- Aggiungere, modificare ed eliminare le attività.
- Impostare lo stato dell'attività su "OK".
- Leggi i campi nome e le note dell'attività.

In tutti i casi, le attività vengono archiviate in un back-end a cui si accede tramite un servizio web.

Quando viene avviata l'applicazione, viene visualizzata una pagina che elenca tutte le attività recuperate dal servizio web e consente all'utente di creare una nuova attività. Facendo clic su un'attività consente di passare all'applicazione di una seconda pagina in cui l'attività può essere modificato, salvato, eliminato e letto. L'applicazione finale è riportata di seguito:

![](walkthrough-images/app-example-1.png "Applicazione TODO: pagina iniziale")
![](walkthrough-images/app-example-2.png "applicazione Todo - seconda pagina")

Ogni argomento di questa guida fornisce un collegamento di download per un *diversi* versione dell'applicazione in cui viene illustrato un tipo specifico di back-end del servizio web. Scaricare il codice di esempio pertinenti nella pagina relativa allo stile ogni servizio web.

## <a name="understanding-the-application-anatomy"></a>Comprendere l'anatomia di applicazione

Il progetto libreria di classi Portabile per ogni applicazione di esempio è costituito da tre cartelle principali:

<table>
    <thead>
        <tr><td><strong>Cartella</strong></td><td><strong>Scopo</strong></td></tr>
    </thead>
    <tbody>
        <tr>
            <td><strong>Dati</strong></td>
                        <td>Contiene le classi e interfacce utilizzate per gestire gli elementi di dati e comunicare con il servizio web. Come minimo, inclusi il <code>TodoItemManager</code> (classe), che viene esposto tramite una proprietà nel <code>App</code> classe per richiamare operazioni del servizio web.</td>
        </tr>
        <tr>
            <td><strong>Modelli</strong></td>
                        <td>Contiene le classi del modello di dati per l'applicazione. Come minimo, inclusi il <code>TodoItem</code> (classe), che modella un singolo elemento di dati utilizzati dall'applicazione. La cartella è inoltre possibile includere qualsiasi classi aggiuntive utilizzate per modellare i dati utente.</td>
        </tr>
        <tr>
            <td><strong>Visualizzazioni</strong></td>
                        <td>Contiene le pagine dell'applicazione. Ciò in genere è costituito il <code>TodoListPage</code> e <code>TodoItemPage</code> classi e le classi aggiuntive utilizzate per scopi di autenticazione.</td>
                </tr>
    </tbody>
</table>

Inoltre, il progetto libreria di classi Portabile per ogni applicazione è costituito da un numero di file importanti:

<table>
    <thead>
      <tr><td><strong>File</strong></td><td><strong>Scopo</strong></td></tr>
    <thead>
    <tbody>
        <tr>
            <td><strong>Constants.cs</strong></td>
            <td>Il <code>Constants</code> (classe), che specifica le costanti usate dall'applicazione per comunicare con il servizio web. Queste costanti richiedono l'aggiornamento per accedere al servizio back-end personale creato su un provider.
        </tr>
        <tr>
            <td><strong>ITextToSpeech.cs</strong></td>
            <td>Il <code>ITextToSpeech</code> interfaccia, che specifica che il <code>Speak</code> metodo deve essere fornito dalle classi di implementazione.</td>
        </tr>
        <tr>
          <td><strong>Todo.cs</strong></td>
          <td>Il <code>App</code> classe responsabile per la creazione di un'istanza sia la prima pagina che verrà visualizzata dall'applicazione in ogni piattaforma, e <code>TodoItemManager</code> classe che viene utilizzato per richiamare operazioni del servizio web.</td>
        </tr>
    </tbody>
</table>

### <a name="viewing-pages"></a>Visualizzazione delle pagine

La maggior parte delle applicazioni di esempio contiene almeno due pagine:

- **TodoListPage** : questa pagina consente di visualizzare un elenco di `TodoItem` istanze e un'icona segno di spunta se il `TodoItem.Done` proprietà `true`. Scegliere un elemento per spostarsi di `TodoItemPage`. Inoltre, è possano creati nuovi elementi facendo clic su di  *+*  simbolo.
- **TodoItemPage** : questa pagina vengono visualizzati i dettagli per il nodo `TodoItem`e consente di modificare, salvare, eliminato e pronunciata.

Inoltre, alcune applicazioni di esempio contengono pagine aggiuntive che consentono di gestire il processo di autenticazione utente.

### <a name="modeling-the-data"></a>Modellazione dei dati

Ogni applicazione di esempio viene utilizzato il `TodoItem` classe per modellare i dati che viene visualizzati e inviati al servizio web per l'archiviazione. L'esempio di codice seguente visualizza la classe `TodoItem`:

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

Il `ID` proprietà viene utilizzata per identificare in modo univoco ogni `TodoItem` istanza e viene utilizzato da ogni servizio web per identificare i dati in modo da essere aggiornato o eliminato.

### <a name="invoking-web-service-operations"></a>Richiamare operazioni del servizio Web

Operazioni del servizio Web si accede tramite il `TodoItemManager` classe e un'istanza della classe sono accessibili tramite il `App.TodoManager` proprietà. La `TodoItemManager` classe fornisce i metodi seguenti per richiamare operazioni del servizio web:

- **GetTasksAsync** : questo metodo viene utilizzato per popolare il `ListView` control per il `TodoListPage` con il `TodoItem` recuperare le istanze del servizio web.
- **SaveTaskAsync** : questo metodo viene utilizzato per creare o aggiornare un `TodoItem` istanza dal servizio web.
- **DeleteTaskAsync** : questo metodo viene utilizzato per eliminare un `TodoItem` istanza dal servizio web.

Inoltre, alcune applicazioni di esempio contengono metodi aggiuntivi con il `TodoItemManager` (classe), che consentono di gestire il processo di autenticazione utente.

Anziché richiamare direttamente, le operazioni del servizio web di `TodoItemManager` metodi richiamano metodi su una classe di dipendenti che viene inserito nel `TodoItemManager` costruttore. Ad esempio, un'applicazione di esempio inserisce il `SimpleDBStorage` classe nel `TodoItemManager` costruttore per fornire l'implementazione che richiama le operazioni nel servizio SimpleDB Amazon.

### <a name="translating-text-to-speech"></a>Conversione di sintesi vocale

La maggior parte delle applicazioni di esempio contengono funzionalità di sintesi vocale (TTS) per leggere i valori di al `TodoItem.Name` e `TodoItem.Notes` proprietà. Questa operazione viene eseguita la `OnSpeakActivated` gestore dell'evento nel `TodoItemPage` classe, come illustrato nell'esempio di codice seguente:

```csharp
void OnSpeakActivated (object sender, EventArgs e)
{
    var todoItem = (TodoItem)BindingContext;
    App.Speech.Speak(todoItem.Name + " " + todoItem.Notes);
}
```

Questo metodo richiama semplicemente il `Speak` metodo implementato da una specifica della piattaforma `Speech` classe. Ogni `Speech` classe implementa il `ITextToSpeech` interfaccia, e il codice di avvio specifico della piattaforma crea un'istanza del `Speech` classe che è possibile accedere tramite il `App.Speech` proprietà.

## <a name="summary"></a>Riepilogo

In questo argomento viene fornita una procedura dettagliata dell'applicazione di esempio xamarin. Forms che viene utilizzato per illustrare come comunicare con servizi web diversi. Mentre ciascun servizio web utilizza un'applicazione di esempio separato, sono tutte basate sulla stessa logica di business e di interfaccia utente come descritto in precedenza, solo il web del servizio dati meccanismo di archiviazione è diverso.


## <a name="related-links"></a>Collegamenti correlati

- [Versione ASMX (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX)
- [Versione WCF (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF)
- [Versione REST (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST)
- [Versione di Azure (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure)
- [Versione di Amazon Web Services (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWS)
