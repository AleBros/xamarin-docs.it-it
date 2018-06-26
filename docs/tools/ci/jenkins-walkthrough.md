---
title: Utilizzo di Jenkins con Xamarin
description: Questo documento viene descritto come utilizzare Jenkins per l'integrazione continua con le applicazioni di Xamarin. Illustra come installare, configurare e utilizzare Jenkins.
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: topgenorth
ms.author: toopge
ms.date: 03/23/2017
ms.openlocfilehash: 8094f1ab78252e6d6bd8f5991bcb567b36ed1e9b
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935345"
---
# <a name="using-jenkins-with-xamarin"></a>Utilizzo di Jenkins con Xamarin

_Questa guida viene illustrato come configurare come server di integrazione continuata Jenkins e automatizzare la compilazione di applicazioni per dispositivi mobili create con Xamarin. Viene descritto come installare Jenkins su OS X, configurarlo e configurare i processi per compilare le applicazioni di xamarin. IOS e xamarin. Android quando le modifiche vengano eseguito il commit per il sistema di gestione del codice sorgente._

[Introduzione all'integrazione continua con Xamarin](~/tools/ci/intro-to-ci.md) introduce integrazione continua come una procedura utile di sviluppo che avvisa tempestivamente del codice interrotto o incompatibile. CI consente agli sviluppatori di risolvere i problemi e problemi in quanto si verificano e mantiene il software in uno stato appropriato per la distribuzione. Questa procedura dettagliata viene illustrato come utilizzare insieme il contenuto da entrambi i documenti.

Questa guida viene illustrato come installare Jenkins in un computer dedicato che esegue OS X e configurarlo per l'esecuzione automatica all'avvio il computer. Una volta Jenkins è installato, verrà installato plug-in aggiuntivi per supportare la Build MS. Jenkins supporta Git impostazione predefinita. Se TFS è utilizzato per controllo del codice sorgente, è necessario installare anche un'utilità di plug-in e riga di comando aggiuntive.

Una volta Jenkins è configurato e sono stati installati tutti i plug-in necessario, verrà creato uno o più processi per compilare i progetti di xamarin. Android e xamarin. IOS. Un processo è un insieme di passaggi e i metadati necessari per eseguire alcune operazioni. Un processo in genere costituita dai seguenti elementi:

-  **Codice di gestione di servizi (SCM) di origine** – si tratta di una voce di metadati nei file di configurazione Jenkins che contiene informazioni su come connettersi a controllo del codice sorgente e i file da recuperare.
-  **I trigger** – i trigger vengono utilizzati per avviare un processo in base a determinate azioni, ad esempio quando uno sviluppatore apporta le modifiche nel repository di codice sorgente.
-  **Istruzioni di compilazione** – si tratta di un plug-in o uno script che verrà compilato il codice sorgente e produrre un file binario che può essere installato nei dispositivi mobili.
-  **Le azioni di compilazione facoltative** : potrebbe trattarsi di esecuzione di unit test, l'esecuzione di analisi statica del codice, di firma codice, o avvio di un altro processo per eseguire altre generazione lavoro correlato.
-  **Le notifiche** : un processo può inviare un tipo di notifica sullo stato di una compilazione.
-  **Sicurezza** : anche se è facoltativo, è consigliabile che sia attivata anche le funzionalità di sicurezza Jenkins.


Questa guida viene illustrato come configurare un server Jenkins relativi a ognuno di questi punti. Entro la fine di esso, è esame del problema dovrebbe avere una buona conoscenza di come impostare e configurare Jenkins per creare file IPA e del file APK per ai progetti per dispositivi mobili con Xamarin.

## <a name="requirements"></a>Requisiti

Il server di compilazione ideale è un computer autonomo dedicato al solo scopo di compilazione e possibilmente test dell'applicazione. Un computer dedicato assicura che gli elementi che potrebbero essere necessari per altri ruoli (ad esempio quello di un server web) non danneggiare la compilazione. Ad esempio, se il server di compilazione viene utilizzata anche come un server web, il server web potrebbe richiedere una versione di alcuni libreria comune in conflitto. A causa di questo conflitto il server web potrebbe non funzionare correttamente o Jenkins potrebbe creare compilazioni che non funzionano quando rivolte agli utenti.

Il server di compilazione Xamarin App per dispositivi mobili viene configurato molto simile a quello workstation dello sviluppatore. Ha un account utente in cui Jenkins, Visual Studio per Mac, e verrà installato xamarin. IOS e xamarin. Android. Tutto il codice dei certificati, il provisioning di profili e gli archivi delle chiavi di firma deve essere installato anche. *In genere account utente del server di compilazione è separati dagli account sviluppatore, assicurarsi di installare e configurare tutte le applicazioni, le chiavi e certificati durante l'accesso con l'account utente di server di compilazione.*

Il diagramma seguente illustra tutti questi elementi in un server di compilazione Jenkins tipico:

 [![](jenkins-walkthrough-images/image1.png "Questo diagramma vengono illustrati tutti questi elementi in un server di compilazione Jenkins tipico")](jenkins-walkthrough-images/image1.png#lightbox)

le applicazioni iOS possono solo essere compilate e firmate in un computer che eseguono Mac OS X. Un Mac Mini è un'opzione basso costo ragionevole, ma qualsiasi computer in grado di eseguire OS X 10.10 (Yosemite) o versione successiva è sufficiente.

Se TFS è utilizzato per controllo del codice sorgente, è opportuno installare [Team Explorer Everywhere](http://www.microsoft.com/en-ca/download/details.aspx?id=40785), resi disponibili da Microsoft. Team Explorer Everywhere fornisce l'accesso multipiattaforma in TFS nei servizi Terminal in OS X.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>Installazione di Jenkins

La prima attività all'utilizzo di Jenkins è per installarlo. Esistono tre modi per eseguire Jenkins in OS x:

-  Come daemon in esecuzione in background.
-  All'interno di un contenitore servlet, ad esempio Tomcat, Jetty o JBoss.
-  Come un normale processo eseguito con un account utente.


Le applicazioni di integrazione continua più tradizionali eseguite in background, come un daemon (su OS X o \*nix) oppure come servizio (in Windows). Ciò è adatta agli scenari in cui è presente alcun intervento dell'interfaccia utente grafica e in cui il programma di installazione dell'ambiente di compilazione può essere eseguita facilmente. Le App per dispositivi mobili richiedono keystores e firma dei certificati che potrebbero essere problematici per l'accesso quando viene eseguito come daemon Jenkins. A causa di questi problemi, in questo documento illustra il terzo scenario – eseguita Jenkins con un account utente nel server di compilazione.

Jenkins.App è un modo pratico per installare Jenkins. Questo è un wrapper AppleScript che semplifica l'avvio e arresto di un server Jenkins. Invece di eseguire una shell bash, Jenkins viene eseguito come un'app con icona di ancoraggio, come illustrato nella schermata seguente:

 [![](jenkins-walkthrough-images/image2.png "Invece di eseguire una shell bash, Jenkins viene eseguito come un'app con icona di ancoraggio, come illustrato in questa schermata")](jenkins-walkthrough-images/image2.png#lightbox)

Avvio e arresto di Jenkins è semplice come l'avvio o arresto Jenkins.App.

Per installare Jenkins.App, scaricare la versione più recente dalla pagina di download del progetto, illustrata nella schermata riportata di seguito:

 [![](jenkins-walkthrough-images/image3.png "App, scaricare la versione più recente dai progetti di pagina, illustrato in questa schermata di download")](jenkins-walkthrough-images/image3.png#lightbox)

Estrarre il file zip per il `/Applications` cartella sul server di compilazione e inizio esattamente come qualsiasi altra applicazione di OS X.
La prima volta che all'avvio di Jenkins.App, presenta una finestra di dialogo che informa che verrà scaricato Jenkins:

 [![](jenkins-walkthrough-images/image4.png "App, presenta una finestra di dialogo che informa che verrà scaricato Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

Una volta Jenkins.App ha completato il download, verrà visualizzato un'altra finestra di dialogo in cui viene richiesto se si desidera personalizzare l'avvio di Jenkins, come illustrato nella schermata seguente:

 [![](jenkins-walkthrough-images/image5.png "App ha completato il download, verrà visualizzata un'altra finestra di dialogo in cui viene richiesto se si desidera personalizzare l'avvio di Jenkins, come illustrato in questa schermata")](jenkins-walkthrough-images/image5.png#lightbox)

Personalizzazione di Jenkins è facoltativa e non deve essere eseguita ogni volta che viene avviata l'app: le impostazioni predefinite di Jenkins funzionerà per la maggior parte dei casi.

Se è necessario personalizzare Jenkins, fare clic sui **modificare le impostazioni predefinite** pulsante. Verrà visualizzata con due finestre di dialogo consecutivi: uno che richiede i parametri della riga di comando Java e l'altro in cui viene richiesto per i parametri della riga di comando Jenkins. Le due schermate seguenti mostrano queste due finestre di dialogo:

 [![](jenkins-walkthrough-images/image6.png "Questa schermata mostra le finestre di dialogo")](jenkins-walkthrough-images/image6.png#lightbox)

 [![](jenkins-walkthrough-images/image7.png "Questa schermata mostra le finestre di dialogo")](jenkins-walkthrough-images/image7.png#lightbox)

Una volta Jenkins è in esecuzione, si desidera impostarlo come elemento di account di accesso in modo che venga avviata ogni volta che l'account di accesso utente nel computer. È possibile farlo facendo clic sull'icona di Jenkins ancoraggio e scegliendo **opzioni... Apri in accesso**, come illustrato nella schermata seguente:

 [![](jenkins-walkthrough-images/image8.png "È possibile farlo facendo clic sull'icona di Jenkins ancoraggio e scegliendo OptionsOpen all'accesso, come illustrato in questa schermata")](jenkins-walkthrough-images/image8.png#lightbox)

Questa operazione comporterà Jenkins.App avviare automaticamente ogni volta che l'utente accede, ma non quando il computer viene avviato. È possibile specificare un account utente che userà OS X per l'accesso automaticamente in fase di avvio. Aprire la **preferenze di sistema**e selezionare il **& gruppi di utenti** icona come illustrato in questo screenshot:

 [![](jenkins-walkthrough-images/image9.png "Aprire le preferenze di sistema e selezionare l'icona di gruppi di utenti come mostrato in questa schermata")](jenkins-walkthrough-images/image9.png#lightbox)

Fare clic sui **opzioni di accesso** e quindi scegliere l'account che verrà utilizzato OS X per account di accesso in fase di avvio.

A questo punto è stato installato Jenkins. Tuttavia, se si desidera compilare applicazioni per dispositivi mobili di Xamarin, si sarà necessario installare alcuni plug-in.


### <a name="installing-plugins"></a>Installazione di plug-in

Quando il programma di installazione Jenkins.App è stata completata, date di inizio Jenkins e avviare il web browser con URL http://localhost:8080, come illustrato nella schermata seguente:

 [![](jenkins-walkthrough-images/image10.png "8080, come illustrato in questa schermata")](jenkins-walkthrough-images/image10.png#lightbox)

In questa pagina, selezionare **Jenkins > Gestisci Jenkins > gestire i plug-in** dal menu nell'angolo superiore sinistro, come illustrato nella schermata seguente:

 [![](jenkins-walkthrough-images/image11.png "In questa pagina, selezionare Jenkins gestire Jenkins gestire i plug-in dal menu nell'angolo superiore sinistro")](jenkins-walkthrough-images/image11.png#lightbox)

Verrà visualizzata la **Jenkins plug-in Gestione** pagina. Se si fa clic sulla scheda disponibile, verrà visualizzato un elenco dei plug-in più di 600 che può essere scaricato e installato. Ciò è illustrato nella schermata riportata di seguito:

 [![](jenkins-walkthrough-images/image12.png "Se si fa clic sulla scheda disponibile, verrà visualizzato un elenco dei plug-in più di 600 che può essere scaricato e installato")](jenkins-walkthrough-images/image12.png#lightbox)

Scorrimento di tutti i plug-in per trovare che alcune possono rivelarsi noiosa e soggetta a errori 600. Jenkins fornisce un campo di ricerca di filtro nell'angolo superiore destro dell'interfaccia. Utilizzo di questo campo di filtro per la ricerca semplificherà l'individuazione e installato da uno o tutti i plug-in seguenti:

-  **Plug-in MSBuild Jenkins** – questo plug-in consente di compilare Visual Studio e Visual Studio per Mac soluzioni (con estensione sln) e progetti (con estensione csproj).
-  **Plug-in Injector dell'ambiente** – si tratta di un facoltativa ma utile plug-in che modo è possibile impostare le variabili di ambiente del processo e un livello superiore. Offre anche protezione aggiuntiva per le variabili, ad esempio le password utilizzate al codice firmare l'applicazione. In alcuni casi è abbreviato come le *EnvInject Plugin* .
-  **Team Foundation Server Plugin** – si tratta di un plug-in facoltativo che è solo necessario se si utilizza Team Foundation Server o servizi di Team Foundation per controllo del codice sorgente.

Jenkins supporta Git senza eventuali altri plug-in.

Dopo aver installato tutti i plug-in, sarà possibile riavviare Jenkins e configurare le impostazioni globali per ciascun plug-in. Le impostazioni globali per un plug-in sono reperibile selezionando **Jenkins > Gestisci Jenkins > Configura sistema** nell'angolo in alto a sinistra, come illustrato nella schermata seguente:

 [![](jenkins-walkthrough-images/image13.png "Le impostazioni globali per un plug-in sono reperibile selezionando Jenkins / gestire Jenkins / Configura sistema dall'angolo superiore sinistro d'angolo")](jenkins-walkthrough-images/image13.png#lightbox)

Quando si seleziona questa opzione, verrà visualizzata per il **configurazione di sistema [Jenkins]** pagina. Questa pagina contiene le sezioni per configurare Jenkins se stesso e impostare alcuni valori globali plug-in.  La schermata riportata di seguito viene illustrato un esempio di questa pagina:

 [![](jenkins-walkthrough-images/image14.png "Questa schermata viene illustrato un esempio di questa pagina")](jenkins-walkthrough-images/image14.png#lightbox)


#### <a name="configuring-the-msbuild-plugin"></a>Configurare il plug-in MSBuild

Il plug-in di MSBuild deve essere configurato per utilizzare **/Library/Frameworks/Mono.framework/Commands/xbuild** per la compilazione di Visual Studio per i file di soluzione e progetto Mac. Scorrere verso il basso il **configurazione di sistema [Jenkins]** pagina finché il **aggiungere MSBuild** pulsante viene visualizzato, come illustrato nella schermata seguente:

 [![](jenkins-walkthrough-images/image15.png "Scorrere verso il basso la pagina di Configura Jenkins sistema fino a quando non viene visualizzato il pulsante Aggiungi MSBuild")](jenkins-walkthrough-images/image15.png#lightbox)

Fare clic su questo pulsante e compilare il **nome** e **percorso** al **MSBuild** campi del modulo che viene visualizzata. Il nome del **MSBuild** deve essere un nome significativo, durante l'installazione di **percorso di MSBuild** deve essere il percorso `xbuild`, che è in genere **/Library/Framework / Mono.framework/Commands/xbuild**. Dopo che è salvare le modifiche, fare clic sul pulsante Applica nella parte inferiore della pagina o salvataggio, Jenkins sarà in grado di utilizzare `xbuild` per compilare le soluzioni.

#### <a name="configuring-the-tfs-plugin"></a>Configurare il plug-in TFS

In questa sezione è obbligatoria se si prevede di usare TFS per il controllo del codice sorgente.

Affinché una workstation OS X interagire con un server TFS, Team Explorer Everywhere deve essere installato nella workstation. Team Explorer Everywhere è un set di strumenti di Microsoft che include un client della riga di comando multipiattaforma per l'accesso a TFS. Team Explorer Everywhere può essere scaricato da Microsoft e installato in tre passaggi:

1. Decomprimere il file di archivio in una directory che è accessibile all'account utente. Ad esempio, si potrebbe decomprimere il file per **~/tee**.
2. Configurare il percorso di sistema o della shell per includere la cartella che contiene i file che sono stati decompressi nel passaggio precedente. Ad esempio,

        echo export PATH~/tee/:$PATH' >> ~/.bash_profile

3. Per confermare che Team Explorer Everywhere sia installato, aprire una sessione terminal ed eseguire il `tf` comando. Se tf è configurata correttamente, verrà visualizzato il seguente output nella sessione terminal:

        $ tf
        Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

        Available commands and their options:

Dopo aver installato il client della riga di comando per TFS, Jenkins deve essere configurato con il percorso completo di `tf` client della riga di comando. Scorrere verso il basso il **configurazione di sistema [Jenkins]** pagina fino a individuare la sezione di Team Foundation Server, come illustrato nella schermata seguente:

 [![](jenkins-walkthrough-images/image17.png "Scorrere verso il basso la pagina di Configura Jenkins sistema fino a individuare la sezione di Team Foundation Server")](jenkins-walkthrough-images/image17.png#lightbox)

Immettere il percorso completo per il `tf` , fare clic su, il **salvare** pulsante.

### <a name="configure-jenkins-security"></a>Configurare la sicurezza di Jenkins

Durante l'installazione iniziale Jenkins la sicurezza è disabilitata, pertanto è possibile per tutti gli utenti configurare ed eseguire qualsiasi tipo di processo in modo anonimo. In questa sezione viene descritto come configurare la sicurezza mediante il database utente di Jenkins per configurare l'autenticazione e autorizzazione.

Le impostazioni di sicurezza sono reperibile selezionando **Jenkins > Gestisci Jenkins > configurare la sicurezza globale**, come illustrato in questo screenshot:

 [![](jenkins-walkthrough-images/image18.png "Le impostazioni di sicurezza sono reperibile selezionando Jenkins / gestire Jenkins / configurare la sicurezza globale")](jenkins-walkthrough-images/image18.png#lightbox)

Nel **configurare la sicurezza globale** pagina, controllare il **Attiva protezione** casella di controllo e il **controllo di accesso** form dovrebbe apparire simile allo screenshot successivo:

 [![](jenkins-walkthrough-images/image19.png "Nella pagina Configura sicurezza globale, controllare la sicurezza di abilitare la casella di controllo e il modulo di controllo di accesso dovrebbe apparire simile a questo screenshot")](jenkins-walkthrough-images/image19.png#lightbox)

Attivare o disattivare il pulsante di opzione per **database utente di Jenkins** nel **sezione sicurezza dell'area di autenticazione**e verificare che **consentire agli utenti di effettuare l'iscrizione** viene inoltre verificato, come illustrato nel cattura di schermata seguente:

 [![](jenkins-walkthrough-images/image20.png "Attivare o disattivare il pulsante di opzione per database di un utente di Jenkins nella sezione sicurezza dell'area di autenticazione e assicurarsi che viene inoltre verificato se consentire agli utenti di effettuare l'iscrizione")](jenkins-walkthrough-images/image20.png#lightbox)

Infine, riavviare Jenkins e creare un nuovo account. Il primo account creato è l'account radice e questo account verrà automaticamente alzata di livello a un amministratore. Tornare ai **configurare la sicurezza globale** pagina e spuntare il **sicurezza basata su una matrice** pulsante di opzione. L'account root deve essere concesso l'accesso completo e l'account anonimo deve avere accesso in lettura, come illustrato nella schermata seguente:

 [![](jenkins-walkthrough-images/image21.png "L'account root deve essere concesso l'accesso completo e l'account anonimo deve avere accesso in lettura")](jenkins-walkthrough-images/image21.png#lightbox)

Dopo che queste impostazioni vengono salvate e Jenkins viene riavviato, sicurezza verrà attivata.


#### <a name="disabling-security"></a>La disabilitazione della sicurezza

In caso di una password dimenticata o blocco di Jenkins a livello, è possibile disabilitare la sicurezza seguendo questi passaggi:

1. Arrestare Jenkins. Se si utilizza Jenkins.app, è possibile farlo facendo clic sull'icona Jenkins.App ancoraggio e selezionando Esci dal menu che viene visualizzato:

    ![](jenkins-walkthrough-images/image19.png "Icona dell'App in ancoraggio e selezionando Esci dal menu a comparsa")
2. Aprire il file **~/.jenkins/config.xml** in un editor di testo.
3. Modificare il valore della `<usesecurity></usesecurity>` elemento dal `true` a `false`.
4. Eliminare il `<authorizationstrategy></authorizationstrategy>` e il `<securityrealm></securityrealm>` elementi dal file.
5. Riavviare Jenkins.

## <a name="setting-up-a-job"></a>Impostazione di un processo

Al primo livello, Jenkins possono essere gestite tutte le varie attività necessarie per compilare software in un *processo*. Un processo ha inoltre i metadati associati, che fornisce informazioni relative alla compilazione, ad esempio come ottenere il codice sorgente, frequenza di esecuzione della compilazione, tutte le variabili speciali che sono necessari per la compilazione e come inviare notifiche agli sviluppatori se la compilazione non riesce.

I processi vengono creati selezionando **Jenkins > nuovo processo** dal menu nell'angolo superiore destra, come illustrato nella schermata seguente:


![](jenkins-walkthrough-images/image22.png "I processi vengono creati selezionando Jenkins nuovo processo dal menu nell'angolo superiore destro")

Verrà visualizzata la **nuovo processo [Jenkins]** pagina. Immettere un nome per il processo, quindi selezionare il **compila un progetto software stile liberare** pulsante di opzione. Nella schermata seguente viene illustrato un esempio:

![](jenkins-walkthrough-images/image23.png "Immettere un nome per il processo e selezionare la compilazione di un pulsante di opzione di stile liberare software progetto")

Scegliere il **OK** pulsante viene visualizzata la pagina di configurazione per il processo. Questo dovrebbe essere simile nella schermata seguente:

![](jenkins-walkthrough-images/image24.png "Questo dovrebbe essere simile a questo screenshot")

Jenkins consente di organizzare i processi in una directory sul disco rigido che si trova nel percorso seguente: **~/.jenkins/jobs/[JOB nome]**

Questa cartella contiene tutti i file e gli elementi specifici per il processo, ad esempio i log, file di configurazione e il codice sorgente che deve essere compilato.

Dopo aver creato il processo iniziale, deve essere configurato con uno o più delle operazioni seguenti:

 - È necessario specificare il sistema di gestione del codice sorgente.
 - Uno o più *azioni di compilazione* deve essere aggiunto al progetto. Questi sono i passaggi o attività necessarie per compilare l'applicazione.
 - Il processo deve essere assegnato uno *trigger di compilazione* : un set di istruzioni che indicano la frequenza Jenkins per recuperare il codice e compilare il progetto finale.

### <a name="configuring-source-code-control"></a>Configurazione di controllo del codice sorgente

La prima attività Jenkins è recuperare il codice sorgente dal sistema di gestione del codice sorgente. Jenkins supporta molti dei sistemi management codice sorgente comuni oggi disponibili. Questa sezione descrive due sistemi più diffusi, Git e Team Foundation Server. Ciascuno di questi sistemi di gestione del codice sorgente è descritto più dettagliatamente nelle sezioni riportate di seguito.

#### <a name="using-git-for-source-code-control"></a>Usando Git per controllo del codice sorgente

Se si usa TFS per controllo del codice sorgente [ignorare](#Using_TFS_for_Source_Code_Management) questa sezione e passare alla sezione successiva mediante TFS.

Jenkins supporta Git impostazione predefinita: nessuna altri plug-in sono necessari. Per utilizzare Git, fare clic sui **Git** pulsante di opzione e immettere l'URL per il repository Git, come illustrato nella schermata seguente:

![](jenkins-walkthrough-images/image25.png "Per utilizzare Git, fare clic sul pulsante di opzione Git e immettere l'URL per il repository Git")

Dopo aver salvato le modifiche, configurazione di Git è stata completata.

#### <a name="using-tfs-for-source-code-management"></a>Utilizzo di TFS per gestione del codice sorgente

In questa sezione si applica solo agli utenti TFS.

Fare clic sui **Team Foundation Server** pulsante di opzione e la sezione di configurazione di TFS vengono visualizzati, simile a quello nella schermata seguente:


![](jenkins-walkthrough-images/image26.png "Fare clic sul pulsante di opzione di Team Foundation Server e la sezione di configurazione TFS dovrebbe essere visualizzato")

Fornire le informazioni necessarie per TFS. Nella schermata seguente viene illustrato un esempio del form completato:

![](jenkins-walkthrough-images/image27.png "Questa schermata è riportato un esempio del form completato")

#### <a name="testing-the-source-code-control-configuration"></a>Verifica la configurazione di controllo del codice sorgente

Dopo aver configurato il controllo del codice sorgente appropriato, fare clic su **salvare** per salvare le modifiche. Ciò per tornare alla home page per il processo, che sarà simili a quello nella schermata seguente:

![](jenkins-walkthrough-images/image28.png "Ciò per tornare alla home page per il processo, che sarà simile a questo screenshot")

Il modo più semplice per convalidare che il controllo del codice sorgente sia configurato correttamente consiste nell'attivare una compilazione manuale, anche se non sono presenti azioni di compilazione specificate. Per avviare manualmente una compilazione, la home page del processo è una **compilare ora** collegamento nel menu a sinistra, come illustrato nella schermata seguente:

![](jenkins-walkthrough-images/image29.png "Per avviare manualmente una compilazione, la home page del processo ha un collegamento a questo punto compilare nel menu di scelta sul lato sinistro")

Quando è stata avviata una compilazione, finestra di dialogo Cronologia di compilazione viene visualizzato un cerchio blu lampeggiante, un indicatore di stato, il numero di build e l'ora di inizio della compilazione, simile alla schermata riportata di seguito:

![](jenkins-walkthrough-images/image30.png "Dopo aver avviata una compilazione, finestra di dialogo Cronologia di compilazione viene visualizzato un cerchio blu lampeggiante, un indicatore di stato, il numero di build e l'ora di inizio della compilazione")

Se il processo ha esito positivo, verrà visualizzato un cerchio blu. Se il processo non riesce, verrà visualizzato un cerchio rosso.

Per facilitare la risoluzione dei problemi che potrebbero verificarsi come parte della compilazione, Jenkins acquisirà tutto l'output di console per il processo. Per visualizzare l'output della console, fare clic sul processo nella **compilare cronologia**e quindi scegliere il **Output della Console** collegamento nel menu a sinistra. La seguente schermata mostra il **Output della Console** collegamento, nonché alcune dell'output da un processo ha esito positivo:

![](jenkins-walkthrough-images/image31.png "Questa schermata è riportato il collegamento di Output della Console, nonché alcune dell'output da un processo ha esito positivo")

#### <a name="location-of-build-artifacts"></a>Percorso di artefatti di compilazione

Jenkins recupererà l'intero codice sorgente in una cartella speciale denominata un *dell'area di lavoro*. Questa directory può trovarsi all'interno della cartella nel percorso seguente:

    ~/.jenkins/jobs/[JOB NAME]/workspace

Il percorso all'area di lavoro verrà archiviato in una variabile di ambiente denominata `$WORKSPACE`.

È possibile passare alla cartella dell'area di lavoro in Jenkins passando alla pagina di destinazione per un processo e quindi fare clic sul **dell'area di lavoro** collegamento nel menu a sinistra. Nella schermata seguente viene illustrato un esempio dell'area di lavoro per un processo denominato **HelloWorld**:

![](jenkins-walkthrough-images/image32.png "Questa schermata è riportato un esempio di area di lavoro per un processo denominato HelloWorld")

### <a name="build-triggers"></a>I trigger di compilazione

Sono disponibili diverse strategie diversi per avviare le compilazioni in Jenkins: queste risorse sono note come *trigger di compilazione*. Un trigger di compilazione consente Jenkins decidere quando avviare un processo e compilare il progetto. Due dei trigger di compilazione più comuni sono:

- **Compilare periodicamente** – questo trigger determina Jenkins avviare un processo a intervalli specificati, ad esempio ogni due ore o a mezzanotte nei giorni feriali. Verrà avviata la compilazione indipendentemente dal fatto che sono state apportate modifiche nel repository del codice sorgente.
- **Polling SCM** – trigger eseguirà il polling di controllo del codice sorgente a intervalli regolari. Se tutte le modifiche sono state eseguite il repository di codice sorgente, Jenkins verrà avviata una nuova compilazione.

Gestione controllo servizi di polling è un trigger comune perché fornisce un feedback rapido quando uno sviluppatore apporta le modifiche che causano l'interruzione della compilazione. Ciò è utile per gli avvisi relativi ai team che causa problemi oltre a codice recentemente eseguito il commit e consente gli sviluppatori di risolvere il problema, mentre le modifiche sono state ancora fresche presente.

Compilazioni periodiche vengono spesso utilizzate per creare una versione dell'applicazione che può essere distribuito ai tester. Ad esempio, una compilazione periodica potrebbe essere programmata per venerdì sera in modo che i membri del team QA possono testare il lavoro della settimana precedente.


### <a name="compiling-a-xamarinios-applications"></a>La compilazione di un'applicazione di xamarin. IOS
I progetti di xamarin. IOS possono essere compilati nella riga di comando mediante `xbuild` o `msbuild`. Il comando di shell verrà eseguito nel contesto dell'account utente dotato di Jenkins. È importante che l'account utente disponga dell'accesso per il profilo di provisioning in modo che l'applicazione può essere inserito correttamente per la distribuzione. È possibile aggiungere questo comando della shell per la pagina Configurazione del processo.

Scorrere verso il basso il **compilare** sezione. Fare clic sui **istruzione di compilazione Add** e selezionare **eseguire shell**, come illustrato nella schermata seguente:

![](jenkins-walkthrough-images/image33.png "Fare clic sul pulsante Aggiungi compilazione passo e selezionare Esegui shell")


[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>Compilazione di un progetto xamarin

Compilazione di un progetto xamarin. Android è concettualmente molto simile alla compilazione di un progetto xamarin. IOS. Per creare un file APK da un progetto xamarin. Android, Jenkins deve essere configurato per eseguire i due passaggi seguenti:

 - Compilare il progetto utilizzando il plug-in di MSBuild
 - Accesso e zip sono allineate l'APK con un keystore di versione valido.

Questi due passaggi verranno descritta più dettagliatamente nelle prossime due sezioni.

### <a name="creating-the-apk"></a>Creazione di file APK

Fare clic sui **istruzione di compilazione Add** pulsante e selezionare **compila un progetto di Visual Studio o una soluzione utilizzando MSBuild**, come illustrato nella schermata seguente:

![](jenkins-walkthrough-images/image36.png "Creazione di fare clic sul file APK in Aggiungi passaggio di compilazione e scegliere Compila un progetto di Visual Studio o una soluzione con MSBuild")

Dopo l'istruzione di compilazione viene aggiunto al progetto, compilare i campi modulo visualizzati. Nella schermata seguente è un esempio del form completato:

![](jenkins-walkthrough-images/image37.png "Dopo l'istruzione di compilazione viene aggiunto al progetto, compilare i campi modulo visualizzati")


Questa istruzione di compilazione eseguirà `xbuild` nella **$WORKSPACE** cartella. Il File di compilazione MSBuild sia impostato il **Xamarin.Android.csproj** file. Il **argomenti della riga di comando** specificare una build di rilascio della destinazione **PackageForAndroid**. Il prodotto di questo passaggio sarà un file APK che nel percorso seguente:

    $WORKSPACE/[PROJECT NAME]/bin/Release

Nella schermata seguente viene illustrato un esempio di questo file APK:

![](jenkins-walkthrough-images/image38.png "Questa schermata è riportato un esempio di questo file APK")

Questo file APK non è pronta per la distribuzione, come non è stato firmato con un keystore privato e devono essere allineati zip.

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>Firma e Zipaligning APK per versione

Firma e zipaligning l'APK sono tecnicamente due operazioni distinte che vengono eseguite dai due strumenti da riga di comando separato da Android SDK. Tuttavia, è utile eseguire tali operazioni nell'azione di compilazione. Per ulteriori informazioni sulla firma e un file APK zipaligning, vedere la documentazione di Xamarin sulla preparazione di un'applicazione Android per il rilascio.

Entrambi questi comandi richiedono parametri della riga di comando che possono variare da progetto a progetto. Inoltre, alcuni di questi parametri della riga di comando sono le password che non devono essere visualizzati nell'output della console quando la compilazione è in esecuzione. È necessario archiviare alcuni di questi parametri della riga di comando nelle variabili di ambiente. Nella tabella seguente sono descritte le variabili di ambiente necessarie per la firma e/o zip allineamento:

|Variabile di ambiente|Descrizione|
|--- |--- |
|KEYSTORE_FILE|Si tratta del percorso per l'archivio chiavi per firmare il file APK|
|KEYSTORE_ALIAS|La chiave nell'archivio che verrà utilizzato per firmare il file APK.|
|INPUT_APK|Il file APK che viene creato da `xbuild`.|
|SIGNED_APK|il firmato APK prodotto da `jarsigner`.|
|FINAL_APK|Questo è il file zip allineato APK prodotto da `zipalign`.|
|STORE_PASS|Si tratta della password utilizzata per accedere al contenuto dell'archivio chiavi per singing il file.|

Come descritto nella sezione requisiti, è possono impostare queste variabili di ambiente durante la compilazione usando il plug-in EnvInject. Il processo è necessario definire una nuova compilazione passaggio aggiunto in base alle variabili di ambiente inserimento, come illustrato nella schermata successiva:

![](jenkins-walkthrough-images/image39.png "Il processo è necessario definire una nuova compilazione passaggio aggiunto in base alle variabili di ambiente inserimento")

Nel **proprietà contenuto** modulo campo che verrà visualizzato, vengono aggiunte le variabili di ambiente, uno per riga, nel formato seguente:

    ENVIRONMENT_VARIABLE_NAME = value

La schermata seguente mostra le variabili di ambiente necessarie per la firma di file APK:

![](jenkins-walkthrough-images/image40.png "Questa schermata mostra le variabili di ambiente necessarie per la firma di file APK")

Si noti che alcune delle variabili di ambiente per i file APK si basano sul `WORKSPACE` variabile di ambiente.

La variabile di ambiente finale è la password per accedere al contenuto di keystore: `STORE_PASS`. Le password sono i valori sensibili che devono essere nascoste o omesso nei file di log. Il plug-in EnvInject può essere configurato per proteggere questi valori in modo che non vengono visualizzati nei log.

Immediatamente prima la **compilare** sezione della configurazione del processo è un **ambiente Build** sezione. Quando il **inserire le password** casella di controllo viene attivato/disattivato, munita campi da visualizzare. Questi campi di formato vengono utilizzati per acquisire il nome e il valore della variabile di ambiente. Nella schermata seguente è riportato un esempio di aggiunta di `STORE_PASS` variabile di ambiente:

![](jenkins-walkthrough-images/image41.png "Questa schermata è riportato un esempio di aggiungere la variabile di ambiente STOREPASS")

Dopo che sono state inizializzate le variabili di ambiente, è il passaggio successivo per aggiungere un'istruzione di compilazione per la firma e codice postale allineare l'APK. Immediatamente dopo l'istruzione di compilazione per inserire le variabili di ambiente sarà un'altra **eseguire shell** compilazione di comando che verrà eseguito `jarsigner` e `zipalign`. Ogni comando richiederanno alto di una riga, come illustrato nel frammento riportato di seguito:


    jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
    zipalign -f -v 4 $SIGNED_APK $FINAL_APK

Nella schermata seguente viene illustrato un esempio su come specificare il `jarsigner` e `zipalign` comandi all'interno di passaggio:

![](jenkins-walkthrough-images/image42.png "Questa schermata è riportato un esempio di come immettere i comandi jarsigner e zipalign nel passaggio")

Una volta tutte le azioni di compilazione, è buona norma ad attivare una compilazione manuale per verificare che tutto stia funzionando. Se la compilazione non riesce, il **Output della Console** devono essere riviste per informazioni su ciò che ha causato l'errore di compilazione.

### <a name="submitting-tests-to-test-cloud"></a>Invio di test per Test Cloud

Test automatizzati possono essere inviati a Test Cloud tramite i comandi della shell. Per ulteriori informazioni sull'impostazione di esecuzione dei Test in Xamarin Test Cloud, vedere questa guida per l'utilizzo [UITest](/appcenter/test-cloud/preparing-for-upload/uitest/).


## <a name="summary"></a>Riepilogo

In questa guida è introdotto Jenkins come un server di compilazione su Mac OS X e configurato in modo da compilare e preparare le applicazioni per dispositivi mobili di Xamarin per il rilascio. Jenkins è installato in un computer Mac OS X insieme a diversi i plug-in per supportare il processo di compilazione. Viene creato e configurato un processo che verrà pull codice da TFS o Git, quindi compilare il codice in un'applicazione commerciale pronto. È inoltre sono stati illustrati due modi diversi per la pianificazione dei processi devono essere eseguiti.

## <a name="related-links"></a>Collegamenti correlati

- [Integrazione continua](https://developer.xamarin.com~/testcloud/ci/)
- [Invio di test con Xamarin Test Cloud](https://developer.xamarin.com~/testcloud/submitting/)
- [Motivo per cui Jenkins non è supportato da Xamarin?](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)
