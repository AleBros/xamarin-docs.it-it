---
title: Uso di Jenkins con Xamarin
description: Questo documento descrive come usare Jenkins per l'integrazione continua con le applicazioni Novell. Viene illustrato come installare, configurare e usare Jenkins.
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: lobrien
ms.author: laobri
ms.date: 03/23/2017
ms.openlocfilehash: d44e7232529386b7cb6b3db5fbb8bc4a285972fb
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529128"
---
# <a name="using-jenkins-with-xamarin"></a>Uso di Jenkins con Xamarin

_Questa guida illustra come configurare Jenkins come server di integrazione continua e automatizzare la compilazione di applicazioni per dispositivi mobili create con Novell. Viene descritto come installare Jenkins in OS X, configurarlo e configurare i processi per compilare applicazioni Novell. iOS e Novell. Android quando viene eseguito il commit delle modifiche nel sistema di gestione del codice sorgente._

[Introduzione all'integrazione continua con Novell](~/tools/ci/intro-to-ci.md) introduce l'integrazione continuata come una pratica di sviluppo software utile che fornisce un avviso tempestivo di codice interrotto o incompatibile. CI consente agli sviluppatori di risolvere problemi e problemi non appena si verificano e mantiene il software in uno stato adatto per la distribuzione. Questa procedura dettagliata illustra come usare il contenuto di entrambi i documenti insieme.

Questa guida illustra come installare Jenkins in un computer dedicato che esegue OS X e configurarlo per l'esecuzione automatica all'avvio del computer. Quando Jenkins viene installato, vengono installati altri plug-in per supportare MS Build. Jenkins supporta git da box. Se TFS è usato per il controllo del codice sorgente, è necessario installare anche un plug-in aggiuntivo e le utilità della riga di comando.

Dopo aver configurato Jenkins e aver installato tutti i plug-in necessari, si creeranno uno o più processi per compilare i progetti Novell. Android e Novell. iOS. Un processo è una raccolta di passaggi e metadati necessari per eseguire alcune operazioni. Un processo è in genere costituito dagli elementi seguenti:

- **Gestione del codice sorgente (SCM)** : si tratta di una voce di metadati nei file di configurazione di Jenkins che contiene informazioni su come connettersi al controllo del codice sorgente e sui file da recuperare.
- **Trigger** : i trigger vengono usati per avviare un processo in base a determinate azioni, ad esempio quando uno sviluppatore esegue il commit delle modifiche nel repository del codice sorgente.
- **Istruzioni di compilazione** : si tratta di un plug-in o di uno script che compilerà il codice sorgente e produrrà un file binario che può essere installato nei dispositivi mobili.
- **Azioni di compilazione facoltative** : possono includere l'esecuzione di unit test, l'analisi statica sul codice, la firma di codice o l'avvio di un altro processo per eseguire altre operazioni correlate alla compilazione.
- **Notifiche** : un processo può inviare un tipo di notifica sullo stato di una compilazione.
- **Sicurezza** : Sebbene sia facoltativo, è consigliabile abilitare anche le funzionalità di sicurezza di Jenkins.

Questa guida illustra come configurare un server Jenkins che copre ognuno di questi punti. Al termine, è necessario avere una conoscenza approfondita di come configurare e configurare Jenkins per la creazione di file IPA e APK per i progetti per dispositivi mobili Novell.

## <a name="requirements"></a>Requisiti

Il server di compilazione ideale è un computer autonomo dedicato all'unico scopo di creare ed eventualmente testare l'applicazione. Un computer dedicato garantisce che gli elementi che potrebbero essere necessari per altri ruoli, ad esempio quelli di un server Web, non incontaminano la compilazione. Se, ad esempio, il server di compilazione funge anche da server Web, il server Web potrebbe richiedere una versione in conflitto di una libreria comune. A causa di questo conflitto, il server Web potrebbe non funzionare correttamente oppure Jenkins potrebbe creare compilazioni che non funzionano se distribuite agli utenti.

Il server di compilazione per le app per dispositivi mobili Novell è configurato in modo molto simile alla workstation di uno sviluppatore. Dispone di un account utente in cui verranno installati Jenkins, Visual Studio per Mac e Novell. iOS e Novell. Android. È necessario installare anche tutti i certificati di firma del codice, i profili di provisioning e gli archivi di chiavi. *L'account utente del server di compilazione, in genere, è separato dagli account sviluppatore. Assicurarsi di installare e configurare tutti i software, le chiavi e i certificati mentre si è connessi con l'account utente del server di compilazione.*

Il diagramma seguente illustra tutti questi elementi in un tipico server di compilazione Jenkins:

[![](jenkins-walkthrough-images/image1.png "Questo diagramma illustra tutti questi elementi in un tipico server di compilazione Jenkins")](jenkins-walkthrough-images/image1.png#lightbox)

le applicazioni iOS possono essere compilate e firmate solo in un computer che esegue macOS. Un Mac mini è un'opzione di costo inferiore ragionevole, ma è sufficiente qualsiasi computer in grado di eseguire OS X 10,10 (Yosemite) o versione successiva.

Se TFS è usato per il controllo del codice sorgente, è necessario installare [Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/). Team Explorer Everywhere fornisce l'accesso multipiattaforma a TFS nel terminale in macOS.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>Installazione di Jenkins

La prima attività per l'uso di Jenkins è l'installazione. Sono disponibili tre modi per eseguire Jenkins in OS X:

- Come daemon, in esecuzione in background.
- All'interno di un contenitore servlet, ad esempio Tomcat, Jetty o JBoss.
- Come processo normale in esecuzione con un account utente.

La maggior parte delle applicazioni di integrazione continua tradizionale viene eseguita in background, come daemon (in OS \*X o nix) o come servizio (in Windows). Questo è adatto per scenari in cui non è necessaria alcuna interazione con GUI e in cui è possibile eseguire facilmente la configurazione dell'ambiente di compilazione. Le app per dispositivi mobili richiedono anche chiavi e certificati di firma che potrebbero essere problematiche per accedere quando Jenkins viene eseguito come daemon. A causa di questi problemi, questo documento è incentrato sul terzo scenario, che esegue Jenkins con un account utente nel server di compilazione.

Jenkins. app è un modo pratico per installare Jenkins. Si tratta di un wrapper AppleScript che semplifica l'avvio e l'arresto di un server Jenkins. Anziché eseguire in una shell bash, Jenkins viene eseguito come un'app con icona nel Dock, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image2.png "Anziché eseguire in una shell bash, Jenkins viene eseguito come un'app con icona nel Dock, come illustrato in questa schermata")](jenkins-walkthrough-images/image2.png#lightbox)

L'avvio o l'arresto di Jenkins è semplice come avviare o arrestare Jenkins. app.

Per installare Jenkins. app, scaricare la versione più recente dalla pagina di download del progetto, illustrata nella schermata seguente:

[![](jenkins-walkthrough-images/image3.png "App, scaricare la versione più recente dalla pagina di download dei progetti, illustrata in questa schermata")](jenkins-walkthrough-images/image3.png#lightbox)

Estrarre il file `/Applications` zip nella cartella del server di compilazione e avviarlo esattamente come qualsiasi altra applicazione OS X.
La prima volta che si avvia Jenkins. app, verrà visualizzata una finestra di dialogo che informa che verrà scaricato Jenkins:

[![](jenkins-walkthrough-images/image4.png "App. verrà visualizzata una finestra di dialogo che informa che verrà scaricato Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

Al termine del download, Jenkins. app visualizzerà un'altra finestra di dialogo in cui viene chiesto se si desidera personalizzare l'avvio di Jenkins, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image5.png "Il download dell'app è terminato. verrà visualizzata un'altra finestra di dialogo in cui viene chiesto se si desidera personalizzare l'avvio di Jenkins, come illustrato in questa schermata")](jenkins-walkthrough-images/image5.png#lightbox)

La personalizzazione di Jenkins è facoltativa e non deve essere eseguita a ogni avvio dell'app: le impostazioni predefinite per Jenkins funzioneranno per la maggior parte delle situazioni.

Se è necessario personalizzare Jenkins, fare clic sul pulsante **Cambia impostazioni predefinite** . Questa operazione consentirà di visualizzare due finestre di dialogo consecutive: una che richiede i parametri della riga di comando Java e un'altra che richiede i parametri della riga di comando di Jenkins. Le due schermate seguenti illustrano queste due finestre di dialogo:

[![](jenkins-walkthrough-images/image6.png "Questa schermata mostra le finestre di dialogo")](jenkins-walkthrough-images/image6.png#lightbox)

[![](jenkins-walkthrough-images/image7.png "Questa schermata mostra le finestre di dialogo")](jenkins-walkthrough-images/image7.png#lightbox)

Quando Jenkins è in esecuzione, è consigliabile impostarlo come elemento di accesso in modo che venga avviato ogni volta che l'utente accede al computer. A tale scopo, fare clic con il pulsante destro del mouse sull'icona di Jenkins nel Dock e scegliere **Opzioni... > Aprire all'accesso**, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image8.png "A tale scopo, fare clic con il pulsante destro del mouse sull'icona di Jenkins nel Dock e scegliere OptionsOpen all'accesso, come illustrato nella schermata seguente.")](jenkins-walkthrough-images/image8.png#lightbox)

In questo modo Jenkins. app verrà avviata automaticamente ogni volta che l'utente esegue l'accesso, ma non all'avvio del computer. È possibile specificare un account utente che verrà usato da OS X per l'accesso automatico in fase di avvio. Aprire **Preferenze di sistema**e selezionare l'icona **Users & Groups** , come illustrato in questo screenshot:

[![](jenkins-walkthrough-images/image9.png "Aprire Preferenze di sistema e selezionare l'icona gruppi di utenti, come illustrato in questa schermata")](jenkins-walkthrough-images/image9.png#lightbox)

Fare clic sul pulsante **Opzioni di accesso** , quindi scegliere l'account che verrà usato da OS X per l'accesso in fase di avvio.

A questo punto Jenkins è stato installato. Tuttavia, se si vuole creare applicazioni per dispositivi mobili Novell, è necessario installare alcuni plug-in.

### <a name="installing-plugins"></a>Installazione di plug-in

Quando il programma di installazione di Jenkins. app è stato completato, avvierà Jenkins e avvierà il Web browser con http://localhost:8080 l'URL, come illustrato nella schermata seguente:

[![](jenkins-walkthrough-images/image10.png "8080, come illustrato in questa schermata")](jenkins-walkthrough-images/image10.png#lightbox)

Da questa pagina selezionare **jenkins > gestire jenkins > gestire i plug** -in dal menu nell'angolo in alto a sinistra, come illustrato nella schermata seguente:

[![](jenkins-walkthrough-images/image11.png "Da questa pagina selezionare Jenkins Manage Jenkins Manage plugins dal menu nell'angolo in alto a sinistra")](jenkins-walkthrough-images/image11.png#lightbox)

Verrà visualizzata la pagina di **Gestione plug** -in Jenkins. Se si fa clic sulla scheda disponibile, verrà visualizzato un elenco di oltre 600 plug-in che possono essere scaricati e installati. Questa operazione è illustrata nella schermata seguente:

[![](jenkins-walkthrough-images/image12.png "Se si fa clic sulla scheda disponibile, verrà visualizzato un elenco di oltre 600 plug-in che possono essere scaricati e installati")](jenkins-walkthrough-images/image12.png#lightbox)

Scorrere tutti i plug-in 600 per trovarne alcuni può essere noioso e soggetto a errori. Jenkins fornisce un campo di ricerca del filtro nell'angolo superiore destro dell'interfaccia. L'uso di questo campo filtro per la ricerca semplificherà l'individuazione e l'installazione di uno o tutti i plug-in seguenti:

- **Plug** -in di MSBuild per Jenkins: questo plug-in consente di compilare soluzioni di Visual Studio e Visual Studio per Mac (. sln) e progetti (con estensione csproj).
- **Plug-in dell'iniettore dell'ambiente** : si tratta di un plug-in facoltativo ma utile che rende possibile impostare le variabili di ambiente a livello di processo e di compilazione. Offre inoltre una protezione aggiuntiva per le variabili, ad esempio le password usate per firmare il codice dell'applicazione. Viene talvolta abbreviato come plug-in *EnvInject* .
- **Plug** -in Team Foundation Server: si tratta di un plug-in facoltativo che è necessario solo se si usa Team Foundation Server o Team Foundation Services per il controllo del codice sorgente.

Jenkins supporta git senza plug-in aggiuntivi.

Dopo aver installato tutti i plug-in, è necessario riavviare Jenkins e configurare le impostazioni globali per ogni plug-in. Le impostazioni globali per un plug-in sono disponibili selezionando **jenkins > Manage jenkins > Configure System** dall'angolo superiore sinistro, come illustrato nella schermata seguente:

[![](jenkins-walkthrough-images/image13.png "È possibile trovare le impostazioni globali per un plug-in selezionando Jenkins/Manage Jenkins/Configure System nell'angolo superiore sinistro.")](jenkins-walkthrough-images/image13.png#lightbox)

Quando si seleziona questa opzione di menu, viene eseguita la pagina **Configura sistema [Jenkins]** . Questa pagina contiene le sezioni per configurare Jenkins e per impostare alcuni dei valori di plug-in globali.  La schermata seguente illustra un esempio di questa pagina:

[![](jenkins-walkthrough-images/image14.png "Questo screenshot illustra un esempio di questa pagina")](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>Configurazione del plug-in MSBuild

Il plug-in MSBuild deve essere configurato per usare **/Library/Frameworks/mono.Framework/Commands/xbuild** per compilare Visual Studio per Mac file di soluzione e di progetto. Scorrere la pagina **Configura sistema [Jenkins]** finché non viene visualizzato il pulsante **Aggiungi MSBuild** , come illustrato nella schermata seguente:

 [![](jenkins-walkthrough-images/image15.png "Scorrere la pagina Configura sistema Jenkins finché non viene visualizzato il pulsante Aggiungi MSBuild")](jenkins-walkthrough-images/image15.png#lightbox)

Fare clic su questo pulsante e compilare il **nome** e il **percorso** dei campi **MSBuild** nel modulo visualizzato. Il nome dell'installazione di **MSBuild** dovrebbe essere significativo, mentre il **percorso di MSBuild** dovrebbe essere il percorso di `xbuild`, che è in genere **/Library/Frameworks/mono.Framework/Commands/xbuild**. Dopo aver salvato le modifiche facendo clic sul pulsante Salva o applica nella parte inferiore della pagina, Jenkins sarà in grado di usare `xbuild` per compilare le soluzioni.

#### <a name="configuring-the-tfs-plugin"></a>Configurazione del plug-in TFS

Questa sezione è obbligatoria se si intende usare TFS per il controllo del codice sorgente.

Per consentire a una workstation macOS di interagire con un server TFS, [Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) necessario installare nella workstation. Team Explorer Everywhere è un set di strumenti di Microsoft che include un client della riga di comando multipiattaforma per l'accesso a TFS. Team Explorer Everywhere possono essere scaricati da Microsoft e installati in tre passaggi:

1. Decomprimere il file di archivio in una directory accessibile per l'account utente. Ad esempio, è possibile decomprimere il file in **~/Tee**.
2. Configurare la shell o il percorso di sistema in modo da includere la cartella che contiene i file che sono stati decompressi nel passaggio uno precedente. Ad esempio,

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. Per verificare che Team Explorer Everywhere sia installato, aprire una sessione terminal ed eseguire il `tf` comando. Se TF è configurato correttamente, nella sessione terminal verrà visualizzato l'output seguente:

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

Una volta installato il client della riga di comando per TFS, Jenkins deve essere configurato con il percorso completo `tf` del client della riga di comando. Scorrere la pagina **Configura sistema [Jenkins]** fino a trovare la sezione Team Foundation Server, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image17.png "Scorrere la pagina Configura sistema Jenkins fino a trovare la sezione Team Foundation Server")](jenkins-walkthrough-images/image17.png#lightbox)

Immettere il percorso completo del `tf` comando e fare clic sul pulsante **Salva** .

### <a name="configure-jenkins-security"></a>Configurare la sicurezza di Jenkins

Quando viene installato per la prima volta, Jenkins ha la sicurezza disabilitata, quindi è possibile che tutti gli utenti configurano ed eseguano qualsiasi tipo di processo in modo anonimo. Questa sezione descrive come configurare la sicurezza usando il database utente Jenkins per configurare l'autenticazione e l'autorizzazione.

È possibile trovare le impostazioni di sicurezza selezionando **jenkins > gestire jenkins > configurare la sicurezza globale**, come illustrato in questo screenshot:

[![](jenkins-walkthrough-images/image18.png "È possibile trovare le impostazioni di sicurezza selezionando Jenkins/Gestisci Jenkins/Configura sicurezza globale")](jenkins-walkthrough-images/image18.png#lightbox)

Nella pagina **Configura sicurezza globale** selezionare la casella di controllo **Abilita sicurezza** . verrà visualizzato il modulo **controllo di accesso** , simile allo screenshot seguente:

[![](jenkins-walkthrough-images/image19.png "Nella pagina Configura sicurezza globale selezionare la casella di controllo Abilita sicurezza. verrà visualizzato il modulo controllo di accesso simile a questa schermata")](jenkins-walkthrough-images/image19.png#lightbox)

Abilitare o disabilitare il pulsante di opzione per il **database utente di Jenkins** nella **sezione dell'area di autenticazione della sicurezza**e assicurarsi che sia selezionata anche l'opzione **Consenti agli utenti di iscriversi** , come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image20.png "Consente di selezionare il pulsante di opzione per il database utente di Jenkins nella sezione dell'area di autenticazione della sicurezza e verificare che sia selezionata anche l'opzione Consenti agli utenti di iscriversi")](jenkins-walkthrough-images/image20.png#lightbox)

Infine, riavviare Jenkins e creare un nuovo account. Il primo account creato è l'account radice e questo account verrà automaticamente promosso a amministratore. Tornare alla pagina **Configura sicurezza globale** e selezionare il pulsante di opzione **sicurezza basata su matrici** . All'account radice deve essere concesso l'accesso completo e all'account anonimo deve essere assegnato l'accesso in sola lettura, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image21.png "All'account radice deve essere concesso l'accesso completo e all'account anonimo deve essere assegnato l'accesso in sola lettura")](jenkins-walkthrough-images/image21.png#lightbox)

Una volta salvate queste impostazioni e Jenkins viene riavviato, la sicurezza verrà attivata.

#### <a name="disabling-security"></a>Disabilitazione della sicurezza

In caso di password dimenticata o blocco a livello di Jenkins, è possibile disabilitare la sicurezza attenendosi alla procedura seguente:

1. Arrestare Jenkins. Se si usa Jenkins. app, è possibile farlo facendo clic con il pulsante destro del mouse sull'icona Jenkins. app nel Dock e scegliendo Esci dal menu visualizzato:

    ![Icona dell'app nel Dock e selezionare Esci dal menu visualizzato](jenkins-walkthrough-images/image19.png)

2. Aprire il file **~/.Jenkins/config.XML** in un editor di testo.
3. Modificare il valore dell' `<usesecurity></usesecurity>` elemento da `true` a `false`.
4. Eliminare gli `<authorizationstrategy></authorizationstrategy>` `<securityrealm></securityrealm>` elementi e dal file.
5. Riavviare Jenkins.

## <a name="setting-up-a-job"></a>Configurazione di un processo

Al livello principale, Jenkins organizza tutte le varie attività necessarie per la creazione di software in un *processo*. Un processo dispone anche di metadati associati, che forniscono informazioni sulla compilazione, ad esempio come ottenere il codice sorgente, la frequenza di esecuzione della compilazione, eventuali variabili speciali necessarie per la compilazione e come notificare agli sviluppatori se la compilazione ha esito negativo.

I processi vengono creati selezionando **Jenkins > nuovo processo** dal menu nell'angolo in alto a destra, come illustrato nello screenshot seguente:

![](jenkins-walkthrough-images/image22.png "I processi vengono creati selezionando nuovo processo Jenkins dal menu nell'angolo in alto a destra")

Verrà visualizzata la pagina **nuovo processo [Jenkins]** . Immettere un nome per il processo e selezionare il pulsante di opzione **Compila un progetto software in stile libero** . Lo screenshot seguente mostra un esempio di questo:

![](jenkins-walkthrough-images/image23.png "Immettere un nome per il processo e selezionare il pulsante di opzione Compila un progetto software in stile libero")

Facendo clic sul pulsante **OK** viene visualizzata la pagina di configurazione per il processo. Dovrebbe essere simile allo screenshot seguente:

![](jenkins-walkthrough-images/image24.png "Questa schermata dovrebbe essere simile alla seguente")

Jenkins organizza i processi in una directory sul disco rigido che si trova nel percorso seguente: **~/.Jenkins/Jobs/[nome processo]**

Questa cartella contiene tutti i file e gli elementi specifici del processo, ad esempio i log, i file di configurazione e il codice sorgente che devono essere compilati.

Una volta creato, il processo iniziale deve essere configurato con uno o più degli elementi seguenti:

- È necessario specificare il sistema di gestione del codice sorgente.
- È necessario aggiungere una o più *azioni di compilazione* al progetto. Questi sono i passaggi o le attività necessarie per compilare l'applicazione.
- Al processo deve essere assegnato un *trigger di compilazione* , un set di istruzioni che informa Jenkins con quale frequenza recuperare il codice e compilare il progetto finale.

### <a name="configuring-source-code-control"></a>Configurazione del controllo del codice sorgente

La prima attività che Jenkins esegue è recuperare il codice sorgente dal sistema di gestione del codice sorgente. Jenkins supporta molti dei più diffusi sistemi di gestione del codice sorgente attualmente disponibili. Questa sezione descrive i due sistemi più diffusi, git e Team Foundation Server. Ognuno di questi sistemi di gestione del codice sorgente viene descritto più dettagliatamente nelle sezioni seguenti.

#### <a name="using-git-for-source-code-control"></a>Uso di Git per il controllo del codice sorgente

Se si usa TFS per il controllo del codice sorgente, [ignorare](#using-tfs-for-source-code-management) questa sezione e passare alla sezione successiva con TFS.

Jenkins supporta git senza alcuna necessità di plug-in aggiuntivi. Per usare git, fare clic sul pulsante di opzione **git** e immettere l'URL per il repository git, come illustrato nello screenshot seguente:

![](jenkins-walkthrough-images/image25.png "Per usare git, fare clic sul pulsante di opzione git e immettere l'URL per il repository git")

Una volta salvate le modifiche, la configurazione di Git è stata completata.

#### <a name="using-tfs-for-source-code-management"></a>Uso di TFS per la gestione del codice sorgente

Questa sezione si applica solo agli utenti TFS.

Fare clic sul pulsante di opzione **Team Foundation Server** per visualizzare la sezione di configurazione di TFS, simile alla seguente schermata:

![](jenkins-walkthrough-images/image26.png "Fare clic sul pulsante di opzione Team Foundation Server per visualizzare la sezione di configurazione di TFS")

Fornire le informazioni necessarie per TFS. Lo screenshot seguente mostra un esempio del formato completato:

![](jenkins-walkthrough-images/image27.png "Questa schermata mostra un esempio del modulo completato")

#### <a name="testing-the-source-code-control-configuration"></a>Test della configurazione del controllo del codice sorgente

Una volta configurato il controllo del codice sorgente appropriato, fare clic su **Salva** per salvare le modifiche. Verrà restituito il home page per il processo, che sarà simile allo screenshot seguente:

![](jenkins-walkthrough-images/image28.png "Verrà nuovamente visualizzata la home page per il processo, che sarà simile a questa schermata")

Il modo più semplice per verificare che il controllo del codice sorgente sia configurato correttamente consiste nell'attivare manualmente una compilazione, anche se non è stata specificata alcuna azione di compilazione. Per avviare una compilazione manualmente, il home page del processo include un collegamento **Compila ora** nel menu sul lato sinistro, come illustrato nella schermata seguente:

![](jenkins-walkthrough-images/image29.png "Per avviare una compilazione manualmente, il home page del processo include un collegamento Compila ora nel menu sul lato sinistro")

Quando una compilazione è stata avviata, la finestra di dialogo cronologia di compilazione Visualizza un cerchio blu lampeggiante, un indicatore di stato, il numero di build e l'ora di inizio della compilazione, simile allo screenshot seguente:

![](jenkins-walkthrough-images/image30.png "Quando una compilazione è stata avviata, la finestra di dialogo cronologia di compilazione Visualizza un cerchio blu lampeggiante, un indicatore di stato, il numero di build e l'ora di inizio della compilazione")

Se il processo ha esito positivo, verrà visualizzato un cerchio blu. Se il processo ha esito negativo, verrà visualizzato un cerchio rosso.

Per semplificare la risoluzione dei problemi che potrebbero verificarsi durante la compilazione, Jenkins acquisisce tutto l'output della console per il processo. Per visualizzare l'output della console, fare clic sul processo in **cronologia di compilazione**e quindi sul collegamento di output della **console** nel menu a sinistra. La schermata seguente mostra il collegamento di **output della console** , oltre ad alcuni output di un processo riuscito:

![](jenkins-walkthrough-images/image31.png "Questa schermata mostra il collegamento di output della console, oltre ad alcuni output di un processo riuscito")

#### <a name="location-of-build-artifacts"></a>Posizione degli artefatti di compilazione

Jenkins recupererà l'intero codice sorgente in una cartella speciale denominata *area di lavoro*. Questa directory si trova all'interno della cartella nel percorso seguente:

```
~/.jenkins/jobs/[JOB NAME]/workspace
```

Il percorso dell'area di lavoro verrà archiviato in una variabile di ambiente `$WORKSPACE`denominata.

È possibile esplorare la cartella dell'area di lavoro in Jenkins passando alla pagina di destinazione di un processo, quindi facendo clic sul collegamento **area** di lavoro nel menu a sinistra. Lo screenshot seguente mostra un esempio di area di lavoro per un processo denominato **HelloWorld**:

![](jenkins-walkthrough-images/image32.png "Questa schermata mostra un esempio di area di lavoro per un processo denominato HelloWorld")

### <a name="build-triggers"></a>Trigger di compilazione

Sono disponibili diverse strategie per avviare le compilazioni in Jenkins, note come *trigger di compilazione*. Un trigger di compilazione consente a Jenkins di decidere quando avviare un processo e compilare il progetto. Due dei trigger di compilazione più comuni sono:

- **Compilazione periodicamente** : questo trigger fa in modo che Jenkins avvii un processo a intervalli specificati, ad esempio ogni due ore o a mezzanotte nei giorni feriali. La compilazione viene avviata indipendentemente dal fatto che siano state apportate modifiche nel repository del codice sorgente.
- **Poll SCM** : questo trigger eseguirà il polling del controllo del codice sorgente a intervalli regolari. Se è stato eseguito il commit delle modifiche nel repository del codice sorgente, Jenkins avvierà una nuova compilazione.

Il polling SCM è un trigger comune perché fornisce un rapido feedback quando uno sviluppatore esegue il commit delle modifiche che causano l'interruzioni della compilazione. Questa operazione è utile per i team che inviano avvisi a causa di problemi nel codice recentemente sottoposta a commit e consente agli sviluppatori di risolvere il problema, mentre le modifiche sono ancora state aggiornate.

Le compilazioni periodiche vengono spesso usate per creare una versione dell'applicazione che può essere distribuita ai tester. Una compilazione periodica, ad esempio, potrebbe essere pianificata per il venerdì sera, in modo che i membri del team di QA possano testare il lavoro della settimana precedente.

### <a name="compiling-a-xamarinios-applications"></a>Compilazione di applicazioni Novell. iOS
I progetti Novell. iOS possono essere compilati dalla riga `xbuild` di `msbuild`comando usando o. Il comando della shell verrà eseguito nel contesto dell'account utente che esegue Jenkins. È importante che l'account utente abbia accesso al profilo di provisioning in modo che l'applicazione possa essere assemblata correttamente per la distribuzione. È possibile aggiungere questo comando della shell alla pagina di configurazione del processo.

Scorrere verso il basso fino alla sezione **Build** . Fare clic sul pulsante **Aggiungi istruzione di compilazione** e selezionare **Esegui Shell**, come illustrato nello screenshot seguente:

![](jenkins-walkthrough-images/image33.png "Fare clic sul pulsante Aggiungi istruzione di compilazione e selezionare Esegui Shell")

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>Creazione di un progetto Novell. Android

La creazione di un progetto Novell. Android è un concetto molto simile alla creazione di un progetto Novell. iOS. Per creare un file APK da un progetto Novell. Android, è necessario configurare Jenkins per eseguire i due passaggi seguenti:

- Compilare il progetto usando il plug-in MSBuild
- Firmare e zip allineare l'APK con un keystore di versione valido.

Questi due passaggi verranno analizzati più dettagliatamente nelle due sezioni successive.

### <a name="creating-the-apk"></a>Creazione dell'APK

Fare clic sul pulsante **Aggiungi istruzione di compilazione** e selezionare **Compila un progetto o una soluzione Visual Studio usando MSBuild**, come illustrato nella schermata seguente:

![](jenkins-walkthrough-images/image36.png "Creazione del file APK fare clic sul pulsante Aggiungi istruzione di compilazione e selezionare Compila un progetto o una soluzione Visual Studio con MSBuild")

Una volta aggiunta l'istruzione di compilazione al progetto, compilare i campi del modulo visualizzati. Lo screenshot seguente è un esempio del formato completato:

![](jenkins-walkthrough-images/image37.png "Una volta aggiunta l'istruzione di compilazione al progetto, compilare i campi del modulo visualizzati")

Questa istruzione di compilazione verrà `xbuild` eseguita nella cartella **$Workspace** . Il file di compilazione MSBuild è impostato sul file **Novell. Android. csproj** . Gli **argomenti della riga di comando** specificano una build di rilascio della **PackageForAndroid**di destinazione. Il prodotto di questo passaggio sarà un file APK che si trova nel percorso seguente:

```
$WORKSPACE/[PROJECT NAME]/bin/Release
```

Lo screenshot seguente mostra un esempio di questo APK:

![](jenkins-walkthrough-images/image38.png "Questo screenshot mostra un esempio di questo APK")

Questo APK non è pronto per la distribuzione, perché non è stato firmato con un keystore privato ed è necessario che sia allineato con zip.

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>Firma e Zipaligning dell'APK per la versione

La firma e il zipaligning dell'APK sono tecnicamente due attività separate eseguite da due strumenti della riga di comando distinti dal Android SDK. Tuttavia, è consigliabile eseguirli in un'unica azione di compilazione. Per ulteriori informazioni sulla firma e il zipaligning di un APK, vedere la documentazione di Novell relativa alla preparazione di un'applicazione Android per la versione.

Entrambi i comandi richiedono parametri della riga di comando che possono variare da progetto a progetto. Inoltre, alcuni di questi parametri della riga di comando sono password che non devono essere visualizzate nell'output della console quando la compilazione è in esecuzione. Alcuni di questi parametri della riga di comando verranno archiviati nelle variabili di ambiente. Le variabili di ambiente necessarie per la firma e/o l'allineamento zip sono descritte nella tabella seguente:

|Variabile di ambiente|Descrizione|
|--- |--- |
|KEYSTORE_FILE|Questo è il percorso dell'archivio chiavi per la firma dell'APK|
|KEYSTORE_ALIAS|Chiave nell'archivio chiavi che verrà usata per firmare l'APK.|
|INPUT_APK|APK creato da `xbuild`.|
|SIGNED_APK|APK firmato prodotto da `jarsigner`.|
|FINAL_APK|Si tratta dell'APK allineato a zip prodotto `zipalign`da.|
|STORE_PASS|Si tratta della password usata per accedere al contenuto dell'archivio chiavi per il canto del file.|

Come descritto nella sezione requisiti, è possibile impostare queste variabili di ambiente durante la compilazione usando il plug-in EnvInject. Al processo dovrebbe essere aggiunta una nuova istruzione di compilazione in base alle variabili di ambiente Inject, come illustrato nello screenshot seguente:

![](jenkins-walkthrough-images/image39.png "Al processo deve essere aggiunta una nuova istruzione di compilazione in base alle variabili di ambiente Inject")

Nel campo modulo **contenuto proprietà** che verrà visualizzato, vengono aggiunte le variabili di ambiente, una per riga, nel formato seguente:

```
ENVIRONMENT_VARIABLE_NAME = value
```

Lo screenshot seguente mostra le variabili di ambiente necessarie per la firma dell'APK:

![](jenkins-walkthrough-images/image40.png "Questo screenshot mostra le variabili di ambiente necessarie per la firma dell'APK")

Si noti che alcune delle variabili di ambiente per i file apk sono compilate sulla `WORKSPACE` variabile di ambiente.

La variabile di ambiente finale è la password per accedere al contenuto dell'archivio chiavi: `STORE_PASS`. Le password sono valori sensibili che devono essere nascosti o omessi nei file di log. Il plug-in EnvInject può essere configurato in modo da proteggere questi valori in modo che non vengano visualizzati nei log.

Immediatamente prima della sezione **Build** della configurazione del processo è una sezione **dell'ambiente di compilazione** . Quando si seleziona la casella di controllo Inserisci **password** , vengono visualizzati alcuni campi del modulo. Questi campi del modulo vengono usati per acquisire il nome e il valore della variabile di ambiente. Lo screenshot seguente è un esempio di aggiunta della `STORE_PASS` variabile di ambiente:

![](jenkins-walkthrough-images/image41.png "Questa schermata è un esempio di aggiunta della variabile di ambiente STOREPASS")

Una volta che le variabili di ambiente sono state inizializzate, il passaggio successivo consiste nell'aggiungere un'istruzione di compilazione per la firma e il file zip che allinea l'APK. Subito dopo l'istruzione di compilazione per l'inserimento delle variabili di ambiente sarà un'altra compilazione del comando **Execute Shell** che eseguirà `jarsigner` e `zipalign`. Ogni comando prenderà una riga, come illustrato nel frammento di codice seguente:

```
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
zipalign -f -v 4 $SIGNED_APK $FINAL_APK
```

Lo screenshot seguente mostra un esempio di come immettere i `jarsigner` comandi e `zipalign` nel passaggio:

![](jenkins-walkthrough-images/image42.png "Questa schermata mostra un esempio di come immettere i comandi jarsigner e Zipalign nel passaggio")

Una volta apportate tutte le azioni di compilazione, è consigliabile attivare una compilazione manuale per verificare che tutto funzioni correttamente. Se la compilazione ha esito negativo, è necessario esaminare l' **output della console** per ottenere informazioni su ciò che ha causato la mancata riuscita della compilazione.

### <a name="submitting-tests-to-test-cloud"></a>Invio di test a Test Cloud

I test automatizzati possono essere inviati a Test Cloud usando i comandi della shell. Per altre informazioni sulla configurazione di un'esecuzione dei test in Xamarin Test Cloud, vedere questa guida per l'uso di [Novell. UITest](/appcenter/test-cloud/preparing-for-upload/uitest/).

## <a name="summary"></a>Riepilogo

In questa guida è stato introdotto Jenkins come server di compilazione in macOS e configurato per compilare e preparare le applicazioni per dispositivi mobili Novell per la versione. Jenkins è stato installato in un computer macOS insieme a diversi plug-in per supportare il processo di compilazione. È stato creato e configurato un processo che effettuerà il pull del codice da TFS o Git e quindi compilerà tale codice in un'applicazione per la versione pronta. Sono stati anche esaminati due modi diversi per pianificare l'esecuzione dei processi.

## <a name="related-links"></a>Collegamenti correlati

- [Integrazione continua](~/tools/ci/index.md)
- [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/)
