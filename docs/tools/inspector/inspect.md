---
title: Controllo delle applicazioni attive
description: Questo documento descrive come usare Xamarin Inspector per controllare le applicazioni. Illustra anche le limitazioni dello strumento Xamarin Inspector.
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2bd68def0a29d4bb94f8cc66c8cbfa00add1700d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103641"
---
# <a name="inspecting-live-applications"></a>Controllo delle applicazioni attive

L'ispezione delle app in tempo reale è disponibile per i clienti aziendali.

1. Aprire qualsiasi [progetto di app supportati](~/tools/inspector/install.md#supported-platforms) in Visual Studio per Mac o Visual Studio.
1. Eseguire l'app in modalità di debug.
1. Fare clic sui **Inspect** pulsante sulla barra degli strumenti dell'IDE (in Visual Studio, il **Inspect app corrente...**  voce di menu è disponibile anche il **Tools** o **Debug** menu).

[![](inspect-images/mac-heres-the-button.png "Fare clic sul pulsante Controlla sulla barra degli strumenti dell'IDE")](inspect-images/mac-heres-the-button.png#lightbox)

Verrà aperta una nuova finestra di client Xamarin Inspector, con un nuovo prompt REPL.

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "Verrà aperta una nuova finestra di client Xamarin Inspector, con un nuovo prompt REPL")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

Una volta che viene visualizzata questa finestra, è necessario un oggetto interattivo C# prompt dei comandi che è possibile usare per eseguire e valutare C# istruzioni ed espressioni. Ciò che rende univoco è che il codice viene valutato nel contesto del processo di destinazione. In questo caso, vengono mostrati il codice in esecuzione nei confronti dell'applicazione iOS visualizzato.

Tutte le modifiche apportate allo stato dell'applicazione si verificano effettivamente nel processo di destinazione, quindi è possibile usare C# per modificare l'applicazione in tempo reale, oppure è possibile controllare lo stato dell'applicazione in tempo reale.

Ad esempio, su iOS, potrebbe essere necessario individuare la classe delegata UIApplication, che è il nostro driver principale (in cui è archiviata una grande quantità di stato dell'applicazione):

    var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
    del.Database.GetAllCustomers ()
    ...
    del.Database.AddCustomer (...)

(Si noti che ogni invio si verifica in un editor a più righe. `Shift + Enter` verrà creata una nuova riga, e `Cmd + Enter` (`Ctrl + Enter` su Windows) invia il codice per la valutazione. `Enter` Invia automaticamente appena possibile.)

Un modo più pratico per ottenere gli elementi visivi dell'applicazione consiste nell'usare il pulsante "Controlla". Dopo aver premuto questo, è possibile selezionare un elemento dell'interfaccia utente, fare clic sull'applicazione. La variabile `selectedView` verrà assegnato in modo che punti all'elemento effettivo sullo schermo. Nella schermata precedente, è possibile visualizzare come si accede e quindi modificata `selectedView.BarTintColor` nella `UISearchBar` è avessimo scelto.

Sono molto utili anche l'albero elementi visivi attivi. Rappresenta lo snapshot corrente della gerarchia di visualizzazione. È possibile selezionare le righe da impostare `selectedView` in REPL e visualizzare i valori delle proprietà della vista. In Mac, è possibile interagire con una visualizzazione esplosa 3D delle viste su più livelli. In Windows, è possibile modificare i valori di proprietà di una vista in modo visivo.

## <a name="known-limitations"></a>Limitazioni note

 - Selezione della visualizzazione è supportata solo sullo schermo principale.
 - La modifica della griglia delle proprietà non è disponibile per Mac e in Windows è limitata per alcuni tipi di dati. Usare la replica per la modifica più potente.
 - Purché l'estensione o un componente aggiuntivo di Inspector è installata e abilitata nell'IDE, si inseriscono codice nell'app ogni volta che viene avviato in modalità di Debug. Se si nota un comportamento strano nell'app,, provare a disattivare o disinstallazione di Inspector addin/estensione, riavviare l'IDE e Ricontrollando le. E. [Segnala bug nei file](~/tools/inspector/install.md#reporting-bugs) per farci sapere!
 - Esaminare un elemento dell'interfaccia utente comporta un cambiamento ogni caso, consultare [segnalarlo](~/tools/inspector/install.md#reporting-bugs), come questo può indicare un bug.

