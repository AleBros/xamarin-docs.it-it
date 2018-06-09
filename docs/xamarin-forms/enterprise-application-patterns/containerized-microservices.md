---
title: Nei contenitori Microservizi
description: In questo capitolo viene illustrato come utilizzare i contenitori e microservizi per compilare agile, scalabile e le applicazioni cloud moderne affidabile.
ms.prod: xamarin
ms.assetid: 5872ad92-04e0-4f1a-9691-79d5602f5683
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 33be84bc17f72c8b70d117a0742b001f1f763d3d
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242261"
---
# <a name="containerized-microservices"></a>Nei contenitori Microservizi

Sviluppo di applicazioni client-server ha comportato l'attenzione viene posta sulla compilazione di applicazioni a più livelli che usano le tecnologie specifiche in ogni livello. Tali applicazioni sono spesso detto *monolitico* applicazioni e vengono inclusi nel pacchetto su hardware pre-ridimensionato per carichi di picco. I principali svantaggi di questo approccio di sviluppo sono la stretta integrazione tra i componenti all'interno di ogni livello, che singoli componenti non possono essere scalati facilmente e il costo del test. Un aggiornamento semplice può avere effetti imprevisti sul resto del livello, quindi una modifica a un componente dell'applicazione richiede il livello di intera ripetizione e la ridistribuzione.

In particolare relative era del cloud, sia i singoli componenti non possono essere ridimensionato. Un'applicazione monolitica contiene funzionalità specifiche del dominio e in genere viene diviso per i livelli funzionali, ad esempio l'archiviazione dei dati, logica di business e front-end. Un'applicazione monolitica viene ridimensionata clonando l'intera applicazione in più computer, come illustrato nella figura 8-1.

![](containerized-microservices-images/monolithicapp.png "Approccio monolitico applicazione")

**Figura 8-1**: applicazione monolitico scalabilità

## <a name="microservices"></a>Microservizi

Microservizi offrono un approccio diverso per lo sviluppo di applicazioni e la distribuzione, un approccio adatto per la flessibilità, scala e requisiti di affidabilità delle applicazioni cloud moderne. Un'applicazione di microservizi viene scomposto in componenti indipendenti che interagiscono per fornire funzionalità complessiva dell'applicazione. Il termine di microservizio evidenzia che applicazioni devono essere composto dai servizi sufficientemente ridotto in modo da riflettere problemi indipendenti, in modo che ogni microservizio implementa una singola funzione. Inoltre, ogni microservizio ha contratti ben definiti in modo che altri microservizi possono comunicare e condividere dati. Esempi tipici di microservizi includono carrelli, l'elaborazione di inventario, sottosistemi e l'elaborazione dei pagamenti di acquisto.

Microservizi possano scalare in orizzontale in modo indipendente rispetto alle applicazioni monolitiche grandi dimensioni che si adattano insieme. Ciò significa che un'area funzionale specifica, che richiede l'elaborazione dell'alimentazione o rete larghezza di banda per supportare richiesta, può essere scalata anziché inutilmente la scalabilità orizzontale altre aree dell'applicazione. Figura 8-2 di seguito viene illustrato questo approccio, microservizi in cui vengono distribuiti e scalati in modo indipendente, creazione di istanze di servizi tra più computer.

![](containerized-microservices-images/microservicesapp.png "Approccio di scalabilità dell'applicazione di Microservizi")

**Figura 8-2**: applicazione di Microservizi scalabilità

È possibile quasi istantaneo, consentendo di un'applicazione in grado di adattarsi a carichi Microservizio scalabilità orizzontale. Ad esempio, un singolo microservizio nella funzionalità di un'applicazione basata sul web potrebbe essere il microservizio solo nell'applicazione che necessita di scalabilità orizzontale per gestire il traffico in ingresso aggiuntivo.

Il modello classico per la scalabilità dell'applicazione è per un livello con bilanciamento del carico, senza stato con un archivio dati esterno condiviso per archiviare i dati persistenti. Informazioni sullo stato microservizi gestiscono i propri dati permanenti, in genere archiviati in locale nei server in cui si trovano, per evitare il sovraccarico della rete accesso e la complessità della tra servizio operazioni. Questo consente un'elaborazione più veloce possibile di dati e consente di eliminare la necessità di sistemi di memorizzazione nella cache. Inoltre, scalabile microservizi con stati in genere partizionare i dati tra le istanze, per gestire la velocità effettiva di dimensioni e il trasferimento dei dati oltre la quale può supportare un singolo server.

