---
title: Introduzione all'integrazione continua con Xamarin
description: Questo documento descrive l'integrazione continua con Xamarin. Vengono illustrati il controllo della versione e diversi ambienti di integrazione continua.
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: davidortinau
ms.author: daortin
ms.date: 07/19/2017
ms.openlocfilehash: 2862f05f2d183c9345d2b92268ddf2101cc2492e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029808"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introduzione all'integrazione continua con Xamarin

_L'integrazione continua è una procedura di progettazione software in cui una compilazione automatizzata compila ed eventualmente testa un'app quando il codice viene aggiunto o modificato dagli sviluppatori nel repository del controllo della versione del progetto. In questo articolo verranno illustrati i concetti generali dell'integrazione continua e alcune delle opzioni disponibili per l'integrazione continua con i progetti Xamarin._

Per gli sviluppatori è frequente lavorare in parallelo con i progetti software. A un certo punto, è necessario integrare tutti questi flussi di lavoro paralleli in un'unica codebase che costituisce il prodotto finale. Nei primi giorni dello sviluppo del software, questa integrazione è stata eseguita alla fine di un progetto, che era un processo difficile e rischioso.

L'integrazione continua (CI) evita tali complessità unendo continuamente le modifiche di ogni sviluppatore nella codebase comune, in genere ogni volta che gli sviluppatori archiviano le modifiche nel repository del codice condiviso del progetto. Ogni archiviazione attiva una compilazione automatizzata ed esegue test automatizzati per verificare che il codice appena introdotto non interrompa il codice esistente.  In questo modo, CI si riferisce immediatamente a errori e problemi e si assicura che tutti i membri del team siano sempre aggiornati. In questo modo si ottiene una codebase coesa e stabile.

I sistemi di integrazione continua sono due parti principali:

- **Controllo della versione** : controllo della versione (VC), denominato anche controllo del codice sorgente o gestione del codice sorgente, consolida tutto il codice di un progetto in un unico repository condiviso e mantiene una cronologia completa di ogni modifica apportata a ogni file. Questo repository, spesso indicato come *principale* o ramo *principale* , contiene il codice sorgente che verrà usato per compilare la versione di produzione o di rilascio dell'app. Per questa attività sono disponibili molti prodotti open source e commerciali, che in genere consentono a team o singoli utenti di creare un fork di una copia del codice in Branch secondari in cui è possibile apportare modifiche estese o condurre esperimenti senza rischi per il ramo master. Una volta convalidate le modifiche apportate a un ramo secondario, possono essere riunite nuovamente nel ramo master.
- **Continuous Integration Server** : il server di integrazione continua è responsabile della raccolta di tutti gli elementi di un progetto (codice sorgente, immagini, video, database, test automatizzati e così via), compilazione dell'app ed esecuzione dei test automatizzati. Anche in questo caso, sono disponibili molti strumenti server CI open source e commerciali.

Gli sviluppatori in genere dispongono di una copia funzionante di uno o più Branch sulle rispettive workstation, in cui il lavoro viene inizialmente eseguito. Una volta completato il set di lavoro appropriato, le modifiche vengono "archiviate" o "salvate" nel ramo appropriato, che le propaga alle copie di lavoro di altri sviluppatori. Questo è il modo in cui un team garantisce che stiano lavorando sullo stesso codice.

Anche in questo caso, con l'integrazione continua, l'azione di commit delle modifiche determina la compilazione del progetto da parte del server CI e l'esecuzione di test automatizzati per verificare la correttezza del codice sorgente. Se si verificano errori di compilazione o errori di test, un server CI informa lo sviluppatore responsabile (tramite posta elettronica, messaggistica immediata, Twitter, ringhio e così via) in modo che possa risolvere il problema. I server CI possono anche rifiutare il commit in caso di errori, denominato "archiviazione gestita".

Il diagramma seguente illustra questo processo:

