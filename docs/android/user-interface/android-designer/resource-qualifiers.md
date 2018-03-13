---
title: Qualificatori delle risorse e le opzioni di visualizzazione
description: "In questo argomento viene illustrato come definire le risorse che verranno utilizzate solo quando vengono confrontati alcuni valori di qualificatore. Un semplice esempio è una risorsa stringa qualificata language. Una risorsa stringa può essere definita come impostazione predefinita, con altre risorse alternative definite per essere utilizzato per lingue aggiuntive. Tutti i tipi di risorse possono essere qualificati, incluso il layout di se stesso."
ms.topic: article
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/29/2018
ms.openlocfilehash: bff6d917fc4ce65daed329f15d6648bbfe0dd069
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="resource-qualifiers-and-visualization-options"></a>Qualificatori delle risorse e le opzioni di visualizzazione

_In questo argomento viene illustrato come definire le risorse che verranno utilizzate solo quando vengono confrontati alcuni valori di qualificatore. Un semplice esempio è una risorsa stringa qualificata language. Una risorsa stringa può essere definita come impostazione predefinita, con altre risorse alternative definite per essere utilizzato per lingue aggiuntive. Tutti i tipi di risorse possono essere qualificati, incluso il layout di se stesso._


## <a name="custom-device-configurations"></a>Configurazioni di dispositivo personalizzata

Android è disponibile in un gran numero di dispositivi e risoluzioni dello schermo.
Per facilitare le interfacce utente di progettazione che molti dispositivi di destinazione, la finestra di progettazione viene fornito con diverse configurazioni di dispositivo incorporato. Supporta inoltre l'aggiunta di configurazioni aggiuntive del dispositivo. Queste configurazioni sono basate su *qualificatori* specificare per distinguere una configurazione del dispositivo da un altro. Esistono molti tipi diversi di qualificatori. Per ulteriori informazioni su questi tipi di risorse, vedere [risorse Android](~/android/app-fundamentals/resources-in-android/index.md).

