---
title: Pubblicazione nell'App Store
description: Questo articolo illustra come configurare, compilare e pubblicare un'applicazione Xamarin.iOS per la distribuzione tramite l'App Store. Contiene una guida dettagliata che illustra come preparare l'applicazione per la distribuzione, come usare gli strumenti di Apple per inviare l'applicazione per la revisione e, infine, come pubblicare l'applicazione nell'App Store.
ms.topic: article
ms.prod: xamarin
ms.assetid: DFBCC0BA-D233-4DC4-8545-AFBD3768C3B9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/23/2017
ms.openlocfilehash: 84db17ede0019e1134b65edaca85ef2401fb3bc0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="publishing-to-the-app-store"></a>Pubblicazione nell'App Store

_Questo articolo illustra come configurare, compilare e pubblicare un'applicazione Xamarin.iOS per la distribuzione tramite l'App Store. Contiene una guida dettagliata che illustra come preparare l'applicazione per la distribuzione, come usare gli strumenti di Apple per inviare l'applicazione per la revisione e, infine, come pubblicare l'applicazione nell'App Store._

Per distribuire le applicazioni in tutti i dispositivi iOS, Apple richiede che le app siano pubblicate tramite l'*App Store*, che diventa così l'unico punto d'acquisto di applicazioni iOS. Con oltre 500.000 applicazioni in archivio, gli sviluppatori di molti tipi di applicazioni hanno sfruttato al meglio l'enorme successo di questo unico punto di distribuzione. L'App Store è una soluzione chiavi in mano, in cui gli sviluppatori di app trovano sia sistemi di distribuzione sia di pagamento.

Il processo di invio di un'applicazione nell'App Store implica le operazioni seguenti:

1. Creazione di un **ID App** e selezione degli **entitlement**.
2. Creazione di un **profilo di provisioning di distribuzione**.
3. Uso del profilo per la compilazione dell'applicazione.
4. Invio dell'applicazione tramite **iTunes Connect**.


Questo articolo descrive tutti i passaggi necessari per eseguire il provisioning, compilare e inviare un'applicazione da distribuire nell'App Store.

## <a name="before-you-submit-an-application"></a>Prima di inviare un'applicazione

Dopo aver inviato un'app da pubblicare nell'App Store, Apple sottopone l'app a un processo di revisione per verificare che sia conforme alle linee guida di Apple in termine di qualità e contenuto. Se l'applicazione non soddisfa queste linee guida, Apple la rifiuterà. A questo punto, sarà necessario risolvere i problemi di non conformità segnalati da Apple e inviarla di nuovo.
Per avere buone probabilità di superare il processo di revisione di Apple, è consigliabile familiarizzare con queste linee guida e tentare di adattare l'applicazione a tali regole. Le linee guida di Apple sono disponibili nella pagina [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) (Linee guida alla revisione nell'App Store).

Di seguito sono specificati un paio di aspetti da verificare prima di inviare un'app:

1. Verificare che la descrizione dell'applicazione corrisponda alla funzionalità inclusa nell'app.
2. Assicurarsi che l'applicazione non si arresti in modo anomalo in condizioni di normale utilizzo, vale a dire l'uso in tutti i dispositivi iOS supportati.


