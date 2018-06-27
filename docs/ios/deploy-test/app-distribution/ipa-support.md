---
title: Supporto IPA in Xamarin.iOS
description: Questo articolo illustra come creare un file IPA che può essere usato per distribuire un'applicazione con la distribuzione ad hoc, per il testing o per la distribuzione interna di applicazioni interne.
ms.prod: xamarin
ms.assetid: D253C2DB-852E-6FC6-C9FD-574730B8DB19
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 288ac813f23f281a1bbed375cadf5faa9d4ff9d0
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784878"
---
# <a name="ipa-support-in-xamarinios"></a>Supporto IPA in Xamarin.iOS

_Questo articolo illustra come creare un file IPA che è possibile usare per distribuire un'applicazione con la distribuzione ad hoc, a scopo di test o per la distribuzione interna di applicazioni interne._

Un'applicazione, oltre a essere rilasciata per la vendita tramite l'App Store iTunes, può essere distribuita per gli usi seguenti:

- **Testing ad hoc**: un'applicazione iOS può essere distribuita fino a 100 utenti (identificati dagli UUID dei dispositivi iOS specifici) per eseguire test alfa e beta. Vedere la documentazione [Provisioning di un dispositivo iOS per lo sviluppo](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning) per informazioni dettagliate sull'aggiunta di dispositivi iOS di test all'account sviluppatore Apple e la guida [Ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md) per altre informazioni su come eseguire questo tipo di distribuzione.
- **Distribuzione interna/aziendale**: per distribuire un'applicazione iOS internamente, in una società, è necessaria l'appartenenza al programma Developer Enterprise di Apple. Per altre informazioni dettagliate sulla distribuzione interna, vedere la guida [Distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md).

In entrambi i casi, è necessario creare un pacchetto IPA (un tipo speciale di file ZIP) e applicarvi una firma digitale con il profilo di provisioning di distribuzione corretto. Questo articolo illustra la procedura necessaria per creare il pacchetto IPA e installarlo in un dispositivo iOS usando iTunes su un Mac o un PC Windows.

<a name="iTunesMetadata" />

## <a name="the-itunesmetadataplist-file"></a>File iTunesMetadata.plist

Quando un'applicazione iOS viene creata in iTunes Connect (per la vendita o il rilascio gratuito dall'App Store iTunes), lo sviluppatore può specificare informazioni come il genere dell'applicazione, il genere secondario, le informazioni sul copyright, i dispositivi iOS supportati e le funzionalità dei dispositivi necessarie.

Le applicazioni iOS distribuite tramite la distribuzione ad hoc o interna devono supportare queste informazioni in modo che siano visibili in iTunes e nel dispositivo dell'utente. Per impostazione predefinita, viene creato un file iTunesMetadata.plist di piccole dimensioni ogni volta che il progetto viene compilato e archiviato nella directory di progetto.