Nella parte inferiore del selettore della periferica di menu è un **Personalizza** opzione come illustrato di seguito:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menu Selettore di dispositivo](resource-qualifiers-images/vs/01-device-selector-sml.png)](resource-qualifiers-images/vs/01-device-selector.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Menu Selettore di dispositivo](resource-qualifiers-images/xs/01-device-selector-sml.png)](resource-qualifiers-images/xs/01-device-selector.png#lightbox)

-----


Selezione **Personalizza** Visualizza una finestra di dialogo che è possibile utilizzare per esaminare le configurazioni dei dispositivi disponibili. Quando si fa clic il **dispositivo definizioni** scheda, viene visualizzato un elenco di tutte le definizioni di dispositivi noti:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![AVD Manager](resource-qualifiers-images/vs/02-device-definitions-sml.png)](resource-qualifiers-images/vs/02-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![AVD Manager](resource-qualifiers-images/xs/02-device-definitions-sml.png)](resource-qualifiers-images/xs/02-device-definitions.png#lightbox)

-----


Dispositivi preconfigurati nella finestra di progettazione non possono essere modificati. Tuttavia, è possibile fare clic su **crea dispositivo...**  per definire una definizione di dispositivo personalizzate. In alternativa, è possibile selezionare una definizione esistente e fare clic su **Clone...**  per utilizzarlo come punto di partenza per una nuova definizione.
Ad esempio, se si seleziona il **Nexus 5** definizione e facendo clic su **Clone...**  viene visualizzata la finestra di dialogo seguente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo di clone](resource-qualifiers-images/vs/03-clone-sml.png)](resource-qualifiers-images/vs/03-clone.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Dispositivo di clone](resource-qualifiers-images/xs/03-clone-sml.png)](resource-qualifiers-images/xs/03-clone.png#lightbox)

-----


Nella schermata successiva, il nome viene modificato in **Nexus 5 personalizzato** e i parametri del dispositivo sono stati modificati per creare una nuova definizione di dispositivo personalizzate. In questo esempio, **verticale** è disabilitato in modo che nella definizione del dispositivo è di tipo solo orizzontale:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizzato](resource-qualifiers-images/vs/04-custom-sml.png)](resource-qualifiers-images/vs/04-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Dispositivo personalizzato](resource-qualifiers-images/xs/04-custom-sml.png)](resource-qualifiers-images/xs/04-custom.png#lightbox)

-----


Fare clic su **Clone Device** (Clona dispositivo) per creare la nuova definizione di dispositivo, che viene visualizzata nell'elenco **Device Definitions** (Definizioni di dispositivo):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Definizioni di dispositivo aggiornati](resource-qualifiers-images/vs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/vs/05-updated-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Definizioni di dispositivo aggiornati](resource-qualifiers-images/xs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/xs/05-updated-device-definitions.png#lightbox)

-----


Si noti che ogni definizione di dispositivo creati dall'utente viene visualizzato con un'icona verde, come illustrato in precedenza. Quando si torna al **dispositivo** menu selettore, la nuova definizione di dispositivo personalizzata viene presentata nella sezione superiore dell'elenco (se non viene visualizzata la configurazione del dispositivo personalizzato in questo elenco, provare a riavviare l'IDE):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizzato viene visualizzato nell'elenco dei dispositivi](resource-qualifiers-images/vs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/vs/06-nexus-5-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Dispositivo personalizzato viene visualizzato nell'elenco dei dispositivi](resource-qualifiers-images/xs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/xs/06-nexus-5-custom.png#lightbox)

-----


Selezione di questa configurazione del dispositivo consente di modificare il layout per rispettare le personalizzazioni create in precedenza (in questo caso solo landscape modalità):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizzata in uso](resource-qualifiers-images/vs/07-custom-in-use-sml.png)](resource-qualifiers-images/vs/07-custom-in-use.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Dispositivo personalizzata in uso](resource-qualifiers-images/xs/07-custom-in-use-sml.png)](resource-qualifiers-images/xs/07-custom-in-use.png#lightbox)

-----



## <a name="resource-qualifier-options"></a>Opzioni qualificatore delle risorse

**Opzioni di qualificatore risorse** è possibile accedere facendo clic sull'icona triangolo verso il basso a destra del **configurazione del dispositivo** opzioni:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Opzioni qualificatore delle risorse](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Opzioni qualificatore delle risorse](resource-qualifiers-images/xs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt.png#lightbox)

-----


Questa finestra di dialogo presenta i menu a discesa per i qualificatori di risorse seguenti:

-   **Lingua** &ndash; consente di visualizzare le risorse della lingua disponibili e offre un'opzione per aggiungere nuove risorse di lingua/area geografica.

-   **Modalità interfaccia utente** &ndash; Elenca le modalità di visualizzazione (ad esempio **Car Dock** e **Dock tecnico**) nonché le direzioni di layout.

Ognuno di questi menu a discesa consente di aprire nuove finestre di dialogo in cui è possibile selezionare e configurare i qualificatori delle risorse (come descritto di seguito).



### <a name="language"></a>Linguaggio

Il **Language** menu a discesa sono elencate solo le lingue che dispongono di risorse definite (o **tutte le lingue**, ovvero l'impostazione predefinita). Tuttavia, è inoltre disponibile un **aggiungere language/area geografica...**  opzione che consente di aggiungere un nuovo linguaggio all'elenco:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Aggiungere language/area geografica](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Aggiungere language/area geografica](resource-qualifiers-images/xs/09-add-language-region-sml.png)](resource-qualifiers-images/xs/09-add-language-region.png#lightbox)

-----


Quando fa clic su **aggiungere language/area geografica...** , **Seleziona lingua** verrà visualizzata la finestra di dialogo per visualizzare gli elenchi a discesa delle lingue disponibili e delle aree:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Elenco delle lingue](resource-qualifiers-images/vs/10-languages.png "elenco delle lingue")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Elenco delle lingue](resource-qualifiers-images/xs/10-languages-sml.png)](resource-qualifiers-images/xs/10-languages.png#lightbox)

-----


In questo esempio, abbiamo scelto **fr (francese)** per la lingua e **BE** (Belgio) per il sottolinguaggio internazionale francese. Si noti che il **area** campo è facoltativo, poiché molte lingue possono essere specificate senza tener conto per aree specifiche. Quando il **Language** viene nuovamente aperta dal menu a discesa, ma visualizza la risorsa appena aggiunti language/area geografica:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Lingua e paese scelto](resource-qualifiers-images/vs/11-language-region-added.png "lingua e paese scelto")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Lingua e scelte di area](resource-qualifiers-images/xs/11-language-region-added-sml.png)](resource-qualifiers-images/xs/11-language-region-added.png#lightbox)

-----


Si noti che se si aggiunge un nuovo linguaggio ma non creare nuove risorse per questo, il lingua aggiunta non verrà più visualizzato la volta successiva che si apre il progetto.



### <a name="ui-mode"></a>Modalità interfaccia utente

Quando si fa clic il **modalità interfaccia utente** viene visualizzato un elenco delle modalità del menu a discesa, ad esempio **normale**, **Car Dock**, **Dock tecnico**, **Televisione**, **accessorio**, e **espressioni di controllo**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menu di modalità interfaccia utente](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

Di seguito in questo elenco sono le modalità di notte **non notte** e **notte**, seguito dalle direzioni di layout **da sinistra a destra** e **da destra a sinistra** (per informazioni su **da sinistra a destra** e **da destra a sinistra** opzioni, vedere [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).
L'ultimo gli elementi nel **Opzioni qualificatore delle risorse** finestra di dialogo sono il **arrotondare schermate** (per l'utilizzo con accenti Android) o **non arrotondare schermate** (per informazioni su round e non round schermate, vedere [layout](https://developer.android.com/training/wearables/ui/layouts.html)).
Per ulteriori informazioni sulle modalità di interfaccia utente di Android, vedere [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Menu di modalità interfaccia utente](resource-qualifiers-images/xs/12-ui-mode-sml.png)](resource-qualifiers-images/xs/12-ui-mode.png#lightbox)

Di seguito in questo elenco sono le modalità di notte **non notte** e **notte**, seguito dalle direzioni di layout **da sinistra a destra** e **da destra a sinistra**. Per ulteriori informazioni sulle modalità di interfaccia utente di Android, vedere [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).
Per informazioni su **da sinistra a destra** e **da destra a sinistra** opzioni, vedere [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).

### <a name="round-screen"></a>Schermata di arrotondamento

L'ultimo elemento di **Opzioni qualificatore delle risorse** finestra di dialogo il **Round schermata** menu. Questo menu consente di selezionare l'opzione **arrotondare schermate** (per l'utilizzo con accenti Android) o **schermate rettangolare**:

[![Menu dello schermo di arrotondamento](resource-qualifiers-images/xs/13-round-screen-sml.png)](resource-qualifiers-images/xs/13-round-screen.png#lightbox)

-----



## <a name="action-bar-settings"></a>Impostazioni della barra di azione

Il **le impostazioni della barra azione** icona è disponibile a sinistra dell'icona del pennello (Editor del tema):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Le impostazioni della barra di azione](resource-qualifiers-images/vs/14-action-bar.png "impostazioni sulla barra delle azioni")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Impostazioni della barra di azione](resource-qualifiers-images/xs/13b-action-bar-sml.png)](resource-qualifiers-images/xs/13b-action-bar.png#lightbox)

-----


Questa icona verrà visualizzata una popover finestra di dialogo che consente di selezionare una delle tre modalità barra delle azioni:

-   **Standard** &ndash; costituita da un testo logo o sull'icona e il titolo con un sottotitolo facoltativo.

-   **Elenco** &ndash; modalità di navigazione di elenco. Anziché il testo del titolo statico, questa modalità viene presentato un menu elenco per la navigazione all'interno dell'attività (ovvero, può essere presentata all'utente come un elenco a discesa).

-   **Schede** &ndash; modalità di navigazione della scheda. Invece di testo del titolo statico, questa modalità presenta una serie di schede per la navigazione all'interno dell'attività.



## <a name="themes"></a>Themes

Il **tema** dal menu a discesa Visualizza tutti i temi definiti nel progetto. Selezione **più temi** apre una finestra di dialogo con un elenco di tutti i temi disponibili da Android SDK installato, come illustrato di seguito:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Elenco di temi più](resource-qualifiers-images/vs/15-theme-menu-sml.png "più temi")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Elenco di più temi](resource-qualifiers-images/xs/14-theme-menu-sml.png)](resource-qualifiers-images/xs/14-theme-menu.png#lightbox)

-----


Quando si seleziona un tema, l'area di progettazione viene aggiornata per mostrare l'effetto del nuovo tema. Si noti che questa modifica verrà rese permanente solo se il **OK** pulsante nel **tema** finestra di dialogo. Dopo aver selezionato un tema, verranno inclusi nel **tema** menu di scelta rapida come mostrato di seguito:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Tema chiaro è ora disponibile](resource-qualifiers-images/vs/16-light-theme.png "tema chiaro è ora disponibile")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Tema chiaro è ora disponibile](resource-qualifiers-images/xs/15-light-theme-sml.png)](resource-qualifiers-images/xs/15-light-theme.png#lightbox)

-----



## <a name="android-version"></a>Versione di Android

Il Android **versione** selettore imposta la versione di Android che viene utilizzata per eseguire il rendering del layout nella finestra di progettazione. Il selettore consente di visualizzare tutte le versioni compatibili con la versione del framework di destinazione del progetto:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Elenco delle versioni Android](resource-qualifiers-images/vs/17-android-version.png "elenco Android versioni")

Impostare la versione del framework di destinazione per le impostazioni del progetto in **proprietà > applicazione > Compila con la versione Android**. Per ulteriori informazioni sulla versione di framework di destinazione, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).

