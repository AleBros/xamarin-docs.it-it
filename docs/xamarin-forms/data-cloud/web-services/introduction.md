---
title: Introduzione ai servizi Web di Xamarin.Forms
description: Questa guida fornisce una procedura dettagliata dell'applicazione di esempio Xamarin.Forms che illustra come comunicare con diversi servizi Web. Mentre ogni servizio Web usa un'applicazione di esempio separata, sono funzionalmente simili e condividono classi comuni.
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: bbeab6a6ab0d4a9d0e3a962240317fc0d54f9e25
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656645"
---
# <a name="xamarinforms-web-services-introduction"></a>Introduzione ai servizi Web di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_Questo argomento fornisce una procedura dettagliata dell'applicazione di esempio Xamarin.Forms che illustra come comunicare con diversi servizi Web. Mentre ogni servizio Web usa un'applicazione di esempio separata, sono funzionalmente simili e condividono classi comuni._

L'applicazione to-do list di esempio descritta di seguito viene usata per illustrare come accedere a tipi diversi di backend di servizi Web con Xamarin.Forms. Fornisce funzionalità per:

- Visualizzare un elenco di attività.
- Aggiunta, modifica ed eliminazione di attività.
- Impostare lo stato di un'attività su "Done".
- Pronunciare i campi nome e note dell'attività.

In tutti i casi, le attività vengono archiviate in un back-end a cui si accede tramite un servizio Web.

Quando viene avviata l'applicazione, viene visualizzata una pagina in cui sono elencate le attività recuperate dal servizio Web e viene consentito all'utente di creare una nuova attività. Facendo clic su un'attività, l'applicazione viene spostata in una seconda pagina in cui l'attività può essere modificata, salvata, eliminata e pronunciata. Il risultato è riportato di seguito:

![](introduction-images/app-example-1.png "Todo application - first page")
![](introduction-images/app-example-2.png "Todo application - second page")

Ogni argomento di questa guida fornisce un collegamento di download a una versione *diversa* dell'applicazione che illustra un tipo specifico di back-end del servizio Web. Scaricare il codice di esempio pertinente nella pagina relativa a ogni stile del servizio Web.

## <a name="understand-the-application-anatomy"></a>Comprendere l'anatomia dell'applicazione

Il progetto di codice condiviso per ogni applicazione di esempio è costituito da tre cartelle principali:

|Cartella|Scopo|
|--- |--- |
|Dati|Contiene le classi e le interfacce utilizzate per gestire gli elementi di dati e comunicare con il servizio Web. Come minimo, include la classe `TodoItemManager`, esposta tramite una proprietà nella classe `App` per richiamare le operazioni del servizio Web.|
|Modelli|Contiene le classi del modello di dati per l'applicazione. Come minimo, questo include la classe `TodoItem`, che modella un singolo elemento di dati usato dall'applicazione. La cartella può includere anche qualsiasi classe aggiuntiva utilizzata per modellare i dati utente.|
|Visualizzazioni|Contiene le pagine per l'applicazione. Questo in genere è costituito dalle classi `TodoListPage` e `TodoItemPage` e da qualsiasi classe aggiuntiva utilizzata a scopo di autenticazione.|

Il progetto di codice condiviso per ogni applicazione è costituito anche da alcuni file importanti:

|File|Scopo|
|--- |--- |
|Constants.cs|Classe `Constants`, che specifica le costanti utilizzate dall'applicazione per comunicare con il servizio Web. Queste costanti richiedono l'aggiornamento per accedere al servizio back-end personale creato in un provider.|
|ITextToSpeech.cs|Interfaccia `ITextToSpeech`, che specifica che il metodo di `Speak` deve essere fornito da qualsiasi classe di implementazione.|
|Todo.cs|Classe `App` responsabile della creazione di un'istanza della prima pagina che verrà visualizzata dall'applicazione in ogni piattaforma e della classe `TodoItemManager` utilizzata per richiamare le operazioni del servizio Web.|

### <a name="view-pages"></a>Visualizza pagine

La maggior parte delle applicazioni di esempio contiene almeno due pagine:

- **TodoListPage** : in questa pagina viene visualizzato un elenco di istanze di `TodoItem` e un'icona di segno di spunta se la proprietà `TodoItem.Done` è `true`. Facendo clic su un elemento si passa alla `TodoItemPage`. Inoltre, è possibile creare nuovi elementi facendo clic sul simbolo *+* .
- **TodoItemPage** : Questa pagina consente di visualizzare i dettagli relativi al `TodoItem` selezionato e di modificarli, salvarli, eliminarli e pronunciarli.

Inoltre, alcune applicazioni di esempio contengono ulteriori pagine utilizzate per gestire il processo di autenticazione utente.

### <a name="model-the-data"></a>Modellare i dati

Ogni applicazione di esempio utilizza la classe `TodoItem` per modellare i dati che vengono visualizzati e inviati al servizio Web per l'archiviazione. L'esempio di codice seguente visualizza la classe `TodoItem`:

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

La proprietà `ID` viene utilizzata per identificare in modo univoco ogni istanza di `TodoItem` e viene utilizzata da ogni servizio Web per identificare i dati da aggiornare o eliminare.

### <a name="invoke-web-service-operations"></a>Richiama le operazioni del servizio Web

È possibile accedere alle operazioni del servizio Web tramite la classe `TodoItemManager` e accedere a un'istanza della classe tramite la proprietà `App.TodoManager`. La classe `TodoItemManager` fornisce i metodi seguenti per richiamare le operazioni del servizio Web:

- **GetTasksAsync** : questo metodo viene usato per popolare il controllo `ListView` sul `TodoListPage` con le istanze di `TodoItem` recuperate dal servizio Web.
- **SaveTaskAsync** : questo metodo viene usato per creare o aggiornare un'istanza di `TodoItem` nel servizio Web.
- **DeleteTaskAsync** : questo metodo viene usato per eliminare un'istanza di `TodoItem` nel servizio Web.

Inoltre, alcune applicazioni di esempio contengono metodi aggiuntivi nella classe `TodoItemManager`, che vengono usati per gestire il processo di autenticazione utente.

Anziché richiamare direttamente le operazioni del servizio Web, i metodi `TodoItemManager` richiamano metodi su una classe dipendente inserita nel costruttore di `TodoItemManager`. Ad esempio, un'applicazione di esempio inserisce la classe `RestService` nel costruttore `TodoItemManager` per fornire l'implementazione che usa le API REST per accedere ai dati.

## <a name="related-links"></a>Collegamenti correlati

- [ASMX (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
