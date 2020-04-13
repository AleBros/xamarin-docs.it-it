---
title: Provisioning manuale per Xamarin.iOS
description: Dopo aver installato correttamente Xamarin.iOS, il passaggio successivo nello sviluppo iOS consiste nell'eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso del provisioning manuale per configurare profili e certificati di sviluppo.
ms.prod: xamarin
ms.assetid: E26ACC94-F4A5-4FF5-B7D4-BE596745A665
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/06/2020
ms.openlocfilehash: 04cb1b9303e571b2a10cdfa621dcd312162e2893
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304863"
---
# <a name="manual-provisioning-for-xamarinios"></a>Provisioning manuale per Xamarin.iOS

_Una volta che Xamarin.iOS è stato installato con successo, il passo successivo nello sviluppo di iOS è quello di eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso del provisioning manuale per configurare i certificati e i profili di sviluppo._

> [!NOTE]
> Le istruzioni in questa pagina sono utili per gli sviluppatori che hanno l'accesso a pagamento al programma Apple Developer. Se si ha un account gratuito, vedere la guida [Provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md) per altre informazioni sui test dei dispositivi.

## <a name="create-a-development-certificate"></a>Creare un certificato di sviluppo

Il primo passaggio nella configurazione di un dispositivo di sviluppo consiste nel creare un certificato di firma. Un certificato di firma è costituito da due elementi:A signing certificate consists of two things:

- Un certificato di sviluppo
- Una chiave privata

