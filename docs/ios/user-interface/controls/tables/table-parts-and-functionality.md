---
title: Parti di tabella e le funzionalità di xamarin. IOS
description: Questo documento descrive le varie parti di un UITableView in iOS. Illustra le intestazioni di sezione, le celle, sezione piè di pagina, l'indice e la modalità di modifica.
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c4d788cce12a9aabdd1170cd1a52915f3b30285f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200511"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>Parti di tabella e le funzionalità di xamarin. IOS

Un UITableView può avere uno stile 'raggruppato' o 'normale' ed è costituito dalle parti seguenti:

-  [Intestazione di sezione](#Section_Header)
-  [Le celle](#Cells) (o le righe, se si preferisce)
-  [Sezione piè di pagina](#Section_Footer)
-  [Index](#Index)
-  [Modalità di modifica](#Edit_Features) (include 'scorrere rapidamente per eliminare' e trascinare i quadratini di ridimensionamento per modificare l'ordine delle righe) 

Questi screenshot mostrano come vengono visualizzati le righe di sezione, le intestazioni, piè di pagina, i controlli di modifica e l'indice.

 [![](table-parts-and-functionality-images/image1a.png "Questi screenshot mostrano come vengono visualizzati le righe di sezione, le intestazioni, piè di pagina, i controlli di modifica e l'indice")](table-parts-and-functionality-images/image1a.png#lightbox)

Queste parti sono descritti in dettaglio più avanti:

<a name="Section_Header" />

## <a name="section-header"></a>Intestazione di sezione

Celle possono facoltativamente essere raggruppate in sezioni, etichettate con un'intestazione personalizzata e/o con l'etichetta con un piè di pagina. L'intestazione può essere impostata con un valore stringa o una vista personalizzata può essere fornita per consentire un layout diverso o uno stile.

<a name="Cells" />

## <a name="cells"></a>Celle

Le celle sono l'elemento dell'interfaccia utente principale per una tabella. Se implementata correttamente, le celle vengono riutilizzate per l'efficienza della memoria. Esistono quattro stili di cella predefiniti ed è possibile creare il proprio le celle personalizzate: nel codice o nella finestra di progettazione quando si usano gli storyboard.

<a name="Section_Footer"/>

## <a name="section-footer"></a>Sezione piè di pagina

Il piè di pagina sezione facoltativa può essere impostato con un valore stringa o una vista personalizzata può essere fornita per consentire un layout diverso o uno stile. Sezione intestazioni e piè di pagina può essere impostato in modo indipendente.

<a name="Index" />

## <a name="index"></a>Indice

L'indice viene visualizzata come una sequenza di caratteri verso il basso il bordo destro della tabella.
Toccare o il trascinamento sull'indice accelera lo scorrimento a tale parte della tabella. Un indice è facoltativo, ma è quindi consigliabile spostarsi lunghi elenchi. Un indice non viene in genere usato con lo stile raggruppato.

<a name="Edit_Features" />

## <a name="editing-mode"></a>Modalità di modifica

Sono disponibili un paio di diverse funzionalità di modifica:

- Scorrere rapidamente per eliminare le singole celle.
- Modalità di modifica per visualizzare i pulsanti di eliminazione su ogni riga 
- Immissione filestreamstorelibrary. modalità di modifica per visualizzare gli handle di ordinamento nuovamente. 
- Inserimento di nuove celle (con animazione).

Il resto di questo documento viene illustrato come implementare tutte queste funzionalità UITableView con xamarin. IOS.


## <a name="classes-overview"></a>Cenni preliminari sulle classi

Le classi primarie utilizzate per visualizzare le visualizzazioni di tabella sono illustrate qui:

[![](table-parts-and-functionality-images/classdiagram.png "Le classi primarie utilizzate per visualizzare le visualizzazioni di tabella vengono visualizzate qui")](table-parts-and-functionality-images/classdiagram.png#lightbox)

Lo scopo di ogni classe è il seguente:

- **UITableView** – una vista che contiene una raccolta di celle all'interno di un contenitore scorrevole. Visualizzazione della tabella viene generalmente utilizzata l'intera schermata in un'app per iPhone, ma possono esistere come parte di una visualizzazione più grande in un iPad (o vengono visualizzati nel popover). 
- **UITableViewCell** – una visualizzazione che rappresenta una cella (riga singola o) in una visualizzazione tabella. Esistono quattro tipi di cella predefiniti ed è possibile creare le celle personalizzate sia in C# o con iOS Designer. 
- **UITableViewSource** esclusivo di xamarin. ios: classe astratta che fornisce tutti i metodi necessari per visualizzare una tabella, incluso il numero di riga, che restituisce una cella di visualizzazione per ogni riga, la gestione di selezione di riga e molte altre funzionalità facoltative. Si *necessario* sottoclasse questa opzione per ottenere un lavoro UITableView. 
- **NSIndexPath** -Contains riga e della sezione proprietà che identificano la posizione di una cella in una tabella. 
- **UITableViewController** : una classe UIViewController pronti da usare con un hardcoded UITableView come relativa visualizzazione e accessibili tramite la proprietà TableView. 
- **UIViewController** : se la tabella non occupa l'intera schermata è possibile aggiungere un UITableView a qualsiasi UIViewController relativo frame di impostare in modo appropriato. 

UITableViewSource sostituisce le due classi seguenti, che sono ancora disponibili in xamarin. IOS, ma non sono richiesti:

- **UITableViewDataSource** – protocollo An Objective-C che è modellata in xamarin. IOS come una classe astratta. Deve essere una sottoclasse per fornire una tabella con una visualizzazione per ogni cella, nonché informazioni sulle intestazioni, piè di pagina e il numero di righe e le sezioni della tabella. 
- **UITableViewDelegate** – protocollo An Objective-C che è modellata in xamarin. IOS come classe. Gestisce la selezione, funzionalità di modifica e altre funzionalità di tabella facoltativi. 

In questo documento gli esempi utilizzano UITableViewSource e ignorare queste due classi. Essi sono indicate qui perché eventuali esempi di Objective-C disponibili nella documentazione di Apple farà riferimento a essi, pertanto è utile per conoscere cosa fanno (e che è possibile usare UITableViewSource di xamarin. IOS).

## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
