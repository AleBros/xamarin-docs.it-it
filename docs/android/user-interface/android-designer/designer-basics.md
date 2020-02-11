---
title: Nozioni di base su Xamarin.Android Designer
description: In questo argomento vengono presentate le funzionalità di Xamarin.Android Designer, viene illustrato come avviare la finestra di progettazione, vengono descritte le Area di progettazione e viene illustrato in dettaglio come utilizzare il riquadro proprietà per modificare le proprietà del widget.
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/05/2018
ms.openlocfilehash: 2d5f20326de56bca77dd8fdd742515e003f996e1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029508"
---
# <a name="xamarinandroid-designer-basics"></a>Nozioni di base su Xamarin.Android Designer

_In questo argomento vengono presentate le funzionalità di Xamarin.Android Designer, viene illustrato come avviare la finestra di progettazione, vengono descritte le Area di progettazione e viene illustrato in dettaglio come utilizzare il riquadro proprietà per modificare le proprietà del widget._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>Avvio della finestra di progettazione

La finestra di progettazione viene avviata automaticamente quando viene creato un layout oppure può essere avviata facendo doppio clic su un file di layout esistente. Ad esempio, facendo doppio clic su **activity_main. aXML** nella cartella **resources > layout** verrà caricata la finestra di progettazione, come illustrato in questo screenshot:

[schermata di![designer in Visual Studio](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

Analogamente, è possibile aggiungere un nuovo layout facendo clic con il pulsante destro del mouse sulla cartella **layout** nel **Esplora soluzioni** e selezionando **Aggiungi > nuovo elemento... Layout > Android**:

[![finestra di dialogo Aggiungi nuovo elemento](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

Viene creato un nuovo file di layout con **estensione aXML** che viene caricato nella finestra di progettazione.

> [!TIP]
> Le versioni più recenti di Visual Studio supportano l'apertura dei file con estensione xml all'interno di Android Designer.
>
> I file con estensione axml e xml sono entrambi supportati in Android Designer.

## <a name="designer-features"></a>Funzionalità della finestra di progettazione

La finestra di progettazione è costituita da diverse sezioni che supportano le varie funzionalità, come illustrato nello screenshot seguente:

[Diagramma![dei riquadri di progettazione](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

Quando si modifica un layout nella finestra di progettazione, è possibile utilizzare le funzionalità seguenti per creare e modellare la progettazione:

- **Area di progettazione** &ndash; facilita la costruzione visiva dell'interfaccia utente fornendo una rappresentazione modificabile del modo in cui il layout verrà visualizzato nel dispositivo. Il **area di progettazione** viene visualizzato all'interno del **riquadro di progettazione** (con la **barra degli strumenti della finestra di progettazione** posizionata sopra).

- **Riquadro di origine** &ndash; fornisce una visualizzazione dell'origine XML sottostante che corrisponde alla progettazione presentata nell' **area di progettazione**.

- **Barra degli strumenti della finestra di progettazione** &ndash; Visualizza un elenco di selettori: **dispositivo**, **versione**, **tema**, configurazione layout e impostazioni barra delle azioni. La **barra degli strumenti di progettazione** include anche le icone per l'avvio dell'editor del tema e per l'abilitazione della griglia di progettazione materiale.

- **Casella degli strumenti** &ndash; fornisce un elenco di widget e layout che è possibile trascinare nel **area di progettazione**.

- **Finestra proprietà** &ndash; elenca le proprietà del widget selezionato per la visualizzazione e la modifica.

- **Struttura documento** &ndash; Visualizza la struttura ad albero dei widget che compongono il layout. È possibile fare clic su un elemento nell'albero per fare in modo che sia selezionato nella **area di progettazione**. Inoltre, facendo clic su un elemento nell'albero vengono caricate le proprietà dell'elemento nella finestra **Proprietà** .

## <a name="design-surface"></a>Area di progettazione

La finestra di progettazione consente di trascinare e rilasciare i widget dalla casella degli strumenti nel **area di progettazione**. Quando si interagisce con i widget nella finestra di progettazione (aggiungendo nuovi widget o riposizionando quelli esistenti), vengono visualizzate linee verticali e orizzontali per contrassegnare i punti di inserimento disponibili. Nell'esempio seguente viene trascinato un nuovo `Button` widget nel **area di progettazione**:

[![righe di inserimento di esempio in Area di progettazione](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

Inoltre, i widget possono essere copiati: è possibile usare copia e incolla per copiare un widget oppure è possibile trascinare e rilasciare un widget esistente mentre si preme il tasto <kbd>CTRL</kbd> .

### <a name="designer-toolbar"></a>Barra degli strumenti della finestra di progettazione

La **barra degli strumenti della finestra di progettazione** (posizionata sopra la **area di progettazione**) presenta i selettori di configurazione e i menu degli

[Diagramma![della barra degli strumenti della finestra di progettazione](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

La **barra degli strumenti di progettazione** consente di accedere alle funzionalità seguenti:

- Il **selettore di layout alternativo** &ndash; consente di scegliere tra diverse versioni del layout.

- Il **selettore di dispositivo** &ndash; definisce un set di qualificatori, ad esempio le dimensioni dello schermo, la risoluzione e la disponibilità della tastiera, associati a un determinato dispositivo. È anche possibile aggiungere ed eliminare nuovi dispositivi.

- Il **selettore di versione di android** &ndash; la versione di Android di destinazione del layout. La finestra di progettazione eseguirà il rendering del layout in base alla versione di Android selezionata.

- **Selettore tema** &ndash; selezionare il tema dell'interfaccia utente per il layout.

- **Selettore configurazione** &ndash; seleziona la configurazione del dispositivo, ad esempio *verticale* o *orizzontale*.

- **Opzioni qualificatore risorse** &ndash; apre una finestra di dialogo in cui sono presenti menu a discesa per la selezione della *lingua*, della *modalità interfaccia utente*, della *modalità notte*e delle opzioni *dello schermo rotondo* .

- **Barra delle azioni impostazioni** &ndash; configura le impostazioni di barra delle azioni per il layout.

- **Editor tema** &ndash; apre l' *Editor del tema*, che consente di personalizzare gli elementi del tema selezionato.

- **Griglia di progettazione materiale** &ndash; Abilita o Disabilita la *griglia di progettazione materiali*. La voce di menu a discesa accanto alla griglia di progettazione materiale apre una finestra di dialogo che consente di personalizzare la griglia.

Ognuna di queste funzionalità è illustrata più dettagliatamente negli argomenti seguenti:

- I [qualificatori di risorse e le opzioni di visualizzazione](~/android/user-interface/android-designer/resource-qualifiers.md) forniscono informazioni dettagliate sul **selettore di dispositivo**, il selettore di versione di **Android**, il **selettore del tema**, il **selettore**  **Opzioni**e **barra delle azioni Impostazioni**.

- [In visualizzazioni di layout alternative](~/android/user-interface/android-designer/alternative-layout-views.md) viene illustrato come utilizzare il **selettore di layout alternativo**.

- [Xamarin.Android designer le funzionalità di progettazione del materiale](~/android/user-interface/android-designer/material-design-features.md) forniscono una panoramica completa dell' **Editor del tema** e della griglia di **progettazione del materiale**.

### <a name="context-menu-commands"></a>Comandi del menu di scelta rapida

È disponibile un menu di scelta rapida nella **area di progettazione** e nella **struttura del documento**. Questo menu Visualizza i comandi disponibili per il widget selezionato e il relativo contenitore, semplificando l'esecuzione di operazioni sui contenitori, che non sono sempre facili da selezionare nella **area di progettazione**. Di seguito è riportato un esempio di menu di scelta rapida:

[![menu di scelta rapida di esempio facendo clic con il pulsante destro del mouse sul Area di progettazione](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

In questo esempio, facendo clic con il pulsante destro del mouse su un `TextView` viene aperto un menu di scelta rapida che fornisce diverse opzioni:

- **LinearLayout** &ndash; apre un sottomenu per la modifica dell'elemento padre `LinearLayout` dell'`TextView`.

- **Eliminare**, **copiare**e **tagliare** &ndash; operazioni che si applicano al `TextView` con il pulsante destro del mouse.

### <a name="zoom-controls"></a>Controlli zoom

Il **area di progettazione** supporta lo zoom con diversi controlli, come illustrato:

[Diagramma![dei controlli di Area di progettazione zoom](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

Questi controlli semplificano la visualizzazione di determinate aree dell'interfaccia utente nella finestra di progettazione:

- **Evidenziare i contenitori** &ndash; evidenzia i contenitori nel **area di progettazione** in modo che risultino più facili da individuare mentre si esegue lo zoom avanti e indietro.

- Le **dimensioni normali** &ndash; eseguono il rendering del pixel di layout per il pixel, in modo che sia possibile visualizzare il modo in cui il layout esaminerà la risoluzione del dispositivo selezionato.

- **Adatta alla finestra** &ndash; imposta il livello di zoom in modo che l'intero layout sia visibile nell'area di progettazione.

- **Zoom avanti** &ndash; esegue lo zoom avanti in modo incrementale a ogni clic, ingrandindo il layout.

- **Zoom indietro** &ndash; esegue lo zoom indietro in modo incrementale a ogni clic, facendo in modo che il layout risulti più piccolo nel area di progettazione.

Si noti che l'impostazione di zoom scelta non influisce sull'interfaccia utente dell'applicazione in fase di esecuzione.

## <a name="switching-between-design-and-source-panes"></a>Spostamento tra i riquadri di progettazione e di origine

Nella striscia centrale tra i riquadri **progettazione** e **origine** sono disponibili diversi pulsanti che consentono di modificare la modalità di visualizzazione dei riquadri di **progettazione** e di **origine** :

[posizione del pulsante di visualizzazione del riquadro![](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

Questi pulsanti eseguono le operazioni seguenti:

- **Progettazione** &ndash; pulsante in primo piano, **progettazione**, seleziona il riquadro di **progettazione** . Quando si fa clic su questo pulsante, i riquadri **casella degli strumenti** e **Proprietà** sono abilitati e la **barra degli strumenti Editor di testo** non viene visualizzata. Quando si fa clic sul pulsante **Comprimi** (vedere di seguito), il riquadro di **progettazione** viene presentato da solo senza il riquadro di **origine** .

- **Scambia riquadri** &ndash; questo pulsante (che assomiglia a due frecce opposte) scambia i riquadri di **progettazione** e di **origine** in modo che il riquadro di **origine** si trovi a sinistra e il riquadro di **progettazione** si trovi a destra. Se si fa clic nuovamente, i riquadri vengono nuovamente scambiati nei rispettivi percorsi originali.

- **Source** &ndash; questo pulsante, che è simile a due parentesi angolari opposte, seleziona il riquadro di **origine** . Quando si fa clic su questo pulsante, i riquadri **casella degli strumenti** e **Proprietà** vengono disabilitati e la **barra degli strumenti Editor di testo** viene resa visibile nella parte superiore di Visual Studio. Quando si fa clic sul pulsante **Comprimi** (vedere di seguito), facendo clic sul pulsante **source** viene visualizzato il riquadro di **origine** anziché il riquadro di **progettazione** .

- **Suddivisione verticale** &ndash; questo pulsante (simile a una barra verticale), Visualizza i riquadri di **progettazione** e di **origine** affiancati. Questa è la disposizione predefinita.

- **Suddivisione orizzontale** &ndash; questo pulsante (simile a una barra orizzontale), Visualizza il riquadro di **progettazione** sopra il riquadro di **origine** . È possibile fare clic sui **riquadri di scambio** per posizionare il riquadro di **origine** sopra il riquadro di **progettazione** .

- **Comprimi riquadro** &ndash; questo pulsante, che è simile a due parentesi angolari a destra, "comprime" la visualizzazione a doppio riquadro della **progettazione** e dell' **origine** in una singola visualizzazione di uno di questi riquadri.
    Questo pulsante diventa il pulsante **Espandi riquadro** (che somiglia a due parentesi angolari a sinistra), su cui è possibile fare clic per tornare alla modalità di visualizzazione dual-pane (**progettazione** e **origine**).

Quando si fa clic sul **riquadro Comprimi** , viene visualizzato solo il riquadro di **progettazione** . Tuttavia, è possibile fare clic sul pulsante **origine** per visualizzare solo il riquadro di **origine** . Fare di nuovo clic sul pulsante **progettazione** per tornare al riquadro di **progettazione** .

## <a name="source-pane"></a>Riquadro di origine

Il riquadro **origine** Visualizza l'origine XML sottostante la progettazione mostrata nell' **area di progettazione**. Poiché entrambe le visualizzazioni sono disponibili allo stesso tempo, è possibile creare una progettazione dell'interfaccia utente passando tra una rappresentazione visiva della progettazione e l'origine XML sottostante per la progettazione:

[![origine XML di esempio nel riquadro di origine](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

Le modifiche apportate all'origine XML vengono immediatamente visualizzate nel **area di progettazione**; le modifiche apportate alla **area di progettazione** causano l'aggiornamento dell'origine XML visualizzata nel riquadro di **origine** di conseguenza. Quando si apportano modifiche a XML nel riquadro di **origine** , le funzionalità di completamento automatico e IntelliSense sono disponibili per velocizzare lo sviluppo dell'interfaccia utente basata su XML, come illustrato di seguito.

Per una maggiore facilità di spostamento quando si lavora con file XML lunghi, il riquadro di **origine** supporta la barra di scorrimento di Visual Studio, come illustrato a destra nello screenshot precedente. Per ulteriori informazioni sulla barra di scorrimento, vedere [come tenere traccia del codice personalizzando la barra di scorrimento](https://msdn.microsoft.com/library/dn237345.aspx).

### <a name="autocompletion"></a>Completamento automatico

Quando si inizia a digitare il nome di un attributo per un widget, è possibile premere <kbd>CTRL + barra spaziatrice</kbd> per visualizzare un elenco di possibili completamenti. Ad esempio, dopo aver immesso `android:lay` nell'esempio seguente (seguito dalla digitazione di <kbd>CTRL + barra spaziatrice</kbd>), viene visualizzato l'elenco seguente:

[![completamento automatico dell'attributo layout](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

Premere <kbd>invio</kbd> per accettare il primo completamento elencato o usare i tasti di direzione per scorrere fino al completamento desiderato e premere <kbd>invio</kbd>. In alternativa, è possibile utilizzare il mouse per scorrere e fare clic sul completamento desiderato.

### <a name="intellisense"></a>IntelliSense

Dopo aver immesso un nuovo attributo per un widget e aver iniziato ad assegnargli un valore, IntelliSense viene visualizzato dopo la digitazione di un carattere trigger e fornisce un elenco di valori validi da usare per l'attributo. Ad esempio, dopo aver immesso le prime virgolette doppie per `android:layout_width` nell'esempio seguente, viene visualizzato un selettore di completamento automatico per fornire l'elenco di scelte valide per questa larghezza:

[esempio di![IntelliSense per la larghezza del layout](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

Nella parte inferiore di questa finestra popup sono presenti due pulsanti, come descritto in rosso nello screenshot precedente. Facendo clic sul pulsante **Risorse progetto** a sinistra, l'elenco viene limitato a risorse che fanno parte del progetto di app, mentre facendo clic sul pulsante **risorse del Framework** a destra viene limitato l'elenco per visualizzare le risorse disponibili dal Framework.
Attivare o disattivare questi pulsanti: è possibile fare clic di nuovo per disabilitare l'azione di filtro fornita da ciascuno di essi.

## <a name="properties-pane"></a>Riquadro proprietà

La finestra di progettazione supporta la modifica delle proprietà del widget tramite il riquadro **Proprietà** : 

![Screenshot della Finestra Proprietà](designer-basics-images/vs/08-property-pad.png)

Le proprietà elencate nel riquadro **Proprietà** cambiano a seconda del widget selezionato nella **area di progettazione**.

### <a name="default-values"></a>Valori predefiniti

Le proprietà della maggior parte dei widget saranno vuote nella finestra **Proprietà** perché i rispettivi valori ereditano dal tema Android selezionato.
Nella finestra **Proprietà** verranno visualizzati solo i valori impostati in modo esplicito per il widget selezionato; non verranno visualizzati i valori ereditati dal tema.

### <a name="referencing-resources"></a>Riferimento a risorse

Alcune proprietà possono fare riferimento a risorse definite in file diversi dal file layout **. aXML** . I casi più comuni di questo tipo sono `string` e `drawable` risorse. Tuttavia, i riferimenti possono essere utilizzati anche per altre risorse, ad esempio valori `Boolean` e dimensioni. Quando una proprietà supporta i riferimenti alle risorse, viene visualizzata un'icona di esplorazione (un quadrato) accanto alla voce di testo per la proprietà. Questo pulsante consente di aprire un selettore di risorse quando viene selezionato.

Ad esempio, lo screenshot seguente mostra le opzioni disponibili quando si fa clic sul quadrato oscurato a destra del campo di testo per un `Text` widget nella finestra **Proprietà** :

[![elenco di opzioni di testo](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

Quando si fa clic su **Resource...** , viene visualizzata la finestra di dialogo **Seleziona risorsa** :

[![screenshot delle risorse di esempio con diverse risorse elencate](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

Da questo elenco è possibile selezionare una risorsa di testo da usare per il widget anziché impostare come hardcoded il testo nel riquadro **Proprietà** . Nell'esempio seguente viene illustrato il selettore di risorse per la proprietà `Src` di un `ImageView`:

[![la risorsa icona dell'elenco di selezione risorse per un ImageView](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

Facendo clic sul quadrato vuoto a destra della proprietà `Src` si apre la finestra di dialogo **Seleziona risorsa** con un elenco di risorse che vanno dai colori (come illustrato in precedenza) a drawables.

### <a name="boolean-property-references"></a>Riferimenti a proprietà booleane

Le proprietà *booleane* vengono in genere selezionate come segni di spunta accanto a una proprietà nel finestra Proprietà. È possibile designare una `true` o un valore di `false` selezionando o deselezionando questa casella di controllo oppure facendo clic sul quadrato scuro a destra della proprietà. Nell'esempio seguente, il testo viene modificato in tutti i limiti facendo clic sul riferimento alla proprietà di **tutti i Caps** booleano di testo associato alla `TextView` selezionata:

![Esempio di impostazione di proprietà booleane](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>Modifica delle proprietà inline

Il Android Designer supporta la modifica diretta di determinate proprietà nell' **area di progettazione** (pertanto non è necessario cercare tali proprietà nell'elenco delle proprietà). Le proprietà che possono essere modificate direttamente includono testo, margine e dimensioni.

### <a name="text"></a>Testo

Le proprietà di testo di alcuni widget, ad esempio `Button` e `TextView`, possono essere modificate direttamente nella **area di progettazione**. Se si fa doppio clic su un widget, questo viene inserito nella modalità di modifica, come illustrato di seguito:

![Risorsa di testo per la stringa Hello](designer-basics-images/vs/12-text-resource.png "Risorsa testo")

È possibile immettere un nuovo valore di testo oppure immettere una nuova stringa di risorsa. Nell'esempio seguente la risorsa `@string/hello` viene sostituita con il testo `CLICK THIS BUTTON`:

![Premere MAIUSC + INVIO per collegare automaticamente il testo a una nuova risorsa](designer-basics-images/vs/13-shift-enter-resource.png)

Questa modifica viene archiviata nella proprietà `text` del widget. non modifica il valore assegnato alla risorsa `@string/hello`.
Quando si esegue la chiave in una nuova stringa di testo, è possibile premere <kbd>maiusc</kbd>  +
<kbd>invio</kbd> per collegare automaticamente il testo immesso a una nuova risorsa.

### <a name="margin"></a>Margini

Quando si seleziona un widget, nella finestra di progettazione vengono visualizzati gli handle che consentono di modificare la dimensione o il margine del widget in modo interattivo. Facendo clic sul widget mentre è selezionata, viene attivata la modalità di modifica dei margini e la modalità di modifica delle dimensioni.

Quando si fa clic su un widget per la prima volta, vengono visualizzati gli handle dei margini. Se si sposta il puntatore del mouse su uno degli handle, nella finestra di progettazione viene visualizzata la proprietà che verrà modificata dall'handle (come illustrato di seguito per la proprietà `layout_marginLeft`):

![Screenshot che Mostra gli handle dei margini nella finestra di progettazione](designer-basics-images/vs/15-margin-handles.png)

Se è già stato impostato un margine, vengono visualizzate linee tratteggiate che indicano lo spazio occupato dal margine:

![Esempio di linee tratteggiate che contrassegnano lo spazio intorno a un pulsante](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>Dimensioni

Come indicato in precedenza, è possibile passare alla modalità di modifica delle dimensioni facendo clic su un widget mentre è già selezionato. Fare clic sul quadratino triangolare per impostare le dimensioni della dimensione indicata su `wrap_content`:

![Racchiudere il contenuto e ridimensionare gli handle](designer-basics-images/vs/17-wrap-content.png)

Se si fa clic sull'handle di **contenuto a capo** , il widget viene compattato in modo da non essere più grande del necessario per eseguire il wrapping del contenuto incluso. In questo esempio il testo del pulsante viene ridotto orizzontalmente, come illustrato nello screenshot successivo.

Quando il valore Size è impostato su **Wrap content**, nella finestra di progettazione viene visualizzato un handle triangolare che punta nella direzione opposta per modificare le dimensioni in `match_parent`:

![Associa handle padre](designer-basics-images/vs/18-match-parent.png)

Facendo clic sull'handle di corrispondenza padre, le dimensioni della dimensione vengono ripristinate in modo che **corrispondano** al widget padre.

È anche possibile trascinare il quadratino di ridimensionamento circolare (come illustrato nelle schermate precedenti) per ridimensionare il widget in un valore di `dp` arbitrario. Quando si esegue questa operazione, vengono presentati sia il **contenuto a capo** che la corrispondenza degli handle **padre** per la dimensione seguente:

![Quadratini di ridimensionamento circolari](designer-basics-images/vs/19-resize-dp.png)

Non tutti i contenitori consentono di modificare la `Size` di un widget. Si noti, ad esempio, che nella schermata riportata di seguito, con la `LinearLayout` selezionata, non vengono visualizzati gli handle di ridimensionamento:

![Nessun handle di ridimensionamento](designer-basics-images/vs/20-no-resize-handles.png)

## <a name="document-outline"></a>Struttura documento

La **struttura del documento** Visualizza la gerarchia dei widget del layout.
Nell'esempio seguente è selezionato il widget contenitore `LinearLayout`:

![Esempio di struttura documento](designer-basics-images/vs/21-document-outline.png)

Il contorno del widget selezionato (in questo caso, un `LinearLayout`) viene evidenziato anche nel **area di progettazione**. Il widget selezionato nella struttura del documento rimane sincronizzato con la controparte del **area di progettazione**. Questa operazione è utile per selezionare i gruppi di visualizzazione, che non sono sempre facili da selezionare nella **area di progettazione**.

La **struttura del documento** supporta copia e incolla oppure è possibile usare il trascinamento della selezione. Il trascinamento della selezione è supportato dalla **struttura del documento** alla **Area di progettazione** e dal **area di progettazione** alla struttura del **documento**. Inoltre, facendo clic con il pulsante destro del mouse su un elemento nella **struttura del documento** viene visualizzato il menu di scelta rapida per l'elemento (lo stesso menu di scelta rapida visualizzato quando si fa clic con il pulsante destro del mouse sullo stesso widget nella **area di progettazione**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="launching-the-designer"></a>Avvio della finestra di progettazione

La finestra di progettazione viene avviata automaticamente quando viene creato un layout oppure può essere avviata facendo doppio clic su un file con estensione aXML esistente. Ad esempio, facendo doppio clic su **Main. aXML** nella cartella **resources > layout** verrà caricata la finestra di progettazione, come illustrato di seguito:

[schermata di![designer in Visual Studio per Mac](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

Allo stesso modo, è possibile aggiungere un nuovo layout facendo clic con il pulsante destro del mouse sulla cartella **layout** nel **riquadro della soluzione** e scegliendo **Aggiungi > nuovo file > layout Android >** :

[![finestra di dialogo Aggiungi nuovo file](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

Viene creato un nuovo file con estensione aXML che viene caricato nel Area di progettazione.

> [!TIP]
> Le versioni più recenti di Visual Studio supportano l'apertura dei file con estensione xml all'interno di Android Designer.
>
> I file con estensione axml e xml sono entrambi supportati in Android Designer.

## <a name="designer-features"></a>Funzionalità della finestra di progettazione

La finestra di progettazione è costituita da diverse sezioni che supportano le varie funzionalità, come illustrato nello screenshot seguente:

[Diagramma![dei riquadri di progettazione](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

Quando si modifica un layout nella finestra di progettazione, è possibile utilizzare le funzionalità seguenti per creare e modellare la progettazione:

- **Area di progettazione** &ndash; facilita la costruzione visiva dell'interfaccia utente fornendo una rappresentazione modificabile del modo in cui il layout verrà visualizzato nel dispositivo.

- **Barra degli strumenti** &ndash; Visualizza un elenco di selettori: **dispositivo**, **versione**, **tema**, configurazione layout e impostazioni barra delle azioni. La barra degli strumenti include anche le icone per l'avvio dell'editor del tema e per l'abilitazione della griglia di progettazione materiali.

- **Casella degli strumenti** &ndash; fornisce un elenco di widget e layout che è possibile trascinare nel area di progettazione.

- **Riquadro delle proprietà** &ndash; elenca le proprietà del widget selezionato per la visualizzazione e la modifica.

- **Struttura documento** &ndash; Visualizza la struttura ad albero dei widget che compongono il layout. È possibile fare clic su un elemento nell'albero per selezionarlo nella finestra di progettazione. Inoltre, facendo clic su un elemento nell'albero vengono caricate le proprietà dell'elemento nel riquadro proprietà.

## <a name="toolbar"></a>ToolBar

La barra degli strumenti (posizionata sopra la Area di progettazione) presenta i selettori di configurazione e i menu degli strumenti:

[Diagramma![della barra degli strumenti della finestra di progettazione](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

La barra degli strumenti consente di accedere alle funzionalità seguenti:

- Il **selettore di layout alternativo** &ndash; consente di scegliere tra diverse versioni del layout.

- Il **selettore di dispositivo** &ndash; definisce un set di qualificatori associati a un dispositivo specifico, ad esempio le dimensioni dello schermo, la risoluzione e la disponibilità della tastiera. È anche possibile aggiungere ed eliminare nuovi dispositivi.

- Il **selettore di versione di android** &ndash; la versione di Android di destinazione del layout. La finestra di progettazione eseguirà il rendering del layout in base alla versione di Android selezionata.

- **Selettore tema** &ndash; selezionare il tema dell'interfaccia utente per il layout.

- **Selettore configurazione** &ndash; seleziona la configurazione del dispositivo, ad esempio *verticale* o *orizzontale*.

- **Opzioni qualificatore risorse** &ndash; apre una finestra di dialogo in cui sono presenti menu a discesa per la selezione della *lingua*, della *modalità interfaccia utente*, della *modalità notte*e delle opzioni *dello schermo rotondo* .

- **Barra delle azioni impostazioni** &ndash; configura le impostazioni di barra delle azioni per il layout.

- **Editor tema** &ndash; apre l' *Editor del tema*, che consente di personalizzare gli elementi del tema selezionato.

- **Griglia di progettazione materiale** &ndash; Abilita o Disabilita la *griglia di progettazione materiali*. La voce di menu a discesa accanto alla griglia di progettazione materiale apre una finestra di dialogo che consente di personalizzare la griglia.

Ognuna di queste funzionalità è illustrata più dettagliatamente negli argomenti seguenti:

I [qualificatori di risorse e le opzioni di visualizzazione](~/android/user-interface/android-designer/resource-qualifiers.md) forniscono informazioni dettagliate sul **selettore di dispositivo**, il selettore di versione di **Android**, il **selettore del tema**, il **selettore**  **Opzioni**e **barra delle azioni Impostazioni**.

[In visualizzazioni di layout alternative](~/android/user-interface/android-designer/alternative-layout-views.md) viene illustrato come utilizzare il **selettore di layout alternativo**.

Le [funzionalità di progettazione del materiale](~/android/user-interface/android-designer/material-design-features.md) forniscono una panoramica completa dell'editor del **tema** e della **griglia di progettazione materiale**.

## <a name="design-surface"></a>Area di progettazione

La finestra di progettazione consente di trascinare e rilasciare i widget dalla casella degli strumenti nel Area di progettazione. Quando si interagisce con i widget nella finestra di progettazione (aggiungendo nuovi widget o riposizionando quelli esistenti), vengono visualizzate linee verticali e orizzontali per contrassegnare i punti di inserimento disponibili. Nell'esempio seguente viene trascinato un nuovo `Button` widget nel Area di progettazione:

[![righe di inserimento di esempio in Area di progettazione](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

Inoltre, i widget possono essere copiati: è possibile usare copia e incolla per copiare un widget oppure è possibile trascinare e rilasciare un widget esistente mentre si preme il tasto <kbd>CTRL</kbd> .

### <a name="context-menu-commands"></a>Comandi del menu di scelta rapida

È disponibile un menu di scelta rapida nella Area di progettazione e nella struttura del documento. Questo menu Visualizza i comandi disponibili per il widget selezionato e il relativo contenitore, semplificando l'esecuzione di operazioni sui contenitori, che non sono sempre facili da selezionare nella Area di progettazione. Di seguito è riportato un esempio di menu di scelta rapida:

[![menu di scelta rapida di esempio facendo clic con il pulsante destro del mouse sul Area di progettazione](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

In questo esempio, facendo clic con il pulsante destro del mouse su un `Button` viene aperto un menu di scelta rapida che fornisce diverse opzioni:

- **LinearLayout** &ndash; apre un sottomenu per la modifica dell'elemento padre `LinearLayout` dell'`Button`.

- **Taglia**, **copia**ed **Elimina** &ndash; operazioni che si applicano al `Button` con il pulsante destro del mouse.

### <a name="zoom-controls"></a>Controlli zoom

Il Area di progettazione supporta lo zoom con diversi controlli, come illustrato:

[Diagramma![dei controlli di Area di progettazione zoom](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

Questi controlli semplificano la visualizzazione di determinate aree dell'interfaccia utente nella finestra di progettazione:

- **Evidenziare i contenitori** &ndash; evidenzia i contenitori nel area di progettazione in modo che risultino più facili da individuare mentre si esegue lo zoom avanti e indietro.

- Le **dimensioni normali** &ndash; eseguono il rendering del pixel di layout per il pixel, in modo che sia possibile visualizzare il modo in cui il layout esaminerà la risoluzione del dispositivo selezionato.

- **Adatta alla finestra** &ndash; imposta il livello di zoom in modo che l'intero layout sia visibile nell'area di progettazione.

- **Zoom avanti** &ndash; esegue lo zoom avanti in modo incrementale a ogni clic, ingrandindo il layout.

- **Zoom indietro** &ndash; esegue lo zoom indietro in modo incrementale a ogni clic, facendo in modo che il layout risulti più piccolo nel area di progettazione.

Si noti che l'impostazione di zoom scelta non influisce sull'interfaccia utente dell'applicazione in fase di esecuzione.

## <a name="property-pad"></a>Riquadro delle proprietà

La finestra di progettazione supporta la modifica delle proprietà del widget tramite il **riquadro delle proprietà**. Le proprietà elencate nel riquadro delle proprietà cambiano a seconda del widget selezionato nell'area di progettazione. Quando si seleziona il `Button` nell'esempio precedente, vengono visualizzate le proprietà del widget `Button`:

[![screenshot del riquadro delle proprietà](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>Sezioni di riempimento delle proprietà

Il riquadro delle proprietà è suddiviso in diverse sezioni che raggruppano proprietà simili &ndash; in questo modo è più semplice individuare le proprietà di interesse:

- **Widget** &ndash; proprietà principali del widget, ad esempio `id`, `visibility`, `text` e così via. Le proprietà per la gestione del contenuto del widget vengono in genere inserite qui.

- **Stile** &ndash; proprietà che modificano l'aspetto visivo del widget, ad esempio `font`, `text color`, `background` e così via.

- **Layout** &ndash; proprietà che impostano la posizione e le dimensioni del widget.

- **Scorrere** &ndash; proprietà di scorrimento.

- **Comportamento** &ndash; flag che impostano il comportamento del widget.

### <a name="default-values"></a>Valori predefiniti

Le proprietà della maggior parte dei widget saranno vuote nel **riquadro delle proprietà** perché i relativi valori ereditano dal tema Android selezionato. Il **riquadro delle proprietà** mostrerà solo i valori impostati in modo esplicito per il widget selezionato; non verranno visualizzati i valori ereditati dal tema.

### <a name="referencing-resources"></a>Riferimento a risorse

Alcune proprietà possono fare riferimento a risorse definite in file diversi dal file layout **. aXML** . I casi più comuni di questo tipo sono `string` e `drawable` risorse. Tuttavia, i riferimenti possono essere utilizzati anche per altre risorse, ad esempio valori `Boolean` e dimensioni.
Quando una proprietà supporta i riferimenti alle risorse, viene visualizzata un'icona di ricerca (con i puntini di sospensione, &hellip;) accanto alla voce di testo per la proprietà.
Quando si fa clic su questo pulsante, viene aperto un selettore di risorse.

Ad esempio, nella schermata seguente sono illustrate le risorse disponibili quando si fa clic sui puntini di sospensione a destra del campo di testo per un `Button` widget nel **riquadro delle proprietà**:

[schermata delle risorse di esempio![con due risorse elencate](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

Nell'esempio seguente viene illustrato il selettore di risorse per la proprietà `Src` di un `ImageView`:

[![la risorsa icona dell'elenco di selezione risorse per un ImageView](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>Riferimenti a proprietà booleane

Le proprietà *booleane* vengono in genere visualizzate come una casella di controllo nel riquadro proprietà. Quando una proprietà `Boolean` supporta i riferimenti alle risorse, viene visualizzata una piccola casella di controllo accanto alla proprietà. Una casella di controllo selezionata indica che `true` e una casella vuota significa `false`. È anche possibile immettere direttamente un valore, ad esempio `true` o `false`. Posizionando il puntatore del mouse sull'input viene visualizzata un'icona di campo di testo di piccole dimensioni. È possibile fare clic su di esso se si desidera immettere manualmente il valore.

[![esempio di impostazione di proprietà booleane](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)

## <a name="grouped-properties"></a>Proprietà raggruppate

Alcuni widget hanno proprietà multivalore raggruppate insieme, ad esempio `Padding`, ad esempio. Questi valori di proprietà sono elencati nel **riquadro delle proprietà** in un'unica riga espandibile. Alcune di queste proprietà possono essere modificate direttamente nella riga raggruppata, ad esempio la proprietà `Padding` illustrata di seguito:

[![impostazioni di esempio per la proprietà Padding](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>Modifica delle proprietà inline

Il Android Designer supporta la modifica diretta di determinate proprietà nell'Area di progettazione (pertanto non è necessario cercare tali proprietà nell'elenco delle proprietà). Le proprietà che possono essere modificate direttamente includono testo, margine e dimensioni.

### <a name="text"></a>Testo

Le proprietà di testo di alcuni widget, ad esempio `Button` e `TextView`, possono essere modificate direttamente nella Area di progettazione. Se si fa doppio clic su un widget, questo viene inserito nella modalità di modifica, come illustrato di seguito:

[![risorsa di testo per la stringa Hello](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

È possibile immettere un nuovo valore di testo oppure immettere una nuova stringa di risorsa. Nell'esempio seguente la risorsa `@string/hello` viene sostituita con il testo `CLICK THIS BUTTON`:

[![MAIUSC + INVIO per collegare automaticamente il testo a una nuova risorsa](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

Questa modifica viene archiviata nella proprietà `text` del widget. non modifica il valore assegnato alla risorsa `@string/hello`.
Quando si esegue la chiave in una nuova stringa di testo, è possibile premere <kbd>maiusc</kbd>  +
<kbd>invio</kbd> per collegare automaticamente il testo immesso a una nuova risorsa.

### <a name="margin"></a>Margini

Quando si seleziona un widget, nella finestra di progettazione vengono visualizzati gli handle che consentono di modificare la dimensione o il margine del widget in modo interattivo. Facendo clic sul widget mentre è selezionata, viene attivata la modalità di modifica dei margini e la modalità di modifica delle dimensioni.

Quando si fa clic su un widget per la prima volta, vengono visualizzati gli handle dei margini. Se si sposta il puntatore del mouse su uno degli handle, nella finestra di progettazione viene visualizzata la proprietà che verrà modificata dall'handle (come illustrato di seguito per la proprietà `layout_marginLeft`):

[![screenshot che Mostra gli handle dei margini nella finestra di progettazione](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

Se è già stato impostato un margine, vengono visualizzate linee tratteggiate che indicano lo spazio occupato dal margine:

[![esempio di linee tratteggiate che contrassegnano lo spazio intorno a un pulsante](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>Dimensioni

Come indicato in precedenza, è possibile passare alla modalità di modifica delle dimensioni facendo clic su un widget mentre è già selezionato. Fare clic sul quadratino triangolare per impostare le dimensioni della dimensione indicata su `wrap_content`:

[![a capo contenuto e ridimensionamento](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

Quando si fa clic sull'handle di **contenuto a capo** , il widget viene compattato in modo da non essere più grande del necessario per eseguire il wrapping del contenuto incluso. In questo esempio il testo del pulsante viene ridotto orizzontalmente, come illustrato nello screenshot successivo.

Quando il valore Size è impostato su **Wrap content**, nella finestra di progettazione viene visualizzato un handle triangolare che punta nella direzione opposta per modificare le dimensioni in `match_parent`:

[handle padre corrispondenza![](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

Facendo clic sull'handle di corrispondenza padre, le dimensioni della dimensione vengono ripristinate in modo che **corrispondano** al widget padre.

È anche possibile trascinare il quadratino di ridimensionamento circolare (come illustrato nelle schermate precedenti) per ridimensionare il widget in un valore di `dp` arbitrario. Quando si esegue questa operazione, vengono presentati sia il **contenuto a capo** che la corrispondenza degli handle **padre** per la dimensione seguente:

[handle di ridimensionamento circolari![](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

Non tutti i contenitori consentono di modificare la `Size` di un widget. Si noti, ad esempio, che nella schermata riportata di seguito, con la `LinearLayout` selezionata, non vengono visualizzati gli handle di ridimensionamento:

[![nessun handle di ridimensionamento](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>Struttura documento

La **struttura del documento** Visualizza la gerarchia dei widget del layout.
Nell'esempio seguente è selezionato il widget contenitore `LinearLayout`:

[Struttura documento![](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

Il contorno del widget selezionato (in questo caso, un `LinearLayout`) viene evidenziato anche nel Area di progettazione. Il widget selezionato nella struttura del documento rimane sincronizzato con la controparte del Area di progettazione. Questa operazione è utile per selezionare i gruppi di visualizzazione, che non sono sempre facili da selezionare nella Area di progettazione.

La struttura del documento supporta copia e incolla oppure è possibile usare il trascinamento della selezione. Il trascinamento della selezione è supportato dalla struttura del documento alla Area di progettazione e dal Area di progettazione alla struttura del documento. Inoltre, facendo clic con il pulsante destro del mouse su un elemento nella struttura del documento viene visualizzato il menu di scelta rapida per l'elemento (lo stesso menu di scelta rapida visualizzato quando si fa clic con il pulsante destro del mouse sullo stesso widget nella Area di progettazione).

-----