[![](intro-to-ci-images/intro01-small.png "This diagram illustrates this process")](intro-to-ci-images/intro01.png#lightbox)

Le app per dispositivi mobili introducono problemi univoci per l'integrazione continua. Le app possono richiedere sensori quali il GPS o la fotocamera disponibili solo nei dispositivi fisici. Inoltre, i simulatori o gli emulatori sono solo un'approssimazione dell'hardware e possono celare o nascondere problemi. Alla fine, è necessario testare un'app per dispositivi mobili su hardware reale per avere la certezza che sia effettivamente pronta per i clienti.

Il [test di App Center](https://docs.microsoft.com/appcenter/test-cloud) risolve questo particolare problema verificando le app direttamente su centinaia di dispositivi fisici. Gli sviluppatori scrivono test di accettazione automatici che consentono di eseguire potenti test dell'interfaccia utente. Una volta caricati i test in App Center, il server CI potrà eseguirli automaticamente come parte di un processo CI come illustrato nel diagramma seguente:

[![](intro-to-ci-images/intro02-small.png "Once these tests are uploaded to App Center, the CI server can run them automatically as part of a CI process as shown in this diagram")](intro-to-ci-images/intro02.png#lightbox)

## <a name="components-of-continuous-integration"></a>Componenti dell'integrazione continua

È disponibile un vasto ecosistema di strumenti commerciali e open source progettati per supportare CI. In questa sezione vengono illustrate alcune delle più comuni.

### <a name="version-control"></a>Controllo della versione

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps e Team Foundation Server

[Azure DevOps](https://azure.microsoft.com/services/devops/) e [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) sono gli strumenti di collaborazione Microsoft per i servizi di compilazione di integrazione continua, il monitoraggio delle attività, la pianificazione agile e gli strumenti per la creazione di report e il controllo della versione. Con il controllo della versione, Azure DevOps e TFS possono funzionare con il proprio sistema (controllo della versione di Team Foundation o TFVC) o con progetti ospitati in GitHub.

- Azure DevOps offre servizi tramite il cloud. Il suo vantaggio principale è che non richiede alcun hardware o infrastruttura dedicata ed è possibile accedervi da qualsiasi luogo attraverso i Web browser e tramite gli strumenti di sviluppo più diffusi, ad esempio Visual Studio, rendendoli accattivanti per i team distribuiti geograficamente . È gratuito per team di cinque sviluppatori o meno, dopo i quali è possibile acquistare licenze aggiuntive per ospitare un team in continua crescita.
- TFS è progettato per i server Windows locali ed è accessibile tramite una rete locale o una connessione VPN alla rete. Il suo vantaggio principale è il controllo completo della configurazione dei server di compilazione e l'installazione di qualsiasi altro software o servizio necessario. TFS offre un'edizione gratuita Express a livello di voce per piccoli team.

Sia TFS che Azure DevOps sono strettamente integrati con Visual Studio e consentono agli sviluppatori di eseguire molte attività di controllo della versione e CI dalla comodità di un singolo IDE. È disponibile anche il plug-in Team Explorer Everywhere per Eclipse (vedere di seguito). Visual Studio per Mac è [disponibile un'anteprima di TFVC](/visualstudio/mac/tf-version-control/).

Le [pipeline di Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/) hanno il supporto diretto per i progetti Xamarin, in cui è possibile creare una definizione di compilazione per ogni piattaforma a cui si vuole fare riferimento (Android, iOS e Windows). Per ogni definizione di compilazione è necessaria la licenza Xamarin appropriata. A questo scopo, è anche possibile connettere un server di compilazione TFS che supporta Xamarin locale a Azure DevOps. Con questa configurazione, le compilazioni accodate ad Azure DevOps verranno delegate al server locale. Per informazioni dettagliate, fare riferimento agli [agenti di compilazione e rilascio](https://docs.microsoft.com/azure/devops/pipelines/agents/agents). In alternativa, è possibile usare un altro strumento di compilazione, ad esempio Jenkins o team City.

Un riepilogo completo di tutte le funzionalità di Application Lifecycle Management (ALM) di Visual Studio, Azure DevOps e Team Foundation Server, vedere [DevOps con le app Xamarin](https://docs.microsoft.com/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps).

#### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) offre la potenza di Team Foundation Server e Azure DevOps ai team che sviluppano all'esterno di Visual Studio. Consente agli sviluppatori di connettersi ai progetti team in locale o nel cloud da Eclipse o il client da riga di comando multipiattaforma per OS X e Linux. Team Explorer Everywhere fornisce accesso completo al controllo della versione (inclusi git), agli elementi di lavoro e alle funzionalità di compilazione per le piattaforme non Windows.

#### <a name="git"></a>Git

[Git](https://git-scm.com) è una diffusa soluzione open source di controllo della versione sviluppata originariamente per gestire il codice sorgente per il kernel Linux. Si tratta di un sistema molto veloce e flessibile, molto diffuso con progetti software di tutte le dimensioni. Si adatta facilmente a singoli sviluppatori con accesso a Internet scarso a team di grandi dimensioni che si estendono a tutto il mondo. Git rende molto semplice la diramazione, che a sua volta può incoraggiare flussi paralleli di sviluppo con rischi minimi.

Git può funzionare interamente tramite Web browser o [client GUI](https://git-scm.com/downloads/guis) eseguiti in Linux, Mac OSX e Windows. È gratuito per i repository pubblici; per i repository privati è necessario un [piano a pagamento](https://github.com/pricing).

Le versioni correnti di Visual Studio per Windows e Mac forniscono il supporto nativo per git. Microsoft fornisce un' [estensione scaricabile per git](https://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c) per le versioni precedenti di Visual Studio. Come indicato in precedenza, Azure DevOps e TFS possono usare git per il controllo della versione invece di TFVC.

#### <a name="subversion"></a>Subversion

[Subversion (SVN](https://subversion.apache.org) ) è un sistema di controllo della versione open source molto diffuso che è stato usato a partire da 2000. SVN viene eseguito in tutte le versioni moderne di OS X, Windows, FreeBSD, Linux e UNIX. Visual Studio per Mac dispone del supporto nativo per SVN. Sono disponibili estensioni di terze parti che portano il supporto SVN a Visual Studio.

### <a name="continuous-integration-environments"></a>Ambienti di integrazione continua

La configurazione di un ambiente di integrazione continua significa combinare un sistema di controllo della versione con un servizio di compilazione.  Per questi ultimi, i due più comuni sono:

- [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/) è il sistema di compilazione di Azure DEVOPS e TFS. È strettamente integrato con Visual Studio, che rende più semplice per gli sviluppatori attivare le compilazioni, eseguire automaticamente i test e visualizzare i risultati.
- Jenkins è un server CI open source con un ampio ecosistema di plug-in per supportare tutti i tipi di sviluppo del software. Viene eseguito in Windows e Mac OS X. Jenkins non è integrato con alcun IDE specifico. Viene invece configurato e gestito tramite un'interfaccia Web. Jenkins CI è anche facile da installare e configurare, rendendolo più interessante per i team di piccole dimensioni.

È possibile usare il DevOps TFS/Azure autonomamente oppure è possibile usare Jenkins insieme a TFS/Azure DevOps o Git come descritto nelle sezioni seguenti.

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps e Team Foundation Server

Come illustrato, Azure DevOps e Team Foundation Server fornisce servizi di controllo della versione e di compilazione. I servizi di compilazione richiedono sempre una licenza Xamarin Business o Enterprise per ogni piattaforma di destinazione.

Con Azure DevOps è possibile creare una definizione di compilazione separata per ogni piattaforma di destinazione e immettere la licenza appropriata. Una volta configurata, Azure DevOps eseguirà le compilazioni e i test nel cloud. Per ulteriori informazioni, vedere [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/) .

Con Team Foundation Server, configurare un computer di compilazione come indicato di seguito per piattaforme di destinazione specifiche:

- **Android e Windows:** Installare Visual Studio e gli strumenti Xamarin (per Android e Windows) e configurarli con le licenze di Xamarin. È anche necessario spostare il Android SDK in un percorso condiviso sul server in cui è possibile trovare l'agente di compilazione TFS. Per informazioni dettagliate, vedere [configurazione di TFVC](https://docs.microsoft.com/azure/devops/repos/tfvc/overview).
- **iOS e Xamarin:** Installare Visual Studio e gli strumenti di Xamarin in Windows Server con la licenza appropriata. Installare quindi Visual Studio per Mac in una macchina Mac OS X accessibile alla rete, che fungerà da host di compilazione e creerà il pacchetto dell'app finale (IPA per iOS, APP per OS X).

Il diagramma seguente illustra questa topografia:

[![](intro-to-ci-images/intro03-small.png "This diagram illustrates this topography")](intro-to-ci-images/intro03.png#lightbox)

È anche possibile collegare un server TFS locale a un progetto DevOps di Azure in modo che le compilazioni di Azure DevOps siano delegate al server locale. Per informazioni dettagliate, vedere [Build and release Agents](https://docs.microsoft.com/azure/devops/pipelines/agents/agents/).

#### <a name="azure-devops-and-jenkins"></a>Azure DevOps e Jenkins

Se si usa Jenkins per compilare le app, è possibile archiviare il codice in Azure DevOps o Team Foundation Server e continuare a usare Jenkins per le compilazioni CI. È possibile attivare una compilazione di Jenkins quando si effettua il push del codice nel repository git del progetto team o quando si archivia il codice in TFVC. Per informazioni dettagliate, vedere [Jenkins con Azure DevOps](https://docs.microsoft.com/azure/devops/service-hooks/services/jenkins).

[![](intro-to-ci-images/intro04-small.png "If you use Jenkins to build your apps, you can store your code in Azure DevOps or Team Foundation Server and continue to use Jenkins for your CI builds")](intro-to-ci-images/intro04.png#lightbox)

#### <a name="git-and-jenkins"></a>Git e Jenkins

Un altro ambiente CI comune può essere basato interamente su OS X. Questo scenario prevede l'uso di Git per il controllo del codice sorgente e di Jenkins per il server di compilazione. Entrambi sono in esecuzione in un singolo computer Mac OS X con Visual Studio per Mac installato. Questa operazione è molto simile all'ambiente Azure DevOps + Jenkins illustrato nella sezione precedente:

[![](intro-to-ci-images/intro05-small.png "This is very similar to the Azure DevOps + Jenkins environment discussed in the previous section")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Jenkins [non è supportato da Microsoft](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md).**
