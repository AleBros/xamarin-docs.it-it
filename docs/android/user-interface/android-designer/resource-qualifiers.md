---
title: Qualificatori delle risorse e opzioni di visualizzazione
description: In questo argomento viene illustrato come definire le risorse che verranno utilizzate solo quando vengono confrontati alcuni valori di qualificatore. Un semplice esempio è una risorsa di stringa qualificata per il linguaggio. È possibile definire una risorsa di stringa come predefinita, con altre risorse alternative definite da usare per lingue aggiuntive. Tutti i tipi di risorse possono essere qualificati, incluso il layout stesso.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: ccac795333dff52d35a66e8cd205c7b5bbd1bb2c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029408"
---
# <a name="resource-qualifiers-and-visualization-options"></a>Qualificatori delle risorse e opzioni di visualizzazione

_In questo argomento viene illustrato come definire le risorse che verranno utilizzate solo quando vengono confrontati alcuni valori di qualificatore. Un semplice esempio è una risorsa di stringa qualificata per il linguaggio. È possibile definire una risorsa di stringa come predefinita, con altre risorse alternative definite da usare per lingue aggiuntive. Tutti i tipi di risorse possono essere qualificati, incluso il layout stesso._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="resource-qualifier-options"></a>Opzioni qualificatore risorse

È possibile accedere alle **Opzioni del qualificatore di risorsa** facendo clic sull'icona con i puntini di sospensione a destra del pulsante della modalità **orizzontale** :

