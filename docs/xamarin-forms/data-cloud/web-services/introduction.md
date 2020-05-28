---
title: Xamarin.FormsIntroduzione ai servizi Web
description: Questa guida fornisce una procedura dettagliata dell' Xamarin.Forms applicazione di esempio che illustra come comunicare con diversi servizi Web. Mentre ogni servizio Web usa un'applicazione di esempio separata, sono funzionalmente simili e condividono classi comuni.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d714b4c9d598d8cca26ae992abf3f15df703d11b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139178"
---
# <a name="xamarinforms-web-services-introduction"></a>Xamarin.FormsIntroduzione ai servizi Web

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_In questo argomento viene fornita una procedura dettagliata dell' Xamarin.Forms applicazione di esempio che illustra come comunicare con diversi servizi Web. Mentre ogni servizio Web usa un'applicazione di esempio separata, sono funzionalmente simili e condividono classi comuni._

L'applicazione to-do list di esempio descritta di seguito viene usata per dimostrare come accedere a tipi diversi di backend di servizi Web con Xamarin.Forms . Fornisce funzionalità per:

- Visualizzare un elenco di attività.
- Aggiunta, modifica ed eliminazione di attività.
- Impostare lo stato di un'attività su "Done".
- Pronunciare i campi nome e note dell'attività.

In tutti i casi, le attività vengono archiviate in un back-end a cui si accede tramite un servizio Web.

Quando viene avviata l'applicazione, viene visualizzata una pagina in cui sono elencate le attività recuperate dal servizio Web e viene consentito all'utente di creare una nuova attività. Facendo clic su un'attività, l'applicazione viene spostata in una seconda pagina in cui l'attività può essere modificata, salvata, eliminata e pronunciata. L'applicazione finale è riportata di seguito:

![](introduction-images/app-example-1.png "Todo application - first page")
![](introduction-images/app-example-2.png "Todo application - second page")

Ogni argomento di questa guida fornisce un collegamento di download a una versione *diversa* dell'applicazione che illustra un tipo specifico di back-end del servizio Web. Scaricare il codice di esempio pertinente nella pagina relativa a ogni stile del servizio Web.

## <a name="understand-the-application-anatomy"></a>Comprendere l'anatomia dell'applicazione

Il progetto di codice condiviso per ogni applicazione di esempio è costituito da tre cartelle principali:

|Cartella|Scopo|
|--- |--- |
|Data|Contiene le classi e le interfacce utilizzate per gestire gli elementi di dati e comunicare con il servizio Web. Come minimo, include la `TodoItemManager` classe, esposta tramite una proprietà nella `App` classe per richiamare le operazioni del servizio Web.|
|Modelli|Contiene le classi del modello di dati per l'applicazione. Come minimo, è inclusa la `TodoItem` classe, che modella un singolo elemento di dati usato dall'applicazione. La cartella può includere anche qualsiasi classe aggiuntiva utilizzata per modellare i dati utente.|
|Viste|Contiene le pagine per l'applicazione. Questo in genere è costituito dalle `TodoListPage` `TodoItemPage` classi e e da qualsiasi classe aggiuntiva utilizzata a scopo di autenticazione.|

Il progetto di codice condiviso per ogni applicazione è costituito anche da alcuni file importanti:

|File|Scopo|
|--- |--- |
|Constants.cs|`Constants`Classe, che specifica le costanti utilizzate dall'applicazione per comunicare con il servizio Web. Queste costanti richiedono l'aggiornamento per accedere al servizio back-end personale creato in un provider.|
|ITextToSpeech.cs|`ITextToSpeech`Interfaccia, che specifica che il `Speak` metodo deve essere fornito da qualsiasi classe di implementazione.|
|Todo.cs|`App`Classe responsabile della creazione di un'istanza della prima pagina che verrà visualizzata dall'applicazione in ogni piattaforma e della `TodoItemManager` classe utilizzata per richiamare le operazioni del servizio Web.|

### <a name="view-pages"></a>Visualizza pagine

La maggior parte delle applicazioni di esempio contiene almeno due pagine:

- **TodoListPage** : in questa pagina viene visualizzato un elenco di `TodoItem` istanze e un'icona di segno di spunta se la `TodoItem.Done` proprietà è `true` . Facendo clic su un elemento si passa a `TodoItemPage` . Inoltre, è possibile creare nuovi elementi facendo clic sul *+* simbolo.
- **TodoItemPage** : Questa pagina consente di visualizzare i dettagli per l'oggetto selezionato `TodoItem` e di modificarli, salvarli, eliminarli e pronunciarli.

Inoltre, alcune applicazioni di esempio contengono ulteriori pagine utilizzate per gestire il processo di autenticazione utente.

### <a name="model-the-data"></a>Modellare i dati

Ogni applicazione di esempio usa la `TodoItem` classe per modellare i dati che vengono visualizzati e inviati al servizio Web per l'archiviazione. L'esempio di codice seguente visualizza la classe `TodoItem`:

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

La `ID` proprietà viene utilizzata per identificare in modo univoco ogni `TodoItem` istanza di e viene utilizzata da ogni servizio Web per identificare i dati da aggiornare o eliminare.

### <a name="invoke-web-service-operations"></a>Richiama le operazioni del servizio Web

È possibile accedere alle operazioni del servizio Web tramite la `TodoItemManager` classe e accedere a un'istanza della classe tramite la `App.TodoManager` Proprietà. La `TodoItemManager` classe fornisce i metodi seguenti per richiamare le operazioni del servizio Web:

- **GetTasksAsync** : questo metodo viene usato per popolare il `ListView` controllo in `TodoListPage` con le `TodoItem` istanze recuperate dal servizio Web.
- **SaveTaskAsync** : questo metodo viene usato per creare o aggiornare un' `TodoItem` istanza nel servizio Web.
- **DeleteTaskAsync** : questo metodo viene usato per eliminare un' `TodoItem` istanza nel servizio Web.

Inoltre, alcune applicazioni di esempio contengono metodi aggiuntivi nella `TodoItemManager` classe, usati per gestire il processo di autenticazione utente.

Anziché richiamare direttamente le operazioni del servizio Web, i `TodoItemManager` metodi richiamano metodi su una classe dipendente inserita nel `TodoItemManager` costruttore. Ad esempio, un'applicazione di esempio inserisce la `RestService` classe nel `TodoItemManager` costruttore per fornire l'implementazione che usa le API REST per accedere ai dati.

## <a name="related-links"></a>Collegamenti correlati

- [ASMX (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
