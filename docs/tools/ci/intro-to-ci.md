---
title: Introduzione all'integrazione continua con Xamarin
description: Questo documento viene descritta l'integrazione continua con Xamarin. Vengono illustrati diversi ambienti di integrazione continua e controllo della versione.
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: topgenorth
ms.author: toopge
ms.date: 07/19/2017
ms.openlocfilehash: 67fc32fc9f79d54274642fbab2d0c2f8afd14d8c
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066507"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introduzione all'integrazione continua con Xamarin

_Integrazione continua è una pratica di progettazione del software in cui viene compilata una compilazione automatica e facoltativamente, verifica di un'app quando il codice viene aggiunto o modificato dagli sviluppatori nel repository di controllo della versione del progetto. In questo articolo verrà illustrati i concetti generali di integrazione continua e alcune delle opzioni disponibili per l'integrazione continua con i progetti di Xamarin._

È comune in progetti software consente agli sviluppatori di lavorare in parallelo. A un certo punto, è necessario integrare tutti questi paralleli flussi di lavoro in una codebase che costituisce il prodotto finale. Nei giorni iniziali di sviluppo del software, tale integrazione è stata eseguita alla fine di un progetto, in cui è stato un processo rischioso e difficile.

Integrazione continua (CI) per evitare tali complessità unendo le modifiche di ogni sviluppatore codebase comune in modo continuo, in genere ogni volta che tutti gli sviluppatori di controlli nelle modifiche apportate al progetto condiviso repository di codice. Ogni controllo aggiuntivo attiva una compilazione automatica ed esegue test automatizzati per verificare che il codice appena introdotto non compromettere il codice esistente.  In questo modo, CI evidenziano gli errori e problemi immediatamente e assicura che tutti i membri del team di rimanere aggiornati con i rispettivi lavoro. Ciò comporta una codebase coesiva e stabile.

I sistemi di integrazione continua hanno due parti principali:

-  **Controllo della versione** – versione controllo (VC), definito anche come controllo del codice sorgente o gestione del codice sorgente, tutto il codice del progetto consente di consolidare in un singolo repository condiviso e mantiene una cronologia completa di ogni modifica apportata a ogni file. Questo repository, noto anche come le *principali* oppure *master* branching, contiene il codice sorgente che verrà utilizzato per compilare la produzione o di versione dell'app. Esistono molti open source e prodotti per questa attività, che in genere consentono team o singoli utenti per dividere una copia del codice in rami secondari dove possono apportare modifiche estese o effettuare esperimenti senza rischio al ramo master. Dopo le modifiche in un ramo secondario vengono convalidate, possono quindi essere tutti insieme riuniti al ramo master.
-  **Server di integrazione continua** : il Server di integrazione continua è responsabile per la raccolta di tutti gli elementi del progetto (codice sorgente, immagini, video, database, i test automatizzati, e così via), la compilazione dell'app e l'esecuzione dei test automatizzati. Anche in questo caso sono presenti molti open source e strumenti di server CI esterna.

Gli sviluppatori devono in genere una copia di lavoro di uno o più rami sulle proprie workstation in cui viene inizialmente completata. Al termine del relativo gruppo di lavoro, le modifiche sono "verificate in" o "commit" per il ramo appropriato, che li propaga alle copie di lavoro di altri sviluppatori. Si tratta come un team assicura che tutti sta lavorando sullo stesso codice.

Con l'integrazione continua, anche in questo caso l'operazione di commit delle modifiche, il server di CI compilare il progetto ed eseguire test automatizzati per verificare la correttezza del codice sorgente. Se sono presenti errori di compilazione o errori di test, un server CI informa lo sviluppatore responsabile (tramite posta elettronica, messaggi immediati, Twitter, Growl, e così via) in modo egli può risolvere il problema. (Server CI può anche rifiutare il commit se sono presenti errori, viene chiamato un "archiviazione gestita".)

Nel diagramma seguente viene illustrato questo processo:

