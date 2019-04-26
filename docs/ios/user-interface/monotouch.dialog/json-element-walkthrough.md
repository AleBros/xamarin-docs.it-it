---
title: Uso di JSON per creare un'interfaccia utente in xamarin. IOS
description: Monotouch. Dialog (MT. D) include il supporto per la generazione dell'interfaccia utente dinamica tramite i dati JSON. In questa esercitazione illustra come usare un JSONElement per creare un'interfaccia utente da JSON a cui è incluso in un'applicazione o caricato da un Url remoto.
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 07bcbfe046fd689e08eed4e64495b56d083ceeb8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304473"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>Uso di JSON per creare un'interfaccia utente in xamarin. IOS

_Monotouch. Dialog (MT. D) include il supporto per la generazione dell'interfaccia utente dinamica tramite i dati JSON. In questa esercitazione illustra come usare un JSONElement per creare un'interfaccia utente da JSON a cui è incluso in un'applicazione o caricato da un Url remoto._

MT. 1!d supporta la creazione di interfacce utente dichiarata in JSON. Quando gli elementi vengono dichiarati usando JSON, MT. 1!d creerà gli elementi associati per l'utente automaticamente. Il file JSON possono essere caricati da un file locale, un analizzato `JsonObject` istanza o anche un Url remoto.

MT. 1!d supporta l'intera gamma di funzionalità disponibili nell'API di elementi quando si utilizza JSON. Ad esempio, l'applicazione nella schermata seguente è completamente dichiarata usando JSON:

[![](json-element-walkthrough-images/01-load-from-file.png "Ad esempio, l'applicazione in questo screenshot viene completamente dichiarata usando JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox) [![](json-element-walkthrough-images/01-load-from-file.png ", ad esempio, l'applicazione in questo screenshot viene completamente dichiarata usando JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

Rivediamo l'esempio dal [procedura dettagliata sull'API elementi](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) esercitazione che illustra come aggiungere una schermata dei dettagli attività usando JSON.

## <a name="setting-up-mtd"></a>Configurazione di MT. 1!D

MT. 1!d viene distribuito con xamarin. IOS. Per usarlo, fare clic sui **riferimenti** nodo di xamarin. IOS del progetto in Visual Studio 2017 o Visual Studio per Mac e aggiungere un riferimento al **monotouch. Dialog-1** assembly. Aggiungere quindi `using MonoTouch.Dialog` istruzioni presenti nell'origine del codice in base alle esigenze.

## <a name="json-walkthrough"></a>Procedura dettagliata JSON

L'esempio per questa procedura dettagliata consente la creazione di attività. Quando un'attività è selezionata nella prima schermata, una schermata dei dettagli viene presentata come illustrato:

 [![](json-element-walkthrough-images/03-task-list.png "Quando un'attività è selezionata nella prima schermata, viene visualizzata una schermata Dettagli come mostrato")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>Creazione di file JSON

In questo esempio si caricherà il file JSON da un file nel progetto denominato `task.json`. MT. 1!d prevede che il codice JSON di conformarsi a una sintassi che rispecchia l'API Elements. Come usare l'API di elementi di codice, quando si utilizza JSON, verrà dichiarata la sezioni e abbiamo aggiungere elementi all'interno di queste sezioni. Per dichiarare le sezioni e gli elementi in formato JSON, utilizziamo le stringhe "sezioni" e "elementi" rispettivamente come chiavi. Per ogni elemento, il tipo di elemento associato viene impostato utilizzando il `type` chiave. Tutte le altre proprietà elementi è impostata con il nome della proprietà come chiave.

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

Si noti che il codice JSON precedente include un id per ogni elemento. Qualsiasi elemento può includere un id, per farvi riferimento in fase di esecuzione. Si vedrà come viene usato più avanti quando viene illustrato come caricare il file JSON nel codice.

## <a name="loading-the-json-in-code"></a>Il caricamento di file JSON nel codice

Dopo aver definito il file JSON, è necessario caricarli in MT. D utilizzo il `JsonElement` classe. Supponendo che un file con il codice JSON creato in precedenza è stato aggiunto al progetto con il nome Sample. JSON e ha un'azione di compilazione del contenuto, il caricamento di `JsonElement` è semplice come richiamare la riga di codice seguente:

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Poiché aggiungeremo su richiesta ogni volta che viene creata un'attività, è possibile modificare il pulsante scelto dall'esempio di API di elementi precedenti nel modo seguente:

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>L'accesso agli elementi in fase di esecuzione

È importante ricordare che abbiamo aggiunto un id per entrambi gli elementi quando essi è dichiarato nel file JSON. È possibile usare la proprietà id per accedere a ogni elemento in fase di esecuzione per modificare le proprietà nel codice. Ad esempio, il codice seguente fa riferimento gli elementi di data e voce per impostare i valori dall'oggetto attività:

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

## <a name="loading-json-from-a-url"></a>Il caricamento di JSON da un url

MT. 1!d supporta anche il caricamento dinamico delle JSON da un Url esterno semplicemente passandone l'Url al costruttore del `JsonElement`. MT. 1!d espanderà gerarchia dichiarata nel file JSON su richiesta durante lo spostamento tra le schermate. Si consideri ad esempio un file JSON, ad esempio quello riportato di seguito che si trova nella radice del server web locale:

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

È possibile caricare questo usando il `JsonElement` come nel codice seguente:

```csharp
_rootElement = new RootElement ("Json Example"){
        new Section (""){ new JsonElement ("Load from url",
                "http://localhost/sample.json")
        }
};
```

In fase di esecuzione, il file verrà recuperato e analizzato da MT. 1!d quando l'utente passa alla seconda visualizzazione, come illustrato nello screenshot seguente:

 [![](json-element-walkthrough-images/04-json-web-example.png "Il file verrà recuperato e analizzato da MT. 1!d quando l'utente passa alla seconda visualizzazione")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare una con interfaccia con MT. 1!d da JSON. È stato illustrato come caricare JSON incluso in un file con l'applicazione, oltre che da un Url remoto. È stato anche illustrato come accedere agli elementi descritti in JSON in fase di esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [MTDJsonDemo (sample)](https://developer.xamarin.com/samples/MTDJsonDemo/)
- [Screencast - Miguel de Icaza consente di creare una schermata di accesso di iOS con monotouch. Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - facilmente creare interfacce utente iOS con monotouch. Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introduzione a monotouch. Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Procedura dettagliata sull'API di elementi](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procedura dettagliata sull'API di Reflection](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Finestra di dialogo di MonoTouch su Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation Application](https://github.com/migueldeicaza/TweetStation)
- [Riferimento alla classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
