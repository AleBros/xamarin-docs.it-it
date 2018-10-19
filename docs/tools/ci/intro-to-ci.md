---
title: Introduzione all'integrazione continua con Xamarin
description: Questo documento descrive l'integrazione continua con Xamarin. Illustra vari ambienti di integrazione continua e il controllo della versione.
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: lobrien
ms.author: laobri
ms.date: 07/19/2017
ms.openlocfilehash: 5468495885e3af2afa2692ccad9191b669fa3328
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "37066507"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introduzione all'integrazione continua con Xamarin

_Integrazione continua è una pratica di ingegneria del software in cui una compilazione automatizzata, compila e verifica facoltativamente un'app quando il codice viene aggiunto o modificato dagli sviluppatori nel repository di controllo della versione del progetto. Questo articolo illustra i concetti generali di Continuous Integration e alcune delle opzioni disponibili per l'integrazione continua con i progetti Xamarin._

È comune a progetti software gli sviluppatori possono operare in parallelo. A un certo punto, è necessario integrare tutti questi flussi paralleli di lavoro in una codebase che costituisce il prodotto finale. Le radici dello sviluppo software, questa integrazione è stata eseguita alla fine di un progetto, che è stato un processo difficile e a rischio.

Integrazione continua (CI) per evitare tale complessità, uniscono le modifiche di ogni sviluppatore nella codebase comune in modo continuo, in genere ogni volta che tutti gli sviluppatori di archiviare le modifiche apportate al progetto condiviso repository di codice. Ogni controllo aggiuntivo attiva una compilazione automatizzata ed esegue test automatizzati per verificare che il codice esistente non interrompe con il codice appena introdotto.  In questo modo, CI evidenziano gli errori e problemi immediatamente e assicura che tutti i membri del team di rimanere aggiornati con i rispettivi lavoro. Ciò comporta una codebase stabile e coesiva.

Sistemi di integrazione continua hanno due parti principali:

- **Controllo della versione** – versione controllo (VC), definito anche come controllo del codice sorgente o gestione del codice sorgente, tutto il codice del progetto consente di consolidare in un unico repository condiviso e mantiene una cronologia completa di tutte le modifiche a tutti i file. Questo repository, noto anche come il *mainline* oppure *master* ramo, contiene il codice sorgente che verrà usato per compilare l'ambiente di produzione o di rilascio versione dell'app. Sono disponibili molti prodotti commerciali per questa attività, che in genere consentono ai team o singoli utenti di creare il fork una copia del codice in rami secondari dove possono apportare modifiche estese o effettuare esperimenti senza rischi per il ramo master e open source. Dopo che le modifiche in un ramo secondario vengono convalidate, possono quindi essere tutti insieme riuniti nel ramo master.
- **Continuous Integration Server** : il Server di integrazione continua è responsabile per la raccolta di tutti gli elementi di un progetto (codice sorgente, immagini, video, i database, i test automatizzati, e così via), la compilazione dell'app e l'esecuzione dei test automatizzati. Anche in questo caso, sono disponibili molti strumenti di server CI commerciali e open source.

Gli sviluppatori devono in genere una copia di uno o più rami di lavoro sulle proprie workstation, in cui inizialmente lavoro. Dopo aver completato un set appropriato di lavoro, le modifiche sono "selezionate in" o "commit" per il ramo appropriato, che li propaga alle copie di lavoro di altri sviluppatori. Si tratta come un team assicura che tutti lavorano sullo stesso codice.

Anche in questo caso, con l'integrazione continua, l'atto di commit delle modifiche, il server di integrazione continua compilare il progetto ed eseguire test automatizzati per verificare la correttezza del codice sorgente. Se sono presenti errori di compilazione o errori di test, un server CI informa lo sviluppatore responsabile (tramite posta elettronica, messaggistica immediata, Twitter, Growl, e così via) in modo che tale persona può risolvere il problema. (Server CI può anche rifiutare il commit se sono presenti errori, che viene chiamato un "archiviazione gestita".)

Il diagramma seguente illustra questo processo:

