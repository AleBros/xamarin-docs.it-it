---
title: Modelli e idiomi in xamarin. Mac comuni
description: Questo documento descrive i modelli di progettazione comuni usati durante la compilazione di App xamarin. Mac. Viene descritto il modello model-view-controller, i modelli dei dati di origine e il delegato e i protocolli.
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 06/17/2016
ms.openlocfilehash: b4266582ce0cec522e207cd06987f2d0eeff8b2d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61090886"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Modelli e idiomi in xamarin. Mac comuni

In tutta l'API Apple esposte tramite c#, alcuni modelli e idiomi ad avviarsi in modo continuativo. Se si ha esperienza con la programmazione con xamarin. IOS, questi potrebbero apparire familiari. Documentazione spesso farà riferimento a questi modelli e idiomi più volte, in modo da avere una conoscenza approfondita di essi consentono di avere senso della documentazione che trova.

## <a name="mvc---model-view-controller"></a>MVC - Model View Controller

Model View Controller o MVC per brevità, è uno schema molto comune trovato tutta Cocoa. Una discussione dettagliata esula dall'ambito di questo documento, ma in breve è un modo strutturare l'applicazione in componenti:

- **Modello** oggetti rappresentano i dati sottostanti da visualizzato e modificato (ad esempio gli indirizzi in una rubrica)
- **Visualizzazione** oggetti gestire il disegno di un determinato oggetto sullo schermo e gestisce l'interazione dell'utente (un campo di testo che mostra l'indirizzo nella schermata)
- **Controller** oggetti gestiscono l'interazione tra il modello e la visualizzazione. Il push delle modifiche del modello "massimo" per aggiornare la visualizzazione e push "modifiche down" dalla vista quando gli utenti di apportare modifiche nell'interfaccia utente.

Se si ha familiarità con MVVM (Model View ViewModel) dalle altre raccolte, ad esempio WPF, il Controller funziona simile all'elemento ViewModel, ma spesso è più strettamente associato a elementi dell'interfaccia utente specifici.

Altri dettagli sono disponibili qui:

- [Apprendimento di MVC nel sito Web di Apple](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Model View Controller in Objective-C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [Utilizzo di Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>Origine dati / delegato / creazione di una sottoclasse

Un altro modello molto comune in Cocoa riguarda la fornitura di dati a elementi dell'interfaccia utente e reagire alle interazioni dell'utente. Usando `NSTableView` ad esempio, è necessario fornire in qualche modo i dati per ogni riga, alcuni set di elementi dell'interfaccia utente che rappresentano quella riga, alcuni set di comportamenti per reagire alle interazioni dell'utente e possibilmente certo livello di personalizzazione. I modelli di origine e il delegato di dati consentono di gestire la maggior parte dei casi senza dover ricorrere alla creazione di una sottoclasse `NSTableView` manualmente.

- Il `DataSource` proprietà viene assegnata un'istanza di una sottoclasse personalizzata della `NSTableViewDataSource` che viene chiamato per popolare la tabella con i dati (tramite `GetRowCount` e `GetObjectValue`).

- Il `Delegate` proprietà viene assegnata un'istanza di una sottoclasse personalizzata della `NSTableViewDelegate` che fornisce la visualizzazione per un oggetto modello specificato (tramite `GetViewForItem`) e gestisce le interazioni dell'interfaccia utente (tramite `DidClickTableColumn`, `MouseDownInHeaderOfTableColumn`e così via).

In alcuni casi, è opportuno personalizzare un controllo in modo oltre gli hook dato nel delegato o un'origine dati e si può creare una sottoclasse la visualizzazione direttamente. Prestare tuttavia attenzione, in molti casi, si esegue l'override predefinito comportamento verrà quindi richiesto di gestire tutto questo comportamento (personalizzazione del comportamento di selezione potrebbe essere necessario implementare manualmente tutti i comportamenti di selezione).

In xamarin. IOS, alcune API, ad esempio `UITableView` sono stati estesi con una proprietà che implementa il delegato sia l'origine dati (`UITableViewSource`). Questo reparto it di risolvere il limite più comune che un singolo C# classe può avere solo una classe di base e l'esposizione dei protocolli avviene tramite le classi di base.

Per altre informazioni sull'uso delle visualizzazioni tabella in un'applicazione xamarin. Mac, vedere la [vista tabella](~/mac/user-interface/table-view.md) documentazione.

## <a name="protocols"></a>Protocolli

Protocolli in Objective-C possono essere confrontati con le interfacce in C#e in molti casi vengono utilizzati in situazioni simili. Ad esempio il `NSTableView` esempio precedente, sia il delegato e l'origine dati sono effettivamente i protocolli. Xamarin. Mac espone queste informazioni come le classi di base con metodi virtuali, che è possibile eseguire l'override. La differenza principale tra C# le interfacce e protocolli di Objective-C è che alcuni metodi in un protocollo possono essere facoltativi per implementare. È possibile esaminare la documentazione e/o definizione di un'API per determinare ciò che è facoltativo.

Altre informazioni, vedere la [delegati, protocolli e gli eventi](~/ios/app-fundamentals/delegates-protocols-and-events.md) documentazione.



## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazioni di tabelle](~/mac/user-interface/table-view.md)
- [Utilizzo di windows](~/mac/user-interface/window.md)
- [I delegati, protocolli e gli eventi](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
