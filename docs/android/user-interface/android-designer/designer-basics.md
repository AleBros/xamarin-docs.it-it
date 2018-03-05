---
title: Nozioni di base della finestra di progettazione
description: "In questo argomento vengono presentate le funzionalità di progettazione, viene illustrato come avviare la finestra di progettazione, descrive la superficie di progettazione e viene illustrato come utilizzare il riquadro proprietà per modificare le proprietà di widget."
ms.topic: article
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: b2ed48ae9df7e950525fdc0cb97181ebe5a44dfb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="designer-basics"></a>Nozioni di base della finestra di progettazione

_In questo argomento vengono presentate le funzionalità di progettazione, viene illustrato come avviare la finestra di progettazione, descrive la superficie di progettazione e viene illustrato come utilizzare il riquadro proprietà per modificare le proprietà di widget._

<a name="Launching_the_Designer" />

## <a name="launching-the-designer"></a>Avviare la finestra di progettazione

La finestra di progettazione viene avviata automaticamente quando viene creato un layout, o può essere avviata facendo doppio clic su un file .axml esistente. Ad esempio, fare doppio clic su **axml** nel **risorse > Layout** cartella verrà caricata la finestra di progettazione, come illustrato di seguito:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Schermata della finestra di progettazione in Visual Studio](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Schermata della finestra di progettazione in Visual Studio per Mac](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Allo stesso modo, è possibile aggiungere un nuovo layout facendo clic con il **layout** cartella il **Esplora** e selezionando **Aggiungi > Nuovo elemento … > Layout Android**:

[![Aggiungere una finestra di dialogo Nuovo elemento](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Allo stesso modo, è possibile aggiungere un nuovo layout facendo clic con il **layout** cartella la **soluzione riempimento** e selezionando **Aggiungi > Nuovo File > Android > Layout**:

[![Aggiungere una finestra di dialogo Nuovo File](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png)

-----

Questo crea un nuovo file .axml e lo carica nell'area di progettazione.


<a name="Designer_Features" />

## <a name="designer-features"></a>Caratteristiche di progettazione

La finestra di progettazione è costituita da diverse sezioni che supportano le varie funzionalità, come illustrato nella schermata seguente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Diagramma dei riquadri di progettazione](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Diagramma dei riquadri di progettazione](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png)

-----

Quando si modifica un layout nella finestra di progettazione, utilizzare le funzionalità seguenti per creare e definire la struttura:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   **Area di progettazione** &ndash; facilita la costruzione dell'interfaccia utente visual fornendo una rappresentazione modificabile di come il layout verrà visualizzato nel dispositivo.

-   **Barra degli strumenti** &ndash; Visualizza un elenco di selettori: **dispositivo**, **versione**, **tema**, configurazione di layout e le impostazioni della barra di azione. La barra degli strumenti include anche le icone per avviare l'Editor del tema e per l'abilitazione della griglia di progettazione del materiale.

-   **Casella degli strumenti** &ndash; fornisce un elenco di widget e layout che è possibile trascinare nell'area di progettazione.

-   **Finestra proprietà** &ndash; sono elencate le proprietà del widget selezionato per la visualizzazione e modifica.

-   **Struttura documento** &ndash; Visualizza l'albero di widget che compongono il layout. È possibile fare clic su un elemento nell'albero per indurla a essere selezionato nella finestra di progettazione. Inoltre, facendo clic su un elemento nell'albero della carica le proprietà dell'elemento nella finestra Proprietà.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

-   **Area di progettazione** &ndash; facilita la costruzione dell'interfaccia utente visual fornendo una rappresentazione modificabile di come il layout verrà visualizzato nel dispositivo.

-   **Barra degli strumenti** &ndash; Visualizza un elenco di selettori: **dispositivo**, **versione**, **tema**, configurazione di layout e le impostazioni della barra di azione. La barra degli strumenti include anche le icone per avviare l'Editor del tema e per l'abilitazione della griglia di progettazione del materiale.

-   **Casella degli strumenti** &ndash; fornisce un elenco di widget e layout che è possibile trascinare nell'area di progettazione.

-   **Proprietà riempimento** &ndash; sono elencate le proprietà del widget selezionato per la visualizzazione e modifica.

-   **Struttura documento** &ndash; Visualizza l'albero di widget che compongono il layout. È possibile fare clic su un elemento nell'albero per indurla a essere selezionato nella finestra di progettazione. Inoltre, facendo clic su un elemento nell'albero della carica le proprietà dell'elemento nel riquadro proprietà.

-----


<a name="Toolbar" />

## <a name="toolbar"></a>ToolBar

La barra degli strumenti (posizionato sopra l'area di progettazione) presenta i selettori di configurazione e i menu di strumento:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Diagramma della barra degli strumenti della finestra di progettazione](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Diagramma della barra degli strumenti della finestra di progettazione](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png)

-----


La barra degli strumenti fornisce l'accesso alle funzionalità seguenti:

-   **Selettore di Layout alternativi** &ndash; consente di selezionare versioni diverse di layout.

-   **Selettore dispositivo** &ndash; definisce un set di qualificatori associata a un dispositivo specifico, ad esempio la disponibilità di tastiera, risoluzione e dimensioni dello schermo. È anche possibile aggiungere ed eliminare nuovi dispositivi.

-   **Selettore di versione di Android** &ndash; versione Android di cui è destinato il layout. La finestra di progettazione verrà eseguito il rendering del layout in base alla versione di Android selezionato.

-   **Selettore del tema** &ndash; consente di selezionare il tema dell'interfaccia utente per il layout.

-   **Configurazione selettore** &ndash; seleziona la configurazione del dispositivo, ad esempio *verticale* o *orizzontale*.

-   **Opzioni qualificatore risorse** &ndash; apre una finestra di dialogo che visualizza il menu di riepilogo a discesa per la selezione di *Language*, *modalità interfaccia utente*, *notturna*, e *Schermata round* opzioni.

-   **Le impostazioni della barra azione** &ndash; Configura le impostazioni della barra delle azioni per il layout.

-   **Editor del tema** &ndash; apre il *Editor del tema*, che rende possibile la personalizzazione di elementi del tema selezionato.

-   **Griglia di progettazione materiale** &ndash; Abilita o disabilita il *griglia di progettazione di materiale*. La voce di menu di riepilogo a discesa adiacente alla griglia di progettazione di materiale viene visualizzata una finestra di dialogo che consente di personalizzare la griglia.

Ognuna di queste funzionalità verrà illustrato in dettaglio negli argomenti seguenti:

[Qualificatori delle risorse e le opzioni di visualizzazione](~/android/user-interface/android-designer/resource-qualifiers.md) fornisce informazioni dettagliate sul **selettore dispositivo**, **Android versione selettore**, **selettore del tema**, **Configurazione selettore**, **risorse qualifiche opzioni**, e **le impostazioni della barra di azione**.

[Viste di Layout alternativi](~/android/user-interface/android-designer/alternative-layout-views.md) viene illustrato come utilizzare il **alternativa Layout selettore**.

[Le caratteristiche di progettazione](~/android/user-interface/android-designer/material-design-features.md) fornisce una panoramica completa del **Editor del tema** e **griglia di progettazione di materiale**.


<a name="Design_Surface" />

## <a name="design-surface"></a>Area di progettazione

La finestra di progettazione consente di trascinare widget dalla casella degli strumenti nell'area di progettazione. Quando si interagisce con i widget nella finestra di progettazione (mediante l'aggiunta di nuovi widget o riposizionamento esistenti), vengono visualizzate linee verticali e orizzontali per contrassegnare i punti di inserimento disponibili. Nell'esempio seguente, un nuovo `Button` widget trascinato nell'area di progettazione:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Righe di inserimento di esempio nell'area di progettazione](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Righe di inserimento di esempio nell'area di progettazione](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png)

-----

Inoltre, possono essere copiati widget: è possibile usare copia e Incolla per copiare un widget o è possibile trascinare e rilasciare un widget esistente mentre si tiene premuto il <kbd>Ctrl</kbd> chiave.

<a name="Context_Menu_Commands" />

### <a name="context-menu-commands"></a>Comandi del Menu contestuale

Un menu di scelta rapida disponibile nell'area di progettazione e nella struttura del documento. Questo menu Visualizza i comandi disponibili per il widget selezionato e il relativo contenitore, rendendo più semplice per eseguire operazioni sui contenitori (che non sono sempre facile selezionare nella finestra di progettazione). Di seguito è riportato un esempio di un menu di scelta rapida:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menu di scelta rapida esempio quando facendo clic nell'area di progettazione](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png)

In questo esempio, facendo clic su un `TextView` apre un menu di scelta rapida che sono disponibili diverse opzioni:

-   **LinearLayout** &ndash; viene aperto un sottomenu per la modifica di `LinearLayout` padre il `TextView`.

-   **Eliminare**, **copia**, e **Taglia** &ndash; operazioni che riguardano il pulsante destro del mouse `TextView`.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Menu di scelta rapida esempio quando facendo clic nell'area di progettazione](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png)

In questo esempio, facendo clic su un `TextView` apre un menu di scelta rapida che sono disponibili diverse opzioni:

-   **RelativeLayout** &ndash; viene aperto un sottomenu per la modifica di `RelativeLayout` padre il `TextView`.

-   **LinearLayout** &ndash; viene aperto un sottomenu per la modifica di `LinearLayout` padre il `TextView`.

-   **Eliminare**, **copia**, e **Taglia** &ndash; operazioni che riguardano il pulsante destro del mouse `TextView`.

-----

In questo esempio, facendo clic su un `TextView` apre un menu di scelta rapida che sono disponibili diverse opzioni:

-   **LinearLayout** &ndash; viene aperto un sottomenu per la modifica di `LinearLayout` padre il `TextView`.

-   **Eliminare**, **copia**, e **Taglia** &ndash; operazioni che riguardano il pulsante destro del mouse `TextView`.


<a name="Zoom_Controls" />

### <a name="zoom-controls"></a>Controlli zoom

Finestra di progettazione supporta lo zoom tramite diversi controlli, come illustrato:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Diagramma dei controlli zoom area di progettazione](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Diagramma dei controlli zoom area di progettazione](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png)