Apple offre anche un elenco di suggerimenti per l'invio nell'App Store, disponibili nella pagina relativa alla [distribuzione nell'pp Store](https://developer.apple.com/appstore/resources/submission/tips.html).

## <a name="configuring-your-application-in-itunes-connect"></a>Configurazione dell'applicazione in iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) comprende una serie di strumenti basati sul Web, con i quali, tra le altre cose, è possibile gestire le applicazioni iOS nell'App Store. È necessario che l'applicazione Xamarin.iOS sia correttamente impostata e configurata in iTunes Connect prima di poter inviare l'app ad Apple per la revisione e, infine, essere rilasciata come app gratuita o in vendita nell'App Store.

Procedere come descritto di seguito:

1. Verificare che esistano i contratti appropriati e che siano aggiornati nella sezione **Agreements, Tax, and Banking** (Contratti, imposte e banche) di iTunes Connect per poter rilasciare un'applicazione iOS gratuita o in vendita.
2. Creare un nuovo **iTunes Connect Record** (Record iTunes Connect) per l'applicazione e specificare **Display Name** (Nome visualizzato), vale a dire come il nome dell'app visualizzato nell'App Store.
3. Selezionare **Sale Price** (Prezzo di vendita) o specificare che l'applicazione viene rilasciata gratuitamente.
5. Specificare una descrizione chiara e concisa in **Description** (Descrizione) che comprende le funzionalità e i vantaggi offerti all'utente finale.
6. In **Categories** (Categorie), **Sub Categories** (Sottocategorie) e **Keywords** (Parole chiave) specificare valori che facilitino all'utente la ricerca dell'app nell'App Store.
7. Specificare gli URL di **Contact** (Contatto) e **Support** (Supporto) al sito Web richiesto da Apple.
8. Impostare un valore per **Rating** (Classificazione) necessario per il controllo genitori nell'App Store.
9. Configurare le tecnologie facoltative dell'App Store, ad esempio **Game Center** e **Acquisto in-app**.

Per altri dettagli, vedere la documentazione [Configurazione di un'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).

## <a name="preparing-for-app-store-distribution"></a>Preparazione per la distribuzione nell'App Store

Per pubblicare un'applicazione nell'App Store, è prima necessario compilarla per la distribuzione, un processo che comprende molti passaggi. Nelle sezioni seguenti vengono illustrate tutte le operazioni necessarie per preparare un'applicazione Xamarin.iOS per la pubblicazione in modo che possa essere compilata, inviata nell'App Store, revisionata e rilasciata.

### <a name="provisioning-for-application-services"></a>Provisioning dei servizi per le applicazioni

Apple offre una selezione di servizi speciali per le applicazioni, denominati anche entitlement, che possono essere attivati per l'applicazione Xamarin.iOS durante la creazione di un ID univoco. Indipendentemente che sia usi un entitlement personalizzato o meno, è necessario creare un ID univoco per l'applicazione Xamarin.iOS prima che sia pubblicata nell'App Store.

Per creare un ID app e facoltativamente selezionare gli entitlement è necessario seguire questa procedura che prevede l'uso del portale di provisioning iOS basato sul Web di Apple:

1. Nella sezione **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili) selezionare **Identifiers** (Identificatori)  > **App ID** (ID app).
2. Fare clic sul pulsante **+** e specificare **Nome** (Nome) e **Bundle ID** (ID bundle) per la nuova applicazione.
3. Scorrere fino alla fine della schermata e selezionare un valore per **App Services** (Servizi app) che sarà richiesto dall'applicazione Xamarin.iOS.
4. Fare clic sul pulsante **Continue** (Continua) e seguire le istruzioni visualizzate per creare un nuovo ID app.

Durante la definizione dell'ID app, oltre a selezionare e configurare i servizi per le applicazioni richiesti, è anche necessario configurare l'ID app e gli entitlement nel progetto Xamarin.iOS modificando i file `Info.plist` e `Entitlements.plist`.

Procedere come descritto di seguito:

1. In **Esplora soluzioni** fare doppio clic sul file `Info.plist` per aprirlo e modificarlo.
2. Nella sezione **Destinazione applicazione iOS** immettere un nome per l'applicazione e l'**identificatore del bundle** creato in fase di definizione dell'ID app.
3. Salvare le modifiche apportate al file `Info.plist`.
4. In **Esplora soluzioni** fare doppio clic sul file `Entitlements.plist` per aprirlo e modificarlo.
5. Selezionare e configurare gli entitlement richiesti per l'applicazione Xamarin.iOS in modo che corrispondano ai valori impostati precedentemente durante la definizione dell'ID app.
6. Salvare le modifiche apportate al file `Entitlements.plist`.

Per istruzioni dettagliate, vedere la documentazione [Provisioning dei servizi applicativi](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices).

### <a name="setting-the-store-icons"></a>Impostazione delle icone per lo Store

Le icone per l'App Store devono raggruppate in un catalogo asset. Per aggiungere un'icona per l'App Store, individuare il set di immagini **AppIcon** nel file **Assets.xcassets** del progetto.

In Catalogo asset l'icona richiesta è denominata **App Store** e le dimensioni devono essere **1024 x 1024**. Secondo le istruzioni di Apple l'icona per l'App Store nel catalogo asset non deve essere trasparente e non deve contenere un canale alfa.

Per informazioni sull'impostazione dell'icona per lo Store, fare riferimento alla guida [App Store Icon](~/ios/app-fundamentals/images-icons/app-store-icon.md) (Icona per l'App Store).

### <a name="setting-the-apps-icons-and-launch-screens"></a>Impostazione delle icone dell'app e delle schermate di avvio

Perché Apple accetti un'applicazione iOS da includere nell'App Store, è necessario che le icone e le schermate di avvio siano corrette per tutti i dispositivi iOS in cui l'applicazione sarà eseguita. Le icone dell'app vengono aggiunte ai progetti in un catalogo asset, tramite un set di immagini **AppIcon** che si trova nel file **Assets.xcassets**. Per aggiungere le schermate di avvio viene usato uno storyboard.

Per istruzioni dettagliate sulla creazione di icone dell'app e schermate di avvio, vedere le guide [Application Icons](~/ios/app-fundamentals/images-icons/app-icons.md) (Icone dell'applicazione) e [Launch Screens](~/ios/app-fundamentals/images-icons/launch-screens.md) (Schermate d'avvio).

### <a name="creating-and-installing-a-distribution-profile"></a>Creazione e installazione di un profilo di distribuzione

iOS usa i *profili di provisioning* per gestire la distribuzione di una build dell'applicazione specifica. Si tratta di file che contengono informazioni sul certificato usato per firmare un'app, sull'*ID applicazione* e sul punto in cui poter installare l'app. Per lo sviluppo e la distribuzione ad hoc, il profilo di provisioning include anche l'elenco dei dispositivi consentiti in cui è possibile distribuire l'app. Per la distribuzione tramite l'App Store, sono invece incluse solo le informazioni che riguardano il certificato e l'ID app, in quanto l'App Store è l'unico meccanismo consentito per la distribuzione pubblica.

Il provisioning richiede questa procedura che prevede l'uso del portale di provisioning iOS basato sul Web di Apple:

1.  Selezionare **Provisioning** (Provisioning)  > **Distribution** (Distribuzione).
2.  Fare clic sul pulsante **+** e selezionare il tipo di profilo di distribuzione che si vuole creare come **App Store**.
3.  Selezionare un **ID App** nell'elenco a discesa per il quale si vuole creare un profilo di distribuzione.
4.  Selezionare un certificato di produzione (distribuzione) valido per firmare l'applicazione.
5.  Immettere un **Name** (Nome) per il nuovo **Distribution Profile** (Profilo di distribuzione) e generare il profilo.
6.  Nel computer Mac aprire Xcode e fare clic su **Preferences > [selezionare l'ID Apple]> View Details...** (Preferenze > Visualizza dettagli...). Scaricare tutti i profili disponibili in Xcode nel Mac.
7.  Tornare all'IDE. Nelle opzioni **Firma del bundle iOS** selezionare il profilo di provisioning di tipo Distribuzione per la _Configurazione build_ corretta, vale a dire **App Store** oppure **Rilascio**.

Per istruzioni dettagliate, vedere [Creating a Distribution Profile](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) (Creazione di un profilo di distribuzione) e [Selecting a Distribution Profile in a Xamarin.iOS Project](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile) (Selezione di un profilo di distribuzione in un progetto Xamarin.iOS).

## <a name="setting-the-build-configuration-for-your-application"></a>Impostazione della configurazione della build per l'applicazione

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Seguire questa procedura:

1. Nel **riquadro della soluzione** fare clic con il pulsante destro del mouse su **Nome progetto** e selezionare **Opzioni** per aprirle e modificarle.
2. Selezionare **Compilazione iOS**, quindi selezionare **Versione | iPhone** dall'elenco a discesa **Configurazione**:

    ![](publishing-to-the-app-store-images/configurevs01.png "Selezionare App Store dal menu a discesa Configurazione")

3. Se la destinazione è una versione iOS specifica, selezionarla dall'elenco del menu a discesa **Versione SDK**, in caso contrario lasciare il valore predefinito.
4. Grazie al collegamento è possibile ridurre le dimensioni generali del file distribuibile dell'applicazione, in quanto vengono rimossi metodi, proprietà e classi inutilizzati. Nella maggior parte dei casi, è consigliabile lasciare il valore predefinito **Collega solo assembly SDK**. In alcune situazioni, ad esempio quando si usano specifiche librerie di terze parti, può essere necessario impostare questo valore su **Non collegare** per evitare che gli elementi necessari siano rimossi. Per altre informazioni, fare riferimento alla guida [iOS Build Mechanics](~/ios/deploy-test/ios-build-mechanics.md) (Meccanismi di compilazione iOS).
5. Selezionare la casella di controllo **Ottimizza file di immagine PNG per iOS** per poter ulteriormente ridurre le dimensioni del risultato finale dell'applicazione.
6. Il debug _non_ deve essere abilitato in quanto aumenta inutilmente le dimensioni della build.
8. Per iOS 11 è necessario selezionare una delle architetture del dispositivo che supporta **ARM64**. Per altre informazioni sulla compilazione per i dispositivi iOS a 64 bit, vedere la sezione **Enabling 64 Bit Builds of Xamarin.iOS Apps** (Abilitazione di build a 64 bit di app Xamarin.iOS) della documentazione [32/64 bit Platform Considerations](~/cross-platform/macios/32-and-64.md) (Considerazioni sulle piattaforme a 32/64 bit).
9. Facoltativamente è possibile usare il compilatore **LLVM** che consente di compilare codice più veloce e di dimensioni ridotte. Il processo di compilazione risulta tuttavia più lungo.
10. In base alle esigenze dell'applicazione, è anche possibile modificare il tipo di **Garbage Collection** in uso e la configurazione per l'**Internazionalizzazione**.
11. Salvare le modifiche alla configurazione della build.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Quando si crea una nuova applicazione Xamarin.iOS in Visual Studio, per impostazioni predefinita le _configurazioni della build_  vengono automaticamente create sia per la distribuzione **Ad Hoc** sia per la distribuzione tramite **App Store**. Prima di finalizzare la build dell'applicazione che sarà inviata ad Apple, è necessario apportare alcune piccole modifiche alla configurazione di base.

Procedere come descritto di seguito:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Nome progetto** e selezionare **Proprietà** per aprirle e modificarle.
2. Selezionare **Compilazione iOS** e **App Store** (o **Versione** se App Store non è disponibile) dall'elenco a discesa **Configurazione**:

    ![](publishing-to-the-app-store-images/configurevs01.png "Selezionare App Store dal menu a discesa Configurazione")

3. Se la destinazione è una versione iOS specifica, selezionarla dall'elenco del menu a discesa **Versione SDK**, in caso contrario lasciare il valore predefinito.
4. Grazie al collegamento è possibile ridurre le dimensioni generali del file distribuibile dell'applicazione, in quanto vengono rimossi metodi, proprietà e classi inutilizzati. Nella maggior parte dei casi, è consigliabile lasciare il valore predefinito **Collega solo assembly SDK**. In alcune situazioni, ad esempio quando si usano specifiche librerie di terze parti, può essere necessario impostare questo valore su **Non collegare** per evitare che gli elementi necessari siano rimossi. Per altre informazioni, fare riferimento alla guida [iOS Build Mechanics](~/ios/deploy-test/ios-build-mechanics.md) (Meccanismi di compilazione iOS).
5. Selezionare la casella di controllo **Ottimizza file di immagine PNG per iOS** per poter ulteriormente ridurre le dimensioni del risultato finale dell'applicazione.
6. Il debug _non_ deve essere abilitato in quanto aumenta inutilmente le dimensioni della build.
7. Fare clic sulla scheda **Avanzate**:

    ![](publishing-to-the-app-store-images/configurevs02.png "Scheda Avanzate")

8. Se la destinazione dell'applicazione Xamarin.iOS sono i dispositivi iOS a 8 e 64 bit, selezionare una delle architetture del dispositivo che supporta **ARM64**. Per altre informazioni sulla compilazione per i dispositivi iOS a 64 bit, vedere la sezione **Enabling 64 Bit Builds of Xamarin.iOS Apps** (Abilitazione di build a 64 bit di app Xamarin.iOS) della documentazione [32/64 bit Platform Considerations](~/cross-platform/macios/32-and-64.md) (Considerazioni sulle piattaforme a 32/64 bit).
9. Facoltativamente è possibile usare il compilatore **LLVM** che consente di compilare codice più veloce e di dimensioni ridotte. Il processo di compilazione risulta tuttavia più lungo.
10. In base alle esigenze dell'applicazione, è anche possibile modificare il tipo di **Garbage Collection** in uso e la configurazione per l'**Internazionalizzazione**.
11. Salvare le modifiche alla configurazione della build.

-----

## <a name="building-and-submitting-the-distributable"></a>Compilazione e invio del file distribuibile

Dopo aver configurato correttamente l'applicazione Xamarin.iOS, è possibile eseguire la compilazione di distribuzione finale che sarà inviata ad Apple per la revisione e il rilascio.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

### <a name="build-your-archive"></a>Compilare l'archivio

1. Selezionare la configurazione **Rilascio | Dispositivo** in Visual Studio per Mac:

    ![](publishing-to-the-app-store-images/buildxs01new.png "Selezionare la configurazione Rilascio | Dispositivo")
1. Scegliere **Archivia per la pubblicazione** dal menu **Compila**:

    ![](publishing-to-the-app-store-images/buildxs02new.png "Selezionare Archivia per la pubblicazione")

1. Dopo aver creato l'archivio, sarà visualizzata la visualizzazione **Archivi**:

    ![](publishing-to-the-app-store-images/buildxs03new.png "Visualizzazione Archivi")


> [!NOTE]
> Nota: mentre le configurazioni precedenti _App Store_ e _Ad hoc_ sono state rimosse da tutti i progetti modello di Visual Studio per Mac, è possibile che in progetti precedenti siano ancora presenti tali configurazioni. In questo caso, è possibile continuare a usare la configurazione **App Store | Dispositivo** specificata prima nel passaggio 1.

### <a name="sign-and-distribute-your-app"></a>Firmare e distribuire l'app

 Ogni volta in cui l'applicazione viene compilata per l'archivio, si apre automaticamente la **visualizzazione Archivi**, in cui vengono visualizzati tutti i progetti archiviati, raggruppati per tipo di soluzione. Per impostazione predefinita, questa visualizzazione contiene soltanto la soluzione attualmente aperta. Per visualizzare tutte le soluzioni con archivi, fare clic sull'opzione **Mostra tutti gli archivi**.

 È consigliabile mantenere gli archivi distribuiti ai clienti (distribuzione tramite App Store o Enterprise), in modo che le informazioni di debug generate possano essere rappresentate in futuro.

 Per firmare l'app e prepararla per la distribuzione:


1. Selezionare **Firma e distribuisci...**, come illustrato di seguito:

    ![](publishing-to-the-app-store-images/buildxs04new.png "Selezionare Firma e distribuisci")

1. Si aprirà la pubblicazione guidata. Selezionare il canale di distribuzione **App Store** per creare un pacchetto e aprire Application Loader:

    ![](publishing-to-the-app-store-images/distribute01.png "Aprire Application Loader")

1. Nella schermata Profilo di provisioning selezionare l'identità di firma e il profilo di provisioning corrispondente o ripetere la firma con un'altra identità:

    ![](publishing-to-the-app-store-images/distribute02.png "Selezionare l'identità di firma e il profilo di provisioning corrispondente")

1. Verificare i dettagli del pacchetto e fare clic su **Pubblica** per salvare il pacchetto `.ipa`:

    ![](publishing-to-the-app-store-images/distribute03.png "Verificare i dettagli del pacchetto")

1. Dopo aver salvato il pacchetto `.ipa`, l'app è pronta per essere caricata in iTunes Connect tramite Application Loader:

    ![](publishing-to-the-app-store-images/distribute04.png "Schermata di pubblicazione completata")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Il plug-in di Xamarin per Visual Studio non supporta attualmente il flusso di lavoro di archiviazione per la pubblicazione di applicazioni iOS nell'App Store. Di conseguenza, è necessario caricare un pacchetto IPA che viene creato tramite il comando **Compila pacchetto ad-hoc IPA**, descritto di seguito.


## <a name="build-an-ipa"></a>Compilare un pacchetto IPA

 Questa sezione descrive come compilare un pacchetto IPA, un processo simile al flusso di lavoro che caratterizza l'uso di una distribuzione ad-hoc o enterprise. Per la firma sarà tuttavia usato il profilo di provisioning dell'App Store creato in precedenza.

 Procedere come descritto di seguito:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto Xamarin.iOS e selezionare **Proprietà** per aprirle e modificarle:

    ![](publishing-to-the-app-store-images/imagevs01.png "Selezionare le proprietà")
1. Selezionare **Firma del bundle iOS** e modificare il profilo di provisioning in un profilo di provisioning dell'App Store:

    ![](publishing-to-the-app-store-images/ipa01.png "Selezionare Firma del bundle iOS e modificare il profilo di provisioning in un profilo di provisioning dell'App Store")
1. Selezionare **Opzioni IPA iOS** e selezionare **Ad-Hoc** dall'elenco del menu a discesa **Configurazione**. Se l'opzione Ad-hoc non viene visualizzata, selezionare **Rilascio**:

    ![](publishing-to-the-app-store-images/imagevs02.png "Selezionare Ad-hoc dal menu a discesa Configurazione")

1. È possibile specificare facoltativamente un **Nome pacchetto** per il pacchetto IPA. Se non viene specificato, il pacchetto avrà lo stesso nome del progetto Xamarin.iOS.
1. Salvare le modifiche alle proprietà del progetto.
1. Selezionare **Ad Hoc** dal menu a discesa **Configurazione compilazione** di Visual Studio per Mac:

    ![](publishing-to-the-app-store-images/imagevs05.png "Selezionare Ad-hoc dal menu a discesa Configurazione compilazione")
1. Compilare il progetto per creare il pacchetto IPA.
1. Il pacchetto IPA sarà compilato nella cartella `Bin` > _iOS Device_ > `Ad Hoc` (Dispositivo iOS):

    ![](publishing-to-the-app-store-images/imagevs06.png "Pacchetto IPA visualizzato in Esplora file")

-----


## <a name="customizing-the-ipa-location"></a>Personalizzazione del percorso del pacchetto IPA

È stata aggiunta una nuova proprietà **MSBuild**`IpaPackageDir` per semplificare la personalizzazione del percorso di output del file `.ipa`. Se si imposta `IpaPackageDir` su un percorso personalizzato, il file `.ipa` sarà inserito in tale posizione anziché nella sottodirectory con timestamp predefinita. Può essere utile durante la creazione di compilazioni automatizzate che si basano su un percorso di directory specifico affinché funzionino correttamente, ad esempio nel caso di compilazioni di Integrazione continua (CI).

Esistono diverse possibilità d'uso della nuova proprietà:

Ad esempio, per eseguire l'output del file `.ipa` nella directory predefinita precedente (ad esempio in Xamarin.iOS 9.6 e versioni precedenti), è possibile impostare la proprietà `IpaPackageDir` su `$(OutputPath)` usando uno degli approcci seguenti. I due approcci sono compatibili con tutte le compilazioni di API unificata di Xamarin.iOS, incluse le compilazioni tramite IDE e quelle tramite riga di comando che usano `xbuild`, `msbuild` o `mdtool`:

  - La prima opzione consiste nell'impostare la proprietà `IpaPackageDir` in un elemento `<PropertyGroup>` del file **MSBuild**. Ad esempio, è possibile aggiungere l'elemento `<PropertyGroup>` seguente alla fine del progetto dell'app iOS `.csproj`, appena prima del tag di chiusura `</Project>`:

      ```xml
        <PropertyGroup>
            <IpaPackageDir>$(OutputPath)</IpaPackageDir>
        </PropertyGroup>
      ```
  - Per un approccio migliore, aggiungere l'elemento `<IpaPackageDir>` alla fine dell'oggetto `<PropertyGroup>` esistente che corrisponde alla configurazione usata per compilare il file `.ipa`. Questa soluzione è preferibile perché prepara il progetto a essere compatibile in futuro con un'impostazione pianificata nella pagina delle proprietà del progetto Opzioni IPA iOS. Se si usa la configurazione `Release|iPhone` per la compilazione del file `.ipa`, il gruppo di proprietà completo aggiornato sarà simile al seguente:

      ```xml
      <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone' )
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
        <MtouchArch>;ARMv7, ARM64</MtouchArch>
        <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
        <MtouchTlsProvider>Default</MtouchTlsProvider>
        <PlatformTarget>x86&</PlatformTarget>
        <BuildIpa>true</BuildIpa>
        <IpaPackageDir>$(OutputPath</IpaPackageDir>
      </PropertyGroup>
      ```
Una tecnica alternativa per le compilazioni tramite riga di comando di `msbuild` e `xbuild` consiste nell'aggiungere un argomento della riga di comando `/p:` per impostare la proprietà `IpaPackageDir`. In questo caso si noti che `msbuild` non espande le espressioni `$()` passate nella riga di comando. Non è pertanto possibile usare la sintassi `$(OutputPath)`. È invece necessario specificare un nome percorso completo. Il comando `xbuild` di Mono espande le espressioni `$()`, ma è comunque preferibile usare un nome percorso completo, in quanto nelle future versioni `xbuild` sarà deprecato e sostituito dalla [versione multipiattaforma di `msbuild`](http://www.mono-project.com/docs/about-mono/releases/4.4.0/#msbuild-preview-for-os-x). Un esempio completo che usa questo approccio è simile al seguente in Windows:

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

O al seguente in Mac:

```bash
xbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

Dopo aver creato e archiviato la compilazione di distribuzione, è possibile inviare l'applicazione a iTunes Connect.

### <a name="automatically-copy-app-bundles-back-to-windows"></a>Copiare automaticamente i bundle dell'app nuovamente in Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]

## <a name="submitting-your-app-to-apple"></a>Invio dell'app ad Apple

> [!NOTE]
> Nota: Apple ha di recente modificato il processo di verifica per le applicazioni iOS e potrebbe pertanto rifiutare le app che includono `iTunesMetadata.plist` nel pacchetto IPA. Se si verifica l'errore `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"` la soluzione alternativa descritta [qui](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1) dovrebbe risolvere il problema.

Dopo aver completato la compilazione di distribuzione, è possibile inviare l'applicazione iOS ad Apple per la revisione e il rilascio nell'App Store.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

 Seguire questa procedura:

1. Avviare **Xcode**.
2. Dal menu **Window** (Finestra) selezionare **Organizer** (Libreria).
3. Fare clic sulla scheda **Archives** (Archivi) e selezionare l'archivio che è stato precedentemente compilato:

    ![](publishing-to-the-app-store-images/publishxs01.png "Selezionare l'archivio da inviare")
4. Fare clic sul pulsante **Validate...**  (Convalida...).
5. Selezionare l'account di verifica e fare clic sul pulsante **Choose** (Scegli):

    ![](publishing-to-the-app-store-images/publishxs02.png "Selezionare l'account da verificare")
6. Fare clic sul pulsante **Validate** (Convalida):

    ![](publishing-to-the-app-store-images/publishxs03.png "Finestra di dialogo del file")
7. Se si sono verificati problemi con il bundle, saranno visualizzati.
8. Correggerli e ricompilare l'archivio in Visual Studio per Mac.
9. Fare clic sul pulsante **Submit...**  (Inoltra...).
10. Selezionare l'account di verifica e fare clic sul pulsante **Choose** (Scegli):

    ![](publishing-to-the-app-store-images/publishxs04.png "Selezionare l'account da verificare")
11. Fare clic sul pulsante **Submit** (Invia):

    ![](publishing-to-the-app-store-images/publishxs05.png "Finestra di dialogo del file")
12. Xcode indicherà quando il file è stato caricato n iTunes Connect.


Il flusso di lavoro di archiviazione in Visual Studio per Mac aprirà Application Loader automaticamente, dopo che il file _.ipa_ è stato salvato:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

L'applicazione da rivedere viene inviata ad Apple tramite l'app Application Loader. Questa procedura deve essere eseguita nell'host di compilazione Mac. È possibile scaricare una copia di Application Loader da [qui](https://itunesconnect.apple.com/apploader/ApplicationLoader_3.0.dmg).

-----

1. Selezionare *Deliver Your App* (Invia l'app) e fare clic sul pulsante *Choose* (Scegli):

    [ ![](publishing-to-the-app-store-images/publishvs01.png "Selezionare l'invio dell'app")](publishing-to-the-app-store-images/publishvs01.png)

2. Selezionare il file ZIP o il file IPA creato in precedenza e fare clic sul pulsante **OK**.

3. Application Loader convaliderà il file:

    [ ![](publishing-to-the-app-store-images/publishvs02.png "Schermata di convalida")](publishing-to-the-app-store-images/publishvs02.png)
4. Fare clic sul pulsante *Next* (Avanti). L'applicazione sarà convalidati per l'App Store:

    [ ![](publishing-to-the-app-store-images/publishvs03.png "Convalida per l'App Store")](publishing-to-the-app-store-images/publishvs03.png)
5. Fare clic sul pulsante **Send** (Invia) per inviare l'applicazione ad Apple per la revisione.
6. Application Loader indicherà quando il file è stato caricato.

## <a name="itunes-connect-status"></a>Stato di iTunes Connect

Se si accedere di nuovo a iTunes Connect e si seleziona l'applicazione dall'elenco delle app disponibili, lo stato in iTunes Connect dovrebbe essere **Waiting for Review** (In attesa per la revisione). Potrebbe temporaneamente essere **Upload Received** (Upload ricevuto) durante la fase di elaborazione:

[ ![](publishing-to-the-app-store-images/image21.png "Lo stato in iTunes Connect dovrebbe essere In attesa per la revisione")](publishing-to-the-app-store-images/image21.png)

## <a name="summary"></a>Riepilogo

In questo articolo è stata illustrata una guida dettagliata alla configurazione, alla creazione e all'invio di un'applicazione per la pubblicazione nell'App Store. Prima sono stati trattati i passaggi necessari per creare e installare un profilo di provisioning di distribuzione. Successivamente è stato esaminato nel dettaglio l'uso di Visual Studio e Visual Studio per Mac per creare una compilazione di distribuzione. Infine è stato illustrato l'uso di iTunes Connect e lo strumento per l'invio di un'applicazione all'App Store.


## <a name="related-links"></a>Collegamenti correlati

- [Uso delle immagini](~/ios/app-fundamentals/images-icons/index.md)
- [iOS App Development Workflow Guide: Distributing Applications](http://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/35-Distributing_Applications/distributing_applications.html) (Guida al flusso di lavoro di sviluppo di app iOS: distribuzione delle applicazioni)
- [App Store Submission Tips](https://developer.apple.com/appstore/resources/submission/tips.html) (Suggerimenti per l'invio nell'App Store)
- [Common App Rejections](https://developer.apple.com/app-store/review/rejections/) (Rifiuti comuni di app)
- [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) (Linee guida sulle recensioni nell'App Store)
