---
title: "Procedura dettagliata: Utilizzo di un elemento di JSON per creare un'interfaccia utente"
description: MonoTouch.Dialog (MT D) include il supporto per la generazione dell'interfaccia utente dinamica tramite i dati JSON. In questa esercitazione verrà esaminato come utilizzare un JSONElement per creare un'interfaccia utente da JSON che è incluso in un'applicazione o caricato da un Url remoto.
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 322857295383d17da03507bdd5ac78753f8c0619
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough-using-a-json-element-to-create-a-user-interface"></a>Procedura dettagliata: Utilizzo di un elemento di JSON per creare un'interfaccia utente

_MonoTouch.Dialog (MT D) include il supporto per la generazione dell'interfaccia utente dinamica tramite i dati JSON. In questa esercitazione verrà esaminato come utilizzare un JSONElement per creare un'interfaccia utente da JSON che è incluso in un'applicazione o caricato da un Url remoto._


MT D supporta la creazione di interfacce utente dichiarata in JSON. Quando gli elementi vengono dichiarati mediante JSON, MT D creerà gli elementi associati di automaticamente. JSON può essere caricato da un file locale, un analizzato `JsonObject` istanza o anche un Url remoto.

MT D supporta una vasta gamma di funzionalità che sono disponibili nell'API di elementi quando si utilizza JSON. Ad esempio, l'applicazione nella schermata seguente è completamente dichiarata mediante JSON:

[![](json-element-walkthrough-images/01-load-from-file.png "Ad esempio, l'applicazione in questa schermata è completamente dichiarato usando JSON") ](json-element-walkthrough-images/01-load-from-file.png#lightbox) [ ![ ] (json-element-walkthrough-images/01-load-from-file.png ", ad esempio, l'applicazione in questa schermata è completamente dichiarata mediante JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

Verrà nuovamente l'esempio dal [elementi API Walkthrough](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) esercitazione che illustra come aggiungere una schermata Dettagli attività usando JSON.

## <a name="json-walkthrough"></a>Procedura dettagliata JSON

Nell'esempio relativo a questa procedura dettagliata consente la creazione di attività. Quando un'attività è stata selezionata nella prima schermata, viene visualizzata una schermata di dettaglio come illustrato:

 [![](json-element-walkthrough-images/03-task-list.png "Quando un'attività è stata selezionata nella prima schermata, viene visualizzata una schermata di dettaglio, come illustrato")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>Creazione di JSON

In questo esempio si caricherà il codice JSON da un file di progetto denominato `task.json`. MT D prevede JSON sia conforme a una sintassi che rispecchia l'API di elementi. Come usare l'API di elementi dal codice, quando si utilizza JSON, vengono dichiarate nelle sezioni e si aggiungere elementi all'interno di tali sezioni. Per dichiarare le sezioni e gli elementi in JSON, è utilizzare le stringhe "sezioni" e "elementi" rispettivamente come chiavi. Per ogni elemento, il tipo di elemento associato viene impostato utilizzando il `type` chiave. Ogni altri elementi viene impostata con il nome della proprietà come chiave.

Ad esempio, il codice JSON seguente descrive le sezioni e gli elementi per i dettagli dell'attività:

```csharp
{
    "title": "Task",
    "sections": [
        {
          "elements" : [
            {
                "id" : "task-description",
                "type": "entry",
                "placeholder": "Enter task description"
            },
            {
                "id" : "task-duedate",
                "type": "date",
                "caption": "Due Date",
                "value": "00:00"
            }
         ]
        }
    ]
  }
```

Si noti la stringa JSON precedente includono un id per ogni elemento. Qualsiasi elemento può includere un id, per farvi riferimento in fase di esecuzione. Vedremo come questo viene utilizzato in un momento quando viene illustrato come caricare JSON nel codice.

 <a name="Loading_the_JSON_in_Code" />


## <a name="loading-the-json-in-code"></a>Il caricamento di JSON nel codice

Una volta definito l'oggetto JSON, è necessario caricarlo MT D utilizzando le `JsonElement` classe. Supponendo che un file con la stringa JSON creato in precedenza è stato aggiunto al progetto con il nome di sample.json e ha un'azione di compilazione del contenuto, il caricamento di `JsonElement` è semplice come chiamare la riga di codice seguente:

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Poiché si aggiungono su richiesta ogni volta che viene creata un'attività, è possibile modificare del pulsante scelto dall'esempio precedente gli elementi API come indicato di seguito:

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        _rootElement [0].Add (taskElement);
};
```

 <a name="Accessing_Elements_at_Runtime" />


## <a name="accessing-elements-at-runtime"></a>L'accesso agli elementi in fase di esecuzione

Tenere presente che è aggiunto un id a entrambi gli elementi quando li dichiarate nel file JSON. È possibile utilizzare la proprietà id per accedere a ogni elemento in fase di esecuzione per modificare le proprietà nel codice. Ad esempio, il codice seguente fa riferimento gli elementi in ingresso e data per impostare i valori dall'oggetto attività:

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        taskElement.Caption = task.Name;

        var description = taskElement ["task-description"] as EntryElement;

        if (description != null) {
                description.Caption = task.Name;
                description.Value = task.Description;       
        }

        var duedate = taskElement ["task-duedate"] as DateElement;

        if (duedate != null) {                
                duedate.DateValue = task.DueDate;
        }
        _rootElement [0].Add (taskElement);
};
```

 <a name="Loading_JSON_from_a_Url" />


## <a name="loading-json-from-a-url"></a>Il caricamento di JSON da un Url

MT D inoltre supporta in modo dinamico il caricamento JSON da un Url esterno semplicemente passando l'Url al costruttore del `JsonElement`. MT D verrà espandere la gerarchia dichiarata nel file JSON su richiesta durante la navigazione tra le schermate. Si consideri ad esempio un file JSON, ad esempio quello illustrato di seguito si trova nella radice del server web locale:

```csharp
{
    "type": "root",
    "title": "home",
    "sections": [
       {
         "header": "Nested view!",
         "elements": [
           {
             "type": "boolean",
             "caption": "Just a boolean",
             "id": "first-boolean",
             "value": false
           },
           {
             "type": "string",
             "caption": "Welcome to the nested controller"
           }
         ]
       }
     ]
}
```

È possibile caricare questa utilizzando il `JsonElement` come illustrato nel codice seguente:

```csharp
_rootElement = new RootElement ("Json Example"){
        new Section (""){ new JsonElement ("Load from url",
                "http://localhost/sample.json")
        }
};
```

In fase di esecuzione, il file verrà recuperato e analizzato da MT D quando l'utente passa alla seconda visualizzazione, come illustrato nella schermata seguente:

 [![](json-element-walkthrough-images/04-json-web-example.png "Il file verrà recuperato e analizzato da MT D quando l'utente passa alla seconda visualizzazione")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come creare un oggetto utilizzando l'interfaccia con MT D da JSON. Mostrato come caricare JSON incluso in un file con l'applicazione e da un Url remoto. Inoltre mostrato come accedere agli elementi descritti in JSON in fase di esecuzione.


## <a name="related-links"></a>Collegamenti correlati

- [MTDJsonDemo (sample)](https://developer.xamarin.com/samples/MTDJsonDemo/)
- [Screencast - Miguel de Icaza crea una schermata di accesso di iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - creare con facilità iOS interfacce utente con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introduzione a MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Procedura dettagliata di API di elementi](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procedura dettagliata di API di Reflection](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Finestra di dialogo MonoTouch su Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Applicazione TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Riferimento alla classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
