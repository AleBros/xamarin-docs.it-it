---
title: Provisioning manuale per Xamarin.iOS
description: Dopo aver installato correttamente Xamarin.iOS, il passaggio successivo nello sviluppo iOS consiste nell'eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso del provisioning manuale per configurare profili e certificati di sviluppo.
ms.prod: xamarin
ms.assetid: E26ACC94-F4A5-4FF5-B7D4-BE596745A665
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 07/15/2017
ms.openlocfilehash: 0702241b5dbc744bf40c59928103d92a73a4c462
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121221"
---
# <a name="manual-provisioning-for-xamarinios"></a>Provisioning manuale per Xamarin.iOS

_Dopo aver installato correttamente Xamarin.iOS, il passaggio successivo per lo sviluppo iOS consiste nell'eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso del provisioning manuale per configurare profili e certificati di sviluppo._

> [!NOTE]
> Le istruzioni in questa pagina sono utili per gli sviluppatori che hanno l'accesso a pagamento al programma Apple Developer. Se si ha un account gratuito, vedere la guida [Provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md) per altre informazioni sui test dei dispositivi.

## <a name="creating-a-signing-identity"></a>Creazione di un'identità di firma

Il primo passaggio nella configurazione di un dispositivo di sviluppo consiste nel creare un'identità di firma. Un'identità di firma è costituita da due elementi:

- Un certificato di sviluppo
- Una chiave privata

