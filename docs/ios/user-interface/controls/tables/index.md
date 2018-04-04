---
title: Utilizzo di tabelle e le celle
description: Visualizzazione di dati tramite UITableView con xamarin
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/06/2016
ms.openlocfilehash: a1cda3632a75c7e462e763a34fdb5b586237b670
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-tables-and-cells"></a>Utilizzo di tabelle e le celle


In questa sezione vengono descritte le classi usate per creare e visualizzare le tabelle, quindi vengono forniti esempi su come utilizzarle in xamarin. IOS. Che verrà applicata mediante l'aspetto predefinito per le tabelle, personalizzazione del layout, l'implementazione di modifica e l'utilizzo di Xamarin iOS finestra di progettazione per progettare visivamente una tabella. In alcuni casi la visualizzazione è ovviamente un elenco di righe (ad esempio, l'app musica), mentre altre volte che è difficile riconoscono il controllo tabella (ad esempio la modifica, l'app dei contatti o una conversazione in app di messaggi).

Per gli utenti lavorano su applicazioni multipiattaforma con xamarin, il controllo UITableView è simile alla classe ListView in Android e la classe UITableViewSource è simile a classi di adattatori di Android.

Questi articoli verranno un quadro completo utilizzo delle tabelle, tra cui:

-   **Tabella parti** : Introduzione alla e descrivere gli elementi visivi del `UITableView` controllo. 
-   **La visualizzazione dei dati nelle tabelle** : illustra come creare e popolare una tabella, utilizzare i diversi stili di tabella e di cella e per evitare problemi di memoria da riciclare gli oggetti di cella. 
-   **Utilizzo avanzato** : compilazione di celle personalizzate e utilizzando le funzionalità di modifica della classe UITableView. 
-   **Creazione di una tabella in modo visivo** : utilizzo di progettazione Xamarin per iOS per creare un'interfaccia basati su tabelle con uno Storyboard. 


## <a name="contents"></a>Sommario

 [Tabella parti &amp; funzionalità](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [Popolamento di una tabella con dati](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [Personalizzazione dell'aspetto di una tabella](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [Modifica](~/ios/user-interface/controls/tables/editing.md)
 
 [Azioni di riga](~/ios/user-interface/controls/tables/row-action.md)

 [Creazione di tabelle in uno Storyboard](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [Ridimensionamento automatico dell'altezza delle righe](~/ios/user-interface/controls/tables/autosizing-row-height.md)


## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables/)
- [Tabelle di storyboard (esempio)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [Storyboard una Recipe TableView](https://developer.xamarin.com/recipes/ios/general/storyboard/storyboard_a_tableview)
- [Introduzione a MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Esempio di TableEditing in Github](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Esempio di TableParts in Github](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Esempio di TableAndCellStyles in Github](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Riferimento alla classe UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [Riferimento alla classe UITableViewCell](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
