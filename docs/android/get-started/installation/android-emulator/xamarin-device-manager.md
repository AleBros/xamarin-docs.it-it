---
title: Gestione dispositivi di Xamarin Android
description: Gestione dispositivi di Xamarin Android, attualmente in anteprima, sostituisce Gestione dispositivi legacy di Google. La presente guida illustra come usare Gestione dispositivi di Xamarin Android per creare e configurare dispositivi virtuali Android (AVD) che emulano i dispositivi Android. È possibile usare questi dispositivi virtuali per eseguire e testare l'app anche senza un dispositivo fisico.
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 94f82c9f893e22074ba95c052b57ce6ff18eaa1e
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2018
---
# <a name="xamarin-android-device-manager"></a>Gestione dispositivi di Xamarin Android

_Gestione dispositivi di Xamarin Android, attualmente in anteprima, sostituisce Gestione dispositivi legacy di Google. La presente guida illustra come usare Gestione dispositivi di Xamarin Android per creare e configurare dispositivi virtuali Android (AVD) che emulano i dispositivi Android. È possibile usare questi dispositivi virtuali per eseguire e testare l'app anche senza un dispositivo fisico._

![Attualmente in anteprima](~/media/shared/preview.png)
 
## <a name="overview"></a>Panoramica

Dopo la verifica dell'abilitazione dell'accelerazione hardware (procedura descritta in [Accelerazione hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), il passaggio successivo è la creazione di dispositivi virtuali per il test e il debug dell'app. È possibile usare Gestione dispositivi di Xamarin Android per creare dispositivi virtuali da usare con l'emulatore di Android SDK.

Perché usare Gestione dispositivi di Xamarin Android anziché [Gestione dispositivi di Google](~/android/get-started/installation/android-emulator/google-emulator-manager.md)?
A partire da Android SDK Tools 26.0.1, Google ha rimosso il supporto per i dispositivi virtuali Android e i gestori SDK basati sull'interfaccia utente, favorendo l'uso dei nuovi strumenti CLI (Command Line Interface). A causa di questa modifica è necessario usare [Xamarin SDK Manager](~/android/get-started/installation/android-sdk.md) e Gestione dispositivi di Xamarin Android quando si aggiorna ad Android SDK Tools 26.0.1 e versioni successive (aggiornamento obbligatorio per lo sviluppo di Android 8.0 Oreo).


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Questa guida illustra come installare e usare Gestione dispositivi di Xamarin Android in Visual Studio per Windows (o [per Mac](?tabs=vsmac)):

[![Screenshot di Gestione dispositivi di Xamarin Android nella scheda Dispositivi](xamarin-device-manager-images/win/01-devices-dialog-sml.png)](xamarin-device-manager-images/win/01-devices-dialog.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Questa guida illustra come installare e usare Gestione dispositivi di Xamarin Android in Visual Studio per Mac (o [per Windows](?tabs=vswin)):

[![Screenshot di Gestione dispositivi di Xamarin Android nella scheda Dispositivi](xamarin-device-manager-images/mac/01-devices-dialog-sml.png)](xamarin-device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> Questa guida è applicabile solo a Visual Studio per Mac.
Xamarin Studio non è compatibile con Gestione dispositivi di Xamarin Android.

-----

Gestione dispositivi di Xamarin Android consente di creare *dispositivi virtuali Android* (AVD) eseguibili nell'[emulatore di Android SDK](~/android/deploy-test/debugging/android-sdk-emulator/index.md).
Ogni AVD è una configurazione dell'emulatore che simula un dispositivo Android fisico. In questo modo è possibile eseguire e sottoporre a test l'applicazione in varie configurazioni che simulano dispositivi Android fisici diversi. Gestione dispositivi di Xamarin Android sostituisce l'applicazione autonoma AVD Manager di Google (che è stata deprecata).

Questa guida illustra come installare e avviare Gestione dispositivi di Xamarin Android. Si apprende come creare, duplicare, personalizzare e avviare i dispositivi virtuali. La guida illustra anche come configurare le proprietà di ogni dispositivo virtuale (ad esempio livello API, CPU, memoria e risoluzione), come abilitare o disabilitare i sensori simulati (ad esempio l'accelerometro, il GPS, l'orientamento e il sensore di luce) e come configurare il tipo di accelerazione hardware usato dal dispositivo virtuale.

## <a name="requirements"></a>Requisiti

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per usare Gestione dispositivi di Xamarin Android sono necessari i componenti seguenti:

- Visual Studio 2017 15.5 o versione successiva. È supportato Visual Studio Community Edition o versione successiva.

- Xamarin for Visual Studio 4.8 o versione successiva. Per informazioni sull'aggiornamento di Xamarin, vedere [Change the Updates Channel](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) (Modificare il canale Aggiornamenti).

- La versione più recente del [programma di installazione di Gestione dispositivi di Xamarin](https://go.microsoft.com/fwlink/?linkid=865528) per Windows.

- **Android SDK**: devono essere installati Android SDK (vedere [Android SDK Setup](~/android/get-started/installation/android-sdk.md) (Installazione di Android SDK)) e SDK Tools 26.0, come descritto nella sezione seguente. Verificare di installare Android SDK nel seguente percorso (se non è già installato): **C:\\Programmi (x86)\\Android\\android-sdk**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

- Visual Studio per Mac 7.4 o versione successiva

- La versione più recente del [programma di installazione di Gestione dispositivi di Xamarin](https://go.microsoft.com/fwlink/?linkid=865527) per macOS.

- **Android SDK** &ndash;Android SDK 8.0 (API 26) o versione successiva deve essere installato usando SDK Manager.

-----

## <a name="installing-the-device-manager"></a>Installazione di Gestione dispositivi

Per installare Gestione dispositivi di Xamarin Android seguire questa procedura:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Scaricare la versione più recente del [programma di installazione di Gestione dispositivi di Xamarin](https://go.microsoft.com/fwlink/?linkid=865528) per Windows.

2. Fare doppio clic su **Xamarin.DeviceManager.msi** e seguire le istruzioni di installazione: 

    ![Installazione guidata di Gestione dispositivi di Xamarin Android](xamarin-device-manager-images/win/30-installer.png)


> [!NOTE]
> A partire da [Visual Studio 2017 Preview 5](https://www.visualstudio.com/vs/preview/) Android Device Manager verrà distribuito come parte del programma di installazione di VS2017. Non è necessario scaricare un programma di installazione separato per ottenere Xamarin Android Device Manager con Visual Studio 2017 Preview 5.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Scaricare la versione più recente del [programma di installazione di Gestione dispositivi di Xamarin](https://go.microsoft.com/fwlink/?linkid=865527) per macOS.

2. Fare doppio clic **AndroidDevices.pkg** e seguire le istruzioni di installazione: 

    [![Installazione guidata di Gestione dispositivi di Xamarin Android](xamarin-device-manager-images/mac/30-installer-sml.png)](xamarin-device-manager-images/mac/30-installer.png#lightbox)

-----
## <a name="launching-the-device-manager"></a>Avvio di Gestione dispositivi

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

In Visual Studio 15.6 Preview 3 o versione successiva è possibile avviare Gestione dispositivi di Xamarin Android dal menu **Strumenti**. Se si usa Visual Studio 15.6 Preview 3 o versione successiva, avviare Gestione dispositivi facendo clic su **Strumenti > Gestione emulatori Android**:

[![Avvio dal menu Strumenti](xamarin-device-manager-images/win/04-tools-menu-sml.png)](xamarin-device-manager-images/win/04-tools-menu.png#lightbox)

Se si usa una versione di Visual Studio precedente, è necessario avviare Gestione dispositivi di Xamarin Android dal menu **Start** di Windows.

![Gestione dispositivi di Xamarin Android nel menu Start](xamarin-device-manager-images/win/31-start-menu.png)

Fare clic con il pulsante destro del mouse su **Gestione dispositivi di Xamarin Android** e selezionare **Altro > Esegui come amministratore**. Se all'avvio viene visualizzato il messaggio di errore seguente, vedere la sezione [Risoluzione dei problemi](#troubleshooting) per informazioni sulle soluzioni alternative:

![Errore di istanza di Android SDK](xamarin-device-manager-images/win/32-sdk-error.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

In Visual Studio per Mac 7.6 Preview 3 (attualmente nel canale alfa) o versione successiva, è possibile avviare Gestione dispositivi di Xamarin Android selezionando **Strumenti > Emulator Manager** (Gestione emulatori):

[![Avvio dal menu Strumenti](xamarin-device-manager-images/mac/16-tools-menu-sml.png)](xamarin-device-manager-images/mac/16-tools-menu.png#lightbox)

Se si usa una versione precedente di Visual Studio per Mac è necessario avviare Gestione dispositivi di Xamarin Android in modo indipendente. Passare a **Dispositivi Android** nella cartella **Applicazioni** e fare doppio clic sul programma per avviarlo:

[![Gestione dispositivi di Xamarin Android nel Finder](xamarin-device-manager-images/mac/31-location-in-finder-sml.png)](xamarin-device-manager-images/mac/31-location-in-finder.png#lightbox)

-----

Per usare Gestione dispositivi di Xamarin Android è necessario installare Android SDK Tools 26.0.0 o versione successiva. Se non è installato Android SDK Tools 26.0.0 o versione successiva, all'avvio viene visualizzato il messaggio di errore seguente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Messaggio di errore di istanza di Android SDK](xamarin-device-manager-images/win/02-sdk-instance-error.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Messaggio di errore di istanza di Android SDK](xamarin-device-manager-images/mac/02-sdk-instance-error.png)

-----

Se viene visualizzato questo messaggio di errore, fare clic su **OK** per aprire Android SDK Manager. In Android SDK Manager fare clic sulla scheda **Strumenti** e installare **Android SDK Tools 26.0.2** o versione successiva, **Android SDK Platform Tools 26.0.0** o versione successiva e **Android SDK Build Tools 26.0.0** o versione successiva:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Installazione di Android SDK Tools 26.0](xamarin-device-manager-images/win/03-sdk-tools-sml.png)](xamarin-device-manager-images/win/03-sdk-tools.png#lightbox)

Dopo aver installato questi pacchetti, chiudere SDK Manager e riavviare Gestione dispositivi di Xamarin Android.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Installazione di Android SDK Tools 26.0](xamarin-device-manager-images/mac/03-sdk-tools-sml.png)](xamarin-device-manager-images/mac/03-sdk-tools.png#lightbox)

-----

## <a name="main-screen"></a>Schermata principale

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Al primo avvio di Gestione dispositivi di Xamarin Android una schermata visualizza tutti i dispositivi virtuali attualmente configurati. Per ogni dispositivo vengono visualizzati i parametri **Nome**, **Sistema operativo** (livello dell'API Android), **CPU**, **Memoria** e la risoluzione dello schermo:

[![Elenco dei dispositivi installati e relativi parametri](xamarin-device-manager-images/win/05-installed-list-sml.png)](xamarin-device-manager-images/win/05-installed-list.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Al primo avvio di Gestione dispositivi di Xamarin Android una schermata visualizza tutti i dispositivi virtuali attualmente configurati. Per ogni dispositivo vengono visualizzati i parametri **Nome**, **System Image** (Immagine del sistema) (livello dell'API Android), **CPU**, **Memoria** e la risoluzione dello schermo:

[![Elenco dei dispositivi installati e relativi parametri](xamarin-device-manager-images/mac/05-devices-list-sml.png)](xamarin-device-manager-images/mac/05-devices-list.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Quando si fa clic su un dispositivo nell'elenco, a destra del nome viene visualizzato il pulsante **Avvia**. Fare clic su **Avvia** per avviare l'emulatore con questo dispositivo virtuale:

[![Pulsante Avvia per un'immagine del dispositivo](xamarin-device-manager-images/win/06-start-button-sml.png)](xamarin-device-manager-images/win/06-start-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Fare clic su un pulsante **Play** (Riproduci) per avviare l'emulatore con il dispositivo virtuale desiderato:

[![Pulsante Avvia per un'immagine del dispositivo](xamarin-device-manager-images/mac/06-start-button-sml.png)](xamarin-device-manager-images/mac/06-start-button.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Dopo che l'emulatore viene avviato con il dispositivo virtuale selezionato, il pulsante **Avvia** diventa **Arresta** e può essere usato per interrompere l'esecuzione dell'emulatore:

[![Pulsante Arresta per il dispositivo in esecuzione](xamarin-device-manager-images/win/07-stop-button-sml.png)](xamarin-device-manager-images/win/07-stop-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Dopo che l'emulatore viene avviato con il dispositivo virtuale selezionato, il pulsante **Play** (Riproduci) diventa **Arresta** e può essere usato per interrompere l'esecuzione dell'emulatore:

[![Pulsante Arresta per il dispositivo in esecuzione](xamarin-device-manager-images/mac/07-stop-button-sml.png)](xamarin-device-manager-images/mac/07-stop-button.png#lightbox)

-----

### <a name="new-device"></a>Nuovo dispositivo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per creare un nuovo dispositivo fare clic sul pulsante **Nuovo** nell'angolo superiore destro della schermata:

[![Pulsante Nuovo per creare un nuovo dispositivo](xamarin-device-manager-images/win/08-new-button-sml.png)](xamarin-device-manager-images/win/08-new-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per creare un nuovo dispositivo fare clic sul pulsante **New Device** (Nuovo dispositivo) nell'angolo superiore destro della schermata:

[![Pulsante Nuovo per creare un nuovo dispositivo](xamarin-device-manager-images/mac/08-new-button-sml.png)](xamarin-device-manager-images/mac/08-new-button.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Quando si fa clic su **Nuovo** viene visualizzata la schermata **New Device** (Nuovo dispositivo):

[![Schermata New Device (Nuovo dispositivo) di Gestione dispositivi](xamarin-device-manager-images/win/09-new-device-editor-sml.png)](xamarin-device-manager-images/win/09-new-device-editor.png#lightbox)

Per configurare un nuovo dispositivo nella schermata **New Device** (Nuovo dispositivo) seguire questa procedura:

1. Selezionare un dispositivo fisico da emulare facendo clic sul menu a discesa **Dispositivo**:

    [![Menu a discesa Dispositivo](xamarin-device-manager-images/win/10-device-menu-sml.png)](xamarin-device-manager-images/win/10-device-menu.png#lightbox)

2. Selezionare un'immagine del sistema da usare con questo dispositivo virtuale facendo clic sul menu a discesa **System image** (Immagine di sistema). In questo menu le immagini di sistema installate vengono visualizzate sotto **Installato**. La sezione **Scarica** elenca le immagini di sistema che non sono attualmente disponibili nel computer di sviluppo, ma possono essere installate automaticamente:

    [![Menu a discesa System image (Immagine di sistema)](xamarin-device-manager-images/win/11-system-image-menu-sml.png)](xamarin-device-manager-images/win/11-system-image-menu.png#lightbox)

3. Assegnare un nuovo nome al dispositivo. Nell'esempio seguente, al nuovo dispositivo viene assegnato il nome **Nexus 5 API 25**:

    [![Assegnazione di un nome al nuovo dispositivo](xamarin-device-manager-images/win/12-device-name-sml.png)](xamarin-device-manager-images/win/12-device-name.png#lightbox)

4. Modificare le proprietà in base alle esigenze. Per apportare modifiche alle proprietà, vedere [Proprietà profilo](#properties) più avanti in questa guida.

5. Aggiungere le eventuali proprietà addizionali che è necessario impostare in modo esplicito. La schermata **New Device** (Nuovo dispositivo) elenca solo le proprietà modificate più di frequente. Per aggiungere altre proprietà fare clic sul menu a discesa **Aggiungi proprietà** nell'angolo inferiore sinistro della schermata. Nell'esempio seguente viene aggiunta la proprietà `hw.lcd.backlight`:

    [![Menu a discesa Aggiungi proprietà](xamarin-device-manager-images/win/13-add-property-menu-sml.png)](xamarin-device-manager-images/win/13-add-property-menu.png#lightbox)

6. Fare clic sul pulsante **Crea** nell'angolo inferiore destro per creare il nuovo dispositivo:

    ![Pulsante Crea](xamarin-device-manager-images/win/14-create-button.png)

7. È possibile che venga visualizzata una schermata **Accettazione della licenza**. Se si accettano le condizioni di licenza, fare clic su **Accetta**:

    ![Schermata Accettazione della licenza](xamarin-device-manager-images/win/15-license-acceptance.png)

8. Gestione dispositivi di Xamarin Android aggiunge il nuovo dispositivo all'elenco di dispositivi virtuali installati, con un indicatore di stato **Creazione in corso** durante la creazione:

    [![Indicatore di stato Creazione in corso](xamarin-device-manager-images/win/16-creating-the-device-sml.png)](xamarin-device-manager-images/win/16-creating-the-device.png#lightbox)

9. Dopo aver completato il processo di creazione, il nuovo dispositivo viene visualizzato nell'elenco di dispositivi virtuali installati, con un pulsante **Avvia** che consente di avviarlo:

   [![Dispositivo appena creato pronto per l'avvio](xamarin-device-manager-images/win/17-created-device-sml.png)](xamarin-device-manager-images/win/17-created-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Quando si fa clic su **New Device** (Nuovo dispositivo) viene visualizzata la schermata **New Device** (Nuovo dispositivo):

[![Schermata New Device (Nuovo dispositivo) di Gestione dispositivi](xamarin-device-manager-images/mac/09-new-device-editor-sml.png)](xamarin-device-manager-images/mac/09-new-device-editor.png#lightbox)

Per configurare un nuovo dispositivo nella schermata **New Device** (Nuovo dispositivo) seguire questa procedura:

1. Selezionare un dispositivo fisico da emulare facendo clic sul menu a discesa **Dispositivo**:

    [![Menu a discesa Dispositivo](xamarin-device-manager-images/mac/10-device-menu-sml.png)](xamarin-device-manager-images/mac/10-device-menu.png#lightbox)

2. Selezionare un'immagine del sistema da usare con questo dispositivo virtuale facendo clic sul menu a discesa **System image** (Immagine di sistema). In questo menu le immagini di sistema installate vengono visualizzate sotto **Installato**. La sezione **Scarica** (se visualizzata) elenca le immagini di sistema che non sono attualmente disponibili nel computer di sviluppo, ma possono essere installate automaticamente:

    [![Menu a discesa System image (Immagine di sistema)](xamarin-device-manager-images/mac/11-system-image-menu-sml.png)](xamarin-device-manager-images/mac/11-system-image-menu.png#lightbox)

3. Assegnare un nuovo nome al dispositivo. Nell'esempio seguente, al nuovo dispositivo viene assegnato il nome **Nexus 5X API 25**:

    [![Assegnazione di un nome al nuovo dispositivo](xamarin-device-manager-images/mac/12-device-name-sml.png)](xamarin-device-manager-images/mac/12-device-name.png#lightbox)

4. Modificare le proprietà in base alle esigenze. Per apportare modifiche alle proprietà, vedere [Proprietà profilo](#properties) più avanti in questa guida.

5. Aggiungere le eventuali proprietà addizionali che è necessario impostare in modo esplicito. La schermata **New Device** (Nuovo dispositivo) elenca solo le proprietà modificate più di frequente. Per aggiungere altre proprietà fare clic sul menu a discesa **Aggiungi proprietà** nell'angolo inferiore sinistro della schermata:

    [![Menu a discesa Aggiungi proprietà](xamarin-device-manager-images/mac/13-add-property-menu-sml.png)](xamarin-device-manager-images/mac/13-add-property-menu.png#lightbox)

6. È anche possibile fare clic su **Personalizzato** per definire una nuova proprietà per il dispositivo:

    ![Pulsante Crea](xamarin-device-manager-images/mac/14-custom-button.png)

7. Fare clic sul pulsante **Crea** nell'angolo inferiore destro per creare il nuovo dispositivo:

    ![Pulsante Crea](xamarin-device-manager-images/mac/15-create-button.png)

8. È possibile che venga visualizzata una schermata **Accettazione della licenza**. Se si accettano le condizioni di licenza, fare clic su **Accetta**.

9. Mentre viene creato, il nuovo dispositivo viene aggiunto all'elenco di dispositivi virtuali installati, con un indicatore di stato **Creazione in corso**:

    [![Indicatore di stato Creazione in corso](xamarin-device-manager-images/mac/17-creating-the-device-sml.png)](xamarin-device-manager-images/mac/17-creating-the-device.png#lightbox)

10. Dopo aver completato il processo di creazione il nuovo dispositivo viene visualizzato nell'elenco di dispositivi, con un pulsante **Avvia** che consente di avviarlo:

   [![Dispositivo appena creato pronto per l'avvio](xamarin-device-manager-images/mac/18-created-device-sml.png)](xamarin-device-manager-images/mac/18-created-device.png#lightbox)

-----


<a name="device-edit" />


### <a name="edit-device"></a>Modificare il dispositivo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per modificare un dispositivo virtuale esistente, selezionare il dispositivo e fare clic sul pulsante **Modifica** nell'angolo superiore destro dello schermo:

[![Pulsante Modifica per modificare un nuovo dispositivo](xamarin-device-manager-images/win/19-edit-button-sml.png)](xamarin-device-manager-images/win/19-edit-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per modificare un dispositivo virtuale esistente, selezionare il menu a discesa **Opzioni aggiuntive** (icona a forma di ingranaggio) e selezionare **Modifica**:
 
[![Comando di menu Modifica per la modifica di un nuovo dispositivo](xamarin-device-manager-images/mac/19-edit-button-sml.png)](xamarin-device-manager-images/mac/19-edit-button.png#lightbox)
 
-----

Quando si fa clic su **Modifica** viene avviata la schermata Device Editor (Editor dispositivi) per il dispositivo virtuale selezionato:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Schermata Device Editor (Editor dispositivi)](xamarin-device-manager-images/win/20-device-editor-sml.png)](xamarin-device-manager-images/win/20-device-editor.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
 
[![Schermata Device Editor (Editor dispositivi)](xamarin-device-manager-images/mac/20-device-editor-sml.png)](xamarin-device-manager-images/mac/20-device-editor.png#lightbox)
 
-----

La schermata **Device Editor** (Editor dispositivi) elenca le proprietà del dispositivo virtuale nella prima colonna e i valori corrispondenti di ogni proprietà nella seconda colonna. Quando si seleziona una proprietà, sul lato destro viene visualizzata una descrizione dettagliata di tale proprietà.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Ad esempio, nello screenshot seguente il valore della proprietà `hw.lcd.density` viene modificato da **420** a **240**:

[![Esempio di modifica dispositivo](xamarin-device-manager-images/win/21-device-editing-sml.png)](xamarin-device-manager-images/win/21-device-editing.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Ad esempio, nella schermata seguente il valore della proprietà `hw.lcd.density` viene modificato da **320** a **240** e il valore della proprietà `hw.ramSize` viene impostato su **768**:
 
[![Esempio di modifica dispositivo](xamarin-device-manager-images/mac/21-device-editing-sml.png)](xamarin-device-manager-images/mac/21-device-editing.png#lightbox)
 
-----

Dopo aver apportato le modifiche di configurazione necessarie, fare clic sul pulsante **Salva**.
Per altre informazioni sulla modifica delle proprietà del dispositivo virtuale, vedere [Proprietà profilo](#properties) più avanti in questa guida.


 
### <a name="additional-options"></a>Opzioni aggiuntive

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Opzioni aggiuntive per l'uso dei dispositivi sono disponibili nel menu &hellip; nell'angolo superiore destro della schermata:

[![Posizione del menu delle opzioni aggiuntive](xamarin-device-manager-images/win/22-overflow-menu-sml.png)](xamarin-device-manager-images/win/22-overflow-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Opzioni aggiuntive per l'uso dei dispositivi sono disponibili nel menu a discesa visualizzato a sinistra del pulsante **Play** (Riproduci):

[![Posizione del menu delle opzioni aggiuntive](xamarin-device-manager-images/mac/22-overflow-menu-sml.png)](xamarin-device-manager-images/mac/22-overflow-menu.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Il menu delle opzioni aggiuntive contiene le voci seguenti:

-   **Duplicate and Edit** (Duplica e modifica): duplica il dispositivo selezionato attualmente e lo apre nella schermata **New Device** (Nuovo dispositivo) con un nome univoco diverso. Ad esempio, se si seleziona **VisualStudio_android 23_x86_phone** e si fa clic su **Duplicate and Edit** (Duplica e modifica) viene accodato un contatore al nome del dispositivo:

    [![Schermata Duplicate and Edit (Duplica e modifica)](xamarin-device-manager-images/win/23-dupe-and-edit-sml.png)](xamarin-device-manager-images/win/23-dupe-and-edit.png#lightbox)

-   **Visualizza in Esplora risorse**: apre una finestra di Esplora risorse con la cartella contenente i file del dispositivo virtuale. Se ad esempio si seleziona **Nexus 5 X API 25** e si fa clic su **Visualizza in Esplora risorse** viene visualizzata una finestra simile alla seguente:

    [![Risultato del clic su Visualizza in Esplora risorse](xamarin-device-manager-images/win/24-reveal-in-explorer-sml.png)](xamarin-device-manager-images/win/24-reveal-in-explorer.png#lightbox)

-   **Ripristino delle impostazioni predefinite**: ripristina le impostazioni predefinite del dispositivo selezionato, eliminando tutte le modifiche apportate dall'utente allo stato interno del dispositivo mentre era in esecuzione. Questa modifica non annulla le modifiche apportate al dispositivo virtuale durante la creazione e la modifica. Un messaggio ricorda che questa operazione di reimpostazione non può essere annullata. Fare clic su **Wipe user data** (Cancella dati utente) per confermare la reimpostazione.

-   **Elimina**: elimina definitivamente il dispositivo virtuale selezionato.
    Un messaggio ricorda che l'eliminazione di un dispositivo non può essere annullata. Fare clic su **Elimina** se si è certi di voler eliminare il dispositivo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Il menu delle opzioni aggiuntive contiene le voci seguenti:

-   **Modifica**: apre il dispositivo attualmente selezionato nell'editor dispositivi, come descritto precedentemente in [Modificare il dispositivo](#device-edit).

-   **Duplicate and Edit** (Duplica e modifica): duplica il dispositivo selezionato attualmente e lo apre nella schermata **New Device** (Nuovo dispositivo) con un nome univoco diverso.
    Se ad esempio si seleziona **Nexus 5X API 25** e si fa clic su **Duplicate and Edit** (Duplica e modifica) viene accodato un contatore al nome del dispositivo:

    [![Schermata Duplicate and Edit (Duplica e modifica)](xamarin-device-manager-images/mac/23-dupe-and-edit-sml.png)](xamarin-device-manager-images/mac/23-dupe-and-edit.png#lightbox)

-   **Visualizza in Finder**: apre una finestra del Finder di macOS con la cartella contenente i file del dispositivo virtuale. Se ad esempio si seleziona **Nexus 5X API 25** e si fa clic su **Visualizza in Finder** viene visualizzata una finestra simile alla seguente:

    [![Risultato del clic su Visualizza in Esplora risorse](xamarin-device-manager-images/mac/24-reveal-in-finder-sml.png)](xamarin-device-manager-images/mac/24-reveal-in-finder.png#lightbox)

-   **Ripristino delle impostazioni predefinite**: ripristina le impostazioni predefinite del dispositivo selezionato, eliminando tutte le modifiche apportate dall'utente allo stato interno del dispositivo mentre era in esecuzione. Questa modifica non annulla le modifiche apportate al dispositivo virtuale durante la creazione e la modifica. Un messaggio ricorda che questa operazione di reimpostazione non può essere annullata. Fare clic su **Wipe user data** (Cancella dati utente) per confermare la reimpostazione.

-   **Elimina**: elimina definitivamente il dispositivo virtuale selezionato.
    Un messaggio ricorda che l'eliminazione di un dispositivo non può essere annullata. Fare clic su **Elimina** se si è certi di voler eliminare il dispositivo.

-----

<a name="properties" />
 

## <a name="profile-properties"></a>Proprietà profilo

Le schermate **New Device** (Nuovo dispositivo) e **Device Editor** (Editor dispositivi) elencano le proprietà del dispositivo virtuale nella prima colonna e i valori corrispondenti di ogni proprietà nella seconda colonna. Quando si seleziona una proprietà, sul lato destro viene visualizzata una descrizione dettagliata di tale proprietà. È possibile modificare le *proprietà del profilo hardware* e le *proprietà AVD*.
Le proprietà del profilo hardware (ad esempio `hw.ramSize` e `hw.accelerometer`) descrivono le caratteristiche fisiche del dispositivo di emulazione. Tali caratteristiche includono le dimensioni dello schermo, la quantità di RAM disponibile e la presenza o meno di un accelerometro. Le proprietà AVD specificano il funzionamento del dispositivo AVD quando è in esecuzione. Ad esempio è possibile configurare le proprietà AVD per specificare come il dispositivo AVD usa la scheda grafica del computer di sviluppo per il rendering.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

È possibile modificare le proprietà in base alle linee guida seguenti:

-   Per modificare una proprietà booleana, fare clic sul segno di spunta a destra della proprietà:

    ![Modifica di una proprietà booleana](xamarin-device-manager-images/win/25-boolean-value.png)

-   Per modificare una proprietà *enum* (di enumerazione), fare clic sulla freccia giù a destra della proprietà e scegliere un nuovo valore.

    ![Modifica di una proprietà enum](xamarin-device-manager-images/win/27-enum-value.png)

-   Per modificare una proprietà di tipo string o integer, fare doppio clic sull'impostazione corrente della proprietà nella colonna del valore e immettere un nuovo valore.

    ![Proprietà integer](xamarin-device-manager-images/win/26-integer-value.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

È possibile modificare le proprietà in base alle linee guida seguenti:

-   Per modificare una proprietà booleana, fare clic sul segno di spunta a destra della proprietà:

    ![Modifica di una proprietà booleana](xamarin-device-manager-images/mac/25-boolean-value.png)

-   Per modificare una proprietà *enum* (di enumerazione), fare clic sul menu a discesa a destra della proprietà e scegliere un nuovo valore.

    ![Modifica di una proprietà enum](xamarin-device-manager-images/mac/27-enum-value.png)

-   Per modificare una proprietà di tipo string o integer, fare doppio clic sull'impostazione corrente della proprietà nella colonna del valore e immettere un nuovo valore.

    ![Proprietà integer](xamarin-device-manager-images/mac/26-integer-value.png)


-----

La tabella seguente spiega in dettaglio le proprietà elencate nelle schermate **New Device** (Nuovo dispositivo) e **Device Editor** (Editor dispositivi):

[!include[](~/android/includes/emulator-properties.md)]

Per altre informazioni su queste proprietà, vedere [Hardware Profile Properties](https://developer.android.com/studio/run/managing-avds.html#hpproperties) (Proprietà del profilo hardware).


<a name="troubleshooting" />

## <a name="troubleshooting"></a>Risoluzione dei problemi

Le sezioni seguenti descrivono problemi comuni riscontrati con Gestione dispositivi di Xamarin Android e le relative soluzioni:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-in-non-standard-location"></a>Android SDK in un percorso non standard

In genere, Android SDK viene installato nel percorso seguente:

**C:\\Programmi (x86)\\Android\\android-sdk**

Se il SDK non viene installato in questa posizione, è possibile che all'avvio venga visualizzato il messaggio di errore seguente:

![Errore di istanza di Android SDK](xamarin-device-manager-images/win/32-sdk-error.png)

Per evitare questo problema, eseguire le operazioni seguenti:

1. Nel desktop di Windows passare a **C:\\Utenti\\*nomeutente*\\AppData\\Roaming\\XamarinDeviceManager**:

    ![Percorso file di log di Gestione dispositivi di Xamarin](xamarin-device-manager-images/win/33-log-files.png)

2. Fare doppio clic per aprire uno dei file di log e trovare il **percorso del file di configurazione**. Ad esempio:

    [![Percorso del file di configurazione nel file di log](xamarin-device-manager-images/win/34-config-file-path-sml.png)](xamarin-device-manager-images/win/34-config-file-path.png#lightbox)

3. Passare a questo percorso e fare doppio clic su **user.config** per aprirlo. 

4. In **user.config** trovare l'elemento **&lt;UserSettings&gt;** e aggiungere un attributo **AndroidSdkPath** a tale elemento. Impostare l'attributo sul percorso in cui è installato Android SDK nel computer in uso e salvare il file. Ad esempio **&lt;UserSettings&gt;** avrà un aspetto simile al seguente se Android SDK è stato installato in **C:\\Programmi\\Android\\SDK**:
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

Dopo aver modificato in questo modo **user.config** è possibile avviare Gestione dispositivi di Xamarin Android.

### <a name="snapshot-disables-wifi-on-android-oreo"></a>Uno snapshot disabilita l'accesso Wi-Fi in Android Oreo

In presenza di un dispositivo virtuale Android configurato per Android Oreo con accesso Wi-Fi simulato, il riavvio dell'AVD dopo uno snapshot può causare la disabilitazione dell'accesso Wi-Fi.

Per risolvere il problema:

1. Selezionare l'AVD in Gestione dispositivi di Xamarin.

2. Scegliere **Visualizza in Esplora risorse** dal menu delle opzioni aggiuntive.

3. Passare a **Snapshot > default_boot**.

4. Eliminare il file **snapshot.pb**:

    [![Percorso del file snapshot.pb](xamarin-device-manager-images/win/36-delete-snapshot-sml.png)](xamarin-device-manager-images/win/36-delete-snapshot.png#lightbox)

5. Riavviare l'AVD. 

Dopo aver apportato queste modifiche, l'AVD verrà riavviato in uno stato che consente di nuovo il funzionamento dell'accesso Wi-Fi.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

### <a name="snapshot-disables-wifi-on-android-oreo"></a>Uno snapshot disabilita l'accesso Wi-Fi in Android Oreo

In presenza di un dispositivo virtuale Android configurato per Android Oreo con accesso Wi-Fi simulato, il riavvio dell'AVD dopo uno snapshot può causare la disabilitazione dell'accesso Wi-Fi.

Per risolvere il problema:

1. Selezionare l'AVD in Gestione dispositivi di Xamarin.

2. Scegliere **Visualizza in Finder** dal menu delle opzioni aggiuntive.

3. Passare a **Snapshot > default_boot**.

4. Eliminare il file **snapshot.pb**:

    [![Percorso del file snapshot.pb](xamarin-device-manager-images/mac/36-delete-snapshot-sml.png)](xamarin-device-manager-images/mac/36-delete-snapshot.png#lightbox)

5. Riavviare l'AVD. 

Dopo aver apportato queste modifiche, l'AVD verrà riavviato in uno stato che consente di nuovo il funzionamento dell'accesso Wi-Fi.

-----


### <a name="generating-a-bug-report"></a>Generazione di un report sui bug

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se si rileva un problema in Gestione dispositivi di Xamarin Android che non può essere risolto con i suggerimenti elencati sopra, inoltrare un report sui bug facendo clic con il pulsante destro del mouse sulla barra del titolo e selezionando **Generate Bug Report** (Genera report sui bug):

![Voce di menu per l'inoltro di un report sui bug](xamarin-device-manager-images/win/35-bug-report.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Se si rileva un problema in Gestione dispositivi di Xamarin Android che non può essere risolto con i suggerimenti elencati sopra, inoltrare un report sui bug facendo clic su **Aiuto > Generate Bug Report** (Genera report sui bug):

![Voce di menu per l'inoltro di un report sui bug](xamarin-device-manager-images/mac/35-bug-report.png)

-----

 
 
## <a name="summary"></a>Riepilogo

Questa guida ha presentato Gestione dispositivi di Xamarin Android, disponibile in Visual Studio per Mac e in Xamarin for Visual Studio. Sono state presentate funzionalità essenziali come l'avvio e l'arresto dell'emulatore Android, la selezione di un dispositivo virtuale Android (AVD) per l'esecuzione, la creazione di nuovi dispositivi virtuali e la modifica di un dispositivo virtuale. È stato anche descritto come modificare le proprietà del profilo hardware per un'ulteriore personalizzazione.


## <a name="related-links"></a>Collegamenti correlati

- [Modifiche agli strumenti di Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Debug con l'emulatore di Android SDK](~/android/deploy-test/debugging/android-sdk-emulator/index.md)
- [Note sulla versione di SDK Tools (Google)](https://developer.android.com/studiohttps://developer.xamarin.com/releases/sdk-tools.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
