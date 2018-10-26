---
title: Funzionalità di progettazione dei materiali di xamarin. Android Designer
description: Questo argomento descrive le funzionalità di progettazione che rendono più semplice per gli sviluppatori creare layout di Material Design conformi. Questa sezione vengono introdotti e viene illustrato come utilizzare la griglia di materiale, la tavolozza dei colori di materiale, la scala tipografica e l'Editor del tema.
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: eb636c3b7a41adbab9162e192ead65def377a1a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118981"
---
# <a name="xamarinandroid-designer-material-design-features"></a>Funzionalità di xamarin. Android Designer Material Design

_Questo argomento descrive le funzionalità di progettazione che rendono più semplice per gli sviluppatori creare layout di Material Design conformi. Questa sezione vengono introdotti e viene illustrato come utilizzare la griglia di materiale, la tavolozza dei colori di materiale, la scala tipografica e l'Editor del tema._

> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evoluzione 2016: Tutti gli utenti possono creare App straordinarie con Material Design**

## <a name="overview"></a>Panoramica

La finestra di progettazione di xamarin. Android include funzionalità che rendono più semplice per creare layout di Material Design-conformi. Se non ha familiarità con Material Design, vedere la [introduction Material Design](https://material.io/design/introduction).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In questa Guida, è necessario esaminare le funzionalità di progettazione seguenti:

-   *Griglia Material* &ndash; sovrapposizione nell'area di progettazione che visualizza una griglia, spaziatura e attivare che consentono di posizionare i widget di layout in base alle linee guida di Material Design.

-   *Editor dei temi* &ndash; un editor di risorse piccola colore che consente di imposta le informazioni di colore per un subset di un tema. Ad esempio, è possibile visualizzare in anteprima e modificare, ad esempio i colori Material `colorPrimary`, `colorPrimaryDark`, e `colorAccent`.

Illustreremo occhiata ognuna di queste funzionalità e vengono forniti esempi di come usarli.

## <a name="material-design-grid"></a>Griglia di struttura materiali

Il menu di griglia di struttura materiale è disponibile nella barra degli strumenti nella parte superiore della finestra di progettazione:

[![Griglia di struttura materiale](material-design-features-images/vs/01-material-design-grid-w158-sml.png)](material-design-features-images/vs/01-material-design-grid-w158.png#lightbox)

Quando si fa clic sull'icona di griglia di struttura materiali, la finestra di progettazione viene visualizzato un controllo overlay nell'area di progettazione che include gli elementi seguenti:

-   Attivare (linee di colore arancione)

-   Spaziatura (aree verdi)

-   Una griglia (linee blu)

Questi elementi possono essere visualizzati nella schermata precedente. Ognuno di questi elementi di sovrimpressione è configurabile. Quando si fa clic sui puntini di sospensione accanto al menu di griglia di struttura materiali, viene visualizzata nel popover una finestra di dialogo che consente di abilitare o disabilitare la griglia, configurare la posizione di attivare e impostare spaziatura. Si noti che tutti i valori sono espressi in `dp` (pixel indipendenti dalla densità):

[![Griglia, cornice e la configurazione di spaziatura](material-design-features-images/vs/03-grid-configuration-w158-sml.png)](material-design-features-images/vs/03-grid-configuration-w158.png#lightbox)

Per aggiungere una nuova cornice, immettere un nuovo valore di offset nella **Offset** selezionare un percorso (**a sinistra**, **top**, **destra**, o  **nella parte inferiore**) e fare clic sull'icona per aggiungere la nuova cornice +. Analogamente, per aggiungere un nuovo spazio, immettere le dimensioni e offset (in dp) nei **dimensioni** e **Offset** caselle, rispettivamente. Selezionare un percorso (**a sinistra**, **top**, **destro**, o **inferiore**) e fare clic sull'icona + per aggiungere il nuovo spazio.

Quando si modificano i valori di configurazione, vengono salvati nel file XML di layout e riutilizzati quando si apre nuovamente il layout.


## <a name="theme-editor"></a>Editor del tema

Il **Theme Editor** consente di personalizzare le informazioni di colore per un subset di attributi di tema. Per aprire la **Theme Editor**, fai clic sull'icona del pennello nella barra degli strumenti:

[![Icona Editor del tema](material-design-features-images/vs/04-theme-editor-icon-w158-sml.png)](material-design-features-images/vs/04-theme-editor-icon-w158.png#lightbox)

Anche se il **Theme Editor** è accessibile dalla barra degli strumenti per tutti i destinazione versioni di Android e i livelli API, solo un subset delle funzionalità descritte di seguito sono disponibili se il livello API di destinazione è precedente alla API 21 (Android 5.0 Lollipop).

Il pannello a sinistra del **Theme Editor** consente di visualizzare l'elenco dei colori che formano il tema attualmente selezionato (in questo esempio, utilizziamo il `Default Theme`):

[![Editor del tema](material-design-features-images/vs/05-theme-editor-w158-sml.png)](material-design-features-images/vs/05-theme-editor-w158.png#lightbox)

Quando si seleziona un colore a sinistra, pannello a destra sono disponibili le schede seguenti che consentono di modificare tale colore:

-   **Ereditare** &ndash; Visualizza un diagramma di ereditarietà di stile del colore selezionato ed elenca il colore risolto e il codice colore assegnato al colore del tema.

-   **Selezione dei colori** &ndash; consente di modificare il colore selezionato su qualsiasi valore arbitrario.

-   **Pannello materiali** &ndash; consente di modificare il colore selezionato a un valore conforme alla progettazione di materiale.

-   **Le risorse** &ndash; consente di modificare il colore selezionato in una delle altre risorse colore esistente nel tema.

Esaminiamo ognuna di queste schede in modo dettagliato.

### <a name="inherit-tab"></a>Ereditare scheda

Come illustrato nell'esempio seguente, il **eredita** Elenca l'ereditarietà di stile per il **sfondo** colore del **tema predefinito**:

[![Ereditare scheda](material-design-features-images/vs/06-inherit-tab-w158-sml.png)](material-design-features-images/vs/06-inherit-tab-w158.png#lightbox)

In questo esempio, il **tema predefinito** eredita da uno stile che usa `@color/background_material_light` ma ne esegue l'override con `color/material_grey_50`, che ha un valore del codice colore `#fffafafa`.
Per altre informazioni sull'ereditarietà degli stili, vedere [stili e temi](http://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Selezione dei colori

Lo screenshot seguente illustra il **Barev**:

[![Selezione colori](material-design-features-images/vs/07-color-picker-w158-sml.png)](material-design-features-images/vs/07-color-picker-w158.png#lightbox)

In questo esempio, il **sfondo** colore può essere modificato a qualsiasi valore in vari modi:

-   Fare clic direttamente su un colore.
-   Immissione filestreamstorelibrary. i valori di tonalità, saturazione e luminosità.
-   Valori RGB (rosso, verde, blu) in formato decimale.
-   Se si imposta il valore alfa (opacity) del colore selezionato.
-   Immissione filestreamstorelibrary. direttamente al codice colore esadecimale.

È il colore scelto nella selezione colori *non* limitato alle linee guida di Material Design o il set di risorse di colore disponibili.

### <a name="resources"></a>Risorse

Il **risorse** scheda include un elenco di risorse di colore che sono già presenti nel tema:

[![Risorse](material-design-features-images/vs/08-resources-w158-sml.png)](material-design-features-images/vs/08-resources-w158.png#lightbox)

Usando il **risorse** scheda vincola le scelte effettuate a questo elenco di colori. Tenere presente che se si sceglie una risorsa di colore che è già assegnata a un'altra parte del tema, due elementi adiacenti dell'interfaccia utente possono "eseguire insieme" (perché hanno lo stesso colore) e diventa difficile per all'utente di distinguere.

### <a name="material-palette"></a>Pannello materiali

Il **materiale tavolozza** verrà visualizzata la scheda le **tavolozza dei colori di Material Design**. Scelta di un valore di colore dalla tavolozza a questo limita la scelta colori in modo che sia coerenza con le linee guida Material Design:

[![Pannello materiali](material-design-features-images/vs/09-material-palette-w158-sml.png)](material-design-features-images/vs/09-material-palette-w158.png#lightbox)

La parte superiore della tavolozza dei colori Visualizza colori primari Material Design mentre la parte inferiore della tavolozza Mostra un intervallo di tonalità del colore primario selezionato. Ad esempio, quando si seleziona **Indigo**, una raccolta di **Indigo** tonalità viene visualizzato nella parte inferiore della finestra di dialogo.
Quando si seleziona un hue, il colore della proprietà viene modificato in tonalità selezionata. Nell'esempio seguente, il `Background Tint` del pulsante viene modificato in *500 Indigo*:

![Selezionare 500 Indigo](material-design-features-images/vs/10-indigo-w158.png)

`Background Tint` è impostato per il codice per il colore *500 Indigo* (`#ff3f51b5`), e la finestra di progettazione aggiorna il colore di sfondo per riflettere questa modifica:

[![Tint sfondo modificato](material-design-features-images/vs/11-background-tint-w158-sml.png)](material-design-features-images/vs/11-background-tint-w158.png#lightbox)

Per altre informazioni sulla tavolozza dei colori Material Design, vedere il Material Design [Guida alla tavolozza dei colori](https://material.io/design/color/).

### <a name="creating-a-new-theme"></a>Creazione di un nuovo tema

Nell'esempio seguente, si userà la tavolozza di materiale per creare un nuovo tema personalizzato. In primo luogo, verranno modificate le **sfondo** colore *900 blu*:

![Modificare in background fino al 900 blu](material-design-features-images/vs/12-change-background-to-blue-w158.png)

Quando viene modificata una risorsa di colore, un messaggio viene visualizzata con il messaggio *tema corrente contiene modifiche non salvate*:

[![Modifiche non salvate avviso](material-design-features-images/vs/13-unsaved-changes-w158-sml.png)](material-design-features-images/vs/13-unsaved-changes-w158.png#lightbox)

Il **sfondo** è cambiato colore nella finestra di progettazione per la nuova selezione di colore, ma questa modifica non è ancora stata salvata. A questo punto, è possibile eseguire una delle operazioni seguenti:

-   Fare clic su **Ignora modifiche** per annullare il nuovo colore scelto (o scelte) e ripristinare il tema allo stato originale. 

-   Premere <kbd>CTRL + S</kbd> per salvare le modifiche per il tema attualmente. 

Nell'esempio riportato di seguito <kbd>CTRL + S</kbd> è stato fatto in modo che le modifiche sono state salvate **AppTheme**:

[![Modifiche salvate per AppTheme](material-design-features-images/vs/14-custom-theme-w158-sml.png)](material-design-features-images/vs/14-custom-theme-w158.png#lightbox)

## <a name="summary"></a>Riepilogo

Questo argomento descrive le funzionalità di Material Design disponibili nella finestra di progettazione di xamarin. Android. Spiega come abilitare e configurare la griglia di struttura materiali e spiega come usare l'Editor del tema per creare nuovi temi personalizzati che sono conformi alle linee guida di Material Design.
Per altre informazioni sul supporto di xamarin. Android per la progettazione di materiale, vedere [tema Material](~/android/user-interface/material-theme.md).




# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In questa Guida, ti invieremo un esaminare le funzionalità di progettazione seguenti:

-   *Griglia di struttura materiali* &ndash; sovrapposizione nell'area di progettazione che visualizza una griglia, spaziatura e attivare che consentono di posizionare i widget di layout in base alle linee guida di Material Design.

-   *Tavolozza dei colori di Material Design* &ndash; finestra di dialogo riempimento di una proprietà che facilitano la scelta di un colore dalla tavolozza Material Design ufficiale.

-   *Scalabilità tipografiche* &ndash; finestra di dialogo riempimento di una proprietà che fornisce una scelta di Material Design-conforme a impostazioni per il `textAppearance` proprietà dei campi di testo.

-   *Editor dei temi* &ndash; un editor di risorse piccola colore che consente di imposta le informazioni di colore per un subset di un tema. Ad esempio, è possibile visualizzare in anteprima e modificare, ad esempio i colori Material `colorPrimary`, `colorPrimaryDark`, e `colorAccent`.

Illustreremo occhiata ognuna di queste funzionalità e vengono forniti esempi di come usarli.

## <a name="material-design-grid"></a>Griglia di struttura materiali

Il menu di griglia di struttura materiale è disponibile nella barra degli strumenti nella parte superiore della finestra di progettazione:

[![Griglia di struttura materiale](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

Quando si fa clic sull'icona di griglia di struttura materiali, la finestra di progettazione viene visualizzato un controllo overlay nell'area di progettazione che include gli elementi seguenti:

-   Attivare (linee di colore arancione)

-   Spaziatura (aree verdi)

-   Una griglia (linee blu)

Questi elementi possono essere visualizzati nella schermata seguente:

[![Cornice e Spaziatura griglia](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

Ognuno di questi elementi di sovrimpressione è configurabile. Quando si fa clic sui puntini di sospensione (&hellip;) accanto al menu di griglia di struttura materiali, viene visualizzata nel popover una finestra di dialogo che consente di abilitare o disabilitare la griglia, configurare la posizione di attivare e impostare la spaziatura. Si noti che tutti i valori sono espressi in `dp` (pixel indipendenti dalla densità):

[![Griglia, cornice e la configurazione di spaziatura](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

Per aggiungere una nuova cornice, immettere un nuovo valore di offset nella **Offset** selezionare un percorso (**a sinistra**, **top**, **destra**, o  **nella parte inferiore**) e fare clic sull'icona, che compare all'oggetto a destra quando viene immesso un valore, + per aggiungere la nuova cornice. Analogamente, per aggiungere un nuovo spazio, immettere le dimensioni e offset (in dp) nei **dimensioni** e **Offset** caselle, rispettivamente. Selezionare un percorso (**a sinistra**, **top**, **destro**, o **inferiore**) e fare clic sull'icona + per aggiungere il nuovo spazio.

Quando si modificano i valori di configurazione, vengono salvati nel file XML di layout e riutilizzati quando si apre nuovamente il layout.

## <a name="material-design-color-palette"></a>Tavolozza dei colori di Material Design

Ogni elemento del Pannello proprietà che accetta un colore a questo punto ha un'icona di tavolozza aggiuntiva che è possibile usare per aprire la tavolozza dei colori di progettazione di materiale, come illustrato in questo screenshot:

[![Icona di colore](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

Quando si fa clic su questa icona, viene aperto nel popover una finestra di dialogo che rende possibile per configurare il colore di tale proprietà dalla tavolozza dei colori Material Design:

[![Tavolozza dei colori di Material Design](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

La parte superiore della tavolozza dei colori Visualizza colori primari Material Design mentre la parte inferiore della tavolozza Mostra un intervallo di tonalità del colore primario selezionato. Ad esempio, quando si seleziona **Indigo**, una raccolta di **Indigo** tonalità viene visualizzato nella parte inferiore della finestra di dialogo.
Quando si seleziona un hue, il colore della proprietà viene modificato in tonalità selezionata. Nell'esempio seguente, il `Background Tint` del pulsante viene modificato in *500 Indigo*:

[![Scegliere 500 Indigo](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` è impostato per il codice per il colore *500 Indigo* (`#ff3f51b5`), e la finestra di progettazione aggiorna il colore di sfondo del pulsante in modo da riflettere questa modifica:

[![Modifiche di sfondo tinta](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

Per altre informazioni sulla tavolozza dei colori Material Design, vedere il Material Design [Guida alla tavolozza dei colori](https://material.io/design/color/).

## <a name="typographic-scale"></a>Scalabilità tipografiche

Il **aspetto del testo** sezione del **proprietà** pad **stile** scheda ha un'icona che consente di effettuare una selezione da un `TextAppearance` stile corrispondendente Material Design specifica:

[![Scheda stili](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

Quando si fa clic su questa icona, viene aperto il **scalabilità tipografiche** nel popover finestra di dialogo, che presenta un elenco degli stili di testo configurati in precedenza che è possibile scegliere tra:

[![Selettore di stile di visualizzazione testo](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

Nell'esempio seguente, facendo clic su **Display 1** modifica il testo del pulsante per il tipo di carattere più grande del **schermo 1**:

[![Stile di visualizzazione 1](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

Lo stile del testo nel **scalabilità tipografiche** segue la **tema** impostazione. Ad esempio, se il **Light** tema viene scelta nella finestra di progettazione, l'elenco dei mirror stili testo disponibile il **Light** tema:

[![Tema chiaro](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

## <a name="theme-editor"></a>Editor del tema

Il **Theme Editor** consente di personalizzare le informazioni di colore per un subset di attributi di tema. Per aprire la **Theme Editor**, fai clic sull'icona del pennello nella barra degli strumenti:

[![Icona Editor del tema](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

Anche se il **Theme Editor** è accessibile dalla barra degli strumenti per tutti i destinazione versioni di Android e i livelli API, solo un subset delle funzionalità descritte di seguito sono disponibili se il livello API di destinazione è precedente alla API 21 (Android 5.0 Lollipop).

Il pannello a sinistra del **Theme Editor** consente di visualizzare l'elenco dei colori che formano il tema attualmente selezionato (in questo esempio, utilizziamo il `Default Theme`):

[![Editor del tema](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

Quando si seleziona un colore a sinistra, pannello a destra sono disponibili le schede seguenti che consentono di modificare tale colore:

-   **Ereditare** &ndash; Visualizza un diagramma di ereditarietà di stile del colore selezionato ed elenca il colore risolto e il codice colore assegnato al colore del tema.

-   **Selezione dei colori** &ndash; consente di modificare il colore selezionato su qualsiasi valore arbitrario.

-   **Pannello materiali** &ndash; consente di modificare il colore selezionato a un valore conforme alla progettazione di materiale.

-   **Le risorse** &ndash; consente di modificare il colore selezionato in una delle altre risorse colore esistente nel tema.

Esaminiamo ognuna di queste schede in modo dettagliato.

### <a name="inherit-tab"></a>Ereditare scheda

Come illustrato nell'esempio seguente, il **eredita** Elenca l'ereditarietà di stile per il **sfondo** colore del **tema predefinito**:

[![Ereditare scheda](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

In questo esempio, il **tema predefinito** eredita da uno stile che usa `@color/background_material_dark` ma ne esegue l'override con `color/material_grey_850`, che ha un valore del codice colore `#ff303030`.
Per altre informazioni sull'ereditarietà degli stili, vedere [stili e temi](http://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Selezione dei colori

Lo screenshot seguente illustra il **Barev**:

[![Selezione colori](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)


In questo esempio, il **sfondo** colore può essere modificato a qualsiasi valore in vari modi:

-   Fare clic direttamente su un colore.
-   Immissione filestreamstorelibrary. i valori di tonalità, saturazione e luminosità.
-   Valori RGB (rosso, verde, blu) in formato decimale.
-   Se si imposta il valore alfa (opacity) del colore selezionato.
-   Immissione filestreamstorelibrary. direttamente al codice colore esadecimale.

È il colore scelto nella selezione colori *non* limitato alle linee guida di Material Design o il set di risorse di colore disponibili.

### <a name="resources"></a>Risorse

Il **risorse** scheda include un elenco di risorse di colore che sono già presenti nel tema:

[![Risorse](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

Usando il **risorse** scheda vincola le scelte effettuate a questo elenco di colori. Tenere presente che se si sceglie una risorsa di colore che è già assegnata a un'altra parte del tema, due elementi adiacenti dell'interfaccia utente possono "eseguire insieme" (perché hanno lo stesso colore) e diventa difficile per all'utente di distinguere.

### <a name="material-palette"></a>Pannello materiali

Il **materiale tavolozza** verrà visualizzata la scheda le **tavolozza dei colori di Material Design** descritto [precedenti](#material_palette). Scelta di un valore di colore dalla tavolozza a questa vincola la scelta di colori in modo che sia coerenza con le linee guida Material Design.

[![Pannello materiali](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

### <a name="creating-a-new-theme"></a>Creazione di un nuovo tema

Nell'esempio seguente, si userà la tavolozza di materiale per creare un nuovo tema personalizzato. In primo luogo, verranno modificate le **sfondo** colore *900 blu*:

[![Modificare in background fino al 900 blu](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

Quando viene modificata una risorsa di colore, un messaggio viene visualizzata con il messaggio *tema corrente contiene modifiche non salvate*:

[![Modifiche non salvate avviso](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

È stata apportata la modifica di colori nella finestra di progettazione, ma questa modifica non è ancora stata salvata. A questo punto, è possibile eseguire una delle operazioni seguenti:

-   Fare clic su **Ignora modifiche** per annullare il nuovo colore scelto (o scelte) e ripristinare il tema allo stato originale. 

-   Premere  **&#8984; + S** per salvare le modifiche a un nuovo tema chiamato **Custom**. 


## <a name="summary"></a>Riepilogo

Questo argomento descrive le funzionalità di Material Design disponibili nella finestra di progettazione di xamarin. Android. Spiega come abilitare e configurare la griglia di struttura materiali, come utilizzare la tavolozza dei colori di materiale di progettazione per modificare le proprietà di colore e come usare il selettore di scala tipografiche per configurare le proprietà di testo. È stato anche illustrato come usare l'Editor del tema per creare nuovi temi personalizzati che sono conformi alle linee guida di Material Design. Per altre informazioni sul supporto di xamarin. Android per la progettazione di materiale, vedere [tema Material](~/android/user-interface/material-theme.md).

-----


## <a name="related-links"></a>Collegamenti correlati

- [Tema Material](~/android/user-interface/material-theme.md)
- [Introduzione alla progettazione di materiale](https://material.io/design/introduction)