Microservizi supportano anche gli aggiornamenti indipendenti. Questo accoppiamento debole tra microservizi fornisce un'evoluzione applicazione rapido e affidabile. La natura distribuita, indipendente supporta gli aggiornamenti in sequenza, in cui solo un subset di istanze di un singolo microservizio verrà aggiornato in qualsiasi momento. Pertanto, se viene rilevato un problema, un aggiornamento a può essere rollback, prima di aggiornano tutte le istanze con la configurazione o codice non corretto. Analogamente, microservizi in genere utilizzano controllo delle versioni dello schema, in modo che i client di visualizzare una versione coerenza quando vengono applicati gli aggiornamenti, indipendentemente dal fatto che microservizio è comunicare istanza.

Pertanto, applicazioni microservizio offrono molti vantaggi rispetto alle applicazioni monolitiche:

-   Ogni microservizio è relativamente piccolo, semplice da gestire e sviluppare.
-   Ogni microservizio può essere sviluppato e distribuito in modo indipendente da altri servizi.
-   Ogni microservizio può essere scalato orizzontalmente in modo indipendente. Catalogo o del servizio del carrello acquisti, ad esempio, potrebbe essere necessario essere più di un servizio di ordinazione di scalabilità orizzontale. Pertanto, l'infrastruttura risulta utilizzerà in modo più efficiente le risorse quando si distribuisce orizzontalmente.
-   Ogni microservizio consente di isolare i problemi. Ad esempio, se si verifica un problema in un servizio solo impatto che il servizio. Gli altri servizi possono continuare a gestire le richieste.
-   Ogni microservizio è possibile utilizzare le tecnologie più recenti. Poiché microservizi sono autonomi e l'esecuzione side-by-side, le tecnologie e i framework più recente sono utilizzabile, anziché utilizzare un framework precedente che potrebbe essere utilizzato da un'applicazione monolitica.

Tuttavia, una soluzione di base microservizio dispone anche di potenziali svantaggi:

-   Scegliere la modalità di partizionamento di un'applicazione in microservizi può essere difficile, poiché ogni microservizio deve essere completamente autonomo, end-to-end, compresa la responsabilità per le origini dati.
-   Gli sviluppatori devono implementare la comunicazione tra servizio, che aggiunge complessità e la latenza per l'applicazione.
-   Le transazioni atomiche tra più microservizi in genere non sono possibili. Pertanto, requisiti aziendali devono adottare la coerenza eventuale tra microservizi.
-   Nell'ambiente di produzione, è una complessità operativa nella distribuzione e gestione di un sistema compromesso di numerosi servizi indipendenti.
-   Comunicazione client-a-microservizio diretta può rendere difficile effettuare il refactoring i contratti di microservizi. Ad esempio, nel corso del tempo modalità di partizionamento in servizi di sistema potrebbe essere necessario modificare. Un singolo servizio può suddividere in due o più servizi, e due servizi potrebbe essere di tipo merge. Quando i client comunicano direttamente con microservizi, questa operazione di refactoring può interrompere la compatibilità con le applicazioni client.

## <a name="containerization"></a>Creazione di contenitori

Containerizzazione è un approccio allo sviluppo del software in cui un'applicazione e il controllo delle versioni di set di dipendenze, nonché la configurazione dell'ambiente estratti come file manifesto di distribuzione, vengono inseriti come un'immagine contenitore, verificata come un'unità, e distribuire un sistema operativo host.

Un contenitore è un, portabile e controllato ambiente operativo isolato risorse, in cui è possibile eseguire un'applicazione senza interessare le risorse di altri contenitori, o l'host. Pertanto, un contenitore di aspetto e funziona come una macchina virtuale o di un computer fisico appena installato.

Sono presenti molte somiglianze tra i contenitori e macchine virtuali, come illustrato nella figura 8-3.

![](containerized-microservices-images/containersvsvirtualmachines.png "Approccio di scalabilità dell'applicazione di Microservizi")

**Figura 8-3**: confronto tra le macchine virtuali e contenitori

Un contenitore esegue un sistema operativo, dispone di un file system e accessibile in rete come se fosse un computer fisico o macchina virtuale. Tuttavia, la tecnologia e i concetti usati dai contenitori sono molto diversi dalle macchine virtuali. Macchine virtuali includono le applicazioni, le dipendenze necessarie e un sistema operativo completo. Contenitori includono l'applicazione e le relative dipendenze, ma il sistema operativo di condividere con gli altri contenitori, in esecuzione come processi isolati nel sistema operativo host (a parte i contenitori di Hyper-V che vengono eseguiti all'interno di una speciale macchina virtuale per ogni contenitore). Di conseguenza, i contenitori condividono le risorse e in genere richiedono meno risorse rispetto a macchine virtuali.

