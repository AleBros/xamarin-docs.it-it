---
title: Pubblicazione in App Store di Apple TV
description: Questo documento descrive come pubblicare un'app in App Store di Apple TV. Illustra come configurare, eseguire il provisioning, compilare e inviare un'applicazione tvOS compilata con Xamarin.
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: ac905caaf0bdefe7f0c5502be0bd63102ca5a813
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789304"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>Pubblicazione in App Store di Apple TV

In ordine di distribuire applicazioni a tutti i dispositivi di Apple TV, Apple richiede che le app da pubblicare tramite il *App Store di Apple TV*, effettua la posizione di acquisto unica per tvOS app di App Store. Gli sviluppatori di molti tipi di applicazioni può in maiuscolo il successo notevole di questo singolo punto di distribuzione. App Store di Apple TV è una soluzione chiavi in mano, offrendo agli sviluppatori di app di sistemi di distribuzione e di pagamento.

Il processo di invio di un'applicazione in App Store di Apple TV include:

1. Creazione di un *ID App* e selezione degli *entitlement*.
2. Creazione di un *profilo di provisioning di distribuzione*.
3. Con questo profilo per compilare l'app.
4. Invio dell'app tramite *iTunes Connect*.


In questo articolo verranno illustrate in tutti i passaggi necessari per eseguire il provisioning, compilare e invii un'app per la distribuzione di App Store di Apple TV.

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>Prima di inviare un'applicazione

