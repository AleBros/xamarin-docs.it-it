---
title: Gestione di dispositivi virtuali con Android Device Manager
description: Questo articolo spiega come usare Android Device Manager per creare e configurare dispositivi virtuali Android (AVD) che emulano i dispositivi fisici Android. È possibile usare questi dispositivi virtuali per eseguire e testare l'app anche senza un dispositivo fisico.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: 3dad36dc93f5c23e5d9ef1e05a1b9419b7a724b6
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855107"
---
# <a name="managing-virtual-devices-with-the-android-device-manager"></a>Gestione di dispositivi virtuali con Android Device Manager

_Questo articolo spiega come usare Android Device Manager per creare e configurare dispositivi virtuali Android (AVD) che emulano i dispositivi fisici Android. È possibile usare questi dispositivi virtuali per eseguire e testare l'app anche senza un dispositivo fisico._

Dopo la verifica dell'abilitazione dell'accelerazione hardware (come descritto in [Accelerazione hardware per le prestazioni dell'emulatore](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), il passaggio successivo consiste nell'usare _Android Device Manager_ (chiamato anche _Xamarin Android Device Manager_) per la creazione di dispositivi virtuali per il test e il debug dell'app.

::: zone pivot="windows"

## <a name="android-device-manager-on-windows"></a>Android Device Manager su Windows

Questo articolo spiega come usare Android Device Manager per creare, duplicare, personalizzare e avviare i dispositivi virtuali Android.

[![SScreenshot di Android Device Manager nella scheda Dispositivi(device-manager-images/win/01-devices-dialog-sml.png)](device-manager-images/win/01-devices-dialog.png#lightbox)

È possibile usare Android Device Manager per creare e configurare _dispositivi virtuali Android_ (AVD) eseguibili nell'[emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md).
Ogni AVD è una configurazione dell'emulatore che simula un dispositivo Android fisico. In questo modo è possibile eseguire e sottoporre a test l'applicazione in varie configurazioni che simulano dispositivi Android fisici diversi.

## <a name="requirements"></a>Requisiti

Per usare Android Device Manager sono necessari i componenti seguenti:

- Visual Studio 2019 Community, Professional, o Enterprise

- oppure Visual Studio 2017 versione 15.8 o versione successiva. Sono supportate le edizioni Visual Studio Community, Professional ed Enterprise.

- Strumenti di Visual Studio per Xamarin 4.9 o versione successiva.

- Deve essere installato Android SDK (vedere [Configurazione di Android SDK per Xamarin.Android](~/android/get-started/installation/android-sdk.md)).
  Verificare di installare Android SDK nel percorso predefinito (se non è già installato): **C:\\Programmi (x86)\\Android\\android-sdk**.

- I pacchetti seguenti devono essere installati usando [Android SDK Manager](~/android/get-started/installation/android-sdk.md): 
    - **Android SDK Tools 26.1.1** o versione successiva
    - **Android SDK Platform-Tools 27.0.1** o versione successiva
    - **Android SDK Build-Tools 27.0.3** o versione successiva 
    - **Emulatore Android 27.2.7** o versione successiva. 

  Questi pacchetti devono essere visualizzati con lo stato **Installato** come illustrato nello screenshot seguente:

  [![IInstallazione di Android SDK Tools(device-manager-images/win/02-sdk-tools-sml.png)](device-manager-images/win/02-sdk-tools.png#lightbox)


## <a name="launching-the-device-manager"></a>Avvio di Gestione dispositivi

Avviare Android Device Manager dal menu **Strumenti** facendo clic su **Strumenti > Android > Android Device Manager**:

[![LAvvio di Device Manager dal menu Strumenti(device-manager-images/win/03-tools-menu-sml.png)](device-manager-images/win/03-tools-menu.png#lightbox)

Se all'avvio viene visualizzato il messaggio di errore seguente, vedere la sezione [Risoluzione dei problemi](#troubleshooting) per informazioni sulle soluzioni alternative:

![Messaggio di errore di istanza di Android SDK](device-manager-images/win/04-sdk-error.png)


## <a name="main-screen"></a>Schermata principale

Al primo avvio di Gestione dispositivi di Xamarin Android una schermata visualizza tutti i dispositivi virtuali attualmente configurati. Per ogni dispositivo virtuale vengono visualizzati i parametri **Nome**, **Sistema operativo** (versione di Android), **Processore**, **Memoria** e la **Risoluzione** dello schermo:

[![LElenco dei dispositivi installati e relativi parametri(device-manager-images/win/05-installed-list-sml.png)](device-manager-images/win/05-installed-list.png#lightbox)

Quando si fa clic su un dispositivo nell'elenco, a destra del nome viene visualizzato il pulsante **Avvia**. Fare clic su **Avvia** per avviare l'emulatore con questo dispositivo virtuale:

[![SPulsante Avvia per un'immagine del dispositivo(device-manager-images/win/06-start-button-sml.png)](device-manager-images/win/06-start-button.png#lightbox)

Dopo che l'emulatore viene avviato con il dispositivo virtuale selezionato, il pulsante **Avvia** diventa **Arresta** e può essere usato per interrompere l'esecuzione dell'emulatore:

[![SPulsante Arresta per il dispositivo in esecuzione(device-manager-images/win/07-stop-button-sml.png)](device-manager-images/win/07-stop-button.png#lightbox)

### <a name="new-device"></a>Nuovo dispositivo

Per creare un nuovo dispositivo fare clic sul pulsante **Nuovo** nell'angolo superiore destro della schermata:

[![NPulsante Nuovo per creare un nuovo dispositivo(device-manager-images/win/08-new-button-sml.png)](device-manager-images/win/08-new-button.png#lightbox)

Quando si fa clic su **Nuovo** viene visualizzata la schermata **New Device** (Nuovo dispositivo):

[![NSchermata Nuovo dispositivo di Gestione dispositivi(device-manager-images/win/09-new-device-editor-sml.png)](device-manager-images/win/09-new-device-editor.png#lightbox)

Per configurare un nuovo dispositivo nella schermata **New Device** (Nuovo dispositivo) seguire questa procedura:

1. Assegnare un nuovo nome al dispositivo. Nell'esempio seguente, al nuovo dispositivo viene assegnato il nome **Pixel_API_27**:

   [![NAssegnazione di un nome al nuovo dispositivo(device-manager-images/win/10-device-name-sml.png)](device-manager-images/win/10-device-name.png#lightbox)

2. Selezionare un dispositivo fisico da emulare facendo clic sul menu a discesa **Dispositivo di base**:

   [![SSelezione del dispositivo fisico da emulare(device-manager-images/win/11-device-menu-sml.png)](device-manager-images/win/11-device-menu.png#lightbox)

3. Selezionare un tipo di processore per questo dispositivo virtuale facendo clic sul menu a discesa **Processore**. La selezione di **x86** migliora al massimo le prestazioni in quanto consente all'emulatore di sfruttare [l'accelerazione hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md).
   Anche l'opzione **x86_64** sfrutterà l'accelerazione hardware, ma funzionerà un po' più lentamente della **x86** (l'opzione **x86_64** in genere viene usata per testare app a 64-bit):

   [![SSelezione del tipo di processore(device-manager-images/win/12-processor-type-menu-sml.png)](device-manager-images/win/12-processor-type-menu.png#lightbox)

4. Selezionare la versione di Android (livello API) facendo clic sul menu a discesa **Sistema operativo**. Ad esempio, selezionare **Oreo 8.1 - API 27** per creare un dispositivo virtuale per il livello API 27:

   [![SSelezione della versione di Android(device-manager-images/win/13-android-version-w158-sml.png)](device-manager-images/win/13-android-version-w158.png#lightbox)

   Se si seleziona un livello API di Android non ancora installato, Device Manager visualizzerà il messaggio **A new device will be downloaded** (Verrà scaricato un nuovo dispositivo) nella parte inferiore della schermata &ndash; lo scaricherà e installerà i file necessari durante la creazione del nuovo dispositivo virtuale:

   ![A new device will be downloaded (Verrà scaricato un nuovo dispositivo)](device-manager-images/win/14-automatic-download-w158.png)

5. Se si desidera includere le API di Google Play Services nel dispositivo virtuale, abilitare l'opzione **Google APIs** (API Google). Per includere l'app Google Play Store, abilitare l'opzione **Google Play Store**:

   [![SSelezione di Google Play Services e Google Play Store(device-manager-images/win/15-google-play-services-sml.png)](device-manager-images/win/15-google-play-services.png#lightbox)

   Le immagini di Google Play Store sono disponibili solo per alcuni tipi di dispositivi di base, ad esempio Pixel, Pixel 2, Nexus 5 e Nexus 5X.

6. Modificare le proprietà in base alle esigenze. Per apportare modifiche alle proprietà, vedere [Modifica delle proprietà del dispositivo virtuale Android](~/android/get-started/installation/android-emulator/device-properties.md).

7. Aggiungere le eventuali proprietà addizionali che è necessario impostare in modo esplicito. La schermata **New Device** (Nuovo dispositivo) elenca solo le proprietà modificate più di frequente. Per aggiungere altre proprietà fare clic sul menu a discesa **Aggiungi proprietà** nella parte inferiore della schermata:

   [![AMenu a discesa Aggiungi proprietà(device-manager-images/win/16-add-property-menu-sml.png)](device-manager-images/win/16-add-property-menu.png#lightbox)

    È anche possibile definire una proprietà personalizzata selezionando **Personalizza...** nella parte superiore dell'elenco.

8. Fare clic sul pulsante **Crea** nell'angolo inferiore destro per creare il nuovo dispositivo:

   [![CPulsante Crea(device-manager-images/win/17-create-button-sml.png)](device-manager-images/win/17-create-button.png#lightbox)

9. È possibile che venga visualizzata una schermata **Accettazione della licenza**. Se si accettano le condizioni di licenza, fare clic su **Accetta**:

   [![LSchermata Accettazione della licenza(device-manager-images/win/18-license-acceptance-sml.png)](device-manager-images/win/18-license-acceptance.png#lightbox)

10. Android Device Manager aggiunge il nuovo dispositivo all'elenco dei dispositivi virtuali installati, visualizzando un indicatore di stato **Creating** (Creazione in corso) durante la creazione del dispositivo:

    [![CIndicatore di stato della creazione(device-manager-images/win/19-creating-the-device-sml.png)](device-manager-images/win/19-creating-the-device.png#lightbox)

11. Dopo aver completato il processo di creazione, il nuovo dispositivo viene visualizzato nell'elenco di dispositivi virtuali installati, con un pulsante **Avvia** che consente di avviarlo:

    [![NDispositivo appena creato pronto per l'avvio(device-manager-images/win/20-created-device-sml.png)](device-manager-images/win/20-created-device.png#lightbox)


### <a name="edit-device"></a>Modificare il dispositivo

Per modificare un dispositivo virtuale esistente, selezionare il dispositivo e fare clic sul pulsante **Modifica** nell'angolo superiore destro dello schermo:

[![EPulsante Modifica per modificare un dispositivo(device-manager-images/win/21-edit-button-sml.png)](device-manager-images/win/21-edit-button.png#lightbox)

Quando si fa clic su **Modifica** viene avviata la schermata Device Editor (Editor dispositivi) per il dispositivo virtuale selezionato:

[![DSchermata Device Editor (Editor dispositivi)(device-manager-images/win/22-device-editor-sml.png)](device-manager-images/win/22-device-editor.png#lightbox)

La schermata **Device Editor** (Editor dispositivi) elenca le proprietà del dispositivo virtuale nella colonna **Proprietà** e i valori corrispondenti di ogni proprietà nella colonna **Valore**. Quando si seleziona una proprietà, sul lato destro viene visualizzata una descrizione dettagliata di tale proprietà.

Per modificare una proprietà, modificarne il valore nella colonna **Valore**.
Ad esempio, nello screenshot seguente il valore della proprietà `hw.lcd.density` viene modificato da **480** a **240**:

[![DEsempio di modifica dispositivo(device-manager-images/win/23-device-editing-sml.png)](device-manager-images/win/23-device-editing.png#lightbox)

Dopo aver apportato le modifiche di configurazione necessarie, fare clic sul pulsante **Salva**.
Per altre informazioni sulla modifica delle proprietà del dispositivo virtuale, vedere [Modifica delle proprietà del dispositivo virtuale Android](~/android/get-started/installation/android-emulator/device-properties.md).


### <a name="additional-options"></a>Opzioni aggiuntive

Opzioni aggiuntive per l'uso dei dispositivi sono disponibili nel menu a discesa **Opzioni aggiuntive** (&hellip;) nell'angolo superiore destro della schermata:

[![LPosizione del menu delle opzioni aggiuntive(device-manager-images/win/24-overflow-menu-sml.png)](device-manager-images/win/24-overflow-menu.png#lightbox)

Il menu delle opzioni aggiuntive contiene le voci seguenti:

- **Duplicate and Edit** (Duplica e modifica): duplica il dispositivo selezionato attualmente e lo apre nella schermata **New Device** (Nuovo dispositivo) con un nome univoco diverso. Se ad esempio si seleziona **Pixel_API_27** e si fa clic su **Duplicate and Edit** (Duplica e modifica) viene accodato un contatore al nome del dispositivo:

  [![DSchermata Duplicate and Edit (Duplica e modifica)(device-manager-images/win/25-dupe-and-edit-sml.png)](device-manager-images/win/25-dupe-and-edit.png#lightbox)

- **Visualizza in Esplora risorse**: apre una finestra di Esplora risorse con la cartella contenente i file del dispositivo virtuale. Se ad esempio si seleziona **Pixel_API_27** e si fa clic su **Visualizza in Esplora risorse** viene visualizzata una finestra simile alla seguente:

  [![RRisultato del clic su Visualizza in Esplora risorse(device-manager-images/win/26-reveal-in-explorer-sml.png)](device-manager-images/win/26-reveal-in-explorer.png#lightbox)

- **Factory Reset** (Ripristino delle impostazioni predefinite): ripristina le impostazioni predefinite del dispositivo selezionato, eliminando tutte le modifiche apportate dall'utente allo stato interno del dispositivo mentre era in esecuzione (eliminando anche l'eventuale snapshot per l'[avvio rapido](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot) corrente). Questa modifica non annulla le modifiche apportate al dispositivo virtuale durante la creazione e la modifica. Un messaggio ricorda che questa operazione di reimpostazione non può essere annullata. Fare clic su **Ripristino delle impostazioni predefinite** per confermare la reimpostazione:

  ![Finestra di dialogo Ripristino delle impostazioni predefinite](device-manager-images/win/27-factory-reset.png)

- **Elimina**: elimina definitivamente il dispositivo virtuale selezionato. Un messaggio ricorda che l'eliminazione di un dispositivo non può essere annullata. Fare clic su **Elimina** se si è certi di voler eliminare il dispositivo.

  ![Finestra di dialogo Elimina dispositivo](device-manager-images/win/28-delete-device-w158.png)


::: zone-end
::: zone pivot="macos"

## <a name="android-device-manager-on-macos"></a>Android Device Manager su macOS

Questo articolo spiega come usare Android Device Manager per creare, duplicare, personalizzare e avviare i dispositivi virtuali Android.

[![SScreenshot di Android Device Manager nella scheda Dispositivi(device-manager-images/mac/01-devices-dialog-sml.png)](device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> Questa guida è applicabile solo a Visual Studio per Mac.
Xamarin Studio non è compatibile con Android Device Manager.

È possibile usare Android Device Manager per creare e configurare *dispositivi virtuali Android* (AVD) eseguibili nell'[emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md).
Ogni AVD è una configurazione dell'emulatore che simula un dispositivo Android fisico. In questo modo è possibile eseguire e sottoporre a test l'applicazione in varie configurazioni che simulano dispositivi Android fisici diversi.

## <a name="requirements"></a>Requisiti

Per usare Android Device Manager sono necessari i componenti seguenti:

- Visual Studio per Mac 7.6 o versione successiva.

- Deve essere installato Android SDK (vedere [Configurazione di Android SDK per Xamarin.Android](~/android/get-started/installation/android-sdk.md)).

- I pacchetti seguenti devono essere installati usando [Android SDK Manager](~/android/get-started/installation/android-sdk.md): 
    -  **SDK Tools 26.1.1** o versione successiva
    -  **Android SDK Platform-Tools 28.0.1** o versione successiva 
    -  **Android SDK Build-Tools 26.0.3** o versione successiva

  Questi pacchetti devono essere visualizzati con lo stato **Installato** come illustrato nello screenshot seguente:

  [![IInstallazione di Android SDK Tools(device-manager-images/mac/02-sdk-tools-sml.png)](device-manager-images/mac/02-sdk-tools.png#lightbox)


## <a name="launching-the-device-manager"></a>Avvio di Gestione dispositivi

Avviare Android Device Manager facendo clic su **Strumenti > Device Manager**:

[![LAvvio di Device Manager dal menu Strumenti(device-manager-images/mac/03-tools-menu-sml.png)](device-manager-images/mac/03-tools-menu.png#lightbox)

Se all'avvio viene visualizzato il messaggio di errore seguente, vedere la sezione [Risoluzione dei problemi](#troubleshooting) per informazioni sulle soluzioni alternative:

![Messaggio di errore di istanza di Android SDK](device-manager-images/mac/04-sdk-instance-error.png)


## <a name="main-screen"></a>Schermata principale

Al primo avvio di Gestione dispositivi di Xamarin Android una schermata visualizza tutti i dispositivi virtuali attualmente configurati. Per ogni dispositivo virtuale vengono visualizzati i parametri **Nome**, **Sistema operativo** (versione di Android), **Processore**, **Memoria** e la **Risoluzione** dello schermo:

[![LElenco dei dispositivi installati e relativi parametri(device-manager-images/mac/05-devices-list-sml.png)](device-manager-images/mac/05-devices-list.png#lightbox)

Quando si fa clic su un dispositivo nell'elenco, a destra del nome viene visualizzato il pulsante **Riproduci**. Fare clic su **Riproduci** per avviare l'emulatore con questo dispositivo virtuale:

[![PPulsante Riproduci per un'immagine del dispositivo(device-manager-images/mac/06-start-button-sml.png)](device-manager-images/mac/06-start-button.png#lightbox)

Dopo che l'emulatore viene avviato con il dispositivo virtuale selezionato, il pulsante **Play** (Riproduci) diventa **Arresta** e può essere usato per interrompere l'esecuzione dell'emulatore:

[![SPulsante Arresta per il dispositivo in esecuzione(device-manager-images/mac/07-stop-button-sml.png)](device-manager-images/mac/07-stop-button.png#lightbox)

Quando si arresta l'emulatore, si può ricevere un messaggio che chiede se si desidera salvare lo stato corrente per il prossimo avvio rapido:

![Salvare lo stato corrente per la finestra di dialogo Avvio rapido](device-manager-images/mac/08-save-for-quick-boot-m76.png)

Il salvataggio dello stato corrente accelererà l'avvio dell'emulatore al riavvio di questo dispositivo virtuale. Per altre informazioni sull'Avvio rapido, vedere [Avvio rapido](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot).

### <a name="new-device"></a>Nuovo dispositivo

Per creare un nuovo dispositivo fare clic sul pulsante **Nuovo dispositivo** (nell'angolo superiore a sinistra della schermata):

[![NPulsante Nuovo per creare un nuovo dispositivo(device-manager-images/mac/09-new-button-sml.png)](device-manager-images/mac/09-new-button.png#lightbox)

Quando si fa clic su **New Device** (Nuovo dispositivo) viene visualizzata la schermata **New Device** (Nuovo dispositivo):

[![NSchermata Nuovo dispositivo di Gestione dispositivi(device-manager-images/mac/10-new-device-editor-sml.png)](device-manager-images/mac/10-new-device-editor.png#lightbox)

Per configurare un nuovo dispositivo nella schermata **New Device** (Nuovo dispositivo) seguire questa procedura:

1. Assegnare un nuovo nome al dispositivo. Nell'esempio seguente, al nuovo dispositivo viene assegnato il nome **Pixel_API_27**:

   [![NAssegnazione di un nome al nuovo dispositivo(device-manager-images/mac/11-device-name-m76-sml.png)](device-manager-images/mac/11-device-name-m76.png#lightbox)

2. Selezionare un dispositivo fisico da emulare facendo clic sul menu a discesa **Dispositivo di base**:

   [![SSelezione del dispositivo fisico da emulare(device-manager-images/mac/12-device-menu-m76-sml.png)](device-manager-images/mac/12-device-menu-m76.png#lightbox)

3. Selezionare un tipo di processore per questo dispositivo virtuale facendo clic sul menu a discesa **Processore**. La selezione di **x86** migliora al massimo le prestazioni in quanto consente all'emulatore di sfruttare [l'accelerazione hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md).
   Anche l'opzione **x86_64** sfrutterà l'accelerazione hardware, ma funzionerà un po' più lentamente della **x86** (l'opzione **x86_64** in genere viene usata per testare app a 64-bit):

   [![SSelezione del tipo di processore(device-manager-images/mac/13-processor-type-menu-m76-sml.png)](device-manager-images/mac/13-processor-type-menu-m76.png#lightbox)

4. Selezionare la versione di Android (livello API) facendo clic sul menu a discesa **Sistema operativo**. Ad esempio, selezionare **Oreo 8.1 - API 27** per creare un dispositivo virtuale per il livello API 27:

   [![SSelezione della versione di Android(device-manager-images/mac/14-android-screenshot-m76-sml.png)](device-manager-images/mac/14-android-screenshot-m76.png#lightbox)

   Se si seleziona un livello API di Android non ancora installato, Device Manager visualizzerà il messaggio **A new device will be downloaded** (Verrà scaricato un nuovo dispositivo) nella parte inferiore della schermata &ndash; lo scaricherà e installerà i file necessari durante la creazione del nuovo dispositivo virtuale:

   ![A new device will be downloaded (Verrà scaricato un nuovo dispositivo)](device-manager-images/mac/15-automatic-download-m76.png)

5. Se si desidera includere le API di Google Play Services nel dispositivo virtuale, abilitare l'opzione **Google APIs** (API Google). Per includere l'app Google Play Store, abilitare l'opzione **Google Play Store**:

   [![SSelezione di Google Play Services e Google Play Store(device-manager-images/mac/16-google-play-services-m76-sml.png)](device-manager-images/mac/16-google-play-services-m76.png#lightbox)

   Le immagini di Google Play Store sono disponibili solo per alcuni tipi di dispositivi di base, ad esempio Pixel, Pixel 2, Nexus 5 e Nexus 5X.

6. Modificare le proprietà in base alle esigenze. Per apportare modifiche alle proprietà, vedere [Modifica delle proprietà del dispositivo virtuale Android](~/android/get-started/installation/android-emulator/device-properties.md).

7. Aggiungere le eventuali proprietà addizionali che è necessario impostare in modo esplicito. La schermata **New Device** (Nuovo dispositivo) elenca solo le proprietà modificate più di frequente. Per aggiungere altre proprietà fare clic sul menu a discesa **Aggiungi proprietà** nella parte inferiore della schermata:

   [![AMenu a discesa Aggiungi proprietà(device-manager-images/mac/17-add-property-menu-m76-sml.png)](device-manager-images/mac/17-add-property-menu-m76.png#lightbox)

   È anche possibile definire una proprietà personalizzata facendo clic su **Personalizza...** nella parte superiore dell'elenco.

8. Fare clic sul pulsante **Crea** nell'angolo inferiore destro per creare il nuovo dispositivo:

   ![Pulsante Crea](device-manager-images/mac/18-create-button-m76.png)

9. Android Device Manager aggiunge il nuovo dispositivo all'elenco dei dispositivi virtuali installati, visualizzando un indicatore di stato **Creating** (Creazione in corso) durante la creazione del dispositivo:

   [![CIndicatore di stato della creazione(device-manager-images/mac/19-creating-the-device-m76-sml.png)](device-manager-images/mac/19-creating-the-device-m76.png#lightbox)

10. Dopo aver completato il processo di creazione, il nuovo dispositivo viene visualizzato nell'elenco di dispositivi virtuali installati, con un pulsante **Avvia** che consente di avviarlo:

    [![NDispositivo appena creato pronto per l'avvio(device-manager-images/mac/20-created-device-m76-sml.png)](device-manager-images/mac/20-created-device-m76.png#lightbox)


### <a name="edit-device"></a>Modificare il dispositivo

Per modificare un dispositivo virtuale esistente, selezionare il menu a discesa **Opzioni aggiuntive** (icona a forma di ingranaggio) e selezionare **Modifica**:

[![EComando di menu Modifica per la modifica di un nuovo dispositivo(device-manager-images/mac/21-edit-button-m76-sml.png)](device-manager-images/mac/21-edit-button-m76.png#lightbox)

Quando si fa clic su **Modifica** viene avviata la schermata Device Editor (Editor dispositivi) per il dispositivo virtuale selezionato:

[![DSchermata Device Editor (Editor dispositivi)(device-manager-images/mac/22-device-editor-sml.png)](device-manager-images/mac/22-device-editor.png#lightbox)

La schermata **Device Editor** (Editor dispositivi) elenca le proprietà del dispositivo virtuale nella colonna **Proprietà** e i valori corrispondenti di ogni proprietà nella colonna **Valore**. Quando si seleziona una proprietà, sul lato destro viene visualizzata una descrizione dettagliata di tale proprietà.

Per modificare una proprietà, modificarne il valore nella colonna **Valore**.
Ad esempio, nello screenshot seguente il valore della proprietà `hw.lcd.density` viene modificato da **480** a **240**:

[![DEsempio di modifica dispositivo(device-manager-images/mac/23-device-editing-sml.png)](device-manager-images/mac/23-device-editing.png#lightbox)

Dopo aver apportato le modifiche di configurazione necessarie, fare clic sul pulsante **Salva**.
Per altre informazioni sulla modifica delle proprietà del dispositivo virtuale, vedere [Modifica delle proprietà del dispositivo virtuale Android](~/android/get-started/installation/android-emulator/device-properties.md).


### <a name="additional-options"></a>Opzioni aggiuntive

Opzioni aggiuntive per l'uso dei dispositivi sono disponibili nel menu a discesa visualizzato a sinistra del pulsante **Play** (Riproduci):

[![LPosizione del menu delle opzioni aggiuntive(device-manager-images/mac/24-overflow-menu-sml.png)](device-manager-images/mac/24-overflow-menu.png#lightbox)

Il menu delle opzioni aggiuntive contiene le voci seguenti:

- **Edit** (Modifica): apre il dispositivo attualmente selezionato nell'editor dispositivi, come descritto in precedenza.

- **Duplicate and Edit** (Duplica e modifica): duplica il dispositivo selezionato attualmente e lo apre nella schermata **New Device** (Nuovo dispositivo) con un nome univoco diverso. Se ad esempio si seleziona **Pixel 2 API 28** e si fa clic su **Duplicate and Edit** (Duplica e modifica) viene accodato un contatore al nome del dispositivo:

  [![DSchermata Duplicate and Edit (Duplica e modifica)(device-manager-images/mac/25-dupe-and-edit-sml.png)](device-manager-images/mac/25-dupe-and-edit.png#lightbox)

- **Visualizza in Finder**: apre una finestra del Finder di macOS con la cartella contenente i file del dispositivo virtuale. Se ad esempio si seleziona **Pixel 2 API 28** e si fa clic su **Reveal in Finder** (Visualizza in Finder) viene visualizzata una finestra simile alla seguente:

  [![RRisultato della selezione di Visualizza in Finder(device-manager-images/mac/26-reveal-in-finder-sml.png)](device-manager-images/mac/26-reveal-in-finder.png#lightbox)

- **Factory Reset** (Ripristino delle impostazioni predefinite): ripristina le impostazioni predefinite del dispositivo selezionato, eliminando tutte le modifiche apportate dall'utente allo stato interno del dispositivo mentre era in esecuzione (eliminando anche l'eventuale snapshot per l'[avvio rapido](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot) corrente). Questa modifica non annulla le modifiche apportate al dispositivo virtuale durante la creazione e la modifica. Un messaggio ricorda che questa operazione di reimpostazione non può essere annullata. Fare clic su **Ripristino delle impostazioni predefinite** per confermare la reimpostazione.

  ![Finestra di dialogo Ripristino delle impostazioni predefinite](device-manager-images/mac/27-factory-reset-m76.png)

- **Elimina**: elimina definitivamente il dispositivo virtuale selezionato. Un messaggio ricorda che l'eliminazione di un dispositivo non può essere annullata. Fare clic su **Elimina** se si è certi di voler eliminare il dispositivo.

  ![Finestra di dialogo Elimina dispositivo](device-manager-images/mac/28-delete-device-m76.png)

-----


<a name="troubleshooting" />

## <a name="troubleshooting"></a>Risoluzione dei problemi

Le sezioni seguenti descrivono come diagnosticare e risolvere i problemi che possono verificarsi quando si usa Android Device Manager per configurare i dispositivi virtuali.

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

### <a name="android-sdk-in-non-standard-location"></a>Android SDK in un percorso non standard

In genere, Android SDK viene installato nel percorso seguente:

**C:\\Programmi (x86)\\Android\\android-sdk**

Se l'SDK non viene installato in questa posizione, è possibile che venga visualizzato il messaggio di errore seguente all'avvio di Android Device Manager:

![Errore di istanza di Android SDK](device-manager-images/win/29-sdk-error.png)

Per risolvere il problema, effettuare i passaggi seguenti:

1. Nel desktop di Windows passare a **C:\\Utenti\\*nomeutente*\\AppData\\Roaming\\XamarinDeviceManager**:

   ![Percorso del file di log di Android Device Manager](device-manager-images/win/30-log-files.png)

2. Fare doppio clic per aprire uno dei file di log e trovare il **percorso del file di configurazione**. Ad esempio:

   [![CPercorso del file di configurazione nel file di log(device-manager-images/win/31-config-file-path-sml.png)](device-manager-images/win/31-config-file-path.png#lightbox)

3. Passare a questo percorso e fare doppio clic su **user.config** per aprirlo.

4. In **user.config** trovare l'elemento `<UserSettings>` e aggiungere un attributo **AndroidSdkPath** a tale elemento. Impostare l'attributo sul percorso in cui è installato Android SDK nel computer in uso e salvare il file. Ad esempio `<UserSettings>` avrà un aspetto simile al seguente se Android SDK è stato installato in **C:\\Programmi\\Android\\SDK**:

   ```xml
   <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:ProgramsAndroidSDK" />
   ```

Dopo aver modificato in questo modo **user.config**, è possibile avviare Android Device Manager.


### <a name="wrong-version-of-android-sdk-tools"></a>Versione errata di Android SDK Tools

Se non è installato Android SDK Tools 26.1.1 o versione successiva, all'avvio potrebbe essere visualizzato il messaggio di errore seguente:

![Messaggio di errore di istanza di Android SDK](device-manager-images/win/32-sdk-instance-error.png)

Se viene visualizzato questo messaggio di errore, fare clic su **Apri SDK Manager** per aprire Android SDK Manager. In Android SDK Manager fare clic sulla scheda **Tools** (Strumenti) e installare i pacchetti seguenti:

- **Android SDK Tools 26.1.1** o versione successiva
- **Android SDK Platform-Tools 27.0.1** o versione successiva
- **Android SDK Build-Tools 27.0.3** o versione successiva


### <a name="snapshot-disables-wifi-on-android-oreo"></a>Uno snapshot disabilita l'accesso Wi-Fi in Android Oreo

In presenza di un dispositivo virtuale Android configurato per Android Oreo con accesso Wi-Fi simulato, il riavvio dell'AVD dopo uno snapshot può causare la disabilitazione dell'accesso Wi-Fi.

Per risolvere il problema:

1. Selezionare l'AVD in Android Device Manager.

2. Scegliere **Visualizza in Esplora risorse** dal menu delle opzioni aggiuntive.

3. Passare a **Snapshot > default_boot**.

4. Eliminare il file **snapshot.pb**:

   ![Percorso del file snapshot.pb](device-manager-images/win/33-delete-snapshot.png)

5. Riavviare l'AVD.

Dopo aver apportato queste modifiche, l'AVD verrà riavviato in uno stato che consente di nuovo il funzionamento dell'accesso Wi-Fi.


# [<a name="visual-studio-for-mac"></a>Visual Studio per Mac](#tab/macos)

### <a name="wrong-version-of-android-sdk-tools"></a>Versione errata di Android SDK Tools

Se non è installato Android SDK Tools 26.1.1 o versione successiva, all'avvio potrebbe essere visualizzato il messaggio di errore seguente:

![Messaggio di errore di istanza di Android SDK](device-manager-images/mac/29-sdk-instance-error.png)

Se viene visualizzato questo messaggio di errore, fare clic su **OK** per aprire Android SDK Manager. In Android SDK Manager fare clic sulla scheda **Tools** (Strumenti) e installare i pacchetti seguenti:

- **Android SDK Tools 26.1.1** o versione successiva
- **Android SDK Platform-Tools 28.0.1** o versione successiva
- **Android SDK Build-Tools 26.0.3** o versione successiva

### <a name="snapshot-disables-wifi-on-android-oreo"></a>Uno snapshot disabilita l'accesso Wi-Fi in Android Oreo

In presenza di un dispositivo virtuale Android configurato per Android Oreo con accesso Wi-Fi simulato, il riavvio dell'AVD dopo uno snapshot può causare la disabilitazione dell'accesso Wi-Fi.

Per risolvere il problema:

1. Selezionare l'AVD in Android Device Manager.

2. Scegliere **Visualizza in Finder** dal menu delle opzioni aggiuntive.

3. Passare a **Snapshot > default_boot**.

4. Eliminare il file **snapshot.pb**:

   [![LPercorso del file snapshot.pb(device-manager-images/mac/30-delete-snapshot-sml.png)](device-manager-images/mac/30-delete-snapshot.png#lightbox)

5. Riavviare l'AVD.

Dopo aver apportato queste modifiche, l'AVD verrà riavviato in uno stato che consente di nuovo il funzionamento dell'accesso Wi-Fi.

-----

### <a name="generating-a-bug-report"></a>Generazione di un report sui bug

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

Se si rileva un problema in Android Device Manager che non può essere risolto con i suggerimenti sopra elencati, inoltrare un report sui bug facendo clic con il pulsante destro del mouse sulla barra del titolo e selezionando **Generate Bug Report** (Genera report sui bug):

[![LVoce di menu per l'inoltro di un report sui bug(device-manager-images/win/34-bug-report-sml.png)](device-manager-images/win/34-bug-report.png#lightbox)


# [<a name="visual-studio-for-mac"></a>Visual Studio per Mac](#tab/macos)

Se si rileva un problema in Android Device Manager che non può essere risolto con i suggerimenti sopra elencati, inoltrare un report sui bug facendo clic su **Help > Report a Problem** (Aiuto > Segnala un problema):

[![LVoce di menu per l'inoltro di un report sui bug(device-manager-images/mac/31-bug-report-sml.png)](device-manager-images/mac/31-bug-report.png#lightbox)

::: zone-end

## <a name="summary"></a>Riepilogo

Questa guida ha presentato Android Device Manager, disponibile in Strumenti di Visual Studio per Xamarin e Visual Studio per Mac. Sono state presentate funzionalità essenziali come l'avvio e l'arresto dell'emulatore Android, la selezione di un dispositivo virtuale Android (AVD) per l'esecuzione, la creazione di nuovi dispositivi virtuali e la modifica di un dispositivo virtuale. È stato illustrato come modificare le proprietà di profilo hardware per un'ulteriore personalizzazione e sono stati offerti suggerimenti per la risoluzione dei problemi comuni.


## <a name="related-links"></a>Collegamenti correlati

- [Modifiche agli strumenti di Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md)
- [Note sulla versione di SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/How-to-Create-and-Manage-Your-Own-Android-Emulators/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
