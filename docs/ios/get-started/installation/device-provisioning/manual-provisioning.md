---
title: Provisioning manuale per Xamarin.iOS
description: Dopo aver installato correttamente Xamarin.iOS, il passaggio successivo nello sviluppo iOS consiste nell'eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso del provisioning manuale per configurare profili e certificati di sviluppo.
ms.prod: xamarin
ms.assetid: E26ACC94-F4A5-4FF5-B7D4-BE596745A665
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/06/2020
ms.openlocfilehash: 9333750432395d008a5454e293648f4e594ae112
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571688"
---
# <a name="manual-provisioning-for-xamarinios"></a>Provisioning manuale per Xamarin.iOS

_Dopo aver installato correttamente Novell. iOS, il passaggio successivo per lo sviluppo di iOS consiste nel provisioning del dispositivo iOS. Questa guida esamina l'uso del provisioning manuale per configurare i profili e i certificati di sviluppo._

> [!NOTE]
> Le istruzioni in questa pagina sono utili per gli sviluppatori che hanno l'accesso a pagamento al programma Apple Developer. Se si ha un account gratuito, vedere la guida [Provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md) per altre informazioni sui test dei dispositivi.

## <a name="create-a-development-certificate"></a>Creare un certificato di sviluppo

Il primo passaggio per la configurazione di un dispositivo di sviluppo consiste nel creare un certificato di firma. Un certificato di firma è costituito da due elementi:

- Un certificato di sviluppo
- Una chiave privata

