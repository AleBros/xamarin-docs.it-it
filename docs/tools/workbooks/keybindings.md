---
title: Tasti di scelta rapida di Xamarin le cartelle di lavoro dell'Editor
ms.topic: article
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: cbbf070a9c94221d98dedcd1df884a897ff7df3e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Tasti di scelta rapida di Xamarin le cartelle di lavoro dell'Editor

## <a name="the-return-key-and-its-nuances"></a>La chiave di restituire e relativi dettagli

La tabella seguente descrive le varie combinazioni di tasti per l'esecuzione del codice e la creazione di markdown. È stato reindirizzato molta attenzione a scegliere appropriati e coerente tasti di scelta rapida che sono familiari e fluida.

<table>
  <tr>
    <th>Tasto di scelta rapida</th>
    <th>Cella di codice</th>
    <th>Cella markdown</th>
  </tr>
  <tr>
    <td><kbd>Return</kbd></td>
    <td>
      <p>Se il cursore si trova alla fine del buffer di cella e la cella può essere analizzata correttamente, verrà eseguito e i risultati verranno visualizzati sotto il buffer e una nuova cella di codice verrà inserita e con stato attivo cella dopo la cella eseguita.</p>
      
      <p>Se l'analisi non è completato, una nuova riga verrà inserita nel buffer. Diagnostica del compilatore non verrà prodotto se l'analisi non è riuscita.</p>
    </td>
    <td>
      <p>
        <kbd>Restituire</kbd> presenta un comportamento diverso a seconda del contesto di Markdown del punto di inserimento.
      </p>
      <ul>
        <li>
Se il cursore si trova in un blocco di codice Markdown, viene inserita una valore letterale nuova riga.
        </li>
        <li>
Se il cursore si trova in un blocco di elenco Markdown, creare un nuovo elemento di elenco o dividere l'elemento di elenco corrente.
        </li>
        <li>
Se il cursore si trova in qualsiasi altro tipo di blocco Markdown, creare un nuovo blocco di paragrafo o suddividere il blocco corrente.
        </li>
    </td>
  </tr>
  <tr>
    <td>
      <dl>
        <dt>Mac</dt>
        <dd><kbd>Command‑Return</kbd></dd>
        <dt>Win</dt>
        <dd><kbd>Control‑Return</kbd></dd>
      </dl>
    </td>
    <td>
      <p>Tenta sempre di analizzare ed eseguire il contenuto della cella. Se la compilazione ha esito positivo, verranno visualizzati sotto il buffer di risultati (incluse le eccezioni di esecuzione), e se non sono presenti celle successive, verrà creato e con stato attivo uno nuovo.</p>
      
      <p>Se sono presenti errori di compilazione, verrà visualizzata la diagnostica e il buffer rimarrà con stato attivo con la posizione del cursore invariata.</p>
    </td>
    <td>
Inserisce e si concentra ora una nuova cella di codice dopo la cella corrente di markdown.
    </td>
  </tr>
  <tr>
    <td>
      <dl>
        <dt>Mac</dt>
        <dd><kbd>Command‑Shift‑Return</kbd></dd>
        <dt>Win</dt>
        <dd><kbd>Control‑Shift‑Return</kbd></dd>
      </dl>
    </td>
    <td>
Inserisce e si concentra ora una nuova cella markdown dopo la cella corrente.
    </td>
    <td>
Stesso comportamento di <kbd>restituire</kbd>
    </td>
  </tr>
  <tr>
    <td><kbd>Shift‑Return</kbd></td>
    <td>
Inserisce sempre una nuova riga, indipendentemente dalla posizione del punto di inserimento o un contenuto.
    </td>
    <td>
Inserisce un'interruzione di riga all'interno del blocco Markdown corrente.
    </td>
  </tr>
</table>
