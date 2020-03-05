---
title: Uso del team City con Novell
description: Questa guida illustra i passaggi necessari per l'uso di TeamCity per compilare applicazioni per dispositivi mobili e quindi inviarli a Xamarin Test Cloud.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 94bc775366d832e0994b8d3c74a45123ff56c13b
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292229"
---
# <a name="using-team-city-with-xamarin"></a>Uso del team City con Novell

_Questa guida illustra i passaggi necessari per l'uso di TeamCity per compilare applicazioni per dispositivi mobili e quindi inviarli a Xamarin Test Cloud._

Come illustrato nella Guida [introduttiva all'integrazione continua](~/tools/ci/intro-to-ci.md) , l'integrazione continua (ci) è una procedura utile per lo sviluppo di applicazioni per dispositivi mobili di qualità. Sono disponibili numerose opzioni valide per il software server di integrazione continua; Questa guida si concentra su [TeamCity](https://www.jetbrains.com/teamcity/) di JetBrains.

Sono disponibili diverse permutazioni di un'installazione TeamCity. Di seguito è riportato un elenco di alcuni di questi:

- **Servizio Windows** : in questo scenario, TeamCity viene avviato quando Windows viene avviato come servizio Windows. Deve essere abbinato a un host di compilazione Mac per compilare qualsiasi applicazione iOS.

- **Launch daemon on OS X** : concettualmente, è molto simile all'esecuzione come un servizio di Windows descritto nel passaggio precedente. Per impostazione predefinita, le compilazioni verranno eseguite con l'account radice.

- **Account utente su OS X** : è possibile eseguire TeamCity con un account utente che viene avviato ogni volta che l'utente esegue l'accesso.

Negli scenari precedenti, l'esecuzione di TeamCity con un account utente in OS X è la più semplice e facile da configurare.

Per la configurazione di TeamCity sono necessari diversi passaggi:

- **Installazione di TeamCity** : l'installazione di TeamCity non è illustrata in questa guida. Questa guida presuppone che TeamCity sia installato e in esecuzione con un account utente. Le istruzioni per [l'installazione di TeamCity](https://confluence.jetbrains.com/display/TCD8/Installation) sono disponibili nella [documentazione di TeamCity 8](https://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) di JetBrains.

- **Preparazione del server di compilazione** : questo passaggio prevede l'installazione del software, degli strumenti e dei certificati necessari per creare applicazioni per dispositivi mobili e prepararle per la distribuzione.

- **Creazione di uno script di compilazione** : questo passaggio non è strettamente necessario, ma uno script di compilazione costituisce un utile strumento per la creazione automatica di applicazioni. L'utilizzo di uno script di compilazione consente di risolvere i problemi di compilazione che possono verificarsi e fornisce un modo coerente e ripetibile per creare i file binari per la distribuzione, anche se l'integrazione continua non viene praticata.

- **Creazione di un progetto TeamCity** : dopo aver completato i tre passaggi precedenti, è necessario creare un progetto TeamCity che conterrà tutti i metadati necessari per recuperare il codice sorgente, compilare i progetti e inviare i test a Xamarin test cloud.

## <a name="requirements"></a>Requisiti

L'esperienza con [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/) è obbligatoria.

È necessaria una certa familiarità con TeamCity 8,1. L'installazione di TeamCity esula dall'ambito di questo documento. Si presuppone che TeamCity sia installato in OS X Mavericks e che sia in esecuzione con un account utente normale e non con l'account radice.

Il server di compilazione deve essere un computer autonomo, che esegue OS X, dedicato all'integrazione continua. Idealmente, il server di compilazione non sarà responsabile di altri ruoli, ad esempio un server di database, un server Web o una workstation per sviluppatori.

> [!IMPORTANT]
> In questa guida non viene illustrata l'installazione di Novell.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparazione del server di compilazione

Un passaggio cruciale per la configurazione di un server di compilazione consiste nell'installare tutti gli strumenti, il software e i certificati necessari per compilare le applicazioni per dispositivi mobili. È importante che il server di compilazione sia in grado di compilare la soluzione mobile ed eseguire tutti i test. Per ridurre al minimo i problemi di configurazione, è necessario installare il software e gli strumenti nello stesso account utente che ospita TeamCity. Di seguito è riportato un elenco degli elementi necessari:

1. **Visual Studio per Mac** : sono inclusi Novell. iOS e Novell. Android.
2. **Accedi all'archivio componenti di Novell** : si tratta di un passaggio facoltativo ed è necessario solo se l'applicazione usa componenti dall'archivio componenti di Novell. L'accesso proattivo nell'archivio componenti a questo punto impedirà eventuali problemi quando una compilazione TeamCity tenta di compilare l'applicazione.
3. **Xcode** : Xcode è necessario per compilare e firmare le applicazioni iOS.
4. **Strumenti da riga di comando Xcode** : questa procedura è descritta nel passaggio 1 della sezione relativa all'installazione della Guida relativa all' [aggiornamento di Ruby con rbenv](https://github.com/calabash/calabash-ios/wiki) .
5. **Firma di identità & profili di provisioning** : importare i certificati e il profilo di provisioning tramite Xcode. Per altri dettagli, vedere la Guida di Apple sull' [esportazione di identità di firma e profili di provisioning](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) .
6. **Keystore Android** : copiare i keystore Android necessari in una directory a cui l'utente TeamCity può accedere, ad esempio `~/Documents/keystores/MyAndroidApp1`.
7. **Calabash** : si tratta di un passaggio facoltativo se l'applicazione include test scritti con Calabash. Per ulteriori informazioni, vedere la Guida [installazione di Calabash in OS X Mavericks](https://github.com/calabash/calabash-ios/wiki) e la guida sull' [aggiornamento di Ruby con rbenv](https://github.com/calabash/calabash-ios/wiki) .

Il diagramma seguente illustra tutti questi componenti:

![](teamcity-images/image1.png "This diagram illustrates all of these components")

Una volta installato tutto il software, accedere all'account utente e verificare che tutto il software sia stato installato correttamente e funzionante. Questa operazione dovrebbe comportare la compilazione della soluzione e l'invio dell'applicazione a Test Cloud. Questo può essere molto semplificato eseguendo lo script di compilazione, come descritto nella sezione successiva.

## <a name="create-a-build-script"></a>Creare uno script di compilazione

Sebbene sia completamente possibile per TeamCity gestire tutti gli aspetti della compilazione e dell'invio delle applicazioni per dispositivi mobili a Test Cloud da solo, è consigliabile creare uno script di compilazione. Uno script di compilazione offre i vantaggi seguenti:

1. **Documentazione** : uno script di compilazione funge da documentazione su come viene compilato il software. In questo modo viene rimossa la "magia" associata alla distribuzione dell'applicazione e gli sviluppatori possono concentrarsi sulle funzionalità.
1. **Ripetibilità** : uno script di compilazione garantisce che ogni volta che l'applicazione viene compilata e distribuita, si verifica esattamente allo stesso modo, indipendentemente da chi o cosa funziona. Questa coerenza ripetibile rimuove eventuali problemi o errori che potrebbero verificarsi a causa di una compilazione non corretta o di un errore umano.
1. Controllo delle **versioni** : è possibile includere uno script di compilazione nel sistema di controllo del codice sorgente. Ciò significa che le modifiche apportate allo script di compilazione possono essere rilevate, monitorate e corrette in caso di errori o inesattezze.
1. **Preparare l'ambiente** : uno script di compilazione può includere la logica per installare le dipendenze di terze parti necessarie. In questo modo si garantisce che le applicazioni vengano compilate con i componenti appropriati.

Lo script di compilazione può essere semplice come un file di PowerShell (in Windows) o uno script bash (su OS X). Quando si crea lo script di compilazione, sono disponibili diverse opzioni per i linguaggi di scripting:

- [**Rake**](https://github.com/jimweirich/rake) : si tratta di un linguaggio specifico di dominio (DSL) per la compilazione di progetti basati su Ruby. Rake presenta il vantaggio della popolarità e un ricco ecosistema di librerie.

- [**psake**](https://github.com/psake/psake) : si tratta di una libreria di Windows PowerShell per la creazione di software

- [**Fake**](https://fsharp.github.io/FAKE/) : si tratta di un linguaggio DSL F# basato su, che consente di utilizzare le librerie .NET esistenti, se necessario.

Il linguaggio di scripting utilizzato dipende dalle preferenze e dai requisiti.

> [!NOTE]
> È possibile utilizzare un sistema di compilazione basato su XML, ad esempio MSBuild o NAnt, ma questi non hanno l'espressività e la gestibilità di un linguaggio DSL dedicato alla creazione di software.

### <a name="parameterizing-the-build-script"></a>Parametrizzazione lo script di compilazione

Il processo di compilazione e testing del software richiede informazioni che devono essere mantenute segrete. In particolare, la creazione di un APK potrebbe richiedere una password per l'archivio chiavi e/o l'alias della chiave nell'archivio chiavi. Allo stesso modo, Test Cloud richiede una chiave API univoca per uno sviluppatore. Questi tipi di valori non devono essere hardcoded nello script di compilazione. Ma devono essere passati come variabili allo script di compilazione.

Meno sensibili sono i valori, ad esempio l'ID dispositivo iOS o l'ID dispositivo Android, che identificano i dispositivi Test Cloud da usare per le esecuzioni dei test. Non si tratta di valori che devono essere protetti, ma possono cambiare da Build a Build.

L'archiviazione di questi tipi di variabili all'esterno dello script di compilazione facilita anche la condivisione dello script di compilazione all'interno di un'organizzazione, ad esempio per gli sviluppatori. Gli sviluppatori possono utilizzare esattamente lo stesso script del server di compilazione, ma possono utilizzare chiavi API e chiavi personalizzate.

Esistono due possibili opzioni per archiviare i valori sensibili:

- **Un file di configurazione** : per proteggere la chiave API test cloud questo valore non deve essere archiviato nel controllo della versione. Il file può essere creato per ogni computer. Il modo in cui i valori vengono letti da questo file dipende dal linguaggio di scripting utilizzato.

- **Variabili di ambiente** : possono essere impostate facilmente in base al computer e analizza indipendentemente dal linguaggio di scripting sottostante.

Ognuna di queste scelte presenta vantaggi e svantaggi. TeamCity funziona perfettamente con le variabili di ambiente, quindi questa guida consiglierà questa tecnica quando si creano script di compilazione.

### <a name="build-steps"></a>Procedura di compilazione

Lo script di compilazione deve essere in grado di eseguire i passaggi seguenti:

- **Compilare l'applicazione** : è inclusa la firma dell'applicazione con il profilo di provisioning corretto.

- **Inviare l'applicazione a Xamarin test cloud** : questa operazione include la firma e il file zip che allinea l'apk con l'archivio chiavi appropriato.

Questi due passaggi verranno illustrati in dettaglio di seguito.

#### <a name="compiling-a-xamarinios-application"></a>Compilazione di un'applicazione Novell. iOS

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>Compilazione di un'applicazione Novell. Android

Per compilare un'applicazione Android, usare **xbuild** (o **MSBuild** in Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

Si noti che per compilare l'applicazione Novell Android **xbuild** usa il progetto e che per compilare l'applicazione iOS **xbuild** richiede la soluzione.

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>Invio di Novell. UITests a Test Cloud

UITests vengono inviati utilizzando l'applicazione `test-cloud.exe`, come illustrato nel frammento di codice seguente:

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

Quando si esegue il test, i risultati del test verranno restituiti sotto forma di file XML di tipo NUnit denominato **report. XML**. TeamCity visualizzerà le informazioni nel log di compilazione.

Per altre informazioni su come inviare UITests a Test Cloud, vedere Preparazione delle app [Novell. Android](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest) o [preparazione delle app Novell. iOS](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest).

#### <a name="submitting-calabash-tests-to-test-cloud"></a>Invio di test di Calabash a Test Cloud

I test Calabash vengono inviati usando il `test-cloud` Gem, come illustrato nel frammento di codice seguente:

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```

Per inviare un'applicazione Android a Test Cloud, è necessario innanzitutto ricompilare il server di test APK usando Calabash-Android:

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```

Per ulteriori informazioni sull'invio di test di Calabash, consultare la Guida di Novell sull' [invio di test Calabash a test cloud](https://github.com/calabash/calabash-ios/wiki).

## <a name="creating-a-teamcity-project"></a>Creazione di un progetto TeamCity

Una volta installato TeamCity e Visual Studio per Mac possibile compilare il progetto, è necessario creare un progetto in TeamCity per compilare il progetto e inviarlo al Test Cloud.

1. Per iniziare, accedere a TeamCity tramite il Web browser. Passare al progetto radice:

    ![Passare al progetto radice](teamcity-images/image2.png "Passare al progetto radice") Sotto il progetto radice creare un nuovo progetto secondario:

    ![Passare al progetto radice sotto il progetto radice e creare un nuovo sottoprogetto](teamcity-images/image3.png "Passare al progetto radice sotto il progetto radice e creare un nuovo sottoprogetto")
2. Una volta creato il sottoprogetto, aggiungere una nuova configurazione di compilazione:

    ![Una volta creato il sottoprogetto, aggiungere una nuova configurazione di compilazione](teamcity-images/image5.png "Una volta creato il sottoprogetto, aggiungere una nuova configurazione di compilazione")
3. Alleghi un progetto VCS alla configurazione della build. Questa operazione viene eseguita tramite la schermata di impostazione del controllo della versione:

    ![Questa operazione viene eseguita tramite la schermata di impostazione del controllo della versione](teamcity-images/image6.png "Questa operazione viene eseguita tramite la schermata di impostazione del controllo della versione")

    Se non è stato creato alcun progetto VCS, è possibile crearne uno dalla nuova pagina radice VCS mostrata di seguito:

    ![Se non è stato creato alcun progetto VCS, è possibile crearne uno dalla nuova pagina radice di VCS](teamcity-images/image7.png "Se non è stato creato alcun progetto VCS, è possibile crearne uno dalla nuova pagina radice di VCS")

    Una volta collegata la radice VCS, TeamCity estrae il progetto e tenta di rilevare automaticamente le istruzioni di compilazione. Se si ha familiarità con TeamCity, è possibile selezionare uno dei passaggi di compilazione rilevati. Per il momento è possibile ignorare i passaggi di compilazione rilevati.

4. Configurare quindi un trigger di compilazione. In questo modo verrà accodata una compilazione quando vengono soddisfatte determinate condizioni, ad esempio quando un utente esegue il commit del codice nel repository. Lo screenshot seguente mostra come aggiungere un trigger di compilazione:

    ![Questa schermata mostra come aggiungere un trigger di compilazione](teamcity-images/image8.png "Questa schermata mostra come aggiungere un trigger di compilazione") Un esempio di configurazione di un trigger di compilazione può essere illustrato nello screenshot seguente:

    ![Un esempio di configurazione di un trigger di compilazione può essere visualizzato in questa schermata](teamcity-images/image9.png "Un esempio di configurazione di un trigger di compilazione può essere visualizzato in questa schermata")

5. La sezione precedente, parametrizzazione lo script di compilazione, suggerisce di archiviare alcuni valori come variabili di ambiente. Queste variabili possono essere aggiunte alla configurazione della build tramite la schermata parametri. Aggiungere le variabili per la chiave API Test Cloud, l'ID dispositivo iOS e l'ID dispositivo Android, come illustrato nella schermata seguente:

    ![Aggiungere le variabili per la chiave API Test Cloud, l'ID dispositivo iOS e l'ID dispositivo Android](teamcity-images/image11.png "Aggiungere le variabili per la chiave API Test Cloud, l'ID dispositivo iOS e l'ID dispositivo Android")

6. Il passaggio finale consiste nell'aggiungere un'istruzione di compilazione che richiama lo script di compilazione per compilare l'applicazione e accodare l'applicazione per Test Cloud. Lo screenshot seguente è un esempio di un'istruzione di compilazione che usa un Rakefile per compilare un'applicazione:

    ![Questa schermata è un esempio di un'istruzione di compilazione che usa un Rakefile per compilare un'applicazione](teamcity-images/image12.png "Questa schermata è un esempio di un'istruzione di compilazione che usa un Rakefile per compilare un'applicazione")

7. A questo punto, la configurazione della build è stata completata. È consigliabile attivare una compilazione per verificare che il progetto sia configurato correttamente. Un modo efficace per eseguire questa operazione consiste nell'eseguire il commit di una piccola modifica irrilevante nel repository. TeamCity deve rilevare il commit e avviare una compilazione.

8. Al termine della compilazione, esaminare il log di compilazione e verificare se sono presenti problemi o avvisi con la compilazione che richiedono attenzione.

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come usare TeamCity per compilare applicazioni per dispositivi mobili Novell e quindi inviarle a Test Cloud. È stato illustrato come creare uno script di compilazione per automatizzare il processo di compilazione. Lo script di compilazione occupa la compilazione dell'applicazione, l'invio a Test Cloud e l'attesa dei risultati

È stato quindi illustrato come creare un progetto in TeamCity che accoda una compilazione ogni volta che uno sviluppatore eseguirà il commit del codice e chiamerà lo script di compilazione.

## <a name="related-links"></a>Collegamenti correlati

- [Preparazione delle app Novell. Android](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest)
- [Preparazione delle app Novell. iOS](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Installazione e configurazione di TeamCity](https://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