I certificati di sviluppo e le [chiavi](#understanding-certificate-key-pairs) associate sono fondamentali per lo sviluppatore di iOS: essi stabiliscono l'identità dell'utente con Apple e lo associano a un determinato dispositivo e profilo per lo sviluppo ed equivalgono all'inserimento della propria firma digitale sulle applicazioni. Apple verifica i certificati per controllare l'accesso ai dispositivi che è possibile distribuire.

I team di sviluppo, i certificati e i profili possono essere gestiti mediante l'accesso alla sezione [Certificates, Identifiers & Profiles](https://developer.apple.com/account/resources/certificates/list) (Certificati, identificatori e profili) di Apple's Members Center (accesso richiesto). Apple richiede un'identità di firma per compilare il proprio codice per il dispositivo o simulatore.  

> [!IMPORTANT]
> È importante notare che possono essere presenti solo due certificati di sviluppo iOS per volta. Se è necessario crearne altri, occorre revocarne uno esistente. Un computer con un certificato revocato non sarà in grado di firmare le app.

Prima di avviare il processo di provisioning manuale, devi assicurarti di avere aggiunto un account sviluppatore Apple in Visual Studio, come descritto nella guida [Gestione account Apple.](~/cross-platform/macios/apple-account-management.md) Dopo aver aggiunto il tuo account sviluppatore Apple, esegui le operazioni seguenti per generare un certificato di firma:

1. Vai alla finestra Account per sviluppatori Apple in Visual Studio.
    1. Mac: **Preferenze di Visual Studio > > account sviluppatore Apple**
    2. Windows: **Opzioni di > degli strumenti > account Xamarin > Apple**

2. Selezionare un team e fare clic su **Visualizza dettagli...**
3. Fare clic su **Crea certificato** e selezionare **Sviluppo Apple** o **Sviluppo iOS**. Se si dispone delle autorizzazioni corrette, verrà visualizzata una nuova identità di firma dopo alcuni secondi.

### <a name="understanding-certificate-key-pairs"></a>Coppie di chiavi del certificato

Il profilo sviluppatore contiene i certificati, le relative chiavi e i profili di provisioning associati all'account. Esistono effettivamente due versioni di un profilo sviluppatore, una sul portale per sviluppatori e l'altra su un computer Mac locale. La differenza tra i due è il tipo di chiavi contenute: _il profilo nel portale contiene tutte le chiavi pubbliche associate ai certificati, mentre la copia nel computer Mac locale contiene tutte le chiavi private_. Affinché il certificato sia valido, le coppie di chiavi devono corrispondere.

> [!WARNING]
> La perdita del certificato e delle chiavi associate può essere incredibilmente dirompente, in quanto richiede la revoca dei certificati esistenti e il nuovo provisioning di tutti i dispositivi associati, inclusi quelli registrati per la distribuzione ad hoc. Dopo aver impostato correttamente i certificati di sviluppo, esportare una copia di backup e archiviarla in un luogo sicuro. Per altre informazioni su come eseguire questa operazione, vedere la sezione Exporting and Importing Certificates and Profiles (Esportazione e importazione di certificati e profili) della guida [Maintaining Certificates](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html) (Manutenzione dei certificati) nella documentazione di Apple.

<a name="provisioning" />

## <a name="provision-an-ios-device-for-development"></a>Effettuare il provisioning di un dispositivo iOS per lo sviluppoProvision an iOS device for development

Ora che hai stabilito la tua identità con Apple e hai un certificato di sviluppo, devi configurare un profilo di provisioning e le entità necessarie in modo che sia possibile distribuire un'app in un dispositivo Apple. Nel dispositivo deve essere in esecuzione una versione di iOS supportata da Xcode; potrebbe essere necessario aggiornare il dispositivo, Xcode o entrambi.

<a name="adddevice" />

## <a name="add-a-device"></a>Aggiungere un dispositivo

Quando si crea un profilo di provisioning per lo sviluppo, è necessario indicare quali dispositivi possono eseguire l'applicazione. A tale scopo, è possibile aggiungere fino a 100 dispositivi per anno di calendario al proprio portale per sviluppatori e da qui è possibile selezionare i dispositivi da aggiungere a un determinato profilo di provisioning. Completare i passaggi seguenti nel Mac per aggiungere un dispositivo al portale per sviluppatori

1. Connettere il dispositivo per il quale eseguire il provisioning al compuer Mac con il cavo USB in dotazione.
2. Aprire Xcode e passare a **Finestra > dispositivi e simulatori**.
3. Nella scheda **Dispositivi,** selezionare il dispositivo dal menu a sinistra.
4. Evidenziare la stringa **Identifier** (Identificatore) e copiarla negli appunti:

   ![Dispositivi Xcode e finestra simulatore con la posizione della stringa identificatore iOS evidenziata.](manual-provisioning-images/xcode-devices.png)

5. In un Web browser passare alla [sezione Dispositivi nel portale per sviluppatori](https://developer.apple.com/account/resources/devices/list) e fare clic sul **+** pulsante:

   ![Screenshot della pagina dei dispositivi nel sito apple Developer con il pulsante Aggiungi evidenziato.](manual-provisioning-images/developer-portal-devices.png)

6. Impostare la **piattaforma** corretta e fornire un nome per il nuovo dispositivo. Incollare l'identificatore copiato in precedenza nel campo **ID dispositivo:**

    ![Screenshot della pagina di registrazione del nuovo dispositivo con i campi popolati correttamente.](manual-provisioning-images/new-device-info.png)

7. Fare clic su **Continua**.
8. Esaminare le informazioni, quindi fare clic su **Registra**.

Ripetere i passaggi precedenti per tutti i dispositivi iOS che verranno usati per testare o eseguire il debug di un'applicazione Xamarin.iOS.

<a name="provisioningprofile" />

## <a name="create-a-development-provisioning-profile"></a>Creare un profilo di provisioning di sviluppoCreate a development provisioning profile

Dopo aver aggiunto il dispositivo al portale per sviluppatori, è necessario creare un profilo di provisioning e aggiungervi il dispositivo. 

Prima di creare un profilo di provisioning, è necessario creare un *App ID* (ID app). Un ID app è una stringa di tipo DNS inverso che identifica in modo univoco un'applicazione. Nella procedura seguente viene illustrato come creare un **Wildcard App ID** (ID app con caratteri jolly), che consente di compilare e installare la maggior parte delle applicazioni. Gli **Explicit App IDs** (ID app espliciti) consentono di installare solo un'applicazione (con l'ID del bundle corrispondente) e vengono in genere usati per determinate funzionalità di iOS, ad esempio Apple Pay e HealthKit. Per informazioni sulla creazione di ID app espliciti, vedere la guida [Working with Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Uso delle funzionalità).

### <a name="new-wildcard-app-id"></a>Nuovo ID applicazione con caratteri jolly

1. Passare alla [sezione Identificatori nel portale per sviluppatori](https://developer.apple.com/account/resources/identifiers/list) e fare clic sul **+** pulsante.
2. Selezionare **ID app** e fare clic su **Continua**.
3. Specificare una **descrizione**. Quindi impostare **Bundle ID** su **Wildcard** `com.[DomainName].*`e immettere un ID nel formato :

   ![Screenshot della nuova pagina di registrazione dell'ID app con i campi obbligatori popolati.](manual-provisioning-images/new-app-id.png)

4. Fare clic su **Continua**.
5. Esaminare le informazioni, quindi fare clic su **Registra**.

### <a name="new-provisioning-profile"></a>Nuovo profilo di provisioning

Dopo aver creato l'ID app, è possibile creare il profilo di provisioning. Questo profilo di *what* provisioning contiene informazioni sull'app (o le app, se si tratta di un ID app con caratteri jolly) a cui si riferisce questo profilo, *a chi* può usare il profilo (a seconda dei certificati per sviluppatori aggiunti) e a *quali* dispositivi possono installare l'app.

Per creare manualmente un profilo di provisioning per lo sviluppo, eseguire le operazioni seguenti:To manually create a provisioning profile for development, do the following:

1. Passare alla [sezione Profili nel portale per sviluppatori](https://developer.apple.com/account/resources/profiles/list) e fare clic sul **+** pulsante.

2. In **Sviluppo**selezionare **Sviluppo app iOS** e fare clic su **Continua**.

3. Selezionare l'ID app da utilizzare dal menu a discesa, quindi fare clic su **Continua**.

4. Selezionare i certificati da includere nel profilo di provisioning, quindi fare clic su **Continua**.

5. Selezionare tutti i dispositivi su cui verrà installata l'app, quindi fare clic su **Continua**.

6. Specificare il nome del profilo di **provisioning,** quindi fare clic su **Genera**.

7. Se lo si desidera, puoi fare clic su **Scarica** nella pagina successiva per scaricare il profilo di provisioning sul Mac.

<a name="download" />

## <a name="download-provisioning-profiles-in-visual-studio"></a>Scaricare i profili di provisioning in Visual StudioDownload provisioning profiles in Visual Studio

Dopo aver creato un nuovo profilo di provisioning nel portale per sviluppatori Apple, usa Visual Studio per scaricarlo in modo che sia disponibile per l'accesso del bundle nella tua app.

1. Vai alla finestra Account per sviluppatori Apple in Visual Studio.
    1. Mac: **Preferenze di Visual Studio > > account sviluppatore Apple**
    2. Windows: **Opzioni di > degli strumenti > account Xamarin > Apple**

2. Selezionare il team e fare clic su **Visualizza dettagli...**
3. Verificare che il nuovo profilo venga visualizzato nell'elenco **Profili di provisioning.** Potrebbe essere necessario riavviare Visual Studio per aggiornare l'elenco. 
4. Fare clic su **Scarica tutti i profili**.

Il nuovo profilo di provisioning sarà ora disponibile in Visual Studio e pronto per l'uso.

## <a name="deploy-to-a-device"></a>Eseguire la distribuzione in un dispositivo

A questo punto il provisioning è completo e l'applicazione è pronta per essere distribuita al dispositivo. A questo scopo, seguire questa procedura:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Collegare il dispositivo al Mac.
2. Apri **Info.plist** e assicurati che **l'identificatore** del bundle corrisponda all'ID app creato in precedenza (a meno che l'ID app non sia un carattere jolly).
3. Nella sezione **Firma** selezionare **Provisioning manuale** come **Schema:**

    ![Screenshot di Info.plist in Visual Studio per Mac con il provisioning manuale selezionato](manual-provisioning-images/vsm-info-plist.png)

4. Fare clic su **Opzioni di firma bundle...**
5. Assicurarsi che la configurazione di compilazione sia impostata su **Debug , iPhone**. Aprire entrambi i menu a discesa **Identità** di firma e **Profilo di provisioning** per verificare che siano elencati i certificati e i profili di provisioning corretti: 

   ![La pagina delle proprietà di firma del bundle iOS con il menu a discesa del profilo di provisioning apre che elenca tutti i profili di provisioning disponibili per l'app.](manual-provisioning-images/vsm-bundle-signing.png)

6. Selezionare un'identità e un profilo specifici da utilizzare oppure lasciarli **come Automatico**. Se impostato su **Automatico**, Visual Studio per Mac selezionerà l'identità e il profilo in base **all'identificatore** del pacchetto in **Info.plist**. 
7. Fare clic su **OK**.
8. Fare clic su **Esegui** per distribuire l'app nel dispositivo.


# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Collegare il dispositivo all'host di compilazione Mac.
2. Apri **Info.plist** e assicurati che **l'identificatore** del bundle corrisponda all'ID app creato in precedenza (a meno che l'ID app non sia un carattere jolly).
3. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto iOS, selezionare Proprietà e passare alla scheda **Firma bundle iOS .In** the Solution Explorer , right-click the iOS project name, select **Properties** and navigate to the iOS Bundle Signing tab.
4. Assicurarsi che la configurazione di compilazione sia impostata su **Debug , iPhone**. In **Firma pacchetto**selezionare Provisioning **manuale** come **Schema**:

    ![Screenshot di Info.plist in Visual Studio per Mac con il provisioning manuale selezionato](manual-provisioning-images/vs-bundle-signing.png)

5. Aprire entrambi i menu a discesa **Identità** di firma e **Profilo di provisioning** per verificare che siano elencati i certificati e i profili di provisioning corretti.
6. Selezionare un'identità e un profilo specifici da utilizzare oppure lasciarli **come Automatico**. Se impostato su **Automatico**, Visual Studio selezionerà l'identità e il profilo in base **all'identificatore** del pacchetto in **Info.plist**. 
7. Fare clic su **Esegui** per distribuire l'app nel dispositivo.

-----

## <a name="provisioning-for-application-services"></a>Provisioning dei servizi delle applicazioni

Apple offre una selezione di servizi speciali per le applicazioni, denominati anche funzionalità, che possono essere attivati per un'applicazione Xamarin.iOS. Questi servizi per le applicazioni devono essere configurati sia nel portale di provisioning iOS quando viene creato l'**ID app** sia nel file **Entitlements.plist** che è parte del progetto dell'applicazione Xamarin.iOS. Per informazioni sull'aggiunta dei servizi per le applicazioni all'app, vedere la guida [Introduction to Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Introduzione alle funzionalità) e la guida [Working with Entitlements](~/ios/deploy-test/provisioning/entitlements.md) (Uso degli entitlement).

- Creare un ID app con i servizi app necessari.
- Creare un nuovo [profilo di provisioning](#provisioningprofile) contenente tale ID app.
- Impostare gli entitlement nel progetto Xamarin.iOS

## <a name="related-links"></a>Collegamenti correlati

- [Provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuzione app](~/ios/deploy-test/app-distribution/index.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
- [Apple - App Distribution Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) (Apple - Guida alla distribuzione dell'app)
