---
title: Tasti di scelta rapida dell'editor Xamarin Workbooks
description: Questo documento descrive le scelte rapide da tastiera disponibili per l'uso nell'editor Xamarin Workbooks. In particolare, vengono esaminati i vari modi in cui viene utilizzata la chiave restituita.
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: f8c76f96ea16b9341d349ca110a3eb39207a4d30
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019112"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Tasti di scelta rapida dell'editor Xamarin Workbooks

## <a name="the-return-key-and-its-nuances"></a>Il tasto restituito e le relative sfumature

Nella tabella seguente vengono descritte le varie combinazioni di tasti per l'esecuzione di codice e la creazione di Markdown. Abbiamo avuto la massima attenzione per scegliere le combinazioni di tasti ragionevoli e coerenti, che sono sia familiari che fluide.

|Chiave di associazione|Cella di codice|Cella Markdown|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>Se il punto di inserimento si trova alla fine del buffer della cella e la cella può essere analizzata correttamente, verrà eseguita e i risultati verranno visualizzati sotto il buffer e verrà inserita una nuova cella di codice con lo stato attivo dopo la cella eseguita.</p><p>Se l'analisi ha esito negativo, verrà inserita una nuova riga nel buffer. La diagnostica del compilatore non verrà generata se l'analisi non riesce.</p>|<p><kbd>Return</kbd> presenta un comportamento diverso a seconda del contesto di Markdown nel punto di inserimento.</p><ul><li>Se il punto di inserimento si trova in un blocco di codice Markdown, viene inserita una nuova riga letterale.</li><li>Se il punto di inserimento si trova in un blocco elenco Markdown, creare un nuovo elemento elenco o suddividere l'elemento dell'elenco corrente.</li><li>Se il punto di inserimento si trova in un altro tipo di blocco Markdown, creare un nuovo blocco di paragrafo o suddividere il blocco corrente.</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Command-Return</kbd></dd><dt>Win</dt><dd><kbd>Controllo-restituzione</kbd></dd></dl>|<p>Tenta sempre di analizzare ed eseguire il contenuto della cella. Se la compilazione ha esito positivo, i risultati (incluse le eccezioni di esecuzione) verranno visualizzati sotto il buffer e se non sono presenti celle successive, ne verrà creata e messa a fuoco uno nuovo.</p><p>Se si verificano errori di compilazione, verranno visualizzati i dati di diagnostica e il buffer rimarrà invariato con la posizione del punto di inserimento.</p>|Inserisce e concentra una nuova cella di codice dopo la cella Markdown corrente.|
|<dl><dt>Mac</dt><dd><kbd>Command-Shift-return</kbd><dd><dt>Win</dt><dd><kbd>CTRL-MAIUSC-return</kbd></dd></dl>|Inserisce e concentra una nuova cella Markdown dopo la cella corrente.|Stesso comportamento <kbd>restituito</kbd>|
|<kbd>Shift-return</kbd>|Inserisce sempre una nuova riga, indipendentemente dalla posizione del punto di inserimento o dal contenuto.|Inserisce un'interruzioni di riga rigida all'interno del blocco Markdown corrente.|
