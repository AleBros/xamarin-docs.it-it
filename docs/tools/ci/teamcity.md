---
title: Utilizzando Team City con Xamarin
description: Questa Guida verrà descritte le procedure relative all'utilizzo TeamCity per compilare applicazioni per dispositivi mobili e quindi inviarli a Xamarin Test Cloud.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/23/2017
ms.openlocfilehash: 643ae8f30dc6447b548448f77883b204d8dc76c2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="using-team-city-with-xamarin"></a>Utilizzando Team City con Xamarin

_Questa Guida verrà descritte le procedure relative all'utilizzo TeamCity per compilare applicazioni per dispositivi mobili e quindi inviarli a Xamarin Test Cloud._

Come descritto nel [Introduzione all'integrazione continua](~/tools/ci/intro-to-ci.md) Guida, integrazione continua (CI) è utile quando si sviluppano applicazioni per dispositivi mobili qualità. Sono disponibili molte opzioni valide per il software server di integrazione continua; Questa guida è incentrata sul [TeamCity](http://www.jetbrains.com/teamcity/) da JetBrains.

Esistono diverse permutazioni diverse di un'installazione TeamCity. Di seguito è riportato un elenco di alcune di queste:

- **Servizio Windows** : In questo scenario, verrà avviata TeamCity quando Windows viene avviato come servizio Windows. Deve essere abbinato a un Host di compilazione Mac per compilare le applicazioni iOS.

- **Avvio del Daemon su OS X** : a livello concettuale, è molto simile all'esecuzione come servizio Windows descritto nel passaggio precedente. Per impostazione predefinita, le compilazioni verranno eseguite nell'account radice.

- **Account utente su OS X** : è possibile eseguire TeamCity con un account utente che viene avviato ogni volta che l'utente accede.

Degli scenari precedenti, in esecuzione TeamCity con un account utente su OS X è il modo più semplice per l'installazione.

Sono disponibili diversi passaggi per configurare TeamCity:

- **L'installazione TeamCity** : l'installazione di TeamCity non viene descritta in questa Guida. Questa guida presuppone che TeamCity sia installato e in esecuzione con un account utente. Istruzioni su [installazione TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation) è reperibile nel [TeamCity 8 documentazione](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) da JetBrains.

- **Preparazione del Server di compilazione** : questo passaggio prevede l'installazione del software necessario, strumenti e i certificati necessari per compilare applicazioni per dispositivi mobili e prepararli per la distribuzione.

- **Creazione di Script di compilazione A** : questo passaggio non è strettamente necessario, ma uno script di compilazione è utile per la creazione di applicazioni automatiche. Tramite uno script di compilazione consentirà di risoluzione dei problemi di compilazione che potrebbero verificarsi e fornisce un modo coerenza e ripetibile per creare i file binari per la distribuzione, anche se l'integrazione continua non è stato adottato.

- **Creazione di un progetto di TeamCity** : dopo aver completati i tre passaggi precedenti, è necessario creare un progetto TeamCity che conterrà tutti i metadati necessari per recuperare il codice sorgente, compilare i progetti e inviare i test da Xamarin Test Cloud.

# <a name="requirements"></a>Requisiti

Esperienza con [Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud) è obbligatorio.

È necessario avere familiarità con TeamCity 8.1. L'installazione di TeamCity non rientra nell'ambito di questo documento. Si presuppone che TeamCity sia installato su OS X Mavericks e viene eseguita in un account utente normale e non l'account radice.

Il server di compilazione deve essere un computer autonomo, che esegue OS X, dedicato a integrazione continua. In teoria, il server di compilazione non sarà responsabile di eventuali altri ruoli, ad esempio un server di database, un server web o una workstation di sviluppo.

> [!IMPORTANT]
> Questa Guida non copre un'installazione di Xamarin "headless".

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparazione del Server di compilazione

Un passaggio fondamentale per la configurazione di un server di compilazione consiste nell'installare tutti i certificati per compilare applicazioni per dispositivi mobili, gli strumenti necessari e software. È importante che il server di compilazione è in grado di compilare la soluzione mobile ed eseguire i test. Per ridurre al minimo i problemi di configurazione, il software e gli strumenti devono essere installati nello stesso account utente che sta ospitando TeamCity. Di seguito è riportato un elenco di requisiti:

1. **Visual Studio per Mac** – include xamarin. IOS e xamarin.
2. **Account di accesso per l'archivio di componenti di Xamarin** : questo passaggio è facoltativo ed è solo necessario se l'applicazione utilizza componenti dall'archivio dei componenti di Xamarin. In modo proattivo accedendo a questo punto l'archivio componenti evitare problemi durante il tentativo di compilare l'applicazione di una compilazione TeamCity.
3. **Xcode** – Xcode è obbligatorio per le applicazioni iOS compilate e accesso.
4. **Gli strumenti da riga di comando Xcode** – come descritto nel passaggio 1 della sezione di installazione del [aggiornamento Ruby con rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) Guida.
5. **La firma di identità e i profili di Provisioning** : importare i certificati e il provisioning di un profilo tramite XCode. Vedere la Guida di Apple sulla [l'esportazione di identità di firma e i profili di Provisioning](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) per altri dettagli.
6. **Android Keystores** : copiare il keystores Android necessari in una directory che l'utente TeamCity disponga dell'accesso, ad esempio `~/Documents/keystores/MyAndroidApp1`.
7. **Calabash** – questo è un passaggio facoltativo se l'applicazione dispone di test scritti usando Calabash. Vedere il [Calabash l'installazione su OS X Mavericks](https://developer.xamarin.com/guides/testcloud/calabash/osx-installation/) Guida e [aggiornamento Ruby con rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) Guida per ulteriori informazioni.

Il diagramma seguente illustra tutti questi componenti:

![](teamcity-images/image1.png "Questo diagramma vengono illustrati tutti questi componenti")

Dopo aver installato tutti i software, accedere all'account utente e verificare che tutto il software sia stato installato correttamente e sia funzionante. Questa operazione deve comportare la compilazione della soluzione e l'invio all'applicazione di Test Cloud. Questo può essere notevolmente semplificato eseguendo lo script di compilazione, come descritto nella sezione successiva.

## <a name="create-a-build-script"></a>Creare uno Script di compilazione

Sebbene sia possibile per TeamCity gestire tutti gli aspetti della compilazione e l'invio di applicazioni per dispositivi mobili al Cloud di Test da solo, è consigliabile creare uno script di compilazione. Uno script di compilazione offre i vantaggi seguenti:

1. **Documentazione** : uno script di compilazione funge da una forma di documentazione su modalità di compilazione del software. Questa operazione rimuove alcune "speciale" che è associata a una distribuzione dell'applicazione e consente agli sviluppatori di concentrarsi sulla funzionalità.
1. **Ripetibilità** : uno script di compilazione garantisce che ogni volta che l'applicazione viene compilata e distribuita, con cui si verifica esattamente nello stesso modo, indipendentemente da chi o cosa esegue il lavoro. Questa verifica di coerenza repeatable rimuove tutti i problemi o gli errori che potrebbero incidere in a causa di una compilazione eseguita in modo non corretto o errori umani.
1. **Controllo delle versioni** : uno script di compilazione può essere incluso nel sistema di controllo di origine. Questo significa che le modifiche apportate allo script di compilazione possono essere rilevate, monitorate e corretti se vengono rilevati errori o imprecisioni.
1. **Preparare l'ambiente** : uno script di compilazione può includere la logica per installare le dipendenze di terze parti 3rd qualsiasi obbligatorio. Ciò garantisce che le applicazioni vengono compilate con i componenti appropriati.

Lo script di compilazione può essere semplice come un file di Powershell (in Windows) o uno script bash (su OS X). Quando si crea lo script di compilazione, sono disponibili numerose opzioni per i linguaggi di scripting:

- [**Inclinazione** ](https://github.com/jimweirich/rake) : si tratta di un linguaggio specifico di dominio (DSL) per la compilazione di progetti, in base Ruby. Il vantaggio di diffusione e un ecosistema ricco di librerie di inclinazione.

- [**psake** ](https://github.com/psake/psake) : si tratta di una libreria di Windows Powershell per la creazione di software

- [**FALSIFICARE** ](http://fsharp.github.io/FAKE/) : si tratta di un linguaggio DSL basato in F # che rende possibile utilizzare le librerie .NET esistenti, se necessario.

Viene utilizzato il linguaggio di script dipende dalle preferenze e requisiti. Il [TaskyPro Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash) contenuto un esempio dell'utilizzo di inclinazione come un [script di compilazione](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile).


> [!NOTE]
> È possibile utilizzare un sistema di compilazione basate su XML, ad esempio MSBuild o NAnt, ma questi mancanza stesso l'espressività e gestibilità di un linguaggio DSL dedicato alla compilazione di software.

### <a name="parameterizing-the-build-script"></a>Applicazione di parametri dello Script di compilazione

Il processo di compilazione e test del software richiede informazioni che devono essere mantenute segrete. In particolare la creazione di un file APK potrebbe richiedere una password per il file keystore e/o l'alias della chiave nell'archivio. Analogamente, Test Cloud richiede una chiave API che è univoca a uno sviluppatore. Questi tipi di valori non devono essere rigidi codificato nello script di compilazione. Deve invece essere passate come variabili di script di compilazione.

Meno sensibili sono valori, ad esempio iOS ID dispositivo o l'ID del dispositivo Android che identificano quali Test Cloud devono usare per i dispositivi di test viene eseguito. Non sono valori che devono essere protetti, ma possono variare da build a build.

Archiviazione di questi tipi di variabili all'esterno dello script di compilazione rende anche più facile condividere lo script di compilazione all'interno dell'organizzazione, ad esempio con gli sviluppatori. Gli sviluppatori possono utilizzare lo stesso script come server di compilazione, ma è possono usare i propri keystores e le chiavi API.

Sono disponibili due opzioni possibili per l'archiviazione di questi valori riservati:

- **Un file di configurazione** : consente di proteggere la chiave API del Test Cloud, questo valore non deve essere selezionato nel controllo della versione. Il file può essere creato per ogni computer. Come i valori vengono letti dal file dipende dal linguaggio di script utilizzato.

- **Le variabili di ambiente** – questi possono essere impostati facilmente in base al computer e vengono indipendentemente dal fatto che il linguaggio di scripting sottostante.

Esistono vantaggi e svantaggi di ciascuna di queste selezioni. TeamCity funziona perfettamente con le variabili di ambiente, pertanto in questa Guida verrà consiglia di usare questa tecnica durante la creazione di script di compilazione.

### <a name="build-steps"></a>Istruzioni di compilazione

Lo script di compilazione deve essere in grado di eseguire la procedura seguente:

- **Compilare l'applicazione** – include firma dell'applicazione con il profilo di provisioning corretto.

- **Invia all'applicazione di Xamarin Test Cloud** : sono incluse la firma e allineamento l'APK con il file appropriato keystore zip.

Questi due passaggi verranno spiegati in dettaglio più avanti.

#### <a name="compiling-a-xamarinios-application"></a>La compilazione di un'applicazione di xamarin

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>La compilazione di un'applicazione di xamarin

Per compilare un'applicazione Android, usare **xbuild** (o **msbuild** in Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

Si noti che per compilare l'applicazione di Android di Xamarin **xbuild** utilizza il progetto e che per compilare l'applicazione iOS **xbuild** richiede che la soluzione.

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>Invio Xamarin.UITests al Cloud di Test

UITests vengono inviate tramite il `test-cloud.exe` applicazione, come illustrato nel frammento riportato di seguito:

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

Quando viene eseguito il test, restituirà i risultati del test sotto forma di un file XML di stile NUnit denominato **report.xml**. TeamCity visualizzerà le informazioni nel Log di compilazione.

Per ulteriori informazioni sull'invio UITests al Cloud di Test, consultare la Guida di Xamarin su [invio di test per Test Cloud](https://developer.xamarin.com/guides/testcloud/uitest/working-with/submitting-tests-to-xamarin-test-cloud/).

#### <a name="submitting-calabash-tests-to-test-cloud"></a>Invio Calabash test per testare Cloud

Test Calabash vengono inviate tramite il `test-cloud` indicatore, come illustrato nel frammento riportato di seguito:

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```
Per inviare un'applicazione Android Test cloud, è necessario ricompilare prima il server di prova APK di calabash android:

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```
Per ulteriori informazioni sull'invio di test Calabash, consultare la Guida di Xamarin su [invio Calabash test per Test Cloud](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/).

## <a name="creating-a-teamcity-project"></a>Creazione di un progetto a TeamCity

Una volta installato TeamCity e Visual Studio per Mac è possibile compilare il progetto, è necessario creare un progetto in TeamCity per compilare il progetto e inviarlo al Cloud di Test.

1. Accedendo a TeamCity tramite il web browser, avviata. Passare al progetto di primo livello:

    ![](teamcity-images/image2.png "Passare al progetto radice") sotto il progetto di primo livello, creare un nuovo progetto secondari:

    ![](teamcity-images/image3.png "Passare al progetto di primo livello progetto sottostante la radice, creare un nuovo progetto secondario")
2. Dopo aver creato il sottoprogetto, aggiungere una nuova configurazione di compilazione:

    ![](teamcity-images/image5.png "Dopo aver creato il sottoprogetto, aggiungere una nuova configurazione di compilazione")
3. Collegare un progetto dei contenitori dei volumi per la configurazione di compilazione. Questa operazione viene eseguita tramite la schermata di impostazione di controllo di versione:

    ![](teamcity-images/image6.png "Questa operazione viene eseguita tramite la schermata di impostazione controllo della versione")

    Se è presente alcun progetto dei contenitori dei volumi creato, è possibile crearne uno dalla pagina nuova radice dei contenitori dei volumi illustrata di seguito:

    ![](teamcity-images/image7.png "Se è presente alcun progetto dei contenitori dei volumi creato, è possibile creare una pagina nuova radice dei contenitori dei volumi")

    Una volta la radice dei contenitori dei volumi è stata collegata, TeamCity verrà estrarre il progetto e provare automaticamente rilevare le istruzioni di compilazione. Se si ha familiarità con TeamCity, è possibile selezionare una delle istruzioni di compilazione rilevati. È possibile ignorare le istruzioni di compilazione rilevati per ora.

4. Configurare quindi un Trigger di compilazione. Questo verrà inseriti nella coda di una compilazione quando vengono soddisfatte determinate condizioni, ad esempio quando un utente esegue il commit di codice al repository. Nella schermata seguente viene illustrato come aggiungere un trigger di compilazione:

    ![](teamcity-images/image8.png "Questa schermata viene illustrato come aggiungere un trigger di compilazione") un esempio di configurazione di un trigger di compilazione può essere visualizzato nella schermata seguente:

    ![](teamcity-images/image9.png "Un esempio di configurazione di un trigger di compilazione può essere visualizzato in questa schermata")

5. La sezione precedente, lo Script di compilazione, la parametrizzazione delle suggerito l'archiviazione di alcuni valori come variabili di ambiente. Queste variabili possono essere aggiunti alla configurazione di compilazione tramite la schermata di parametri. Aggiungere le variabili per la chiave API Cloud di Test, l'ID del dispositivo iOS e l'ID dispositivo Android, come illustrato nella schermata seguente:

    ![](teamcity-images/image11.png "Aggiungere le variabili per la chiave API Cloud di Test, l'ID del dispositivo iOS e l'ID del dispositivo Android")

6. Il passaggio finale consiste nell'aggiungere un'istruzione di compilazione che verrà richiamato lo script di compilazione per compilare l'applicazione e accodare l'applicazione cloud di Test. Nella schermata riportata di seguito è riportato un esempio di un'istruzione di compilazione che utilizza un Rakefile per compilare un'applicazione:

    ![](teamcity-images/image12.png "Questa schermata è riportato un esempio di un'istruzione di compilazione che utilizza un Rakefile per compilare un'applicazione")

7. A questo punto, la configurazione della build è stata completata. È consigliabile attivare una compilazione per verificare che il progetto è configurato correttamente. Un buon metodo per eseguire questa operazione è eseguire il commit di una modifica di piccole dimensioni e non significativa nel repository. TeamCity deve rilevare il commit e avviare una compilazione.

8. Una volta completata la compilazione, esaminare il log di compilazione e vedere se sono presenti problemi o gli avvisi con la compilazione che richiedono attenzione.

## <a name="summary"></a>Riepilogo

Questa guida viene descritto come utilizzare TeamCity per applicazioni mobili di Xamarin e quindi inviarli a Test Cloud. Abbiamo parlato la creazione di uno script di compilazione per automatizzare il processo di compilazione. Lo script di compilazione si occupa della compilazione dell'applicazione, invio al Cloud di Test e in attesa dei risultati

È quindi descritto come creare un progetto in TeamCity accodare una compilazione ogni volta che uno sviluppatore esegue il commit di codice che chiama lo script di compilazione.

## <a name="related-links"></a>Collegamenti correlati

- [Invio di test con Xamarin Test Cloud (UITest)](https://developer.xamarin.com~/testcloud/uitest/working-with/submitting-tests-to-xamarin-test-cloud/)
- [Invio di test con Xamarin Test Cloud (Calabash)](https://developer.xamarin.com~/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/)
- [Installazione e configurazione TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
