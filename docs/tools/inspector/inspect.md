---
title: Ispezione delle applicazioni in tempo reale
description: In questo documento viene descritto come utilizzare il Xamarin Inspector per esaminare le applicazioni. Vengono inoltre illustrate le limitazioni dello strumento Xamarin Inspector.
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: bbb1e21139b5f073e2cc7e3d4781e8bc38334449
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006311"
---
# <a name="inspecting-live-applications"></a>Ispezione delle applicazioni in tempo reale

Per i clienti aziendali è disponibile l'ispezione di app in tempo reale.

1. Aprire qualsiasi [progetto di app supportato](~/tools/inspector/install.md#supported-platforms) in Visual Studio per Mac o Visual Studio.
1. Eseguire l'app in modalità di debug.
1. Fare clic sul pulsante **Controlla** sulla barra degli strumenti dell'IDE (in Visual Studio la voce di menu **Controlla app corrente...** è disponibile anche nel menu **strumenti** o **debug** ).

[![](inspect-images/mac-heres-the-button.png "Click the Inspect button in the IDE toolbar")](inspect-images/mac-heres-the-button.png#lightbox)

Verrà aperta una nuova finestra del client di Xamarin Inspector con un prompt REPL aggiornato.

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "A new Xamarin Inspector client window will open, with a fresh REPL prompt")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

Una volta visualizzata questa finestra, è disponibile un C# prompt interattivo che è possibile utilizzare per eseguire e C# valutare le istruzioni e le espressioni. Ciò rende univoco il fatto che il codice viene valutato nel contesto del processo di destinazione. In questo caso, viene mostrato il codice in esecuzione nell'applicazione iOS visualizzata.

Tutte le modifiche apportate allo stato dell'applicazione si verificano effettivamente nel processo di destinazione, quindi è possibile utilizzare C# per modificare l'applicazione in tempo reale oppure controllare lo stato dell'applicazione in tempo reale.

Ad esempio, in iOS, potrebbe essere necessario individuare la classe delegata UIApplication, che è il driver principale (in cui viene archiviato un gran numero di stato dell'applicazione):

```csharp
var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
del.Database.GetAllCustomers ()
...
del.Database.AddCustomer (...)
```

Si noti che ogni invio viene eseguito in un editor su più righe. `Shift + Enter` creerà una nuova riga e `Cmd + Enter` (`Ctrl + Enter` in Windows) invierà il codice per la valutazione. `Enter` invia automaticamente quando è sicuro).

Un modo più pratico per ottenere gli elementi visivi dell'applicazione consiste nell'usare il pulsante "ispeziona". Quando si preme questo pulsante, è possibile selezionare un elemento dell'interfaccia utente facendo clic sull'applicazione. La variabile `selectedView` verrà assegnata in modo da puntare all'elemento effettivo sullo schermo. Nella schermata precedente è possibile vedere come è stato effettuato l'accesso e quindi modificato `selectedView.BarTintColor` nel `UISearchBar` selezionato.

Anche l'albero elementi visivi attivi è molto utile. Rappresenta lo snapshot corrente della gerarchia di visualizzazione. È possibile selezionare le righe da impostare `selectedView` in REPL e per visualizzare i valori delle proprietà della visualizzazione. In Mac è possibile interagire con una visualizzazione esplosa 3D delle visualizzazioni a più livelli. In Windows è possibile modificare visivamente i valori delle proprietà di una visualizzazione.

## <a name="known-limitations"></a>Limitazioni note

- La selezione della vista è supportata solo nella visualizzazione principale.
- La modifica della griglia delle proprietà non è disponibile per Mac e in Windows è limitata a alcuni tipi di dati. Usare REPL per apportare modifiche più potenti.
- Fino a quando l'estensione o il componente aggiuntivo di Inspector viene installato e abilitato nell'IDE, il codice viene inserito nell'app ogni volta che viene avviato in modalità di debug. Se si nota un comportamento anomalo nell'app, provare a disabilitare o disinstallare l'estensione o il componente aggiuntivo per il controllo, riavviare l'IDE e ricontrollarlo. E inviare i [bug](~/tools/inspector/install.md#reporting-bugs) per segnalarli.
- Se il controllo di un elemento dell'interfaccia utente ne comporta la modifica in qualsiasi [momento, è](~/tools/inspector/install.md#reporting-bugs)possibile segnalarlo, perché questo potrebbe indicare un bug.
