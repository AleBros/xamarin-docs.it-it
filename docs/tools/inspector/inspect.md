---
title: Controllo delle applicazioni in tempo reale
description: Visualizzare ed eseguire il debug dell'app in tempo reale
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: topgenorth
ms.author: toopge
ms.date: 03/29/2017
ms.openlocfilehash: 8bcdc5ac50a0f03086ad9e9c3265e3ce86b06704
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="inspecting-live-applications"></a>Controllo delle applicazioni in tempo reale

L'ispezione applicazione in tempo reale è disponibile per i clienti aziendali.


1. [Le cartelle di lavoro di installare Xamarin & controllo.](~/tools/inspector/install.md)

1. Aprire qualsiasi [supportati progetto di app](~/tools/inspector/install.md#supported-platforms) in Visual Studio per Mac o Visual Studio.
1. Eseguire l'app in modalità di debug.
1. Fare clic su di **controlla** sulla barra degli strumenti dell'IDE (in Visual Studio, il **app corrente controlla....**  voce di menu è disponibile anche la **strumenti** o **Debug** menu).



[![](inspect-images/mac-heres-the-button.png "Fare clic sul pulsante Controlla sulla barra degli strumenti dell'IDE")](inspect-images/mac-heres-the-button.png#lightbox)

Verrà aperta una nuova finestra di client di controllo di Xamarin, con una nuova richiesta REPL.

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "Verrà visualizzata una nuova finestra del client Xamarin controllo con una nuova richiesta REPL")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

Una volta che viene visualizzata questa finestra, è necessario un prompt c# interattivo che è possibile utilizzare per eseguire e valutare espressioni e istruzioni di c#. Ciò che rende unico è che il codice viene valutato nel contesto del processo di destinazione. In questo caso, viene mostrato il codice in esecuzione per l'applicazione iOS visualizzato.

Tutte le modifiche apportate allo stato dell'applicazione vengono effettivamente eseguite nel processo di destinazione, quindi è possibile usare c# per modificare l'applicazione in tempo reale o è possibile controllare lo stato dell'applicazione in tempo reale.

In iOS, ad esempio, potrebbe essere opportuno individuare la classe UIApplication delegato, ovvero il driver principale (in cui è memorizzare molti lo stato dell'applicazione):

    var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
    del.Database.GetAllCustomers ()
    ...
    del.Database.AddCustomer (...)

(Si noti che ogni invio si verifica in un editor di più righe. `Shift + Enter` verrà creata una nuova riga e `Cmd + Enter` (`Ctrl + Enter` in Windows) invia il codice per la valutazione. `Enter` Invia automaticamente quando è sicura.)

Un modo più pratico per ottenere gli elementi visivi dell'applicazione è tramite il pulsante "Controlla". Quando si preme, è possibile selezionare un elemento dell'interfaccia utente facendo clic sull'applicazione. La variabile `selectedView` verrà assegnato in modo da puntare all'elemento effettivo sullo schermo. Nella schermata precedente, è possibile vedere come si accede e quindi modificare `selectedView.BarTintColor` sul `UISearchBar` è stato selezionato.

È molto utile anche la struttura ad albero visuale in tempo reale. Rappresenta lo snapshot corrente della gerarchia di visualizzazione. È possibile selezionare le righe per impostare `selectedView` in REPL e visualizzare i valori delle proprietà della vista. In Mac, è possibile interagire con una visualizzazione esplosa 3D delle visualizzazioni a più livelli. In Windows, è possibile modificare i valori delle proprietà di una vista in modo visivo.

## <a name="known-limitations"></a>Limitazioni note

 - Selezione della visualizzazione è supportata solo sullo schermo principale.
 - Modifica della griglia delle proprietà non è disponibile per Mac e in Windows è limitata a pochi tipi di dati. Utilizzare la replica per la modifica più potente.
 - Purché il componente aggiuntivo/estensione di controllo è installato e abilitato in dell'IDE, ci stiamo inserimento di codice nell'app ogni volta che viene avviato in modalità di Debug. Se si nota un comportamento anomalo dell'App, provare a disabilitare o disinstallare il componente aggiuntivo/estensione di controllo, riavviare l'IDE e vengono effettuati nuovi controlli. E. [segnalare bug](~/tools/inspector/install.md#reporting-bugs) per farci sapere!
 - Se il controllo di un elemento dell'interfaccia utente comporta la modifica comunque, [segnalarlo](~/tools/inspector/install.md#reporting-bugs), come questo potrebbe indicare un bug.

