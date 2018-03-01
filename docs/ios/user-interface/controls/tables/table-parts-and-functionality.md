---
title: "Funzionalità e le parti di tabella"
ms.topic: article
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 1a47cad858e4b0b362da18ebe58142ade2574be2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="table-parts-and-functionality"></a>Funzionalità e le parti di tabella

Un UITableView può avere uno stile 'raggruppato' o 'normale' ed è costituito dalle parti seguenti:

-  [Intestazione di sezione](#Section_Header)
-  [Celle](#Cells) (o righe, se si preferisce)
-  [Sezione piè di pagina](#Section_Footer)
-  [Index](#Index)
-  [Modalità di modifica](#Edit_Features) (include 'scorrere per eliminare' e trascinare i quadratini di ridimensionamento per modificare l'ordine delle righe) 


Questi screenshot mostrano la modalità di visualizzazione delle righe di sezione, intestazioni, piè di pagina, i controlli di modifica e l'indice.

 [ ![](table-parts-and-functionality-images/image1a.png "Questi screenshot Mostra la modalità di visualizzazione delle righe di sezione, intestazioni, piè di pagina, i controlli di modifica e l'indice")](table-parts-and-functionality-images/image1a.png)

Queste parti sono descritti in dettaglio più avanti:

 <a name="Section_Header" />


## <a name="section-header"></a>Intestazione di sezione

Celle possono facoltativamente essere raggruppate in sezioni, dotate di un'intestazione personalizzata e/o dotate di un piè di pagina. L'intestazione può essere impostata con un valore stringa o una vista personalizzata può essere fornita per consentire un layout diverso o uno stile.

 <a name="Cells" />


## <a name="cells"></a>Celle

Le celle sono l'elemento dell'interfaccia utente principale per una tabella. Quando implementato in modo corretto, celle vengono riutilizzate per l'efficienza della memoria. Esistono quattro stili di cella predefiniti ed è possibile creare la propria celle personalizzate: nel codice o nella finestra di progettazione quando si utilizzano gli storyboard.


## <a name="section-footer"></a>Sezione piè di pagina

Il piè di pagina sezione facoltativa può essere impostata con un valore stringa o una vista personalizzata può essere fornita per consentire un layout diverso o uno stile. Piè di pagina e le intestazioni di sezione possono essere impostati in modo indipendente.

 <a name="Index" />


## <a name="index"></a>Indice

L'indice viene visualizzato come una sequenza di caratteri verso il basso il bordo destro della tabella.
Toccare o trascinamento sull'indice accelera lo scorrimento a tale parte della tabella. Un indice è facoltativo, ma è consigliabile spostarsi lunghi elenchi. Un indice non è in genere utilizzato con lo stile raggruppato.

 <a name="Edit_Features" />


## <a name="editing-mode"></a>Modalità di modifica

Sono disponibili un paio di diverse funzionalità di modifica:

-  Scorrere rapidamente per eliminare singole celle.
-  Modalità di modifica per visualizzare pulsanti delete su ogni riga 
-  Modalità di modifica per visualizzare gli handle di ordinamento nuovamente. 
-  Inserimento di nuove celle (con animazione).


Il resto di questo documento viene illustrato come implementare tutte queste funzionalità UITableView con xamarin. IOS.

 <a name="Classes_Overview" />


## <a name="classes-overview"></a>Cenni preliminari sulle classi

Classi principali utilizzate per visualizzare le visualizzazioni di tabella sono illustrate qui:

 [ ![](table-parts-and-functionality-images/classdiagram.png "Classi principali utilizzate per visualizzare le visualizzazioni di tabella vengono visualizzate qui")](table-parts-and-functionality-images/classdiagram.png)

Lo scopo di ogni classe è descritta di seguito:

-   **UITableView** – una vista che contiene una raccolta di celle all'interno di un contenitore a scorrimento. Visualizzazione della tabella in genere utilizza l'intero schermo in un'app iPhone ma può esistere come parte di una visualizzazione più grande in un iPad (o vengono visualizzati in un popover). 
-   **UITableViewCell** – una vista che rappresenta una singola cella o riga, in una visualizzazione tabella. Sono disponibili quattro tipi di cella predefiniti ed è possibile creare celle personalizzate in c# o con progettazione iOS. 
-   **UITableViewSource** xamarin esclusivo: classe astratta che fornisce tutti i metodi necessari per visualizzare una tabella, incluso il numero di riga, la restituzione di una vista di cella per ogni riga, la gestione di selezione di riga e molte altre funzionalità facoltative. Si *deve* sottoclasse questa opzione per ottenere un lavoro UITableView. 
-   **NSIndexPath** -sezione e la riga contiene proprietà che identificano la posizione di una cella in una tabella. 
-   **UITableViewController** – un UIViewController pronto all'uso con l'impostazione hardcoded UITableView come la relativa visualizzazione e accessibile tramite la proprietà TableView. 
-   **UIViewController** : se la tabella non occupa l'intero schermo, è possibile aggiungere un UITableView per qualsiasi UIViewController con il relativo Frame impostare in modo appropriato. 


UITableViewSource sostituisce le due classi seguenti che sono ancora disponibili in xamarin. IOS, ma non sono richiesti:

-   **UITableViewDataSource** – Objective-C un protocollo modellate in xamarin. IOS come una classe astratta. Deve essere una sottoclasse per fornire una tabella con una visualizzazione per ogni cella, nonché informazioni sulle intestazioni, piè di pagina e il numero di righe e le sezioni della tabella. 
-   **UITableViewDelegate** – Objective-C un protocollo modellate in xamarin. IOS come una classe. Gestisce la selezione, la modifica di funzionalità e altre funzionalità di tabella facoltativi. 


In questo documento gli esempi utilizzano tutti UITableViewSource e ignorare queste due classi. Citate in questo caso poiché eventuali esempi Objective-C disponibili nella documentazione di Apple farà riferimento a essi, pertanto è utile comprendere le operazioni eseguite (e che è possibile utilizzare UITableViewSource del xamarin).


## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
