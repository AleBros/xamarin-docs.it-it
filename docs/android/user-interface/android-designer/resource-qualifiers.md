---
title: Qualificatori delle risorse e opzioni di visualizzazione
description: In questo argomento illustra come definire le risorse che verranno utilizzate solo quando vengono confrontati alcuni valori di qualificatore. Un semplice esempio è una risorsa di stringa completo di linguaggio. Una risorsa stringa può essere definita come impostazione predefinita, le altre risorse alternative definiti per essere usato per le lingue aggiuntive. Tutti i tipi di risorse possono essere qualificati, tra cui il layout se stesso.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 9d99e6a59b57b59d585b32befdadc0890d41448c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115633"
---
# <a name="resource-qualifiers-and-visualization-options"></a>Qualificatori delle risorse e opzioni di visualizzazione

_In questo argomento illustra come definire le risorse che verranno utilizzate solo quando vengono confrontati alcuni valori di qualificatore. Un semplice esempio è una risorsa di stringa completo di linguaggio. Una risorsa stringa può essere definita come impostazione predefinita, le altre risorse alternative definiti per essere usato per le lingue aggiuntive. Tutti i tipi di risorse possono essere qualificati, tra cui il layout se stesso._


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="resource-qualifier-options"></a>Opzioni per qualificatori di risorse

**Opzioni per qualificatori di risorse** sono accessibili facendo clic sull'icona con puntini di sospensione a destra del **panorama** pulsante modalità:

