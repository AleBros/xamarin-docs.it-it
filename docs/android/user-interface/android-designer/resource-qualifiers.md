---
title: Qualificatori delle risorse e opzioni di visualizzazione
description: In questo argomento illustra come definire le risorse che verranno utilizzate solo quando vengono confrontati alcuni valori di qualificatore. Un semplice esempio è una risorsa di stringa completo di linguaggio. Una risorsa stringa può essere definita come impostazione predefinita, le altre risorse alternative definiti per essere usato per le lingue aggiuntive. Tutti i tipi di risorse possono essere qualificati, tra cui il layout se stesso.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/29/2018
ms.openlocfilehash: 7bc8c1066e557085c1bf34f77765edbb2259ba7a
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403299"
---
# <a name="resource-qualifiers-and-visualization-options"></a>Qualificatori delle risorse e opzioni di visualizzazione

_In questo argomento illustra come definire le risorse che verranno utilizzate solo quando vengono confrontati alcuni valori di qualificatore. Un semplice esempio è una risorsa di stringa completo di linguaggio. Una risorsa stringa può essere definita come impostazione predefinita, le altre risorse alternative definiti per essere usato per le lingue aggiuntive. Tutti i tipi di risorse possono essere qualificati, tra cui il layout se stesso._


## <a name="custom-device-configurations"></a>Configurazioni di dispositivo personalizzato

Android è disponibile in una vasta gamma di dispositivi e risoluzioni dello schermo.
Per progettare le interfacce utente destinate a più dispositivi, la finestra di progettazione include un'ampia gamma di configurazioni di dispositivi incorporati. Supporta anche l'aggiunta di configurazioni aggiuntive del dispositivo. Queste configurazioni si basano *qualificatori* specificata per distinguere una configurazione del dispositivo da un altro. Esistono molti tipi diversi di qualificatori. Per altre informazioni su questi tipi di risorse, vedere [risorse Android](~/android/app-fundamentals/resources-in-android/index.md).

