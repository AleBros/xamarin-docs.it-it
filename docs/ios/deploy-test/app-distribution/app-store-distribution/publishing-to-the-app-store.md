---
title: Pubblicazione di app Xamarin.iOS nell'App Store
description: Questo documento descrive come configurare, compilare e pubblicare un'applicazione Xamarin.iOS per la distribuzione nell'App Store.
ms.prod: xamarin
ms.assetid: DFBCC0BA-D233-4DC4-8545-AFBD3768C3B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 3803d7e14b161a7c166bcae37e3d9f46b7637984
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026641"
---
# <a name="publishing-xamarinios-apps-to-the-app-store"></a>Pubblicazione di app Xamarin.iOS nell'App Store

Per pubblicare un'app nell'[App Store](https://www.apple.com/ios/app-store/) è necessario che lo sviluppatore dell'app invii prima l'app con screenshot, descrizione, icone e altre informazioni ad Apple per la revisione. Dopo l'approvazione, Apple inserisce l'app nell'App Store dove gli utenti possono acquistarla e installarla direttamente dai propri dispositivi iOS.

Questa guida descrive i passaggi da eseguire per preparare un'app per l'App Store e inviarla ad Apple per la revisione. Nello specifico, la guida tratta gli argomenti seguenti:

> [!div class="checklist"]
>
> - Linee guida di revisione dell'App Store
> - Impostazione di un ID app e degli entitlement
> - Offerta di un'icona App Store e delle icone di app
> - Configurazione di un profilo di provisioning dell'App Store
> - Aggiornamento della configurazione della build di **versione**
> - Configurazione dell'app in iTunes Connect
> - Creazione dell'app e invio ad Apple

> [!IMPORTANT]
> Apple [ha indicato](https://developer.apple.com/ios/submit/) che a partire da marzo 2019 tutte le app e gli aggiornamenti inviati all'App Store devono essere stati compilati con iOS 12.1 SDK o versioni successive, disponibili in Xcode 10.1 o versioni successive.
> Le app devono anche supportare iPhone Xs e le dimensioni dello schermo 12,9" di iPad Pro.

## <a name="app-store-guidelines"></a>Linee guida dell'App Store

Prima di inviare un'app per la pubblicazione nell'App Store, assicurarsi che soddisfi gli standard definiti in [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) (Linee guida di revisione dell'App Store).
Quando si invia un'app all'App Store, Apple esegue una revisione dell'app per verificare che soddisfi i requisiti. Se l'app non soddisfa i requisiti, Apple rifiuterà l'app e sarà necessario risolvere i problemi segnalati e reinviarla.
Per questa ragione, è consigliabile conoscere le linee guida il prima possibile durante il processo di sviluppo.

Di seguito sono specificati un paio di aspetti da verificare prima di inviare un'app:

1. Assicurarsi che la descrizione dell'app corrisponda alla relativa funzionalità.
2. Verificare che nell'app non si verifichino arresti anomali in condizioni di normale utilizzo, inclusi tutti i dispositivi iOS supportati.

Esaminare anche le [risorse correlate all'App Store](https://developer.apple.com/app-store/resources/) offerte da Apple.

## <a name="set-up-an-app-id-and-entitlements"></a>Impostare l'ID app e gli entitlement

Ogni app iOS ha un ID app univoco a cui è associata una serie di servizi di applicazione denominati *entitlement*. Gli entitlement consentono alle app di eseguire diverse operazioni, ad esempio ricevere le notifiche push, accedere a funzioni iOS come HealthKit e molto altro ancora.

Per creare un ID app e selezionare gli entitlement necessari, visitare il [portale Apple Developer](https://developer.apple.com/account/) e seguire questa procedura:

1. Nella sezione **Certificates, IDs & Profiles** (Certificati, ID e profili) selezionare **Identifiers (Identificatori) > App IDs (ID app)**.
2. Fare clic sul pulsante **+** e specificare **Nome** (Nome) e **Bundle ID** (ID bundle) per la nuova applicazione.
3. Scorrere fino alla fine della schermata e selezionare un valore per **App Services** (Servizi app) che sarà richiesto dall'applicazione Xamarin.iOS. I servizi app sono descritti in dettaglio nella guida [Uso delle funzionalità in Xamarin.iOS](~/ios/deploy-test/provisioning/capabilities/index.md).
4. Fare clic sul pulsante **Continue** (Continua) e seguire le istruzioni visualizzate sullo schermo per creare il nuovo ID app.

Durante la definizione dell'ID app, oltre a selezionare e configurare i servizi dell'applicazione richiesti, è necessario configurare l'ID app e gli entitlement nel progetto Xamarin.iOS modificando i file **Info.plist** e **Entitlements.plist**. Per altre informazioni, vedere la guida [Uso degli entitlement in Xamarin.iOS](~/ios/deploy-test/provisioning/entitlements.md) che descrive come creare il file **Entitlements.plist** e il significato delle diverse impostazioni degli entitlement in esso contenute.

## <a name="include-an-app-store-icon"></a>Includere un'icona dell'App Store

Quando si invia un'app ad Apple, assicurarsi che includa un catalogo di asset che contiene un'icona dell'App Store. Per informazioni su come eseguire questa operazione, vedere la guida [Icone dell'App Store in Xamarin.iOS](~/ios/app-fundamentals/images-icons/app-store-icon.md).

## <a name="set-the-apps-icons-and-launch-screens"></a>Impostare le icone delle app e le schermate di avvio

Affinché Apple renda disponibile un'app iOS nell'App Store, è necessario che l'app disponga delle icone e delle schermate di avvio appropriate per tutti i dispositivi iOS in cui può essere eseguita. Per altre informazioni sull'impostazione delle icone dell'app e delle schermate di avvio, leggere le guide seguenti:

- [Icone dell'applicazione in Xamarin.iOS](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Schermate di avvio per le app Xamarin.iOS](~/ios/app-fundamentals/images-icons/launch-screens.md)

## <a name="create-and-install-an-app-store-provisioning-profile"></a>Creare e installare un profilo di provisioning dell'App Store

iOS usa i *profili di provisioning* per gestire la distribuzione di una build dell'applicazione specifica. Si tratta di file che contengono informazioni sul certificato usato per firmare un'app, sull'ID app e sul percorso in cui è possibile installare l'app. Per lo sviluppo e la distribuzione ad hoc, il profilo di provisioning include anche l'elenco dei dispositivi consentiti in cui è possibile distribuire l'app. Per la distribuzione tramite l'App Store, sono invece incluse solo le informazioni che riguardano il certificato e l'ID app, in quanto l'App Store è l'unico meccanismo per la distribuzione pubblica.

Per creare e installare un profilo di provisioning dell'App Store, seguire questa procedura:

1. Accedere al [portale Apple Developer](https://developer.apple.com/account/).
2. In **Certificates, IDs & Profiles** (Certificati, ID e profili) selezionare **Provisioning Profiles (Profili di provisioning) > Distribution (Distribuzione)**.
3. Fare clic sul pulsante **+**, selezionare **App Store** e fare clic su **Continue** (Continua).
4. Selezionare l'**App ID** (ID app) dall'elenco e fare clic su **Continue** (Continua).
5. Selezionare un certificato di firma e fare clic su **Continue** (Continua).
6. Immettere un **Profile Name** (Nome profilo) e fare clic su **Continue** (Continua) per generare il profilo.
7. Usare gli strumenti di [gestione account Apple](~/cross-platform/macios/apple-account-management.md) di Xamarin per scaricare il nuovo profilo di provisioning creato nel Mac. In Mac è anche possibile scaricare il profilo di provisioning direttamente dal portale Apple Developer e fare doppio clic su di esso per procedere all'installazione.

Per istruzioni dettagliate, vedere [Creazione di un profilo di provisioning di sviluppo](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) e [Selezione di un profilo di distribuzione in un progetto Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile).

## <a name="update-the-release-build-configuration"></a>Aggiornare la configurazione della build di versione

I nuovi progetti Xamarin.iOS impostano automaticamente le _configurazioni della build_ di **debug** e **versione**. Per configurare correttamente la build di **versione**, seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Dal **riquadro della soluzione** aprire **Info.plist**. Selezionare **Provisioning manuale**. Salvare e chiudere il file.
2. Fare clic con il pulsante destro del mouse su **Nome progetto** nel **riquadro della soluzione**, selezionare **Opzioni** e passare alla scheda **Compilazione iOS**.
3. Impostare **Configurazione** su **Versione** e **Piattaforma** su **iPhone**.
4. Per compilare con un SDK per iOS specifico, selezionarlo dall'elenco **Versione SDK**. In alternativa, lasciare questo valore impostato su **Predefinito**.
5. Il collegamento consente di ridurre le dimensioni complessive dell'applicazione rimuovendo il codice non utilizzato. Nella maggior parte dei casi **Comportamento del linker** deve essere impostato sul valore predefinito **Collega solo SDK del framework**. In alcune situazioni, ad esempio durante l'uso di alcune librerie di terze parti, può essere necessario impostare questo valore su **Non collegare** per assicurarsi che il codice necessario non venga rimosso. Per altre informazioni, vedere la guida [Collegamento di app Xamarin.iOS](~/ios/deploy-test/linker.md).
6. Selezionare **Ottimizza le immagini PNG** per ridurre ulteriormente le dimensioni dell'applicazione.
7. Il debug _non_ deve essere abilitato in quanto aumenta inutilmente le dimensioni della build.
8. Per iOS 11, selezionare una delle architetture del dispositivo che supporta **ARM64**. Per altre informazioni sulla compilazione per i dispositivi iOS a 64 bit, vedere la sezione **Abilitazione delle build a 64 bit delle app Xamarin.iOS** in [Considerazioni relative alla piattaforma a 32/64 bit](~/cross-platform/macios/32-and-64/index.md).
9. È possibile usare il compilatore **LLVM** per generare codice più veloce e conciso. Questa opzione, tuttavia, aumenta i tempi di compilazione.
10. In base alle esigenze dell'applicazione, è anche possibile modificare il tipo di **Garbage Collection** in uso e impostare l'**Internazionalizzazione**.

    Dopo aver impostato le opzioni descritte in precedenza, le impostazioni di compilazione dovrebbero essere simili alle seguenti:

    ![impostazioni di compilazione iOS](publishing-to-the-app-store-images/build-m157.png "impostazioni di compilazione iOS")

    Vedere anche la guida [Meccanismi di compilazione per iOS](~/ios/deploy-test/ios-build-mechanics.md) per una descrizione dettagliata delle impostazioni di compilazione.

11. Passare alla scheda **firma bundle iOS** . Se le opzioni non sono modificabili, assicurarsi che il **provisioning manuale** sia selezionato nel file **info. plist** .
12. Verificare che l'opzione **Configurazione** sia impostata su **Versione** e **Piattaforma** sia impostata su **iPhone**.
13. Impostare **Identità di firma** su **Distribuzione (automatico)**.
14. Per **Profilo di provisioning**, selezionare il profilo di provisioning dell'App Store [creato in precedenza](#create-and-install-an-app-store-provisioning-profile).

    Le opzioni di firma del bundle del progetto dovrebbero essere simili alle seguenti:

    ![Firma del bundle iOS](publishing-to-the-app-store-images/bundleSigning-m157.png "Firma del bundle iOS")

15. Fare clic su **OK** per salvare le modifiche alle proprietà del progetto.

# <a name="visual-studio-2019tabwindows"></a>[Visual Studio 2019](#tab/windows)

1. Verificare che Visual Studio 2019 sia stato [abbinato a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Fare clic con il pulsante destro del mouse sul **nome del progetto** in **Esplora soluzioni** e scegliere **Proprietà**.
3. Passare alla scheda **Compilazione iOS** e impostare **Configurazione** su **Versione** e **Piattaforma** su **iPhone**.
4. Per compilare con un SDK per iOS specifico, selezionarlo dall'elenco **Versione SDK**. In alternativa, lasciare questo valore impostato su **Predefinito**.
5. Il collegamento consente di ridurre le dimensioni complessive dell'applicazione rimuovendo il codice non utilizzato. Nella maggior parte dei casi **Comportamento del linker** deve essere impostato sul valore predefinito **Collega solo SDK del framework**. In alcune situazioni, ad esempio durante l'uso di alcune librerie di terze parti, può essere necessario impostare questo valore su **Non collegare** per assicurarsi che il codice necessario non venga rimosso. Per altre informazioni, vedere la guida [Collegamento di app Xamarin.iOS](~/ios/deploy-test/linker.md).
6. Selezionare **Ottimizza le immagini PNG** per ridurre ulteriormente le dimensioni dell'applicazione.
7. Il debug non deve essere abilitato in quanto aumenta inutilmente le dimensioni della build.
8. Per iOS 11, selezionare una delle architetture del dispositivo che supporta **ARM64**. Per altre informazioni sulla compilazione per i dispositivi iOS a 64 bit, vedere la sezione **Abilitazione delle build a 64 bit delle app Xamarin.iOS** in [Considerazioni relative alla piattaforma a 32/64 bit](~/cross-platform/macios/32-and-64/index.md).
9. È possibile usare il compilatore **LLVM** per generare codice più veloce e conciso. Questa opzione, tuttavia, aumenta i tempi di compilazione.
10. In base alle esigenze dell'applicazione, è anche possibile modificare il tipo di **Garbage Collection** in uso e impostare l'**Internazionalizzazione**.

    Dopo aver impostato le opzioni descritte in precedenza, le impostazioni di compilazione dovrebbero essere simili alle seguenti:

    ![impostazioni di compilazione iOS](publishing-to-the-app-store-images/build-w157.png "impostazioni di compilazione iOS")

    Vedere anche la guida [Meccanismi di compilazione per iOS](~/ios/deploy-test/ios-build-mechanics.md) per una descrizione dettagliata delle impostazioni di compilazione.

11. Passare alla scheda relativa alla **firma del bundle iOS** . Assicurarsi che la **configurazione** sia impostata su **Release**, che **Platform** sia impostato su **iPhone**e che sia selezionato il **provisioning manuale** .
12. Impostare **Identità di firma** su **Distribuzione (automatico)**.
13. Per **Profilo di provisioning**, selezionare il profilo di provisioning dell'App Store [creato in precedenza](#create-and-install-an-app-store-provisioning-profile).

    Le opzioni di firma del bundle del progetto dovrebbero essere simili alle seguenti:

    ![impostazioni di firma del bundle iOS](publishing-to-the-app-store-images/bundleSigning-w157.png "impostazioni di firma del bundle iOS")

14. Salvare la configurazione della build e chiuderla.

# <a name="visual-studio-2017tabwin-vs2017"></a>[Visual Studio 2017](#tab/win-vs2017)

1. Assicurarsi che Visual Studio 2017 sia stato [associato a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Fare clic con il pulsante destro del mouse sul **nome del progetto** in **Esplora soluzioni** e scegliere **Proprietà**.
3. Passare alla scheda **Compilazione iOS** e impostare **Configurazione** su **Versione** e **Piattaforma** su **iPhone**.
4. Per compilare con un SDK per iOS specifico, selezionarlo dall'elenco **Versione SDK**. In alternativa, lasciare questo valore impostato su **Predefinito**.
5. Il collegamento consente di ridurre le dimensioni complessive dell'applicazione rimuovendo il codice non utilizzato. Nella maggior parte dei casi **Comportamento del linker** deve essere impostato sul valore predefinito **Collega solo SDK del framework**. In alcune situazioni, ad esempio durante l'uso di alcune librerie di terze parti, può essere necessario impostare questo valore su **Non collegare** per assicurarsi che il codice necessario non venga rimosso. Per altre informazioni, vedere la guida [Collegamento di app Xamarin.iOS](~/ios/deploy-test/linker.md).
6. Selezionare **Ottimizza le immagini PNG** per ridurre ulteriormente le dimensioni dell'applicazione.
7. Il debug non deve essere abilitato in quanto aumenta inutilmente le dimensioni della build.
8. Per iOS 11, selezionare una delle architetture del dispositivo che supporta **ARM64**. Per altre informazioni sulla compilazione per i dispositivi iOS a 64 bit, vedere la sezione **Abilitazione delle build a 64 bit delle app Xamarin.iOS** in [Considerazioni relative alla piattaforma a 32/64 bit](~/cross-platform/macios/32-and-64/index.md).
9. È possibile usare il compilatore **LLVM** per generare codice più veloce e conciso. Questa opzione, tuttavia, aumenta i tempi di compilazione.
10. In base alle esigenze dell'applicazione, è anche possibile modificare il tipo di **Garbage Collection** in uso e impostare l'**Internazionalizzazione**.

    Dopo aver impostato le opzioni descritte in precedenza, le impostazioni di compilazione dovrebbero essere simili alle seguenti:

    ![impostazioni di compilazione iOS](publishing-to-the-app-store-images/build-w157.png "impostazioni di compilazione iOS")

    Vedere anche la guida [Meccanismi di compilazione per iOS](~/ios/deploy-test/ios-build-mechanics.md) per una descrizione dettagliata delle impostazioni di compilazione.

11. Passare alla scheda relativa alla **firma del bundle iOS** . Assicurarsi che la **configurazione** sia impostata su **Release**, che **Platform** sia impostato su **iPhone**e che sia selezionato il **provisioning manuale** .
12. Impostare **Identità di firma** su **Distribuzione (automatico)**.
13. Per **Profilo di provisioning**, selezionare il profilo di provisioning dell'App Store [creato in precedenza](#create-and-install-an-app-store-provisioning-profile).

    Le opzioni di firma del bundle del progetto dovrebbero essere simili alle seguenti:

    ![impostazioni di firma del bundle iOS](publishing-to-the-app-store-images/bundleSigning-w157.png "impostazioni di firma del bundle iOS")

14. Passare alla scheda **Opzioni IPA iOS**.
15. Verificare che l'opzione **Configurazione** sia impostata su **Versione** e **Piattaforma** sia impostata su **iPhone**.
16. Selezionare la casella di controllo **Compila archivio pacchetto iTunes (IPA)**. Questa impostazione farà in modo che ogni build di **versione** (che corrisponde alla configurazione selezionata) generi un file con estensione ipa. Questo file può essere inviato ad Apple per il rilascio nell'App Store.

    > [!NOTE]
    > I **metadati di iTunes** e **iTunesArtwork** non sono necessari per le versioni dell'App Store. Per altre informazioni, vedere [File iTunesMetadata.plist nelle app Xamarin.iOS](~/ios/deploy-test/app-distribution/itunesmetadata.md) e [iTunes Artwork](~/ios/app-fundamentals/images-icons/app-icons.md#itunes-artwork).

17. Per specificare un nome file con estensione ipa diverso dal nome del progetto Xamarin.iOS, immetterlo nel campo **Nome del pacchetto**.

    ![impostazioni di firma del bundle iOS](publishing-to-the-app-store-images/ipaOptions-w157.png "impostazioni di firma del bundle iOS")

18. Salvare la configurazione della build e chiuderla.

-----

## <a name="configure-your-app-in-itunes-connect"></a>Configurare l'app in iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com) comprende una serie di strumenti basati sul Web per la gestione delle applicazioni iOS nell'App Store. È necessario che l'applicazione Xamarin.iOS sia configurata correttamente in iTunes Connect prima di essere inviata ad Apple per la revisione e il rilascio nell'App Store.

Per informazioni su come eseguire questa operazione, vedere la guida [Configurazione di un'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).

## <a name="build-and-submit-your-app"></a>Compilare e inviare l'app

Con le impostazioni di compilazione configurate correttamente e iTunes Connect in attesa dell'invio, è possibile compilare l'app e inviarla ad Apple.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. In Visual Studio per Mac selezionare la configurazione della build **Versione** e un dispositivo (non un simulatore) per la compilazione.

    ![Configurazione della build e selezione della piattaforma](publishing-to-the-app-store-images/chooseConfig-m157.png "Configurazione della build e selezione della piattaforma")

2. Scegliere **Archivia per la pubblicazione** dal menu **Compila**.
3. Una volta creato l'archivio, verrà visualizzata la visualizzazione **archivi** . Fare clic su **Firma e distribuisci...** per aprire la procedura guidata di pubblicazione.

    ![Screenshot della posizione del pulsante firma e Distribuisci nella visualizzazione archivi.](publishing-to-the-app-store-images/archives-mac.png "Screenshot della posizione del pulsante firma e Distribuisci nella visualizzazione archivi.")

    > [!NOTE]
    > Per impostazione predefinita la visualizzazione **Archivi** mostra solo gli archivi per la soluzione aperta. Per visualizzare tutte le soluzioni con archivi, selezionare la casella di controllo **Mostra tutti gli archivi**. È consigliabile conservare gli archivi precedenti per poter usare le informazioni sul debug in essi contenute per aggiungere, se necessario, simboli di debug nei report relativi agli arresti anomali del sistema.

4. Selezionare il canale di distribuzione **App Store**. Scegliere **Avanti**.

5. Selezionare **carica** come destinazione. Scegliere **Avanti**.

6. Nella finestra **Profilo di provisioning** selezionare l'identità di firma, l'app e il profilo di provisioning. Scegliere **Avanti**.

    ![Screenshot della pagina della creazione guidata profilo di provisioning che mostra un'identità di firma valida, un'app e una selezione del profilo di provisioning.](publishing-to-the-app-store-images/provProfileSelect-mac.png "Screenshot della pagina della procedura guidata del profilo di provisioning con un'identità di firma, un'app e un profilo di provisioning validi selezionati.")

7. Nella finestra delle **informazioni di connessione dell'app Store** selezionare un nome utente Apple ID dal menu e immettere [una password specifica per l'app](https://support.apple.com/ht204397). Scegliere **Avanti**.

    ![Screenshot della pagina della procedura guidata per le informazioni di connessione dell'app Store che mostra un nome utente Apple ID selezionato.](publishing-to-the-app-store-images/connectInfo-mac.png "Screenshot della pagina della procedura guidata per le informazioni di connessione dell'app Store che mostra un nome utente Apple ID selezionato.")

8. Verificare i dettagli del pacchetto e fare clic su **pubblica**. Dopo aver selezionato un percorso in cui salvare il file con estensione IPA, l'app verrà caricata nell'App Store Connect.

    > [!NOTE]
    > È possibile che Apple rifiuti le app con **iTunesMetadata.plist** incluso nel file con estensione ipa generando un errore simile al seguente:
    >
    > `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`
    >
    > Per risolvere questo problema, vedere [questo post nei forum di Xamarin](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1).

# <a name="visual-studio-2019tabwindows"></a>[Visual Studio 2019](#tab/windows)

> [!NOTE]
> La pubblicazione nell'App Store è supportata in Visual Studio 2019 versione 16,3 e successive.

1. Assicurarsi che Visual Studio 2019 sia associato [a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Selezionare **versione** dall'elenco a discesa **configurazioni soluzione** e **iPhone** dall'elenco a discesa **piattaforme soluzione** .

    ![Screenshot della barra degli strumenti di Visual Studio che mostra la configurazione della soluzione impostata su Release, Platform della soluzione impostata su iPhone e destinazione impostata sul dispositivo.](publishing-to-the-app-store-images/chooseConfig-w157.png "Screenshot della barra degli strumenti di Visual Studio che mostra la configurazione della soluzione impostata su Release, Platform della soluzione impostata su iPhone e destinazione impostata sul dispositivo.")

3. Scegliere Archivia dal menu **Compila** **.** Verrà aperto **Gestione archivi** e verrà avviata la creazione di un archivio.

4. Una volta creato l'archivio, fare clic su **Distribuisci** per aprire la pubblicazione guidata.

    ![Screenshot della posizione del pulsante Distribuisci nella visualizzazione Gestione archivi.](publishing-to-the-app-store-images/archives-win.png "Screenshot della posizione del pulsante Distribuisci nella visualizzazione Gestione archivi.")

5. Selezionare il canale di distribuzione **App Store**.

6. Selezionare l'identità di firma e il profilo di provisioning. Fare clic su **carica per archiviare**.

    ![Screenshot della pubblicazione guidata che mostra un'identità di firma valida e una selezione del profilo di provisioning.](publishing-to-the-app-store-images/provProfileSelect-win.png "Screenshot della pubblicazione guidata che mostra un'identità di firma valida e una selezione del profilo di provisioning.")

7. Immettere l'ID Apple e [una password specifica dell'app](https://support.apple.com/ht204397). Fare clic su **OK** per avviare il caricamento dell'app nell'App Store Connect.

    ![Screenshot della finestra popup per immettere l'ID Apple e la password specifica dell'app.](publishing-to-the-app-store-images/connectInfo-win.png "Screenshot della finestra popup per immettere l'ID Apple e la password specifica dell'app.")

# <a name="visual-studio-2017tabwin-vs2017"></a>[Visual Studio 2017](#tab/win-vs2017)

> [!NOTE]
> Visual Studio 2017 non supporta il flusso di lavoro di pubblicazione completo disponibile in Visual Studio per Mac e Visual Studio 2019.
>
> I passaggi seguenti sono per Xcode 10.
>
> È comunque possibile seguire la procedura riportata di seguito per creare un. File IPA, ma per la distribuzione nell'App Store con Xcode 11 (necessario per il supporto di iOS 13) è necessario [usare Visual Studio per Mac](?tabs=macos#build-and-submit-your-app).

1. Assicurarsi che Visual Studio 2017 sia stato [associato a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Selezionare **Versione** dall'elenco a discesa **Configurazioni soluzione** di Visual Studio 2017 e **iPhone** dall'elenco a discesa **Piattaforme soluzione**.

    ![Configurazione della build e selezione della piattaforma](publishing-to-the-app-store-images/chooseConfig-w157.png "Configurazione della build e selezione della piattaforma")

3. Compilare il progetto. Viene creato un file con estensione ipa.

    > [!NOTE]
    > Nella sezione [Aggiornare la configurazione della build di versione](#update-the-release-build-configuration) della presente documentazione sono state configurate le impostazioni di compilazione dell'app per creare un file con estensione ipa per ogni build di **versione**.

4. Per trovare il file con estensione ipa nel computer Windows, fare clic con il pulsante destro del mouse sul nome del progetto Xamarin.iOS in **Esplora soluzioni** in Visual Studio 2019 o Visual Studio 2017 e scegliere **Apri cartella in Esplora file**. Quindi, in **Esplora file** in Windows passare alla sottodirectory **bin/iPhone/Release**. Se non è stato [personalizzato il percorso di output del file con estensione ipa](#customize-the-ipa-location), la directory dovrebbe corrispondere.
5. Per visualizzare il file con estensione ipa nell'host di compilazione Mac, fare clic con il pulsante destro del mouse sul nome del progetto Xamarin.iOS in **Esplora soluzioni** in Visual Studio 2019 o Visual Studio 2017 (Windows) e selezionare **Mostra il file IPA nel server di compilazione**. Verrà aperta una finestra **Finder** nell'host di compilazione Mac con il file con estensione ipa selezionato.

    > [!TIP]
    >
    > I passaggi seguenti sono validi solo se si usa Xcode 10 e si esegue la compilazione per iOS 12 e versioni precedenti.
    >
    > Per eseguire la distribuzione nell'App Store con Xcode 11 (per iOS 13), è necessario [usare Visual Studio per Mac](?tabs=macos#build-and-submit-your-app) per compilare e caricare l'app. Il **caricatore di applicazioni** non sarà disponibile per Xcode 11.

6. Nell'host di compilazione Mac aprire il **Caricatore di applicazione**. In Xcode selezionare **Xcode > Open Developer Tool (Apri strumento di sviluppo) > Application Loader (Caricatore di applicazione)**.

    > [!NOTE]
    > Per altre informazioni sullo strumento, vedere la [documentazione di Apple relativa ad Application Loader](https://help.apple.com/itc/apploader/#/apdS673accdb).

7. Accedere al Caricatore di applicazione (si noti che è necessario [creare una password specifica dell'app](https://support.apple.com/ht204397) per l'ID Apple).
8. Selezionare **Deliver Your App** (Invia l'app) e fare clic sul pulsante **Choose** (Scegli):

    ![Selezionare Distribuisci l'app](publishing-to-the-app-store-images/publishvs01.png "Selezionare Distribuisci l'app")

9. Selezionare il file con estensione ipa creato in precedenza e fare clic su **OK**.
10. Application Loader convaliderà il file:

    ![Schermata di convalida](publishing-to-the-app-store-images/publishvs02.png "Schermata di convalida")

11. Fare clic sul pulsante **Next** (Avanti). L'applicazione sarà convalidati per l'App Store:

    ![Convalida nell'App Store](publishing-to-the-app-store-images/publishvs03.png "Convalida nell'App Store")

12. Fare clic sul pulsante **Send** (Invia) per inviare l'applicazione ad Apple per la revisione.
13. Application Loader indicherà quando il file è stato caricato.

    > [!NOTE]
    > È possibile che Apple rifiuti le app con **iTunesMetadata.plist** incluso nel file con estensione ipa generando un errore simile al seguente:
    >
    > `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`
    >
    > Per risolvere questo problema, vedere [questo post nei forum di Xamarin](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1).

-----

## <a name="itunes-connect-status"></a>Stato di iTunes Connect

Per visualizzare lo stato dell'invio dell'app, accedere a iTunes Connect e selezionare l'app. Lo stato iniziale dovrebbe essere **Waiting For Review** (In attesa di revisione), sebbene possa essere indicato temporaneamente **Upload Received** (Caricamento ricevuto) durante l'elaborazione.

![In attesa di Revisione](publishing-to-the-app-store-images/image21.png "In attesa di Revisione")

## <a name="tips-and-tricks"></a>Suggerimenti

### <a name="customize-the-ipa-location"></a>Personalizzare il percorso del file con estensione ipa

Una proprietà **MSBuild**, `IpaPackageDir`, consente di personalizzare il percorso di output del file con estensione ipa. Se `IpaPackageDir` è impostata su un percorso personalizzato, il file con estensione ipa verrà inserito nel percorso specificato anziché nella sottodirectory predefinita con timestamp. Può essere utile durante la creazione automatica di build che si basano su un percorso di directory specifico per il corretto funzionamento, ad esempio quelli usati per le compilazioni di integrazione continua (CI).

La nuova proprietà può essere usata in modi diversi. Ad esempio, per eseguire l'output del file con estensione ipa nella directory predefinita precedente (come in Xamarin.iOS 9.6 e versioni precedenti), è possibile impostare la proprietà `IpaPackageDir` su `$(OutputPath)` usando uno degli approcci seguenti. I due approcci sono compatibili con tutte le compilazioni di Xamarin.iOS basate sull'API unificata, incluse le compilazioni IDE e quelle da riga di comando che usano **msbuild** o **mdtool**:

- La prima opzione consiste nell'impostare la proprietà `IpaPackageDir` in un elemento `<PropertyGroup>` del file **MSBuild**. È ad esempio possibile aggiungere l'elemento `<PropertyGroup>` seguente alla fine del file con estensione csproj del progetto dell'app iOS, appena prima del tag `</Project>` di chiusura:

    ```xml
    <PropertyGroup>
      <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

- Per un approccio migliore, aggiungere `<IpaPackageDir>` alla fine dell'elemento `<PropertyGroup>` esistente che corrisponde alla configurazione usata per compilare il file con estensione ipa. Questa soluzione è preferibile perché prepara il progetto a essere compatibile in futuro con un'impostazione pianificata nella pagina delle proprietà del progetto Opzioni IPA iOS. Se si usa la configurazione `Release|iPhone` per la compilazione del file con estensione ipa, il gruppo di proprietà completo aggiornato sarà simile al seguente:

    ```xml
    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone'">
       <Optimize>true</Optimize>
       <OutputPath>bin\iPhone\Release</OutputPath>
       <ErrorReport>prompt</ErrorReport>
       <WarningLevel>4</WarningLevel>
       <ConsolePause>false</ConsolePause>
       <CodesignKey>iPhone Developer</CodesignKey>
       <MtouchUseSGen>true</MtouchUseSGen>
       <MtouchUseRefCounting>true</MtouchUseRefCounting>
       <MtouchFloat32>true</MtouchFloat32>
       <CodesignEntitlements>Entitlements.plist</CodesignEntitlements>
       <MtouchLink>SdkOnly</MtouchLink>
       <MtouchArch>ARMv7, ARM64</MtouchArch>
       <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
       <MtouchTlsProvider>Default</MtouchTlsProvider>
       <BuildIpa>true</BuildIpa>
       <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

Una tecnica alternativa per le compilazioni da riga di comando **msbuild** consiste nell'aggiungere un argomento della riga di comando `/p:` per impostare la proprietà `IpaPackageDir`. In questo caso si noti che **msbuild** non espande le espressioni `$()` passate nella riga di comando, quindi non è possibile usare la sintassi `$(OutputPath)`. È invece necessario specificare un nome percorso completo.

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

O al seguente in Mac:

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

Dopo aver creato e archiviato la compilazione di distribuzione, è possibile inviare l'applicazione a iTunes Connect.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come configurare, compilare e inviare un'app iOS per il rilascio nell'App Store.

## <a name="related-links"></a>Collegamenti correlati

- [Portale Apple Developer (Apple)](https://developer.apple.com/account/)
- [iTunes Connect (Apple)](https://itunesconnect.apple.com)
- [App Store Review Guidelines (Apple)](https://developer.apple.com/appstore/resources/approval/guidelines.html) (Linee guida di revisione dell'App Store)
- [Common App Rejections (Apple)](https://developer.apple.com/app-store/review/rejections/) (Rifiuti comuni di app)
- [Uso delle funzionalità in Xamarin.iOS](~/ios/deploy-test/provisioning/capabilities/index.md)
- [Uso degli entitlement in Xamarin.iOS](~/ios/deploy-test/provisioning/entitlements.md)
- [Configurazione di un'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Icone dell'applicazione in Xamarin.iOS](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Schermate di avvio per le app Xamarin.iOS](~/ios/app-fundamentals/images-icons/launch-screens.md)