[Opzioni del qualificatore della risorsa![](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

Questa finestra di dialogo presenta i menu a discesa per i qualificatori di risorse seguenti:

- **Lingua** &ndash; Visualizza le risorse della lingua disponibili e offre un'opzione per aggiungere nuove risorse di lingua/area geografica.

- La **modalità interfaccia utente** &ndash; elenca le modalità di visualizzazione (ad esempio, docking **auto** e **Dock desk**), nonché le direzioni di layout.

Ognuno di questi menu a discesa apre nuove finestre di dialogo in cui è possibile selezionare e configurare i qualificatori delle risorse (come illustrato di seguito).

### <a name="language"></a>Language

Nel menu a discesa **lingua** sono elencate solo le lingue per le quali sono definite risorse (o **tutte le lingue**, che è l'impostazione predefinita). Tuttavia, è disponibile anche un'opzione **Aggiungi lingua/area geografica** che consente di aggiungere un nuovo linguaggio all'elenco:

[![Aggiungi lingua/area geografica](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

Quando si fa clic su **Aggiungi lingua/area geografica...** , viene visualizzata la finestra di dialogo **Seleziona lingua** per visualizzare gli elenchi a discesa delle lingue e delle aree disponibili:

![Elenco di lingue](resource-qualifiers-images/vs/10-languages.png "Elenco di lingue")

In questo esempio abbiamo scelto **fr (French)** per la lingua e **be** (Belgium) per il dialetto regionale del francese. Si noti che il campo **Region** è facoltativo perché è possibile specificare molte lingue senza considerare aree specifiche. Quando il menu a discesa della **lingua** viene nuovamente aperto, viene visualizzata la risorsa lingua/area appena aggiunta:

![Lingua e area scelta](resource-qualifiers-images/vs/11-language-region-added.png "Lingua e area scelta")

Si noti che se si aggiunge una nuova lingua, ma non si creano nuove risorse, la lingua aggiunta non verrà più visualizzata alla successiva apertura del progetto.

### <a name="ui-mode"></a>Modalità interfaccia utente

Quando si fa clic sul menu a discesa **modalità interfaccia utente** , viene visualizzato un elenco di modalità, ad esempio **normale**, **docking auto**, **docking desk**, **televisione**, **dispositivo**ed espressione di **controllo**:

[menu modalità interfaccia utente![](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

Sotto questo elenco sono disponibili le modalità notturne **non notturne** e **notturne**, seguite dalle direzioni di layout da **sinistra a** destra e da **destra a sinistra** . per informazioni sulle opzioni da **sinistra a destra** e **da destra a sinistra** , vedere [ LayoutDirection](xref:Android.Util.LayoutDirection)).
Gli ultimi elementi della finestra di dialogo **Opzioni qualificatore risorse** sono le **schermate rotonde** (per l'uso con Android Wear) o **non le schermate rotonde**.
Per informazioni sulle schermate rotonde e non arrotondate, vedere [layout](https://developer.android.com/training/wearables/ui/layouts.html).
Per ulteriori informazioni sulle modalità di interfaccia utente di Android, vedere [UiModeManager](xref:Android.App.UiModeManager).

## <a name="action-bar-settings"></a>Impostazioni Barra delle azioni

L'icona **delle impostazioni della barra delle azioni** è disponibile a sinistra dell'icona del pennello (editor del tema):

![Impostazioni Barra delle azioni](resource-qualifiers-images/vs/14-action-bar.png "Impostazioni Barra delle azioni")

Questa icona consente di aprire una finestra di dialogo in cui è possibile selezionare una delle tre modalità Barra delle azioni:

- &ndash; **standard** è costituito da un logo o da un'icona e da un testo del titolo con un sottotitolo facoltativo.

- **Elenca** &ndash; modalità di navigazione elenco. Anziché il testo del titolo statico, in questa modalità viene visualizzato un menu di elenco per la navigazione all'interno dell'attività, ovvero può essere presentato all'utente come elenco a discesa.

- **Schede** &ndash; modalità di navigazione della scheda. Anziché il testo del titolo statico, questa modalità presenta una serie di schede per la navigazione all'interno dell'attività.

## <a name="themes"></a>Themes

Il menu a discesa **tema** Visualizza tutti i temi definiti nel progetto. Selezionando **altri temi** viene aperta una finestra di dialogo con un elenco di tutti i temi disponibili dal Android SDK installato, come illustrato di seguito:

[![Elenco di altri temi](resource-qualifiers-images/vs/15-theme-menu-sml.png "Elenco di altri temi")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

Quando viene selezionato un tema, il Area di progettazione viene aggiornato per mostrare l'effetto del nuovo tema. Si noti che questa modifica viene resa permanente solo se si fa clic sul pulsante **OK** nella finestra di dialogo del **tema** . Una volta selezionato, un tema verrà incluso nel menu a discesa del **tema** come illustrato di seguito:

![Il tema chiaro è ora disponibile](resource-qualifiers-images/vs/16-light-theme.png "Il tema chiaro è ora disponibile")

## <a name="android-version"></a>Versione di Android

Il selettore di **versione** di Android imposta la versione di Android usata per il rendering del layout nella finestra di progettazione. Il selettore Visualizza tutte le versioni compatibili con la versione del Framework di destinazione del progetto:

![Elenco delle versioni di Android](resource-qualifiers-images/vs/17-android-version.png "Elenco delle versioni di Android")

La versione del Framework di destinazione può essere impostata nelle impostazioni del progetto in **proprietà > applicazione > compilare usando la versione di Android**. Per altre informazioni sulla versione del Framework di destinazione, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).

Il set di widget disponibili nella casella degli strumenti è determinato dalla versione del Framework di destinazione del progetto. Questo vale anche per le proprietà disponibili nella **finestra Proprietà**. L'elenco dei widget disponibili *non* è determinato dal valore selezionato nel selettore di **versione** della barra degli strumenti. Ad esempio, se si imposta la versione di destinazione del progetto su Android 4,4, è comunque possibile selezionare Android 6,0 nel selettore di versione della barra degli strumenti per vedere il progetto simile in Android 6,0, ma non sarà possibile aggiungere widget specifici per Android 6,0 &ndash; l'utente w i widget disponibili in Android 4,4 sono comunque limitati.

Per altre informazioni sui tipi di risorse, vedere [risorse Android](~/android/app-fundamentals/resources-in-android/index.md).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="resource-qualifier-options"></a>Opzioni qualificatore risorse

È possibile accedere alle **Opzioni del qualificatore di risorsa** facendo clic sull'icona con i puntini di sospensione a destra del pulsante della modalità **orizzontale** :

[opzioni del qualificatore della risorsa![](resource-qualifiers-images/xs/08-resource-qual-opt-m75-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt-m75.png#lightbox)

Questa finestra di dialogo presenta i menu a discesa per i qualificatori di risorse seguenti:

- **Lingua** &ndash; Visualizza le risorse della lingua disponibili e offre un'opzione per aggiungere nuove risorse di lingua/area geografica.

- La **modalità interfaccia utente** &ndash; elenca le modalità di visualizzazione (ad esempio, docking **auto** e **Dock desk**), nonché le direzioni di layout.

Ognuno di questi menu a discesa apre nuove finestre di dialogo in cui è possibile selezionare e configurare i qualificatori delle risorse (come illustrato di seguito).

### <a name="language"></a>Language

Nel menu a discesa **lingua** sono elencate solo le lingue per le quali sono definite risorse (o **tutte le lingue**, che è l'impostazione predefinita). Tuttavia, è disponibile anche un'opzione **Aggiungi lingua/area geografica** che consente di aggiungere un nuovo linguaggio all'elenco:

[![Aggiungi lingua/area geografica](resource-qualifiers-images/xs/09-add-language-region-m75-sml.png)](resource-qualifiers-images/xs/09-add-language-region-m75.png#lightbox)

Quando si fa clic su **Aggiungi lingua/area geografica...** , viene visualizzata la finestra di dialogo **Seleziona lingua** per visualizzare gli elenchi a discesa delle lingue e delle aree disponibili:

[![elenco di lingue](resource-qualifiers-images/xs/10-languages-m75-sml.png)](resource-qualifiers-images/xs/10-languages-m75.png#lightbox)

In questo esempio abbiamo scelto **fr (French)** per la lingua e **be** (Belgium) per il dialetto regionale del francese. Si noti che il campo **Region** è facoltativo perché è possibile specificare molte lingue senza considerare aree specifiche. Quando il menu a discesa della **lingua** viene nuovamente aperto, viene visualizzata la risorsa lingua/area appena aggiunta:

[Lingua![e area scelta](resource-qualifiers-images/xs/11-language-region-added-m75-sml.png)](resource-qualifiers-images/xs/11-language-region-added-m75.png#lightbox)

Si noti che se si aggiunge una nuova lingua, ma non si creano nuove risorse, la lingua aggiunta non verrà più visualizzata alla successiva apertura del progetto.

### <a name="ui-mode"></a>Modalità interfaccia utente

Quando si fa clic sul menu a discesa **modalità interfaccia utente** , viene visualizzato un elenco di modalità, ad esempio **normale**, **docking auto**, **docking desk**, **televisione**, **dispositivo**ed espressione di **controllo**:

[menu modalità interfaccia utente![](resource-qualifiers-images/xs/12-ui-mode-m75-sml.png)](resource-qualifiers-images/xs/12-ui-mode-m75.png#lightbox)

Sotto questo elenco sono disponibili le modalità notturne **non notturne** e **notturne**, seguite dalle direzioni di layout da **sinistra a destra** e da **destra a sinistra**. L'ultima coppia di opzioni consente di selezionare **schermate rotonde** o **schermate rettangolari** (utili per i dispositivi Android Wear).

Per ulteriori informazioni sulle modalità di interfaccia utente di Android, vedere [UiModeManager](xref:Android.App.UiModeManager).
Per informazioni sulle opzioni **da sinistra a destra** e **da destra a sinistra** , vedere [LayoutDirection](xref:Android.Util.LayoutDirection).

## <a name="action-bar-settings"></a>Impostazioni Barra delle azioni

L'icona **delle impostazioni della barra delle azioni** è disponibile a sinistra dell'icona del pennello (editor del tema):

[Impostazioni Barra delle azioni![](resource-qualifiers-images/xs/13-action-bar-m75-sml.png)](resource-qualifiers-images/xs/13-action-bar-m75.png#lightbox)

Questa icona consente di aprire una finestra di dialogo in cui è possibile selezionare una delle tre modalità Barra delle azioni:

- &ndash; **standard** è costituito da un logo o da un'icona e da un testo del titolo con un sottotitolo facoltativo.

- **Elenca** &ndash; modalità di navigazione elenco. Anziché il testo del titolo statico, in questa modalità viene visualizzato un menu di elenco per la navigazione all'interno dell'attività, ovvero può essere presentato all'utente come elenco a discesa.

- **Schede** &ndash; modalità di navigazione della scheda. Anziché il testo del titolo statico, questa modalità presenta una serie di schede per la navigazione all'interno dell'attività.

## <a name="themes"></a>Themes

Il menu a discesa **tema** Visualizza tutti i temi definiti nel progetto. Selezionando **altri temi** viene aperta una finestra di dialogo con un elenco di tutti i temi disponibili dal Android SDK installato, come illustrato di seguito:

[![più elenco di temi](resource-qualifiers-images/xs/14-theme-menu-m75-sml.png)](resource-qualifiers-images/xs/14-theme-menu-m75.png#lightbox)

Quando viene selezionato un tema, il Area di progettazione viene aggiornato per mostrare l'effetto del nuovo tema. Si noti che questa modifica viene resa permanente solo se si fa clic sul pulsante **OK** nella finestra di dialogo del **tema** . Una volta selezionato, un tema verrà incluso nel menu a discesa del **tema** come illustrato di seguito:

[![tema chiaro è ora disponibile](resource-qualifiers-images/xs/15-light-theme-m75-sml.png)](resource-qualifiers-images/xs/15-light-theme-m75.png#lightbox)

## <a name="android-version"></a>Versione di Android

Il selettore di **versione** di Android imposta la versione di Android usata per il rendering del layout nella finestra di progettazione. Il selettore Visualizza tutte le versioni compatibili con la versione del Framework di destinazione del progetto:

[![elenco di versioni di Android](resource-qualifiers-images/xs/16-android-version-m75-sml.png)](resource-qualifiers-images/xs/16-android-version-m75.png#lightbox)

È possibile impostare la versione del Framework di destinazione nelle impostazioni del progetto nella sezione **Opzioni progetto > compila > generale** . Per altre informazioni sulla versione del Framework di destinazione, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).

Il set di widget disponibili nella casella degli strumenti è determinato dalla versione del Framework di destinazione del progetto. Questo vale anche per le proprietà disponibili nel **riquadro Proprietà**. L'elenco dei widget disponibili *non* è determinato dal valore selezionato nel selettore di **versione** della barra degli strumenti. Ad esempio, se si imposta la versione di destinazione del progetto su Android 4,4, è comunque possibile selezionare Android 6,0 nel selettore di versione della barra degli strumenti per vedere il progetto simile in Android 6,0, ma non sarà possibile aggiungere widget specifici per Android 6,0 &ndash; l'utente w i widget disponibili in Android 4,4 sono comunque limitati.

Per altre informazioni sui tipi di risorse, vedere [risorse Android](~/android/app-fundamentals/resources-in-android/index.md).

-----