Il vantaggio di un approccio di sviluppo e distribuzione orientata ai servizi contenitore è che elimina la maggior parte dei problemi riscontrati dalle impostazioni di ambiente non coerente e i problemi relativi. Inoltre, contenitori consentono la funzionalità di scalabilità verticale rapido dell'applicazione da istanze nuovi contenitori come richiesto.

I concetti chiave durante la creazione e utilizzo dei contenitori sono:

-   Host contenitore: La macchina virtuale o fisica configurato per i contenitori di host. L'host contenitore esegue uno o più contenitori.
-   Immagine contenitore: Un'immagine è costituito da un'unione di file System a più livelli sovrapposti e costituisce la base di un contenitore. Un'immagine non dispone di stato e non cambia mai, che viene distribuito in ambienti diversi.
-   Contenitore: Un contenitore è un'istanza di runtime di un'immagine.
-   Immagine del sistema operativo contenitore: Contenitori vengono distribuiti dalle immagini. Immagine del sistema operativo contenitore è il primo livello che costituiscono un contenitore di molti potenziali livelli dell'immagine. Un sistema operativo contenitore è immutabile e non può essere modificato.
-   Repository del contenitore: Ogni volta che viene creata un'immagine contenitore, l'immagine e le relative dipendenze vengono archiviati in un repository locale. Queste immagini possono essere riusate più volte nell'host contenitore. Le immagini contenitore possono essere archiviate anche in un registro di sistema pubblico o privato, ad esempio [Hub Docker](https://hub.docker.com/), in modo che possono essere utilizzati tra gli host contenitore diverso.

Le aziende siano adottando sempre più i contenitori quando implementazione microservizio basato su applicazioni, e Docker è diventata l'implementazione di un contenitore standard che è stata adottata la maggior parte delle piattaforme software e i fornitori di cloud.

L'applicazione di riferimento eShopOnContainers Usa Docker per ospitare quattro microservizi di back-end nei contenitori, come illustrato nella figura 8-4.

![](containerized-microservices-images/microservicesarchitecture.png "Fare riferimento a eShopOnContainers microservizi back-end dell'applicazione")

**Figura 8-4**: eShopOnContainers riferimento applicazione back-end microservizi

L'architettura di servizi back-end dell'applicazione di riferimento viene scomposto in sottosistemi più autonomi sotto forma di microservizi in collaborazione e di contenitori. Ogni microservizio fornisce un'unica area di funzionalità: un servizio di identità, un servizio di catalogo, un servizio di ordinamento e un servizio carrello.

