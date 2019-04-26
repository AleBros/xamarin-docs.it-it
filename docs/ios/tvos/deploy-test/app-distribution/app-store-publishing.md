---
title: Pubblicazione in Apple TV App Store
description: Questo documento descrive come pubblicare un'app di Store di App Apple TV. Illustra come configurare, eseguire il provisioning, compilare e inviare un'applicazione tvOS compilata con Xamarin.
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: b941bcc8588e7fb0377430cca2829ad72ecbc8c6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416677"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>Pubblicazione in Apple TV App Store

Distribuire applicazioni a tutti i dispositivi di Apple TV, Apple richiede che le app siano pubblicate tramite il *Apple TV App Store*, rendendo il percorso di acquisti unificato per le app tvOS Store l'App. Gli sviluppatori di molti tipi di App possono l'iniziale maiuscola nell'enorme successo di questo singolo punto di distribuzione. La Store di App Apple TV è una soluzione chiavi in mano, offre agli sviluppatori di app sistemi di distribuzione e di pagamento.

Il processo di invio di un'applicazione per la Store di App Apple TV comporta:

1. Creazione di un *ID App* e selezione degli *entitlement*.
2. Creazione di un *profilo di provisioning di distribuzione*.
3. Con questo profilo per compilare l'app.
4. Invio dell'app attraverso *iTunes Connect*.


Questo articolo illustra tutti i passaggi necessari per eseguire il provisioning, compilare e inviare un'app per la distribuzione di Apple TV App Store.

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>Prima di inviare un'applicazione

