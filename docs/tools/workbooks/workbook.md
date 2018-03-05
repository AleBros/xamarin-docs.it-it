---
title: Cartelle di lavoro interattive
description: Utilizzare le cartelle di lavoro per creare documenti in tempo reale con il codice c# per la sperimentazione, insegnamento, training o l'esplorazione.
ms.topic: article
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: bd53074fcde25736d37544efc719ecef1110c364
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="interactive-workbooks"></a>Cartelle di lavoro interattive

_Utilizzare le cartelle di lavoro per creare documenti in tempo reale con il codice c# per la sperimentazione, insegnamento, training o l'esplorazione._

È possibile utilizzare le cartelle di lavoro come un'applicazione autonoma, separata dall'IDE.

Per iniziare a creare una nuova cartella di lavoro, eseguire l'app cartelle di lavoro. Se non è installato questo già, visitare il [installazione](~/tools/workbooks/install.md#install) pagina. Verrà richiesto di creare una cartella di lavoro nella piattaforma di scelta, viene automaticamente connesso a un'app di agente che consente di visualizzare il documento in tempo reale.

Se l'app cartelle di lavoro è già in esecuzione, è possibile creare un nuovo documento passando a **File > Nuovo**.

Le cartelle di lavoro può essere salvati ed è aperto in un secondo momento all'interno dell'applicazione. È quindi possibile condividerli con altri utenti per illustrare idee, esplorare nuove API o insegna nuovi concetti.

## <a name="code-editing"></a>Modifica del codice

Finestra di modifica del codice fornisce il completamento del codice, la colorazione della sintassi, inline live-diagnostica e supporto di istruzione su più righe.

[ ![](workbook-images/inspector-0.6.0-repl-small.png "Finestra di modifica del codice fornisce il completamento del codice, la colorazione della sintassi, inline live-diagnostica e supporto di istruzione su più righe")](workbook-images/inspector-0.6.0-repl.png)