I certificati di sviluppo e le [chiavi](#understanding-certificate-key-pairs) associate sono fondamentali per lo sviluppatore di iOS: essi stabiliscono l'identità dell'utente con Apple e lo associano a un determinato dispositivo e profilo per lo sviluppo ed equivalgono all'inserimento della propria firma digitale sulle applicazioni. Apple verifica i certificati per controllare l'accesso ai dispositivi che è possibile distribuire.

I team di sviluppo, i certificati e i profili possono essere gestiti mediante l'accesso alla sezione [Certificates, Identifiers & Profiles](https://developer.apple.com/account/overview.action) (Certificati, identificatori e profili) di Apple's Members Center (accesso richiesto). Apple richiede un'identità di firma per compilare il proprio codice per il dispositivo o simulatore.  

> [!IMPORTANT]
> È importante notare che possono essere presenti solo due certificati di sviluppo iOS per volta. Se è necessario crearne altri, occorre revocarne uno esistente. Un computer con un certificato revocato non sarà in grado di firmare le app.

Per generare un'identità di firma, eseguire le operazioni seguenti:

1. Accedere alla [sezione Certificates, Identifiers, and Profiles section (Certificati, identificatori e profili) del portale per sviluppatori](https://developer.apple.com/account/overview.action) e selezionare la sezione **Certificates** (Certificati) nella colonna **iOS Apps** (App iOS). Toccare **+** per creare un nuovo certificato:

    [![](manual-provisioning-images/cert-plus.png "Fare clic su + per creare un nuovo certificato")](manual-provisioning-images/cert-plus.png#lightbox)

2. Selezionare l'opzione **iOS App Development** (Sviluppo di app iOS) per il tipo di certificato e fare clic su **Continue** (Continua). Questa schermata potrebbe essere diversa a seconda dei privilegi dell'account:

    [![](manual-provisioning-images/cert-first.png "Selezionare l'opzione per lo sviluppo di app iOS per il tipo di certificato")](manual-provisioning-images/cert-first.png#lightbox)

3. Richiedere una richiesta di firma del certificato, che verrà caricata per generare manualmente un certificato. A tale scopo, avviare **Keychain Access** (Accesso portachiavi) su un computer Mac. Passare al menu principale e selezionare **Certificate Assistant** (Assistente certificato) e **Request a Certificate from a Certificate Authority...** (Richiedi un certificato da un'autorità di certificazione), come illustrato di seguito:

      [![](manual-provisioning-images/key-first.png "Richiedere una richiesta di firma del certificato")](manual-provisioning-images/key-first.png#lightbox)

4. Immettere le informazioni e selezionare l'opzione che consente di **salvare su disco**:

    [![](manual-provisioning-images/key-second.png "Immettere le informazioni")](manual-provisioning-images/key-second.png#lightbox)

5. Salvare la richiesta di firma del certificato in una posizione in cui possa essere facilmente trovata:

    [![](manual-provisioning-images/cert-third.png "Salvare la richiesta di firma del certificato")](manual-provisioning-images/cert-third.png#lightbox)

6. Tornare al portale di provisioning, caricare il certificato nel portale e inviarlo:

    [![](manual-provisioning-images/cert-second.png "Caricare il certificato nel portale")](manual-provisioning-images/cert-second.png#lightbox)

    Se non si possiedono i privilegi amministrativi, il certificato deve essere approvato da un amministratore o agente del team.

7. Dopo che il certificato è stato approvato, scaricarlo dal portale di provisioning:

    [![](manual-provisioning-images/status-dev.png "Scaricare il certificato dal portale di provisioning")](manual-provisioning-images/status-dev.png#lightbox)

8. Fare doppio clic sul certificato scaricato per avviare Keychain Access (Accesso portachiavi) e aprire il pannello **My Certificates** (I miei certificati), nel quale sono visualizzati i nuovi certificati e le chiavi private associate:

    [![](manual-provisioning-images/keychain.png "Certificato nell'accesso keychain")](manual-provisioning-images/keychain.png#lightbox)

### <a name="understanding-certificate-key-pairs"></a>Coppie di chiavi del certificato

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Il profilo sviluppatore contiene i certificati, le relative chiavi e i profili di provisioning associati all'account. Esistono effettivamente due versioni di un profilo sviluppatore, una sul portale per sviluppatori e l'altra su un computer Mac locale. La differenza tra i due è il tipo di chiavi contenute: _il profilo nel portale contiene tutte le chiavi pubbliche associate ai certificati, mentre la copia nel computer Mac locale contiene tutte le chiavi private_. Affinché il certificato sia valido, le coppie di chiavi devono corrispondere. Mantenere un backup del profilo sviluppatore sul computer Mac locale, perché se le chiavi private vengono perse tutti i certificati e i profili di provisioning dovranno essere rigenerati.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Il profilo sviluppatore contiene i certificati, le relative chiavi e i profili di provisioning associati all'account. Esistono effettivamente due versioni di un profilo sviluppatore, una sul portale per sviluppatori e l'altra su un computer Mac. La differenza tra i due è il tipo di chiavi contenute: _il profilo nel portale contiene tutte le chiavi pubbliche associate ai certificati, mentre la copia nel computer Mac contiene tutte le chiavi private_. Affinché il certificato sia valido, le coppie di chiavi devono corrispondere. Mantenere un backup del profilo sviluppatore sul computer Mac dell'host di compilazione Xamarin, perché se le chiavi private vengono perse tutti i certificati e i profili di provisioning dovranno essere rigenerati.

-----

> [!WARNING]
> La perdita del certificato e delle chiavi associate può essere estremamente problematica, perché comporta la revoca dei certificati esistenti e la ripetizione del provisioning di tutti i dispositivi associati, inclusi quelli registrati per la distribuzione ad hoc. Dopo aver impostato correttamente i certificati di sviluppo, esportare una copia di backup e archiviarla in un luogo sicuro. Per altre informazioni su come eseguire questa operazione, vedere la sezione Exporting and Importing Certificates and Profiles (Esportazione e importazione di certificati e profili) della guida [Maintaining Certificates](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html) (Manutenzione dei certificati) nella documentazione di Apple.

<a name="provisioning" />

## <a name="provisioning-an-ios-device-for-development"></a>Esecuzione del provisioning di un dispositivo iOS per lo sviluppo

Dopo aver stabilito la propria identità con Apple e aver ottenuto un certificato di sviluppo, è necessario impostare un profilo di provisioning e le entità necessarie in modo da poter distribuire un'app a un dispositivo Apple. Nel dispositivo deve essere in esecuzione una versione di iOS supportata da Xcode; potrebbe essere necessario aggiornare il dispositivo, Xcode o entrambi.

<a name="adddevice" />

## <a name="add-a-device"></a>Aggiungere un dispositivo

Quando si crea un profilo di provisioning per lo sviluppo, è necessario indicare quali dispositivi possono eseguire l'applicazione. A tale scopo, è possibile aggiungere fino a 100 dispositivi per anno di calendario al proprio portale per sviluppatori e da qui è possibile selezionare i dispositivi da aggiungere a un determinato profilo di provisioning. Completare i passaggi seguenti nel Mac per aggiungere un dispositivo al portale per sviluppatori

1. Avviare Xcode.
2. Connettere il dispositivo per il quale eseguire il provisioning al compuer Mac con il cavo USB in dotazione.
3. Dal menu **Windows** (Finestre) selezionare **Devices** (Dispositivi):

   [![](manual-provisioning-images/add01.png "Dal menu Windows (Finestre) selezionare Devices (Dispositivi)")](manual-provisioning-images/add01.png#lightbox)

4. Selezionare il dispositivo iOS necessario dall'elenco **DEVICES** (DISPOSITIVI) sul lato sinistro della finestra Devices (Dispositivi).
5. Evidenziare la stringa **Identifier** (Identificatore) e copiarla negli appunti:

   [![](manual-provisioning-images/add02.png "Evidenziare la stringa dell'identificatore")](manual-provisioning-images/add02.png#lightbox)

6. In Safari, individuare [Apple Developer Center](https://developer.apple.com/membercenter/index.action) ed eseguire l'accesso.
7. Fare clic sul collegamento **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili):

   [![](manual-provisioning-images/add03.png "Fare clic sul collegamento Certificates, Identifiers and Profiles (Certificati, identificatori e profili)")](manual-provisioning-images/add03.png#lightbox)

8. Fare clic sul collegamento **Devices** (Dispositivi):

   [![](manual-provisioning-images/add04.png "Fare clic sul collegamento Devices (Dispositivi)")](manual-provisioning-images/add04.png#lightbox)

9. Fare clic sul pulsante **+** :

   [![](manual-provisioning-images/add05.png "Fare clic sul pulsante +")](manual-provisioning-images/add05.png#lightbox)

10. Specificare un nome per il nuovo dispositivo e incollare l'**identificatore** del dispositivo copiato in precedenza nel campo **UUID**:

    [![](manual-provisioning-images/add06.png "Specificare un nome per il nuovo dispositivo e l'identificatore del dispositivo")](manual-provisioning-images/add06.png#lightbox)

11. Fare clic sul pulsante **Continue** (Continue).
12. Esaminare infine le informazioni e fare clic sul pulsante **Register** (Registra):

    [![](manual-provisioning-images/add07.png "Esaminare le informazioni")](manual-provisioning-images/add07.png#lightbox)

Ripetere i passaggi precedenti per tutti i dispositivi iOS che verranno usati per testare o eseguire il debug di un'applicazione Xamarin.iOS.

Dopo aver aggiunto il dispositivo al portale per sviluppatori, è necessario creare un profilo di provisioning e aggiungervi il dispositivo.

<a name="provisioningprofile" />

## <a name="creating-a-development-provisioning-profile"></a>Creazione di un profilo di provisioning di sviluppo

Come con il certificato di sviluppo, i profili di provisioning possono essere creati manualmente tramite la sezione [Certificates, Identifiers & Profiles](https://developer.apple.com/account/overview.action) (Certificati, identificatori e profili) di Apple's Members Center.

Prima di creare un profilo di provisioning, è necessario creare un *App ID* (ID app). Un ID app è una stringa di tipo DNS inverso che identifica in modo univoco un'applicazione. Nella procedura seguente viene illustrato come creare un **Wildcard App ID** (ID app con caratteri jolly), che consente di compilare e installare la maggior parte delle applicazioni. Gli **Explicit App IDs** (ID app espliciti) consentono di installare solo un'applicazione (con l'ID del bundle corrispondente) e vengono in genere usati per determinate funzionalità di iOS, ad esempio Apple Pay e HealthKit. Per informazioni sulla creazione di ID app espliciti, vedere la guida [Working with Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Uso delle funzionalità).

### <a name="app-id"></a>ID app

1. Nel [portale per sviluppatori](https://developer.apple.com/account/overview.action) passare alla sezione *Certificate, Identifiers and Profiles* (Certificati, identificatori e profili) di Apple Developer Center. Selezionare **App IDs** (ID app) in **Identifiers** (Identificatori).
2. Fare clic sul pulsante **+** e specificare un **Name** (Nome):

    [![](manual-provisioning-images/appid05a.png "Specificare un nome")](manual-provisioning-images/appid05a.png#lightbox)
3. Il prefisso app deve essere preimpostato. Selezionare **Wildcard App ID** (ID app con caratteri jolly) per il suffisso app. Immettere un ID bundle nel formato `com.[DomainName].*`:

   [![](manual-provisioning-images/appid05b.png "Immettere un ID bundle")](manual-provisioning-images/appid05b.png#lightbox)

4. Fare clic sul pulsante **Continue** (Continua) e seguire le istruzioni visualizzate sullo schermo per creare il nuovo ID app.

### <a name="provisioning-profile"></a>Profilo di provisioning

Dopo aver creato l'ID app, è possibile creare il profilo di provisioning. Il profilo di provisioning contiene informazioni su *quale* app (o quali app, in caso di ID app con caratteri jolly) è correlata a questo profilo, *chi* può usare il profilo (a seconda di quali certificati sviluppatore vengono aggiunti) e *quali* dispositivi possono installare l'app.

Per creare manualmente un profilo di provisioning per lo sviluppo, procedere come descritto di seguito:

1. Usare Safari per navigare in [Apple Developers Member Center](https://developer.apple.com/membercenter/index.action) e nella sezione *Certificates, Identifiers & Profiles* (Certificati, identificatori e profili) selezionare Provisioning Profiles (Profili di provisioning).
2. Fare clic sul pulsante **+** nell'angolo in alto a destra per creare un nuovo profilo.
3. Nella sezione **Development** (Sviluppo), selezionare il pulsante di opzione accanto a **iOS App Development** (Sviluppo di app iOs) e premere **Continue** (Continua):

    [![](manual-provisioning-images/provisioning-profile01.png "Selezionare il tipo di profilo da creare")](manual-provisioning-images/provisioning-profile01.png#lightbox)
4. Selezionare l'ID app da usare nel menu a discesa:

    [![](manual-provisioning-images/provisioning-profile02.png "Selezionare l'ID app da usare")](manual-provisioning-images/provisioning-profile02.png#lightbox)
5. Selezionare uno o più certificati da includere nel profilo di provisioning e premere **Continue** (Continua):

    [![](manual-provisioning-images/provisioning-profile03.png "Selezionare i certificati da includere nel profilo di provisioning")](manual-provisioning-images/provisioning-profile03.png#lightbox)
6. Selezionare tutti i dispositivi sui quali verrà installata l'app.

    [![](manual-provisioning-images/provisioning-profile04.png "Selezionare tutti i dispositivi nei quali verrà installata l'app")](manual-provisioning-images/provisioning-profile04.png#lightbox)
7. Assegnare al profilo di provisioning un nome identificabile e premere **Continue** (Continua) per creare il profilo:

    [![](manual-provisioning-images/provisioning-profile05.png "Assegnare al profilo di provisioning un nome identificabile")](manual-provisioning-images/provisioning-profile05.png#lightbox)
8. Premere **Download** (Scarica) per scaricare il profilo di provisioning in un computer Mac:

    [![](manual-provisioning-images/provisioning-profile06.png "Scaricare il profilo di provisioning")](manual-provisioning-images/provisioning-profile06.png#lightbox)
9. Fare doppio clic sul file per installare il profilo di provisioning in Xcode. Si noti che Xcode potrebbe non visualizzare tracce visive che indicano l'installazione del profilo, tranne che per l'apertura. È possibile verificarlo passando a **Xcode > Preferenze > Account**. Selezionare il proprio ID Apple e fare clic su **Visualizza dettagli**. Il nuovo profilo di provisioning viene elencato come illustrato di seguito:

      [![](manual-provisioning-images/provisioning-profile07.png "Visualizzazione del profilo in Xcode")](manual-provisioning-images/provisioning-profile07.png#lightbox)

Dopo che è stato creato correttamente il profilo di provisioning, potrebbe essere necessario aggiornare Xcode in modo che tutti i certificati di sviluppo siano disponibili per Visual Studio per Mac e Visual Studio.

<a name="download" />

## <a name="downloading-profiles-and-certificates-in-xcode"></a>Download di profili e certificati in Xcode

I certificati e i profili di provisioning che sono stati creati nel portale Apple Developer potrebbero non essere visualizzati automaticamente in Xcode. Potrebbe quindi essere necessario scaricarli in modo da potervi accedere da Visual Studio per Mac e Visual Studio. Per aggiornare e scaricare i certificati creati nel portale Apple Developer, eseguire le operazioni seguenti:

1. Uscire da Visual Studio per Mac o Visual Studio.
2. Avviare Xcode.
3. Scegliere **Menu Xcode > Preferenze**
4. Fare clic sulla scheda **Account**.
5. Selezionare un team e fare clic sul pulsante **Download Manual Profiles** (Scarica profili manuali):  [![](manual-provisioning-images/selectteam1.png "Download dei profili manuali")](manual-provisioning-images/selectteam1.png#lightbox)

6. Uscire da Xcode.
7. Avviare Visual Studio per Mac o Visual Studio.

I nuovi certificati o profili di provisioning saranno disponibili in Visual Studio per Mac o Visual Studio e pronti per l'uso.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

> [!IMPORTANT]
> Può essere necessario arrestare e riavviare Visual Studio per Mac prima di poter visualizzare i certificati o i profili nuovi o modificati aggiornati da Xcode.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

> [!IMPORTANT]
> Può essere necessario arrestare e riavviare Visual Studio prima di poter visualizzare i certificati o i profili nuovi o modificati aggiornati da Xcode.

-----

<a name="appservices" />

## <a name="provisioning-for-application-services"></a>Provisioning dei servizi delle applicazioni

Apple offre una selezione di servizi speciali per le applicazioni, denominati anche funzionalità, che possono essere attivati per un'applicazione Xamarin.iOS. Questi servizi per le applicazioni devono essere configurati sia nel portale di provisioning iOS quando viene creato l'**ID app** sia nel file **Entitlements.plist** che è parte del progetto dell'applicazione Xamarin.iOS. Per informazioni sull'aggiunta dei servizi per le applicazioni all'app, vedere la guida [Introduction to Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Introduzione alle funzionalità) e la guida [Working with Entitlements](~/ios/deploy-test/provisioning/entitlements.md) (Uso degli entitlement).

- Creare un ID app con i servizi app necessari.
- Creare un nuovo [profilo di provisioning](#provisioningprofile) contenente tale ID app.
- Impostare gli entitlement nel progetto Xamarin.iOS

## <a name="deploying-to-a-device"></a>Distribuzione in un dispositivo

A questo punto il provisioning è completo e l'applicazione è pronta per essere distribuita al dispositivo. A questo scopo, eseguire la procedura seguente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

> [!IMPORTANT]
> Prima di iniziare, assicurarsi di selezionare **Provisioning manuale** in **Info.plist**.

1. Collegare il dispositivo a un computer Mac.
2. Nell'area **Info.plist** del progetto verificare che l'identificatore del bundle corrisponda all'ID app (a meno che l'ID app non sia un carattere jolly):

   ![](manual-provisioning-images/deploydevice01xs.png "Inserimento di un identificatore")

3. Fare clic con il pulsante destro del mouse per visualizzare la finestra di dialogo delle opzioni del progetto e passare a **Compila > Firma del bundle iOS**. Nell'elenco a discesa accanto a **Identità di firma** e **Profilo di provisioning** verificare che Visual Studio per Mac sia in grado di visualizzare i profili corretti e selezionare un profilo e un'identità specifici:

   ![](manual-provisioning-images/deploydevice02xs.png "Selezionare un profilo e un'identità specifici")

   Se è impostato su **Automatico**, Visual Studio per Mac selezionerà l'identità e il profilo in base all'ID del pacchetto impostato nel passaggio 2.

4. Verificare di impostare la configurazione della build su **iPhone** / **iPad** e non sul simulatore.
5. Fare clic su **Esegui** in Visual Studio per Mac e visualizzare l'app in esecuzione nel dispositivo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

> [!IMPORTANT]
> Prima di iniziare, assicurarsi di selezionare **Provisioning manuale** in **Progetto > Provisioning Properties (Proprietà di provisioning)** .

1. Collegare il dispositivo all'host di compilazione Mac.
2. Nell'area **Info.plist** del progetto verificare che l'identificatore del bundle corrisponda all'ID app:

   ![](manual-provisioning-images/servicevs01.png "Inserimento di un identificatore")

3. Fare clic con il pulsante destro del mouse per visualizzare la finestra di dialogo delle opzioni del progetto e passare a **Compila > Firma del bundle iOS**. Nell'elenco a discesa accanto a **Identità di firma** e **Profilo di provisioning** verificare che Visual Studio sia in grado di visualizzare i profili corretti e selezionare un profilo e un'identità specifici.

    Se è impostato su **Automatico**, Visual Studio selezionerà l'identità e il profilo in base all'ID del pacchetto impostato nel passaggio 2.

4. Verificare di impostare la configurazione della build su **iPhone** o **iPad** e non sul simulatore.
5. Fare clic su **Esegui** in Visual Studio per Mac e visualizzare l'app in esecuzione nel dispositivo.


-----

## <a name="summary"></a>Summary

In questa guida sono stati illustrati i passaggi necessari per impostare l'ambiente di sviluppo per Xamarin.iOS. È stato spiegato come un'applicazione viene firmata con codice contenente informazioni sullo sviluppatore e il relativo team, i dispositivi sui quali può essere eseguita l'app e un ID app individuale.

## <a name="related-links"></a>Collegamenti correlati

- [Provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuzione di app](~/ios/deploy-test/app-distribution/index.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
- [Apple - App Distribution Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) (Apple - Guida alla distribuzione dell'app)
