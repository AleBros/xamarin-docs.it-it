---
title: Utilizzo di tabelle e celle in Xamarin.iOS
description: Questo documento contiene collegamenti a diverse guide che descrivono come visualizzare i dati con il controllo UITableView in un'app Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/06/2016
ms.openlocfilehash: 7f5af84a8dfb9f774822e28e50cf8bbca9acf94b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021886"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>Utilizzo di tabelle e celle in Xamarin.iOS

Questa sezione presenta le classi usate per creare e visualizzare le tabelle, quindi fornisce esempi su come usarle in Xamarin.iOS. Verrà utilizzato l'aspetto predefinito per le tabelle, la personalizzazione del layout, l'implementazione della modifica e l'utilizzo di Novell iOS designer per progettare una tabella visivamente. In alcuni casi, la visualizzazione è ovviamente un elenco di righe (ad esempio l'app musica) e altre volte è difficile riconoscere il controllo tabella, ad esempio la modifica nell'app Contatti o una conversazione nell'app messaggi.

Per coloro che lavorano su applicazioni multipiattaforma con Xamarin.Android, il controllo UITableView è simile alla classe ListView in Android (e la classe UITableViewSource è simile alle classi adapter di Android).

In questi articoli verranno esaminati in maniera completa l'utilizzo delle tabelle, tra cui:

- **Parti della tabella** : introduzione e spiegazione degli elementi visivi del controllo `UITableView`. 
- **Visualizzazione di dati nelle tabelle** : viene illustrato come creare e popolare una tabella, utilizzare stili di tabelle e celle diversi ed evitare problemi di memoria rilasciando gli oggetti cella. 
- **Utilizzo avanzato** : creazione di celle personalizzate e utilizzo delle funzionalità di modifica della classe UITableView. 
- **Creazione visiva di una tabella** : utilizzo del Xamarin designer per iOS per creare un'interfaccia basata su tabelle con uno storyboard. 

## <a name="contents"></a>Contenuti

 [Funzionalità &amp; delle parti della tabella](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [Popolamento di una tabella con dati](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [Personalizzazione dell'aspetto di una tabella](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [Modifica](~/ios/user-interface/controls/tables/editing.md)

 [Azioni riga](~/ios/user-interface/controls/tables/row-action.md)

 [Creazione di tabelle in uno storyboard](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)

 [Ridimensionamento automatico dell'altezza delle righe](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
- [Tabelle negli storyboard (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable)
- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [Storyboard di una ricetta TableView](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [Introduzione a MonoTouch. Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Esempio TableEditing su GitHub](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Esempio TableParts su GitHub](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Esempio TableAndCellStyles su GitHub](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Riferimento alla classe UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [Riferimento alla classe UITableViewCell](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
