---
title: Utilizzando Team City con Xamarin
description: Questa Guida verrà descritte le operazioni necessarie per l'utilizzo a TeamCity per compilare applicazioni per dispositivi mobili e quindi inviarli a Xamarin Test Cloud.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: topgenorth
ms.author: toopge
ms.date: 03/23/2017
ms.openlocfilehash: e7279c03c730e95f211b555e5b832942c19ea8aa
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935415"
---
# <a name="using-team-city-with-xamarin"></a>Utilizzando Team City con Xamarin

_Questa Guida verrà descritte le operazioni necessarie per l'utilizzo a TeamCity per compilare applicazioni per dispositivi mobili e quindi inviarli a Xamarin Test Cloud._

Come descritto nel [Introduzione all'integrazione continua](~/tools/ci/intro-to-ci.md) Guida, integrazione continua (CI) è un'operazione utile quando si sviluppano applicazioni per dispositivi mobili di qualità. Sono disponibili molte opzioni valide per il software server di integrazione continua; Questa guida è incentrata sulla [a TeamCity](http://www.jetbrains.com/teamcity/) da JetBrains.

Esistono diverse permutazioni diverse di un'installazione a TeamCity. Di seguito è riportato un elenco di alcune di queste:

- **Servizio Windows** : In questo scenario, verrà avviata a TeamCity quando Windows viene avviato come servizio Windows. Deve essere abbinato a un Host di compilazione Mac per compilare le applicazioni iOS.

- **Avvio del daemon di filtri su OS X** : a livello concettuale, è molto simile all'esecuzione come servizio Windows descritto nel passaggio precedente. Per impostazione predefinita le compilazioni verranno eseguite nell'account radice.

- **Account utente su OS X** – è possibile eseguire a TeamCity con un account utente che viene avviato ogni volta che l'utente accede.

Degli scenari precedenti, in esecuzione a TeamCity con un account utente su OS X è il modo più semplice per l'installazione.

Sono disponibili diversi passaggi con impostazione a TeamCity:

- **Installazione a TeamCity** : l'installazione di a TeamCity non viene descritta in questa Guida. Questa guida si presuppone che a TeamCity sia installato e in esecuzione con un account utente. Istruzioni sulla [installazione a TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation) è reperibile nella [documentazione a TeamCity 8](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) da JetBrains.

- **Preparazione del Server di compilazione** : questo passaggio prevede l'installazione del software necessario, strumenti e i certificati necessari per compilare applicazioni per dispositivi mobili e prepararli per la distribuzione.

- **Creazione di Script di compilazione A** : questo passaggio non è strettamente necessario, ma uno script di compilazione è utile per la compilazione di applicazioni automatiche. Utilizzo di uno script di compilazione favorirà la risoluzione dei problemi di compilazione che potrebbero verificarsi e fornisce un modo coerenza e ripetibile per creare i file binari per la distribuzione, anche se non è eseguiti integrazione continua.

- **Creazione di un progetto di a TeamCity** : dopo aver completati i tre passaggi precedenti, è necessario creare un progetto a TeamCity che conterrà tutti i metadati necessari per recuperare il codice sorgente, compilare i progetti e inviare i test da Xamarin Test Cloud.

## <a name="requirements"></a>Requisiti

Esperienza con [Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud) è obbligatorio.

È necessario avere familiarità con a TeamCity 8.1. L'installazione di a TeamCity esula dall'ambito di questo documento. Si presuppone che a TeamCity sia installato su OS X Mavericks e viene eseguita in un account utente normale e non l'account root.

Il server di compilazione deve essere un computer autonomo, che esegue OS X, dedicato a integrazione continua. In teoria, il server di compilazione non sarà responsabile di eventuali altri ruoli, ad esempio un server di database, un server web o una workstation di sviluppo.

> [!IMPORTANT]
> Questa Guida non viene illustrata un'installazione di Xamarin "headless".

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparazione del Server di compilazione

Un importante passo nella configurazione di un server di compilazione consiste nell'installare tutti gli strumenti necessari, software e i certificati per compilare applicazioni per dispositivi mobili. È importante che il server di compilazione in grado di compilare la soluzione mobile ed eseguire tutti i test. Per ridurre al minimo i problemi di configurazione, il software e gli strumenti devono essere installati nello stesso account utente di cui si trova a TeamCity. Di seguito è riportato un elenco di ciò che è necessario:

1. **Visual Studio per Mac** – include xamarin. IOS e xamarin. Android.
2. **Account di accesso per l'archivio di componenti di Xamarin** – in questo passaggio è facoltativo ed è solo necessario se l'applicazione utilizza componenti dall'archivio dei componenti di Xamarin. In modo proattivo la registrazione nell'archivio del componente a questo punto verrà evitare problemi durante il tentativo di compilare l'applicazione di una compilazione a TeamCity.
3. **Xcode** – Xcode è obbligatorio per le applicazioni iOS compilate e accesso.
4. **Strumenti della riga di comando Xcode** -questo aspetto è descritto nel passaggio 1 della sezione di installazione del [aggiornamento Ruby con rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) Guida.
5. **La firma di identità e i profili di Provisioning** : importare i certificati e il provisioning di un profilo tramite XCode. Vedere la Guida di Apple sulla [esportazione identità di firma e i profili di Provisioning](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) per altri dettagli.
6. **Android Keystores** : copiare il keystores Android necessari in una directory che l'utente a TeamCity disponga dell'accesso, ad esempio `~/Documents/keystores/MyAndroidApp1`.
7. **Calabash** – questo è un passaggio facoltativo se l'applicazione dispone di test scritti usando Calabash. Vedere la [Calabash installazione su OS X Mavericks](https://developer.xamarin.com/guides/testcloud/calabash/osx-installation/) Guida e il [aggiornamento Ruby con rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) Guida per ulteriori informazioni.

Il diagramma seguente illustra tutti questi componenti:

![](teamcity-images/image1.png "Questo diagramma vengono illustrati tutti questi componenti")

Dopo aver installato tutti i software, accedere all'account utente e verificare che tutto il software sia stato installato correttamente e sia funzionante. Questa deve comportare la compilazione della soluzione e l'invio all'applicazione di Test Cloud. Ciò può essere notevolmente semplificato eseguendo lo script di compilazione, come descritto nella sezione successiva.

## <a name="create-a-build-script"></a>Creare uno Script di compilazione

Anche se è dunque possibile che a TeamCity gestire tutti gli aspetti della compilazione e l'invio di applicazioni per dispositivi mobili al Cloud di Test da solo, è consigliabile creare uno script di compilazione. Uno script di compilazione offre i vantaggi seguenti:

1. **Documentazione** – uno script di compilazione funge da una forma di documentazione su modalità di compilazione del software. Questa operazione rimuove alcune "chiave" che è associata la distribuzione dell'applicazione e consente agli sviluppatori di concentrarsi sulla funzionalità.
1. **Ripetibilità** – uno script di compilazione garantisce che ogni volta che l'applicazione viene compilata e distribuita, con cui si verifica in esattamente allo stesso modo, indipendentemente da chi o cosa esegue il lavoro. Questa verifica di coerenza repeatable rimuove tutti i problemi o gli errori che potrebbero incidere in a causa di una compilazione eseguita in modo non corretto o errori umani.
1. **Controllo delle versioni** – uno script di compilazione può essere incluso nel sistema di controllo di origine. Ciò significa che le modifiche apportate allo script di compilazione possono essere rilevate e monitorate, mentre corretti se vengono rilevati errori o imprecisioni.
1. **Preparare l'ambiente** : uno script di compilazione può includere la logica per installare qualsiasi obbligatorio 3rd dipendenze di terze parti. Ciò garantisce che le applicazioni vengono compilate con i componenti appropriati.

Script di compilazione può essere semplice come un file di Powershell (in Windows) o uno script bash (su OS X). Quando si crea lo script di compilazione, sono disponibili numerose opzioni per i linguaggi di scripting:

- [**Inclinazione** ](https://github.com/jimweirich/rake) – si tratta di un linguaggio specifico di dominio (DSL) per la compilazione di progetti, in base a Ruby. Il vantaggio di diffusione e un ecosistema completo delle librerie di inclinazione.

- [**psake** ](https://github.com/psake/psake) – si tratta di una libreria di Windows Powershell per la creazione di software

- [**FALSIFICARE** ](http://fsharp.github.io/FAKE/) – si tratta di un linguaggio DSL basato in F # che rende possibile utilizzare librerie .NET esistenti, se necessario.

Viene utilizzato il linguaggio di scripting dipende dalle preferenze personali e requisiti. Il [TaskyPro Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash) contenuto un esempio dell'utilizzo di inclinazione come una [compilarne](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile).

> [!NOTE]
> È possibile utilizzare un sistema di compilazione basate su XML, ad esempio MSBuild o NAnt, ma questi mancanza stesso l'espressività e gestibilità di un linguaggio DSL dedicato alla compilazione di software.

### <a name="parameterizing-the-build-script"></a>Applicazione di parametri dello Script di compilazione

Il processo di compilazione e test del software richiede le informazioni che devono essere mantenute segrete. In particolare la creazione di un file APK potrebbe richiedere una password per il file keystore e/o l'alias della chiave nell'archivio. Analogamente, Test Cloud richiede una chiave API che è univoca a uno sviluppatore. Questi tipi di valori non devono essere rigidi codificati nello script di compilazione. Deve invece essere passate come variabili dello script di compilazione.

Natura meno sensibili sono valori ad esempio iOS ID dispositivo o l'ID del dispositivo Android che identificano l'attività eseguite dai dispositivi per che devono usare Cloud di Test test viene eseguito. Non sono valori che devono essere protette, ma queste potrebbero cambiare da build a build.

Archiviazione di questi tipi di variabili all'esterno dello script di compilazione rende più facile la condivisione dello script di compilazione all'interno di un'organizzazione, con gli sviluppatori, ad esempio. Gli sviluppatori possono utilizzare lo stesso script come server di compilazione, ma è possono usare i propri keystores e le chiavi API.

Sono disponibili due opzioni possibili per l'archiviazione di questi valori riservati:

- **Un file di configurazione** : consente di proteggere la chiave API del Cloud Test questo valore non deve essere selezionato nel controllo della versione. Il file può essere creato per ogni computer. Modo in cui i valori vengono letti da questo file dipende il linguaggio di scripting utilizzato.

- **Le variabili di ambiente** – questi possono essere impostati facilmente in base al computer e vengono indipendentemente dal linguaggio di scripting sottostante.

Esistono vantaggi e svantaggi a ognuna di queste opzioni. A TeamCity funziona correttamente con le variabili di ambiente, quindi in questa guida consiglierà questa tecnica durante la creazione di script di compilazione.

### <a name="build-steps"></a>Istruzioni di compilazione

Lo script di compilazione deve essere in grado di eseguire la procedura seguente:

- **Compilare l'applicazione** – tra l'applicazione con il profilo di provisioning corretto.

- **Invia all'applicazione di Xamarin Test Cloud** – sono inclusi la firma e allineamento dei file APK con il file keystore appropriato zip.

Questi due passaggi verranno spiegati in dettaglio più avanti.

#### <a name="compiling-a-xamarinios-application"></a>La compilazione di un'applicazione di xamarin. IOS

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>La compilazione di un'applicazione di xamarin

Per compilare un'applicazione Android, usare **xbuild** (o **msbuild** in Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

Si noti che per compilare l'applicazione di Android di Xamarin **xbuild** utilizza il progetto e che per compilare l'applicazione iOS **xbuild** richiede che la soluzione.

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>Invio Xamarin.UITests al Cloud di Test

UITests vengono inviate tramite il `test-cloud.exe` dell'applicazione, come illustrato nel frammento riportato di seguito:

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

Quando viene eseguito il test, i risultati del test verranno restituiti sotto forma di un file XML di stile NUnit chiamato **XML**. A TeamCity visualizzerà le informazioni nel Log di compilazione.

Per ulteriori informazioni sull'invio UITests al Cloud di Test, consultare questa Guida nella [Xamarin.UITests preparazione per il caricamento](/appcenter/test-cloud/preparing-for-upload/uitest/).

#### <a name="submitting-calabash-tests-to-test-cloud"></a>Invio Calabash test per testare Cloud

Test Calabash vengono inviate tramite il `test-cloud` indicatore, come illustrato nel frammento riportato di seguito:

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```
Per inviare un'applicazione Android al Cloud di Test, è necessario ricompilare prima il server di prova APK di android calabash:

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```
Per ulteriori informazioni sull'invio di test Calabash, consultare la Guida di Xamarin sul [invio Calabash test per Test Cloud](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/).

## <a name="creating-a-teamcity-project"></a>Creazione di un progetto a TeamCity

Una volta a TeamCity sia installato e Visual Studio per Mac può compilare il progetto, è necessario creare un progetto in a TeamCity per compilare il progetto e inviarlo al Cloud di Test.

1. Avviato eseguendo l'accesso a TeamCity tramite il web browser. Passare al progetto radice:

    ![Passare al progetto di primo livello](teamcity-images/image2.png "passare al progetto di primo livello") sotto il progetto di primo livello, creare un nuovo progetto secondari:

    ![Passare al progetto di primo livello progetto sottostante la radice, creare un nuovo progetto secondari](teamcity-images/image3.png "passare per il progetto sottostante la radice progetto radice, creare un nuovo progetto secondario")
2. Dopo aver creato il sottoprogetto, aggiungere una nuova configurazione di compilazione:

    ![Dopo aver creato il sottoprogetto, aggiungere una nuova configurazione di compilazione](teamcity-images/image5.png "dopo aver creato il sottoprogetto, aggiungere una nuova configurazione di compilazione")
3. Collegare un progetto dei contenitori dei volumi per la configurazione di compilazione. Questa operazione viene eseguita tramite la schermata di impostazione controllo della versione:

    ![Questa operazione viene eseguita tramite la schermata di impostazione controllo della versione](teamcity-images/image6.png "questa operazione viene eseguita tramite la schermata di impostazione controllo della versione")

    Se è presente alcun progetto dei contenitori dei volumi creato, si ha la possibilità di crearne uno dalla pagina nuova radice dei contenitori dei volumi illustrata di seguito:

    ![Se è presente alcun progetto dei contenitori dei volumi creato, si ha la possibilità di crearne uno dalla pagina nuova radice dei contenitori dei volumi](teamcity-images/image7.png "se è presente alcun progetto dei contenitori dei volumi creato, si ha la possibilità di crearne uno dalla pagina nuova radice dei contenitori dei volumi")

    Dopo la radice dei contenitori dei volumi è stata associata, a TeamCity verrà estrarre il progetto e provare automaticamente di rilevare le istruzioni di compilazione. Se si ha familiarità con a TeamCity, è possibile selezionare una delle istruzioni di compilazione rilevati. È possibile ignorare le istruzioni di compilazione rilevati per il momento.

4. A questo punto, configurare un Trigger di compilazione. In questo verrà inseriti nella coda di una compilazione quando vengono soddisfatte determinate condizioni, ad esempio quando un utente esegue il commit di codice al repository. Nella schermata seguente viene illustrato come aggiungere un trigger di compilazione:

    ![Questa schermata viene illustrato come aggiungere un trigger di compilazione](teamcity-images/image8.png "questa schermata viene illustrato come aggiungere un trigger di compilazione") un esempio di configurazione di un trigger di compilazione può essere visualizzato nella schermata seguente:

    ![Un esempio di configurazione di un trigger di compilazione può essere visualizzato in questa schermata](teamcity-images/image9.png "un esempio di configurazione di un trigger di compilazione può essere visualizzato in questa schermata")

5. La sezione precedente, lo Script di compilazione, la parametrizzazione delle suggerito l'archiviazione di alcuni valori come variabili di ambiente. Queste variabili possono essere aggiunti alla configurazione di compilazione tramite la schermata di parametri. Aggiungere le variabili per la chiave API Cloud di Test, l'ID del dispositivo iOS e l'ID dispositivo Android, come illustrato nella schermata seguente:

    ![Aggiungere le variabili per la chiave API Cloud di Test, l'ID del dispositivo iOS e l'ID del dispositivo Android](teamcity-images/image11.png "aggiungere le variabili per la chiave API Cloud di Test, l'ID del dispositivo iOS e l'ID del dispositivo Android")

6. Il passaggio finale consiste nell'aggiungere un'istruzione di compilazione che verrà richiamato lo script di compilazione per compilare l'applicazione e accodare l'applicazione al Cloud di Test. Nella schermata seguente è riportato un esempio di un'istruzione di compilazione che utilizza un Rakefile per compilare un'applicazione:

    ![Questa schermata è riportato un esempio di un'istruzione di compilazione che utilizza un Rakefile per compilare un'applicazione](teamcity-images/image12.png "questa schermata è riportato un esempio di un'istruzione di compilazione che utilizza un Rakefile per compilare un'applicazione")

7. A questo punto, la configurazione della build è stata completata. È consigliabile attivare una compilazione per verificare che il progetto è configurato correttamente. Un buon metodo per eseguire questa operazione è eseguire il commit di una modifica di piccole dimensioni, non significativa nel repository. A TeamCity deve rilevare il commit e avviare una compilazione.

8. Una volta completata la compilazione, controllare il log di compilazione, vedere se sono presenti problemi o gli avvisi con la compilazione che richiedono attenzione.

## <a name="summary"></a>Riepilogo

Questa guida viene descritto come utilizzare a TeamCity per compilare applicazioni per dispositivi mobili Xamarin e quindi inviarli a Test Cloud. Abbiamo parlato la creazione di uno script di compilazione per automatizzare il processo di compilazione. Script di compilazione si occupa della compilazione dell'applicazione, l'invio al Cloud di Test e i risultati in attesa

È quindi descritto come creare un progetto in a TeamCity che verranno inseriti nella coda una build ogni volta che uno sviluppatore esegue il commit di codice e chiamerà dello script di compilazione.

## <a name="related-links"></a>Collegamenti correlati

- [Preparazione ad Xamarin.UITests caricamento](/appcenter/test-cloud/preparing-for-upload/uitest/)
- [Installazione e configurazione a TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
