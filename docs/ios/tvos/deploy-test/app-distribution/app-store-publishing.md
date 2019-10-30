---
title: Pubblicazione in Apple TV App Store
description: Questo documento descrive come pubblicare un'app nell'App Store di Apple TV. Viene illustrato come configurare, effettuare il provisioning, compilare e inviare un'applicazione tvOS compilata con Novell.
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: f49fa08e5d9b2ad76fc069b415d6ea5b899b2bf7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030781"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>Pubblicazione in Apple TV App Store

Per distribuire le applicazioni a tutti i dispositivi Apple TV, Apple richiede che le app vengano pubblicate tramite l' *App Store di Apple TV*, facendo in modo che l'app memorizzi la località di acquisto con una sola interruzione per le app tvOS. Gli sviluppatori di molti tipi di app possono capitalizzare il successo di questo singolo punto di distribuzione. L'App Store di Apple TV è una soluzione chiavi in mano che offre agli sviluppatori di app sia sistemi di distribuzione che di pagamento.

Il processo di invio di un'applicazione all'App Store di Apple TV implica:

1. Creazione di un *ID App* e selezione degli *entitlement*.
2. Creazione di un *profilo di provisioning di distribuzione*.
3. Usare questo profilo per compilare l'app.
4. Invio dell'app tramite *iTunes Connect*.

In questo articolo vengono illustrati tutti i passaggi necessari per eseguire il provisioning, compilare e inviare un'app per la distribuzione dell'app Store di Apple TV.

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>Prima di inviare un'applicazione

Dopo aver inviato un'app per la pubblicazione nell'App Store di Apple TV, viene eseguito un processo di revisione di Apple per assicurarsi che soddisfi le linee guida di Apple per la qualità e il contenuto. Se l'applicazione non soddisfa queste linee guida, Apple la rifiuterà. A questo punto, sarà necessario risolvere i problemi di non conformità segnalati da Apple e inviarla di nuovo.
Per avere buone probabilità di superare il processo di revisione di Apple, è consigliabile familiarizzare con queste linee guida e tentare di adattare l'applicazione a tali regole. Le linee guida di Apple sono disponibili nelle [linee guida di revisione di App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html) e [preparano l'invio dell'app per il nuovo Apple TV](https://developer.apple.com/tvos/submit/).

Di seguito sono specificati un paio di aspetti da verificare prima di inviare un'app:

1. Verificare che la descrizione dell'app corrisponda alla funzionalità inclusa nell'app.
2. Verificare che nell'app non si verifichino arresti anomali in condizioni di normale utilizzo, Questo include l'utilizzo in ogni dispositivo Apple TV supportato.