-----

Questi controlli rendono più leggibile in determinate aree dell'interfaccia utente nella finestra di progettazione:

-   **Evidenziare contenitori** &ndash; evidenzia contenitori nell'area di progettazione in modo che siano più facile individuare durante lo zoom avanti e indietro.

-   **Dimensioni normali** &ndash; esegue il rendering del layout pixel per pixel in modo che è possibile visualizzare l'aspetto di layout con la risoluzione del dispositivo selezionato.

-   **Adatta alla finestra** &ndash; imposta il livello di zoom in modo che l'intero layout è visibile nell'area di progettazione.

-   **Zoom avanti** &ndash; viene ingrandito in modo incrementale con ogni clic, il layout di ingrandimento.

-   **Zoom indietro** &ndash; riduce in modo incrementale con ogni clic, visualizzando il layout più piccoli nell'area di progettazione.

Si noti che l'impostazione dello zoom scelto non influisce l'interfaccia utente dell'applicazione in fase di esecuzione.

<a name="property_pad" />

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

## <a name="property-pad"></a>Proprietà riempimento

La finestra di progettazione supporta la modifica delle proprietà di widget tramite il **proprietà riempimento**. Le proprietà elencate nella modifica di proprietà riempimento a seconda di quale widget è selezionato nell'area di progettazione. Quando il `Button` nell'esempio precedente è selezionata, le proprietà per tale `Button` widget vengono visualizzati:

