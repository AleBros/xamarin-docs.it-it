---
title: Cartelle di lavoro interattive
description: Questo documento descrive come usare Xamarin Workbooks per creare documenti dinamici contenenti C# codice per la sperimentazione, l'insegnamento, il training o l'esplorazione.
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: a6ca347c231d001cab521d7280a66b714b6a5aef
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029567"
---
# <a name="interactive-workbooks"></a>Cartelle di lavoro interattive

È possibile usare le cartelle di lavoro come applicazione autonoma, separate dall'IDE.

Per iniziare a creare una nuova cartella di lavoro, eseguire l'app cartelle di lavoro. Se questa operazione non è già stata eseguita, visitare la pagina [installazione](~/tools/workbooks/install.md#install) . Verrà richiesto di creare una cartella di lavoro nella piattaforma preferita, che si connetterà automaticamente a un'app agente che consente di visualizzare il documento in tempo reale.

Se l'app cartelle di lavoro è già in esecuzione, è possibile creare un nuovo documento passando a **File > nuovo**.

Le cartelle di lavoro di possono essere salvate e riaperte in un secondo momento all'interno dell'applicazione. È quindi possibile condividerli con altri utenti per dimostrare idee, esplorare nuove API o insegnare nuovi concetti.

## <a name="code-editing"></a>Modifica del codice

La finestra di modifica del codice fornisce il completamento del codice, la colorazione della sintassi, la diagnostica in tempo reale inline e il supporto per istruzioni a più righe.

