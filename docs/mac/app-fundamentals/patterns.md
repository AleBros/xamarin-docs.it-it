---
title: Modelli e idiomi in Xamarin.Mac comuni
description: Questo documento descrive i modelli di progettazione comuni utilizzati durante la compilazione di App Xamarin.Mac. Viene descritto il modello model-view-controller, i modelli di origine e il delegato di dati e i protocolli.
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/17/2016
ms.openlocfilehash: f6bba5575edf2dcbddbd354b590e03f9fed06291
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791537"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Modelli e idiomi in Xamarin.Mac comuni

In tutta l'API Apple esposte tramite c#, determinati idiomi e i pattern di avviarsi in modo continuativo. Se si ha esperienza con la programmazione con xamarin, questi possono essere noti. Documentazione spesso farà riferimento a questi modelli e idiomi più volte, in modo da avere una conoscenza approfondita di essi consentono di senso della documentazione di cui che trova.

## <a name="mvc---model-view-controller"></a>MVC - Model View Controller

Model View Controller o MVC in breve, è uno schema molto comune presente Cocoa. Una discussione dettagliata non rientra nell'ambito di questo documento, ma in breve è un modo per strutturare l'applicazione in componenti:

- **Modello** oggetti rappresentano i dati sottostanti da visualizzare e modificare (ad esempio gli indirizzi in una rubrica)
- **Visualizzazione** oggetti gestire il disegno di un determinato oggetto sullo schermo e gestisce l'interazione dell'utente (un campo di testo che mostra l'indirizzo nella schermata)
- **Controller** oggetti gestire l'interazione tra il modello e la visualizzazione. Essi push delle modifiche al modello "massimo" per aggiornare la visualizzazione e push "delle modifiche giù" dalla vista quando gli utenti di apportare modifiche nell'interfaccia utente.

Se si ha familiarità con MVVM (Model View ViewModel) dalle altre raccolte, ad esempio WPF, il Controller funziona simile per l'elemento ViewModel ma spesso è più strettamente associato a elementi dell'interfaccia utente specifici.

Ulteriori dettagli sono disponibili qui:

- [Apprendimento MVC nel sito Web di Apple](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Model View Controller in Objective-C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [Utilizzo di Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>Origine dati / delegato / creazione di sottoclassi

Un altro schema molto comune in Cocoa riguarda la fornitura di dati a elementi dell'interfaccia utente e la reazione alle interazioni dell'utente. Utilizzando `NSTableView` ad esempio, è necessario fornire in qualche modo i dati per ogni riga, alcuni set di elementi dell'interfaccia utente che rappresenta quella riga, alcuni set di comportamenti reagire alle interazioni dell'utente e possibilmente tempo di personalizzazione. I modelli di origine e il delegato dati consentono di gestire la maggior parte dei casi senza dover ricorrere alla creazione di una sottoclasse `NSTableView` manualmente.

- Il `DataSource` proprietà viene assegnata un'istanza di una sottoclasse personalizzata di `NSTableViewDataSource` che viene chiamato per popolare la tabella con i dati (tramite `GetRowCount` e `GetObjectValue`).

- Il `Delegate` proprietà viene assegnata un'istanza di una sottoclasse personalizzata di `NSTableViewDelegate` che fornisce la visualizzazione per un oggetto modello specificato (tramite `GetViewForItem`) e gestisce le interazioni dell'interfaccia utente (tramite `DidClickTableColumn`, `MouseDownInHeaderOfTableColumn`e così via).

In alcuni casi, è opportuno personalizzare un controllo in modo oltre gli hook fornito con il delegato o un'origine dati ed è possibile creare una sottoclasse la vista direttamente. Prestare attenzione, in molti casi, si esegue l'override predefinito comportamento verrà quindi richiesto di gestire tutto tale comportamento (personalizzazione del comportamento di selezione potrebbe essere necessario implementare tutti i comportamenti di selezione).

In xamarin. IOS, alcune API, ad esempio `UITableView` sono stati estesi con una proprietà che implementa il delegato sia l'origine dati (`UITableViewSource`). Per aggirare la limitazione comune che un'unica classe c# può avere solo una classe di base e la replica dei protocolli ciò tramite le classi di base.

Per ulteriori informazioni sull'utilizzo di viste di tabella in un'applicazione Xamarin.Mac, consultare il nostro [tabella vista](~/mac/user-interface/table-view.md) documentazione.

## <a name="protocols"></a>Protocolli

Protocolli in Objective-C possono essere confrontati con le interfacce in c# e in molti casi vengono utilizzati in situazioni analoghe. Ad esempio il `NSTableView` esempio precedente, sia il delegato e l'origine dati sono effettivamente protocolli. Xamarin.Mac espone queste informazioni come le classi di base con metodi virtuali, che è possibile eseguire l'override. La differenza principale tra i protocolli di Objective-C e c# interfacce è che alcuni metodi di un protocollo possono essere facoltativi per implementare. È necessario esaminare la documentazione e/o la definizione di un'API per determinare ciò che è facoltativo.

Altre informazioni, vedere il nostro [delegati, protocolli e gli eventi](~/ios/app-fundamentals/delegates-protocols-and-events.md) documentazione.



## <a name="related-links"></a>Collegamenti correlati

- [Viste delle tabelle](~/mac/user-interface/table-view.md)
- [Utilizzo di windows](~/mac/user-interface/window.md)
- [Delegati, protocolli e gli eventi](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
