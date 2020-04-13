---
title: Utilizzo di Team City con Xamarin
description: In questa guida verranno illustrati i passaggi necessari per l'utilizzo di TeamCity per compilare le applicazioni per dispositivi mobili e quindi inviarle all'App Center Test.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: davidortinau
ms.author: daortin
ms.date: 04/01/2020
ms.openlocfilehash: 6ecd453180e8c392ba7d7527778617eb40950a9e
ms.sourcegitcommit: 6f3281a32017cfcebadde8a2d6e10651a277828f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587458"
---
# <a name="using-team-city-with-xamarin"></a>Utilizzo di Team City con Xamarin

_In questa guida verranno illustrati i passaggi necessari per l'utilizzo di TeamCity per compilare le applicazioni per dispositivi mobili e quindi inviarle all'App Center Test._

Come illustrato nella guida [Introduzione all'integrazione continua,](~/tools/ci/intro-to-ci.md) l'integrazione continua (CI) è una pratica utile quando si sviluppano applicazioni mobili di qualità. Ci sono molte opzioni praticabili per il software server di integrazione continua; questa guida si concentrerà su [TeamCity](https://www.jetbrains.com/teamcity/) da JetBrains.

Ci sono diverse permutazioni diverse di un'installazione teamCity. Nell'elenco seguente vengono descritte alcune di queste permutazioni:

- **Servizio Windows:** in questo scenario, TeamCity viene avviato all'avvio di Windows come servizio Windows.Windows Service – In this scenario, TeamCity starts up when Windows boot as a Windows Service. Deve essere associato a un host di compilazione Mac per compilare tutte le applicazioni iOS.It must be paired with a Mac Build Host to compile any iOS applications.

- **Avviare Daemon su OS X** – Concettualmente, questo è simile all'esecuzione come un servizio di Windows descritto nel passaggio precedente. Per impostazione predefinita, le compilazioni verranno eseguite con l'account radice.

- **Account utente su OS X** – È possibile eseguire TeamCity con un account utente che si avvia ogni volta che l'utente accede.

Tra gli scenari precedenti, l'esecuzione di TeamCity con un account utente su OS X è la più semplice e semplice da configurare.

La configurazione di TeamCity prevede diversi passaggi:

- **Installazione di TeamCity:** l'installazione di TeamCity non è trattata in questa guida. In questa guida si presuppone che TeamCity sia installato e in esecuzione con un account utente. Le istruzioni per [l'installazione](https://confluence.jetbrains.com/display/TCD8/Installation) di TeamCity sono disponibili nella documentazione di [TeamCity 8](https://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) di JetBrains.

- **Preparazione del server** di compilazione: questo passaggio prevede l'installazione del software, degli strumenti e dei certificati necessari per creare applicazioni per dispositivi mobili e prepararle per la distribuzione.

- **Creazione di** uno script di compilazione: questo passaggio non è strettamente necessario, ma uno script di compilazione è un utile utile per la creazione di applicazioni incustodite. L'utilizzo di uno script di compilazione consente di risolvere i problemi di compilazione che possono verificarsi e fornisce un modo coerente e ripetibile per creare i file binari per la distribuzione, anche se l'integrazione continua non è praticata.

- **Creazione di un progetto TeamCity:** una volta completati i tre passaggi precedenti, è necessario creare un progetto TeamCity che conterrà tutti i metadati necessari per recuperare il codice sorgente, compilare i progetti e inviare i test ad App Center Test.

## <a name="requirements"></a>Requisiti

L'esperienza con [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/) è necessaria.

È richiesta familiarità con TeamCity 8.1. L'installazione di TeamCity esula dall'ambito di questo documento. Si presuppone che TeamCity sia installato su OS X Mavericks e sia in esecuzione con un account utente normale e non con l'account root.

Il server di compilazione deve essere un computer autonomo, che esegue OS X, dedicato all'integrazione continua. In teoria, il server di compilazione non sarà responsabile di altri ruoli, ad esempio un server di database, un server Web o una workstation di sviluppo.

> [!IMPORTANT]
> Questa guida non copre un'installazione "headless" di Xamarin.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparazione del server di compilazione

Un passaggio cruciale nella configurazione di un server di compilazione consiste nell'installare tutti gli strumenti, il software e i certificati necessari per creare le applicazioni per dispositivi mobili. È importante che il server di compilazione sia in grado di compilare la soluzione per dispositivi mobili ed eseguire qualsiasi test. Per ridurre al minimo i problemi di configurazione, il software e gli strumenti devono essere installati nello stesso account utente che ospita TeamCity. L'elenco seguente descrive in dettaglio ciò che è necessario:

1. **Visual Studio per Mac:** include Xamarin.iOS e Xamarin.Android.Visual Studio for Mac – This includes Xamarin.iOS and Xamarin.Android.
2. **Accedere all'archivio di componenti Xamarin:** questo passaggio è facoltativo e necessario solo se l'applicazione utilizza componenti dell'archivio dei componenti Xamarin. Accesso proattivo all'archivio dei componenti a questo punto impedirà eventuali problemi quando una compilazione TeamCity tenta di compilare l'applicazione.
3. **Xcode** – Xcode è necessario per compilare e firmare le applicazioni iOS.
4. **Xcode Command-Line Tools** – Questo è descritto nel passaggio 1 della sezione installazione della guida [Aggiornamento Ruby con rbenv.](https://github.com/calabash/calabash-ios/wiki)
5. Firma dei profili di **provisioning di identità &** di provisioning: importare i certificati e il profilo di provisioning tramite XCode.Signing Identity to Provisioning Profiles – Import the certificates and provisioning profile via XCode. Per ulteriori informazioni, consulta la guida Apple [sull'esportazione delle identità](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) di firma e dei profili di provisioning.
6. **Apple Keystores Android** – Copiare i keystore Android necessari in una `~/Documents/keystores/MyAndroidApp1`directory a cui l'utente TeamCity ha accesso, ad esempio .
7. **Calabash** – Questo è un passaggio facoltativo se l'applicazione dispone di test scritti utilizzando Calabash. Per ulteriori informazioni, consulta la guida [Installazione di Calabash su OS X Mavericks](https://github.com/calabash/calabash-ios/wiki) e la guida [Aggiornamento di Rubino con rbenv.](https://github.com/calabash/calabash-ios/wiki)

Il diagramma seguente illustra tutti questi componenti:The following diagram illustrates all of these components:

![](teamcity-images/image1.png "This diagram illustrates all of these components")

Una volta installato tutto il software, accedere all'account utente e confermare che tutto il software sia installato e funzionante correttamente. Ciò dovrebbe comportare la compilazione della soluzione e l'invio dell'applicazione a App Center Test. Ciò può essere semplificato eseguendo lo script di compilazione, come descritto nella sezione successiva.

## <a name="create-a-build-script"></a>Creare uno script di compilazioneCreate a Build Script

Anche se è possibile per TeamCity gestire tutti gli aspetti della compilazione e dell'invio di applicazioni per dispositivi mobili ad App Center Test da solo; è consigliabile creare uno script di compilazione. Uno script di compilazione offre i vantaggi seguenti:A build script provides the following advantages:

1. **Documentazione** – Uno script di compilazione serve come una forma di documentazione su come viene costruito il software. In questo modo vengono rimosse alcune delle "magia" associate alla distribuzione dell'applicazione e gli sviluppatori possono concentrarsi sulle funzionalità.
1. **Ripetibilità:** uno script di compilazione garantisce che ogni volta che l'applicazione viene compilata e distribuita, avvenga nello stesso modo, indipendentemente da chi o cosa svolge il lavoro. Questa coerenza ripetibile rimuove eventuali problemi o errori che potrebbero verificarsi a causa di una compilazione eseguita in modo non corretto o di un errore umano.
1. **Controllo delle versioni:** uno script di compilazione può essere incluso nel sistema di controllo del codice sorgente. Ciò significa che le modifiche apportate allo script di compilazione possono essere rilevate, monitorate e corrette se vengono rilevati errori o imprecisioni.
1. **Preparare l'ambiente:** uno script di compilazione può includere la logica per installare le dipendenze di terze parti necessarie. Ciò garantirà che le applicazioni siano compilate con i componenti appropriati.

Lo script di compilazione può essere semplice come un file di PowerShell (in Windows) o uno script bash (in OS X). Quando si crea lo script di compilazione, sono disponibili diverse opzioni per i linguaggi di scripting:When creating the build script, there are several choices for scripting languages:

- [**Rastrello**](https://github.com/jimweirich/rake) – questo è un linguaggio specifico di dominio (DSL) per la costruzione di progetti, basato su Ruby. Rake ha il vantaggio della popolarità e un ricco ecosistema di biblioteche.

- [**psake**](https://github.com/psake/psake) – questa è una libreria di Windows PowerShell per la creazione di software

- [**FAKE**](https://fsharp.github.io/FAKE/) – si tratta di un linguaggio DSL basato in F , che consente di utilizzare le librerie .NET esistenti, se necessario.

Il linguaggio di scripting utilizzato dipende dalle preferenze e dai requisiti.

> [!NOTE]
> È possibile utilizzare un sistema di compilazione basato su XML, ad esempio MSBuild o NAnt, ma questi non dispongono dell'espressività e della manutenibilità di un linguaggio DSL dedicato alla creazione di software.

### <a name="parameterizing-the-build-script"></a>Parametrizzazione dello script di compilazioneParameterizing The Build Script

Il processo di creazione e test del software richiede informazioni che devono essere tenute segrete. La creazione di un APK può richiedere una password per l'archivio chiavi e/o l'alias della chiave nel keystore. Analogamente, App Center Test richiede una [chiave API](/appcenter/api-docs/) univoca per uno sviluppatore. Questi tipi di valori non devono essere hardcoded nello script di compilazione. Devono invece essere passati come variabili allo script di compilazione.

Meno sensibili sono valori come l'ID del dispositivo iOS o l'ID del dispositivo Android che identificano i dispositivi che App Center deve usare per le esecuzioni dei test. Questi non sono valori che devono essere protetti, ma possono cambiare da compilazione a compilazione.

L'archiviazione di questi tipi di variabili all'esterno dello script di compilazione semplifica inoltre la condivisione dello script di compilazione all'interno di un'organizzazione, ad esempio con gli sviluppatori. Gli sviluppatori possono utilizzare esattamente lo stesso script del server di compilazione, ma possono utilizzare i propri archivi chiavi e [chiavi API.](/appcenter/api-docs/)

Sono disponibili due opzioni per l'archiviazione di questi valori sensibili:There are two possible options for storing these sensitive values:

- **Un file di configurazione:** per proteggere la [chiave API](/appcenter/api-docs/) questo valore non deve essere controllato nel controllo della versione. Il file può essere creato per ogni macchina. La modalità di lettura dei valori da questo file dipende dal linguaggio di script utilizzato.

- **Variabili di ambiente:** queste possono essere impostate facilmente in base al computer e sono indipendenti dal linguaggio di script sottostante.

Ci sono vantaggi e svantaggi per ognuna di queste scelte. TeamCity funziona bene con le variabili di ambiente, pertanto questa guida consiglia questa tecnica durante la creazione di script di compilazione.

### <a name="build-steps"></a>Passaggi di compilazioneBuild Steps

Lo script di compilazione deve eseguire i passaggi seguenti:The build script must do the following steps:

- **Compilare l'applicazione:** include la firma dell'applicazione con il profilo di provisioning corretto.Compile the Application – This includes signing the application with the correct provisioning profile.

- **Invia l'applicazione a Xamarin Test Cloud:** include la firma e l'allineamento dell'APK con l'archivio chiavi appropriato.

Questi due passaggi verranno illustrati in modo più dettagliato di seguito.

#### <a name="compiling-a-xamarinios-application"></a>Compilazione di un'applicazione Xamarin.iOSCompiling a Xamarin.iOS Application

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>Compilazione di un'applicazione Xamarin.AndroidCompiling a Xamarin.Android Application

Per compilare un'applicazione Android, usare xbuild (o msbuild in Windows):To compile an Android application, use **xbuild** (or **msbuild** on Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```
La compilazione dell'applicazione Android **xbuild** usa il progetto, mentre l'applicazione iOS **xbuild** usa la soluzione.

#### <a name="submitting-xamarinuitests-to-app-center"></a>Invio di Xamarin.UITests a App Center

I UITests vengono inviati utilizzando [l'interfaccia della riga di comando](https://github.com/microsoft/appcenter-cli)di App Center , come illustrato nel frammento di codice seguente:

```bash
appcenter test run uitest --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --merge-nunit-xml report.xml --build-dir pathToUITestBuildDir
```

Quando si esegue il test, i risultati del test verranno restituiti sotto forma di un file XML in stile NUnit denominato **report.xml**. TeamCity visualizzerà le informazioni nel registro di compilazione.

Per ulteriori informazioni su come inviare UITests ad App Center, vedere [Preparazione di applicazioni Xamarin.Android](/appcenter/test-cloud/uitest/preparing-for-upload-android) o [Preparazione di applicazioni Xamarin.iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios).

#### <a name="submitting-calabash-tests-to-app-center"></a>Invio di test Calabash a App Center

I test Calabash vengono inviati utilizzando l'interfaccia della riga di [comando di App Center](https://github.com/microsoft/appcenter-cli), come illustrato nel frammento di codice seguente:

```bash
appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --project-dir pathToProjectDir
```

Per inviare un'applicazione Android a App Center Test, è necessario prima ricostruire il server di test APK utilizzando calabash-android:

```bash
$ calabash-android build </path/to/signed/APK>
$ appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK> --project-dir pathToProjectDir
```

Per ulteriori informazioni sull'invio dei test Calabash, consultare la guida di Xamarin sull'invio dei [test di Calabash al test cloud](https://github.com/calabash/calabash-ios/wiki).

## <a name="creating-a-teamcity-project"></a>Creazione di un progetto TeamCity

Una volta installato TeamCity e Visual Studio per Mac può compilare il progetto, è il momento di creare un progetto in TeamCity per compilare il progetto e inviarlo ad App Center.

1. Iniziato accedendo a TeamCity tramite il browser web. Passare al progetto radice:

    ![Passare al progetto radice](teamcity-images/image2.png "Passare al progetto radice") Sotto il progetto radice, creare un nuovo sottoprogetto:

    ![Passare al progetto radice sotto il progetto radice, creare un nuovo sottoprogetto](teamcity-images/image3.png "Passare al progetto radice sotto il progetto radice, creare un nuovo sottoprogetto")
2. Dopo aver creato il sottoprogetto, aggiungere una nuova configurazione di compilazione:Once the subproject is created, add a new Build Configuration:

    ![Dopo aver creato il sottoprogetto, aggiungere una nuova configurazione di compilazioneOnce the subproject is created, add a new Build Configuration](teamcity-images/image5.png "Una volta creato il sottoprogetto, aggiungere una nuova configurazione di compilazione")
3. Collegare un progetto VCS alla configurazione di compilazione. Questa operazione viene eseguita tramite la schermata Impostazioni controllo versione:This is done via the Version Control Setting screen:

    ![Questa operazione viene eseguita tramite la schermata Impostazioni controllo versione](teamcity-images/image6.png "Questa operazione viene eseguita tramite la schermata Impostazioni controllo versione")

    Se non viene creato alcun progetto VCS, è possibile crearne uno dalla pagina Nuova radice VCS illustrata di seguito:

    ![Se non viene creato alcun progetto VCS, è possibile crearne uno dalla pagina Nuova radice VCS](teamcity-images/image7.png "Se non è stato creato alcun progetto VCS, è possibile crearne uno dalla pagina Nuova radice VCS")

    Una volta collegata la radice VCS, TeamCity estrarrà il progetto e tenterà di rilevare automaticamente le istruzioni di compilazione. Se si ha familiarità con TeamCity, è possibile selezionare una delle istruzioni di compilazione rilevate. È sicuro ignorare le istruzioni di compilazione rilevate per il momento.

4. Configurare quindi un trigger di compilazione. In questo modo verrà accodata una compilazione quando vengono soddisfatte determinate condizioni, ad esempio quando un utente esegue il commit del codice nel repository. La schermata seguente mostra come aggiungere un trigger di compilazione:The following screenshot shows how to add a build trigger:

    ![Questa schermata mostra come aggiungere un trigger di compilazioneThis screenshot shows how to add a build trigger](teamcity-images/image8.png "Questa schermata mostra come aggiungere un trigger di compilazioneThis screenshot shows how to add a build trigger") Un esempio di configurazione di un trigger di compilazione può essere visualizzato nella schermata seguente:An example of configuring a build trigger can be seen in the following screenshot:

    ![Un esempio di configurazione di un trigger di compilazione può essere visualizzato in questa schermata](teamcity-images/image9.png "Un esempio di configurazione di un trigger di compilazione può essere visualizzato in questa schermata")

5. Nella sezione precedente, Parametrizzazione dello script di compilazione, è stata suggerita l'archiviazione di alcuni valori come variabili di ambiente. Queste variabili possono essere aggiunte alla configurazione di compilazione tramite la schermata Parametri.These variables can be added to the build configuration via the Parameters screen. Aggiungere le variabili per la [chiave API](/appcenter/api-docs/)App Center , l'ID del dispositivo iOS e l'ID dispositivo Android, come illustrato nella schermata seguente:

    ![Aggiungere le variabili per la chiave API di test di App Center, l'ID del dispositivo iOS e l'ID dispositivo AndroidAdd the variables for the App Center Test API Key, the iOS device ID, and the Android Device ID](teamcity-images/image11.png "Aggiungere le variabili per la chiave API Test Cloud, l'ID del dispositivo iOS e l'ID dispositivo AndroidAdd the variables for the Test Cloud API Key, the iOS device ID, and the Android Device ID")

6. Il passaggio finale consiste nell'aggiungere un'istruzione di compilazione che richiamerà lo script di compilazione per compilare l'applicazione e passare l'applicazione a App Center Test. La schermata seguente è un esempio di un'istruzione di compilazione che usa un Rakefile per compilare un'applicazione:The following screenshot is an example of a build step that uses a Rakefile to build an application:

    ![Questa schermata è un esempio di un'istruzione di compilazione che usa un Rakefile per compilare un'applicazioneThis screenshot is an example of a build step that uses a Rakefile to build an application](teamcity-images/image12.png "Questa schermata è un esempio di un'istruzione di compilazione che usa un Rakefile per compilare un'applicazioneThis screenshot is an example of a build step that uses a Rakefile to build an application")

7. A questo punto, la configurazione di compilazione è completa. È consigliabile attivare una compilazione per verificare che il progetto sia configurato correttamente. Un buon modo per eseguire questa operazione consiste nell'eseguire il commit di una modifica piccola e insignificante nel repository. TeamCity deve rilevare il commit e avviare una compilazione.

8. Al termine della compilazione, esaminare il log di compilazione e verificare se sono presenti problemi o avvisi con la compilazione che richiedono attenzione.

## <a name="summary"></a>Riepilogo

In questa guida è stato illustrato come utilizzare TeamCity per creare applicazioni Xamarin Mobile e quindi inviarle a App Center Test. È stata illustrata la creazione di uno script di compilazione per automatizzare il processo di compilazione. Lo script di compilazione si occupa della compilazione dell'applicazione, dell'invio a App Center Test e dell'attesa dei risultati.

È quindi stato illustrato come creare un progetto in TeamCity che verrà accodata una compilazione ogni volta che uno sviluppatore esegue il commit del codice e chiamerà lo script di compilazione.

## <a name="related-links"></a>Collegamenti correlati

- [Preparazione Xamarin.Android Apps](/appcenter/test-cloud/uitest/preparing-for-upload-android)
- [Preparazione di applicazioni Xamarin.iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios)
- [Installazione e configurazione di TeamCity](https://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