Dopo aver inviato un'app per la pubblicazione per la Store di App Apple TV, la chiamata passa attraverso un processo di revisione di Apple per garantire che soddisfi le linee guida di Apple per la qualità e contenuto. Se l'applicazione non soddisfa queste linee guida, Apple la rifiuterà. A questo punto, sarà necessario risolvere i problemi di non conformità segnalati da Apple e inviarla di nuovo.
Per avere buone probabilità di superare il processo di revisione di Apple, è consigliabile familiarizzare con queste linee guida e tentare di adattare l'applicazione a tali regole. Linee guida di Apple sono disponibili all'indirizzo [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) e [preparare Your App inoltro per il nuovo televisore Apple](https://developer.apple.com/tvos/submit/).

Di seguito sono specificati un paio di aspetti da verificare prima di inviare un'app:

1. Assicurarsi che la descrizione dell'app corrisponde alla funzionalità incluse nell'app.
2. Verificare che nell'app non si verifichino arresti anomali in condizioni di normale utilizzo, Ciò include l'utilizzo in tutti i dispositivi Apple TV che è supportato.


Apple offre anche un elenco di suggerimenti per la registrazione di Apple TV App Store. disponibili nella pagina relativa alla [distribuzione nell'pp Store](https://developer.apple.com/appstore/resources/submission/tips.html).

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configurazione dell'applicazione in iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) è una suite di strumenti basati sul web, tra le altre cose, la gestione delle App tvOS nel Store di App Apple TV. L'app xamarin. tvos dovrà sia correttamente impostata e configurata in iTunes Connect prima che può essere inviata ad Apple per la revisione e, essere rilasciata come app gratuita nel Store di App Apple TV o in vendita.

Seguire questa procedura:

1. Verificare che esistano i contratti appropriati e che siano aggiornati nella sezione **Agreements, Tax, and Banking** (Contratti, imposte e banche) di iTunes Connect per poter rilasciare un'applicazione iOS gratuita o in vendita.
2. Creare una nuova **Record iTunes Connect** per l'applicazione e specificare relativo **nome visualizzato** (come illustrato nella Store di App Apple TV).
3. Selezionare **Sale Price** (Prezzo di vendita) o specificare che l'applicazione viene rilasciata gratuitamente.
4. Fornire un' **icona dell'App Store** (icona di grandi dimensioni) e le schermate dell'applicazione in azione, nei dispositivi Apple TV supporta. Vedere la [uso di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) Guida per altre informazioni.
5. Fornire un chiaro, conciso **descrizione** comprende le funzionalità dell'app e trarre vantaggio per l'utente finale.
6. Fornire **categorie**, **sottocategorie**, e **parole chiave** per consentire all'utente di trovare la tua app nel Store di App Apple TV.
7. Specificare gli URL di **Contact** (Contatto) e **Support** (Supporto) al sito Web richiesto da Apple.
8. Impostare l'applicazione **Rating**, che viene usato per i controlli dei genitori di Store di App Apple TV.
9. Configurare le tecnologie facoltative dell'App Store, ad esempio **Game Center** e **Acquisto in-app**.

Per altre informazioni, vedere la [configurare l'App tvOS in iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) documentazione.

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>Preparazione per la distribuzione nell'App Store

Per pubblicare un'app di Store di App Apple TV, è necessario innanzitutto compilare la soluzione per la distribuzione, che comprende molti passaggi. Le sezioni seguenti vengono illustrate tutte le operazioni necessarie per preparare un'app xamarin. tvos per la pubblicazione in modo che può essere compilato e inviarlo alla Store di App Apple TV revisionata e rilasciata.

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>Provisioning dei servizi per le applicazioni

Apple offre una gamma di servizi applicazione speciali, denominati anche Entitlement, che possono essere attivati per l'app tvOS quando si crea un ID univoco per tale. Se si usa Entitlement personalizzati o No, è comunque necessario creare un ID univoco per l'app xamarin. tvos prima di poter essere pubblicata sulla Store di App Apple TV.

Per creare un ID app e facoltativamente selezionare gli entitlement è necessario seguire questa procedura che prevede l'uso del portale di provisioning iOS basato sul Web di Apple:

1. Selezionare **Provisioning** > **sviluppo**.
2. Fare clic sul pulsante **+** e specificare **Nome** (Nome) e **Bundle ID** (ID bundle) per la nuova applicazione.
3. Scorrere fino alla parte inferiore della schermata e selezionare una qualsiasi **servizi App** che sarà richiesto dall'app per xamarin. tvos.
4. Fare clic sul pulsante **Continue** (Continua) e seguire le istruzioni visualizzate per creare un nuovo ID app.

Oltre a selezionare e configurare i servizi delle applicazioni necessarie quando si definisce l'ID dell'App, è anche necessario configurare l'ID App e gli Entitlement nel progetto xamarin. tvos modificando entrambi i `Info.plist` e `Entitlements.plist` file.

Eseguire le operazioni seguenti in Visual Studio per Mac:

1. In **Esplora soluzioni** fare doppio clic sul file `Info.plist` per aprirlo e modificarlo.
2. Nel **destinazione applicazione tvOS** sezione, immettere un nome per l'applicazione e immettere il **identificatore del Bundle** creato al momento della definizione ID. l'App
3. Salvare le modifiche apportate al file `Info.plist`.
4. In **Esplora soluzioni** fare doppio clic sul file `Entitlements.plist` per aprirlo e modificarlo.
5. Selezionare e configurare gli Entitlement richiesti per l'app xamarin. tvos in modo che corrispondano alla configurazione eseguita in precedenza al momento della definizione ID. l'App
6. Salvare le modifiche apportate al file `Entitlements.plist`.

Per istruzioni dettagliate, vedere la documentazione [Provisioning dei servizi applicativi](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices). Mentre in questo documento è stata scritta per iOS, vengono utilizzati gli stessi passaggi per effettuare il provisioning di un'app xamarin. tvos.

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>Impostazione delle icone App, l'immagine di avvio e l'immagine in evidenza

Per un'app tvOS devono essere accettati da Apple per l'inclusione nella Store di App Apple TV, richiede corretto delle icone, avvio e le immagini di evidenza per tutti i dispositivi Apple TV che utilizzato per l'esecuzione in. Si sarà necessario aggiungere gli asset necessari di immagine che verrà compilato in un `Assets.car` file e inclusi nel bundle dell'app xamarin. tvos prima di essere caricate in iTunes Connect.

Per istruzioni dettagliate, vedere la [uso di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) documentazione.

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>Creazione e installazione di un profilo di distribuzione

Usa tvOS *profili di provisioning* per controllare come è possibile distribuire una build dell'applicazione specifica. Si tratta di file che contengono informazioni sul certificato usato per firmare un'app, sull'*ID applicazione* e sul punto in cui poter installare l'app. Per lo sviluppo e la distribuzione ad hoc, il profilo di provisioning include anche l'elenco dei dispositivi consentiti in cui è possibile distribuire l'app. Tuttavia, per la distribuzione di Apple TV App Store, solo le informazioni di ID app e i certificati sono inclusi, poiché è l'unico meccanismo per la distribuzione pubblica attraverso la Store di App Apple TV.

Il provisioning richiede questa procedura che prevede l'uso del portale di provisioning iOS basato sul Web di Apple:

1.  Selezionare **Provisioning** (Provisioning)  > **Distribution** (Distribuzione).
2.  Scegliere il **+** e selezionare il tipo di profilo di distribuzione che si vuole creare come **Apple TV App Store**.
3.  Selezionare un **ID App** nell'elenco a discesa per il quale si vuole creare un profilo di distribuzione.
4.  Selezionare il certificato necessario per firmare l'applicazione.
5.  Immettere un **Name** (Nome) per il nuovo **Distribution Profile** (Profilo di distribuzione) e generare il profilo.
6.  Aggiornare l'elenco dei profili disponibili in Xcode.
7.  Selezionare la distribuzione del profilo in Visual Studio per il Provisioning di **App Store** _configurazione della Build_.

Per istruzioni dettagliate, vedere [Creating a Distribution Profile](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile) (Creazione di un profilo di distribuzione) e [Selecting a Distribution Profile in a Xamarin.iOS Project](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile) (Selezione di un profilo di distribuzione in un progetto Xamarin.iOS). Anche in questo caso, entrambi i documenti sono specifici per iOS, ma la stessa tecnica viene usata per le app tvOS.


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>Impostazione della configurazione della build per l'applicazione

Per impostazione predefinita, quando si crea una nuova app xamarin. tvos _configurazioni della Build_ vengono creati automaticamente per entrambi **Debug** e **versione** distribuzione. Prima di finalizzare la build dell'app che sarà inviata ad Apple, ci sono alcune piccole modifiche da apportare alla base **rilascio** configurazione.

Seguire questa procedura:

1. Fare clic sui **nome progetto** nel **Esplora soluzioni** e la selezione **opzioni** per aprirle e modificarle.
2. Se la destinazione è una versione specifica di tvOS, selezionarlo in **compilazione tvOS** > **iOS SDK versione**. Per la versione di anteprima del supporto per tvOS, lasciare questo valore è impostato su **predefinito**.
3. Collegamento riduce la dimensione complessiva della tua app distribuibile in estraendola inutilizzati metodi, proprietà, classi, e così via e nella maggior parte dei casi è consigliabile lasciare il valore predefinito **collegamento SDK solo framework**. In alcune situazioni, ad esempio quando usano specifiche librerie di terze parti 3rd, potrebbe essere obbligata a impostare questo valore su **non collegare** per mantenere l'elemento necessario non verrà rimossa.
4. Per inviare un'app xamarin. tvos, è necessario usare il compilatore ottimizzatore LLVM. Assicurarsi che il **usare il compilatore ottimizzatore LLVM** casella è selezionata sotto il **rilascio** configurazione.
5. Apple è inoltre necessario che le app tvOS usano bitcode. Nuovamente nel **Release** configuration, aggiungere `--bitcode=asmonly` per il **argomenti aggiuntivi di mtouch** casella.
6. Il **file di immagine PNG ottimizzare per iOS** casella di controllo deve essere selezionata come ciò è utile per ridurre ulteriormente le dimensioni del risultato finale dell'app.
7. Debug dovrebbe *non* abilitare quanto aumenta la compilazione più inutilmente grandi.


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>Compilazione e invio del file distribuibile

Con l'app xamarin. tvos configurato correttamente, sono ora pronti per eseguire la compilazione di distribuzione finale che sarà inviata ad Apple per la revisione e rilascio.

#### <a name="build-your-archive"></a>Compilare l'archivio

1. Selezionare la configurazione **Rilascio | Dispositivo** in Visual Studio per Mac:

    ![](app-store-publishing-images/buildxs01new.png "Selezionare la configurazione di rilascio")
2. Scegliere **Archivia per la pubblicazione** dal menu **Compila**:

    [![](app-store-publishing-images/buildxs02new.png "Selezionare Archivia per la pubblicazione")](app-store-publishing-images/buildxs02new.png#lightbox)
3. Dopo aver creato l'archivio, sarà visualizzata la visualizzazione **Archivi**:

    [![](app-store-publishing-images/buildxs03new.png "La visualizzazione archivi")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>Firmare e distribuire l'app

Ogni volta in cui l'applicazione viene compilata per l'archivio, si apre automaticamente la *visualizzazione Archivi*, in cui vengono visualizzati tutti i progetti archiviati, raggruppati per tipo di soluzione. Per impostazione predefinita, questa visualizzazione contiene soltanto la soluzione attualmente aperta. Per visualizzare tutte le soluzioni con archivi, fare clic sull'opzione **Mostra tutti gli archivi**.

È consigliabile mantenere gli archivi distribuiti ai clienti (distribuzione tramite App Store o Enterprise), in modo che le informazioni di debug generate possano essere rappresentate in futuro.

Per firmare l'app e prepararla per la distribuzione:

1. Selezionare il **firma e Distribuisci...** , come illustrato di seguito:

    [![](app-store-publishing-images/buildxs04new.png ", Selezionare theSign e Distribuisci...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. Si aprirà la pubblicazione guidata. Selezionare il canale di distribuzione **App Store** per creare un pacchetto e aprire Application Loader:

    [![](app-store-publishing-images/distribute01.png "Selezionare il canale di distribuzione di App Store")](app-store-publishing-images/distribute01.png#lightbox)
3. Nella schermata profilo di Provisioning, selezionare l'identità di firma e profilo di provisioning corrispondente o ripetere la firma con un'altra identità:

    [![](app-store-publishing-images/distribute02.png "Selezionare l'identità di firma e profilo di provisioning corrispondente")](app-store-publishing-images/distribute02.png#lightbox)
4. Verificare i dettagli del pacchetto e fare clic su **Pubblica** per salvare il pacchetto `.ipa`:

    [![](app-store-publishing-images/distribute03.png "Verificare i dettagli del pacchetto")](app-store-publishing-images/distribute03.png#lightbox)
5. Dopo aver salvato il pacchetto `.ipa`, l'app è pronta per essere caricata in iTunes Connect tramite Application Loader:

    [![](app-store-publishing-images/distribute04.png "Caricata in iTunes Connect tramite Application Loader")](app-store-publishing-images/distribute04.png#lightbox)

Dopo aver creato e archiviato la compilazione di distribuzione, è possibile inviare l'applicazione a iTunes Connect.

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>Invio dell'app ad Apple

Dopo aver completato la compilazione di distribuzione, è possibile inviare l'applicazione iOS ad Apple per la revisione e il rilascio nell'App Store.


Il flusso di lavoro di archivio in Visual Studio per Mac aprirà Application Loader automaticamente, dopo aver salvato il `.ipa`:

2. Selezionare *Deliver Your App* (Invia l'app) e fare clic sul pulsante *Choose* (Scegli):

    [![](app-store-publishing-images/publishvs01.png "Selezionare l'invio dell'app")](app-store-publishing-images/publishvs01.png#lightbox)

3. Selezionare il file ZIP o il file IPA creato in precedenza e fare clic sul pulsante **OK**.
4. Application Loader convaliderà il file:

    [![](app-store-publishing-images/publishvs02.png "Schermata di convalida Application Loader")](app-store-publishing-images/publishvs02.png#lightbox)
5. Fare clic sul pulsante *Next* (Avanti). L'applicazione sarà convalidati per l'App Store:

    [![](app-store-publishing-images/publishvs03.png "L'applicazione viene convalidato in base l'App Store")](app-store-publishing-images/publishvs03.png#lightbox)
6. Fare clic sul pulsante **Send** (Invia) per inviare l'applicazione ad Apple per la revisione.
7. Application Loader indicherà quando il file è stato caricato.

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>Stato di iTunes Connect

Se si accede in iTunes Connect e seleziona l'app nell'elenco delle App disponibili, lo stato in iTunes Connect dovrebbe ora mostra che è **in attesa per la revisione** (può leggere temporaneamente **caricare ricevuto** durante l'elaborazione):

[![](app-store-publishing-images/image21.png "Lo stato in iTunes Connect che mostra in attesa per la revisione")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si sono verificati problemi di invio dell'app xamarin. tvos ad la Store di App Apple TV, vedere la [Troubleshooting](~/ios/tvos/troubleshooting.md) Guida. Contiene alcuni problemi noti che possono verificarsi e come risolverli in di xamarin. tvos.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo è presentata una Guida dettagliata alla configurazione, la creazione e invio di un'app per la pubblicazione di Apple TV App Store. Prima sono stati trattati i passaggi necessari per creare e installare un profilo di provisioning di distribuzione. Successivamente, è stato illustrato come usare Visual Studio per Mac per creare una build di distribuzione. Infine, illustra come usare iTunes Connect e lo strumento di archiviazione di Xcode per inviare un'applicazione per la Store di App Apple TV.


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md)
- [Preparare la presentazione di App per il nuovo televisore Apple](https://developer.apple.com/tvos/submit/)
- [App Store Submission Tips](https://developer.apple.com/appstore/resources/submission/tips.html) (Suggerimenti per l'invio nell'App Store)
- [Common App Rejections](https://developer.apple.com/app-store/review/rejections/) (Rifiuti comuni di app)
- [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) (Linee guida sulle recensioni nell'App Store)