[![](intro-to-ci-images/intro01-small.png "Questo diagramma viene illustrato questo processo")](intro-to-ci-images/intro01.png#lightbox)

App per dispositivi mobili introducono virtualizzati pongono sfide straordinarie per l'integrazione continua. Le applicazioni possono richiedere sensori, ad esempio la fotocamera GPS che sono disponibili solo in dispositivi fisici. Inoltre, emulatori o simulatori sono solo un'approssimazione dell'hardware e potrebbero nascondere o nascondere i problemi. Alla fine, è necessario testare un'app per dispositivi mobili sull'hardware effettivo per essere sicuri che è effettivamente pronta.

Il [App centro Test](https://docs.microsoft.com/appcenter/test-cloud) risolve questo problema particolare test di App direttamente in centinaia di dispositivi fisici. Gli sviluppatori di scrivono i test automatizzati accettazioni, che consentono di test dell'interfaccia utente potenti. Dopo che questi test vengono caricati al centro di App, il server CI possibile eseguirli automaticamente come parte di un processo di integrazione continua come illustrato nel diagramma seguente:

[![](intro-to-ci-images/intro02-small.png "Una volta questi test vengono caricati al centro di App, il server CI possibile eseguirli automaticamente come parte di un processo di integrazione continua come illustrato in questo diagramma")](intro-to-ci-images/intro02.png#lightbox)

# <a name="components-of-continuous-integration"></a>Componenti di integrazione continua

È presente un ampio ecosistema commerciali e open-source strumenti progettati per supportare CI. Questa sezione illustra alcune delle cause più comuni.

## <a name="version-control"></a>Controllo della versione

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services e Team Foundation Server

[Visual Studio Team Services](https://visualstudio.microsoft.com/team-services/) (VSTS) e [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) sono strumenti di collaborazione di Microsoft per l'integrazione continuata compilare servizi attività di rilevamento, pianificazione agile e reporting strumenti e versione controllo. Con controllo delle versioni, Visual Studio Team Services e TFS possono essere usati con il proprio sistema (Team Foundation Version Control o TFVC) o con i progetti ospitati in GitHub.

 - Visual Studio Team Services fornisce servizi tramite il cloud. È maggiore vantaggio è che non richiede hardware dedicato o dell'infrastruttura e sono accessibili da ovunque tramite browser web e gli strumenti di sviluppo comuni, ad esempio Visual Studio, rendendo più interessante per i team che sono geograficamente distribuito. È disponibile per i team di cinque sviluppatori o meno, dopo aver possono essere acquistate le licenze aggiuntive per gestire un team crescente.
 - TFS è progettato per i server Windows in locale e si accede tramite una rete locale o una connessione VPN alla rete. Il vantaggio principale è completamente il controllo della configurazione dei server di compilazione e può installare qualsiasi altro software o servizi sono necessari. TFS è un'edizione Express entry level gratuita per piccoli team.

TFS e Visual Studio Team Services sono strettamente integrati con Visual Studio e consentono agli sviluppatori di eseguire molti controllo della versione e le attività di CI da entro la comodità di un singolo IDE. È disponibile anche il Team Explorer Everywhere plug-in per Eclipse (vedere sotto). Visual Studio per Mac non offre alcun supporto per TFS o Visual Studio Team Services.

Il sistema di compilazione di Visual Studio Team del servizio ha supporto diretto per i progetti di Xamarin, entro il quale si crea una definizione di compilazione per ogni piattaforma che si desidera destinazione (Android, iOS e Windows) La licenza di Xamarin è necessaria per ogni definizione di compilazione. È inoltre possibile per la connessione locale, in grado di supportare Xamarin TFS build server per Visual Studio Team Services per questo scopo. Con questa configurazione, le compilazioni che vengono messe in coda per Visual Studio Team Services verranno delegate al server locale. Per informazioni dettagliate, consultare [distribuire e configurare un server di compilazione](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts). In alternativa, è possibile utilizzare un altro strumento di compilazione, ad esempio Jenkins o città Team.

Un elenco completo di tutte le funzionalità di Application Lifecycle Management (ALM) di Visual Studio, Visual Studio Team Services e Team Foundation Server, vedere [gestione del ciclo di vita delle applicazioni con App Xamarin](https://msdn.microsoft.com/library/mt162217(v=vs.140).aspx) su MSDN.


### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](http://msdn.microsoft.com/library/gg413285.aspx) le potenzialità di Team Foundation Server e Visual Studio Team Services per team di sviluppo esterni a Visual Studio. Consente agli sviluppatori di connettersi ai progetti team in locale o nel cloud da Eclipse o il client della riga di comando multipiattaforma per Linux e OS X. Team Explorer Everywhere completo fornisce l'accesso al controllo della versione, inclusi Git, gli elementi di lavoro e compilare funzionalità per le piattaforme non Windows.


### <a name="git"></a>Git

[GIT](http://git-scm.com) è una soluzione di controllo di versione open source comuni che è stata sviluppata per gestire il codice sorgente per il kernel Linux. È un sistema molto veloce, flessibile di frequente con i progetti di software di tutte le dimensioni. Si adatta facilmente da sviluppatori singoli con accesso a Internet scarsa ai team di grandi dimensioni che si estendono su tutto il mondo. GIT consente inoltre di creare un ramo molto semplice, che a sua volta possono incoraggiare i flussi paralleli dello sviluppo con il rischio minimo.

GIT possono operare interamente mediante un web browser oppure tramite [client GUI](http://git-scm.com/downloads/guis) eseguite in Linux, Mac OS x e Windows. È gratuito per repository pubblici; repository privati richiedono un [pagato piano](https://github.com/pricing).

Visual Studio 2015 e Visual Studio per Mac offrono supporto nativo per Git; per le versioni precedenti, Microsoft fornisce un [estensione scaricabile per Git](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c). Come notato in precedenza, Visual Studio Team Services e TFS possono utilizzare Git per il controllo della versione anziché TFVC.


### <a name="subversion"></a>Subversion

[Subversion](http://subversion.apache.org) (SVN) è un sistema di controllo di versione open source più diffusi che è stato in uso rispetto a 2000. SVN viene eseguito su tutte le versioni moderne di OS X, Windows, FreeBSD, Linux e Unix. Visual Studio per Mac offre supporto nativo per SVN. Sono presenti estensioni di terze parti che portano supporto SVN a Visual Studio.


## <a name="continuous-integration-environments"></a>Ambienti di integrazione continua

Configurazione di un ambiente di integrazione continua comporta l'unione di un sistema di controllo della versione con un servizio di compilazione.  Nel secondo caso, le due cause più comuni sono:

- [Team Foundation Build](https://msdn.microsoft.com/Library/vs/alm/Build/overview) è il sistema di compilazione di Visual Studio Team Services e TFS. È altamente integrato con Visual Studio, che consente agli sviluppatori di attivare compila, automaticamente eseguire test e visualizzare i risultati.
- Jenkins è un server di CI open source che con un ecosistema ricco dei plug-in per supportare tutti i tipi di sviluppo del software. Viene eseguito in Windows e Mac OS X. Jenkins non è integrato con qualsiasi IDE specifico. Al contrario, viene configurato e gestito tramite un'interfaccia web. Integrazione Continuata Jenkins è anche facile da installare e configurare rende interessante al team di piccole dimensioni.

È possibile utilizzare TFS/VSTS autonomamente oppure è possibile utilizzare Jenkins in combinazione con TFS/Visual Studio Team Services o Git, come descritto nelle sezioni seguenti.

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services e Team Foundation Server

Come illustrato in precedenza, Visual Studio Team Services e Team Foundation Server offre sia versione controllare e servizi di compilazione. Servizi di compilazione richiedono sempre una licenza di Xamarin Business o aziendali per ciascuna piattaforma di destinazione.

Con Visual Studio Team Services, creare una definizione di compilazione separata per ogni piattaforma di destinazione e immettere la licenza non esiste. Una volta configurato, Visual Studio Team Services eseguirà le compilazioni e i test nel cloud. Vedere [Team Foundation Build](https://msdn.microsoft.com/Library/vs/alm/Build/overview) per altri dettagli.

Con Team Foundation Server, configurare un computer di compilazione come indicato di seguito per piattaforme di destinazione specifico:

- **Android e Windows:** installare Visual Studio e il Xamarin degli strumenti (per Android e Windows) e configurare con le licenze Xamarin. È anche necessario spostare il Android SDK in un percorso condiviso nel server in cui l'agente di compilazione TFS possibile trovarlo. Per informazioni dettagliate, vedere [TFVC configurazione](https://docs.microsoft.com/vsts/tfvc/overview).
- **iOS e Xamarin:** installare Visual Studio e gli strumenti di Xamarin nel server di Windows con licenza appropriata. Quindi, installare Visual Studio per Mac in un computer Mac OS X accessibile dalla rete, che fungono da un host di compilazione e creare il pacchetto dell'app finale (IPA per iOS, APP per OS X).

Il diagramma seguente illustra questo topografia:

[![](intro-to-ci-images/intro03-small.png "Questo diagramma viene illustrata questa topografia")](intro-to-ci-images/intro03.png#lightbox)

È anche possibile collegare un server TFS locale a un progetto di Visual Studio Team Services in modo che le compilazioni di Visual Studio Team Services sono delegate al server locale. Per informazioni dettagliate, vedere [distribuire e configurare un server di compilazione](http://msdn.microsoft.com/library/ms181712.aspx) su MSDN.

### <a name="visual-studio-team-services-and-jenkins"></a>Jenkins e visual Studio Team Services

Se si usa Jenkins per compilare le applicazioni, è possibile archiviare il codice in Visual Studio Team Services o Team Foundation Server e continuare a utilizzare Jenkins per le compilazioni CI. È possibile attivare una compilazione Jenkins quando si esegue il push codice per il progetto team repository Git o quando si archivia codice in tfvc. Per informazioni dettagliate, vedere [Jenkins con Visual Studio Team Services](https://docs.microsoft.com/en-us/vsts/service-hooks/services/jenkins?view=vsts).

[![](intro-to-ci-images/intro04-small.png "Se si usa Jenkins per compilare le applicazioni, è possibile archiviare il codice in Visual Studio Team Services o Team Foundation Server e continuare a utilizzare Jenkins per le compilazioni di CI")](intro-to-ci-images/intro04.png#lightbox)

### <a name="git-and-jenkins"></a>GIT e Jenkins

Un altro ambiente CI comune può essere interamente OS X in base. Questo scenario prevede l'uso di Git per controllo del codice sorgente e Jenkins per il server di compilazione. Entrambe queste sono in esecuzione in un singolo computer Mac OS X con Visual Studio per Mac installato. Questo è molto simile a Visual Studio Team Services + ambiente Jenkins illustrate nella sezione precedente:

[![](intro-to-ci-images/intro05-small.png "Questo è molto simile a Visual Studio Team Services + ambiente Jenkins illustrate nella sezione precedente")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Nota: È stata Jenkins [non è supportato da Xamarin](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md).**


# <a name="summary"></a>Riepilogo

Questo documento ha introdotto il concetto di integrazione continua e i vantaggi che offre ai team di sviluppo software. L'importanza del controllo della versione è stato illustrato insieme ai ruoli e responsabilità del server di compilazione. Il documento quindi frequentate vengono illustrati alcuni degli strumenti che sono possibile utilizzare per controllo del codice sorgente e server di compilazione. È stata inoltre introdotta App centro Test, che consente agli sviluppatori di pubblicare App eccezionali eseguendo i test automatizzati che si rivelerà la qualità e le funzionalità delle proprie app. Documentazione sull'invio di App e i test per App Center sono disponibili più dettagliata [qui](https://docs.microsoft.com/appcenter/test-cloud). Infine, per comprendere come gli strumenti e i componenti si integrino, delineato ambienti CI differenti che le organizzazioni potrebbero stabilire per l'integrazione continua. Per ulteriori informazioni sull'utilizzo di Visual Studio Team Services e Team Foundation Server con i progetti di Xamarin, vedere [configurazione TFVC](https://docs.microsoft.com/vsts/tfvc/overview) questa [introduzione continuate](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1). Analogamente, se si usa Jenkins, vedere [Jenkins utilizzando con Xamarin](~/tools/ci/jenkins-walkthrough.md) per informazioni dettagliate su come configurare un'integrazione continua.