[![](workbook-images/inspector-0.6.0-repl-small.png "The code editing window provides code completion, syntax coloring, inline live-diagnostics, and multi-line statement support")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin Workbooks vengono salvati in un file di `.workbook`, ovvero un file CommonMark con alcuni metadati nella parte superiore. per ulteriori informazioni su come salvare le cartelle di lavoro, vedere [tipi di file delle cartelle di lavoro](#workbooks-files-types) .

### <a name="nuget-package-support"></a>Supporto per pacchetti NuGet

Molti pacchetti NuGet diffusi sono supportati direttamente in Xamarin Workbooks. È possibile cercare i pacchetti esplorando il **File > Aggiungi pacchetto**. L'aggiunta di un pacchetto consentirà di importare automaticamente `#r` le istruzioni che fanno riferimento agli assembly del pacchetto, per poterle usare immediatamente.

Quando si salva una cartella di lavoro con i riferimenti al pacchetto, anche questi riferimenti vengono salvati. Se si condivide la cartella di lavoro con un'altra persona, verranno scaricati automaticamente i pacchetti a cui si fa riferimento.

Esistono alcune limitazioni note relative al supporto dei pacchetti NuGet nelle cartelle di lavoro di:

- Le librerie native sono supportate solo in iOS e solo se collegate con la libreria gestita.
- I pacchetti che dipendono da `.targets` file o script di PowerShell probabilmente non funzioneranno come previsto.
- Per rimuovere o modificare una dipendenza del pacchetto, modificare il manifesto della cartella di lavoro con un editor di testo. La gestione corretta dei pacchetti è in corso.

### <a name="xamarinforms-support"></a>Supporto di Xamarin.Forms

Se si fa riferimento al pacchetto NuGet Xamarin.Forms nella cartella di lavoro, l'app cartella di lavoro cambierà la visualizzazione principale in modo che sia Xamarin.Forms. È possibile accedervi tramite `Xamarin.Forms.Application.Current.MainPage`.

Nella scheda Visualizza controllo è inoltre presente un supporto speciale per la visualizzazione della gerarchia di visualizzazione Xamarin.Forms per facilitare la comprensione dei layout.

## <a name="rich-text-editing"></a>Modifica di testo RTF

È possibile modificare il testo attorno al codice usando l'editor di testo RTF incluso, come illustrato di seguito:

![](workbook-images/inspector-0.6.2-editing.gif "Edit the text around the code using the built-in rich text editor")

### <a name="markdown-authoring"></a>Creazione di Markdown

Gli autori di cartelle di lavoro possono a volte risultare più semplice modificare direttamente il CommonMark "origine" della cartella di lavoro con il proprio editor preferito.

Tenere presente che, se si modifica e si salva la cartella di lavoro nel client di cartelle di lavoro, il testo CommonMark potrebbe essere riformattato.

Si noti che a causa dell'estensione CommonMark usata per abilitare i metadati YAML nei file della cartella di lavoro, `---` è riservata a tale scopo. Se si desidera creare [interruzioni tematiche](https://spec.commonmark.org/0.27/#thematic-break) nel testo, è necessario utilizzare `***` o `___`. Tali interruzioni dovrebbero essere evitate nelle cartelle di lavoro 1,2 e versioni precedenti a causa di un bug durante il salvataggio.

### <a name="improvements-in-workbooks-13"></a>Miglioramenti apportati alle cartelle di lavoro 1,3

Per migliorare la presentazione, è stata anche estesa leggermente la sintassi delle virgolette dei blocchi Markdown. Aggiungendo un emoji come primo carattere nell'offerta di blocco, è possibile influenzare il colore di sfondo dell'offerta:

- `> [!NOTE]`
    > eseguirà il rendering come una nota con uno sfondo blu
- `> [!IMPORTANT]`
    > verrà eseguito il rendering come avviso con sfondo giallo
- `> [!WARNING]`
    > il rendering viene eseguito come un problema con uno sfondo rosso

È inoltre possibile eseguire il collegamento a intestazioni nel documento della cartella di lavoro. Si generano ancoraggi per ogni intestazione, con l'ID di ancoraggio che è il testo dell'intestazione, elaborato come segue:

1. L'intestazione è in lettere minuscole.
1. Vengono rimossi tutti i caratteri ad eccezione dei caratteri alfanumerici e trattini.
1. Tutti gli spazi vengono sostituiti con trattini.

Ciò significa che un'intestazione come "important header" ottiene un ID di `important-header` e può essere collegata inserendo un collegamento per `#important-header` nella cartella di lavoro.

## <a name="document-structure"></a>Struttura del documento

### <a name="cell"></a>Cella

Unità di contenuto discreta, che rappresenta il codice eseguibile o Markdown. Una cella di codice è costituita da un massimo di quattro sottocomponenti:

- Editor
  - Buffer
- Diagnostica del compilatore
- Output della console
- Risultati esecuzione

### <a name="editor"></a>Editor

Componente di testo interattivo di una cella. Per le celle di codice, questo è l'editor di codice effettivo con evidenziazione della sintassi e così via. Per le celle Markdown si tratta di un editor di contenuto RTF con una barra degli strumenti per la formattazione e la modifica sensibile al contesto.

### <a name="buffer"></a>Buffer

Contenuto di testo effettivo di un editor.

### <a name="compiler-diagnostics"></a>Diagnostica del compilatore

Qualsiasi diagnostica prodotta durante la compilazione del codice, sottoposta a rendering solo quando viene richiesta l'esecuzione esplicita. Visualizzato immediatamente sotto l'editor di celle.

### <a name="console-output"></a>Output della console

Qualsiasi output scritto nell'errore standard o standard durante l'esecuzione della cella. Verrà eseguito il rendering dello standard out in testo nero e verrà eseguito il rendering di un errore standard in testo rosso.

### <a name="execution-results"></a>Risultati esecuzione

Al completamento della compilazione, viene eseguito il rendering di rappresentazioni avanzate e potenzialmente interattive dei risultati per una cella, purché un risultato venga effettivamente prodotto dall'esecuzione. Le eccezioni vengono considerate come risultati in questo contesto, dal momento che vengono generate in seguito all'esecuzione effettiva della compilazione.

## <a name="workbooks-files-types"></a>Tipi di file di cartelle di lavoro

### <a name="plain-files"></a>File normali

Per impostazione predefinita, una cartella di lavoro viene salvata come testo normale `.workbook` file contenente testo formattato con CommonMark.

### <a name="packages"></a>Pacchetti

Un pacchetto di cartella di lavoro è una directory denominata con l'estensione `.workbook`.
Nel Finder di Mac e nel menu Xamarin Workbooks Apri finestra di dialogo e file recenti questa directory verrà riconosciuta come se fosse un file.

La directory deve contenere un file di `index.workbook`, ovvero la cartella di lavoro in testo normale che verrà caricata nel Xamarin Workbooks. La directory può inoltre contenere risorse richieste da `index.workbook`, incluse immagini o altri file.

Se un file di `.workbook` testo normale che fa riferimento alle risorse dalla stessa directory viene aperto nelle cartelle di lavoro 0.99.3 o versioni successive, quando viene salvato, verrà convertito in un pacchetto di `.workbook`. Questa operazione è valida sia in Mac che in Windows.

> [!NOTE]
> Gli utenti di Windows apriranno direttamente il file di `package.workbook\index.workbook`. in caso contrario, il comportamento del pacchetto è identico a quello di Mac.

### <a name="archives"></a>archivi

I pacchetti di cartelle di lavoro, che sono directory, possono essere difficili da distribuire facilmente tramite Internet. La soluzione è archivi di cartelle di lavoro. Un archivio di cartelle di lavoro è un pacchetto di cartelle di lavoro compresso con zip, denominato con l'estensione `.workbook`.

A partire dalle cartelle di lavoro 1,1, quando si salva un pacchetto di cartella di lavoro, nella finestra di dialogo Salva è possibile scegliere di salvare invece come archivio. Le cartelle di lavoro 1,0 non hanno incorporato alcun modo per creare o salvare gli archivi.

Nelle cartelle di lavoro 1,0, quando è stato aperto, l'archivio di una cartella di lavoro è stato convertito in modo trasparente in un pacchetto di cartella di lavoro e il file zip è andato perso. Nelle cartelle di lavoro 1,1, il file zip rimane. Quando l'utente salva l'archivio, viene sostituito con un nuovo file zip.

È possibile creare manualmente l'archivio di una cartella di lavoro facendo clic con il pulsante destro del mouse su un pacchetto di cartella di lavoro e selezionando **Comprimi** in Mac oppure **Invia a > cartella compressa** in Windows. Rinominare quindi il file zip in modo che abbia un'estensione di file `.workbook`. Funziona solo con i pacchetti di cartelle di lavoro, non con i file di cartella di lavoro.

## <a name="related-links"></a>Collegamenti correlati

- [Benvenuti in cartelle di lavoro](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
