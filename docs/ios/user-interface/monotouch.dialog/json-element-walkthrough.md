---
title: Uso di JSON per creare un'interfaccia utente in Xamarin.iOS
description: MonoTouch. Dialog (MT. D) include il supporto per la generazione dinamica dell'interfaccia utente tramite dati JSON. Questa esercitazione illustra come usare un oggetto JSONelement per creare un'interfaccia utente da JSON inclusa in un'applicazione o caricata da un URL remoto.
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: ad2386d912dba28041c02c4fb4a8046d341a85ed
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002262"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>Uso di JSON per creare un'interfaccia utente in Xamarin.iOS

_MonoTouch. Dialog (MT. D) include il supporto per la generazione dinamica dell'interfaccia utente tramite dati JSON. Questa esercitazione illustra come usare un oggetto JSONelement per creare un'interfaccia utente da JSON inclusa in un'applicazione o caricata da un URL remoto._

Mt. D supporta la creazione di interfacce utente dichiarate in JSON. Quando gli elementi vengono dichiarati usando JSON, MT. D creerà automaticamente gli elementi associati. Il formato JSON può essere caricato da un file locale, da un'istanza di `JsonObject` analizzata o anche da un URL remoto.

Mt. D supporta la gamma completa di funzionalità disponibili nell'API degli elementi quando si usa JSON. Ad esempio, l'applicazione nello screenshot seguente è completamente dichiarata usando JSON:

[![](json-element-walkthrough-images/01-load-from-file.png "Ad esempio, l'applicazione in questa schermata è stata dichiarata completamente usando JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)[![](json-element-walkthrough-images/01-load-from-file.png "Ad esempio, l'applicazione in questa schermata è stata dichiarata completamente usando JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

Verrà ora rivisitato l'esempio dell'esercitazione relativa alla [procedura dettagliata dell'API Elements](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) , che illustra come aggiungere una schermata dei dettagli delle attività usando JSON.

## <a name="setting-up-mtd"></a>Configurazione di MT. D

Mt. D viene distribuito con Xamarin.iOS. Per usarlo, fare clic con il pulsante destro del mouse sul nodo **riferimenti** di un progetto Xamarin.iOS in Visual Studio 2017 o Visual Studio per Mac e aggiungere un riferimento all'assembly **MonoTouch. Dialog-1** . Aggiungere quindi `using MonoTouch.Dialog` istruzioni nel codice sorgente in base alle esigenze.

## <a name="json-walkthrough"></a>Procedura dettagliata JSON

L'esempio per questa procedura dettagliata consente la creazione di attività. Quando si seleziona un'attività nella prima schermata, viene visualizzata una schermata dei dettagli, come illustrato:

 [![](json-element-walkthrough-images/03-task-list.png "When a task is selected on the first screen, a detail screen is presented as shown")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>Creazione di JSON

Per questo esempio, il codice JSON verrà caricato da un file nel progetto denominato `task.json`. Mt. D prevede che JSON sia conforme a una sintassi che rispecchia l'API degli elementi. Analogamente all'uso dell'API Elements dal codice, quando si usa JSON, si dichiarano le sezioni e all'interno di queste sezioni si aggiungono elementi. Per dichiarare sezioni ed elementi in JSON, vengono usate rispettivamente le stringhe "sections" e "elements" come chiavi. Per ogni elemento, il tipo di elemento associato viene impostato utilizzando la chiave `type`. Ogni altra proprietà Elements viene impostata con il nome della proprietà come chiave.

Il codice JSON seguente, ad esempio, descrive le sezioni e gli elementi per i dettagli dell'attività:

```json
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

Si noti che il codice JSON precedente include un ID per ogni elemento. Qualsiasi elemento può includere un ID, per farvi riferimento in fase di esecuzione. Vedremo come viene usato in un momento in cui viene illustrato come caricare il codice JSON nel codice.

## <a name="loading-the-json-in-code"></a>Caricamento del codice JSON nel codice

Una volta definito il codice JSON, è necessario caricarlo in MT. D uso della classe `JsonElement`. Supponendo che un file con il codice JSON creato in precedenza sia stato aggiunto al progetto con il nome Sample. JSON e data un'azione di compilazione del contenuto, il caricamento del `JsonElement` è semplice quanto la chiamata alla riga di codice seguente:

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Poiché questa operazione viene aggiunta su richiesta ogni volta che viene creata un'attività, è possibile modificare il pulsante selezionato dall'esempio di API degli elementi precedenti, come indicato di seguito:

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>Accesso agli elementi in fase di esecuzione

Si ricordi che è stato aggiunto un ID a entrambi gli elementi quando sono stati dichiarati nel file JSON. È possibile usare la proprietà ID per accedere a ogni elemento in fase di esecuzione per modificarne le proprietà nel codice. Il codice seguente, ad esempio, fa riferimento agli elementi entry e date per impostare i valori dall'oggetto attività:

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

## <a name="loading-json-from-a-url"></a>Caricamento di JSON da un URL

Mt. D supporta anche il caricamento dinamico di JSON da un URL esterno semplicemente passando l'URL al costruttore del `JsonElement`. Mt. D espande la gerarchia dichiarata in JSON su richiesta mentre si naviga tra le schermate. Si consideri, ad esempio, un file JSON come quello riportato di seguito nella radice del server Web locale:

```json
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

È possibile caricarla usando il `JsonElement` come nel codice seguente:

```csharp
_rootElement = new RootElement ("Json Example") {
    new Section ("") {
        new JsonElement ("Load from url", "http://localhost/sample.json")
    }
};
```

In fase di esecuzione, il file verrà recuperato e analizzato da MT. D quando l'utente passa alla seconda visualizzazione, come illustrato nella schermata seguente:

 [![](json-element-walkthrough-images/04-json-web-example.png "The file will be retrieved and parsed by MT.D when the user navigates to the second view")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un usando l'interfaccia con MT. D da JSON. È stato illustrato come caricare JSON incluso in un file con l'applicazione e da un URL remoto. È stato inoltre illustrato come accedere agli elementi descritti in JSON in fase di esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [MTDJsonDemo (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdjsondemo)
- [Introduzione a MonoTouch. Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Procedura dettagliata per l'API elementi](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procedura dettagliata dell'API Reflection](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Finestra di dialogo MonoTouch su GitHub](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Applicazione TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Riferimento alla classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