È anche possibile creare un file **iTunesMetadata.plist** personalizzato per fornire le informazioni aggiuntive a una distribuzione. Per altre informazioni sul contenuto di questo file e su come crearlo, vedere la documentazione [Contenuti di iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md#iTunesMetadata_contents) e [Creazione di un file iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md#iTunesMetadata_creating).

<a name="iTunesArtwork" />

## <a name="itunes-artwork"></a>Illustrazione di iTunes

Quando si distribuisce l'app senza App Store, è anche necessario includere un'immagine in formato 512x512 e 1024x1024 che verrà usata per rappresentare l'applicazione in iTunes.

Per specificare l'illustrazione di iTunes, seguire questa procedura:

1. Fare doppio clic sul file **Info.plist** in **Esplora soluzioni** per aprirlo e modificarlo.
2. Scorrere fino alla sezione **iTunes Artwork** dell'editor.
3. Per un'immagine mancante, fare clic sull'anteprima nell'editor, selezionare il file di immagine per l'illustrazione di iTunes desiderata dalla finestra di dialogo **Apri file** e fare clic sul pulsante **OK** o **Apri**.
4. Ripetere questo passaggio finché non vengono specificate tutte le immagini necessarie per l'applicazione.

Per informazioni più dettagliate, vedere la documentazione [Illustrazione di iTunes](~/ios/app-fundamentals/images-icons/app-icons.md).

<a name="createipa" />

## <a name="creating-an-ipa"></a>Creazione di un file IPA

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

La creazione di un file IPA ora è predefinita nel nuovo flusso di lavoro di pubblicazione. A questo scopo, seguire le istruzioni riportate sotto per archiviare l'app, firmarla e salvare il file IPA.

Prima di iniziare a creare un file IPA per una soluzione multipiattaforma, assicurarsi di avere selezionato il progetto iOS come progetto di avvio:

![](ipa-support-images/setasstartup.png "Selezionare il progetto iOS come progetto di avvio")

### <a name="build-your-archive"></a>Compilare l'archivio

Per compilare un file IPA, è necessario creare un _archivio_ di una compilazione di rilascio dell'applicazione. Questo archivio contiene l'app e ne identifica le informazioni.


1. Selezionare la configurazione **Rilascio | Dispositivo** in Visual Studio per Mac:

    ![](ipa-support-images/buildxs01new.png "Selezionare la configurazione Rilascio | Dispositivo")

1. Scegliere **Archivia per la pubblicazione** dal menu **Compila**:

    ![](ipa-support-images/buildxs02new.png "Selezionare Archivia per la pubblicazione")

1. Dopo aver creato l'archivio, sarà visualizzata la visualizzazione **Archivi**:

    ![](ipa-support-images/buildxs03new.png "Sarà visualizzata la vista Archivi")


### <a name="sign-and-distribute-your-app"></a>Firmare e distribuire l'app

Ogni volta in cui l'applicazione viene compilata per l'archivio, si aprirà automaticamente la **vista Archivi**, in cui saranno visualizzati tutti i progetti archiviati, raggruppati per soluzione. Per impostazione predefinita, in questa vista viene visualizzata soltanto la soluzione attualmente aperta. Per visualizzare tutte le soluzioni con archivi, fare clic sull'opzione **Mostra tutti gli archivi**.

È consigliabile mantenere gli archivi distribuiti ai clienti (distribuzione ad hoc o interna), in modo che le informazioni di debug generate possano essere rappresentate in futuro.

Se noti che per le compilazioni non App Store il file **iTunesMetadata.plist** e il set di illustrazioni di iTunes verranno automaticamente inclusi nel file IPA se vengono trovati nell'archivio.

Per firmare l'app e prepararla per la distribuzione:


1. Selezionare il pulsante **Firma e distribuisci**, come illustrato di seguito:

    ![](ipa-support-images/buildxs04new.png "Selezionare Firma e distribuisci")

1. Si aprirà la pubblicazione guidata. Selezionare il canale di distribuzione **Ad hoc** o **Enterprise**(Internamente) per creare un pacchetto:

    ![](ipa-support-images/distribute01.png "Selezionare la distribuzione ad hoc o Enterprise interna")

1. Nella schermata Profilo di provisioning selezionare l'identità di firma e il profilo di provisioning corrispondente o ripetere la firma con un'altra identità:

    ![](ipa-support-images/distribute02.png "Selezionare l'identità di firma e il profilo di provisioning corrispondente")

1. Verificare i dettagli del pacchetto e fare clic su **Pubblica**:

    ![](ipa-support-images/distribute03.png "Verificare i dettagli del pacchetto")

1. Salvare infine il file IPA nel computer:

    ![](ipa-support-images/distribute04.png "Salvare il file IPA nel computer")


### <a name="building-via-the-command-line-on-mac"></a>Compilazione tramite la riga di comando (su Mac)

In alcuni casi, ad esempio in un ambiente di integrazione continua, può essere necessario compilare il file IPA tramite la riga di comando. A questo scopo, seguire questa procedura:


1. Assicurarsi che **Opzioni progetto > Opzioni IPA iOS > Includi immagini per iTunes Artwork e iTunesMetadata.plist** sia selezionato e che **Compila pacchetto ad-hoc/enterprise (IPA)** sia selezionato:

    ![](ipa-support-images/imagexs04.png "Includi immagini per iTunes Artwork e Compila pacchetto ad-hoc/enterprise (IPA) sono selezionati")

    Se si preferisce, è invece possibile modificare il file con estensione **csproj** in un editor di testo e aggiungere manualmente le due proprietà corrispondenti a `PropertyGroup` per la configurazione che verrà usata per compilare l'app:

    ```xml
    <BuildIpa>true</BuildIpa>
    <IpaIncludeArtwork>false</IpaIncludeArtwork>
    ```

1. Se si include un file **iTunesMetadata.plist** facoltativo, fare clic sul pulsante **...**, selezionarlo dall'elenco e fare clic sul pulsante **OK**:

     ![](ipa-support-images/imagexs03.png "Selezionare iTunesMetadata.plist dall'elenco")

1. Chiamare direttamente **xbuild** (o **mdtool** per l'API classica) e passare questa proprietà nella riga di comando:

    ```bash
    /Library/Frameworks/Mono.framework/Commands/xbuild YourSolution.sln /p:Configuration=Ad-Hoc /p:Platform=iPhone /p:BuildIpa=true
    ```

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Dopo la creazione e la selezione del profilo di provisioning, la creazione del file **iTunesMetadata.plist** facoltativo e l'impostazione di iTunes Artwork in Visual Studio, è possibile compilare un file IPA per la distribuzione. Sarà quindi necessario configurare il progetto. Seguire questa procedura:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto Xamarin.iOS e selezionare **Proprietà** per aprirle e modificarle:

    ![](ipa-support-images/imagevs01.png "Selezionare Proprietà")

2. Selezionare **Opzioni IPA iOS** e selezionare **Ad hoc** dall'elenco a discesa **Configurazione**:

    ![](ipa-support-images/imagevs02.png "Selezionare Ad hoc dall'elenco a discesa Configurazione")

    > [!NOTE]
    > Una configurazione ad hoc potrebbe non essere disponibile per i progetti Xamarin.iOS più recenti. Se non è disponibile, selezionare la configurazione **Rilascio**.

3. Se si include un file **iTunesMetadata.plist** facoltativo, fare clic sul pulsante **...**, selezionarlo dall'elenco e fare clic sul pulsante **Apri**:

    ![](ipa-support-images/imagevs03.png "Selezionare iTunesMetadata.plist dall'elenco")

4. È possibile specificare facoltativamente un **nome pacchetto** per il file IPA. Se non viene specificato, avrà lo stesso nome del progetto Xamarin.iOS.
5. Salvare le modifiche alle proprietà del progetto.
6. Selezionare **Ad hoc** dall'elenco a discesa **Configurazione della build**, se disponibile. In alternativa selezionare **Rilascio**:

    ![](ipa-support-images/imagevs05.png "Selezionare Ad hoc dall'elenco a discesa Configurazione della build")

7. Compilare il progetto per creare il pacchetto IPA.
8. Il pacchetto IPA verrà compilato nella cartella **Bin > iOS Device > Ad Hoc (o Release)**:

    ![](ipa-support-images/imagevs06.png "Pacchetto IPA in Esplora file")

-----

<a name="Customizing-the-IPA-Location" />

## <a name="customizing-the-ipa-location"></a>Personalizzazione del percorso del pacchetto IPA

Per semplificare la personalizzazione del percorso di output del file **IPA**, è stata aggiunta una nuova proprietà `IpaPackageDir` **MSBuild**. Se si imposta `IpaPackageDir` su un percorso personalizzato, il file **IPA** sarà inserito in tale posizione, invece che nella sottodirectory con timestamp predefinita. Può essere utile durante la creazione automatica di build che si basano su un percorso di directory specifico per il corretto funzionamento, ad esempio quelli usati per le compilazioni di integrazione continua (CI).

La nuova proprietà può essere usata in diversi modi:

Ad esempio, per eseguire l'output del file **IPA** nella directory predefinita precedente (come in Xamarin.iOS 9.6 e versioni precedenti), è possibile impostare la proprietà `IpaPackageDir` su `$(OutputPath)` usando uno degli approcci seguenti. I due approcci sono compatibili con tutte le compilazioni di Xamarin.iOS basate sull'API unificata, incluse le compilazioni IDE e quelle da riga di comando che usano **xbuild**, **msbuild** o **mdtool**:

- La prima opzione consiste nell'impostare la proprietà `IpaPackageDir` in un elemento `<PropertyGroup>` di un file **MSBuild**. È ad esempio possibile aggiungere l'elemento `<PropertyGroup>` seguente alla fine del file con estensione **csproj** del progetto dell'app iOS, appena prima del tag `</Project>` di chiusura:

    ```xml
    <PropertyGroup>
        <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

- Per un approccio migliore, aggiungere `<IpaPackageDir>` alla fine dell'elemento `<PropertyGroup>` esistente che corrisponde alla configurazione usata per compilare il file **IPA**. Questa soluzione è preferibile perché prepara il progetto a essere compatibile in futuro con un'impostazione pianificata nella pagina delle proprietà del progetto Opzioni IPA iOS. Se si usa la configurazione `Release|iPhone` per la compilazione del file **IPA**, il gruppo di proprietà completo aggiornato sarà simile al seguente:

    ```xml
    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone' ">
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

Una tecnica alternativa per le compilazioni da riga di comando **msbuild** o **xbuild** consiste nell'aggiungere un argomento della riga di comando `/p:` per impostare la proprietà `IpaPackageDir`. In questo caso si noti che **msbuild** non espande le espressioni `$()` passate nella riga di comando, quindi non è possibile usare la sintassi `$(OutputPath)`. È invece necessario specificare un nome percorso completo. Il comando **xbuild** di Mono espande le espressioni `$()`, ma è comunque preferibile usare un nome percorso completo, perché **xbuild** nelle future versioni sarà deprecato e sostituito dalla [versione multipiattaforma di **msbuild**](http://www.mono-project.com/docs/about-mono/releases/4.4.0/#msbuild-preview-for-os-x).

Un esempio completo che usa questo approccio è simile al seguente in Windows:


```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

O al seguente in Mac:

```bash
xbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

<a name="installipa" />

## <a name="installing-an-ipa-using-itunes"></a>Installazione di un file IPA con iTunes

Il pacchetto IPA risultante può essere distribuito agli utenti di test per l'installazione nei dispositivi iOS o inviato per la distribuzione Enterprise. Indipendentemente dal metodo scelto, l'utente finale installerà il pacchetto dell'applicazione iTunes nel Mac o nel PC Windows facendo doppio clic sul file IPA (o trascinandolo sulla finestra di iTunes aperta).

La nuova applicazione iOS verrà visualizzata nella sezione **My Apps** (App personali), su cui è possibile fare clic con il pulsante destro del mouse per ottenere informazioni sull'applicazione:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

 ![](ipa-support-images/installxs01.png "Nuova applicazione iOS nella sezione My Apps (App personali)")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 ![](ipa-support-images/installvs01.png "Nuova applicazione iOS nella sezione My Apps (App personali)")

-----

L'utente ora può sincronizzare iTunes con il dispositivo per installare la nuova applicazione iOS.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la configurazione necessaria per preparare un'applicazione Xamarin.iOS per una compilazione senza App Store. Ha spiegato come creare un pacchetto IPA e come installare l'applicazione iOS risultante nel dispositivo iOS dell'utente finale per il test o la distribuzione interna.


## <a name="related-links"></a>Collegamenti correlati

- [Distribuzione tramite l'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Configurazione di un'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Pubblicazione nell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribuzione ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [File iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
- [Illustrazione di iTunes](~/ios/app-fundamentals/images-icons/app-icons.md#itunes)
- [Distribuzione di app aziendali per dispositivi iOS](http://developer.apple.com/library/ios/#featuredarticles/FA_Wireless_Enterprise_App_Distribution/Introduction/Introduction.html)