Xamarin le cartelle di lavoro vengono salvati in un `.workbook` file, ovvero un file CommonMark con alcuni metadati nella parte superiore (vedere [tipi di File di cartelle di lavoro](#Workbooks_Files_Types) per ulteriori informazioni su come è possibile salvare le cartelle di lavoro).

### <a name="nuget-package-support"></a>Supporto per il pacchetto NuGet

Numero di pacchetti NuGet più diffusi è supportati direttamente nelle cartelle di lavoro di Xamarin. È possibile cercare i pacchetti di accedere a **File > Aggiungi pacchetto**. Aggiunta di un pacchetto verrà visualizzato automaticamente `#r` istruzioni che fanno riferimento gli assembly di pacchetto, che consente di usare immediatamente.

Quando si salva una cartella di lavoro con riferimenti del pacchetto, vengono salvati anche tali riferimenti. Se si condivide la cartella di lavoro con un altro utente, verrà scaricato automaticamente i pacchetti di cui si fa riferimento.

Esistono alcune limitazioni note con il supporto del pacchetto NuGet nelle cartelle di lavoro:

  * Librerie native sono supportati solo su iOS e solo durante il collegamento con la libreria gestita.
  * I pacchetti che dipendono da `.targets` file o gli script di PowerShell probabilmente avrà esito negativo a funzionare come previsto.
  * Per rimuovere o modificare una dipendenza pacchetto, modificare il manifesto della cartella di lavoro con un editor di testo. Gestione dei pacchetti corretto sarà presto disponibile.

### <a name="xamarinforms-support"></a>Supporto xamarin. Forms

Se si fa riferimento il pacchetto NuGet di xamarin. Forms nella cartella di lavoro, l'app di cartella di lavoro cambierà la visualizzazione principale per essere basati su xamarin. Forms. È possibile accedervi tramite `Xamarin.Forms.Application.Current.MainPage`.

La scheda di controllo di visualizzazione ha anche un supporto speciale per visualizzare la gerarchia della visualizzazione xamarin. Forms che consentono di comprendere il layout.

## <a name="rich-text-editing"></a>Modifica del testo RTF

È possibile modificare il testo intorno al codice usando l'editor di testo RTF incluso, come illustrato di seguito:

![](workbook-images/inspector-0.6.2-editing.gif "Modificare il testo nel codice utilizzando l'editor di testo RTF incorporato")

### <a name="markdown-authoring"></a>Creazione di markdown

Gli autori di cartella di lavoro possono talvolta risultare più semplice modificare direttamente il CommonMark "source" della cartella di lavoro con il proprio editor preferito.

Tenere presente che se si modifica e si salva la cartella di lavoro all'interno del client di cartelle di lavoro, il testo CommonMark potrebbe riformattato.

Si noti che a causa dell'estensione CommonMark viene usata per abilitare i metadati YAML nei file di cartella di lavoro, `---` è riservato a tale scopo. Se si desidera creare [interruzioni tematiche](http://spec.commonmark.org/0.27/#thematic-break) nel testo, è necessario utilizzare `***` o `___` invece. Tali interruzioni devono essere evitate in cartelle di lavoro 1.2 e versioni precedenti a causa di un bug durante il salvataggio.

### <a name="improvements-in-workbooks-13"></a>Miglioramenti nelle cartelle di lavoro 1.3

La sintassi di offerta di blocco Markdown leggermente per migliorare la presentazione abbiamo inoltre esteso. Aggiungendo un emoji come primo carattere dell'offerta di blocco, è possibile influenzare il colore di sfondo dell'offerta:

- `> [!NOTE]
>' verrà eseguito il rendering come una nota con uno sfondo blu
- `> [!IMPORTANT]
>' verrà eseguito il rendering come un avviso con uno sfondo giallo
- `> [!WARNING]
>' verrà eseguito il rendering di un problema con uno sfondo rosso

È anche possibile collegare le intestazioni nel documento di cartella di lavoro. Punti di ancoraggio per ogni intestazione, verranno generati con l'ID di ancoraggio dal testo dell'intestazione, elaborato come segue:

1. L'intestazione è in minuscolo.
1. Vengono rimossi tutti i caratteri eccetto caratteri alfanumerici e trattini.
1. Tutti gli spazi vengono sostituiti con trattini.

Ciò significa che un'intestazione come "importante" riceve un id di `important-header` e possono essere collegati a inserendo un collegamento a `#important-header` nella cartella di lavoro.

## <a name="document-structure"></a>Struttura del documento

### <a name="cell"></a>Cella

Unità discreta di contenuto, che rappresenta il codice eseguibile o markdown. Una cella di codice è costituita da un massimo di quattro componenti secondari:

- Editor
  - Buffer
- Diagnostica del compilatore
- Output della console
- Risultati dell'esecuzione

### <a name="editor"></a>Editor

Il componente di testo interattiva di una cella. Per le celle di codice, questo è l'editor di codice con evidenziazione della sintassi e così via. Per le celle markdown questo è un editor di contenuto RTF con una formattazione sensibile al contesto e la creazione della barra degli strumenti.

### <a name="buffer"></a>Buffer
Il contenuto effettivo di un editor.

### <a name="compiler-diagnostics"></a>Diagnostica del compilatore

Qualsiasi diagnostica generati durante la compilazione di codice, il rendering solo quando viene richiesto di esecuzione esplicito. Viene visualizzata immediatamente sotto l'editor della cella.

### <a name="console-output"></a>Output della console

Qualsiasi output venga scritto in un output standard o di errore standard durante l'esecuzione della cella. Verrà visualizzato un output standard nel testo di colore nero e verrà visualizzato l'errore standard nel testo di colore rosso.

### <a name="execution-results"></a>Risultati dell'esecuzione

Verranno visualizzate durante la compilazione ha esito positivo, complete e interattive potenzialmente rappresentazioni dei risultati di una cella fornito un risultato viene effettivamente generato dall'esecuzione. Le eccezioni vengono considerate risultati in questo contesto, poiché non appena vengono prodotti in seguito all'esecuzione effettivamente la compilazione.

## <a name="workbooks-files-types"></a>Tipi di file delle cartelle di lavoro

### <a name="plain-files"></a>File semplici

Per impostazione predefinita, una cartella di lavoro vengono salvati come testo normale `.workbook` file contenente testo in formato CommonMark.

### <a name="packages"></a>Pacchetti

Un pacchetto di cartella di lavoro è una directory denominata con il `.workbook` estensione.
Nella ricerca del Mac e nella finestra di dialogo aprirlo Xamarin le cartelle di lavoro e dal menu File recenti, questa directory verrà riconosciuta come se fosse un file.

La directory deve contenere un `index.workbook` file, ovvero la cartella di lavoro effettivo di testo normale che verrà caricata nelle cartelle di lavoro di Xamarin. La directory può contenere anche risorse richieste dalla `index.workbook`, ad esempio immagini o altri file.

Se un testo normale `.workbook` file che fa riferimento a risorse dalla directory stesso viene aperto in cartelle di lavoro 0.99.3 o versioni successive, quando viene salvato, verrà convertito in un `.workbook` pacchetto. Questo vale in Windows e Mac.

> [!NOTE]
> **Nota:** gli utenti di Windows verranno aperto il `package.workbook\index.workbook` file direttamente, ma in caso contrario il pacchetto si comporterà come su Mac.

### <a name="archives"></a>Archivi

Pacchetti di cartella di lavoro, da una directory, possono essere difficili da distribuire facilmente tramite internet. La soluzione è archivi di cartella di lavoro. Un archivio di cartella di lavoro è un pacchetto compresso zip cartella di lavoro, denominato con il `.workbook` estensione.

A partire da cartelle di lavoro 1.1, quando si salva un pacchetto di cartella di lavoro, la finestra di dialogo Salva offre la possibilità di salvare come archivio invece. Le cartelle di lavoro 1.0 non potevano predefinite di creazione o archivi di salvataggio.

Le cartelle di lavoro 1.0, quando è stato aperto un archivio di cartella di lavoro, che è stato convertito in modo trasparente in un pacchetto di cartella di lavoro e il file zip è stata interrotta. Le cartelle di lavoro 1.1, rimane il file zip. Quando l'utente salva l'archivio, questo viene sostituito con un nuovo file zip.

È possibile creare manualmente un archivio di cartella di lavoro facendo un pacchetto di cartella di lavoro **comprimere** sul Mac o **Invia a > Compressed cartella compressa** in Windows. Rinominare il file zip di un `.workbook` estensione di file. Tale operazione funziona solo con i pacchetti di cartella di lavoro, i file di cartella di lavoro non semplice.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a cartelle di lavoro](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