[![Schermata del riquadro proprietà](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png)

-----

<a name="Property_Pad_Sections" />

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="properties-window"></a>Finestra Proprietà

La finestra di progettazione supporta la modifica delle proprietà di widget tramite il **finestra proprietà**. Le proprietà elencate nella modifica della finestra proprietà a seconda di quale widget è selezionato nella finestra di progettazione.
Quando il `Button` nell'esempio precedente è selezionata, le proprietà per tale `Button` widget vengono visualizzati:

![Schermata della finestra proprietà](designer-basics-images/vs/08-property-pad.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

## <a name="property-pad-sections"></a>Sezioni di proprietà riempimento

Il riquadro proprietà è suddiviso in sezioni diverse che raggruppano proprietà simili &ndash; questo rende più semplice individuare le proprietà di interesse:

-   **Widget** &ndash; proprietà principali del widget, ad esempio `id`, `visibility`, `text`e così via. Le proprietà per la gestione del contenuto del widget vengono in genere posizionate qui.

-   **Stile** &ndash; proprietà che modificano l'aspetto visivo del widget, ad esempio `font`, `text color`, `background`e così via.

-   **Layout** &ndash; proprietà impostare la posizione e dimensioni del widget.

-   **Scorrimento** &ndash; le proprietà di scorrimento.

-   **Comportamento** &ndash; flag impostati come si comporta il widget.

-----


<a name="Default_Values" />

### <a name="default-values"></a>Valori predefiniti

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Le proprietà della maggior parte dei widget sarà vuote nel **proprietà** finestra perché i relativi valori ereditano il tema selezionato Android.
Il **proprietà** finestra visualizzerà soltanto i valori impostati in modo esplicito per il widget selezionato; non visualizza i valori che vengono ereditati dal tema.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Le proprietà della maggior parte dei widget sarà vuote nel **proprietà riempimento** perché i relativi valori ereditano il tema selezionato Android. Il **proprietà riempimento** visualizzerà soltanto i valori impostati in modo esplicito per il widget selezionato; non visualizza i valori che vengono ereditati dal tema.

-----

<a name="Referencing_resources" />

### <a name="referencing-resources"></a>Riferimenti alle risorse

Alcune proprietà possono fare riferimento a risorse che sono definite nel file diversi dal layout **.axml** file. Il maggior parte dei casi di questo tipo sono `string` e `drawable` risorse. Tuttavia, i riferimenti utilizzabile anche per le altre risorse, ad esempio `Boolean` valori e le dimensioni.
Quando una proprietà supporta i riferimenti alle risorse, un'icona di ricerca (un puntini di sospensione, &hellip;) viene visualizzato accanto alla voce di testo per la proprietà.
Apre un selettore di risorse quando si fa clic.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Ad esempio, nella schermata seguente mostra le risorse disponibili quando si fa clic sui puntini di sospensione a destra del campo di testo per un `Button` widget di **proprietà** finestra:

[![Schermata di risorse di esempio con due risorse elencate](designer-basics-images/vs/09-resources-sml.png)](designer-basics-images/vs/09-resources.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Ad esempio, nella schermata seguente mostra le risorse disponibili quando si fa clic sui puntini di sospensione a destra del campo di testo per un `Button` widget di **proprietà riempimento**:

[![Schermata di risorse di esempio con due risorse elencate](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png)

-----

Nell'esempio successivo viene illustrato il selettore risorse per il `Src` proprietà di un `ImageView`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Selettore risorse listato risorsa icona per un ImageView](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Selettore risorse listato risorsa icona per un ImageView](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png)

-----


<a name="Boolean_Property_References" />

### <a name="boolean-property-references"></a>Proprietà booleana riferimenti

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

*Booleano* proprietà sono in genere scelte da un menu a discesa nella finestra Proprietà. È possibile selezionare un `true` o `false` oppure è possibile selezionare un riferimento a proprietà, fare clic su **Seleziona...** . È possibile immettere direttamente un valore, ad esempio `true` o `false`.

![Esempio di impostazione di proprietà booleane](designer-basics-images/vs/11-boolean.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

*Booleano* proprietà sono in genere visualizzate come una casella di controllo nel riquadro proprietà. Quando un `Boolean` proprietà supporta i riferimenti alle risorse, viene visualizzata una piccola casella di controllo accanto alla proprietà. Indica che una casella di controllo selezionata `true` e una casella vuota significa `false`. È possibile immettere direttamente un valore, ad esempio `true` o `false`. Passaggio del mouse sull'input viene visualizzata un'icona di campo di testo di piccole dimensioni. È possibile fare clic su di esso se si desidera immettere manualmente il valore.

[![Esempio di impostazione di proprietà booleane](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png)

<a name="Grouped_Properties" />

## <a name="grouped-properties"></a>Proprietà raggruppate

Alcuni widget dispone di proprietà con più valori raggruppati (ad esempio `Padding`, ad esempio). Valori di queste proprietà sono elencati nel **proprietà riempimento** in una riga singola, espandibile. Alcune di queste proprietà possono essere modificate direttamente nella riga raggruppata, ad esempio il `Padding` proprietà illustrato di seguito:

[![Impostazioni di esempio per la proprietà di riempimento](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png)

-----

<a name="Editing_Properties_Inline" />

## <a name="editing-properties-inline"></a>Modifica le proprietà Inline

La finestra di progettazione Android supporta la modifica diretta di determinate proprietà nella finestra di progettazione (non è necessario eseguire la ricerca di queste proprietà nell'elenco di proprietà). Proprietà che può essere modificata direttamente includono testo, margini e alla dimensione.

<a name="Text" />

### <a name="text"></a>Testo

Le proprietà del testo di alcuni widget (ad esempio `Button` e `TextView`), possono essere modificati direttamente nell'area di progettazione. Fare doppio clic su un widget verrà inserirlo nella modalità di modifica, come illustrato di seguito:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Risorse di testo per la stringa hello](designer-basics-images/vs/12-text-resource.png "risorse di testo")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Risorse di testo per la stringa hello](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png)

-----

È possibile immettere un nuovo valore di testo o è possibile immettere una nuova stringa di risorsa. Nell'esempio seguente, il `@string/hello` risorsa verrà sostituita con il testo, `CLICK THIS BUTTON`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![MAIUSC + INVIO per il testo del collegamento automaticamente a una nuova risorsa](designer-basics-images/vs/13-shift-enter-resource.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![MAIUSC + INVIO per il testo del collegamento automaticamente a una nuova risorsa](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png)

-----

Questa modifica viene archiviata all'interno del widget `text` proprietà; non modifica il valore assegnato al `@string/hello` risorse.
Quando la chiave in una nuova stringa di testo, è possibile premere <kbd>MAIUSC</kbd> +
<kbd>invio</kbd> per collegare automaticamente il testo immesso per una nuova risorsa.

<a name="Margin" />

### <a name="margin"></a>Margini

Quando si seleziona un widget, la finestra di progettazione vengono visualizzati handle che consentono di modificare le dimensioni o il margine del widget in modo interattivo. Facendo clic sul widget mentre è selezionato Alterna tra la modalità di modifica dei margini e la modalità di modifica delle dimensioni.

Quando si fa clic su un widget per la prima volta, vengono visualizzati gli handle di margine. Se si sposta il puntatore del mouse su uno dei quadratini di ridimensionamento, la finestra di progettazione viene visualizzata la proprietà che verrà modificata l'handle (come illustrato di seguito per la `layout_marginLeft` proprietà):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Schermata che illustra il margine gestisce nella finestra di progettazione](designer-basics-images/vs/15-margin-handles.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Schermata che illustra il margine gestisce nella finestra di progettazione](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png)

-----

Se un margine è già stato impostato, vengono visualizzate linee punteggiate, che indica lo spazio che occupa il margine:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Esempio di linee tratteggiate, contrassegnare spazio attorno a un pulsante](designer-basics-images/vs/16-margins-set.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Esempio di linee tratteggiate, contrassegnare spazio attorno a un pulsante](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png)

-----


<a name="Size" />

### <a name="size"></a>Dimensione

Come accennato in precedenza, è possibile passare alla modalità di modifica di dimensioni, fare clic su un widget quando è già selezionato. Fare clic sull'handle triangolare per impostare le dimensioni per la dimensione specificata per `wrap_content`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Handle di ridimensionamento e il contenuto a capo automatico](designer-basics-images/vs/17-wrap-content.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Handle di ridimensionamento e il contenuto a capo automatico](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png)

-----

Fare clic su di **eseguire il wrapping di contenuto** handle compatta il widget in tale dimensione non in modo che sia maggiore rispetto al necessario per eseguire il wrapping del contenuto tra parentesi. In questo esempio, il testo del pulsante compatta orizzontalmente, come illustrato nella schermata successiva.

Quando il valore di dimensione è impostato su **eseguire il wrapping del contenuto**, la finestra di progettazione consente di visualizzare un handle triangolare che puntano in direzione opposta per la modifica della dimensione per `match_parent`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Handle di corrispondenza padre](designer-basics-images/vs/18-match-parent.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Handle di corrispondenza padre](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png)

-----

Fare clic su di **padre corrispondenza** handle Ripristina le dimensioni in tale dimensione, in modo che sia lo stesso come il widget padre.

Inoltre, è possibile trascinare il quadratino di ridimensionamento circolare (come illustrato nelle schermate precedenti) per ridimensionare il widget a un oggetto arbitrario `dp` valore. In tal caso, entrambi **eseguire il wrapping del contenuto** e **padre corrispondenza** vengono presentati gli handle per la dimensione:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Quadratini di ridimensionamento circolare](designer-basics-images/vs/19-resize-dp.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Quadratini di ridimensionamento circolare](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png)

-----

Non tutti i contenitori consentono la modifica di `Size` di un widget. Ad esempio, si noti che nella schermata seguente con il `LinearLayout` selezionata, i quadratini di ridimensionamento non vengono visualizzati:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Nessun quadratini di ridimensionamento](designer-basics-images/vs/20-no-resize-handles.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Nessun quadratini di ridimensionamento](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png)

-----


<a name="Outline_View" />

## <a name="document-outline"></a>Struttura documento

Il **struttura documento** Visualizza la gerarchia di widget del layout.
Nell'esempio seguente, contenente `LinearLayout` widget è selezionato:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Struttura documento](designer-basics-images/vs/21-document-outline.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Struttura documento](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png)

-----

La struttura del widget selezionato (in questo caso, un `LinearLayout`) viene inoltre evidenziato nella finestra di progettazione. Il widget selezionato nella struttura del documento rimane sincronizzato con la controparte nell'area di progettazione. Ciò è utile per la selezione di gruppi di visualizzazione, che non sono sempre facili selezionare nella finestra di progettazione.

La struttura del documento supporta la copia e Incolla oppure è possibile utilizzare il trascinamento. Trascinamento della selezione è supportata la struttura del documento nell'area di progettazione, nonché dall'area di progettazione per la struttura del documento. Inoltre, facendo clic su un elemento nella struttura del documento Visualizza menu di scelta rapida per l'elemento (lo stesso menu di scelta rapida che viene visualizzato quando si fa clic su tale stesso widget nell'area di progettazione).
