---
title: "Funzionalità di progettazione materiale"
description: "Questo argomento descrive le funzionalità di progettazione che rendono più semplice per gli sviluppatori creare layout di progettazione conformi materiale. In questa sezione vengono presentati e come utilizzare la griglia di materiale, la tavolozza dei colori di materiale, la scala tipografica e l'Editor del tema."
ms.topic: article
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 9c1797398fba580ab7f34526b10e1da455eb2dc5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="material-design-features"></a>Funzionalità di progettazione materiale

_Questo argomento descrive le funzionalità di progettazione che rendono più semplice per gli sviluppatori creare layout di progettazione conformi materiale. In questa sezione vengono presentati e come utilizzare la griglia di materiale, la tavolozza dei colori di materiale, la scala tipografica e l'Editor del tema._


> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evoluzione 2016: Tutti gli utenti possono creare efficaci App con progettazione materiale**

## <a name="overview"></a>Panoramica

La finestra di progettazione xamarin include funzionalità che rendono più semplice per creare layout di materiale conformi progettazione. Se non si ha familiarità con progettazione materiale, vedere il [materiale progettazione Introduzione](https://www.google.com/design/spec/material-design/introduction.html).

In questa Guida, è necessario esaminare le funzionalità di progettazione seguenti:

-   *Griglia materiale* &ndash; sovrapposizione nell'area di progettazione che mostra una griglia, spaziatura e attivare consentono di inserire i widget di layout in base alle linee guida di progettazione di materiale.

-   *Tavolozza dei colori materiale* &ndash; finestra di dialogo riempimento di una proprietà che consente di scegliere un colore dalla tavolozza di progettazione di materiale ufficiale.

-   *Scala tipografica* &ndash; finestra di dialogo riempimento di una proprietà che fornisce una scelta di materiale conformi con Progettazione impostazioni per il `textAppearance` proprietà dei campi di testo.

-   *Editor del tema* &ndash; un editor di risorse del colore che consente di imposta le informazioni di colore per un subset di un tema. Ad esempio, è possibile visualizzare in anteprima e modificare i colori materiali come `colorPrimary`, `colorPrimaryDark`, e `colorAccent`.

È verranno esaminati ognuna di queste funzionalità e vengono forniti esempi di come utilizzarli.



## <a name="material-design-grid"></a>Griglia di progettazione materiale

Il menu di griglia di progettazione di materiale è disponibile nella barra degli strumenti nella parte superiore della finestra di progettazione:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Materiale della griglia di progettazione](material-design-features-images/vs/01-material-design-grid-sml.png)](material-design-features-images/vs/01-material-design-grid.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Materiale della griglia di progettazione](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

-----

Quando si sceglie l'icona del materiale della griglia di progettazione, la finestra di progettazione consente di visualizzare una sovrapposizione nell'area di progettazione che include gli elementi seguenti:

-   Attivare (linee di colore arancione)

-   Spaziatura (aree verde)

-   Una griglia (linee blu)

Questi elementi possono essere visualizzati nella schermata seguente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Cornice e Spaziatura griglia](material-design-features-images/vs/02-grid-and-keylines-sml.png)](material-design-features-images/vs/02-grid-and-keylines.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Cornice e Spaziatura griglia](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Ognuno di questi elementi di sovrapposizione è configurabile. Quando si fa clic sui puntini di sospensione accanto al menu di materiale griglia di progettazione, viene visualizzata popover una finestra di dialogo che consente di disabilitare o abilitare la griglia, configurare la posizione di attivare e impostare spaziature l'avanzamento. Si noti che tutti i valori sono espressi in `dp` (densità independent pixel):

![Griglia, cornice e la configurazione di spaziatura](material-design-features-images/vs/03-grid-configuration.png)

Per aggiungere una nuova cornice, immettere un nuovo valore di offset nella **Offset** , selezionare un percorso (**sinistro**, **top**, **destra**, o  **nella parte inferiore**) e fare clic sull'icona + per aggiungere la nuova cornice.

Analogamente, per aggiungere un nuovo spazio, immettere le dimensioni e offset (dp) nei **dimensioni** e **Offset** caselle, rispettivamente. Selezionare un percorso (**sinistro**, **superiore**, **destra**, o **inferiore**) e fare clic sull'icona per aggiungere la nuova spaziatura +.

Quando si modificano i valori di configurazione, vengono salvate nel file XML di layout e riutilizzati quando si apre di nuovo il layout.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Ognuno di questi elementi di sovrapposizione è configurabile. Quando si fa clic sul triangolo verso il basso accanto al menu di griglia di progettazione di materiale, viene visualizzata popover una finestra di dialogo che consente di disabilitare o abilitare la griglia, configurare la posizione di attivare e impostare spaziature l'avanzamento. Si noti che tutti i valori sono espressi in `dp` (densità independent pixel):

[![Griglia, cornice e la configurazione di spaziatura](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

Per aggiungere una nuova cornice, immettere un nuovo valore di offset nella **Offset** , selezionare un percorso (**sinistro**, **top**, **destra**, o  **nella parte inferiore**) e fare clic sull'icona + per aggiungere la nuova cornice.

Analogamente, per aggiungere un nuovo spazio, immettere le dimensioni e offset (dp) nei **dimensioni** e **Offset** caselle, rispettivamente. Selezionare un percorso (**sinistro**, **superiore**, **destra**, o **inferiore**) e fare clic sull'icona per aggiungere la nuova spaziatura +.

Quando si modificano i valori di configurazione, vengono salvate nel file XML di layout e riutilizzati quando si apre di nuovo il layout.


## <a name="material-design-color-palette"></a>Tavolozza dei colori di materiale di progettazione

Ogni elemento del Pannello proprietà che accetta un colore ora presenta un'icona aggiuntiva che è possibile utilizzare per aprire la tavolozza dei colori di progettazione di materiale, come illustrato in questa schermata:

[![Icona di colore](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

Quando si fa clic su questa icona, viene visualizzata popover una finestra di dialogo che consente di configurare il colore di tale proprietà dalla tavolozza dei colori materiale progettazione:

[![Materiale tavolozza dei colori di progettazione](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

La parte superiore della tavolozza dei colori Visualizza colori materiale progettazione primari mentre nella parte inferiore del riquadro viene visualizzato un intervallo di tonalità del colore primario selezionato. Ad esempio, quando si seleziona **Indigo**, una raccolta di **Indigo** tonalità viene visualizzato nella parte inferiore della finestra di dialogo.
Quando si seleziona una tonalità, il colore della proprietà viene modificato in tonalità selezionata. Nell'esempio seguente, il `Background Tint` del pulsante viene modificato in *500 Indigo*:

[![Scegliere Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` è impostato per il codice per il colore *500 Indigo* (`#ff3f51b5`), e la finestra di progettazione aggiorna il colore di sfondo del pulsante per riflettere la modifica:

[![Modifiche di tonalità di sfondo](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

Per ulteriori informazioni sulla tavolozza dei colori materiale progettazione, vedere la progettazione di materiale [Guida tavolozza di colori](http://www.google.com/design/spec/style/color.html#color-color-palette).

## <a name="typographic-scale"></a>Scala tipografica

Il **aspetto del testo** sezione del **proprietà** riempimento **stile** scheda contiene un'icona che consente di selezionare da un `TextAppearance` stile conforme alla progettazione materiale specifica:

[![Scheda stili](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

Quando si fa clic su questa icona, viene aperto il **scala tipografica** popover finestra di dialogo, che presenta un elenco degli stili di testo configurati in precedenza che è possibile scegliere tra:

[![Selezione di stile di testo](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

Nell'esempio seguente, fare clic su **schermo 1** viene modificato il testo del pulsante per il tipo di carattere maggiore di **schermo 1**:

[![Stile di visualizzazione 1](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

Lo stile del testo nel **scala tipografica** finestra di dialogo segue il **tema** impostazione. Ad esempio, se il **luce** tema viene scelto nella finestra di progettazione, l'elenco di testo disponibile stili mirror il **luce** tema:

[![Tema chiaro](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

-----



## <a name="theme-editor"></a>Editor del tema

Il **Editor del tema** consente di personalizzare le informazioni di colore per un subset di attributi di tema. Per aprire la **Editor del tema**, fare clic sull'icona pennello sulla barra degli strumenti:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Icona Editor del tema](material-design-features-images/vs/04-theme-editor-icon.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Icona Editor del tema](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

-----

Sebbene il **Editor del tema** è accessibile dalla barra degli strumenti per tutti i livelli di API e le versioni Android destinazione solo un subset delle funzionalità descritte di seguito sono disponibili se il livello API di destinazione è anteriore alle API 21 (Android 5.0 Simbolo).

Il pannello sinistro del **Editor del tema** consente di visualizzare l'elenco di colori che costituiscono il tema attualmente selezionato (in questo esempio viene usato il `Default Theme`):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Editor del tema](material-design-features-images/vs/05-theme-editor-sml.png)](material-design-features-images/vs/05-theme-editor.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Editor del tema](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

-----

Quando si seleziona un colore a sinistra, nel riquadro destro sono disponibili le schede seguenti che consentono di modificare il colore:

-   **Ereditare** &ndash; Visualizza un diagramma di ereditarietà di stile per il colore selezionato ed elenca il colore risolto e il codice colore assegnato per il colore del tema.

-   **Colore selezione** &ndash; consente di modificare il colore da un valore arbitrario.

-   **Tavolozza materiale** &ndash; consente di modificare il colore da un valore che sia conforme alla progettazione di materiale.

-   **Risorse** &ndash; risorse nel tema di colore consente di modificare il colore a per uno degli altri esistenti.

Esaminare ognuna di queste schede in modo dettagliato.



### <a name="inherit-tab"></a>Ereditare scheda

Come illustrato nell'esempio seguente, il **eredita** Elenca l'ereditarietà di stile per il **Background** colore del **tema predefinito**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Ereditare scheda](material-design-features-images/vs/06-inherit-tab-sml.png)](material-design-features-images/vs/06-inherit-tab.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Ereditare scheda](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

-----

In questo esempio, il **tema predefinito** eredita da uno stile che utilizza `@color/background_material_dark` ma ne esegue l'override con `color/material_grey_850`, che presenta un valore del codice colore `#ff303030`.
Per ulteriori informazioni sull'ereditarietà degli stili, vedere [stili e temi](http://developer.android.com/guide/topics/ui/themes.html#Inheritance).



### <a name="color-picker"></a>Selezione dei colori

Nella schermata seguente viene illustrato il **selezione colori**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Selezione dei colori](material-design-features-images/vs/07-color-picker-sml.png)](material-design-features-images/vs/07-color-picker.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Selezione dei colori](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)

-----

In questo esempio, il **Background** colore può essere modificato per qualsiasi valore in vari modi:

-   Fare clic direttamente su un colore.
-   Immissione di valori di tonalità, saturazione e luminosità.
-   Valori RGB (rosso, verde e blu) in formato decimale.
-   Se si imposta il valore alfa (opacità) per il colore selezionato.
-   Immettere direttamente il codice esadecimale del colore.

È il colore scelto nella selezione colori *non* limitato alle linee guida di progettazione di materiale o per il set di risorse di colore disponibili.


### <a name="resources"></a>Risorse

Il **risorse** scheda offre un elenco di risorse di colore che sono già presenti nel tema:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Risorse](material-design-features-images/vs/08-resources-sml.png)](material-design-features-images/vs/08-resources.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Risorse](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

-----

Utilizzo di **risorse** scheda vincola le scelte effettuate a questo elenco di colori. Tenere presente che se si sceglie una risorsa colore che è già assegnata a un'altra parte del tema, due elementi adiacenti dell'interfaccia utente possono "eseguire insieme" (perché hanno lo stesso colore) e diventare difficile distinguere all'utente.



### <a name="material-palette"></a>Tavolozza materiale

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Il **tavolozza materiale** verrà visualizzata la scheda di **tavolozza dei colori di progettazione di materiale**. Scelta di un valore di colore dalla tavolozza a questo vincola la scelta di colori in modo che sia coerenza con le linee guida di progettazione di materiale.

[![Tavolozza materiale](material-design-features-images/vs/09-material-palette-sml.png)](material-design-features-images/vs/09-material-palette.png#lightbox)

La parte superiore della tavolozza dei colori Visualizza colori materiale progettazione primari mentre nella parte inferiore del riquadro viene visualizzato un intervallo di tonalità del colore primario selezionato. Ad esempio, quando si seleziona **Indigo**, una raccolta di **Indigo** tonalità viene visualizzato nella parte inferiore della finestra di dialogo.
Quando si seleziona una tonalità, il colore della proprietà viene modificato in tonalità selezionata. Nell'esempio seguente, il `Background Tint` del pulsante viene modificato in *500 Indigo*:

![Selezionare Indigo 500](material-design-features-images/vs/10-indigo.png)

`Background Tint` è impostato per il codice per il colore *500 Indigo* (`#ff3f51b5`), e la finestra di progettazione aggiorna il colore di sfondo per riflettere la modifica:

[![Tint background modificato](material-design-features-images/vs/11-background-tint-sml.png)](material-design-features-images/vs/11-background-tint.png#lightbox)

Per ulteriori informazioni sulla tavolozza dei colori materiale progettazione, vedere la progettazione di materiale [Guida tavolozza di colori](http://www.google.com/design/spec/style/color.html#color-color-palette).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Il **tavolozza materiale** verrà visualizzata la scheda di **tavolozza dei colori di progettazione di materiale** descritto [precedenti](#material_palette). Scelta di un valore di colore dalla tavolozza a questo vincola la scelta di colori in modo che sia coerenza con le linee guida di progettazione di materiale.

[![Tavolozza materiale](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

-----



### <a name="creating-a-new-theme"></a>Creazione di un nuovo tema

Nell'esempio seguente, la tavolozza materiale useremo per creare un nuovo tema personalizzato. Prima di tutto, verranno modificate le **Background** colore *900 blu*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Impostare sfondo su blu 900](material-design-features-images/vs/12-change-background-to-blue.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Impostare sfondo su blu 900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

-----


Quando viene modificata una risorsa di colore, un messaggio viene visualizzata con il messaggio, *il tema corrente contiene modifiche non salvate*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Modifiche non salvate avviso](material-design-features-images/vs/13-unsaved-changes-sml.png)](material-design-features-images/vs/13-unsaved-changes.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Modifiche non salvate avviso](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

-----


Il **Background** colore nella finestra di progettazione è stato modificato per la nuova selezione colore, ma questa modifica non è ancora stata salvata. Come gestire la modifica, sono disponibili due opzioni:

-   **Annullare le modifiche** &ndash; Elimina il nuovo colore scelto (o scelte) e viene ripristinato il tema allo stato originale.

-   **Crea nuovo tema** &ndash; crea un nuovo tema che è derivato dal tema attualmente selezionato e utilizza le sostituzioni di colore apportate il **Editor del tema**.

Quando fa clic su **Crea nuovo tema**, otterrà uno dei seguenti, a seconda del tema selezionato:

-   Se il tema attualmente selezionato nella finestra di progettazione non è un tema di progetto, viene visualizzata con l'opzione per creare un nuovo file di risorse con il tema personalizzato (usando il tema selezionato come padre del tema personalizzato). Le opzioni di progettazione per il tema personalizzato dopo averlo creato.

-   Se il tema selezionato è un progetto che è definito in un'unica posizione, viene visualizzata la **tema aggiornamento** opzione; facendo clic su questa opzione di aggiornamento del tema selezionato anziché crearne uno nuovo.

-   Se il tema selezionato è un progetto che è definito in più posizioni (ad esempio, in modo diverso-suffisso **valori** cartelle, ad esempio **valori v21**), viene visualizzata una finestra di dialogo che chiede per un nuovo percorso per salvare il tema personalizzato.

Continuare l'esempio precedente, fare clic su **Crea nuovo tema** comporta la creazione di un nuovo tema denominato **personalizzato**:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Tema personalizzato aggiunto](material-design-features-images/vs/14-custom-theme-sml.png)](material-design-features-images/vs/14-custom-theme.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Tema personalizzato aggiunto](material-design-features-images/xs/19-custom-theme-sml.png)](material-design-features-images/xs/19-custom-theme.png#lightbox)

-----


Poiché il tema attualmente selezionato non è un tema di progetto, non vi è nessuna finestra di dialogo per aggiornare il tema selezionato o per specificare un nuovo percorso.


## <a name="summary"></a>Riepilogo

In questo argomento viene descritto le caratteristiche di progettazione materiale disponibili nella finestra di progettazione di xamarin. Descritto come abilitare e configurare la griglia di progettazione di materiale, come utilizzare la tavolozza dei colori di materiale di progettazione per modificare le proprietà di colore e come utilizzare il selettore di scala tipografiche per configurare le proprietà di testo. Inoltre illustrato come utilizzare l'Editor del tema per creare nuovi temi personalizzati conformi alle linee guida di progettazione di materiale. Per ulteriori informazioni sul supporto di xamarin per la progettazione di materiale, vedere [tema materiale](~/android/user-interface/material-theme.md).



## <a name="related-links"></a>Collegamenti correlati

- [Tema Material](~/android/user-interface/material-theme.md)
- [Introduzione a progettazione materiale](https://www.google.com/design/spec/material-design/introduction.html)
