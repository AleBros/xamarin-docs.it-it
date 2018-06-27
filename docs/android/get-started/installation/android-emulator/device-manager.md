---
title: Gestione di dispositivi virtuali con Android Device Manager
description: Questo articolo spiega come usare Android Device Manager per creare e configurare dispositivi virtuali Android (AVD) che emulano i dispositivi fisici Android. È possibile usare questi dispositivi virtuali per eseguire e testare l'app anche senza un dispositivo fisico.
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/02/2018
ms.openlocfilehash: 888f126d3e58b0300ba7ce3ad1cb5a8001fc545a
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34733588"
---
# <a name="managing-virtual-devices-with-the-android-device-manager"></a>Gestione di dispositivi virtuali con Android Device Manager

_Questo articolo spiega come usare Android Device Manager per creare e configurare dispositivi virtuali Android (AVD) che emulano i dispositivi fisici Android. È possibile usare questi dispositivi virtuali per eseguire e testare l'app anche senza un dispositivo fisico._

## <a name="overview"></a>Panoramica

Dopo la verifica dell'abilitazione dell'accelerazione hardware (come descritto in [Accelerazione hardware per le prestazioni dell'emulatore](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), il passaggio successivo consiste nell'usare _Android Device Manager_ (chiamato anche _Xamarin Android Device Manager_) per la creazione di dispositivi virtuali per il test e il debug dell'app.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Questo articolo spiega come usare Android Device Manager per creare, duplicare, personalizzare e avviare i dispositivi virtuali Android.

[![Screenshot di Android Device Manager nella scheda Dispositivi](device-manager-images/win/01-devices-dialog-sml.png)](device-manager-images/win/01-devices-dialog.png#lightbox)

È possibile usare Android Device Manager per creare e configurare _dispositivi virtuali Android_ (AVD) eseguibili nell'[emulatore Android di Google](~/android/deploy-test/debugging/android-sdk-emulator/index.md).
Ogni AVD è una configurazione dell'emulatore che simula un dispositivo Android fisico. In questo modo è possibile eseguire e sottoporre a test l'applicazione in varie configurazioni che simulano dispositivi Android fisici diversi.

## <a name="requirements"></a>Requisiti

Per usare Android Device Manager, sono necessari i componenti seguenti:

-   È necessario Visual Studio 2017 versione 15.7 o versione successiva. Sono supportate le edizioni Visual Studio Community, Professional ed Enterprise.

-   Strumenti di Visual Studio per Xamarin 4.9 o versione successiva.

-   Devono essere installati Android SDK (vedere [Configurazione di Android SDK per Xamarin.Android](~/android/get-started/installation/android-sdk.md)) e SDK Tools 26.1.1 o versione successiva, come descritto nella sezione seguente. Verificare di installare Android SDK nel seguente percorso (se non è già installato): **C:\\Programmi (x86)\\Android\\android-sdk**.


## <a name="launching-the-device-manager"></a>Avvio di Gestione dispositivi

Avviare Android Device Manager dal menu **Strumenti** facendo clic su **Strumenti > Android > Android Device Manager**:

[![Avvio dal menu Strumenti](device-manager-images/win/04-tools-menu-sml.png)](device-manager-images/win/04-tools-menu.png#lightbox)

Se all'avvio viene visualizzato il messaggio di errore seguente, vedere [Risoluzione dei problemi di configurazione dell'emulatore](~/android/get-started/installation/android-emulator/troubleshooting.md) per informazioni sulle soluzioni alternative:

![Errore di istanza di Android SDK](device-manager-images/win/32-sdk-error.png)

Per usare Android Device Manager, è necessario installare Android SDK Tools 26.1.1 o versione successiva. Se non è installato Android SDK Tools 26.1.1 o versione successiva, all'avvio potrebbe essere visualizzato il messaggio di errore seguente:

![Messaggio di errore di istanza di Android SDK](device-manager-images/win/02-sdk-instance-error.png)

Se viene visualizzato questo messaggio di errore, fare clic su **Apri SDK Manager** per aprire Android SDK Manager. In Android SDK Manager fare clic sulla scheda **Tools** (Strumenti) e installare i componenti seguenti:

-   **Android SDK Tools 26.1.1** o versione successiva 
-   **Android SDK Platform-Tools 27.0.1** o versione successiva  
-   **Android SDK Build-Tools 27.0.3** o versione successiva

Questi pacchetti devono essere visualizzati con lo stato **Installed** (Installato) come illustrato nello screenshot seguente:

[![Installazione di Android SDK Tools 27.0](device-manager-images/win/03-sdk-tools-sml.png)](device-manager-images/win/03-sdk-tools.png#lightbox)

Dopo aver installato questi pacchetti, chiudere SDK Manager e riavviare Gestione dispositivi di Xamarin Android.

## <a name="main-screen"></a>Schermata principale

Al primo avvio di Gestione dispositivi di Xamarin Android una schermata visualizza tutti i dispositivi virtuali attualmente configurati. Per ogni dispositivo vengono visualizzati i parametri **Nome**, **Sistema operativo** (livello dell'API Android), **CPU**, **Memoria** e la risoluzione dello schermo:

[![Elenco dei dispositivi installati e relativi parametri](device-manager-images/win/05-installed-list-sml.png)](device-manager-images/win/05-installed-list.png#lightbox)

Quando si fa clic su un dispositivo nell'elenco, a destra del nome viene visualizzato il pulsante **Avvia**. Fare clic su **Avvia** per avviare l'emulatore con questo dispositivo virtuale:

[![Pulsante Avvia per un'immagine del dispositivo](device-manager-images/win/06-start-button-sml.png)](device-manager-images/win/06-start-button.png#lightbox)

Dopo che l'emulatore viene avviato con il dispositivo virtuale selezionato, il pulsante **Avvia** diventa **Arresta** e può essere usato per interrompere l'esecuzione dell'emulatore:

[![Pulsante Arresta per il dispositivo in esecuzione](device-manager-images/win/07-stop-button-sml.png)](device-manager-images/win/07-stop-button.png#lightbox)

### <a name="new-device"></a>Nuovo dispositivo

Per creare un nuovo dispositivo fare clic sul pulsante **Nuovo** nell'angolo superiore destro della schermata:

[![Pulsante Nuovo per creare un nuovo dispositivo](device-manager-images/win/08-new-button-sml.png)](device-manager-images/win/08-new-button.png#lightbox)

Quando si fa clic su **Nuovo** viene visualizzata la schermata **New Device** (Nuovo dispositivo):

[![Schermata New Device (Nuovo dispositivo) di Gestione dispositivi](device-manager-images/win/09-new-device-editor-sml.png)](device-manager-images/win/09-new-device-editor.png#lightbox)

Per configurare un nuovo dispositivo nella schermata **New Device** (Nuovo dispositivo) seguire questa procedura:

1. Selezionare un dispositivo fisico da emulare facendo clic sul menu a discesa **Dispositivo**:

    [![Menu a discesa Dispositivo](device-manager-images/win/10-device-menu-sml.png)](device-manager-images/win/10-device-menu.png#lightbox)

2. Selezionare un'immagine del sistema da usare con questo dispositivo virtuale facendo clic sul menu a discesa **System image** (Immagine di sistema). In questo menu le immagini di sistema di Device Manager installate vengono visualizzate sotto **Installed** (Installato). La sezione **Download** elenca le immagini di sistema di Device Manager che non sono attualmente disponibili nel computer di sviluppo, ma possono essere installate automaticamente:

    [![Menu a discesa System image (Immagine di sistema)](device-manager-images/win/11-system-image-menu-sml.png)](device-manager-images/win/11-system-image-menu.png#lightbox)

3. Assegnare un nuovo nome al dispositivo. Nell'esempio seguente, al nuovo dispositivo viene assegnato il nome **Nexus 5 API 25**:

    [![Assegnazione di un nome al nuovo dispositivo](device-manager-images/win/12-device-name-sml.png)](device-manager-images/win/12-device-name.png#lightbox)

4. Modificare le proprietà in base alle esigenze. Per apportare modifiche alle proprietà, vedere [Modifica delle proprietà del dispositivo virtuale Android](~/android/get-started/installation/android-emulator/device-properties.md).

5. Aggiungere le eventuali proprietà addizionali che è necessario impostare in modo esplicito. La schermata **New Device** (Nuovo dispositivo) elenca solo le proprietà modificate più di frequente. Per aggiungere altre proprietà fare clic sul menu a discesa **Aggiungi proprietà** nell'angolo inferiore sinistro della schermata. Nell'esempio seguente viene aggiunta la proprietà `hw.lcd.backlight`:

    [![Menu a discesa Aggiungi proprietà](device-manager-images/win/13-add-property-menu-sml.png)](device-manager-images/win/13-add-property-menu.png#lightbox)

6. Fare clic sul pulsante **Crea** nell'angolo inferiore destro per creare il nuovo dispositivo:

    ![Pulsante Crea](device-manager-images/win/14-create-button.png)

7. È possibile che venga visualizzata una schermata **Accettazione della licenza**. Se si accettano le condizioni di licenza, fare clic su **Accetta**:

    ![Schermata Accettazione della licenza](device-manager-images/win/15-license-acceptance.png)

8. Android Device Manager aggiunge il nuovo dispositivo all'elenco dei dispositivi virtuali installati, visualizzando un indicatore di stato **Creating** (Creazione in corso) durante la creazione del dispositivo:

    [![Indicatore di stato Creazione in corso](device-manager-images/win/16-creating-the-device-sml.png)](device-manager-images/win/16-creating-the-device.png#lightbox)

9. Dopo aver completato il processo di creazione, il nuovo dispositivo viene visualizzato nell'elenco di dispositivi virtuali installati, con un pulsante **Avvia** che consente di avviarlo:

   [![Dispositivo appena creato pronto per l'avvio](device-manager-images/win/17-created-device-sml.png)](device-manager-images/win/17-created-device.png#lightbox)


### <a name="edit-device"></a>Modificare il dispositivo

Per modificare un dispositivo virtuale esistente, selezionare il dispositivo e fare clic sul pulsante **Modifica** nell'angolo superiore destro dello schermo:

[![Pulsante Modifica per modificare un nuovo dispositivo](device-manager-images/win/19-edit-button-sml.png)](device-manager-images/win/19-edit-button.png#lightbox)

Quando si fa clic su **Modifica** viene avviata la schermata Device Editor (Editor dispositivi) per il dispositivo virtuale selezionato:

[![Schermata Device Editor (Editor dispositivi)](device-manager-images/win/20-device-editor-sml.png)](device-manager-images/win/20-device-editor.png#lightbox)

La schermata **Device Editor** (Editor dispositivi) elenca le proprietà del dispositivo virtuale nella prima colonna e i valori corrispondenti di ogni proprietà nella seconda colonna. Quando si seleziona una proprietà, sul lato destro viene visualizzata una descrizione dettagliata di tale proprietà.

Ad esempio, nello screenshot seguente il valore della proprietà `hw.lcd.density` viene modificato da **420** a **240**:

[![Esempio di modifica dispositivo](device-manager-images/win/21-device-editing-sml.png)](device-manager-images/win/21-device-editing.png#lightbox)

Dopo aver apportato le modifiche di configurazione necessarie, fare clic sul pulsante **Salva**.
Per altre informazioni sulla modifica delle proprietà del dispositivo virtuale, vedere [Modifica delle proprietà del dispositivo virtuale Android](~/android/get-started/installation/android-emulator/device-properties.md).

 
### <a name="additional-options"></a>Opzioni aggiuntive

Opzioni aggiuntive per l'uso dei dispositivi sono disponibili nel menu &hellip; nell'angolo superiore destro della schermata:

[![Posizione del menu delle opzioni aggiuntive](device-manager-images/win/22-overflow-menu-sml.png)](device-manager-images/win/22-overflow-menu.png#lightbox)

Il menu delle opzioni aggiuntive contiene le voci seguenti:

-   **Duplicate and Edit** (Duplica e modifica): duplica il dispositivo selezionato attualmente e lo apre nella schermata **New Device** (Nuovo dispositivo) con un nome univoco diverso. Ad esempio, se si seleziona **VisualStudio_android 23_x86_phone** e si fa clic su **Duplicate and Edit** (Duplica e modifica) viene accodato un contatore al nome del dispositivo:

    [![Schermata Duplicate and Edit (Duplica e modifica)](device-manager-images/win/23-dupe-and-edit-sml.png)](device-manager-images/win/23-dupe-and-edit.png#lightbox)

-   **Visualizza in Esplora risorse**: apre una finestra di Esplora risorse con la cartella contenente i file del dispositivo virtuale. Se ad esempio si seleziona **Nexus 5 X API 25** e si fa clic su **Visualizza in Esplora risorse** viene visualizzata una finestra simile alla seguente:

    [![Risultato del clic su Visualizza in Esplora risorse](device-manager-images/win/24-reveal-in-explorer-sml.png)](device-manager-images/win/24-reveal-in-explorer.png#lightbox)

-   **Factory Reset** (Ripristino delle impostazioni predefinite): ripristina le impostazioni predefinite del dispositivo selezionato, eliminando tutte le modifiche apportate dall'utente allo stato interno del dispositivo mentre era in esecuzione (eliminando anche l'eventuale snapshot per l'[avvio rapido](~/android/deploy-test/debugging/android-sdk-emulator/running-the-emulator.md#quick-boot) corrente). Questa modifica non annulla le modifiche apportate al dispositivo virtuale durante la creazione e la modifica. Un messaggio ricorda che questa operazione di reimpostazione non può essere annullata. Fare clic su **Wipe user data** (Cancella dati utente) per confermare la reimpostazione.

-   **Elimina**: elimina definitivamente il dispositivo virtuale selezionato.
    Un messaggio ricorda che l'eliminazione di un dispositivo non può essere annullata. Fare clic su **Elimina** se si è certi di voler eliminare il dispositivo.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Questo articolo spiega come usare Android Device Manager per creare, duplicare, personalizzare e avviare i dispositivi virtuali Android.

[![Screenshot di Android Device Manager nella scheda Dispositivi](device-manager-images/mac/01-devices-dialog-sml.png)](device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> Questa guida è applicabile solo a Visual Studio per Mac.
Xamarin Studio non è compatibile con Android Device Manager.

È possibile usare Android Device Manager per creare e configurare *dispositivi virtuali Android* (AVD) eseguibili nell'[emulatore Android di Google](~/android/deploy-test/debugging/android-sdk-emulator/index.md).
Ogni AVD è una configurazione dell'emulatore che simula un dispositivo Android fisico. In questo modo è possibile eseguire e sottoporre a test l'applicazione in varie configurazioni che simulano dispositivi Android fisici diversi.

## <a name="requirements"></a>Requisiti

- Visual Studio per Mac 7.5 o versione successiva.

- Android SDK 8.0 (API 26) o versione successiva deve essere installato usando Android SDK Manager.


## <a name="launching-the-device-manager"></a>Avvio di Gestione dispositivi

Avviare Android Device Manager facendo clic su **Strumenti > Device Manager**:

[![Avvio dal menu Strumenti](device-manager-images/mac/16-tools-menu-sml.png)](device-manager-images/mac/16-tools-menu.png#lightbox)

Per usare Android Device Manager, è necessario installare Android SDK Tools 26.0.2 o versione successiva. Se non è installato Android SDK Tools 26.0.2 o versione successiva, all'avvio viene visualizzato il messaggio di errore seguente:

![Messaggio di errore di istanza di Android SDK](device-manager-images/mac/02-sdk-instance-error.png)

Se viene visualizzato questo messaggio di errore, fare clic su **OK** per aprire Android SDK Manager. In Android SDK Manager fare clic sulla scheda **Tools** (Strumenti) e installare i componenti seguenti:

-   **Android SDK Tools 26.0.2** o versione successiva 
-   **Android SDK Platform-Tools 26.0.0** o versione successiva 
-   **Android SDK Build-Tools 26.0.0** o versione successiva

Questi pacchetti devono essere visualizzati con lo stato **Installed** (Installato) come illustrato nello screenshot seguente:

[![Installazione di Android SDK Tools 26.0](device-manager-images/mac/03-sdk-tools-sml.png)](device-manager-images/mac/03-sdk-tools.png#lightbox)

## <a name="main-screen"></a>Schermata principale

Al primo avvio di Gestione dispositivi di Xamarin Android una schermata visualizza tutti i dispositivi virtuali attualmente configurati. Per ogni dispositivo vengono visualizzati i parametri **Nome**, **System Image** (Immagine del sistema) (livello dell'API Android), **CPU**, **Memoria** e la risoluzione dello schermo:

[![Elenco dei dispositivi installati e relativi parametri](device-manager-images/mac/05-devices-list-sml.png)](device-manager-images/mac/05-devices-list.png#lightbox)

Fare clic su un pulsante **Play** (Riproduci) per avviare l'emulatore con il dispositivo virtuale desiderato:

[![Pulsante Avvia per un'immagine del dispositivo](device-manager-images/mac/06-start-button-sml.png)](device-manager-images/mac/06-start-button.png#lightbox)

Dopo che l'emulatore viene avviato con il dispositivo virtuale selezionato, il pulsante **Play** (Riproduci) diventa **Arresta** e può essere usato per interrompere l'esecuzione dell'emulatore:

[![Pulsante Arresta per il dispositivo in esecuzione](device-manager-images/mac/07-stop-button-sml.png)](device-manager-images/mac/07-stop-button.png#lightbox)

### <a name="new-device"></a>Nuovo dispositivo

Per creare un nuovo dispositivo fare clic sul pulsante **New Device** (Nuovo dispositivo) nell'angolo superiore destro della schermata:

[![Pulsante Nuovo per creare un nuovo dispositivo](device-manager-images/mac/08-new-button-sml.png)](device-manager-images/mac/08-new-button.png#lightbox)

Quando si fa clic su **New Device** (Nuovo dispositivo) viene visualizzata la schermata **New Device** (Nuovo dispositivo):

[![Schermata New Device (Nuovo dispositivo) di Gestione dispositivi](device-manager-images/mac/09-new-device-editor-sml.png)](device-manager-images/mac/09-new-device-editor.png#lightbox)

Per configurare un nuovo dispositivo nella schermata **New Device** (Nuovo dispositivo) seguire questa procedura:

1. Selezionare un dispositivo fisico da emulare facendo clic sul menu a discesa **Dispositivo**:

    [![Menu a discesa Dispositivo](device-manager-images/mac/10-device-menu-sml.png)](device-manager-images/mac/10-device-menu.png#lightbox)

2. Selezionare un'immagine del sistema da usare con questo dispositivo virtuale facendo clic sul menu a discesa **System image** (Immagine di sistema). In questo menu le immagini di sistema di Device Manager installate vengono visualizzate sotto **Installed** (Installato). La sezione **Download** (se visualizzata) elenca le immagini di sistema di Device Manager che non sono attualmente disponibili nel computer di sviluppo, ma possono essere installate automaticamente:

    [![Menu a discesa System image (Immagine di sistema)](device-manager-images/mac/11-system-image-menu-sml.png)](device-manager-images/mac/11-system-image-menu.png#lightbox)

3. Assegnare un nuovo nome al dispositivo. Nell'esempio seguente, al nuovo dispositivo viene assegnato il nome **Nexus 5X API 25**:

    [![Assegnazione di un nome al nuovo dispositivo](device-manager-images/mac/12-device-name-sml.png)](device-manager-images/mac/12-device-name.png#lightbox)

4. Modificare le proprietà in base alle esigenze. Per apportare modifiche alle proprietà, vedere [Modifica delle proprietà del dispositivo virtuale Android](~/android/get-started/installation/android-emulator/device-properties.md).

5. Aggiungere le eventuali proprietà addizionali che è necessario impostare in modo esplicito. La schermata **New Device** (Nuovo dispositivo) elenca solo le proprietà modificate più di frequente. Per aggiungere altre proprietà fare clic sul menu a discesa **Aggiungi proprietà** nell'angolo inferiore sinistro della schermata:

    [![Menu a discesa Aggiungi proprietà](device-manager-images/mac/13-add-property-menu-sml.png)](device-manager-images/mac/13-add-property-menu.png#lightbox)

6. È anche possibile fare clic su **Personalizzato** per definire una nuova proprietà per il dispositivo:

    ![Pulsante Crea](device-manager-images/mac/14-custom-button.png)

7. Fare clic sul pulsante **Crea** nell'angolo inferiore destro per creare il nuovo dispositivo:

    ![Pulsante Crea](device-manager-images/mac/15-create-button.png)

8. È possibile che venga visualizzata una schermata **Accettazione della licenza**. Se si accettano le condizioni di licenza, fare clic su **Accetta**.

9. Android Device Manager aggiunge il nuovo dispositivo all'elenco dei dispositivi virtuali installati, visualizzando un indicatore di stato **Creating** (Creazione in corso) durante la creazione del dispositivo:

    [![Indicatore di stato Creazione in corso](device-manager-images/mac/17-creating-the-device-sml.png)](device-manager-images/mac/17-creating-the-device.png#lightbox)

10. Dopo aver completato il processo di creazione il nuovo dispositivo viene visualizzato nell'elenco di dispositivi, con un pulsante **Avvia** che consente di avviarlo:

   [![Dispositivo appena creato pronto per l'avvio](device-manager-images/mac/18-created-device-sml.png)](device-manager-images/mac/18-created-device.png#lightbox)


### <a name="edit-device"></a>Modificare il dispositivo

Per modificare un dispositivo virtuale esistente, selezionare il menu a discesa **Opzioni aggiuntive** (icona a forma di ingranaggio) e selezionare **Modifica**:
 
[![Comando di menu Modifica per la modifica di un nuovo dispositivo](device-manager-images/mac/19-edit-button-sml.png)](device-manager-images/mac/19-edit-button.png#lightbox)

Quando si fa clic su **Modifica** viene avviata la schermata Device Editor (Editor dispositivi) per il dispositivo virtuale selezionato:
 
[![Schermata Device Editor (Editor dispositivi)](device-manager-images/mac/20-device-editor-sml.png)](device-manager-images/mac/20-device-editor.png#lightbox)

La schermata **Device Editor** (Editor dispositivi) elenca le proprietà del dispositivo virtuale nella prima colonna e i valori corrispondenti di ogni proprietà nella seconda colonna. Quando si seleziona una proprietà, sul lato destro viene visualizzata una descrizione dettagliata di tale proprietà.

Ad esempio, nella schermata seguente il valore della proprietà `hw.lcd.density` viene modificato da **320** a **240** e il valore della proprietà `hw.ramSize` viene impostato su **768**:
 
[![Esempio di modifica dispositivo](device-manager-images/mac/21-device-editing-sml.png)](device-manager-images/mac/21-device-editing.png#lightbox)

Dopo aver apportato le modifiche di configurazione necessarie, fare clic sul pulsante **Salva**.
Per altre informazioni sulla modifica delle proprietà del dispositivo virtuale, vedere [Modifica delle proprietà del dispositivo virtuale Android](~/android/get-started/installation/android-emulator/device-properties.md).

 
### <a name="additional-options"></a>Opzioni aggiuntive

Opzioni aggiuntive per l'uso dei dispositivi sono disponibili nel menu a discesa visualizzato a sinistra del pulsante **Play** (Riproduci):

[![Posizione del menu delle opzioni aggiuntive](device-manager-images/mac/22-overflow-menu-sml.png)](device-manager-images/mac/22-overflow-menu.png#lightbox)

Il menu delle opzioni aggiuntive contiene le voci seguenti:

-   **Edit** (Modifica): apre il dispositivo attualmente selezionato nell'editor dispositivi, come descritto in precedenza.

-   **Duplicate and Edit** (Duplica e modifica): duplica il dispositivo selezionato attualmente e lo apre nella schermata **New Device** (Nuovo dispositivo) con un nome univoco diverso.
    Se ad esempio si seleziona **Nexus 5X API 25** e si fa clic su **Duplicate and Edit** (Duplica e modifica) viene accodato un contatore al nome del dispositivo:

    [![Schermata Duplicate and Edit (Duplica e modifica)](device-manager-images/mac/23-dupe-and-edit-sml.png)](device-manager-images/mac/23-dupe-and-edit.png#lightbox)

-   **Visualizza in Finder**: apre una finestra del Finder di macOS con la cartella contenente i file del dispositivo virtuale. Se ad esempio si seleziona **Nexus 5X API 25** e si fa clic su **Visualizza in Finder** viene visualizzata una finestra simile alla seguente:

    [![Risultato del clic su Visualizza in Esplora risorse](device-manager-images/mac/24-reveal-in-finder-sml.png)](device-manager-images/mac/24-reveal-in-finder.png#lightbox)

-   **Ripristino delle impostazioni predefinite**: ripristina le impostazioni predefinite del dispositivo selezionato, eliminando tutte le modifiche apportate dall'utente allo stato interno del dispositivo mentre era in esecuzione. Questa modifica non annulla le modifiche apportate al dispositivo virtuale durante la creazione e la modifica. Un messaggio ricorda che questa operazione di reimpostazione non può essere annullata. Fare clic su **Wipe user data** (Cancella dati utente) per confermare la reimpostazione.

-   **Elimina**: elimina definitivamente il dispositivo virtuale selezionato.
    Un messaggio ricorda che l'eliminazione di un dispositivo non può essere annullata. Fare clic su **Elimina** se si è certi di voler eliminare il dispositivo.

-----

## <a name="summary"></a>Riepilogo

Questa guida ha presentato Android Device Manager, disponibile in Visual Studio per Mac e in Strumenti di Visual Studio per Xamarin. Sono state presentate funzionalità essenziali come l'avvio e l'arresto dell'emulatore Android, la selezione di un dispositivo virtuale Android (AVD) per l'esecuzione, la creazione di nuovi dispositivi virtuali e la modifica di un dispositivo virtuale. È stato anche descritto come modificare le proprietà del profilo hardware per un'ulteriore personalizzazione.


## <a name="related-links"></a>Collegamenti correlati

- [Modifiche agli strumenti di Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Debug con l'emulatore di Android SDK](~/android/deploy-test/debugging/android-sdk-emulator/index.md)
- [Note sulla versione di SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