Dopo l'invio di un'app per la pubblicazione di App Store di Apple TV, passano attraverso un processo di revisione da Apple per garantire che soddisfi le linee guida di Apple per contenuto e la qualità. Se l'applicazione non soddisfa queste linee guida, Apple la rifiuterà. A questo punto, sarà necessario risolvere i problemi di non conformità segnalati da Apple e inviarla di nuovo.
Per avere buone probabilità di superare il processo di revisione di Apple, è consigliabile familiarizzare con queste linee guida e tentare di adattare l'applicazione a tali regole. Sono disponibili linee guida di Apple [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) e [preparare l'App invio per il nuovo Apple TV](https://developer.apple.com/tvos/submit/).

Di seguito sono specificati un paio di aspetti da verificare prima di inviare un'app:

1. Verificare che la descrizione dell'app corrisponde alla funzionalità inclusa nell'app.
2. Test che non viene arrestato in modo anomalo l'app in condizioni di utilizzo normale. Ciò include l'utilizzo su ogni dispositivo di Apple TV, che è supportata.


Apple gestisce anche un elenco di suggerimenti per la registrazione di App Store di Apple TV. disponibili nella pagina relativa alla [distribuzione nell'pp Store](https://developer.apple.com/appstore/resources/submission/tips.html).

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configurazione dell'applicazione in iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) è una suite di strumenti basati sul web, tra le altre cose, la gestione di App tvOS in App Store di Apple TV. L'app Xamarin.tvOS dovrà essere correttamente il programma di installazione e configurata in iTunes Connect prima che può essere inviata a Apple per la revisione e alla fine, rilasciato per la vendita o come un'applicazione disponibile in App Store di Apple TV.

Seguire questa procedura:

1. Verificare che esistano i contratti appropriati e che siano aggiornati nella sezione **Agreements, Tax, and Banking** (Contratti, imposte e banche) di iTunes Connect per poter rilasciare un'applicazione iOS gratuita o in vendita.
2. Creare un nuovo **iTunes Connect Record** per l'applicazione e specificare il relativo **nome visualizzato** (come mostrato nell'App Store di Apple TV).
3. Selezionare **Sale Price** (Prezzo di vendita) o specificare che l'applicazione viene rilasciata gratuitamente.
4. Fornire un **App Store icona** (icona di grandi dimensioni) e le schermate dell'applicazione in azione, sui dispositivi Apple TV supporta. Vedere il nostro [utilizzo di immagini e icone](~/ios/tvos/app-fundamentals/icons-images.md) Guida per ulteriori dettagli.
5. Fornire un chiaro, conciso **descrizione** dell'app, incluse le funzionalità e trarre vantaggio per l'utente finale.
6. Fornire **categorie**, **sottocategorie**, e **parole chiave** per consentire all'utente di trovare l'app in App Store di Apple TV.
7. Specificare gli URL di **Contact** (Contatto) e **Support** (Supporto) al sito Web richiesto da Apple.
8. Impostazione dell'applicazione **classificazione**, che viene utilizzato dal controllo genitori in App Store di Apple TV.
9. Configurare le tecnologie facoltative dell'App Store, ad esempio **Game Center** e **Acquisto in-app**.

Per ulteriori dettagli, consultare il nostro [configurare l'App tvOS in iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) documentazione.

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>Preparazione per la distribuzione nell'App Store

Per pubblicare un'app in App Store di Apple TV, è innanzitutto necessario compilarlo per la distribuzione, che include il numero di passaggi. Nelle sezioni seguenti vengono illustrate tutte le operazioni necessarie per preparare un'app Xamarin.tvOS per la pubblicazione in modo che può essere compilato e li inviano all'App Store di Apple TV per la revisione e versione.

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>Provisioning dei servizi per le applicazioni

Apple fornisce una selezione di servizi delle applicazioni speciale, denominato anche dei diritti, che possono essere attivati per l'app tvOS quando si crea un ID univoco per tale. Se si utilizza diritti personalizzati o non, sarà necessario creare un ID univoco per l'app Xamarin.tvOS prima di poter essere pubblicato in App Store di Apple TV.

Per creare un ID app e facoltativamente selezionare gli entitlement è necessario seguire questa procedura che prevede l'uso del portale di provisioning iOS basato sul Web di Apple:

1. Selezionare **Provisioning** > **sviluppo**.
2. Fare clic sul pulsante **+** e specificare **Nome** (Nome) e **Bundle ID** (ID bundle) per la nuova applicazione.
3. Scorrere fino alla fine della schermata e selezionare una qualsiasi **servizi App** che sarà necessario dall'app Xamarin.tvOS.
4. Fare clic sul pulsante **Continue** (Continua) e seguire le istruzioni visualizzate per creare un nuovo ID app.

Oltre a selezionare e configurare i servizi delle applicazioni necessarie quando si definisce l'ID di App, è inoltre necessario configurare l'ID dell'App e i diritti nel progetto Xamarin.tvOS modificando entrambi i `Info.plist` e `Entitlements.plist` file.

Eseguire le operazioni seguenti in Visual Studio per Mac:

1. In **Esplora soluzioni** fare doppio clic sul file `Info.plist` per aprirlo e modificarlo.
2. Nel **tvOS applicazione di destinazione** sezione, immettere un nome per l'applicazione e immettere il **identificatore Bundle** creato al momento di definire il ID. App
3. Salvare le modifiche apportate al file `Info.plist`.
4. In **Esplora soluzioni** fare doppio clic sul file `Entitlements.plist` per aprirlo e modificarlo.
5. Selezionare e configurare i diritti necessari per si Xamarin.tvOS app in modo che corrispondano il programma di installazione eseguite di sopra, al momento di definire il ID. App
6. Salvare le modifiche apportate al file `Entitlements.plist`.

Per istruzioni dettagliate, vedere la documentazione [Provisioning dei servizi applicativi](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices). Sebbene in questo documento è stato scritto per iOS, vengono utilizzati gli stessi passaggi per eseguire il provisioning di un'app Xamarin.tvOS.

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>Impostazione di App icone, immagine di avvio e di immagine scaffale superiore

Per un'app tvOS di essere accettati da Apple per l'inclusione in App Store di Apple TV, è necessario icone appropriate, avvio e immagini scaffale superiore per tutti i dispositivi di Apple TV verrà eseguiti nel. È necessario aggiungere gli asset di immagini necessarie che verranno compilati in un `Assets.car` file e incluso nel bundle dell'app Xamarin.tvOS prima di essere caricate in iTunes Connect.

Per istruzioni dettagliate, vedere il nostro [utilizzo di immagini e icone](~/ios/tvos/app-fundamentals/icons-images.md) documentazione.

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>Creazione e installazione di un profilo di distribuzione

Usa tvOS *profili di provisioning* per controllare come è possibile distribuire una compilazione specifica applicazione. Si tratta di file che contengono informazioni sul certificato usato per firmare un'app, sull'*ID applicazione* e sul punto in cui poter installare l'app. Per lo sviluppo e la distribuzione ad hoc, il profilo di provisioning include anche l'elenco dei dispositivi consentiti in cui è possibile distribuire l'app. Tuttavia, per la distribuzione di App Store di Apple TV, solo le informazioni ID certificato e app vengono inclusi, poiché è l'unico meccanismo per la distribuzione pubblica tramite App Store di Apple TV.

Il provisioning richiede questa procedura che prevede l'uso del portale di provisioning iOS basato sul Web di Apple:

1.  Selezionare **Provisioning** (Provisioning)  > **Distribution** (Distribuzione).
2.  Fare clic su di **+** e selezionare il tipo di profilo di distribuzione che si desidera creare come **App Store di Apple TV**.
3.  Selezionare un **ID App** nell'elenco a discesa per il quale si vuole creare un profilo di distribuzione.
4.  Selezionare il certificato richiesto per firmare l'applicazione.
5.  Immettere un **Name** (Nome) per il nuovo **Distribution Profile** (Profilo di distribuzione) e generare il profilo.
6.  Aggiornare l'elenco dei profili disponibili in Xcode.
7.  Selezionare la distribuzione in Visual Studio per il profilo di Provisioning di **App Store** _configurazione compilazione_.

Per istruzioni dettagliate, vedere [Creating a Distribution Profile](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile) (Creazione di un profilo di distribuzione) e [Selecting a Distribution Profile in a Xamarin.iOS Project](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile) (Selezione di un profilo di distribuzione in un progetto Xamarin.iOS). Nuovamente, entrambi i documenti sono specifici per iOS ma viene utilizzata la stessa tecnica per le app tvOS.


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>Impostazione della configurazione della build per l'applicazione

Per impostazione predefinita, quando si crea una nuova app Xamarin.tvOS, _configurazioni della Build_ vengono create automaticamente sia per **Debug** e **versione** distribuzione. Prima di eseguire la build dell'app che verrà inviato ad Apple, esistono alcune modifiche che è necessario apportare alla base **versione** configurazione.

Seguire questa procedura:

1. Fare clic sul **nome progetto** nel **Esplora** e selezione **opzioni** per aprirli e la modifica.
2. Se la destinazione è una versione specifica di tvOS, selezionarlo in **tvOS compilazione** > **iOS SDK versione**. La versione di anteprima di supporto per tvOS, lasciare questo valore è impostato su **predefinito**.
3. Collegamento riduce le dimensioni complessive della tua app distribuibile rimuovendo le classi di metodi, proprietà inutilizzate, e così via. e nella maggior parte dei casi lasciare il valore predefinito di **collegamento SDK framework solo**. In alcune situazioni, ad esempio, quando tramite determinati 3rd librerie di terze parti, potrebbe essere necessario impostare questo valore su **non collegare** per mantenere l'elemento necessario non verrà rimossa.
4. Per fornire un'app Xamarin.tvOS, è necessario usare il compilatore di ottimizzazione LLVM. Verificare che il **utilizzare l'ottimizzazione del compilatore LLVM** è selezionata la casella sotto il **versione** configurazione.
5. Apple è inoltre necessario che le app tvOS utilizzano bitcode. Nuovo nel **versione** configurazione, aggiungere `--bitcode=asmonly` per il **mtouch ulteriori argomenti** casella.
6. Il **i file di immagine PNG ottimizzare per iOS** casella di controllo deve essere selezionata come ciò è utile per ridurre ulteriormente le dimensioni del risultato finale dell'app.
7. Il debug deve *non* essere abilitato come effettuare maggiore inutilmente la compilazione.


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>Compilazione e invio del file distribuibile

Con l'app Xamarin.tvOS configurato correttamente, si è ora pronti per eseguire la compilazione di distribuzione finale che verrà inviato ad Apple per la revisione e versione.

#### <a name="build-your-archive"></a>Compilare l'archivio

1. Selezionare la configurazione **Rilascio | Dispositivo** in Visual Studio per Mac:

    ![](app-store-publishing-images/buildxs01new.png "Selezionare la configurazione di rilascio")
2. Scegliere **Archivia per la pubblicazione** dal menu **Compila**:

    [![](app-store-publishing-images/buildxs02new.png "Selezionare Archivia per la pubblicazione")](app-store-publishing-images/buildxs02new.png#lightbox)
3. Dopo aver creato l'archivio, sarà visualizzata la visualizzazione **Archivi**:

    [![](app-store-publishing-images/buildxs03new.png "La visualizzazione di archivi")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>Firmare e distribuire l'app

Ogni volta in cui l'applicazione viene compilata per l'archivio, si apre automaticamente la *visualizzazione Archivi*, in cui vengono visualizzati tutti i progetti archiviati, raggruppati per tipo di soluzione. Per impostazione predefinita, questa visualizzazione contiene soltanto la soluzione attualmente aperta. Per visualizzare tutte le soluzioni con archivi, fare clic sull'opzione **Mostra tutti gli archivi**.

È consigliabile mantenere gli archivi distribuiti ai clienti (distribuzione tramite App Store o Enterprise), in modo che le informazioni di debug generate possano essere rappresentate in futuro.

Per firmare l'app e prepararla per la distribuzione:

1. Selezionare il **firmare e distribuire...** , illustrato di seguito:

    [![](app-store-publishing-images/buildxs04new.png ", Selezionare theSign e distribuzione...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. Si aprirà la pubblicazione guidata. Selezionare il canale di distribuzione **App Store** per creare un pacchetto e aprire Application Loader:

    [![](app-store-publishing-images/distribute01.png "Selezionare il canale di distribuzione di App Store")](app-store-publishing-images/distribute01.png#lightbox)
3. Nella schermata di profilo di Provisioning, selezionare l'identità di firma e la corrispondente profilo di provisioning o firmare nuovamente con un'altra identità:

    [![](app-store-publishing-images/distribute02.png "Selezionare le identità di firma e profilo di provisioning di corrispondente")](app-store-publishing-images/distribute02.png#lightbox)
4. Verificare i dettagli del pacchetto e fare clic su **Pubblica** per salvare il pacchetto `.ipa`:

    [![](app-store-publishing-images/distribute03.png "Verificare i dettagli del pacchetto")](app-store-publishing-images/distribute03.png#lightbox)
5. Dopo aver salvato il pacchetto `.ipa`, l'app è pronta per essere caricata in iTunes Connect tramite Application Loader:

    [![](app-store-publishing-images/distribute04.png "Caricato in iTunes Connect tramite il caricatore dell'applicazione")](app-store-publishing-images/distribute04.png#lightbox)

Dopo aver creato e archiviato la compilazione di distribuzione, è possibile inviare l'applicazione a iTunes Connect.

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>Invio dell'app ad Apple

Dopo aver completato la compilazione di distribuzione, è possibile inviare l'applicazione iOS ad Apple per la revisione e il rilascio nell'App Store.


Il flusso di lavoro di archiviazione in Visual Studio per Mac verrà aperta Application Loader automaticamente, dopo aver salvato il `.ipa`:

2. Selezionare *Deliver Your App* (Invia l'app) e fare clic sul pulsante *Choose* (Scegli):

    [![](app-store-publishing-images/publishvs01.png "Selezionare l'invio dell'app")](app-store-publishing-images/publishvs01.png#lightbox)

3. Selezionare il file ZIP o il file IPA creato in precedenza e fare clic sul pulsante **OK**.
4. Application Loader convaliderà il file:

    [![](app-store-publishing-images/publishvs02.png "La schermata convalida Loader di applicazioni")](app-store-publishing-images/publishvs02.png#lightbox)
5. Fare clic sul pulsante *Next* (Avanti). L'applicazione sarà convalidati per l'App Store:

    [![](app-store-publishing-images/publishvs03.png "L'applicazione venga convalidato in base all'App Store")](app-store-publishing-images/publishvs03.png#lightbox)
6. Fare clic sul pulsante **Send** (Invia) per inviare l'applicazione ad Apple per la revisione.
7. Application Loader indicherà quando il file è stato caricato.

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>Stato di iTunes Connect

Se si accedere di nuovo iTunes Connect e seleziona l'app nell'elenco delle App disponibili, lo stato in iTunes Connect deve ora mostra che è **in attesa per la revisione** (può leggere temporaneamente **caricare ricevuto** durante l'elaborazione):

[![](app-store-publishing-images/image21.png "Lo stato in iTunes Connect che mostra in attesa per la revisione")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si sono verificati problemi di invio dell'app Xamarin.tvOS per App Store di Apple TV, consultare il nostro [Troubleshooting](~/ios/tvos/troubleshooting.md) Guida. Contiene diversi problemi che potrebbero verificarsi e come risolverli nel Xamarin.tvOS.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è visualizzata una Guida dettagliata alla configurazione, la creazione e invio di un'app per la pubblicazione di App Store di Apple TV. Prima sono stati trattati i passaggi necessari per creare e installare un profilo di provisioning di distribuzione. Successivamente, esaminato in dettaglio come utilizzare Visual Studio per Mac per creare una build di distribuzione. Infine, si è stato spiegato come usare iTunes Connect e lo strumento di archiviazione Xcode per inviare una richiesta all'App Store di Apple TV.


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md)
- [Preparare l'invio di App per il nuovo Apple TV](https://developer.apple.com/tvos/submit/)
- [App Store Submission Tips](https://developer.apple.com/appstore/resources/submission/tips.html) (Suggerimenti per l'invio nell'App Store)
- [Common App Rejections](https://developer.apple.com/app-store/review/rejections/) (Rifiuti comuni di app)
- [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) (Linee guida sulle recensioni nell'App Store)
