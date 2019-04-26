---
title: Utilizzo delle tabelle e celle di xamarin. IOS
description: Questo documento include collegamenti alle varie guide che descrivono come visualizzare i dati con il controllo UITableView in un'app xamarin. IOS.
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/06/2016
ms.openlocfilehash: 275c7553e465da67ca0780ea6aa9e986ca33b1f8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61379125"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>Utilizzo delle tabelle e celle di xamarin. IOS

In questa sezione vengono presentate le classi usate per creare e visualizzare tabelle, quindi vengono forniti esempi su come usarli in xamarin. IOS. Verrà coperto utilizzando l'aspetto predefinito per le tabelle, personalizzazione del layout, implementazione della modifica e l'uso di Xamarin iOS Designer progettare visivamente una tabella. In alcuni casi la visualizzazione è ovviamente un elenco di righe (ad esempio, l'app Music) e altre volte invece lo difficile da riconoscere il controllo della tabella (ad esempio modifica di una conversazione nell'app per i messaggi, o nell'app contatti).

Per coloro che lavorano su applicazioni multipiattaforma con xamarin. Android, il controllo UITableView è simile alla classe ListView in Android (e la classe UITableViewSource è simile a classi di adattatori di Android).

Questi articoli avrà un quadro completo di utilizzo delle tabelle, tra cui:

-   **Parti di tabella** – Introducing e spiegando gli elementi visivi del `UITableView` controllo. 
-   **Visualizzazione dei dati in tabelle** – che illustra come creare e popolare una tabella, usare diversi stili di tabella e di cella ed evitare i problemi di memoria tramite il riciclo degli oggetti di cella. 
-   **Uso avanzato** : creazione di celle personalizzate e usando le funzionalità di modifica della classe UITableView. 
-   **Creazione di una tabella in modo visivo** – usando la finestra di progettazione di Xamarin per iOS per creare un'interfaccia basati su tabelle con uno Storyboard. 

## <a name="contents"></a>Sommario

 [Parti di tabella &amp; funzionalità](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [Popolamento di una tabella con dati](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [Personalizzazione dell'aspetto di una tabella](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [Modifica](~/ios/user-interface/controls/tables/editing.md)
 
 [Azioni riga](~/ios/user-interface/controls/tables/row-action.md)

 [Creazione di tabelle in uno Storyboard](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [Ridimensionamento automatico dell'altezza delle righe](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables/)
- [Tabelle di storyboard (esempio)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [Storyboard una ricetta TableView](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [Introduzione a monotouch. Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Esempio di TableEditing su Github](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Esempio di TableParts su Github](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Esempio di TableAndCellStyles su Github](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Riferimento alla classe UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [Riferimento alla classe UITableViewCell](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
