---
title: Parti e funzionalità della tabella in Novell. iOS
description: Questo documento descrive le varie parti di un UITableView in iOS. Vengono illustrate le intestazioni, le celle, i piè di pagina, l'indice e la modalità di modifica della sezione.
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: d6ad088f9223dccb1966148fe8f53d76e85040a6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645610"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>Parti e funzionalità della tabella in Novell. iOS

Un UITableView può avere uno stile "raggruppato" o "normale" ed è costituito dalle parti seguenti:

-  [Intestazione della sezione](#Section_Header)
-  [Celle](#Cells) di (o righe, se si preferisce)
-  [Piè di pagina della sezione](#Section_Footer)
-  [Index](#Index)
-  [Modalità di modifica](#Edit_Features) (include ' scorri da eliminare ' e trascinare gli handle per modificare l'ordine delle righe) 

Queste schermate mostrano come vengono visualizzate le righe, le intestazioni, i piè di pagina, i controlli di modifica e l'indice.

 [![](table-parts-and-functionality-images/image1a.png "Queste schermate mostrano come vengono visualizzate le righe, le intestazioni, i piè di pagina, i controlli di modifica e l'indice")](table-parts-and-functionality-images/image1a.png#lightbox)

Queste parti sono descritte in dettaglio di seguito:

<a name="Section_Header" />

## <a name="section-header"></a>Intestazione della sezione

Facoltativamente, le celle possono essere raggruppate in sezioni, etichettate con un'intestazione personalizzata e/o contrassegnate con un piè di pagina. L'intestazione può essere impostata con un valore stringa oppure è possibile specificare una visualizzazione personalizzata per consentire un layout o uno stile diverso.

<a name="Cells" />

## <a name="cells"></a>Celle

Le celle sono l'elemento principale dell'interfaccia utente per una tabella. Una volta implementato correttamente, le celle vengono riutilizzate per l'efficienza della memoria. Sono disponibili quattro stili di cella predefiniti ed è possibile creare celle personalizzate, nel codice o nella finestra di progettazione quando si utilizzano gli storyboard.

<a name="Section_Footer"/>

## <a name="section-footer"></a>Piè di pagina della sezione

Il piè di pagina facoltativo può essere impostato con un valore stringa oppure è possibile specificare una visualizzazione personalizzata per consentire un layout o uno stile diverso. Le intestazioni di sezione e i piè di pagina possono essere impostati in modo indipendente.

<a name="Index" />

## <a name="index"></a>Indice

L'indice viene visualizzato come una striscia di caratteri sul bordo destro della tabella.
Il tocco o il trascinamento dell'indice accelera lo scorrimento fino a tale parte della tabella. Un indice è facoltativo, ma è consigliato per spostarsi tra gli elenchi lunghi. Un indice non viene in genere utilizzato con lo stile raggruppato.

<a name="Edit_Features" />

## <a name="editing-mode"></a>Modalità di modifica

Sono disponibili un paio di funzionalità di modifica diverse:

- Scorrere rapidamente per eliminare le singole celle.
- Immissione della modalità di modifica per rivelare i pulsanti Elimina in ogni riga 
- Passaggio alla modalità di modifica per rivelare gli handle di nuovo ordinamento. 
- Inserimento di nuove celle (con animazione).

Nella parte restante di questo documento viene illustrato come implementare tutte queste funzionalità di UITableView con Novell. iOS.


## <a name="classes-overview"></a>Cenni preliminari sulle classi

Di seguito sono illustrate le classi primarie usate per visualizzare le visualizzazioni di tabella:

[![](table-parts-and-functionality-images/classdiagram.png "Di seguito sono illustrate le classi primarie usate per visualizzare le visualizzazioni di tabella")](table-parts-and-functionality-images/classdiagram.png#lightbox)

Lo scopo di ogni classe è descritto di seguito:

- **UITableView** : vista che contiene una raccolta di celle all'interno di un contenitore di scorrimento. La visualizzazione tabella USA in genere l'intera schermata in un'app iPhone, ma potrebbe esistere come parte di una visualizzazione più ampia nell'iPad (o apparire in un popopov). 
- **UITableViewCell** : vista che rappresenta una singola cella (o riga) in una vista tabella. Sono disponibili quattro tipi di celle incorporati ed è possibile creare celle personalizzate in C# o con iOS designer. 
- **UITableViewSource** – Novell. iOS: classe astratta esclusiva che fornisce tutti i metodi necessari per visualizzare una tabella, incluso il conteggio delle righe, restituendo una visualizzazione di celle per ogni riga, gestendo la selezione delle righe e molte altre funzionalità facoltative. È *necessario* sottoclassare questo per ottenere un UITableView funzionante. 
- **NSIndexPath** : contiene le proprietà di riga e sezione che identificano in modo univoco la posizione di una cella in una tabella. 
- **UITableViewController** : UIViewController pronto per l'uso con un UITableView hardcoded come vista e accessibile tramite la proprietà Tableview. 
- **UIViewController** : se la tabella non occupa l'intera schermata, è possibile aggiungere un UITableView a qualsiasi UIViewController con il relativo frame impostato in modo appropriato. 

UITableViewSource sostituisce le due classi seguenti, che sono ancora disponibili in Novell. iOS, ma in genere non sono necessarie:

- **UITableViewDataSource** : protocollo Objective-C modellato in Novell. iOS come classe astratta. Deve essere sottoclassata per fornire una tabella con una visualizzazione per ogni cella, nonché informazioni sulle intestazioni, i piè di pagina e il numero di righe e sezioni della tabella. 
- **UITableViewDelegate** : protocollo Objective-C modellato in Novell. iOS come classe. Gestisce la selezione, la modifica delle funzionalità e altre funzionalità facoltative della tabella. 

In questo documento gli esempi usano UITableViewSource e ignorano queste due classi. Sono citati in questo articolo, poiché tutti gli esempi di Objective-C presenti nella documentazione di Apple vi faranno riferimento, quindi è utile capire cosa fanno (e che è possibile usare invece UITableViewSource di Novell. iOS).

## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
