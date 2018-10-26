---
title: Uso Team City con Xamarin
description: Questa guida illustra i passaggi necessari per l'uso di TeamCity per compilare applicazioni per dispositivi mobili e quindi inviarli a Xamarin Test Cloud.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: lobrien
ms.author: laobri
ms.date: 03/23/2017
ms.openlocfilehash: a3cb79f33b64d933aa8ab4d3555479cc16238992
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118266"
---
# <a name="using-team-city-with-xamarin"></a>Uso Team City con Xamarin

_Questa guida illustra i passaggi necessari per l'uso di TeamCity per compilare applicazioni per dispositivi mobili e quindi inviarli a Xamarin Test Cloud._

Come descritto nel [Introduzione all'integrazione continua](~/tools/ci/intro-to-ci.md) Guida, integrazione continua (CI) è uno strumento utile quando si sviluppano applicazioni per dispositivi mobili di qualità. Sono disponibili molte opzioni valide per il software server di integrazione continua; Questa guida è incentrata sul [TeamCity](http://www.jetbrains.com/teamcity/) di JetBrains.

Esistono diverse permutazioni diverse di un'installazione di TeamCity. Di seguito è riportato un elenco di alcune di queste:

- **Servizio Windows** : In questo scenario, avvio TeamCity quando Windows viene avviato come servizio Windows. Deve essere abbinato con un Host di compilazione Mac per compilare le applicazioni iOS.

- **Avvio del Daemon su OS X** : a livello concettuale, è molto simile all'esecuzione come servizio Windows descritto nel passaggio precedente. Per impostazione predefinita le compilazioni verranno eseguite con l'account radice.

- **Account utente in OS X** – è possibile eseguire TeamCity con un account utente che viene avviato ogni volta che l'utente accede.

Allo scenario precedente, l'esecuzione di TeamCity con un account utente in OS X è il modo più semplice per l'installazione.

Sono disponibili diversi passaggi di configurazione di TeamCity:

- **Installazione di TeamCity** : l'installazione di TeamCity non trattata in questa Guida. Questa guida si presuppone che TeamCity sia installato e in esecuzione con un account utente. Istruzioni sul [installazione TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation) sono reperibili nel [TeamCity 8 documentazione](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) di JetBrains.

- **Preparazione del Server di compilazione** : questo passaggio prevede l'installazione del software necessario, strumenti e i certificati necessari per compilare applicazioni per dispositivi mobili e prepararli per la distribuzione.

- **Creazione di Script di compilazione A** : questo passaggio non è strettamente necessario, ma uno script di compilazione è utile per la creazione di applicazioni automatiche. Usando uno script di compilazione, si consentirà alla risoluzione dei problemi di compilazione che potrebbero verificarsi e offre un modo coerente e ripetibile per creare i file binari per la distribuzione, anche se l'integrazione continua non è noto.

- **Creazione di un progetto di TeamCity** : dopo aver completati i tre passaggi precedenti, è necessario creare un progetto di TeamCity che conterrà tutti i metadati necessari per recuperare il codice sorgente, compilare i progetti e inviare i test in Xamarin Test Cloud.

## <a name="requirements"></a>Requisiti

Sperimentare [Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud) è obbligatorio.

È necessario avere familiarità con TeamCity 8.1. L'installazione di TeamCity esula dall'ambito di questo documento. Si presuppone che TeamCity è installato in OS X Mavericks e sia in esecuzione in un account utente normale e non l'account radice.

Il server di compilazione deve essere un computer autonomo, che esegue OS X, dedicato per l'integrazione continua. In teoria, il server di compilazione non sarai ritenuto responsabile per eventuali altri ruoli, ad esempio un server di database, server web o workstation di sviluppo.

> [!IMPORTANT]
> Questa Guida non viene illustrata un'installazione di Xamarin "headless".

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparazione del Server di compilazione

Un passaggio fondamentale nella configurazione di un server di compilazione consiste nell'installare tutti gli strumenti necessari, software e i certificati per compilare le applicazioni per dispositivi mobili. È importante che il server di compilazione in grado di compilare la soluzione per dispositivi mobili ed eseguire i test. Per ridurre al minimo i problemi di configurazione, gli strumenti e il software devono essere installati nello stesso account utente che sta ospitando TeamCity. Di seguito è riportato un elenco di cosa è necessaria:

1. **Visual Studio per Mac** – include xamarin. IOS e xamarin. Android.
2. **Account di accesso a Store il componente Xamarin** – in questo passaggio è facoltativo ed è solo necessario se l'applicazione utilizza componenti dall'archivio del componente Xamarin per. In modo proattivo accedendo a questo punto l'archivio componenti impedirà eventuali problemi durante una compilazione TeamCity prova a compilare l'applicazione.
3. **Xcode** – Xcode è necessaria per le applicazioni iOS compilate e accesso.
4. **Strumenti da riga di comando Xcode** – come descritto nel passaggio 1 della sezione installazione del [aggiornamento Ruby con rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) Guida.
5. **I profili di Provisioning e un'identità di firma** : importare i certificati e tramite XCode profilo di provisioning. Vedere la Guida di Apple sul [esportazione di identità di firma e i profili di Provisioning](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) per altri dettagli.
6. **Android Keystores** : copiare il keystores Android necessarie in una directory che l'utente TeamCity ha accesso, vale a dire `~/Documents/keystores/MyAndroidApp1`.
7. **Calabash** – si tratta di un passaggio facoltativo se l'applicazione dispone di test scritti usando Calabash. Vedere la [Calabash installazione in OS X Mavericks](https://developer.xamarin.com/guides/testcloud/calabash/osx-installation/) Guida e il [aggiornamento Ruby con rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) Guida per altre informazioni.

Il diagramma seguente illustra tutti questi componenti:

![](teamcity-images/image1.png "Questo diagramma illustra tutti questi componenti")

Dopo aver installato il software, accedere all'account utente e verificare che tutto il software sia stato installato correttamente e funzioni. Questa deve comportare la compilazione della soluzione e l'invio all'applicazione di Test Cloud. Ciò può essere notevolmente semplificata dall'esecuzione dello script di compilazione, come descritto nella sezione successiva.

## <a name="create-a-build-script"></a>Creare uno Script di compilazione

Anche se è assolutamente possibile per TeamCity gestire tutti gli aspetti della compilazione e invio di applicazioni per dispositivi mobili in Test Cloud da solo, è consigliabile creare uno script di compilazione. Uno script di compilazione offre i vantaggi seguenti:

1. **Documentazione** : uno script di compilazione viene utilizzato come una forma di documentazione su come viene compilato il software. Questa operazione rimuove alcuni "magic" che è associata la distribuzione dell'applicazione e consente agli sviluppatori di concentrarsi sulla funzionalità.
1. **La ripetibilità** : uno script di compilazione garantisce che ogni volta che l'applicazione viene compilata e distribuita, si verifica in esattamente allo stesso modo, indipendentemente da chi o cosa esegue il lavoro. Questa coerenza ripetibile rimuove tutti i problemi o errori umani o gli errori che potrebbero diventare a causa di una compilazione eseguita in modo non corretto.
1. **Controllo delle versioni** : uno script di compilazione può essere incluso nel sistema di controllo di origine. Ciò significa che le modifiche apportate allo script di compilazione possono essere rilevate, monitorate e se vengono trovati gli errori o inesattezze corretta.
1. **Preparare l'ambiente** : uno script di compilazione può includere la logica per installare le dipendenze di terze parti 3rd qualsiasi obbligatorio. Ciò garantisce che le applicazioni vengono compilate con i componenti appropriati.

Lo script di compilazione può essere semplice quanto un file di Powershell (on Windows) o uno script bash (in OS X). Quando si crea lo script di compilazione, esistono diverse opzioni per i linguaggi di script:

- [**Rake** ](https://github.com/jimweirich/rake) – si tratta di un linguaggio specifico di dominio (DSL) per la compilazione di progetti, in base Ruby. Rake ha il vantaggio di diffusione e un ricco ecosistema di librerie.

- [**psake** ](https://github.com/psake/psake) – si tratta di una libreria di Windows Powershell per la creazione di software

- [**SIMULARE** ](http://fsharp.github.io/FAKE/) – si tratta di un linguaggio DSL basati in F# che rende possibile usare le librerie .NET esistenti, se necessario.

Quale linguaggio di scripting viene usato dipende dai requisiti e preferenze. Il [TaskyPro Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash) contenuto un esempio dell'uso di Rake come una [script di compilazione](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile).

> [!NOTE]
> È possibile usare un sistema di compilazione basato su XML, ad esempio MSBuild o NAnt, ma questi mancanza stesso l'espressività e la gestibilità di un linguaggio DSL dedicato alla creazione di software.

### <a name="parameterizing-the-build-script"></a>Definizione di parametri per lo Script di compilazione

Il processo di compilazione e test del software richiede le informazioni che devono essere mantenute segrete. In particolare la creazione di un file APK può richiedere una password per l'archivio chiavi e/o l'alias chiave nell'archivio chiavi. Analogamente, Test Cloud richiede una chiave API che è univoca a uno sviluppatore. Questi tipi di valori non devono essere difficile codificati nello script di compilazione. Devono invece essere passati come variabili dello script di compilazione.

Meno sensibili sono valori, ad esempio iOS device ID o l'ID del dispositivo Android che identificano quali Test Cloud deve usare per i dispositivi di test viene eseguito. Non sono valori che devono essere protetti, ma possono variare da una build a altra.

Archiviazione di questi tipi di variabili all'esterno dello script di build inoltre rende più semplice condividere lo script di compilazione di un'organizzazione, con gli sviluppatori, ad esempio. Gli sviluppatori possono usare lo stesso script esattamente come server di compilazione, ma è possono usare i propri keystores e le chiavi API.

Sono disponibili due opzioni possibili per l'archiviazione di questi valori sensibili:

- **Un file di configurazione** : consente di proteggere la chiave API del Test Cloud, questo valore non deve essere selezionato nel controllo della versione. Il file può essere creato per ogni macchina. Modo in cui i valori vengono letti da questo file dipende il linguaggio di scripting usato.

- **Le variabili di ambiente** : possono essere impostate facilmente in base al computer e ordi condivisi, indipendentemente dal linguaggio di scripting di sottostante.

Esistono vantaggi e svantaggi per ognuna di queste opzioni. TeamCity funziona perfettamente con le variabili di ambiente, quindi consiglia questa tecnica durante la creazione di script di compilazione in questa Guida.

### <a name="build-steps"></a>Istruzioni di compilazione

Lo script di compilazione deve essere in grado di eseguire la procedura seguente:

- **Compilare l'applicazione** – ciò include la firma dell'applicazione con il profilo di provisioning corretto.

- **Inviare l'applicazione per Xamarin Test Cloud** – ciò include la firma e zip allineando il file APK con l'archivio chiavi appropriata.

Questi due passaggi verranno spiegati in dettaglio più avanti.

#### <a name="compiling-a-xamarinios-application"></a>La compilazione di un'applicazione xamarin. IOS

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>La compilazione di un'applicazione xamarin. Android

Per compilare un'applicazione Android, usare **xbuild** (o **msbuild** su Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

Si noti che per compilare l'applicazione Xamarin. Android **xbuild** Usa il progetto e che per compilare l'applicazione iOS **xbuild** richiede che la soluzione.

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>Invio di UITest in Test Cloud

Test dell'interfaccia utente vengono inviate usando il `test-cloud.exe` applicazione, come illustrato nel frammento di codice seguente:

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

Quando viene eseguito il test, restituirà i risultati del test sotto forma di un file XML di stile NUnit denominato **XML**. TeamCity verranno visualizzate le informazioni nel Log di compilazione.

Per altre informazioni su come inviare test dell'interfaccia utente di Test Cloud, consultare questa Guida sul [uitests preparazione per il caricamento](/appcenter/test-cloud/preparing-for-upload/uitest/).

#### <a name="submitting-calabash-tests-to-test-cloud"></a>Invio di Calabash test per Test Cloud

I test di Calabash vengono inviati usando il `test-cloud` gem, come illustrato nel frammento di codice seguente:

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```
Per inviare un'applicazione Android in Test Cloud, è necessario prima di tutto ricompila il server di prova di file APK usando calabash-android:

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```
Per altre informazioni sull'invio di test di Calabash, consultare la Guida di Xamarin sul [invio Calabash test in Test Cloud](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/).

## <a name="creating-a-teamcity-project"></a>Creazione di un progetto di TeamCity

Una volta TeamCity è installato e Visual Studio per Mac può compilare il progetto, è possibile creare un progetto in TeamCity per compilare il progetto e inviarlo in Test Cloud.

1. Avviato eseguendo l'accesso TeamCity tramite il web browser. Passare al progetto di primo livello:

    ![Passare al progetto di primo livello](teamcity-images/image2.png "passare al progetto di primo livello") sotto il progetto di primo livello, creare un nuovo progetto secondarie:

    ![Passare al progetto di primo livello progetto visualizzati sotto la radice, crea un nuovo progetto secondarie](teamcity-images/image3.png "passare al progetto di primo livello progetto visualizzati sotto la radice, crea un nuovo progetto secondarie")
2. Dopo aver creato il sottoprogetto, aggiungere una nuova configurazione di compilazione:

    ![Dopo aver creato il sottoprogetto, aggiungere una nuova configurazione di compilazione](teamcity-images/image5.png "dopo aver creato il sottoprogetto, aggiungere una nuova configurazione di compilazione")
3. Collegare un progetto del VCS per la configurazione di compilazione. Questa operazione viene eseguita tramite la schermata di impostazione di controllo di versione:

    ![Questa operazione viene eseguita tramite la schermata di impostazione controllo della versione](teamcity-images/image6.png "questa operazione viene eseguita tramite la schermata di impostazione controllo della versione")

    Se è presente alcun progetto del VCS creato, è possibile crearne uno dalla pagina nuova radice VCS illustrata di seguito:

    ![Se è presente alcun progetto del VCS creato, è possibile crearne uno dalla pagina nuova radice VCS](teamcity-images/image7.png "se è presente alcun progetto del VCS creato, è possibile crearne uno dalla pagina nuova radice VCS")

    Dopo aver associata la radice VCS, TeamCity will estrazione del progetto e provare a auto rilevare istruzioni di compilazione. Se si ha familiarità con TeamCity, è possibile selezionare una delle istruzioni di compilazione rilevati. È possibile ignorare i passaggi di compilazione rilevati per il momento.

4. Successivamente, configurare un Trigger di compilazione. Si accodare una compilazione quando vengono soddisfatte determinate condizioni, ad esempio quando un utente esegue il commit di codice nel repository. Lo screenshot seguente illustra come aggiungere un trigger di compilazione:

    ![Questo screenshot Mostra come aggiungere un trigger di compilazione](teamcity-images/image8.png "questo screenshot Mostra come aggiungere un trigger di compilazione") un esempio di configurazione di un trigger di compilazione può essere visualizzato nello screenshot seguente:

    ![Un esempio di configurazione di un trigger di compilazione può essere visualizzato in questo screenshot](teamcity-images/image9.png "un esempio di configurazione di un trigger di compilazione può essere visualizzato in questo screenshot")

5. La sezione precedente, lo Script di compilazione, parametrizzazione suggerito archiviare alcuni valori come variabili di ambiente. È possibile aggiungere queste variabili per la configurazione di compilazione tramite la schermata dei parametri. Aggiungere le variabili per la chiave API del Test Cloud, l'ID del dispositivo iOS e l'ID dispositivo Android, come illustrato nello screenshot seguente:

    ![Aggiungere le variabili per la chiave API del Test Cloud, l'ID del dispositivo iOS e l'ID del dispositivo Android](teamcity-images/image11.png "aggiungere le variabili per la chiave API del Test Cloud, l'ID del dispositivo iOS e l'ID del dispositivo Android")

6. Il passaggio finale consiste nell'aggiungere un'istruzione di compilazione che verrà richiamato lo script di compilazione per compilare l'applicazione e accodare l'applicazione in Test Cloud. Lo screenshot seguente è riportato un esempio di un'istruzione di compilazione che usa un Rakefile per compilare un'applicazione:

    ![Questa schermata è riportato un esempio di un'istruzione di compilazione che consente di compilare un'applicazione un Rakefile](teamcity-images/image12.png "questa schermata è riportato un esempio di un'istruzione di compilazione che usa un Rakefile per compilare un'applicazione")

7. A questo punto, la configurazione di compilazione è stata completata. È buona norma attivare una compilazione da selezionare per confermare che il progetto sia configurato correttamente. Un buon metodo per eseguire questa operazione è eseguire il commit di una modifica di piccole dimensioni e non significativa nel repository. TeamCity deve rilevare il commit e avviare una compilazione.

8. Una volta completata la compilazione, esaminare il log di compilazione e verificare se esistono eventuali problemi o avvisi con la compilazione che richiedono attenzione.

## <a name="summary"></a>Riepilogo

Questa guida viene descritto come utilizzare TeamCity per compilare applicazioni per dispositivi mobili Xamarin e inviarle quindi al Test Cloud. Abbiamo parlato di creazione di uno script di compilazione per automatizzare il processo di compilazione. Lo script di compilazione si occupa della compilazione dell'applicazione, inviando in Test Cloud e in attesa di risultati

Quindi è stato illustrato come creare un progetto in TeamCity che accodi una compilazione ogni volta che uno sviluppatore esegue il commit di codice e chiamerà lo script di compilazione.

## <a name="related-links"></a>Collegamenti correlati

- [Preparazione ad uitests caricamento](/appcenter/test-cloud/preparing-for-upload/uitest/)
- [Installazione e configurazione di TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
