---
title: Novell. Android Designer funzionalità di progettazione materiali
description: In questo argomento vengono descritte le funzionalità di progettazione che rendono più semplice per gli sviluppatori la creazione di layout materiali conformi alla progettazione. In questa sezione viene illustrato come utilizzare la griglia Material, la tavolozza dei colori Material, la scala tipografica e l'editor del tema.
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: 43397fb855bdf872cf17b315044f34a468c22d00
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029442"
---
# <a name="xamarinandroid-designer-material-design-features"></a>Novell. Android Designer funzionalità di progettazione materiali

_In questo argomento vengono descritte le funzionalità di progettazione che rendono più semplice per gli sviluppatori la creazione di layout materiali conformi alla progettazione. In questa sezione viene illustrato come utilizzare la griglia Material, la tavolozza dei colori Material, la scala tipografica e l'editor del tema._

> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evolve 2016: chiunque può creare app bellissime con la progettazione del materiale**

## <a name="overview"></a>Panoramica

In Novell. Android Designer sono incluse funzionalità che semplificano la creazione di layout compatibili con la progettazione del materiale. Se non si ha familiarità con la progettazione di materiali, vedere [Introduzione alla progettazione di materiali](https://material.io/design/introduction).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In questa guida, verranno esaminate le funzionalità di progettazione seguenti:

- *Griglia materiale* &ndash; una sovrapposizione sul area di progettazione che mostra una griglia, spaziatura e linee guida per inserire i widget di layout in base alle linee guida per la progettazione di materiali.

- *Editor del tema* &ndash; un editor di risorse di colore piccolo che consente di impostare le informazioni sui colori per un subset di un tema. Ad esempio, è possibile visualizzare in anteprima e modificare i colori materiali come `colorPrimary`, `colorPrimaryDark`e `colorAccent`.

Si osserveranno ognuna di queste funzionalità e verranno forniti esempi di come utilizzarle.

## <a name="material-design-grid"></a>Griglia di progettazione materiale

Il menu griglia Progettazione materiale è disponibile dalla barra degli strumenti nella parte superiore della finestra di progettazione:

[griglia di progettazione materiale![](material-design-features-images/vs/01-material-design-grid-w158-sml.png)](material-design-features-images/vs/01-material-design-grid-w158.png#lightbox)

Quando si fa clic sull'icona della griglia di progettazione materiale, nella finestra di progettazione viene visualizzata una sovrapposizione nell'Area di progettazione che include gli elementi seguenti:

- Linee di riga (linee arancioni)

- Spaziatura (aree verdi)

- Griglia (linee blu)

Questi elementi possono essere visualizzati nella schermata precedente. Ognuno di questi elementi sovrapposti è configurabile. Quando si fa clic sui puntini di sospensione accanto al menu della griglia di progettazione materiale, viene aperta una finestra di dialogo che consente di disabilitare o abilitare la griglia, configurare il posizionamento delle linee di tasto e impostare spaziatura. Si noti che tutti i valori sono espressi in `dp` (pixel indipendenti dalla densità):

[configurazione della griglia, della riga di![e della spaziatura](material-design-features-images/vs/03-grid-configuration-w158-sml.png)](material-design-features-images/vs/03-grid-configuration-w158.png#lightbox)

Per aggiungere una nuova riga di tasto, immettere un nuovo valore di offset nella casella **offset** , selezionare un percorso (a**sinistra**, in **alto**, a **destra**o in **basso**) e fare clic sull'icona + per aggiungere la nuova riga di tasto. Analogamente, per aggiungere una nuova spaziatura, immettere rispettivamente le dimensioni e l'offset (in DP) nelle caselle **dimensioni** e **offset** . Selezionare un percorso (a**sinistra**, in **alto**, a **destra**o in **basso**) e fare clic sull'icona + per aggiungere la nuova spaziatura.

Quando si modificano questi valori di configurazione, questi vengono salvati nel file XML di layout e riusati quando si apre di nuovo il layout.

## <a name="theme-editor"></a>Editor tema

L' **Editor del tema** consente di personalizzare le informazioni sui colori per un subset di attributi del tema. Per aprire l' **Editor del tema**, fare clic sull'icona del pennello sulla barra degli strumenti:

[icona dell'editor del tema![](material-design-features-images/vs/04-theme-editor-icon-w158-sml.png)](material-design-features-images/vs/04-theme-editor-icon-w158.png#lightbox)

Sebbene l' **Editor del tema** sia accessibile dalla barra degli strumenti per tutte le versioni di Android e i livelli API di destinazione, è disponibile solo un subset delle funzionalità descritte di seguito se il livello API di destinazione è precedente all'API 21 (Android 5,0 Lollipop).

Il pannello a sinistra dell'editor del **tema** Visualizza l'elenco dei colori che costituiscono il tema attualmente selezionato (in questo esempio viene usato il `Default Theme`):

[Editor tema![](material-design-features-images/vs/05-theme-editor-w158-sml.png)](material-design-features-images/vs/05-theme-editor-w158.png#lightbox)

Quando si seleziona un colore a sinistra, nel pannello destro sono disponibili le schede seguenti che consentono di modificare il colore:

- **Eredita** &ndash; Visualizza un diagramma di ereditarietà dello stile per il colore selezionato ed elenca il colore risolto e il codice colori assegnato a tale colore del tema.

- **Selezione colori** &ndash; consente di modificare il colore selezionato in qualsiasi valore arbitrario.

- **Tavolozza materiali** &ndash; consente di modificare il colore selezionato in un valore conforme a progettazione materiale.

- &ndash; **risorse** consente di modificare il colore selezionato in una delle altre risorse di colore esistenti nel tema.

Esaminiamo ognuna di queste schede in dettaglio.

### <a name="inherit-tab"></a>Eredita scheda

Come illustrato nell'esempio seguente, la scheda **eredita** elenca l'ereditarietà dello stile per il colore di **sfondo** del **tema predefinito**:

[Scheda eredita![](material-design-features-images/vs/06-inherit-tab-w158-sml.png)](material-design-features-images/vs/06-inherit-tab-w158.png#lightbox)

In questo esempio, il **tema predefinito** eredita da uno stile che usa `@color/background_material_light` ma lo sostituisce con `color/material_grey_50`, che ha un valore di codice colore di `#fffafafa`.
Per ulteriori informazioni sull'ereditarietà dello stile, vedere [stili e temi](https://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Selezione dei colori

Lo screenshot seguente illustra la **selezione colori**:

[Selezione colori![](material-design-features-images/vs/07-color-picker-w158-sml.png)](material-design-features-images/vs/07-color-picker-w158.png#lightbox)

In questo esempio, il colore di **sfondo** può essere modificato in qualsiasi valore tramite vari modi:

- Facendo clic direttamente su un colore.
- Immissione dei valori di tonalità, saturazione e luminosità.
- Immissione di valori RGB (rosso, verde, blu) in decimali.
- Impostazione dell'alfa (opacità) per il colore selezionato.
- Immissione diretta del codice colore esadecimale.

Il colore scelto nella selezione colori *non* è limitato alle linee guida di progettazione del materiale o al set di risorse di colore disponibili.

### <a name="resources"></a>Risorse

La scheda **risorse** offre un elenco di risorse di colore già presenti nel tema:

[Risorse![](material-design-features-images/vs/08-resources-w158-sml.png)](material-design-features-images/vs/08-resources-w158.png#lightbox)

L'uso della scheda **risorse** vincola le scelte a questo elenco di colori. Tenere presente che se si sceglie una risorsa di colore già assegnata a un'altra parte del tema, due elementi adiacenti dell'interfaccia utente possono essere "eseguiti insieme" (poiché hanno lo stesso colore) e diventano difficili da distinguere con l'utente.

### <a name="material-palette"></a>Tavolozza materiali

La scheda **tavolozza materiale** apre la **tavolozza dei colori di progettazione materiali**. La scelta di un valore di colore da questa tavolozza vincola la scelta del colore in modo che sia coerente con le linee guida di progettazione del materiale:

[Tavolozza materiale![](material-design-features-images/vs/09-material-palette-w158-sml.png)](material-design-features-images/vs/09-material-palette-w158.png#lightbox)

Nella parte superiore della tavolozza dei colori vengono visualizzati i colori di progettazione materiale primario, mentre nella parte inferiore della tavolozza viene visualizzato un intervallo di colori per il colore primario selezionato. Ad esempio, quando si seleziona **Indigo**, viene visualizzata una raccolta di tonalità **Indigo** nella parte inferiore della finestra di dialogo.
Quando si seleziona una tonalità, il colore della proprietà viene impostato sulla tonalità selezionata. Nell'esempio seguente, la `Background Tint` del pulsante è stata modificata in *Indigo 500*:

![Selezionare Indigo 500](material-design-features-images/vs/10-indigo-w158.png)

`Background Tint` è impostato sul codice colore per *Indigo 500* (`#ff3f51b5`) e la finestra di progettazione aggiorna il colore di sfondo in modo da riflettere questa modifica:

[tonalità sfondo![modificata](material-design-features-images/vs/11-background-tint-w158-sml.png)](material-design-features-images/vs/11-background-tint-w158.png#lightbox)

Per ulteriori informazioni sulla tavolozza dei colori di progettazione materiale, vedere la pagina relativa alla [Guida della tavolozza dei colori](https://material.io/design/color/)di progettazione materiali.

### <a name="creating-a-new-theme"></a>Creazione di un nuovo tema

Nell'esempio seguente verrà usata la tavolozza Material per creare un nuovo tema personalizzato. In primo luogo, il colore di **sfondo** verrà modificato in *blu 900*:

![Imposta sfondo su blu 900](material-design-features-images/vs/12-change-background-to-blue-w158.png)

Quando viene modificata una risorsa di colore, viene visualizzato un messaggio con il messaggio, *il tema corrente contiene modifiche non salvate*:

[avviso modifiche non salvate![](material-design-features-images/vs/13-unsaved-changes-w158-sml.png)](material-design-features-images/vs/13-unsaved-changes-w158.png#lightbox)

Il colore di sfondo nella finestra di progettazione è stato modificato in **base** alla nuova selezione colore, ma questa modifica non è ancora stata salvata. A questo punto, è possibile eseguire una delle operazioni seguenti:

- Fare clic su Rimuovi **modifiche** per eliminare la nuova scelta di colore (o opzioni) e ripristinare lo stato originale del tema.

- Premere <kbd>CTRL + S</kbd> per salvare le modifiche apportate al tema corrente.

Nell'esempio seguente è stato premuto <kbd>CTRL + S</kbd> , in modo che le modifiche siano state salvate in **AppTheme**:

[![modifiche salvate in AppTheme](material-design-features-images/vs/14-custom-theme-w158-sml.png)](material-design-features-images/vs/14-custom-theme-w158.png#lightbox)

## <a name="summary"></a>Riepilogo

Questo argomento descrive le funzionalità di progettazione dei materiali disponibili in Novell. Android Designer. È stato illustrato come abilitare e configurare la griglia di progettazione del materiale e come usare l'editor di temi per creare nuovi temi personalizzati conformi alle linee guida per la progettazione di materiali.
Per ulteriori informazioni sul supporto di Novell. Android per la progettazione di materiali, vedere [Material Theme](~/android/user-interface/material-theme.md).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In questa guida, verranno esaminate le funzionalità di progettazione seguenti:

- *Griglia di progettazione materiale* &ndash; una sovrapposizione sul area di progettazione che mostra una griglia, una spaziatura e le linee di tastiera che consentono di posizionare i widget di layout in base alle linee guida per la progettazione di materiali.

- *Tavolozza dei colori di progettazione materiale* &ndash; una finestra di dialogo di riempimento delle proprietà che facilita la scelta di un colore dalla tavolozza di progettazione materiale ufficiale.

- *Scala tipografica* &ndash; una finestra di dialogo di riempimento delle proprietà che consente di scegliere le impostazioni compatibili con la progettazione del materiale per la proprietà `textAppearance` dei campi di testo.

- *Editor del tema* &ndash; un editor di risorse di colore piccolo che consente di impostare le informazioni sui colori per un subset di un tema. Ad esempio, è possibile visualizzare in anteprima e modificare i colori materiali come `colorPrimary`, `colorPrimaryDark`e `colorAccent`.

Si osserveranno ognuna di queste funzionalità e verranno forniti esempi di come utilizzarle.

## <a name="material-design-grid"></a>Griglia di progettazione materiale

Il menu griglia Progettazione materiale è disponibile dalla barra degli strumenti nella parte superiore della finestra di progettazione:

[griglia di progettazione materiale![](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

Quando si fa clic sull'icona della griglia di progettazione materiale, nella finestra di progettazione viene visualizzata una sovrapposizione nell'Area di progettazione che include gli elementi seguenti:

- Linee di riga (linee arancioni)

- Spaziatura (aree verdi)

- Griglia (linee blu)

Questi elementi possono essere visualizzati nello screenshot seguente:

[![riga, spaziatura e griglia](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

Ognuno di questi elementi sovrapposti è configurabile. Quando si fa clic sui puntini di sospensione (&hellip;) accanto al menu della griglia di progettazione materiale, viene aperta una finestra di dialogo che consente di disabilitare o abilitare la griglia, configurare il posizionamento delle linee di tasto e impostare le spaziatura. Si noti che tutti i valori sono espressi in `dp` (pixel indipendenti dalla densità):

[configurazione della griglia, della riga di![e della spaziatura](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

Per aggiungere una nuova riga di tasto, immettere un nuovo valore di offset nella casella **offset** , selezionare un percorso (a**sinistra**, in **alto**, a **destra**o in **basso**) e fare clic sull'icona + (visualizzata a destra quando viene immesso un valore) per aggiungere la nuova riga di tasto. Analogamente, per aggiungere una nuova spaziatura, immettere rispettivamente le dimensioni e l'offset (in DP) nelle caselle **dimensioni** e **offset** . Selezionare un percorso (a**sinistra**, in **alto**, a **destra**o in **basso**) e fare clic sull'icona + per aggiungere la nuova spaziatura.

Quando si modificano questi valori di configurazione, questi vengono salvati nel file XML di layout e riusati quando si apre di nuovo il layout.

## <a name="material-design-color-palette"></a>Tavolozza colori progettazione materiale

Ogni elemento del pannello Proprietà che accetta un colore dispone ora di un'icona della tavolozza aggiuntiva che è possibile usare per aprire la tavolozza dei colori di progettazione materiale, come illustrato nello screenshot seguente:

[icona colore![](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

Quando si fa clic su questa icona, viene aperta una finestra di dialogo in cui è possibile configurare il colore della proprietà dalla tavolozza dei colori di progettazione materiale:

[tavolozza colori![progettazione materiale](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

Nella parte superiore della tavolozza dei colori vengono visualizzati i colori di progettazione materiale primario, mentre nella parte inferiore della tavolozza viene visualizzato un intervallo di colori per il colore primario selezionato. Ad esempio, quando si seleziona **Indigo**, viene visualizzata una raccolta di tonalità **Indigo** nella parte inferiore della finestra di dialogo.
Quando si seleziona una tonalità, il colore della proprietà viene impostato sulla tonalità selezionata. Nell'esempio seguente, la `Background Tint` del pulsante è stata modificata in *Indigo 500*:

[![scegliere Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` è impostato sul codice colore per *Indigo 500* (`#ff3f51b5`) e la finestra di progettazione aggiorna il colore di sfondo del pulsante in modo da riflettere questa modifica:

[![modifiche tonalità sfondo](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

Per ulteriori informazioni sulla tavolozza dei colori di progettazione materiale, vedere la pagina relativa alla [Guida della tavolozza dei colori](https://material.io/design/color/)di progettazione materiali.

## <a name="typographic-scale"></a>Scala tipografica

Nella sezione **aspetto testo** della scheda **stile** del riquadro **Proprietà** è presente un'icona che consente di selezionare uno stile `TextAppearance` conforme alla specifica di progettazione materiale:

[scheda stile![](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

Quando si fa clic su questa icona, viene aperta la finestra di dialogo relativa alla **scala dei tipografia** , che presenta un elenco di stili di testo preconfigurati tra cui è possibile scegliere:

[selezione stile testo![](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

Nell'esempio seguente, se si fa clic su **Visualizza 1** , il testo del pulsante viene modificato in un tipo di carattere più grande del **display 1**:

[![Visualizza 1 stile](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

Lo stile del testo nella finestra di dialogo **scala tipografica** segue l'impostazione del **tema** . Se, ad esempio, il tema **chiaro** viene scelto nella finestra di progettazione, l'elenco degli stili di testo disponibili rispecchia il tema **chiaro** :

[tema chiaro![](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

## <a name="theme-editor"></a>Editor tema

L' **Editor del tema** consente di personalizzare le informazioni sui colori per un subset di attributi del tema. Per aprire l' **Editor del tema**, fare clic sull'icona del pennello sulla barra degli strumenti:

[icona dell'editor del tema![](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

Sebbene l' **Editor del tema** sia accessibile dalla barra degli strumenti per tutte le versioni di Android e i livelli API di destinazione, è disponibile solo un subset delle funzionalità descritte di seguito se il livello API di destinazione è precedente all'API 21 (Android 5,0 Lollipop).

Il pannello a sinistra dell'editor del **tema** Visualizza l'elenco dei colori che costituiscono il tema attualmente selezionato (in questo esempio viene usato il `Default Theme`):

[Editor tema![](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

Quando si seleziona un colore a sinistra, nel pannello destro sono disponibili le schede seguenti che consentono di modificare il colore:

- **Eredita** &ndash; Visualizza un diagramma di ereditarietà dello stile per il colore selezionato ed elenca il colore risolto e il codice colori assegnato a tale colore del tema.

- **Selezione colori** &ndash; consente di modificare il colore selezionato in qualsiasi valore arbitrario.

- **Tavolozza materiali** &ndash; consente di modificare il colore selezionato in un valore conforme a progettazione materiale.

- &ndash; **risorse** consente di modificare il colore selezionato in una delle altre risorse di colore esistenti nel tema.

Esaminiamo ognuna di queste schede in dettaglio.

### <a name="inherit-tab"></a>Eredita scheda

Come illustrato nell'esempio seguente, la scheda **eredita** elenca l'ereditarietà dello stile per il colore di **sfondo** del **tema predefinito**:

[Scheda eredita![](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

In questo esempio, il **tema predefinito** eredita da uno stile che usa `@color/background_material_dark` ma lo sostituisce con `color/material_grey_850`, che ha un valore di codice colore di `#ff303030`.
Per ulteriori informazioni sull'ereditarietà dello stile, vedere [stili e temi](https://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Selezione dei colori

Lo screenshot seguente illustra la **selezione colori**:

[Selezione colori![](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)

In questo esempio, il colore di **sfondo** può essere modificato in qualsiasi valore tramite vari modi:

- Facendo clic direttamente su un colore.
- Immissione dei valori di tonalità, saturazione e luminosità.
- Immissione di valori RGB (rosso, verde, blu) in decimali.
- Impostazione dell'alfa (opacità) per il colore selezionato.
- Immissione diretta del codice colore esadecimale.

Il colore scelto nella selezione colori *non* è limitato alle linee guida di progettazione del materiale o al set di risorse di colore disponibili.

### <a name="resources"></a>Risorse

La scheda **risorse** offre un elenco di risorse di colore già presenti nel tema:

[Risorse![](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

L'uso della scheda **risorse** vincola le scelte a questo elenco di colori. Tenere presente che se si sceglie una risorsa di colore già assegnata a un'altra parte del tema, due elementi adiacenti dell'interfaccia utente possono essere "eseguiti insieme" (poiché hanno lo stesso colore) e diventano difficili da distinguere con l'utente.

### <a name="material-palette"></a>Tavolozza materiali

La scheda **tavolozza materiale** apre la **tavolozza dei colori di progettazione materiale** descritta in [precedenza](#material-design-color-palette). La scelta di un valore di colore da questa tavolozza vincola la scelta del colore in modo che sia coerente con le linee guida di progettazione del materiale.

[Tavolozza materiale![](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

### <a name="creating-a-new-theme"></a>Creazione di un nuovo tema

Nell'esempio seguente verrà usata la tavolozza Material per creare un nuovo tema personalizzato. In primo luogo, il colore di **sfondo** verrà modificato in *blu 900*:

[![impostare lo sfondo su blu 900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

Quando viene modificata una risorsa di colore, viene visualizzato un messaggio con il messaggio, *il tema corrente contiene modifiche non salvate*:

[avviso modifiche non salvate![](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

La modifica del colore nella finestra di progettazione è stata apportata, ma questa modifica non è ancora stata salvata. A questo punto, è possibile eseguire una delle operazioni seguenti:

- Fare clic su Rimuovi **modifiche** per eliminare la nuova scelta di colore (o opzioni) e ripristinare lo stato originale del tema.

- Premere  **&#8984; + S** per salvare le modifiche apportate a un nuovo tema denominato **Custom**.

## <a name="summary"></a>Riepilogo

Questo argomento descrive le funzionalità di progettazione dei materiali disponibili in Novell. Android Designer. È stato illustrato come abilitare e configurare la griglia di progettazione materiale, come utilizzare la tavolozza dei colori di progettazione materiale per modificare le proprietà dei colori e come utilizzare il selettore di scala tipografico per configurare le proprietà di testo. È stato inoltre illustrato come utilizzare l'editor del tema per creare nuovi temi personalizzati conformi alle linee guida per la progettazione di materiali. Per ulteriori informazioni sul supporto di Novell. Android per la progettazione di materiali, vedere [Material Theme](~/android/user-interface/material-theme.md).

-----

## <a name="related-links"></a>Collegamenti correlati

- [Tema Material](~/android/user-interface/material-theme.md)
- [Introduzione alla progettazione del materiale](https://material.io/design/introduction)
