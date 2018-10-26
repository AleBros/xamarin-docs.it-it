---
title: Nozioni fondamentali sulla progettazione di xamarin. Android
description: In questo argomento introduce le funzionalità di progettazione di xamarin. Android, viene illustrato come avviare la finestra di progettazione, descrive l'area di progettazione e illustra in dettaglio come usare il riquadro proprietà per modificare le proprietà del widget.
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/05/2018
ms.openlocfilehash: fe909d72f3c6d6733318b5dcbd1858a1a9e28b37
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108197"
---
# <a name="xamarinandroid-designer-basics"></a>Nozioni fondamentali sulla progettazione di xamarin. Android

_In questo argomento introduce le funzionalità di progettazione di xamarin. Android, viene illustrato come avviare la finestra di progettazione, descrive l'area di progettazione e illustra in dettaglio come usare il riquadro proprietà per modificare le proprietà del widget._


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>Avviare la finestra di progettazione

La finestra di progettazione viene avviato automaticamente quando viene creato un layout, o possono essere avviata facendo doppio clic su un file di layout esistente. Ad esempio, facendo doppio clic su **activity_main.axml** nel **risorse > Layout** cartella caricherà la finestra di progettazione, come illustrato in questo screenshot:

[![Schermata della finestra di progettazione di Visual Studio](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

Allo stesso modo, è possibile aggiungere un nuovo layout facendo clic con il **layout** cartella la **Esplora soluzioni** e selezionando **Aggiungi > Nuovo elemento... > Layout Android**:

[![Aggiungi finestra di dialogo Nuovo elemento](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

Verrà creata una nuova **axml** file di layout e lo carica in una finestra di progettazione.

## <a name="designer-features"></a>Funzionalità della finestra di progettazione

La finestra di progettazione è costituita da diverse sezioni che supportano le varie funzionalità, come illustrato nello screenshot seguente:

[![Diagramma dei riquadri di progettazione](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

Quando si modifica un layout nella finestra di progettazione, per creare e definire la progettazione è usare le funzionalità seguenti:

-   **Area di progettazione** &ndash; facilita la costruzione visual dell'interfaccia utente fornendo una rappresentazione modificabile di modalità di visualizzazione del layout nel dispositivo. Il **nell'area di progettazione** viene visualizzato all'interno di **riquadro di progettazione** (con il **barra degli strumenti Progettazione** posizionato sopra di esso).

-   **Riquadro di origine** &ndash; consente di visualizzare l'origine XML sottostante che corrisponde alla progettazione nel **nell'area di progettazione**.

-   **Finestra di progettazione della barra degli strumenti** &ndash; Visualizza un elenco di selettori: **dispositivo**, **versione**, **tema**, configurazione di layout e le impostazioni della barra delle azioni. Il **sulla barra degli strumenti Progettazione** include anche le icone per avviare l'Editor del tema e per l'abilitazione della griglia di struttura materiali.

-   **Casella degli strumenti** &ndash; fornisce un elenco di widget e layout che è possibile trascinare e rilasciare il **nell'area di progettazione**.

-   **Finestra delle proprietà** &ndash; sono elencate le proprietà del widget selezionato per la visualizzazione e modifica.

-   **Struttura documento** &ndash; Visualizza l'albero di widget che compongono il layout. È possibile fare clic su un elemento nell'albero in modo che si siano selezionati nella **nell'area di progettazione**. Inoltre, fare clic su un elemento nell'albero della carica le proprietà dell'elemento nella **proprietà** finestra.

## <a name="design-surface"></a>Area di progettazione

La finestra di progettazione consente di trascinare e rilasciare i widget dalla casella degli strumenti di **nell'area di progettazione**. Quando si interagisce con i widget nella finestra di progettazione (mediante l'aggiunta di nuovi widget o riposizionamento quelle esistenti), vengono visualizzate linee orizzontali e verticali per contrassegnare i punti di inserimento disponibili. Nell'esempio seguente, una nuova `Button` widget trascinato nel **nell'area di progettazione**:

[![Esempio di linee di inserimento nell'area di progettazione](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

Inoltre, possono essere copiati i widget: è possibile usare copia e Incolla per copiare un widget o è possibile trascinare e rilasciare un widget esistente mentre si tiene premuto il <kbd>CTRL</kbd> chiave.

### <a name="designer-toolbar"></a>Sulla barra degli strumenti della finestra di progettazione

Il **sulla barra degli strumenti di progettazione** (posizionato sopra il **nell'area di progettazione**) presenta i selettori di configurazione e i menu degli strumenti:

[![Diagramma della barra degli strumenti della finestra di progettazione](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

Il **sulla barra degli strumenti Progettazione** fornisce l'accesso alle funzionalità seguenti:

-   **Selettore di Layout alternativi** &ndash; consente di selezionare rispetto alle versioni di un layout diverso.

-   **Selettore di dispositivo** &ndash; definisce un set di qualificatori (ad esempio dimensioni dello schermo, la risoluzione e la disponibilità della tastiera) associato a un dispositivo specifico. È anche possibile aggiungere ed eliminare nuovi dispositivi.

-   **Selettore di versione di Android** &ndash; The Android versione che il layout è destinato a. La finestra di progettazione verrà eseguito il rendering del layout in base alla versione di Android selezionata.

-   **Selettore temi** &ndash; consente di selezionare il tema dell'interfaccia utente per il layout.

-   **Selettore configurazione** &ndash; seleziona la configurazione del dispositivo, ad esempio *verticale* oppure *panorama*.

-   **Opzioni per qualificatori di risorse** &ndash; apre una finestra di dialogo che presenta i menu a discesa per la selezione *Language*, *modalità UI*, *modalità notturna*, e *Schermo arrotondato* opzioni.

-   **Impostazioni azione barra** &ndash; Configura le impostazioni della barra delle azioni per il layout.

-   **Editor dei temi** &ndash; consente di aprire le *Theme Editor*, che rende possibile per personalizzare gli elementi del tema selezionato.

-   **Griglia di struttura materiali** &ndash; Abilita o disabilita le *griglia di struttura materiali*. La voce di menu di riepilogo a discesa accanto a griglia di struttura materiali apre una finestra di dialogo che consente di personalizzare la griglia.

Ognuna di queste funzionalità è illustrata in dettaglio negli argomenti seguenti:

-   [Qualificatori delle risorse e opzioni di visualizzazione](~/android/user-interface/android-designer/resource-qualifiers.md) fornisce informazioni dettagliate sulle **selettore di dispositivo**, **selettore di versione di Android**, **selettore del tema**, **Selettore configurazione**, **risorsa qualificazioni opzioni**, e **le impostazioni della barra delle azioni**.

-   [Viste di Layout alternativi](~/android/user-interface/android-designer/alternative-layout-views.md) spiega come usare il **alternativa Layout selettore**.

-   [Funzionalità di progettazione di xamarin. Android Designer materiale](~/android/user-interface/android-designer/material-design-features.md) fornisce una panoramica completa del **Theme Editor** e il **griglia di struttura materiali**.

### <a name="context-menu-commands"></a>Comandi di menu di scelta rapida

Un menu di scelta rapida è disponibile sia nel **nell'area di progettazione** e nel **struttura documento**. Questo menu consente di visualizzare i comandi disponibili per il widget selezionato e il relativo contenitore, rendendo più semplice per eseguire operazioni su contenitori (che non sono sempre facili da selezionare nella **nell'area di progettazione**). Di seguito è riportato un esempio di un menu di scelta rapida:

[![Esempio menu di scelta rapida quando il pulsante destro del mouse nell'area di progettazione](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

In questo esempio, facendo clic su un `TextView` apre un menu di scelta rapida sono disponibili diverse opzioni:

-   **LinearLayout** &ndash; viene aperto un sottomenu per la modifica di `LinearLayout` padre del `TextView`.

-   **Eliminare**, **copia**, e **Taglia** &ndash; operazioni eseguibili per il pulsante destro del mouse `TextView`.


### <a name="zoom-controls"></a>Controlli zoom

Il **nell'area di progettazione** supporta lo zoom tramite vari controlli, come illustrato:

[![Diagramma dei controlli zoom nell'area di progettazione](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

Questi controlli rendono più semplice visualizzare determinate aree dell'interfaccia utente nella finestra di progettazione:

-   **Evidenzia contenitori** &ndash; evidenzia contenitori sulle **nell'area di progettazione** in modo che siano più semplici da individuare durante lo zoom avanti e indietro.

-   **Dimensioni normali** &ndash; esegue il rendering di layout pixel per pixel in modo che è possibile vedere un layout con la risoluzione del dispositivo selezionato.

-   **Adatta alla finestra** &ndash; imposta il livello di zoom in modo che l'intero layout è visibile nell'area di progettazione.

-   **Zoom avanti** &ndash; viene ingrandita in modo incrementale con ogni clic, per l'ingrandimento del layout.

-   **Zoom indietro** &ndash; esegue lo zoom indietro in modo incrementale con ogni clic, rendendo il layout vengono visualizzate più piccoli nell'area di progettazione.

Si noti che l'impostazione dello zoom scelto non interessa con l'interfaccia utente dell'applicazione in fase di esecuzione.

## <a name="switching-between-design-and-source-panes"></a>Il passaggio tra i riquadri di progettazione e di origine

Nell'elenco di center tra il **progettazione** e **origine** riquadri, esistono diversi pulsanti che consentono di modificare il modo in **progettazione** e **origine**vengono visualizzati i riquadri:

[![Posizioni dei pulsanti riquadro visualizzazione](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

Questi pulsanti le operazioni seguenti:

-   **Progettazione** &ndash; sul pulsante, più in alto **progettazione**, consente di selezionare il **progettazione** riquadro. Quando viene fatto clic su questo pulsante, il **casella degli strumenti** e **proprietà** riquadri sono abilitati e la **sulla barra degli strumenti Editor di testo** non viene visualizzata. Quando la **Comprimi** viene fatto clic sul pulsante (vedere sotto), il **progettazione** riquadro viene visualizzato da sola senza la **origine** riquadro.

-   **Scambia riquadri** &ndash; Scambia questo pulsante (che è simile a due frecce opposti) le **progettazione** e **origine** riquadri in modo che il **origine** riquadro è in sinistra e il **progettazione** è riquadro a destra. Fare clic nuovamente scambi questi riquadri eseguire il backup nei percorsi originali.

-   **Origine** &ndash; consente di selezionare questo pulsante (che è simile a due parentesi angolari opposti) le **origine** riquadro. Quando viene fatto clic su questo pulsante, il **casella degli strumenti** e **proprietà** riquadri sono disabilitati e le **sulla barra degli strumenti Editor di testo** viene reso visibile nella parte superiore di Visual Studio. Quando il **Comprimi** viene fatto clic sul pulsante (vedere sotto), fare clic sul **origine** pulsante consente di visualizzare il **origine** riquadro anziché il **progettazione** riquadro.

-   **Divisione verticale** &ndash; consente di visualizzare sul pulsante (che è simile a una barra verticale), il **progettazione** e **origine** riquadri side-by-side. Si tratta la disposizione predefinita.

-   **Divisione orizzontale** &ndash; consente di visualizzare sul pulsante (che è simile a una barra orizzontale), il **progettazione** sopra il **origine** riquadro. **Scambia riquadri** può essere selezionato per inserire il **origine** sopra il **progettazione** riquadro.

-   **Comprimi riquadro** &ndash; questo pulsante (che è simile a due angolari verso destra) "viene compresso" la visualizzazione dual-riquadro delle **progettazione** e **origine** in una singola visualizzazione di uno di Questi riquadri.
    Questo pulsante diventa il **riquadro espandere** pulsante (che assomiglia a due angolari verso sinistra), che può essere selezionato per la restituzione della visualizzazione a riquadro dual (**progettazione** e **origine**) la modalità di visualizzazione.

Quando **Comprimi riquadro** viene selezionata, solo le **progettazione** viene visualizzato il riquadro. Tuttavia, è possibile fare clic il **origine** pulsante per visualizzare invece solo la **origine** riquadro. Fare clic sui **Design** pulsante per tornare alla finestra la **progettazione** riquadro.

## <a name="source-pane"></a>Riquadro origine

Il **origine** riquadro Visualizza l'origine XML sottostante la progettazione illustrata nel **nell'area di progettazione**. Poiché entrambe le visualizzazioni sono disponibili nello stesso momento, è possibile creare una progettazione dell'interfaccia utente andando avanti e indietro tra una rappresentazione visiva della progettazione e l'origine XML sottostante per la progettazione:

[![Origine XML di esempio nel riquadro di origine](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

Le modifiche apportate all'origine XML vengono immediatamente eseguito il rendering nel **nell'area di progettazione**; le modifiche apportate nel **nell'area di progettazione** causano l'origine XML visualizzato nei **origine** riquadro per aggiornato di conseguenza. Quando si apportano modifiche al codice XML nel **origine** riquadro, completamento automatico e le funzionalità di IntelliSense sono disponibili per velocizzare lo sviluppo dell'interfaccia utente basata su XML come descritto di seguito.

Per maggiore semplicità per la navigazione quando si lavora con file XML lunghi, il **origine** riquadro supporta la barra di scorrimento di Visual Studio (come illustrato a destra nella schermata precedente). Per altre informazioni sulla barra di scorrimento, vedere [come tenere traccia del codice personalizzando la barra di scorrimento](https://msdn.microsoft.com/library/dn237345.aspx).


### <a name="autocompletion"></a>Completamento automatico

Quando si inizia a digitare il nome di un attributo per un widget, è possibile premere <kbd>CTRL + BARRA SPAZIATRICE</kbd> per visualizzare un elenco di completamenti possibili. Ad esempio, dopo aver immesso `android:lay` nell'esempio seguente (seguito digitando <kbd>CTRL + BARRA SPAZIATRICE</kbd>), viene visualizzato nell'elenco seguente:

[![Completamento automatico dell'attributo di layout](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

Premere <kbd>invio</kbd> per accettare il primo completamento elencato oppure usare i tasti di direzione per scorrere il completamento desiderato e premere <kbd>invio</kbd>. In alternativa, è possibile utilizzare il mouse per scorrere e fare clic su di completamento desiderato.

### <a name="intellisense"></a>IntelliSense

Dopo avere immesso un nuovo attributo per un widget e iniziare ad assegnare un valore, IntelliSense viene visualizzata dopo che un carattere del trigger è tipizzato e offre un elenco di valori validi da usare per l'attributo. Ad esempio, dopo il primo doppie, viene inserita per `android:layout_width` nell'esempio seguente, un selettore di completamento automatico viene visualizzata per fornire l'elenco di scelte valide per questa larghezza:

[![Esempio di IntelliSense per la larghezza del layout](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

Nella parte inferiore di questo controllo popup sono due pulsanti (come indicato in rosso nello screenshot precedente). Facendo clic sul **le risorse del progetto** pulsante a sinistra consente di limitare l'elenco per le risorse che fanno parte del progetto dell'app, mentre si fa clic il **risorse del Framework** pulsante a destra consente di limitare l'elenco per visualizzare le risorse disponibili dal framework.
Questi pulsanti attivare o disattivare: è possibile fare clic di nuovo in modo da disabilitare l'azione di filtro da ogni area.



## <a name="properties-pane"></a>Riquadro proprietà

La finestra di progettazione supporta la modifica delle proprietà di widget tramite il **proprietà** riquadro: 

![Screenshot della finestra proprietà](designer-basics-images/vs/08-property-pad.png)

Le proprietà elencate nella **delle proprietà** modifica riquadro a seconda di quale widget è selezionato nella **nell'area di progettazione**.

### <a name="default-values"></a>Valori predefiniti

Le proprietà della maggior parte dei widget saranno vuote nel **proprietà** finestra perché i relativi valori ereditano dal tema selezionato Android.
Il **proprietà** finestra mostrerà solo i valori impostati in modo esplicito per il widget selezionato; non verranno invece visualizzati i valori che vengono ereditati dal tema.

### <a name="referencing-resources"></a>Riferimento alle risorse

Alcune proprietà possono fare riferimento a risorse che sono definite nel file, ad eccezione del layout **axml** file. Il maggior parte dei casi di questo tipo vengono `string` e `drawable` le risorse. Tuttavia, i riferimenti utilizzabile anche per le altre risorse, ad esempio `Boolean` valori e le dimensioni. Quando una proprietà supporta i riferimenti alle risorse, viene visualizzata un'icona di ricerca (un quadrato) accanto alla voce di testo per la proprietà. Questo pulsante apre un selettore di risorse quando si fa clic.

Ad esempio, lo screenshot seguente mostra le opzioni disponibili quando si fa clic più scura quadrato a destra del campo di testo per un `Text` widget nel **proprietà** finestra:

[![Esempio di elenco di opzioni di testo](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

Quando **risorsa...**  viene selezionata, il **Seleziona risorsa** viene visualizzata una finestra di dialogo:

[![Screenshot delle risorse di esempio con diverse risorse elencati](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

In questo elenco, è possibile selezionare una risorsa di testo da utilizzare per anziché codificare il testo nel widget di **proprietà** riquadro. L'esempio seguente illustra il selettore risorse per la `Src` proprietà di un `ImageView`:

[![Selettore risorse listato risorsa icona per un ImageView](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

Facendo clic su quadrato vuoto a destra del `Src` verrà visualizzata la proprietà di **Seleziona risorsa** finestra di dialogo con un elenco di risorse, compreso tra i colori (come illustrato in precedenza) e drawable.


### <a name="boolean-property-references"></a>Riferimenti alle proprietà booleane

*Booleano* le proprietà vengono in genere selezionate come segni di spunta accanto a una proprietà nella finestra Proprietà. È possibile designare una `true` o `false` valore, selezionare o deselezionare questa casella di controllo, oppure è possibile selezionare un riferimento a proprietà facendo clic sul quadrato piena scuro a destra della proprietà. Nell'esempio seguente, il testo viene modificato per tutte maiuscole facendo il **testo tutte maiuscole** riferimento della proprietà booleana associata all'elemento selezionato `TextView`:

![Esempio di impostazione di proprietà booleane](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>Modifica le proprietà inline

La finestra di progettazione Android supporta la modifica diretta di determinate proprietà per il **nell'area di progettazione** (quindi non è necessario eseguire la ricerca di queste proprietà nell'elenco delle proprietà). Le proprietà che possono essere modificate direttamente includono testo, margine e dimensioni.

### <a name="text"></a>Testo

Le proprietà di testo di alcuni widget (ad esempio `Button` e `TextView`), possono essere modificate direttamente nel **nell'area di progettazione**. Fare doppio clic su un widget verranno inserirlo nella modalità di modifica, come illustrato di seguito:

![Risorsa di testo per la stringa hello](designer-basics-images/vs/12-text-resource.png "risorsa di testo")

È possibile immettere un nuovo valore di testo oppure è possibile immettere una nuova stringa di risorsa. Nell'esempio seguente, il `@string/hello` risorsa è stata sostituita con il testo, `CLICK THIS BUTTON`:

![MAIUSC + INVIO per collegare automaticamente il testo in una nuova risorsa](designer-basics-images/vs/13-shift-enter-resource.png)

Questa modifica viene archiviata all'interno del widget `text` proprietà; non modifica il valore assegnato per il `@string/hello` risorsa.
Quando è una chiave in una nuova stringa di testo, è possibile premere <kbd>Shift</kbd> +
<kbd>invio</kbd> per collegare automaticamente il testo immesso in una nuova risorsa.

### <a name="margin"></a>Margini

Quando si seleziona un widget, la finestra di progettazione vengono visualizzati handle che consentono di modificare le dimensioni o un margine del widget in modo interattivo. Facendo clic sul widget mentre è selezionato Alterna tra modalità di modifica dei margini e la modalità di modifica delle dimensioni.

Quando si fa clic su un widget per la prima volta, vengono visualizzati gli handle dei margini. Se si sposta il puntatore del mouse su uno dei quadratini di ridimensionamento, la finestra di progettazione consente di visualizzare le proprietà che verrà modificata l'handle (come illustrato di seguito per il `layout_marginLeft` proprietà):

![Screenshot che Mostra margine gestisce nella finestra di progettazione](designer-basics-images/vs/15-margin-handles.png)

Se un margine è già stato impostato, vengono visualizzate linee tratteggiate, che indica lo spazio che occupa il margine:

![Esempio di contrassegno di spazio intorno a un pulsante che linee tratteggiate](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>Dimensione

Come accennato in precedenza, è possibile passare alla modalità di modifica delle dimensioni, fare clic su un widget mentre è già selezionato. Fare clic sull'handle triangolare per impostare le dimensioni per la dimensione indicata per `wrap_content`:

![Handle di contenuto a capo automatico e ridimensionamento](designer-basics-images/vs/17-wrap-content.png)

Facendo clic sui **eseguire il wrapping di contenuto** handle si riduce il widget in tale dimensione, in modo che sia non superiore rispetto al necessario per eseguire il wrapping del contenuto. In questo esempio, il testo del pulsante si riduce in senso orizzontale, come illustrato nel prossimo screenshot.

Quando il valore delle dimensioni è impostato su **eseguire il wrapping di contenuto**, la finestra di progettazione vengono visualizzati un handle triangolare che punta nella direzione opposta per la modifica delle dimensioni a `match_parent`:

![Handle padre di corrispondenza](designer-basics-images/vs/18-match-parent.png)

Facendo clic sui **corrispondenza padre** handle consente di ripristinare le dimensioni in tale dimensione, in modo che sia quello utilizzato per il widget padre.

Inoltre, è possibile trascinare il quadratino di ridimensionamento circolare (come illustrato negli screenshot precedente) per ridimensionare un widget a un oggetto arbitrario `dp` valore. Quando si esegue questa operazione, entrambe **eseguire il wrapping di contenuto** e **corrispondenza padre** vengono presentati gli handle per la dimensione:

![Quadratini di ridimensionamento circolare](designer-basics-images/vs/19-resize-dp.png)

Non tutti i contenitori consentono la modifica di `Size` di un widget. Ad esempio, si noti che nella schermata seguente con il `LinearLayout` selezionata, i quadratini di ridimensionamento non vengono visualizzati:

![Senza quadratini di ridimensionamento](designer-basics-images/vs/20-no-resize-handles.png)


## <a name="document-outline"></a>Struttura documento

Il **struttura documento** Visualizza la gerarchia di widget del layout.
Nell'esempio seguente, il che contiene `LinearLayout` widget è selezionato:

![Esempio di struttura documento](designer-basics-images/vs/21-document-outline.png)

La struttura del widget selezionato (in questo caso, un `LinearLayout`) viene inoltre evidenziato nel **nell'area di progettazione**. Il widget selezionato nella struttura documento resta sincronizzato con la controparte nel **nell'area di progettazione**. Ciò è utile per la selezione dei gruppi di visualizzazione, che non sono sempre facili da selezionare nella **nell'area di progettazione**.

Il **struttura documento** supporta la copia e Incolla oppure è possibile utilizzare il trascinamento. Trascinamento della selezione è supportato dal **struttura documento** per il **nell'area di progettazione** oltre che dal **nell'area di progettazione** per il **struttura documento**. Inoltre, facendo clic su un elemento nel **struttura documento** consente di visualizzare il menu di scelta rapida per l'elemento (stesso menu di scelta rapida visualizzato quando stesso widget facendo clic sul **nell'area di progettazione**).




# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="launching-the-designer"></a>Avviare la finestra di progettazione

La finestra di progettazione viene avviato automaticamente quando viene creato un layout, o possono essere avviata facendo doppio clic su un file axml esistente. Ad esempio, facendo doppio clic su **Main. axml** nel **risorse > Layout** cartella verrà caricata la finestra di progettazione, come illustrato di seguito:

[![Schermata della finestra di progettazione di Visual Studio per Mac](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

Allo stesso modo, è possibile aggiungere un nuovo layout facendo clic con il **layout** cartella le **riquadro della soluzione** e selezionando **Aggiungi > Nuovo File > Android > Layout**:

[![Aggiungi finestra di dialogo Nuovo File](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

Ciò crea un nuovo file axml e li carica nell'area di progettazione.

## <a name="designer-features"></a>Funzionalità della finestra di progettazione

La finestra di progettazione è costituita da diverse sezioni che supportano le varie funzionalità, come illustrato nello screenshot seguente:

[![Diagramma dei riquadri di progettazione](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

Quando si modifica un layout nella finestra di progettazione, per creare e definire la progettazione è usare le funzionalità seguenti:

-   **Area di progettazione** &ndash; facilita la costruzione visual dell'interfaccia utente fornendo una rappresentazione modificabile di modalità di visualizzazione del layout nel dispositivo.

-   **Sulla barra degli strumenti** &ndash; Visualizza un elenco di selettori: **dispositivo**, **versione**, **tema**, configurazione di layout e le impostazioni della barra delle azioni. Barra degli strumenti include anche le icone per avviare l'Editor del tema e per l'abilitazione della griglia di struttura materiali.

-   **Casella degli strumenti** &ndash; fornisce un elenco di widget e layout che è possibile trascinare e rilasciare nell'area di progettazione.

-   **Proprietà Pad** &ndash; sono elencate le proprietà del widget selezionato per la visualizzazione e modifica.

-   **Struttura documento** &ndash; Visualizza l'albero di widget che compongono il layout. È possibile fare clic su un elemento nell'albero per indurla a essere selezionato nella finestra di progettazione. Inoltre, facendo clic su un elemento nell'albero della carica le proprietà dell'elemento nel riquadro proprietà.

## <a name="toolbar"></a>ToolBar

La barra degli strumenti (posizionato sopra l'area di progettazione) presenta i selettori di configurazione e i menu degli strumenti:

[![Diagramma della barra degli strumenti della finestra di progettazione](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

Barra degli strumenti consente di accedere alle funzionalità seguenti:

-   **Selettore di Layout alternativi** &ndash; consente di selezionare rispetto alle versioni di un layout diverso.

-   **Selettore di dispositivo** &ndash; definisce un set di qualificatori associata a un dispositivo specifico, ad esempio dimensioni dello schermo, la risoluzione e la disponibilità della tastiera. È anche possibile aggiungere ed eliminare nuovi dispositivi.

-   **Selettore di versione di Android** &ndash; The Android versione che il layout è destinato a. La finestra di progettazione verrà eseguito il rendering del layout in base alla versione di Android selezionata.

-   **Selettore temi** &ndash; consente di selezionare il tema dell'interfaccia utente per il layout.

-   **Selettore configurazione** &ndash; seleziona la configurazione del dispositivo, ad esempio *verticale* oppure *panorama*.

-   **Opzioni per qualificatori di risorse** &ndash; apre una finestra di dialogo che presenta i menu a discesa per la selezione *Language*, *modalità UI*, *modalità notturna*, e *Schermo arrotondato* opzioni.

-   **Impostazioni azione barra** &ndash; Configura le impostazioni della barra delle azioni per il layout.

-   **Editor dei temi** &ndash; consente di aprire le *Theme Editor*, che rende possibile per personalizzare gli elementi del tema selezionato.

-   **Griglia di struttura materiali** &ndash; Abilita o disabilita le *griglia di struttura materiali*. La voce di menu di riepilogo a discesa accanto a griglia di struttura materiali apre una finestra di dialogo che consente di personalizzare la griglia.

Ognuna di queste funzionalità è illustrata in dettaglio negli argomenti seguenti:

[Qualificatori delle risorse e opzioni di visualizzazione](~/android/user-interface/android-designer/resource-qualifiers.md) fornisce informazioni dettagliate sulle **selettore di dispositivo**, **selettore di versione di Android**, **selettore del tema**, **Selettore configurazione**, **risorsa qualificazioni opzioni**, e **le impostazioni della barra delle azioni**.

[Viste di Layout alternativi](~/android/user-interface/android-designer/alternative-layout-views.md) spiega come usare il **alternativa Layout selettore**.

[Le funzionalità di Material Design](~/android/user-interface/android-designer/material-design-features.md) fornisce una panoramica completa del **Theme Editor** e il **griglia di struttura materiali**.

## <a name="design-surface"></a>Area di progettazione

La finestra di progettazione consente di trascinare e rilasciare i widget dalla casella degli strumenti nell'area di progettazione. Quando si interagisce con i widget nella finestra di progettazione (mediante l'aggiunta di nuovi widget o riposizionamento quelle esistenti), vengono visualizzate linee orizzontali e verticali per contrassegnare i punti di inserimento disponibili. Nell'esempio seguente, un nuovo `Button` widget trascinato nell'area di progettazione:

[![Esempio di linee di inserimento nell'area di progettazione](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

Inoltre, possono essere copiati i widget: è possibile usare copia e Incolla per copiare un widget o è possibile trascinare e rilasciare un widget esistente mentre si tiene premuto il <kbd>Ctrl</kbd> chiave.

### <a name="context-menu-commands"></a>Comandi di menu di scelta rapida

Un menu di scelta rapida è disponibile nell'area di progettazione sia nella struttura documento. Questo menu consente di visualizzare i comandi disponibili per il widget selezionato e il relativo contenitore, rendendo più semplice per eseguire operazioni su contenitori (che non sono sempre più semplice la selezione nell'area di progettazione). Di seguito è riportato un esempio di un menu di scelta rapida:

[![Esempio menu di scelta rapida quando il pulsante destro del mouse nell'area di progettazione](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

In questo esempio, facendo clic su un `Button` apre un menu di scelta rapida sono disponibili diverse opzioni:

-   **LinearLayout** &ndash; viene aperto un sottomenu per la modifica di `LinearLayout` padre del `Button`.

-   **Tagliare**, **copia**, e **eliminare** &ndash; operazioni eseguibili per il pulsante destro del mouse `Button`.

### <a name="zoom-controls"></a>Controlli zoom

L'area di progettazione supporta lo zoom tramite vari controlli, come illustrato:

[![Diagramma dei controlli zoom nell'area di progettazione](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

Questi controlli rendono più semplice visualizzare determinate aree dell'interfaccia utente nella finestra di progettazione:

-   **Evidenzia contenitori** &ndash; evidenzia contenitori nell'area di progettazione in modo che siano più semplici da individuare durante lo zoom avanti e indietro.

-   **Dimensioni normali** &ndash; esegue il rendering di layout pixel per pixel in modo che è possibile vedere un layout con la risoluzione del dispositivo selezionato.

-   **Adatta alla finestra** &ndash; imposta il livello di zoom in modo che l'intero layout è visibile nell'area di progettazione.

-   **Zoom avanti** &ndash; viene ingrandita in modo incrementale con ogni clic, per l'ingrandimento del layout.

-   **Zoom indietro** &ndash; esegue lo zoom indietro in modo incrementale con ogni clic, rendendo il layout vengono visualizzate più piccoli nell'area di progettazione.

Si noti che l'impostazione dello zoom scelto non interessa con l'interfaccia utente dell'applicazione in fase di esecuzione.

## <a name="property-pad"></a>Proprietà riempimento

La finestra di progettazione supporta la modifica delle proprietà di widget tramite il **Riempi proprietà**. Le proprietà elencate nella modifica riquadro della proprietà a seconda di quale widget è selezionato nella superficie di progettazione. Quando la `Button` nell'esempio precedente è selezionata, le proprietà per cui `Button` widget vengono visualizzati:

[![Screenshot del riquadro proprietà](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>Sezioni di riquadro della proprietà

Il riquadro proprietà è suddiviso in sezioni diverse che raggruppano proprietà simili &ndash; questo rende più semplice individuare le proprietà di interesse:

-   **Widget** &ndash; delle proprietà principali del widget, ad esempio `id`, `visibility`, `text`e così via. Le proprietà per la gestione dei contenuti del widget vengono in genere posizionate qui.

-   **Stile** &ndash; delle proprietà che modificano l'aspetto visivo del widget, ad esempio `font`, `text color`, `background`e così via.

-   **Layout** &ndash; proprietà impostare la posizione e dimensioni del widget.

-   **Scroll** &ndash; le proprietà di scorrimento.

-   **Il comportamento** &ndash; flag che impostati come si comporta il widget.

### <a name="default-values"></a>Valori predefiniti

Le proprietà della maggior parte dei widget saranno vuote nel **Riempi proprietà** perché i relativi valori ereditano dal tema selezionato Android. Il **Riempi proprietà** mostrerà solo i valori impostati in modo esplicito per il widget selezionato; non verranno invece visualizzati i valori che vengono ereditati dal tema.

### <a name="referencing-resources"></a>Riferimento alle risorse

Alcune proprietà possono fare riferimento a risorse che sono definite nel file, ad eccezione del layout **axml** file. Il maggior parte dei casi di questo tipo vengono `string` e `drawable` le risorse. Tuttavia, i riferimenti utilizzabile anche per le altre risorse, ad esempio `Boolean` valori e le dimensioni.
Quando una proprietà supporta i riferimenti a risorse, un'icona di ricerca (un puntini di sospensione, &hellip;) viene visualizzato accanto alla voce di testo per la proprietà.
Quando si fa clic, questo pulsante apre un selettore di risorse.

Ad esempio, lo screenshot seguente mostra le risorse disponibili quando si fa clic sui puntini di sospensione a destra del campo di testo per un `Button` widget nel **riquadro della proprietà**:

[![Screenshot delle risorse di esempio con due risorse elencati](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

L'esempio seguente illustra il selettore risorse per la `Src` proprietà di un `ImageView`:

[![Selettore risorse listato risorsa icona per un ImageView](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>Riferimenti alle proprietà booleane

*Booleano* proprietà vengono in genere visualizzate come una casella di controllo nel riquadro proprietà. Quando un `Boolean` proprietà supporta i riferimenti alle risorse, viene visualizzata una piccola casella di controllo accanto alla proprietà. Indica che una casella di controllo selezionata `true` e una casella vuota significa `false`. È anche possibile immettere un valore, ad esempio `true` o `false`. Passando il puntatore del mouse sull'input, viene visualizzata un'icona di campo di testo di piccole dimensioni. È possibile fare clic su di esso se si desidera immettere manualmente il valore.

[![Esempio di impostazione di proprietà booleane](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)


## <a name="grouped-properties"></a>Gruppo di proprietà

Alcuni widget dispongono di proprietà con più valori raggruppati (ad esempio `Padding`, ad esempio). I valori delle proprietà sono elencati nel **Pad proprietà** in una riga singola, espandibile. Alcune di queste proprietà possono essere modificate direttamente nella riga raggruppata, ad esempio il `Padding` proprietà illustrato di seguito:

[![Impostazioni di esempio per la proprietà Padding](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>Modifica le proprietà inline

La finestra di progettazione Android supporta la modifica diretta di determinate proprietà nella finestra di progettazione (in modo da non dover cercare queste proprietà nell'elenco delle proprietà). Le proprietà che possono essere modificate direttamente includono testo, margine e dimensioni.

### <a name="text"></a>Testo

Le proprietà di testo di alcuni widget (ad esempio `Button` e `TextView`), possono essere modificate direttamente nell'area di progettazione. Fare doppio clic su un widget verranno inserirlo nella modalità di modifica, come illustrato di seguito:

[![Risorsa di testo per la stringa hello](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

È possibile immettere un nuovo valore di testo oppure è possibile immettere una nuova stringa di risorsa. Nell'esempio seguente, il `@string/hello` risorsa è stata sostituita con il testo, `CLICK THIS BUTTON`:

[![MAIUSC + INVIO per collegare automaticamente il testo in una nuova risorsa](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

Questa modifica viene archiviata all'interno del widget `text` proprietà; non modifica il valore assegnato per il `@string/hello` risorsa.
Quando è una chiave in una nuova stringa di testo, è possibile premere <kbd>Shift</kbd> +
<kbd>invio</kbd> per collegare automaticamente il testo immesso in una nuova risorsa.

### <a name="margin"></a>Margini

Quando si seleziona un widget, la finestra di progettazione vengono visualizzati handle che consentono di modificare le dimensioni o un margine del widget in modo interattivo. Facendo clic sul widget mentre è selezionato Alterna tra modalità di modifica dei margini e la modalità di modifica delle dimensioni.

Quando si fa clic su un widget per la prima volta, vengono visualizzati gli handle dei margini. Se si sposta il puntatore del mouse su uno dei quadratini di ridimensionamento, la finestra di progettazione consente di visualizzare le proprietà che verrà modificata l'handle (come illustrato di seguito per il `layout_marginLeft` proprietà):

[![Screenshot che Mostra margine gestisce nella finestra di progettazione](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

Se un margine è già stato impostato, vengono visualizzate linee tratteggiate, che indica lo spazio che occupa il margine:

[![Esempio di contrassegno di spazio intorno a un pulsante che linee tratteggiate](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>Dimensione

Come accennato in precedenza, è possibile passare alla modalità di modifica delle dimensioni, fare clic su un widget mentre è già selezionato. Fare clic sull'handle triangolare per impostare le dimensioni per la dimensione indicata per `wrap_content`:

[![Handle di contenuto a capo automatico e ridimensionamento](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

Facendo clic sui **eseguire il wrapping di contenuto** handle si riduce il widget in tale dimensione non in modo che sia superiore rispetto al necessario per eseguire il wrapping del contenuto. In questo esempio, il testo del pulsante si riduce in senso orizzontale, come illustrato nel prossimo screenshot.

Quando il valore delle dimensioni è impostato su **eseguire il wrapping di contenuto**, la finestra di progettazione vengono visualizzati un handle triangolare che punta nella direzione opposta per la modifica delle dimensioni a `match_parent`:

[![Handle padre di corrispondenza](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

Facendo clic sui **corrispondenza padre** handle consente di ripristinare le dimensioni in tale dimensione, in modo che sia quello utilizzato per il widget padre.

Inoltre, è possibile trascinare il quadratino di ridimensionamento circolare (come illustrato negli screenshot precedente) per ridimensionare un widget a un oggetto arbitrario `dp` valore. Quando si esegue questa operazione, entrambe **eseguire il wrapping di contenuto** e **corrispondenza padre** vengono presentati gli handle per la dimensione:

[![Quadratini di ridimensionamento circolare](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

Non tutti i contenitori consentono la modifica di `Size` di un widget. Ad esempio, si noti che nella schermata seguente con il `LinearLayout` selezionata, i quadratini di ridimensionamento non vengono visualizzati:

[![Senza quadratini di ridimensionamento](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>Struttura documento

Il **struttura documento** Visualizza la gerarchia di widget del layout.
Nell'esempio seguente, il che contiene `LinearLayout` widget è selezionato:

[![Struttura documento](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

La struttura del widget selezionato (in questo caso, un `LinearLayout`) viene inoltre evidenziato nell'area di progettazione. Il widget selezionato nella struttura documento resta sincronizzato con la controparte nell'area di progettazione. Ciò è utile per la selezione di visualizzare i gruppi, che non sono sempre più semplici la selezione nell'area di progettazione.

La struttura del documento supporta la copia e Incolla, oppure è possibile utilizzare il trascinamento. Trascinamento della selezione è supportata dalla struttura del documento nell'area di progettazione, oltre che dall'area di progettazione per la struttura del documento. Inoltre, facendo clic su un elemento nella struttura documento consente di visualizzare il menu di scelta rapida per l'elemento (stesso menu di scelta rapida visualizzato facendo clic su tale widget stesso nell'area di progettazione).

-----