Ogni microservizio dispone del proprio database, in modo che possa essere completamente separato da altri microservizi il. Se necessario, la coerenza tra i database da diversi microservizi viene eseguita mediante gli eventi a livello di applicazione. Per ulteriori informazioni, vedere [la comunicazione tra Microservizi](#communication_between_microservices).

Per ulteriori informazioni sull'applicazione di riferimento, vedere [Microservizi .NET: architettura per le applicazioni .NET contenitore](https://aka.ms/microservicesebook).

<a name="communication_between_client_and_microservices" />

## <a name="communication-between-client-and-microservices"></a>Comunicazione tra Client e Microservizi

L'app mobile eShopOnContainers comunica con il microservizi nei contenitori di back-end mediante *diretto client-a-microservizio* comunicazione, come illustrata nella figura 8-5.

![](containerized-microservices-images/directclienttomicroservicecommunication.png "Approccio di scalabilità dell'applicazione di Microservizi")

**Figura 8-5**: la comunicazione client-a-microservizio diretta

Con la comunicazione client-a-microservizio diretta, l'app mobile effettua richieste per ogni microservizio direttamente tramite endpoint pubblici, con una porta TCP diversa per ogni microservizio. Nell'ambiente di produzione, l'endpoint consente in genere il mapping a bilanciamento del carico del microservizio, che distribuisce le richieste tra le istanze disponibili.

> [!TIP]
> Prendere in considerazione l'uso della comunicazione gateway API. Comunicazione client-a-microservizio diretta può avere svantaggi durante la creazione di un microservizio grande e complessi basati su applicazione, ma è più che sufficiente per una piccola applicazione. Quando si progetta un microservizio di grandi dimensioni, l'applicazione con decine di microservizi basata su, prendere in considerazione l'uso della comunicazione gateway API. Per ulteriori informazioni, vedere [Microservizi .NET: architettura per le applicazioni .NET contenitore](https://aka.ms/microservicesebook).

<a name="communication_between_microservices" />

## <a name="communication-between-microservices"></a>Comunicazione tra Microservizi

Un'applicazione di microservizi basata è un sistema distribuito, potenzialmente in esecuzione su più computer. Ogni istanza del servizio è in genere un processo. Di conseguenza, i servizi devono interagire utilizzando un protocollo di comunicazione tra processi, ad esempio HTTP, TCP, Advanced Message Queuing Protocol (AMQP) o i protocolli binari, a seconda della natura di ogni servizio.

I due approcci comuni per la comunicazione microservizio-a-microservizio sono comunicazione REST basata su HTTP quando si eseguono query per i dati e leggero messaggistica asincrona durante la comunicazione degli aggiornamenti tra più microservizi.

Messaggistica basata su basata sugli eventi comunicazione asincrona è fondamentale per la propagazione delle modifiche tra più microservizi. Con questo approccio, un microservizio pubblica un evento quando un elemento rilevanti si verifica, ad esempio, quando aggiorna un'entità di business. Altri microservizi sottoscrivono di tali eventi. Quindi, quando un microservizio riceve un evento, aggiorna il proprio entità aziendali, che a sua volta potrebbe causare più eventi in corso di pubblicazione. Questa pubblicazione-sottoscrizione funzionalità viene in genere ottenuta con un bus di eventi.

Un bus di eventi consente la comunicazione tra microservizi, senza i componenti da tenere in modo esplicito, come illustrato nella figura 8-6 di pubblicazione-sottoscrizione.

![](containerized-microservices-images/eventbus.png "Pubblicazione-sottoscrizione con un bus di eventi")

**Figura 8-6:** pubblicazione-sottoscrizione con un bus di eventi

Dal punto di vista dell'applicazione, il bus di eventi è semplicemente una pubblicazione-sottoscrizione canale esposto tramite un'interfaccia. Tuttavia, il modo in cui che viene implementato il bus di eventi può variare. Ad esempio, un'implementazione del bus di eventi è possibile utilizzare altri bus di servizio, ad esempio NServiceBus e MassTransit RabbitMQ o Azure Service Bus. Figura 8-7 viene illustrato come utilizzare un bus di eventi dell'applicazione di riferimento eShopOnContainers.

![](containerized-microservices-images/microservicesarchitecturewitheventbus.png "Comunicazione asincrona basata sugli eventi dell'applicazione di riferimento")

**Figura 8-7:** comunicazione asincrona basata sugli eventi dell'applicazione di riferimento

Il bus di eventi eShopOnContainers, implementato tramite RabbitMQ, fornisce funzionalità di pubblicazione-sottoscrizione uno-a-molti asincrono. Ciò significa che dopo la pubblicazione di un evento, possono essere presenti più sottoscrittori in attesa dell'evento stesso. Figura 8-9 viene illustrata questa relazione.

![](containerized-microservices-images/eventdrivencommunication.png "Comunicazione uno-a-molti")

**Figura 8-9**: uno-a-molti comunicazione

Questo approccio comunicazione uno-a-molti utilizza gli eventi per implementare le transazioni di business che si estendono su più servizi, verificare la coerenza eventuale tra i servizi. Un eventuale coerente per le transazioni è costituito da una serie di passaggi distribuiti. Pertanto, quando microservizio il profilo utente riceve il comando UpdateUser, aggiorna i dettagli dell'utente nel proprio database e pubblica l'evento UserUpdated il bus di eventi. Sia il microservizio carrello e l'ordinamento microservizio sottoscritti per la ricezione di questo evento e in risposta aggiornamento delle informazioni relative buyer nei rispettivi database.

> [!NOTE]
> Il bus di eventi eShopOnContainers, implementato tramite RabbitMQ, deve essere utilizzato solo come un modello di prova. Per i sistemi di produzione, è necessario considerare le implementazioni del bus evento alternativo.

Per informazioni sull'implementazione del bus di eventi, vedere [Microservizi .NET: architettura per le applicazioni .NET contenitore](https://aka.ms/microservicesebook).

## <a name="summary"></a>Riepilogo

Microservizi offrono un approccio di sviluppo di applicazioni e di distribuzione che è adatta ai requisiti di flessibilità, scalabilità e affidabilità delle applicazioni cloud moderne. Uno dei principali vantaggi di microservizi è che possono essere scalate orizzontalmente in modo indipendente, il che significa che un'area funzionale specifica può essere scalata che richiede l'elaborazione dell'alimentazione o rete larghezza di banda per supportare la domanda, senza ridimensionamento inutilmente aree della l'applicazione in cui non si verifica un aumento della domanda.

Un contenitore è un, portabile e controllato ambiente operativo isolato risorse, in cui è possibile eseguire un'applicazione senza interessare le risorse di altri contenitori, o l'host. Le aziende siano adottando sempre più i contenitori quando implementazione microservizio basato su applicazioni, e Docker è diventata l'implementazione di un contenitore standard che è stata adottata la maggior parte delle piattaforme software e i fornitori di cloud.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