[![](intro-to-ci-images/intro01-small.png "Questo diagramma viene illustrato questo processo")](intro-to-ci-images/intro01.png#lightbox)

App per dispositivi mobili presentano difficoltà specifiche per l'integrazione continua. Le app possono richiedere sensori, ad esempio il GPS o della fotocamera che sono disponibili solo in dispositivi fisici. Inoltre, emulatori o simulatori sono solo un'approssimazione dell'hardware e potrebbero nascondere o nascondere i problemi. Al termine, è necessario testare un'app per dispositivi mobili sulll'hardware effettivo per essere sicuri che è effettivamente pronta.

Il [Test App Center](https://docs.microsoft.com/appcenter/test-cloud) risolve questo problema specifico eseguendo il test delle App direttamente su centinaia di dispositivi fisici. Gli sviluppatori di scrivono i test automatizzati accettazioni, che consentono di test dell'interfaccia utente potenti. Una volta che questi test vengono caricati in App Center, il server CI possibile eseguirli automaticamente come parte di un processo di integrazione continua come illustrato nel diagramma seguente:

[![](intro-to-ci-images/intro02-small.png "Una volta che questi test vengono caricati in App Center, il server CI possibile eseguirli automaticamente come parte di un processo di integrazione continua come illustrato nella figura seguente")](intro-to-ci-images/intro02.png#lightbox)

# <a name="components-of-continuous-integration"></a>Componenti di integrazione continua

È presente un esteso ecosistema di strumenti commerciali e open source progettata per supportare CI. Questa sezione illustra alcune delle cause più comuni.

## <a name="version-control"></a>Controllo della versione

### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps e Team Foundation Server

[Azure DevOps](https://azure.microsoft.com/services/devops/) e [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) sono strumenti di collaborazione di Microsoft per l'integrazione continua di compilazione servizi, attività di rilevamento, agile planning e gli strumenti e il controllo della versione di reporting. Il controllo della versione, Azure DevOps e TFS possono lavorare con il proprio sistema (controllo della versione di Team Foundation o TFVC) o con i progetti ospitati in GitHub.

- Visual Studio Team Services offre servizi tramite il cloud. Il vantaggio principale è che non richiede hardware dedicato o dell'infrastruttura e sono accessibili da ovunque tramite web browser e strumenti di sviluppo più diffusi, ad esempio Visual Studio, rendendo più interessante per i team geograficamente distribuito. È gratuito per i team di cinque sviluppatori o meno, dopo il quale licenze aggiuntive possono essere acquistate per soddisfare un team in costante crescita.
- TFS è progettato per i server Windows locali e si accede tramite una rete locale o una connessione VPN alla rete. Il vantaggio principale è che completamente il controllo della configurazione dei server di compilazione e possono installare qualsiasi altro software o i servizi sono necessari. TFS ha una Express edition livello base gratuito per piccoli team.

TFS e DevOps di Azure sono strettamente integrati con Visual Studio e consentono agli sviluppatori di eseguire molti controllo della versione e le attività di integrazione continua da in tutta comodità un unico IDE. È disponibile anche il Team Explorer Everywhere plug-in per Eclipse (vedere sotto). Visual Studio per Mac offre [un'anteprima di TFVC disponibili](/visualstudio/mac/tf-version-control/).

[Azure DevOps pipeline](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/) include il supporto diretto per i progetti Xamarin, entro il quale creare una definizione di compilazione per ogni piattaforma da specificare come destinazione (Android, iOS e Windows). Per ogni definizione di compilazione è necessaria la licenza Xamarin appropriata. È anche possibile connettersi a una variabile locale, in grado di supportare Xamarin TFS build server per Azure DevOps per questo scopo. Con questa configurazione, le compilazioni accodate a DevOps di Azure verranno delegate al server locale. Per informazioni dettagliate, consultare [Build e release Agent](https://docs.microsoft.com/azure/devops/pipelines/agents/agents). In alternativa, è possibile usare un altro strumento di compilazione, ad esempio Team città o Jenkins.

Un riepilogo completo di tutte le funzionalità di Application Lifecycle Management (ALM) di Visual Studio, Azure DevOps e Team Foundation Server, vedere [DevOps con le app Xamarin](https://docs.microsoft.com/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps).

### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) estende le potenzialità di Team Foundation Server e Visual Studio Team Services per team che sviluppano soluzioni esternamente a Visual Studio. Consente agli sviluppatori di connettersi ai progetti team in locale o nel cloud da Eclipse o il client della riga di comando multipiattaforma per OS X e Linux. Accedere al controllo della versione (incluso Git), gli elementi di lavoro di Team Explorer Everywhere offre completo e funzionalità di compilazione per piattaforme non Windows.

### <a name="git"></a>Git

[GIT](http://git-scm.com) è una soluzione di controllo di versione open source molto diffuso che è stato originariamente sviluppata per gestire il codice sorgente per il kernel Linux. È un sistema molto veloce e flessibile che è comune con i progetti software di qualsiasi dimensione. Lo Ridimensiona facilmente dal singoli sviluppatore con accesso a Internet scadente al team di grandi dimensioni che si estendono su tutto il mondo. GIT consente anche di creazione di rami molto semplice, che a sua volta possono incoraggiare flussi paralleli di sviluppo al minimo i rischi.

GIT possono operare interamente tramite un web browser o tramite [client GUI](http://git-scm.com/downloads/guis) in esecuzione su Linux, Mac OSX e Windows. È gratuito per i repository pubblici; repository privati richiedono una [piano a pagamento](https://github.com/pricing).

Visual Studio 2015 e Visual Studio per Mac offrono supporto nativo per Git; per le versioni precedenti, Microsoft fornisce una [estensione scaricabile per Git](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c). Come indicato in precedenza, Visual Studio Team Services e TFS possono usare Git per il controllo della versione anziché TFVC.

### <a name="subversion"></a>Subversion

[Subversion](http://subversion.apache.org) (SVN) è un sistema di controllo di versione open source molto diffuso che è stata utilizzata dal 2000. SVN viene eseguito su tutte le versioni moderne di OS X, Windows, FreeBSD, Linux e Unix. Visual Studio per Mac offre supporto nativo per SVN. Sono disponibili estensioni di terze parti che offrono supporto SVN a Visual Studio.

## <a name="continuous-integration-environments"></a>Ambienti di integrazione continua

Configurazione di un ambiente di integrazione continuata si intende la combinazione di un sistema di controllo della versione con un servizio di compilazione.  Nel secondo caso, le due cause più comuni sono:

- [Le pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/) è il sistema di compilazione di TFS e la metodologia DevOps di Azure. È altamente integrato con Visual Studio, rendendo è utile per gli sviluppatori attivare compilazioni, automaticamente eseguire i test e visualizzare i risultati.
- Jenkins è un server di integrazione continua open source che con un ampio ecosistema di plug-in per supportare tutti i tipi di sviluppo del software. Viene eseguito in Windows e Mac OS X. Jenkins non è integrata con qualsiasi IDE specifico. Al contrario, viene configurato e gestito tramite un'interfaccia web. Integrazione Continuata Jenkins è anche facile da installare e configurare che rende interessante per i piccoli team.

È possibile usare TFS, Azure DevOps autonomamente oppure è possibile usare Jenkins in combinazione con Git o TFS, Azure DevOps, come descritto nelle sezioni seguenti.

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services e Team Foundation Server

Come illustrato, Visual Studio Team Services e Team Foundation Server fornisce le versioni di controllo e creazione di servizi. Servizi di compilazione richiedono sempre una licenza di Xamarin Business o Enterprise per ogni piattaforma di destinazione.

Con Visual Studio Team Services, creare una definizione di compilazione separata per ogni piattaforma di destinazione e immettere la licenza appropriata non esiste. Una volta configurato, Azure DevOps verrà run compilare e testare nel cloud. Visualizzare [pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/) per altri dettagli.

Con Team Foundation Server, si configura un computer di compilazione come indicato di seguito per le piattaforme di destinazione specifico:

- **Android e Windows:** installare Visual Studio e Xamarin (per Android e Windows entrambe) degli strumenti e configurare con le licenze Xamarin. È anche necessario spostare il Android SDK in un percorso condiviso sul server dell'agente di compilazione TFS in cui è possibile trovarlo. Per informazioni dettagliate, vedere [TFVC configurazione](https://docs.microsoft.com/azure/devops/repos/tfvc/overview).
- **iOS e Xamarin:** installare Visual Studio e gli strumenti Xamarin nel server di Windows con la licenza appropriata. Quindi, installare Visual Studio per Mac in un computer Mac OS X accessibile dalla rete, che fungono da un host di compilazione e creare il pacchetto di applicazione finale (IPA per APP per OS X, iOS).

Il diagramma seguente illustra la topografia di:

[![](intro-to-ci-images/intro03-small.png "Questa figura illustra la topografia di")](intro-to-ci-images/intro03.png#lightbox)

È anche possibile collegare un server TFS locale a un Visual Studio Team Services project, in modo che Azure DevOps crea vengono delegati al server locale. Per informazioni dettagliate, vedere [Build e release Agent](https://docs.microsoft.com/azure/devops/pipelines/agents/agents/).

### <a name="visual-studio-team-services-and-jenkins"></a>Jenkins e visual Studio Team Services

Se si userà Jenkins per compilare le app, è possibile archiviare il codice in Visual Studio Team Services o Team Foundation Server e continuare a usare Jenkins per le compilazioni di integrazione continua. Quando si effettua il push di codice al repository Git del progetto team o quando si archivia codice in TFVC, è possibile attivare una compilazione di Jenkins. Per informazioni dettagliate, vedere [Jenkins con Azure DevOps](https://docs.microsoft.com/azure/devops/service-hooks/services/jenkins).

[![](intro-to-ci-images/intro04-small.png "Se si userà Jenkins per compilare le app, è possibile archiviare il codice in Visual Studio Team Services o Team Foundation Server e continuare a usare Jenkins per le compilazioni di integrazione continua")](intro-to-ci-images/intro04.png#lightbox)

### <a name="git-and-jenkins"></a>GIT e Jenkins

Un altro ambiente CI comune può essere completamente OS X in base. Questo scenario prevede l'uso di Git per controllo del codice sorgente e Jenkins per il server di compilazione. Entrambi sono in esecuzione in un singolo computer Mac OS X con Visual Studio per Mac installati. Ciò è molto simile a Visual Studio Team Services + ambiente Jenkins illustrate nella sezione precedente:

[![](intro-to-ci-images/intro05-small.png "Ciò è molto simile a Visual Studio Team Services + ambiente Jenkins illustrate nella sezione precedente")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Jenkins è [non è supportato da Microsoft](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md).**

# <a name="summary"></a>Riepilogo

Questo documento ha introdotto il concetto di integrazione continua e i vantaggi che offre ai team di sviluppo software. L'importanza del controllo della versione è stato illustrato insieme ai ruoli e responsabilità del server di compilazione. Il documento si illustrano alcuni degli strumenti che possono essere utilizzato per il controllo codice sorgente e i server di compilazione. È stata anche introdotta Test App Center, che consente agli sviluppatori di pubblicare App eccezionali eseguendo test automatizzati che si rivelerà la qualità e la funzionalità delle proprie app. Documentazione sull'invio di App e i test da App Center sono reperibili dettagliata [qui](https://docs.microsoft.com/appcenter/test-cloud). Infine, per consentire di comprendere interazione di tutti questi strumenti e componenti, sono descritti diversi ambienti CI diversi organizzazioni potrebbero stabilire per l'integrazione continua. Per altre informazioni sull'utilizzo di Visual Studio Team Services e Team Foundation Server con i progetti Xamarin, vedere [configurazione TFVC](https://docs.microsoft.com/azure/devops/repos/tfvc/overview/) e ciò [Introduzione a integrazione continua](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer/). Analogamente, se si usa Jenkins, vedere [uso di Jenkins con Xamarin](~/tools/ci/jenkins-walkthrough.md) per informazioni dettagliate su come configurare l'integrazione continua.