Apple gestisce anche un elenco di suggerimenti per l'invio di App Store di Apple TV. disponibili nella pagina relativa alla [distribuzione nell'pp Store](https://developer.apple.com/appstore/resources/submission/tips.html).

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configurazione dell'applicazione in iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) è una suite di strumenti basati sul Web per, tra le altre cose, la gestione delle app TvOS nell'App Store di Apple TV. È necessario configurare e configurare correttamente l'app Novell. tvOS in iTunes Connect prima di poterla inviare ad Apple per la revisione e, infine, essere rilasciata per la vendita o come app gratuita nell'App Store di Apple TV.

Procedere come descritto di seguito:

1. Verificare che esistano i contratti appropriati e che siano aggiornati nella sezione **Agreements, Tax, and Banking** (Contratti, imposte e banche) di iTunes Connect per poter rilasciare un'applicazione iOS gratuita o in vendita.
2. Creare un nuovo **record di iTunes Connect** per l'applicazione e specificare il relativo **nome visualizzato** , come visualizzato nell'App Store di Apple TV.
3. Selezionare **Sale Price** (Prezzo di vendita) o specificare che l'applicazione viene rilasciata gratuitamente.
4. Fornire un' **icona dell'app Store** (icona grande) e schermate dell'applicazione in azione nei dispositivi Apple TV supportati. Per altri dettagli, vedere la Guida [uso delle icone e delle immagini](~/ios/tvos/app-fundamentals/icons-images.md) .
5. Fornire una **Descrizione** chiara e concisa dell'app, incluse le funzionalità e i vantaggi offerti all'utente finale.
6. Fornire **categorie**, **sottocategorie**e **parole chiave** per aiutare l'utente a trovare l'app nell'App Store di Apple TV.
7. Specificare gli URL di **Contact** (Contatto) e **Support** (Supporto) al sito Web richiesto da Apple.
8. Impostare la **classificazione**dell'applicazione, che viene usata dai controlli padre nell'App Store di Apple TV.
9. Configurare le tecnologie facoltative dell'App Store, ad esempio **Game Center** e **Acquisto in-app**.

Per altri dettagli, vedere la documentazione [configurare l'app tvOS in iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) .

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>Preparazione per la distribuzione nell'App Store

Per pubblicare un'app nell'App Store di Apple TV, è prima necessario compilarla per la distribuzione, che comporta molti passaggi. Le sezioni seguenti illustrano tutti gli elementi necessari per preparare un'app Novell. tvOS per la pubblicazione, in modo che possa essere compilata e inviata all'App Store di Apple TV per la revisione e il rilascio.

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>Povisioning per i servizi per le applicazioni

Apple offre una selezione di Servizi per le applicazioni speciali, denominati anche diritti, che possono essere attivati per l'app tvOS quando si crea un ID univoco. Indipendentemente dal fatto che si utilizzino diritti personalizzati, sarà comunque necessario creare un ID univoco per l'app Novell. tvOS prima di poterlo pubblicare nell'App Store di Apple TV.

Per creare un ID app e facoltativamente selezionare gli entitlement è necessario seguire questa procedura che prevede l'uso del portale di provisioning iOS basato sul Web di Apple:

1. Selezionare **provisioning** > **sviluppo**.
2. Fare clic sul pulsante **+** e specificare **Nome** (Nome) e **Bundle ID** (ID bundle) per la nuova applicazione.
3. Scorrere fino alla fine della schermata e selezionare tutti i **Servizi app** che saranno richiesti dall'app Novell. tvOS.
4. Fare clic sul pulsante **Continue** (Continua) e seguire le istruzioni visualizzate per creare un nuovo ID app.

Oltre a selezionare e configurare la Servizi per le applicazioni obbligatoria quando si definisce l'ID app, è necessario configurare anche l'ID e i diritti dell'app nel progetto Novell. tvOS modificando i file di `Info.plist` e di `Entitlements.plist`.

In Visual Studio per Mac eseguire le operazioni seguenti:

1. In **Esplora soluzioni** fare doppio clic sul file `Info.plist` per aprirlo e modificarlo.
2. Nella sezione **destinazione applicazione tvOS** compilare un nome per l'applicazione e immettere l' **identificatore del bundle** creato quando è stato definito l'ID app.
3. Salvare le modifiche apportate al file `Info.plist`.
4. In **Esplora soluzioni** fare doppio clic sul file `Entitlements.plist` per aprirlo e modificarlo.
5. Selezionare e configurare i diritti necessari per l'app Novell. tvOS in modo che corrispondano alla configurazione eseguita in precedenza quando è stato definito l'ID app.
6. Salvare le modifiche apportate al file `Entitlements.plist`.

Per istruzioni dettagliate, vedere la documentazione [Provisioning dei servizi applicativi](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices). Mentre questo documento è stato scritto per iOS, vengono usati gli stessi passaggi per eseguire il provisioning di un'app Novell. tvOS.

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>Impostazione delle icone delle app, dell'immagine di avvio e dell'immagine in primo piano

Per consentire a un'app tvOS di essere accettata da Apple per l'inclusione nell'App Store di Apple TV, sono necessarie icone, lanci e immagini top shelf per tutti i dispositivi Apple TV su cui verrà eseguita. È necessario aggiungere gli asset immagine richiesti che verranno compilati in un file di `Assets.car` e inclusi nel bundle dell'app Novell. tvOS prima che venga caricato in iTunes Connect.

Per istruzioni dettagliate, vedere la documentazione sull' [uso di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>Creazione e installazione di un profilo di distribuzione

tvOS usa i *profili di provisioning* per controllare come può essere distribuita una determinata compilazione di applicazione. Si tratta di file che contengono informazioni sul certificato usato per firmare un'app, sull'*ID applicazione* e sul punto in cui poter installare l'app. Per lo sviluppo e la distribuzione ad hoc, il profilo di provisioning include anche l'elenco dei dispositivi consentiti in cui è possibile distribuire l'app. Tuttavia, per la distribuzione dell'app Store di Apple TV, sono incluse solo le informazioni relative a certificati e ID app, poiché l'unico meccanismo per la distribuzione pubblica è tramite l'App Store di Apple TV.

Il provisioning richiede questa procedura che prevede l'uso del portale di provisioning iOS basato sul Web di Apple:

1. Selezionare **Provisioning** (Provisioning)  > **Distribution** (Distribuzione).
2. Fare clic sul pulsante **+** e selezionare il tipo di profilo di distribuzione che si vuole creare come **Apple TV App Store**.
3. Selezionare un **ID App** nell'elenco a discesa per il quale si vuole creare un profilo di distribuzione.
4. Selezionare il certificato necessario per firmare l'applicazione.
5. Immettere un **Name** (Nome) per il nuovo **Distribution Profile** (Profilo di distribuzione) e generare il profilo.
6. Aggiornare l'elenco dei profili disponibili in Xcode.
7. Selezionare il profilo di provisioning di distribuzione in Visual Studio per la _configurazione della build_dell' **App Store** .

Per istruzioni dettagliate, vedere [Creating a Distribution Profile](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile) (Creazione di un profilo di distribuzione) e [Selecting a Distribution Profile in a Xamarin.iOS Project](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile) (Selezione di un profilo di distribuzione in un progetto Xamarin.iOS). Anche in questo caso, entrambi i documenti sono specifici per iOS, ma la stessa tecnica viene usata per le app tvOS.

<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>Impostazione della configurazione della build per l'applicazione

Per impostazione predefinita, quando si crea una nuova app Novell. tvOS, le _configurazioni di compilazione_ vengono create automaticamente per la distribuzione di **debug** e di **rilascio** . Prima di eseguire la build finale dell'app che verrà inviata ad Apple, è necessario apportare alcune modifiche alla configurazione della **versione** di base.

Procedere come descritto di seguito:

1. Fare clic con il pulsante destro del mouse sul **nome del progetto** nella **Esplora soluzioni** e selezionare **Opzioni** per aprirle per la modifica.
2. Se la destinazione è una versione specifica di tvOS, selezionarla in **TvOS Build** > **iOS SDK Version**. Per la versione di anteprima del supporto tvOS, lasciare questo valore impostato su **predefinito**.
3. Il collegamento riduce le dimensioni complessive dell'app distribuibile rimuovendo i metodi, le proprietà, le classi e così via. nella maggior parte dei casi, è consigliabile lasciare il valore predefinito di **link Framework SDK**. In alcune situazioni, ad esempio quando si usano alcune librerie di terze parti specifiche, potrebbe essere necessario impostare questo valore su **non collegare** per evitare la rimozione dell'elemento necessario.
4. Per distribuire un'app Novell. tvOS, è necessario usare il compilatore di ottimizzazione LLVM. Assicurarsi che la casella di controllo **Usa il compilatore di ottimizzazione di LLVM** sia selezionata sotto la configurazione di **rilascio** .
5. Apple ha inoltre richiesto che le app tvOS usino bitcode. Nuovamente sotto la configurazione della **versione** , aggiungere `--bitcode=asmonly` alla casella **argomenti aggiuntivi di mTouch** .
6. È necessario selezionare la casella di controllo **Ottimizza file di immagine PNG per iOS** per ridurre ulteriormente le dimensioni del risultato finale dell'app.
7. Il debug *non* deve essere abilitato perché renderà la compilazione inutilmente più grande.

<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>Compilazione e invio del file distribuibile

Con l'app Novell. tvOS configurata correttamente, è ora possibile eseguire la build di distribuzione finale che verrà inviata ad Apple per la revisione e il rilascio.

#### <a name="build-your-archive"></a>Compilare l'archivio

1. Selezionare la configurazione **Rilascio | Dispositivo** in Visual Studio per Mac:

    ![](app-store-publishing-images/buildxs01new.png "Select the Release configuration")
2. Scegliere **Archivia per la pubblicazione** dal menu **Compila**:

    [![](app-store-publishing-images/buildxs02new.png "Select Archive for Publishing")](app-store-publishing-images/buildxs02new.png#lightbox)
3. Dopo aver creato l'archivio, sarà visualizzata la visualizzazione **Archivi**:

    [![](app-store-publishing-images/buildxs03new.png "The Archives view")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>Firmare e distribuire l'app

Ogni volta in cui l'applicazione viene compilata per l'archivio, si aprirà automaticamente la *vista Archivi*, in cui saranno visualizzati tutti i progetti archiviati, raggruppati per soluzione. Per impostazione predefinita, questa visualizzazione contiene soltanto la soluzione attualmente aperta. Per visualizzare tutte le soluzioni con archivi, fare clic sull'opzione **Mostra tutti gli archivi**.

È consigliabile mantenere gli archivi distribuiti ai clienti (distribuzione tramite App Store o Enterprise), in modo che le informazioni di debug generate possano essere rappresentate in futuro.

Per firmare l'app e prepararla per la distribuzione:

1. Selezionare **firma e Distribuisci...** , illustrato di seguito:

    [![](app-store-publishing-images/buildxs04new.png ", Select theSign and Distribute...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. Si aprirà la pubblicazione guidata. Selezionare il canale di distribuzione **App Store** per creare un pacchetto e aprire Application Loader:

    [![](app-store-publishing-images/distribute01.png "Select the App Store distribution channel")](app-store-publishing-images/distribute01.png#lightbox)
3. Nella schermata Profilo di provisioning selezionare l'identità di firma e il profilo di provisioning corrispondente oppure ripetere la firma con un'altra identità:

    [![](app-store-publishing-images/distribute02.png "Select the signing identity and corresponding provisioning profile")](app-store-publishing-images/distribute02.png#lightbox)
4. Verificare i dettagli del pacchetto e fare clic su **Pubblica** per salvare il pacchetto `.ipa`:

    [![](app-store-publishing-images/distribute03.png "Verify the details of the package")](app-store-publishing-images/distribute03.png#lightbox)
5. Dopo aver salvato il pacchetto `.ipa`, l'app è pronta per essere caricata in iTunes Connect tramite Application Loader:

    [![](app-store-publishing-images/distribute04.png "Uploaded to iTunes Connect via the Application Loader")](app-store-publishing-images/distribute04.png#lightbox)

Dopo aver creato e archiviato la compilazione di distribuzione, è possibile inviare l'applicazione a iTunes Connect.

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>Invio dell'app ad Apple

Dopo aver completato la compilazione di distribuzione, è possibile inviare l'applicazione iOS ad Apple per la revisione e il rilascio nell'App Store.

Il flusso di lavoro di archiviazione in Visual Studio per Mac aprirà automaticamente il caricatore applicazione, dopo aver salvato il `.ipa`:

1. Selezionare *Deliver Your App* (Invia l'app) e fare clic sul pulsante *Choose* (Scegli):

    [![](app-store-publishing-images/publishvs01.png "Select Deliver Your App")](app-store-publishing-images/publishvs01.png#lightbox)

2. Selezionare il file ZIP o il file IPA creato in precedenza e fare clic sul pulsante **OK**.
3. Application Loader convaliderà il file:

    [![](app-store-publishing-images/publishvs02.png "The Application Loader validation screen")](app-store-publishing-images/publishvs02.png#lightbox)
4. Fare clic sul pulsante *Next* (Avanti). L'applicazione sarà convalidati per l'App Store:

    [![](app-store-publishing-images/publishvs03.png "The application being validated against the App Store")](app-store-publishing-images/publishvs03.png#lightbox)
5. Fare clic sul pulsante **Send** (Invia) per inviare l'applicazione ad Apple per la revisione.
6. Application Loader indicherà quando il file è stato caricato.

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>Stato di iTunes Connect

Se si accede di nuovo a iTunes Connect e si seleziona l'app nell'elenco delle app disponibili, lo stato in iTunes Connect dovrebbe ora indicare che è **in attesa di revisione** (è possibile che la lettura del caricamento venga temporaneamente **ricevuta** durante l'elaborazione):

[![](app-store-publishing-images/image21.png "The status in iTunes Connect showing Waiting for Review")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Troubleshooting

Se si verificano problemi durante l'invio dell'app Novell. tvOS all'App Store di Apple TV, vedere la guida alla [risoluzione dei problemi](~/ios/tvos/troubleshooting.md) . Contiene diversi problemi noti che possono verificarsi e come risolverli in Novell. tvOS.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato una guida dettagliata alla configurazione, compilazione e invio di un'app per la pubblicazione di App Store di Apple TV. Prima sono stati trattati i passaggi necessari per creare e installare un profilo di provisioning di distribuzione. Viene quindi illustrato come usare Visual Studio per Mac per creare una compilazione di distribuzione. Infine, è stato illustrato come usare iTunes Connect e lo strumento di archiviazione Xcode per inviare un'applicazione all'App Store di Apple TV.

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md)
- [Preparare l'invio dell'app per il nuovo Apple TV](https://developer.apple.com/tvos/submit/)
- [App Store Submission Tips](https://developer.apple.com/appstore/resources/submission/tips.html) (Suggerimenti per l'invio nell'App Store)
- [Common App Rejections](https://developer.apple.com/app-store/review/rejections/) (Rifiuti comuni di app)
- [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) (Linee guida sulle recensioni nell'App Store)