Il set di widget disponibili nella casella degli strumenti è determinato dalla versione di framework di destinazione del progetto. Ciò vale anche per le proprietà disponibili nel **finestra proprietà**. L'elenco di widget disponibile è *non* determinato dal valore selezionato nel **versione** selettore della barra degli strumenti. Ad esempio, se si imposta la versione di destinazione del progetto per Android 4.4, è comunque possibile selezionare Android 6.0 nel selettore versione degli strumenti per visualizzare l'aspetto del progetto in Android 6.0, ma non sarà possibile aggiungere widget che sono specifici di Android 6.0 &ndash;  sarà comunque possibile solo per i widget predefiniti disponibili in Android 4.4.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Elenco delle versioni di Android](resource-qualifiers-images/xs/16-android-version-sml.png)](resource-qualifiers-images/xs/16-android-version.png#lightbox)

Impostare la versione del framework di destinazione nelle impostazioni del progetto sotto il **opzioni progetto > compilare > Generale** sezione. Per ulteriori informazioni sulla versione di framework di destinazione, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).

Il set di widget disponibili nella casella degli strumenti è determinato dalla versione di framework di destinazione del progetto. Ciò vale anche per le proprietà disponibili nel **proprietà riempimento**. L'elenco di widget disponibile è *non* determinato dal valore selezionato nel **versione** selettore della barra degli strumenti. Ad esempio, se si imposta la versione di destinazione del progetto per Android 4.4, è comunque possibile selezionare Android 6.0 nel selettore versione degli strumenti per visualizzare l'aspetto del progetto in Android 6.0, ma non sarà possibile aggiungere widget che sono specifici di Android 6.0 &ndash;  sarà comunque possibile solo per i widget predefiniti disponibili in Android 4.4.

-----
