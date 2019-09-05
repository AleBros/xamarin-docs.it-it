---
title: Modelli comuni e idiomi in Novell. Mac
description: Questo documento descrive i modelli di progettazione comuni usati per la compilazione di app Novell. Mac. Vengono illustrati il modello MVC (Model-View-Controller), i modelli di origine dati e delegati e i protocolli.
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 06/17/2016
ms.openlocfilehash: 188bc8a04b62c97e9d6f80669fe50da1bf4d9340
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289543"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Modelli comuni e idiomi in Novell. Mac

In tutte le API Apple esposte tramite C#, determinati idiomi e modelli vengono riavviati più volte. Se si ha esperienza con la programmazione con Novell. iOS, questi possono avere un aspetto familiare. La documentazione si riferisce spesso a questi schemi e idiomi ripetutamente, quindi avere una conoscenza approfondita di questi concetti ti aiuterà a comprendere la documentazione che trovi.

## <a name="mvc---model-view-controller"></a>MVC-controller Visualizzazione modelli

Il controller di visualizzazione modelli, o MVC, è un modello molto comune trovato in tutto il cacao. Una discussione dettagliata esula dall'ambito di questo documento, ma in breve è un modo per strutturare l'applicazione in componenti:

- Gli oggetti **modello** rappresentano i dati sottostanti visualizzati e modificati (come gli indirizzi in una rubrica)
- **Visualizza** oggetti gestisce il disegno di un determinato oggetto sullo schermo e la gestione dell'interazione dell'utente (un campo di testo che mostra l'indirizzo sullo schermo)
- Gli oggetti **controller** gestiscono l'interazione tra il modello e la visualizzazione. Eseguono il push delle modifiche del modello "up" per aggiornare la visualizzazione e inserire le modifiche "inattive" dalla visualizzazione quando gli utenti apportano modifiche nell'interfaccia utente.

Se si ha familiarità con MVVM (Model View ViewModel) di altre librerie, ad esempio WPF, il controller funziona in modo simile al ViewModel, ma spesso è strettamente associato agli elementi dell'interfaccia utente specifici.

Ulteriori informazioni sono disponibili qui:

- [Learning MVC nel sito Web Apple](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Controller di visualizzazione modelli in Objective-C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [Utilizzo di Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>Origine dati/delegato/sottoclasse

Un altro modello molto comune in Cocoa riguarda la fornitura di dati a elementi dell'interfaccia utente e la reazione alle interazioni degli utenti. Utilizzando `NSTableView` come esempio, è necessario fornire i dati per ogni riga, un set di elementi dell'interfaccia utente che rappresentano tale riga, un set di comportamenti per rispondere alle interazioni dell'utente e possibilmente una certa quantità di personalizzazione. L'origine dati e i modelli delegati consentono di gestire la maggior parte dei casi senza dover `NSTableView` ricorrere a una sottoclasse.

- Alla `DataSource` proprietà viene assegnata un'istanza di una sottoclasse `NSTableViewDataSource` personalizzata di cui viene chiamato il metodo per popolare la tabella `GetRowCount` con `GetObjectValue`i dati (tramite e).

- Alla `Delegate` proprietà viene assegnata un'istanza di una sottoclasse `NSTableViewDelegate` personalizzata di che fornisce la visualizzazione per un determinato oggetto modello `GetViewForItem`(via) e gestisce le interazioni `DidClickTableColumn`dell' `MouseDownInHeaderOfTableColumn`interfaccia utente (via, e così via).

In alcuni casi, è necessario personalizzare un controllo in un modo oltre gli hook specificati nel delegato o nell'origine dati ed è possibile creare una sottoclasse della vista direttamente. Prestare attenzione, tuttavia, in molti casi, per eseguire l'override del comportamento predefinito, sarà necessario gestire tutto questo comportamento (la personalizzazione del comportamento di selezione potrebbe richiedere di implementare tutti i comportamenti di selezione).

In Novell. iOS alcune API, ad esempio `UITableView` , sono state estese con una proprietà che implementa sia il delegato che l'origine dati (`UITableViewSource`). Per aggirare la limitazione comune che una singola C# classe può avere una sola classe di base e l'emersione dei protocolli viene eseguita tramite le classi base.

Per altre informazioni sull'uso delle visualizzazioni tabella in un'applicazione Novell. Mac, vedere la documentazione di [visualizzazione tabella](~/mac/user-interface/table-view.md) .

## <a name="protocols"></a>Protocolli

I protocolli in Objective-C possono essere confrontati C#con le interfacce in e in molti casi vengono usati in situazioni analoghe. Ad esempio, `NSTableView` l'esempio precedente, sia il delegato che l'origine dati sono in realtà protocolli. Novell. Mac espone tali classi come classi base con i metodi virtuali di cui è possibile eseguire l'override. La differenza principale tra C# le interfacce e i protocolli Objective-C è che alcuni metodi in un protocollo possono essere facoltativi per l'implementazione. Sarà necessario esaminare la documentazione e/o la definizione di un'API per determinare cosa è facoltativo.

Per ulteriori informazioni, vedere la documentazione relativa [a delegati, protocolli ed eventi](~/ios/app-fundamentals/delegates-protocols-and-events.md) .



## <a name="related-links"></a>Collegamenti correlati

- [Viste tabella](~/mac/user-interface/table-view.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Delegati, protocolli ed eventi](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
