---
title: Cartelle di lavoro interattive
description: Questo documento descrive come usare Xamarin Workbooks per creare documenti in tempo reale che contengono C# codice per sperimentare, insegnare, corsi di formazione o esplorazione.
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: a900d427ad6ac2a0e211ef4f00d2f014b13e5d1c
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674412"
---
# <a name="interactive-workbooks"></a>Cartelle di lavoro interattive

È possibile usare le cartelle di lavoro come applicazione autonoma, separata dal tuo ambiente IDE.

Per iniziare a creare una nuova cartella di lavoro, eseguire l'app cartelle di lavoro. Se non è stato installato questo già, visitare il [installazione](~/tools/workbooks/install.md#install) pagina. Verrà chiesto di creare una cartella di lavoro nella tua piattaforma preferita, che verrà connesse automaticamente a un'app dell'agente consente di visualizzare il documento in tempo reale.

Se l'app cartelle di lavoro è già in esecuzione, è possibile creare un nuovo documento selezionando **File > New**.

Le cartelle di lavoro può essere salvati e aperto in un secondo momento all'interno dell'applicazione. È quindi possibile condividerli con altri utenti per illustrare le idee, esplorare le API nuove o spiegare nuovi concetti.

## <a name="code-editing"></a>Modifica del codice

Finestra di modifica del codice fornisce il completamento del codice, colorazione della sintassi, inline live-diagnostica e supporto di istruzioni a più righe.

