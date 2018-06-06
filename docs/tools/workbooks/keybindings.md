---
title: Tasti di scelta rapida di Xamarin le cartelle di lavoro dell'Editor
description: Questo documento vengono descritti i tasti di scelta rapida disponibili per l'utilizzo nell'editor di cartelle di lavoro di Xamarin. In particolare, appare in vari modi che il tasto INVIO viene usato.
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: topgenorth
ms.author: toopge
ms.openlocfilehash: b0ba268367ebf6a9eb55bd00dd84014de247649b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793904"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Tasti di scelta rapida di Xamarin le cartelle di lavoro dell'Editor

## <a name="the-return-key-and-its-nuances"></a>La chiave di restituire e relativi dettagli

La tabella seguente descrive le varie combinazioni di tasti per l'esecuzione del codice e la creazione di markdown. È stato reindirizzato molta attenzione a scegliere appropriati e coerente tasti di scelta rapida che sono familiari e fluida.

|Tasto di scelta rapida|Cella di codice|Cella markdown|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>Se il cursore si trova alla fine del buffer di cella e la cella può essere analizzata correttamente, verrà eseguito e i risultati verranno visualizzati sotto il buffer e una nuova cella di codice verrà inserita e con stato attivo cella dopo la cella eseguita.</p><p>Se l'analisi non è completato, una nuova riga verrà inserita nel buffer. Diagnostica del compilatore non verrà prodotto se l'analisi non è riuscita.</p>|<p><kbd>Restituire</kbd> presenta un comportamento diverso a seconda del contesto di Markdown del punto di inserimento.</p><ul><li>Se il cursore si trova in un blocco di codice Markdown, viene inserita una valore letterale nuova riga.</li><li>Se il cursore si trova in un blocco di elenco Markdown, creare un nuovo elemento di elenco o dividere l'elemento di elenco corrente.</li><li>Se il cursore si trova in qualsiasi altro tipo di blocco Markdown, creare un nuovo blocco di paragrafo o suddividere il blocco corrente.</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Command‑Return</kbd></dd><dt>Win</dt><dd><kbd>Control‑Return</kbd></dd></dl>|<p>Tenta sempre di analizzare ed eseguire il contenuto della cella. Se la compilazione ha esito positivo, verranno visualizzati sotto il buffer di risultati (incluse le eccezioni di esecuzione), e se non sono presenti celle successive, verrà creato e con stato attivo uno nuovo.</p><p>Se sono presenti errori di compilazione, verrà visualizzata la diagnostica e il buffer rimarrà con stato attivo con la posizione del cursore invariata.</p>|Inserisce e si concentra ora una nuova cella di codice dopo la cella corrente di markdown.|
|<dl><dt>Mac</dt><dd><kbd>Command‑Shift‑Return</kbd><dd><dt>Win</dt><dd><kbd>Control‑Shift‑Return</kbd></dd></dl>|Inserisce e si concentra ora una nuova cella markdown dopo la cella corrente.|Stesso comportamento di <kbd>restituire</kbd>|
|<kbd>Shift‑Return</kbd>|Inserisce sempre una nuova riga, indipendentemente dalla posizione del punto di inserimento o un contenuto.|Inserisce un'interruzione di riga all'interno del blocco Markdown corrente.|
