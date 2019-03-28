---
title: Uso di Jenkins con Xamarin
description: Questo documento descrive come usare Jenkins per l'integrazione continua con le applicazioni Xamarin. Illustra come installare, configurare e usare Jenkins.
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: lobrien
ms.author: laobri
ms.date: 03/23/2017
ms.openlocfilehash: 2e6a75fa3c4c63e8dea402c6761f8ef753908540
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507201"
---
# <a name="using-jenkins-with-xamarin"></a>Uso di Jenkins con Xamarin

_Questa guida illustra come configurare come server di integrazione continua Jenkins e automatizzare la compilazione di applicazioni per dispositivi mobili create con Xamarin. Viene descritto come installare Jenkins in OS X, configurarlo e configurare i processi per compilare le applicazioni xamarin. IOS e xamarin. Android quando viene eseguito il commit per il sistema di gestione del codice sorgente delle modifiche._

[Introduzione all'integrazione continua con Xamarin](~/tools/ci/intro-to-ci.md) introduce l'integrazione continua come una procedura di sviluppo software utile che fornisce un avviso anticipato del codice danneggiato o non compatibile. CI consente agli sviluppatori di risolvere i problemi e problemi perché si verificano e mantiene il software nello stato appropriato per la distribuzione. Questa procedura dettagliata illustra come usare insieme il contenuto da entrambi i documenti.

Questa guida illustra come installare Jenkins in un computer dedicato che esegue OS X e configurarlo affinché venga eseguito automaticamente quando il computer viene avviato. Dopo aver installato Jenkins, verrà installato plug-in aggiuntivi per supportare la Build MS. Jenkins supporta Git impostazione predefinita. Se TFS è in uso per controllo del codice sorgente, è necessario installare anche un'utilità di plug-in e da riga di comando aggiuntive.

Una volta configurato Jenkins ed eventuali plug-in necessari siano stati installati, verrà creato uno o più processi per compilare i progetti xamarin. Android e xamarin. IOS. Un processo è una raccolta di passaggi e i metadati necessari per eseguire alcune operazioni. Un processo in genere costituito dagli elementi seguenti:

- **Codice di gestione di origine** – si tratta di una voce di metadati nel file di configurazione di Jenkins che contiene informazioni su come connettersi a controllo del codice sorgente e i file da recuperare.
- **I trigger** – i trigger vengono utilizzati per avviare un processo in base a determinate azioni, ad esempio quando uno sviluppatore esegue il commit delle modifiche al repository del codice sorgente.
- **Istruzioni di creazione** – si tratta di un plug-in o uno script che compila il codice sorgente e genera un file binario che può essere installato nei dispositivi mobili.
- **Le azioni di compilazione facoltative** : può trattarsi di esecuzione di unit test, analisi statica sul codice, firma del codice, o l'avvio di un altro processo per eseguire altre attività correlate di compilazione.
- **Le notifiche** – potrebbe inviare un processo di qualche tipo di notifica sullo stato di una compilazione.
- **Sicurezza** : sebbene sia facoltativo, è consigliabile che sia abilitata anche la funzionalità di sicurezza di Jenkins.

Questa guida descrive come configurare un server Jenkins ognuno di questi punti. Al termine di esso, si dovrà una buona conoscenza di come impostare e configurare Jenkins per creare file IPA e un APK per i nostri progetti per dispositivi mobili Xamarin.

## <a name="requirements"></a>Requisiti

Il server di compilazione ideale è un computer autonomo dedicato al solo scopo di compilazione e possibilmente test dell'applicazione. Un computer dedicato garantisce che gli elementi che potrebbero essere necessari per altri ruoli (ad esempio quello di un server web) non contaminare la compilazione. Ad esempio, se il server di compilazione viene utilizzata anche come un server web, il server web potrebbe richiedere una versione in conflitto di alcune librerie comuni. A causa di questo conflitto sul server web potrebbe non funzionare correttamente o Jenkins possono creare le build che non funzionano quando distribuiti agli utenti.

Il server di compilazione per le App per dispositivi mobili Xamarin è configurato molto simile a quello di workstation dello sviluppatore. Ha un account utente in cui Jenkins, Visual Studio per Mac, e verrà installato xamarin. IOS e xamarin. Android. Tutti i certificati, il provisioning di profili e gli archivi delle chiavi di firma del codice deve essere installati anche. *In genere è separato dagli account di developer account utente del server di compilazione: assicurarsi di installare e configurare tutti i software, chiavi e certificati mentre si è connessi con l'account utente server di compilazione.*

Il diagramma seguente illustra tutti questi elementi in un server di compilazione Jenkins tipico:

[![](jenkins-walkthrough-images/image1.png "Questo diagramma illustra tutti questi elementi in un server di compilazione Jenkins tipico")](jenkins-walkthrough-images/image1.png#lightbox)

le applicazioni iOS possono solo essere compilate ed eseguito l'accesso in un computer che eseguono macOS. Una versione minima di Mac è un'opzione a costi ridotti ragionevole, ma qualsiasi computer in grado di eseguire OS X 10.10 (Yosemite) o versioni successive è sufficiente.

Se TFS è in uso per controllo del codice sorgente, è consigliabile installare [Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/). Team Explorer Everywhere offre accesso cross-platform per TFS, il terminale in macOS.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>Installazione di Jenkins

La prima attività all'uso di Jenkins è necessario installarlo. Esistono tre modi per eseguire Jenkins in OS x:

- Come un daemon, in esecuzione in background.
- All'interno di un contenitore di servlet, ad esempio Tomcat o Jetty, JBoss.
- Come un normale processo eseguito con un account utente.

Le applicazioni di integrazione continua più tradizionali eseguite in background, come un daemon (in OS X o \*nix) o come servizio (in Windows). Ciò è adatto per scenari in cui è presente alcun intervento dell'interfaccia utente grafica e in cui il programma di installazione dell'ambiente di compilazione può essere eseguita facilmente. App per dispositivi mobili richiedono anche keystores e firma dei certificati che potrebbero causare problemi per l'accesso quando Jenkins è in esecuzione come servizio daemon. A causa di queste preoccupazioni, questo documento illustra il terzo scenario: esecuzione di Jenkins con un account utente nel server di compilazione.

Jenkins.App è un modo pratico per installare Jenkins. Questo è un wrapper AppleScript che semplifica l'avvio e arresto di un server Jenkins. Invece di eseguire in una shell bash, Jenkins viene eseguito come un'app con l'icona nel Dock, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image2.png "Invece di eseguire in una shell bash, Jenkins viene eseguito come un'app con l'icona nel Dock, come illustrato in questo screenshot")](jenkins-walkthrough-images/image2.png#lightbox)

Avvio e arresto di Jenkins è semplice come avviare o arrestare Jenkins.App.

Per installare Jenkins.App, scaricare la versione più recente dalla pagina di download del progetto illustrata nello screenshot seguente:

[![](jenkins-walkthrough-images/image3.png "App, scaricare la versione più recente dai progetti 2000A, illustrati in questo screenshot")](jenkins-walkthrough-images/image3.png#lightbox)

Estrarre il file zip per il `/Applications` cartella nel server di compilazione e start esattamente come qualsiasi altra applicazione OS X.
La prima volta che si avvia Jenkins.App, presenta una finestra di dialogo che informa che Jenkins verrà scaricato:

[![](jenkins-walkthrough-images/image4.png "App, presenta una finestra di dialogo che informa che viene eseguito il download Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

Una volta Jenkins.App ha terminato il download, verrà visualizzato un'altra finestra di dialogo che chiede se si desidera personalizzare l'avvio di Jenkins, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image5.png "App ha terminato il download, verrà visualizzata un'altra finestra di dialogo che chiede se si desidera personalizzare l'avvio di Jenkins, come illustrato in questo screenshot")](jenkins-walkthrough-images/image5.png#lightbox)

Personalizzazione di Jenkins è facoltativa e non deve essere eseguita ogni volta che l'app viene avviata: le impostazioni predefinite per Jenkins adatte alla maggior parte delle situazioni.

Se è necessario personalizzare Jenkins, fare clic sui **modificare le impostazioni predefinite** pulsante. Questo, verrà visualizzate due finestre di dialogo consecutive: uno che richiede i parametri della riga di comando Java e l'altro in cui viene richiesto per i parametri della riga di comando di Jenkins. I due screenshot seguenti illustrano queste due finestre di dialogo:

[![](jenkins-walkthrough-images/image6.png "Questo screenshot Mostra le finestre di dialogo")](jenkins-walkthrough-images/image6.png#lightbox)

[![](jenkins-walkthrough-images/image7.png "Questo screenshot Mostra le finestre di dialogo")](jenkins-walkthrough-images/image7.png#lightbox)

Quando Jenkins è in esecuzione, è possibile impostarlo come elemento account di accesso in modo che venga avviata ogni volta che l'account di accesso utente nel computer. È possibile farlo facendo clic sull'icona di Jenkins nel Dock e scegliendo **opzioni... > Apri all'accesso**, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image8.png "È possibile farlo facendo clic sull'icona di Jenkins nel Dock e scegliendo OptionsOpen all'accesso, come illustrato in questo screenshot")](jenkins-walkthrough-images/image8.png#lightbox)

In questo modo Jenkins.App avviare automaticamente ogni volta che l'utente esegue l'accesso, ma non quando il computer si avvia. È possibile specificare un account utente che userà OS X per l'accesso automaticamente in fase di avvio. Aprire il **preferenze di sistema**e selezionare il **utenti e gruppi** icona come illustrato in questo screenshot:

[![](jenkins-walkthrough-images/image9.png "Aprire le preferenze di sistema e selezionare l'icona di gruppi di utenti, come illustrato in questo screenshot")](jenkins-walkthrough-images/image9.png#lightbox)

Fare clic sui **opzioni di accesso** pulsante e quindi scegliere l'account che verrà usato OS X per l'accesso in fase di avvio.

A questo punto è stato installato Jenkins. Tuttavia, se si desidera compilare le applicazioni per dispositivi mobili Xamarin, è necessario installare alcuni plug-in.

### <a name="installing-plugins"></a>Installare i plug-in

Quando il programma di installazione Jenkins.App è stata completata, date di inizio Jenkins e avviare il web browser con URL http://localhost:8080, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image10.png "8080, come illustrato in questo screenshot")](jenkins-walkthrough-images/image10.png#lightbox)

In questa pagina, selezionare **Jenkins > Gestisci Jenkins > Manage Plugins** dal menu nell'angolo superiore sinistro, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image11.png "In questa pagina, selezionare Gestisci Jenkins gestire plug-in Jenkins dal menu nell'angolo superiore sinistro")](jenkins-walkthrough-images/image11.png#lightbox)

Verrà visualizzata la **Manager di plug-in Jenkins** pagina. Se si fa clic sulla scheda disponibile, si verrà visualizzato un elenco di plug-in più di 600 che possono essere scaricati e installati. Ciò è illustrato nella schermata seguente:

[![](jenkins-walkthrough-images/image12.png "Se si fa clic sulla scheda disponibile, si verrà visualizzato un elenco di plug-in più di 600 che possono essere scaricati e installati")](jenkins-walkthrough-images/image12.png#lightbox)

Lo scorrimento di tutti i plug-in per trovare che alcune possono rivelarsi noiose e soggetta a errori 600. Jenkins fornisce un campo di ricerca filtro nell'angolo superiore destro dell'interfaccia. Utilizzo di questo campo di filtro per la ricerca semplificherà l'individuazione e installato da uno o tutti i plug-in seguenti:

- **Plug-in Jenkins MSBuild** – questo plug-in consente di compilare Visual Studio e Visual Studio per Mac soluzioni (con estensione sln) e progetti (con estensione csproj).
- **Plug-in Injector dell'ambiente** – si tratta di un facoltativo ma utile plug-in che modo è possibile impostare le variabili di ambiente del processo e a livello di compilazione. Offre anche protezione aggiuntiva per le variabili, ad esempio le password utilizzate per codice firmare l'applicazione. Talvolta abbreviato nel *EnvInject Plugin* .
- **Team Foundation Server Plugin** – si tratta di un plug-in facoltativo che è solo necessario se si usa Team Foundation Server o Team Foundation Services per il controllo del codice sorgente.

Jenkins supporta Git senza eventuali altri plug-in.

Dopo aver installato tutti i plug-in, è opportuno riavviare Jenkins e configurare le impostazioni globali per ogni plug-in. Le impostazioni globali per un plug-in sono reperibili selezionando **Jenkins > Gestisci Jenkins > Configura sistema** nell'angolo in alto a sinistra, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image13.png "Le impostazioni globali per un plug-in sono reperibili selezionando Jenkins / Manage Jenkins / configurazione di sistema in alto a sinistra passare nell'angolo")](jenkins-walkthrough-images/image13.png#lightbox)

Quando si seleziona questa opzione di menu, verrà visualizzata per il **Configure System [Jenkins]** pagina. Questa pagina contiene sezioni per configurare Jenkins per se stesso e impostare alcuni dei valori globali plug-in.  Lo screenshot seguente illustra un esempio di questa pagina:

[![](jenkins-walkthrough-images/image14.png "In questo screenshot Mostra un esempio di questa pagina")](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>Configurare il plug-in MSBuild

Il plug-in di MSBuild deve essere configurato per utilizzare **/Library/Frameworks/Mono.framework/Commands/xbuild** per la compilazione di Visual Studio per i file di soluzione e progetto Mac. Scorrere verso il basso il **Configure System [Jenkins]** pagina finché il **aggiungere MSBuild** pulsante viene visualizzato, come illustrato nello screenshot seguente:

 [![](jenkins-walkthrough-images/image15.png "Scorrere verso il basso la pagina Configura sistema Jenkins fino a quando non viene visualizzato il pulsante Aggiungi MSBuild")](jenkins-walkthrough-images/image15.png#lightbox)

Fare clic su questo pulsante e compilare il **Name** e **percorso** al **MSBuild** campi del modulo che viene visualizzato. Il nome del **MSBuild** deve essere un nome significativo, durante l'installazione di **percorso di MSBuild** deve essere il percorso `xbuild`, che in genere è **/Library./Framework / Mono.framework/Commands/xbuild**. Dopo che si salvano le modifiche facendo clic sul pulsante Applica nella parte inferiore della pagina o il salvataggio, Jenkins saranno in grado di usare `xbuild` per compilare le soluzioni.

#### <a name="configuring-the-tfs-plugin"></a>Configurare il plug-in TFS

In questa sezione è obbligatoria se si prevede di usare TFS per il controllo del codice sorgente.

Affinché una workstation di macOS interagire con un server TFS [Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) deve essere installato nella workstation. Team Explorer Everywhere è un set di strumenti di Microsoft che include un client della riga di comando multipiattaforma per l'accesso a TFS. Team Explorer Everywhere può essere scaricato da Microsoft e installato in tre passaggi:

1. Decomprimere il file di archivio in una directory che è accessibile all'account utente. Ad esempio, è possibile decomprimere il file **~/tee**.
2. Configurare il percorso di sistema o della shell per includere la cartella che contiene i file che sono stati decompressi nel passaggio precedente. Ad esempio,

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. Per verificare che sia installato Team Explorer Everywhere, aprire una finestra del terminale ed eseguire il `tf` comando. Se Team Foundation sia configurato correttamente, si verrà visualizzato l'output seguente nella sessione terminal:

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

Dopo aver installato il client della riga di comando per TFS, Jenkins deve essere configurato con il percorso completo di `tf` client della riga di comando. Scorrere verso il basso il **Configure System [Jenkins]** pagina fino a individuare la sezione di Team Foundation Server, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image17.png "Scorrere verso il basso la pagina Configura sistema Jenkins fino a individuare la sezione di Team Foundation Server")](jenkins-walkthrough-images/image17.png#lightbox)

Immettere il percorso completo per il `tf` comando e fare clic sui **salvare** pulsante.

### <a name="configure-jenkins-security"></a>Configurare la sicurezza di Jenkins

Durante l'installazione iniziale Jenkins sicurezza è disabilitata, pertanto è possibile per tutti gli utenti configurare ed eseguire qualsiasi tipo di processo in modo anonimo. Questa sezione descrive come configurare la sicurezza utilizzando il database utente di Jenkins per configurare l'autenticazione e autorizzazione.

Le impostazioni di sicurezza sono reperibili selezionando **Jenkins > Gestisci Jenkins > Configura sicurezza globale**, come illustrato in questo screenshot:

[![](jenkins-walkthrough-images/image18.png "Le impostazioni di sicurezza sono reperibili selezionando Jenkins / Manage Jenkins / Configura sicurezza globale")](jenkins-walkthrough-images/image18.png#lightbox)

Nel **Configure Global Security** pagina, controllo il **attiva sicurezza** casella di controllo e il **controllo degli accessi** form dovrebbe apparire simile allo screenshot seguente:

[![](jenkins-walkthrough-images/image19.png "Nella pagina Configura sicurezza globale, controllare la sicurezza di abilitare la casella di controllo e il modulo di controllo di accesso dovrebbe apparire simile al seguente")](jenkins-walkthrough-images/image19.png#lightbox)

Attiva/disattiva il pulsante di opzione per **database utente di Jenkins** nel **sezione sicurezza dell'area di autenticazione**e assicurarsi che **consentire agli utenti di effettuare l'iscrizione** viene inoltre verificato, come illustrato di schermata seguente:

[![](jenkins-walkthrough-images/image20.png "Attiva/disattiva il pulsante di opzione per database di un utente di Jenkins nella sezione sicurezza dell'area di autenticazione e assicurarsi di consentire agli utenti di effettuare l'iscrizione viene anche selezionato")](jenkins-walkthrough-images/image20.png#lightbox)

Infine, riavviare Jenkins e creare un nuovo account. Il primo account creato è l'account root e questo account verrà automaticamente alzata di livello a un amministratore. Tornare al **Configure Global Security** pagina e spuntare la **la sicurezza basata sulla matrice** pulsante di opzione. L'account radice deve essere concesso l'accesso completo e l'account anonimo deve avere accesso in sola lettura, come illustrato nello screenshot seguente:

[![](jenkins-walkthrough-images/image21.png "L'account radice deve essere concesso l'accesso completo e l'account anonimo deve avere accesso in sola lettura")](jenkins-walkthrough-images/image21.png#lightbox)

Dopo queste impostazioni vengono salvate e riavvio di Jenkins, sicurezza verrà attivata.

#### <a name="disabling-security"></a>La disattivazione della protezione

In caso di password dimenticata o un blocco a livello di Jenkins, è possibile disabilitare la sicurezza seguendo questa procedura:

1. Arrestare Jenkins. Se si usa Jenkins.app, è possibile farlo facendo clic sull'icona Jenkins.App nel Dock, e selezionando Quit dal menu a comparsa:

    ![Icona dell'app nel Dock e selezionando Esci dal menu a comparsa](jenkins-walkthrough-images/image19.png)

2. Aprire il file **~/.jenkins/config.xml** in un editor di testo.
3. Modificare il valore della `<usesecurity></usesecurity>` elemento dal `true` a `false`.
4. Eliminare il `<authorizationstrategy></authorizationstrategy>` e il `<securityrealm></securityrealm>` elementi dal file.
5. Riavviare Jenkins.

## <a name="setting-up-a-job"></a>Configurazione di un processo

Al livello superiore, Jenkins possono essere gestite tutte le varie attività necessarie per compilare software in un *processo*. Un processo ha anche i metadati associati, che fornisce informazioni relative alla compilazione, ad esempio come ottenere il codice sorgente, frequenza di esecuzione della compilazione, tutte le variabili speciali che sono necessari per la compilazione e come inviare notifiche agli sviluppatori se la compilazione non riesce.

I processi vengono creati selezionando **Jenkins > nuovo processo** dal menu nell'angolo superiore destra, come illustrato nello screenshot seguente:

![](jenkins-walkthrough-images/image22.png "I processi vengono creati, selezionare il nuovo processo di Jenkins dal menu nell'angolo superiore destro")

Verrà visualizzata la **nuovo processo [Jenkins]** pagina. Immettere un nome per il processo e selezionare il **compila un progetto freestyle software** pulsante di opzione. Lo screenshot seguente mostra un esempio di questo oggetto:

![](jenkins-walkthrough-images/image23.png "Immettere un nome per il processo e selezionare la compilazione di un pulsante di opzione di progetto freestyle software")

Scegliere il **OK** pulsante viene visualizzata la pagina di configurazione per il processo. Questo dovrebbe essere simile alla seguente:

![](jenkins-walkthrough-images/image24.png "Questo dovrebbe essere simile a questo screenshot")

Jenkins consente di organizzare i processi in una directory sul disco rigido che si trova nel percorso seguente: **~/.jenkins/jobs/[JOB nome]**

Questa cartella contiene tutti i file e gli elementi specifici per il processo, ad esempio i log, file di configurazione e il codice sorgente che deve essere compilata.

Dopo aver creato il processo iniziale, deve essere configurato con uno o più delle seguenti operazioni:

- È necessario specificare il sistema di gestione del codice sorgente.
- Uno o più *azioni di compilazione* deve essere aggiunto al progetto. Questi sono i passaggi o attività necessarie per compilare l'applicazione.
- Il processo deve essere assegnato uno *trigger di compilazione* : un set di istruzioni che informa sulla frequenza Jenkins per recuperare il codice e compilare il progetto finale.

### <a name="configuring-source-code-control"></a>Configurazione di controllo del codice sorgente

La prima attività viene Jenkins è recuperare il codice sorgente dal sistema di gestione del codice sorgente. Jenkins supporta molti dei più diffusi sistemi di origine codice gestione attualmente disponibili. Questa sezione descrive i due sistemi più diffusi, Git e Team Foundation Server. Ciascuno di questi sistemi di gestione del codice sorgente è descritto più dettagliatamente nelle sezioni seguenti.

#### <a name="using-git-for-source-code-control"></a>Uso di Git per controllo del codice sorgente

Se si usa TFS per controllo del codice sorgente [ignorare](#using-tfs-for-source-code-management) questa sezione e passare alla sezione successiva mediante TFS.

Jenkins supporta Git impostazione predefinita-Nessun plug-in aggiuntivi sono necessari. Per utilizzare Git, fare clic sui **Git** pulsante di opzione e immettere l'URL per il repository Git, come illustrato nello screenshot seguente:

![](jenkins-walkthrough-images/image25.png "Per utilizzare Git, fare clic sul pulsante di opzione Git e immettere l'URL per il repository Git")

Dopo aver salvate le modifiche, configurazione di Git è stata completata.

#### <a name="using-tfs-for-source-code-management"></a>Utilizzo di TFS per la gestione del codice sorgente

In questa sezione si applica solo agli utenti TFS.

Fare clic sui **Team Foundation Server** pulsante di opzione e la sezione di configurazione TFS dovrebbe apparire simile a quello nello screenshot seguente:

![](jenkins-walkthrough-images/image26.png "Fare clic sul pulsante di opzione di Team Foundation Server e dovrà essere visualizzata la sezione di configurazione TFS")

Fornire le informazioni necessarie per TFS. Lo screenshot seguente mostra un esempio del modulo compilato:

![](jenkins-walkthrough-images/image27.png "Questo screenshot Mostra un esempio del form completato")

#### <a name="testing-the-source-code-control-configuration"></a>Test della configurazione di controllo codice sorgente

Dopo aver configurato il controllo del codice sorgente appropriato, fare clic su **salvare** per salvare le modifiche. Si torna alla home page per il processo, che sarà simile alla seguente:

![](jenkins-walkthrough-images/image28.png "Si torna alla home page per il processo, che sarà simile a questo screenshot")

Il modo più semplice per verificare che il controllo del codice sorgente sia configurato correttamente consiste nell'attivare manualmente una compilazione anche se non sono presenti azioni di compilazione specificate. Per avviare manualmente una compilazione, la home page del processo ha un **Build Now** collegamento nel menu a sinistra, come illustrato nello screenshot seguente:

![](jenkins-walkthrough-images/image29.png "Per avviare manualmente una compilazione, la home page del processo ha un collegamento a questo punto compilare nel menu a sinistra")

Dopo aver avviata una compilazione, la finestra di dialogo Cronologia di compilazione viene visualizzato un cerchio blu lampeggiante, un indicatore di stato, il numero di build e l'ora di inizio della compilazione, simile allo screenshot seguente:

![](jenkins-walkthrough-images/image30.png "Dopo aver avviata una compilazione, la finestra di dialogo Cronologia di compilazione viene visualizzato un cerchio blu lampeggia, un indicatore di stato, il numero di build e l'ora di inizio della compilazione")

Se il processo ha esito positivo, verrà visualizzato un cerchio blu. Se il processo non riesce, verrà visualizzato un cerchio rosso.

Per facilitare la risoluzione dei problemi che potrebbero verificarsi durante la compilazione, Jenkins acquisirà tutto l'output di console per il processo. Per visualizzare l'output della console, fare clic sul processo nella **cronologia di compilazione**e quindi scegliere il **Output della Console** collegamento nel menu a sinistra. La schermata seguente mostra le **Output della Console** collegamento, come pure alcuni dell'output da un processo ha esito positivo:

![](jenkins-walkthrough-images/image31.png "Questo screenshot appare il collegamento di Output della Console, nonché parte dell'output da un processo ha esito positivo")

#### <a name="location-of-build-artifacts"></a>Percorso degli artefatti di compilazione

Jenkins recupererà l'intero codice sorgente in una cartella speciale denominata un *dell'area di lavoro*. Questa directory sono disponibili all'interno della cartella nel percorso seguente:

    ```
    ~/.jenkins/jobs/[JOB NAME]/workspace
    ```

Il percorso all'area di lavoro verrà archiviato nella variabile di ambiente denominata `$WORKSPACE`.

È possibile passare alla cartella dell'area di lavoro in Jenkins passando alla pagina di destinazione per un processo, e quindi facendo clic sui **dell'area di lavoro** collegamento nel menu a sinistra. Lo screenshot seguente mostra un esempio dell'area di lavoro per un processo denominato **HelloWorld**:

![](jenkins-walkthrough-images/image32.png "Questo screenshot Mostra un esempio dell'area di lavoro per un processo denominato HelloWorld")

### <a name="build-triggers"></a>Trigger di compilazione

Sono disponibili diverse strategie differenti per l'avvio di build in Jenkins:, detti *trigger di compilazione*. Un trigger di compilazione consente di Jenkins per decidere quando avviare un processo e compilare il progetto. Due dei trigger di compilazione più comuni sono:

- **Creare periodicamente** : questo trigger fa in modo che Jenkins avviare un processo a intervalli specificati, ad esempio ogni due ore o a mezzanotte nei giorni feriali. Verrà avviata la compilazione indipendentemente dal fatto che sono state apportate eventuali modifiche nel repository del codice sorgente.
- **Polling SCM** : questo trigger viene eseguito il polling di controllo del codice sorgente a intervalli regolari. Se tutte le modifiche hanno eseguito il commit al repository del codice sorgente, Jenkins verrà avviata una nuova compilazione.

Polling SCM è un trigger popolare perché fornisce feedback rapido quando uno sviluppatore esegue il commit delle modifiche che causano l'interruzione della compilazione. Ciò è utile per gli avvisi ai team che causa problemi oltre a codice recentemente eseguito il commit e consente gli sviluppatori di risolvere il problema, mentre le modifiche sono ancora aggiornate presente.

Le compilazioni periodiche vengono spesso usate per creare una versione dell'applicazione che può essere distribuita ai tester. Una generazione periodica, ad esempio, può essere pianificata per venerdì sera in modo che i membri del team di controllo di qualità possono testare il lavoro della settimana precedente.

### <a name="compiling-a-xamarinios-applications"></a>La compilazione di un'applicazione xamarin. IOS
Progetti xamarin. IOS possono essere compilati nella riga di comando usando `xbuild` o `msbuild`. Il comando della shell verrà eseguita nel contesto dell'account utente che è in esecuzione Jenkins. È importante che l'account utente abbia accesso al profilo di provisioning, in modo che l'applicazione può essere inserita in modo corretto per la distribuzione. È possibile aggiungere questo comando della shell alla pagina di configurazione del processo.

Scorrere verso il basso il **compilazione** sezione. Scegliere il **Add build step** e selezionare **Execute shell**, come illustrato nello screenshot seguente:

![](jenkins-walkthrough-images/image33.png "Fare clic sul pulsante di passaggio di compilazione Aggiungi e scegliere Execute shell")

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>Compilazione di un progetto xamarin. Android

Compilazione di un progetto xamarin. Android è concettualmente molto simile alla creazione di un progetto xamarin. IOS. Per creare un file APK da un progetto xamarin. Android, Jenkins deve essere configurato per eseguire i due passaggi seguenti:

- Compilare il progetto usando il plug-in di MSBuild
- Allinea il file APK con un archivio chiavi di rilascio valida Sign e zip.

Questi due passaggi verranno trattati più dettagliatamente in due sezioni successive.

### <a name="creating-the-apk"></a>Creazione del file APK

Fare clic sui **Add build step** e selezionare **compila un progetto di Visual Studio o la soluzione usando MSBuild**, come illustrato nello screenshot seguente:

![](jenkins-walkthrough-images/image36.png "Creazione di file APK fare clic sul pulsante di Add build step e Seleziona compilazione un progetto di Visual Studio o una soluzione con MSBuild")

Dopo l'istruzione di compilazione viene aggiunto al progetto, compilare i campi modulo che vengono visualizzati. Lo screenshot seguente è un esempio del form completato:

![](jenkins-walkthrough-images/image37.png "Dopo l'istruzione di compilazione viene aggiunto al progetto, compilare i campi modulo che vengono visualizzati")

Questa istruzione di compilazione eseguirà `xbuild` nella **$WORKSPACE** cartella. Il File di compilazione MSBuild è impostato il **Xamarin.Android.csproj** file. Il **argomenti della riga di comando** specificare una build di rilascio della destinazione **PackageForAndroid**. Il prodotto di questo passaggio sarà un file APK che nel percorso seguente:

    ```
    $WORKSPACE/[PROJECT NAME]/bin/Release
    ```

Lo screenshot seguente illustra un esempio di questo file APK:

![](jenkins-walkthrough-images/image38.png "Questo screenshot Mostra un esempio di questo file APK")

Questo file APK non è pronto per la distribuzione, perché non è stato firmato con un archivio chiavi privato e devono essere allineati con estensione zip.

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>Firma ed eseguito Zipalign per il file APK per versione

Firma ed eseguito zipalign per il file APK sono tecnicamente due attività separate che vengono eseguite da due strumenti da riga di comando separata da Android SDK. Tuttavia, è utile eseguire tali operazioni nell'azione di compilazione. Per altre informazioni sulla firma ed eseguito zipalign per un file APK, vedere la documentazione di Xamarin sulla preparazione di un'applicazione Android per il rilascio.

Entrambi questi comandi richiedono parametri della riga di comando che possono variare da un progetto a progetto. Inoltre, alcuni di questi parametri della riga di comando sono le password che non devono essere visualizzati nell'output della console quando la compilazione è in esecuzione. Alcuni di questi parametri della riga di comando verrà archiviato nelle variabili di ambiente. Nella tabella seguente sono descritte le variabili di ambiente necessarie per la firma e/o zip allineamento:

|Variabile di ambiente|Descrizione|
|--- |--- |
|KEYSTORE_FILE|Si tratta del percorso dell'archivio chiavi per firmare l'APK|
|KEYSTORE_ALIAS|La chiave nell'archivio chiavi che verrà usato per firmare l'APK.|
|INPUT_APK|Il file APK che viene creato da `xbuild`.|
|SIGNED_APK|Il file APK firmato prodotta da `jarsigner`.|
|FINAL_APK|Questo è il file zip allineato APK generato da `zipalign`.|
|STORE_PASS|Si tratta della password che viene usata per accedere al contenuto dell'archivio chiavi per firmare il file.|

Come descritto nella sezione requisiti, è possono impostare queste variabili di ambiente durante la compilazione usando il plug-in EnvInject. Il processo è necessario definire una nuova compilazione passaggio aggiunta sulla base delle variabili di ambiente di inserimento, come illustrato nel prossimo screenshot:

![](jenkins-walkthrough-images/image39.png "Il processo è necessario definire una nuova compilazione passaggio aggiunto sulla base delle variabili di ambiente di inserimento")

Nel **proprietà contenuto** formano campo che verrà visualizzato, vengono aggiunte le variabili di ambiente, uno per ogni riga, nel formato seguente:

    ```
    ENVIRONMENT_VARIABLE_NAME = value
    ```

Lo screenshot seguente mostra le variabili di ambiente necessarie per la firma dell'APK:

![](jenkins-walkthrough-images/image40.png "Questo screenshot Mostra le variabili di ambiente necessarie per la firma dell'APK")

Si noti che alcune delle variabili di ambiente per i file APK si basano sul `WORKSPACE` variabile di ambiente.

La variabile di ambiente finale è la password per accedere al contenuto dell'archivio chiavi: `STORE_PASS`. Le password sono i valori sensibili che devono essere nascosto o omesso nei file di log. Il plug-in EnvInject può essere configurato per proteggere questi valori in modo che non vengono visualizzati nei log.

Immediatamente prima la **compilare** sezione della configurazione del processo è un **ambiente Build** sezione. Quando la **inserire password** è attivata o disattivata la casella di controllo, qualche forma di campi da visualizzare. Questi campi modulo vengono usati per acquisire il nome e il valore della variabile di ambiente. Lo screenshot seguente è riportato un esempio di aggiunta di `STORE_PASS` variabile di ambiente:

![](jenkins-walkthrough-images/image41.png "Questa schermata è un esempio di aggiunta la variabile di ambiente STOREPASS")

Dopo che sono state inizializzate le variabili di ambiente, il passaggio successivo è aggiungere un'istruzione di compilazione per la firma e codice postale allineando il file APK. Immediatamente dopo il passaggio di compilazione per inserire le variabili di ambiente sarà un'altra **Execute shell** generazione di comandi che verrà eseguiti `jarsigner` e `zipalign`. Ogni comando per passare alla precedente riga, come illustrato nel frammento di codice seguente:

    ```
    jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
    zipalign -f -v 4 $SIGNED_APK $FINAL_APK
    ```

Lo screenshot seguente mostra un esempio di come immettere il `jarsigner` e `zipalign` comandi al passaggio:

![](jenkins-walkthrough-images/image42.png "Questo screenshot Mostra un esempio di come immettere i comandi di jarsigner ed eseguire zipalign per il passaggio")

Dopo che tutte le azioni di compilazione sono presenti, è buona norma attivare una compilazione manuale per verificare che tutto funzioni. Se la compilazione non riesce, il **Output della Console** devono essere rivisti per informazioni su ciò che ha causato l'errore di compilazione.

### <a name="submitting-tests-to-test-cloud"></a>Invio di test in Test Cloud

I test automatizzati possono essere inviati in Test Cloud utilizzando comandi della shell. Per altre informazioni sulla configurazione di esecuzione dei Test in Xamarin Test Cloud, vedere questa guida per l'utilizzo [xamarin. UITest](/appcenter/test-cloud/preparing-for-upload/uitest/).

## <a name="summary"></a>Riepilogo

In questa guida abbiamo introdotto Jenkins come server di compilazione in macOS e configurato per la compilazione e preparare le applicazioni per dispositivi mobili Xamarin per il rilascio. Jenkins è installato in un computer macOS con più plug-in per supportare il processo di compilazione. Viene creato e configurato un processo che il pull di codice da TFS o Git e quindi compilare tale codice in un'applicazione pronta di rilascio. Abbiamo anche analizzato due diversi modi per pianificare l'esecuzione dei processi.

## <a name="related-links"></a>Collegamenti correlati

- [Integrazione continua](~/tools/ci/index.md)
- [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/)