Nella parte inferiore del selettore di dispositivo di menu è un **Personalizza** opzione come illustrato di seguito:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menu Selettore di dispositivo](resource-qualifiers-images/vs/01-device-selector-sml.png)](resource-qualifiers-images/vs/01-device-selector.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Menu Selettore di dispositivo](resource-qualifiers-images/xs/01-device-selector-sml.png)](resource-qualifiers-images/xs/01-device-selector.png#lightbox)

-----


Selezionando **Personalizza** Visualizza una finestra di dialogo che è possibile usare per esplorare le configurazioni dei dispositivi disponibili. Quando si sceglie la **definizioni di dispositivo** scheda, viene visualizzato un elenco di tutte le definizioni di dispositivo noto:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![AVD Manager](resource-qualifiers-images/vs/02-device-definitions-sml.png)](resource-qualifiers-images/vs/02-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![AVD Manager](resource-qualifiers-images/xs/02-device-definitions-sml.png)](resource-qualifiers-images/xs/02-device-definitions.png#lightbox)

-----


I dispositivi preconfigurati nella finestra di progettazione non possono essere modificati. Tuttavia, è possibile fare clic su **crea dispositivo...**  per definire una definizione di dispositivo personalizzata. In alternativa, è possibile selezionare una definizione esistente e fare clic su **Clone...**  usarlo come punto di partenza per una nuova definizione.
Ad esempio, se si seleziona il **Nexus 5** definizione e facendo clic su **Clone...**  viene visualizzata la finestra di dialogo seguente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Clona dispositivo](resource-qualifiers-images/vs/03-clone-sml.png)](resource-qualifiers-images/vs/03-clone.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Clona dispositivo](resource-qualifiers-images/xs/03-clone-sml.png)](resource-qualifiers-images/xs/03-clone.png#lightbox)

-----


Nel prossimo screenshot, il nome viene modificato in **Nexus 5 Custom** e vengono modificati i parametri del dispositivo per creare una nuova definizione di dispositivo personalizzato. In questo esempio **verticale** è disabilitato in modo che la definizione di dispositivo è di tipo solo landscape:

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


Si noti che ogni definizione di dispositivo creata dall'utente viene visualizzato con un'icona verde, come illustrato in precedenza. Quando si torna al **dispositivo** menu selettore, la nuova definizione di dispositivo personalizzato viene visualizzata nella parte superiore dell'elenco (se non viene visualizzata la configurazione del dispositivo personalizzate in questo elenco, provare a riavviare l'IDE):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizzato verrà visualizzato nell'elenco dei dispositivi](resource-qualifiers-images/vs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/vs/06-nexus-5-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Dispositivo personalizzato verrà visualizzato nell'elenco dei dispositivi](resource-qualifiers-images/xs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/xs/06-nexus-5-custom.png#lightbox)

-----


Selezione di questa configurazione del dispositivo consente di modificare il layout per garantire la conformità per le personalizzazioni create in precedenza (in questo caso, solo landscape modalità):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizzato in uso](resource-qualifiers-images/vs/07-custom-in-use-sml.png)](resource-qualifiers-images/vs/07-custom-in-use.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Dispositivo personalizzato in uso](resource-qualifiers-images/xs/07-custom-in-use-sml.png)](resource-qualifiers-images/xs/07-custom-in-use.png#lightbox)

-----



## <a name="resource-qualifier-options"></a>Opzioni per qualificatori di risorse

**Opzioni per qualificatori di risorse** sono accessibili facendo clic sui tre puntini a destra del **configurazione del dispositivo** opzioni:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Opzioni per qualificatori di risorse](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Opzioni per qualificatori di risorse](resource-qualifiers-images/xs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt.png#lightbox)

-----


Questa finestra di dialogo presenta i menu a discesa per i qualificatori di risorse seguenti:

-   **Linguaggio** &ndash; consente di visualizzare le risorse della lingua disponibili e offre un'opzione per aggiungere nuove risorse di lingua/area geografica.

-   **Modalità dell'interfaccia utente** &ndash; Elenca le modalità di visualizzazione (ad esempio **Dock per auto** e **Dock da scrivania**), nonché le direzioni di layout.

Ognuno di questi menu a discesa apre nuove finestre di dialogo in cui è possibile selezionare e configurare qualificatori delle risorse (come descritto di seguito).



### <a name="language"></a>Linguaggio

Il **Language** dal menu a discesa Elenca solo le lingue che dispone di risorse definite (o **tutte le lingue**, ovvero l'impostazione predefinita). Tuttavia, è inoltre disponibile un **Aggiungi lingua/area geografica...**  opzione che consente di aggiungere un nuovo linguaggio all'elenco:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Aggiungi lingua/area geografica](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Aggiungi lingua/area geografica](resource-qualifiers-images/xs/09-add-language-region-sml.png)](resource-qualifiers-images/xs/09-add-language-region.png#lightbox)

-----


Quando si fa clic **Aggiungi lingua/area geografica...** , il **Seleziona lingua** verrà visualizzata la finestra di dialogo per visualizzare gli elenchi a discesa delle lingue disponibili e le aree:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Elenco delle lingue](resource-qualifiers-images/vs/10-languages.png "elenco delle lingue")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Elenco delle lingue](resource-qualifiers-images/xs/10-languages-sml.png)](resource-qualifiers-images/xs/10-languages.png#lightbox)

-----


In questo esempio sono stati scelti **fr (francese)** per la lingua e **BE** (Belgio) per il sottolinguaggio regionale francese. Si noti che il **regione** campo è facoltativo perché molti linguaggi possono essere specificati senza tener conto di aree specifiche. Quando la **linguaggio** dal menu a discesa è aperto anche in questo caso, viene visualizzata la risorsa appena aggiunti lingua/area geografica:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Lingua e al paese scelto](resource-qualifiers-images/vs/11-language-region-added.png "lingua e al paese scelto")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Lingua e area geografica selezionata](resource-qualifiers-images/xs/11-language-region-added-sml.png)](resource-qualifiers-images/xs/11-language-region-added.png#lightbox)

-----


Si noti che se si aggiunge un nuovo linguaggio ma non è possibile creare nuove risorse per esso, il lingua aggiunta non verrà più visualizzato la volta successiva che si apre il progetto.



### <a name="ui-mode"></a>Modalità dell'interfaccia utente

Quando si fa clic il **modalità dell'interfaccia utente** dal menu a discesa un elenco delle modalità è riportato, ad esempio **Normal**, **Dock per auto**, **Dock da scrivania**, **Televisione**, **Appliance**, e **Watch**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menu di modalità dell'interfaccia utente](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

Di seguito in questo elenco sono le modalità di notte **non notte** e **notte**, seguito dalle direzioni di layout **da sinistra a destra** e **zprava Doleva** (per informazioni sulle **da sinistra a destra** e **zprava Doleva** opzioni, vedere [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).
L'ultimo elementi nel **opzioni per qualificatori di risorse** finestra di dialogo sono il **arrotondare le schermate** (per l'uso con Android Wear) o **Arrotonda schermate** (per informazioni su round e vedere le schermate non round [layout](https://developer.android.com/training/wearables/ui/layouts.html)).
Per altre informazioni sulle modalità di interfaccia utente Android, vedere [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Menu di modalità dell'interfaccia utente](resource-qualifiers-images/xs/12-ui-mode-sml.png)](resource-qualifiers-images/xs/12-ui-mode.png#lightbox)

Di seguito in questo elenco sono le modalità di notte **non notte** e **notte**, seguito dalle direzioni di layout **da sinistra a destra** e **zprava Doleva**. Per altre informazioni sulle modalità di interfaccia utente Android, vedere [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).
Per informazioni sulle **da sinistra a destra** e **zprava Doleva** opzioni, vedere [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).

### <a name="round-screen"></a>Schermo arrotondato

L'ultimo elemento nella **opzioni per qualificatori di risorse** finestra di dialogo è la **Round schermata** menu. Questo menu consente di selezionare uno **arrotondare schermate** (per l'uso con Android Wear) o **schermi rettangolari**:

[![Menu di schermata della funzione round](resource-qualifiers-images/xs/13-round-screen-sml.png)](resource-qualifiers-images/xs/13-round-screen.png#lightbox)

-----



## <a name="action-bar-settings"></a>Impostazioni della barra delle azioni

Il **Impostazioni barra azione** icona è disponibile a sinistra dell'icona del pennello (Editor del tema):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Le impostazioni della barra delle azioni](resource-qualifiers-images/vs/14-action-bar.png "impostazioni sulla barra delle azioni")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Impostazioni della barra delle azioni](resource-qualifiers-images/xs/13b-action-bar-sml.png)](resource-qualifiers-images/xs/13b-action-bar.png#lightbox)

-----


Questa icona viene visualizzata nel popover una finestra di dialogo che consente di selezionare una delle tre modalità barra delle azioni:

-   **Standard** &ndash; è costituito da un testo logo o icona e il titolo con un sottotitolo facoltativo.

-   **Elenco** &ndash; modalità di navigazione di elenco. Invece di testo del titolo statico, questa modalità presenta un menu elenco per la navigazione all'interno dell'attività (vale a dire, possa essere presentato all'utente come un elenco a discesa).

-   **Le schede** &ndash; la modalità di spostamento della scheda. Invece di testo del titolo statico, questa modalità presenta una serie di schede per la navigazione all'interno dell'attività.



## <a name="themes"></a>Themes

Il **tema** dal menu a discesa Visualizza tutti i temi definiti nel progetto. Selezionando **più temi** apre una finestra di dialogo con un elenco di tutti i temi disponibili da Android SDK installato, come illustrato di seguito:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Elenco dei temi ulteriori](resource-qualifiers-images/vs/15-theme-menu-sml.png "elenco più temi")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Elenco dei temi più](resource-qualifiers-images/xs/14-theme-menu-sml.png)](resource-qualifiers-images/xs/14-theme-menu.png#lightbox)

-----


Quando viene selezionato un tema, l'area di progettazione viene aggiornato per mostrare l'effetto del nuovo tema. Si noti che questa modifica verrà rese permanente solo se il **OK** si fa clic sul pulsante nel **tema** finestra di dialogo. Dopo aver selezionato un tema, verrà incluso nel **tema** menu elenco a discesa come illustrato sotto:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![È ora disponibile con tema chiaro](resource-qualifiers-images/vs/16-light-theme.png "è ora disponibile con tema chiaro")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![È ora disponibile con tema chiaro](resource-qualifiers-images/xs/15-light-theme-sml.png)](resource-qualifiers-images/xs/15-light-theme.png#lightbox)

-----



## <a name="android-version"></a>Versione di Android

Android **versione** selettore imposta la versione di Android che consente di eseguire il rendering del layout nella finestra di progettazione. Il selettore consente di visualizzare tutte le versioni compatibili con la versione del framework di destinazione del progetto:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Elenco delle versioni di Android](resource-qualifiers-images/vs/17-android-version.png "versioni di elenco di Android")

La versione di framework di destinazione può essere impostata nella sezione Impostazioni del progetto in **proprietà > applicazione > la compilazione con la versione di Android**. Per altre informazioni sulla versione di framework di destinazione, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md).

Il set di widget disponibile nella casella degli strumenti è determinato dalla versione di framework di destinazione del progetto. Questo vale anche per le proprietà disponibili nella **finestra proprietà**. L'elenco disponibile dei widget è *non* determinato dal valore selezionato nel **versione** selettore della barra degli strumenti. Ad esempio, se si imposta la versione di destinazione del progetto per Android 4.4, è comunque possibile selezionare Android 6.0 nel selettore di versione degli strumenti per visualizzare l'aspetto del progetto in Android 6.0, ma non sarà possibile aggiungere widget specifici ad Android 6.0 &ndash;  continuerà a essere limitato per i widget che sono disponibili in Android 4.4.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Elenco delle versioni di Android](resource-qualifiers-images/xs/16-android-version-sml.png)](resource-qualifiers-images/xs/16-android-version.png#lightbox)

La versione di framework di destinazione può essere impostata nella sezione Impostazioni del progetto nel **opzioni progetto > compilazione > Generale** sezione. Per altre informazioni sulla versione di framework di destinazione, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md).

Il set di widget disponibile nella casella degli strumenti è determinato dalla versione di framework di destinazione del progetto. Questo vale anche per le proprietà disponibili nella **Riempi proprietà**. L'elenco disponibile dei widget è *non* determinato dal valore selezionato nel **versione** selettore della barra degli strumenti. Ad esempio, se si imposta la versione di destinazione del progetto per Android 4.4, è comunque possibile selezionare Android 6.0 nel selettore di versione degli strumenti per visualizzare l'aspetto del progetto in Android 6.0, ma non sarà possibile aggiungere widget specifici ad Android 6.0 &ndash;  continuerà a essere limitato per i widget che sono disponibili in Android 4.4.

-----