I certificati di sviluppo e le [chiavi](#understanding-certificate-key-pairs) associate sono fondamentali per lo sviluppatore di iOS: essi stabiliscono l'identità dell'utente con Apple e lo associano a un determinato dispositivo e profilo per lo sviluppo ed equivalgono all'inserimento della propria firma digitale sulle applicazioni. Apple verifica i certificati per controllare l'accesso ai dispositivi che è possibile distribuire.

I team di sviluppo, i certificati e i profili possono essere gestiti mediante l'accesso alla sezione [Certificates, Identifiers & Profiles](https://developer.apple.com/account/resources/certificates/list) (Certificati, identificatori e profili) di Apple's Members Center (accesso richiesto). Apple richiede un'identità di firma per compilare il proprio codice per il dispositivo o simulatore.  

> [!IMPORTANT]
> È importante notare che possono essere presenti solo due certificati di sviluppo iOS per volta. Se è necessario crearne altri, occorre revocarne uno esistente. Un computer con un certificato revocato non sarà in grado di firmare le app.

Prima di avviare il processo di provisioning manuale, è necessario assicurarsi di avere aggiunto un account per sviluppatore Apple in Visual Studio, come descritto nella Guida alla [gestione degli account Apple](~/cross-platform/macios/apple-account-management.md) . Dopo aver aggiunto l'account per sviluppatore Apple, eseguire le operazioni seguenti per generare un certificato di firma:

1. Passare alla finestra Apple Developer Accounts in Visual Studio.
    1. Mac: **Preferenze di Visual Studio > > account per sviluppatore Apple**
    2. Windows: **strumenti > opzioni > novell > account Apple**

2. Selezionare un team e fare clic su **Visualizza dettagli...**
3. Fare clic su **Crea certificato** e selezionare **sviluppo Apple** o **sviluppo iOS**. Se si dispone delle autorizzazioni corrette, dopo alcuni secondi verrà visualizzata una nuova identità di firma.

### <a name="understanding-certificate-key-pairs"></a>Coppie di chiavi del certificato

Il profilo sviluppatore contiene i certificati, le relative chiavi e i profili di provisioning associati all'account. Esistono effettivamente due versioni di un profilo sviluppatore, una sul portale per sviluppatori e l'altra su un computer Mac locale. La differenza tra i due è il tipo di chiavi contenute: _il profilo nel portale contiene tutte le chiavi pubbliche associate ai certificati, mentre la copia nel computer Mac locale contiene tutte le chiavi private_. Affinché il certificato sia valido, le coppie di chiavi devono corrispondere.

> [!WARNING]
> La perdita del certificato e delle chiavi associate può essere estremamente problematica, poiché richiede la revoca dei certificati esistenti e il nuovo provisioning di tutti i dispositivi associati, inclusi quelli registrati per la distribuzione ad hoc. Dopo aver impostato correttamente i certificati di sviluppo, esportare una copia di backup e archiviarla in un luogo sicuro. Per altre informazioni su come eseguire questa operazione, vedere la sezione Exporting and Importing Certificates and Profiles (Esportazione e importazione di certificati e profili) della guida [Maintaining Certificates](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html) (Manutenzione dei certificati) nella documentazione di Apple.

<a name="provisioning"></a>

## <a name="provision-an-ios-device-for-development"></a>Effettuare il provisioning di un dispositivo iOS per lo sviluppo

Ora che è stata stabilita l'identità con Apple e si dispone di un certificato di sviluppo, è necessario configurare un profilo di provisioning e le entità necessarie in modo che sia possibile distribuire un'app in un dispositivo Apple. Nel dispositivo deve essere in esecuzione una versione di iOS supportata da Xcode; potrebbe essere necessario aggiornare il dispositivo, Xcode o entrambi.

<a name="adddevice"></a>

## <a name="add-a-device"></a>Aggiungere un dispositivo

Quando si crea un profilo di provisioning per lo sviluppo, è necessario indicare quali dispositivi possono eseguire l'applicazione. A tale scopo, è possibile aggiungere fino a 100 dispositivi per anno di calendario al proprio portale per sviluppatori e da qui è possibile selezionare i dispositivi da aggiungere a un determinato profilo di provisioning. Completare i passaggi seguenti nel Mac per aggiungere un dispositivo al portale per sviluppatori

1. Connettere il dispositivo per il quale eseguire il provisioning al compuer Mac con il cavo USB in dotazione.
2. Aprire Xcode e passare a **finestra > dispositivi e simulatori**.
3. Nella scheda **dispositivi** selezionare il dispositivo dal menu a sinistra.
4. Evidenziare la stringa **Identifier** (Identificatore) e copiarla negli appunti:

   ![Dispositivi Xcode e finestra del simulatore con il percorso della stringa dell'identificatore iOS evidenziato.](manual-provisioning-images/xcode-devices.png)

5. In un Web browser passare alla [sezione dispositivi nel portale per sviluppatori](https://developer.apple.com/account/resources/devices/list) e fare clic sul **+** pulsante:

   ![Screenshot della pagina dispositivi sul sito Apple Developer con il pulsante Aggiungi evidenziato.](manual-provisioning-images/developer-portal-devices.png)

6. Impostare la **piattaforma** corretta e specificare un nome per il nuovo dispositivo. Incollare l'identificatore copiato in precedenza nel campo **ID dispositivo** :

    ![Screenshot della pagina di registrazione del nuovo dispositivo con i campi inseriti correttamente.](manual-provisioning-images/new-device-info.png)

7. Fare clic su **Continua**.
8. Esaminare le informazioni e quindi fare clic su **registra**.

Ripetere i passaggi precedenti per tutti i dispositivi iOS che verranno usati per testare o eseguire il debug di un'applicazione Xamarin.iOS.

<a name="provisioningprofile"></a>

## <a name="create-a-development-provisioning-profile"></a>Creare un profilo di provisioning di sviluppo

Dopo aver aggiunto il dispositivo al portale per sviluppatori, è necessario creare un profilo di provisioning e aggiungervi il dispositivo. 

Prima di creare un profilo di provisioning, è necessario creare un *App ID* (ID app). Un ID app è una stringa di tipo DNS inverso che identifica in modo univoco un'applicazione. Nella procedura seguente viene illustrato come creare un **Wildcard App ID** (ID app con caratteri jolly), che consente di compilare e installare la maggior parte delle applicazioni. Gli **Explicit App IDs** (ID app espliciti) consentono di installare solo un'applicazione (con l'ID del bundle corrispondente) e vengono in genere usati per determinate funzionalità di iOS, ad esempio Apple Pay e HealthKit. Per informazioni sulla creazione di ID app espliciti, vedere la guida [Working with Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Uso delle funzionalità).

### <a name="new-wildcard-app-id"></a>Nuovo ID app con caratteri jolly

1. Passare alla [sezione Identifiers (identificatori) nel portale per sviluppatori](https://developer.apple.com/account/resources/identifiers/list) e fare clic sul **+** pulsante.
2. Selezionare **ID app** e fare clic su **continua**.
3. Fornire una **Descrizione**. Impostare quindi **ID bundle** su **carattere jolly** e immettere un ID nel formato `com.[DomainName].*` :

   ![Screenshot della pagina di registrazione dell'ID app con i campi obbligatori popolati.](manual-provisioning-images/new-app-id.png)

4. Fare clic su **Continua**.
5. Esaminare le informazioni e quindi fare clic su **registra**.

### <a name="new-provisioning-profile"></a>Nuovo profilo di provisioning

Una volta creato l'ID app, è possibile creare il profilo di provisioning. Questo profilo di provisioning contiene informazioni sull'app ( *o app,* se si tratta di un ID app con caratteri jolly) a cui si riferisce il profilo, *che* può usare il profilo (a seconda dei certificati per sviluppatori aggiunti) e sui dispositivi *che* possono installare l'app.

Per creare manualmente un profilo di provisioning per lo sviluppo, procedere come segue:

1. Passare alla [sezione profili nel portale per sviluppatori](https://developer.apple.com/account/resources/profiles/list) e fare clic sul **+** pulsante.

2. In **sviluppo**selezionare **sviluppo app iOS** e fare clic su **continua**.

3. Selezionare l'ID app da usare nel menu a discesa e quindi fare clic su **continua**.

4. Selezionare i certificati da includere nel profilo di provisioning e quindi fare clic su **continua**.

5. Selezionare tutti i dispositivi in cui verrà installata l'app, quindi fare clic su **continua**.

6. Specificare **il nome del profilo di provisioning** e quindi fare clic su **generate**.

7. Facoltativamente, è possibile fare clic su **download** nella pagina successiva per scaricare il profilo di provisioning nel Mac.

<a name="download"></a>

## <a name="download-provisioning-profiles-in-visual-studio"></a>Scaricare i profili di provisioning in Visual Studio

Dopo aver creato un nuovo profilo di provisioning nel portale Apple Developer, usare Visual Studio per scaricarlo in modo che sia disponibile per la firma del bundle nell'app.

1. Passare alla finestra Apple Developer Accounts in Visual Studio.
    1. Mac: **Preferenze di Visual Studio > > account per sviluppatore Apple**
    2. Windows: **strumenti > opzioni > novell > account Apple**

2. Selezionare il team e fare clic su **Visualizza dettagli...**
3. Verificare che il nuovo profilo sia visualizzato nell'elenco **profili di provisioning** . Per aggiornare l'elenco, potrebbe essere necessario riavviare Visual Studio. 
4. Fare clic su **Scarica tutti i profili**.

Il nuovo profilo di provisioning sarà ora disponibile in Visual Studio e pronto per l'uso.

## <a name="deploy-to-a-device"></a>Eseguire la distribuzione in un dispositivo

A questo punto il provisioning è completo e l'applicazione è pronta per essere distribuita al dispositivo. A questo scopo, seguire questa procedura:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Connettere il dispositivo al Mac.
2. Aprire **info. plist** e verificare che l' **identificatore del bundle** corrisponda all'ID app creato in precedenza, a meno che l'ID app non sia un carattere jolly.
3. Nella sezione **firma** selezionare **provisioning manuale** come **schema**:

    ![Screenshot di info. plist in Visual Studio per Mac con provisioning manuale selezionato](manual-provisioning-images/vsm-info-plist.png)

4. Fare clic su **Opzioni di firma bundle...**
5. Assicurarsi che la configurazione di compilazione sia impostata su **debug | iPhone**. Aprire i menu a discesa **identità di firma** e **profilo di provisioning** per verificare che siano elencati i certificati corretti e i profili di provisioning: 

   ![pagina delle proprietà di firma del bundle iOS con il menu a discesa del profilo di provisioning aperto che elenca tutti i profili di provisioning disponibili per l'app.](manual-provisioning-images/vsm-bundle-signing.png)

6. Selezionare un'identità e un profilo specifici da usare oppure lasciarlo come **automatico**. Se impostato su **automatico**, Visual Studio per Mac selezionerà l'identità e il profilo in base all' **identificatore del bundle** in **info. plist**. 
7. Fare clic su **OK**.
8. Fare clic su **Esegui** per distribuire l'app nel dispositivo.


# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Connettere il dispositivo all'host di compilazione Mac.
2. Aprire **info. plist** e verificare che l' **identificatore del bundle** corrisponda all'ID app creato in precedenza, a meno che l'ID app non sia un carattere jolly.
3. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto iOS, scegliere **Proprietà** e passare alla scheda **firma bundle iOS** .
4. Assicurarsi che la configurazione di compilazione sia impostata su **debug | iPhone**. In **bundle Signing (firma bundle**) selezionare **provisioning manuale** come **schema**:

    ![Screenshot di info. plist in Visual Studio per Mac con provisioning manuale selezionato](manual-provisioning-images/vs-bundle-signing.png)

5. Aprire i menu a discesa **identità di firma** e **profilo di provisioning** per verificare che siano elencati i certificati e i profili di provisioning corretti.
6. Selezionare un'identità e un profilo specifici da usare oppure lasciarlo come **automatico**. Se impostato su **automatico**, Visual Studio selezionerà l'identità e il profilo in base all' **identificatore del bundle** in **info. plist**. 
7. Fare clic su **Esegui** per distribuire l'app nel dispositivo.

-----

## <a name="provisioning-for-application-services"></a>Provisioning dei servizi delle applicazioni

Apple offre una selezione di servizi speciali per le applicazioni, denominati anche funzionalità, che possono essere attivati per un'applicazione Xamarin.iOS. Questi servizi per le applicazioni devono essere configurati sia nel portale di provisioning iOS quando viene creato l'**ID app** sia nel file **Entitlements.plist** che è parte del progetto dell'applicazione Xamarin.iOS. Per informazioni sull'aggiunta dei servizi per le applicazioni all'app, vedere la guida [Introduction to Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Introduzione alle funzionalità) e la guida [Working with Entitlements](~/ios/deploy-test/provisioning/entitlements.md) (Uso degli entitlement).

- Creare un ID app con i servizi app necessari.
- Creare un nuovo [profilo di provisioning](#provisioningprofile) contenente tale ID app.
- Impostare gli entitlement nel progetto Xamarin.iOS

## <a name="related-links"></a>Collegamenti correlati

- [Provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [App Distribution (Distribuzione dell'app)](~/ios/deploy-test/app-distribution/index.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
- [Apple - App Distribution Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) (Apple - Guida alla distribuzione dell'app)