[![](workbook-images/inspector-0.6.0-repl-small.png "Finestra di modifica del codice fornisce il completamento del codice, colorazione della sintassi, inline live-diagnostica e supporto di istruzioni a più righe")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin Workbooks vengono salvati in un `.workbook` file, ovvero un file CommonMark con alcuni metadati nella parte superiore (vedere [tipi di File di cartelle di lavoro](#workbooks-files-types) per altri dettagli sul modo in cui le cartelle di lavoro possono essere salvati).

### <a name="nuget-package-support"></a>Supporto per il pacchetto NuGet

Sono supportati molti pacchetti NuGet più diffusi direttamente in Xamarin Workbooks. È possibile cercare i pacchetti, passare a **File > Aggiungi pacchetto**. Aggiunta di un pacchetto verrà automaticamente `#r` istruzioni che fanno riferimento assembly dei pacchetti, consentendo di usare immediatamente.

Quando si salva una cartella di lavoro con i riferimenti ai pacchetti, vengono salvate anche tali riferimenti. Se si condivide la cartella di lavoro con un'altra persona, scaricherà automaticamente i pacchetti con riferimenti.

Esistono alcune limitazioni note con supporto per il pacchetto NuGet nelle cartelle di lavoro:

- Le librerie native sono supportati solo in iOS e solo quando collegato con la libreria gestita.
- I pacchetti che dipendono da `.targets` file o script di PowerShell probabilmente avrà esito negativo a funzionare come previsto.
- Per rimuovere o modificare una dipendenza del pacchetto, modificare il manifesto della cartella di lavoro con un editor di testo. Gestione dei pacchetti appropriata è in uscita.

### <a name="xamarinforms-support"></a>Supporto di xamarin. Forms

Se si fa riferimento il pacchetto NuGet di xamarin. Forms nella cartella di lavoro, l'app della cartella di lavoro cambierà la visualizzazione principale per essere basata su xamarin. Forms. È possibile accedervi tramite `Xamarin.Forms.Application.Current.MainPage`.

La scheda di controllo di visualizzazione include anche uno speciale supporto per la visualizzazione della gerarchia di visualizzazione che aiutano a comprendere il layout di xamarin. Forms.

## <a name="rich-text-editing"></a>Modifica del testo avanzata

È possibile modificare il testo intorno al codice usando l'editor di testo RTF incluso, come illustrato di seguito:

![](workbook-images/inspector-0.6.2-editing.gif "Modificare il testo intorno al codice usando l'editor di testi con formattazione predefinita")

### <a name="markdown-authoring"></a>Creazione di markdown

Gli autori della cartella di lavoro potrebbero talvolta essere più semplice di modificare direttamente il CommonMark "source" della cartella di lavoro con loro editor preferito.

Tenere presente che se si modifica e si salva la cartella di lavoro all'interno del client di cartelle di lavoro, il testo CommonMark potrebbe da riformattare.

Si noti che a causa dell'estensione CommonMark utilizziamo per abilitare i metadati YAML nei file della cartella di lavoro, `---` è riservato a tale scopo. Se si desidera creare [interruzioni tematiche](http://spec.commonmark.org/0.27/#thematic-break) nel testo, è consigliabile usare `***` o `___` invece. Nella versione 1.2 di cartelle di lavoro e in precedenza a causa di un bug durante il salvataggio, è consigliabile evitare tali interruzioni.

### <a name="improvements-in-workbooks-13"></a>Miglioramenti nelle cartelle di lavoro 1.3

Abbiamo inoltre esteso la sintassi di offerta di blocco Markdown leggermente per migliorare la presentazione. Quando si aggiungono un emoji come primo carattere dell'offerta di blocco, è possibile influenzare il colore di sfondo dell'offerta:

- `> [!NOTE]`
    > verrà eseguito il rendering come nota con uno sfondo blu
- `> [!IMPORTANT]`
    > verrà eseguito il rendering come un avviso con uno sfondo giallo
- `> [!WARNING]`
    > verrà eseguito il rendering come un problema con uno sfondo rosso

È anche possibile collegare a intestazioni nel documento di cartella di lavoro. Punti di ancoraggio per ogni intestazione, verranno generati con l'ID di ancoraggio in corso il testo dell'intestazione, elaborato come segue:

1. L'intestazione è minuscolo.
1. Vengono rimossi tutti i caratteri eccetto caratteri alfanumerici e trattini.
1. Tutti gli spazi vengono sostituiti con i trattini.

Ciò significa che un'intestazione, ad esempio "Header importante" Ottiene un id del `important-header` e può essere collegato a inserendo un collegamento a `#important-header` nella cartella di lavoro.

## <a name="document-structure"></a>Struttura del documento

### <a name="cell"></a>cella

Unità discreta del contenuto, che rappresenta il codice eseguibile o markdown. Una cella di codice è costituita da un massimo di quattro componenti secondari:

- Editor
  - Buffer
- Diagnostica del compilatore
- Output della console
- Risultati dell'esecuzione

### <a name="editor"></a>Editor

Il componente di testo interattiva di una cella. Per le celle di codice, si tratta di editor del codice effettivo con l'evidenziazione della sintassi e così via. Per le celle di markdown è un editor di contenuto RTF con una formattazione sensibile al contesto e sulla barra degli strumenti di creazione.

### <a name="buffer"></a>Buffer

Il contenuto di un editor di testo effettivo.

### <a name="compiler-diagnostics"></a>Diagnostica del compilatore

Diagnostica di qualsiasi prodotto durante la compilazione di codice, viene eseguito il rendering solo quando viene richiesto l'esecuzione esplicita. Viene visualizzata immediatamente sotto l'editor della cella.

### <a name="console-output"></a>Output della console

Qualsiasi output scritti durante l'esecuzione della cella output standard o di errore standard. Verrà visualizzato un output standard nel testo di colore nero e verrà eseguito il rendering degli errori standard nel testo di colore rosso.

### <a name="execution-results"></a>Risultati dell'esecuzione

Verranno eseguito il rendering di accattivanti e interattive potenzialmente rappresentazioni dei risultati di una cella dopo la compilazione ha esito positivo, fornito un risultato viene effettivamente generato dall'esecuzione. Le eccezioni vengono considerate i risultati in questo contesto, poiché essi vengono generati come risultato non eseguendo effettivamente la compilazione.

## <a name="workbooks-files-types"></a>Tipi di file delle cartelle di lavoro

### <a name="plain-files"></a>Semplici file

Per impostazione predefinita, una cartella di lavoro vengono salvati come testo normale `.workbook` file che contiene il testo formattato CommonMark.

### <a name="packages"></a>Pacchetti

Un pacchetto di cartella di lavoro è una directory denominata con il `.workbook` estensione.
Nella ricerca del Mac e nella finestra di dialogo aprirlo Xamarin Workbooks e recenti dal menu file, questa directory verrà riconosciuta come se fosse un file.

La directory deve contenere un `index.workbook` file, ovvero la cartella di lavoro di testo normale effettivi che verrà caricato in Xamarin Workbooks. La directory può contenere anche risorse richieste dalla `index.workbook`, ad esempio immagini o altri file.

Se un testo normale `.workbook` nelle cartelle di lavoro 0.99.3 è aprire file che fa riferimento a risorse dalla relativa directory stesso o in un secondo momento, quando viene salvato, verrà convertito in un `.workbook` pacchetto. Ciò è vero in Mac e Windows.

> [!NOTE]
> Gli utenti di Windows verranno aperto il `package.workbook\index.workbook` file direttamente, ma in caso contrario, il pacchetto si comporterà come nei computer Mac.

### <a name="archives"></a>Archivi

I pacchetti della cartella di lavoro, in fase di directory, possono essere difficili da distribuire con facilità la rete Internet. La soluzione è archivi cartella di lavoro. Un archivio di cartella di lavoro è un pacchetto con estensione zip compresso della cartella di lavoro, denominato con il `.workbook` estensione.

1\.1 le cartelle di lavoro, quando si salva un pacchetto di cartella di lavoro, la finestra di dialogo Salva offre la possibilità di salvare invece come un archivio. Le cartelle di lavoro 1.0 prevedeva alcuna funzionalità integrata di creazione o di archivi di salvataggio.

Nelle cartelle di lavoro 1.0 quando è stato aperto in un archivio cartella di lavoro, che è stato convertito in modo trasparente in un pacchetto di cartella di lavoro e il file zip è stata perso. Nelle cartelle di lavoro 1.1, rimane il file zip. Quando l'utente salva l'archivio, viene sostituito con un nuovo file zip.

È possibile creare manualmente un archivio di cartella di lavoro facendo clic su un pacchetto di cartella di lavoro e selezionando **comprimere** in Mac, o **Invia a > cartella compressa Compressed** su Windows. Quindi rinominare il file con estensione zip per avere una `.workbook` estensione di file. Funziona solo con i pacchetti della cartella di lavoro, i file della cartella di lavoro non semplice.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a cartelle di lavoro](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