[![Opzioni per qualificatori di risorse](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

Questa finestra di dialogo presenta i menu a discesa per i qualificatori di risorse seguenti:

-   **Linguaggio** &ndash; consente di visualizzare le risorse della lingua disponibili e offre un'opzione per aggiungere nuove risorse di lingua/area geografica.

-   **Modalità dell'interfaccia utente** &ndash; Elenca le modalità di visualizzazione (ad esempio **Dock per auto** e **Dock da scrivania**), nonché le direzioni di layout.

Ognuno di questi menu a discesa apre nuove finestre di dialogo in cui è possibile selezionare e configurare qualificatori delle risorse (come descritto di seguito).

### <a name="language"></a>Linguaggio

Il **Language** dal menu a discesa Elenca solo le lingue che dispone di risorse definite (o **tutte le lingue**, ovvero l'impostazione predefinita). Tuttavia, è inoltre disponibile un **Aggiungi lingua/area geografica...**  opzione che consente di aggiungere un nuovo linguaggio all'elenco:

[![Aggiungi lingua/area geografica](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

Quando si fa clic **Aggiungi lingua/area geografica...** , il **Seleziona lingua** verrà visualizzata la finestra di dialogo per visualizzare gli elenchi a discesa delle lingue disponibili e le aree:

![Elenco delle lingue](resource-qualifiers-images/vs/10-languages.png "elenco delle lingue")

In questo esempio sono stati scelti **fr (francese)** per la lingua e **BE** (Belgio) per il sottolinguaggio regionale francese. Si noti che il **regione** campo è facoltativo perché molti linguaggi possono essere specificati senza tener conto di aree specifiche. Quando la **linguaggio** dal menu a discesa è aperto anche in questo caso, viene visualizzata la risorsa appena aggiunti lingua/area geografica:

![Lingua e al paese scelto](resource-qualifiers-images/vs/11-language-region-added.png "lingua e al paese scelto")

Si noti che se si aggiunge un nuovo linguaggio ma non è possibile creare nuove risorse per esso, il lingua aggiunta non verrà più visualizzato la volta successiva che si apre il progetto.

### <a name="ui-mode"></a>Modalità dell'interfaccia utente

Quando si fa clic il **modalità dell'interfaccia utente** dal menu a discesa un elenco delle modalità è riportato, ad esempio **Normal**, **Dock per auto**, **Dock da scrivania**, **Televisione**, **Appliance**, e **Watch**:


[![Menu di modalità dell'interfaccia utente](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

Di seguito in questo elenco sono le modalità di notte **non notte** e **notte**, seguito dalle direzioni di layout **da sinistra a destra** e **zprava Doleva** (per informazioni sulle **da sinistra a destra** e **zprava Doleva** opzioni, vedere [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/)).
L'ultimo elementi nel **opzioni per qualificatori di risorse** finestra di dialogo sono le **arrotondare le schermate** (per l'uso con Android Wear) o **schermate non arrotondato**.
Per informazioni sulla funzione round e non round schermate, vedere [layout](https://developer.android.com/training/wearables/ui/layouts.html).
Per altre informazioni sulle modalità di interfaccia utente Android, vedere [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).

## <a name="action-bar-settings"></a>Impostazioni della barra delle azioni

Il **Impostazioni barra azione** icona è disponibile a sinistra dell'icona del pennello (Editor del tema):

![Le impostazioni della barra delle azioni](resource-qualifiers-images/vs/14-action-bar.png "impostazioni sulla barra delle azioni")

Questa icona viene visualizzata nel popover una finestra di dialogo che consente di selezionare una delle tre modalità barra delle azioni:

-   **Standard** &ndash; è costituito da un testo icona e il titolo con un sottotitolo facoltativo o un logo.

-   **Elenco** &ndash; modalità di navigazione di elenco. Invece di testo del titolo statico, questa modalità presenta un menu elenco per la navigazione all'interno dell'attività (vale a dire, possa essere presentato all'utente come un elenco a discesa).

-   **Le schede** &ndash; la modalità di spostamento della scheda. Invece di testo del titolo statico, questa modalità presenta una serie di schede per la navigazione all'interno dell'attività.

## <a name="themes"></a>Themes

Il **tema** dal menu a discesa Visualizza tutti i temi definiti nel progetto. Selezionando **più temi** apre una finestra di dialogo con un elenco di tutti i temi disponibili da Android SDK installato, come illustrato di seguito:

[![Elenco dei temi ulteriori](resource-qualifiers-images/vs/15-theme-menu-sml.png "elenco più temi")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

Quando viene selezionato un tema, l'area di progettazione viene aggiornato per mostrare l'effetto del nuovo tema. Si noti che questa modifica verrà rese permanente solo se il **OK** si fa clic sul pulsante nel **tema** finestra di dialogo. Dopo aver selezionato un tema, verrà incluso nel **tema** menu elenco a discesa come illustrato sotto:

![È ora disponibile con tema chiaro](resource-qualifiers-images/vs/16-light-theme.png "è ora disponibile con tema chiaro")

## <a name="android-version"></a>Versione di Android

Android **versione** selettore imposta la versione di Android che consente di eseguire il rendering del layout nella finestra di progettazione. Il selettore consente di visualizzare tutte le versioni compatibili con la versione del framework di destinazione del progetto:

![Elenco delle versioni di Android](resource-qualifiers-images/vs/17-android-version.png "versioni di elenco di Android")

La versione di framework di destinazione può essere impostata nella sezione Impostazioni del progetto in **proprietà > applicazione > la compilazione con la versione di Android**. Per altre informazioni sulla versione di framework di destinazione, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md).

Il set di widget disponibile nella casella degli strumenti è determinato dalla versione di framework di destinazione del progetto. Questo vale anche per le proprietà disponibili nella **finestra proprietà**. L'elenco disponibile dei widget è *non* determinato dal valore selezionato nel **versione** selettore della barra degli strumenti. Ad esempio, se si imposta la versione di destinazione del progetto per Android 4.4, è comunque possibile selezionare Android 6.0 nel selettore di versione degli strumenti per visualizzare l'aspetto del progetto in Android 6.0, ma non sarà possibile aggiungere widget specifici ad Android 6.0 &ndash;  continuerà a essere limitato per i widget che sono disponibili in Android 4.4.

Per altre informazioni sui tipi di risorse, vedere [risorse Android](~/android/app-fundamentals/resources-in-android/index.md).



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="resource-qualifier-options"></a>Opzioni per qualificatori di risorse

**Opzioni per qualificatori di risorse** sono accessibili facendo clic sull'icona con puntini di sospensione a destra del **panorama** pulsante modalità:

[![Opzioni per qualificatori di risorse](resource-qualifiers-images/xs/08-resource-qual-opt-m75-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt-m75.png#lightbox)

Questa finestra di dialogo presenta i menu a discesa per i qualificatori di risorse seguenti:

-   **Linguaggio** &ndash; consente di visualizzare le risorse della lingua disponibili e offre un'opzione per aggiungere nuove risorse di lingua/area geografica.

-   **Modalità dell'interfaccia utente** &ndash; Elenca le modalità di visualizzazione (ad esempio **Dock per auto** e **Dock da scrivania**), nonché le direzioni di layout.

Ognuno di questi menu a discesa apre nuove finestre di dialogo in cui è possibile selezionare e configurare qualificatori delle risorse (come descritto di seguito).

### <a name="language"></a>Linguaggio

Il **Language** dal menu a discesa Elenca solo le lingue che dispone di risorse definite (o **tutte le lingue**, ovvero l'impostazione predefinita). Tuttavia, è inoltre disponibile un **Aggiungi lingua/area geografica...**  opzione che consente di aggiungere un nuovo linguaggio all'elenco:

[![Aggiungi lingua/area geografica](resource-qualifiers-images/xs/09-add-language-region-m75-sml.png)](resource-qualifiers-images/xs/09-add-language-region-m75.png#lightbox)

Quando si fa clic **Aggiungi lingua/area geografica...** , il **Seleziona lingua** verrà visualizzata la finestra di dialogo per visualizzare gli elenchi a discesa delle lingue disponibili e le aree:

[![Elenco delle lingue](resource-qualifiers-images/xs/10-languages-m75-sml.png)](resource-qualifiers-images/xs/10-languages-m75.png#lightbox)

In questo esempio sono stati scelti **fr (francese)** per la lingua e **BE** (Belgio) per il sottolinguaggio regionale francese. Si noti che il **regione** campo è facoltativo perché molti linguaggi possono essere specificati senza tener conto di aree specifiche. Quando la **linguaggio** dal menu a discesa è aperto anche in questo caso, viene visualizzata la risorsa appena aggiunti lingua/area geografica:

[![Lingua e area geografica selezionata](resource-qualifiers-images/xs/11-language-region-added-m75-sml.png)](resource-qualifiers-images/xs/11-language-region-added-m75.png#lightbox)

Si noti che se si aggiunge un nuovo linguaggio ma non è possibile creare nuove risorse per esso, il lingua aggiunta non verrà più visualizzato la volta successiva che si apre il progetto.

### <a name="ui-mode"></a>Modalità dell'interfaccia utente

Quando si fa clic il **modalità dell'interfaccia utente** dal menu a discesa un elenco delle modalità è riportato, ad esempio **Normal**, **Dock per auto**, **Dock da scrivania**, **Televisione**, **Appliance**, e **Watch**:

[![Menu di modalità dell'interfaccia utente](resource-qualifiers-images/xs/12-ui-mode-m75-sml.png)](resource-qualifiers-images/xs/12-ui-mode-m75.png#lightbox)

Di seguito in questo elenco sono le modalità di notte **non notte** e **notte**, seguito dalle direzioni di layout **da sinistra a destra** e **zprava Doleva**. L'ultima coppia di opzioni consente di selezionare uno **arrotondare schermate** oppure **schermi rettangolari** (utile per i dispositivi Android Wear).

Per altre informazioni sulle modalità di interfaccia utente Android, vedere [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).
Per informazioni sulle **da sinistra a destra** e **zprava Doleva** opzioni, vedere [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).


## <a name="action-bar-settings"></a>Impostazioni della barra delle azioni

Il **Impostazioni barra azione** icona è disponibile a sinistra dell'icona del pennello (Editor del tema):

[![Impostazioni della barra delle azioni](resource-qualifiers-images/xs/13-action-bar-m75-sml.png)](resource-qualifiers-images/xs/13-action-bar-m75.png#lightbox)

Questa icona viene visualizzata nel popover una finestra di dialogo che consente di selezionare una delle tre modalità barra delle azioni:

-   **Standard** &ndash; è costituito da un testo logo o icona e il titolo con un sottotitolo facoltativo.

-   **Elenco** &ndash; modalità di navigazione di elenco. Invece di testo del titolo statico, questa modalità presenta un menu elenco per la navigazione all'interno dell'attività (vale a dire, possa essere presentato all'utente come un elenco a discesa).

-   **Le schede** &ndash; la modalità di spostamento della scheda. Invece di testo del titolo statico, questa modalità presenta una serie di schede per la navigazione all'interno dell'attività.

## <a name="themes"></a>Themes

Il **tema** dal menu a discesa Visualizza tutti i temi definiti nel progetto. Selezionando **più temi** apre una finestra di dialogo con un elenco di tutti i temi disponibili da Android SDK installato, come illustrato di seguito:

[![Elenco dei temi più](resource-qualifiers-images/xs/14-theme-menu-m75-sml.png)](resource-qualifiers-images/xs/14-theme-menu-m75.png#lightbox)

Quando viene selezionato un tema, l'area di progettazione viene aggiornato per mostrare l'effetto del nuovo tema. Si noti che questa modifica verrà rese permanente solo se il **OK** si fa clic sul pulsante nel **tema** finestra di dialogo. Dopo aver selezionato un tema, verrà incluso nel **tema** menu elenco a discesa come illustrato sotto:

[![È ora disponibile con tema chiaro](resource-qualifiers-images/xs/15-light-theme-m75-sml.png)](resource-qualifiers-images/xs/15-light-theme-m75.png#lightbox)

## <a name="android-version"></a>Versione di Android

Android **versione** selettore imposta la versione di Android che consente di eseguire il rendering del layout nella finestra di progettazione. Il selettore consente di visualizzare tutte le versioni compatibili con la versione del framework di destinazione del progetto:

[![Elenco delle versioni di Android](resource-qualifiers-images/xs/16-android-version-m75-sml.png)](resource-qualifiers-images/xs/16-android-version-m75.png#lightbox)

La versione di framework di destinazione può essere impostata nella sezione Impostazioni del progetto nel **opzioni progetto > compilazione > Generale** sezione. Per altre informazioni sulla versione di framework di destinazione, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md).

Il set di widget disponibile nella casella degli strumenti è determinato dalla versione di framework di destinazione del progetto. Questo vale anche per le proprietà disponibili nella **Riempi proprietà**. L'elenco disponibile dei widget è *non* determinato dal valore selezionato nel **versione** selettore della barra degli strumenti. Ad esempio, se si imposta la versione di destinazione del progetto per Android 4.4, è comunque possibile selezionare Android 6.0 nel selettore di versione degli strumenti per visualizzare l'aspetto del progetto in Android 6.0, ma non sarà possibile aggiungere widget specifici ad Android 6.0 &ndash;  continuerà a essere limitato per i widget che sono disponibili in Android 4.4.

Per altre informazioni sui tipi di risorse, vedere [risorse Android](~/android/app-fundamentals/resources-in-android/index.md).

-----
