---
title: Microservizi in contenitori
description: Questo capitolo illustra come usare i microservizi e i contenitori per creare applicazioni cloud moderne agili, scalabili e affidabili.
ms.prod: xamarin
ms.assetid: 5872ad92-04e0-4f1a-9691-79d5602f5683
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: dc71da512519cdd7fcc56df1ff987ffbc1354663
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760399"
---
# <a name="containerized-microservices"></a>Microservizi in contenitori

Lo sviluppo di applicazioni client-server ha determinato la creazione di applicazioni a più livelli che usano tecnologie specifiche in ogni livello. Tali applicazioni sono spesso denominate applicazioni *monolitiche* e vengono assemblate in un pacchetto hardware pre-ridimensionato per i picchi di carico. Gli svantaggi principali di questo approccio di sviluppo sono l'accoppiamento stretto tra i componenti all'interno di ogni livello, che i singoli componenti non possono essere facilmente ridimensionati e il costo dei test. Un semplice aggiornamento può avere effetti imprevisti sul resto del livello e pertanto una modifica a un componente dell'applicazione richiede che l'intero livello venga ritestato e ridistribuito.

In particolare per quanto riguarda l'età del cloud, i singoli componenti non possono essere facilmente ridimensionati. Un'applicazione monolitica contiene funzionalità specifiche del dominio ed è in genere divisa per i livelli funzionali, ad esempio front-end, la logica di business e l'archiviazione di dati. Un'applicazione monolitica viene ridimensionata clonando l'intera applicazione su più computer, come illustrato nella figura 8-1.

![](containerized-microservices-images/monolithicapp.png "Approccio per la scalabilità delle applicazioni monolitiche")

**Figura 8-1**: Approccio per la scalabilità delle applicazioni monolitiche

## <a name="microservices"></a>Microservizi

I microservizi offrono un approccio diverso per lo sviluppo e la distribuzione di applicazioni, un approccio adatto ai requisiti di agilità, scalabilità e affidabilità delle applicazioni cloud moderne. Un'applicazione di microservizi è scomposta in componenti indipendenti che interagiscono per fornire la funzionalità complessiva dell'applicazione. Il termine microservizio sottolinea che le applicazioni devono essere costituite da servizi sufficientemente piccoli da riflettere i problemi indipendenti, in modo che ogni microservizio implementi una singola funzione. Ogni microservizio dispone inoltre di contratti ben definiti, in modo che altri microservizi possano comunicare e condividere dati con esso. Esempi tipici di microservizi sono i carrelli acquisti, l'elaborazione dell'inventario, i sottosistemi di acquisto e l'elaborazione dei pagamenti.

I microservizi possono essere scalati in modo indipendente rispetto alle applicazioni monolitiche gigantesche che si ridimensionano insieme. Ciò significa che un'area funzionale specifica, che richiede una maggiore potenza di elaborazione o la larghezza di banda di rete per supportare la richiesta, può essere ridimensionata piuttosto che in modo inutilmente scalabile in altre aree dell'applicazione. La figura 8-2 illustra questo approccio, in cui i microservizi vengono distribuiti e ridimensionati in modo indipendente, creando istanze di servizi tra computer.

![](containerized-microservices-images/microservicesapp.png "Approccio per la scalabilità delle applicazioni di microservizi")

**Figura 8-2**: Approccio per la scalabilità delle applicazioni di microservizi

La scalabilità orizzontale del microservizio può essere quasi istantanea, consentendo a un'applicazione di adattarsi ai carichi modificabili. Ad esempio, un singolo microservizio nella funzionalità Web di un'applicazione può essere l'unico microservizio nell'applicazione che deve essere scalato in orizzontale per gestire il traffico in ingresso aggiuntivo.

Il modello classico per la scalabilità delle applicazioni è quello di avere un livello senza stato con carico bilanciato con un archivio dati esterno condiviso per archiviare i dati persistenti. I microservizi con stato gestiscono i propri dati persistenti, in genere archiviati localmente nei server in cui sono posizionati, per evitare il sovraccarico dell'accesso alla rete e la complessità delle operazioni tra servizi. In questo modo è possibile elaborare i dati in modo più rapido ed eliminare la necessità di utilizzare i sistemi di memorizzazione nella cache. Inoltre, i microservizi con stato scalabile in genere partizionano i dati tra le istanze, per gestire le dimensioni dei dati e trasferire la velocità effettiva oltre la quale un singolo server può supportare.

I microservizi supportano anche gli aggiornamenti indipendenti. Questo accoppiamento libero tra microservizi offre un'evoluzione rapida e affidabile dell'applicazione. La natura distribuita indipendente supporta gli aggiornamenti in sequenza, in cui solo un subset di istanze di un singolo microservizio viene aggiornato in un determinato momento. Di conseguenza, se viene rilevato un problema, è possibile eseguire il rollback di un aggiornamento bacato prima che tutte le istanze vengano aggiornate con la configurazione o il codice difettoso. Analogamente, i microservizi usano in genere il controllo delle versioni dello schema, in modo che i client visualizzino una versione coerente quando vengono applicati gli aggiornamenti, indipendentemente dall'istanza di microservizio con cui viene eseguita la comunicazione.

Pertanto, le applicazioni di microservizi presentano molti vantaggi rispetto alle applicazioni monolitiche:

- Ogni microservizio è relativamente piccolo, facile da gestire e sviluppare.
- Ogni microservizio può essere sviluppato e distribuito in modo indipendente da altri servizi.
- Ogni microservizio può essere scalato orizzontalmente in modo indipendente. Ad esempio, potrebbe essere necessario scalare orizzontalmente un servizio di catalogo o un servizio carrello acquisti più di un servizio di ordinamento. Pertanto, l'infrastruttura risultante utilizzerà in modo più efficiente le risorse durante la scalabilità orizzontale.
- Ogni microservizio isola gli eventuali problemi. Se, ad esempio, si verifica un problema in un servizio, esso influisca solo sul servizio. Gli altri servizi possono continuare a gestire le richieste.
- Ogni microservizio può usare le tecnologie più recenti. Poiché i microservizi sono autonomi ed eseguiti side-by-Side, è possibile usare le tecnologie e i Framework più recenti, anziché forzare l'uso di un Framework obsoleto che potrebbe essere usato da un'applicazione monolitica.

Tuttavia, una soluzione basata su microservizi presenta anche potenziali svantaggi:

- La scelta di come partizionare un'applicazione in microservizi può essere complessa, perché ogni microservizio deve essere completamente autonomo, end-to-end, inclusa la responsabilità per le relative origini dati.
- Gli sviluppatori devono implementare la comunicazione tra servizi, che aggiunge complessità e latenza all'applicazione.
- Le transazioni atomiche tra più microservizi non sono in genere possibili. I requisiti aziendali devono pertanto includere la coerenza finale tra i microservizi.
- In produzione esiste una complessità operativa nella distribuzione e nella gestione di un sistema compromesso da molti servizi indipendenti.
- La comunicazione diretta da client a microservizio può rendere difficile effettuare il refactoring dei contratti di microservizi. Nel tempo, ad esempio, potrebbe essere necessario modificare il partizionamento del sistema nei servizi. Un singolo servizio può essere suddiviso in due o più servizi e due servizi possono essere Uniti. Quando i client comunicano direttamente con i microservizi, questo lavoro di refactoring può interrompere la compatibilità con le app client.

## <a name="containerization"></a>Creazione di contenitori

Il contenitore è un approccio allo sviluppo del software in cui un'applicazione e il set di dipendenze con versione, oltre alla configurazione dell'ambiente astratta come file manifesto di distribuzione, vengono assemblati insieme come un'immagine del contenitore, testati come un'unità e distribuito in un sistema operativo host.

Un contenitore è un ambiente operativo isolato, controllato dalle risorse e portabile, in cui un'applicazione può essere eseguita senza toccare le risorse di altri contenitori o l'host. Pertanto, un contenitore sembra e funge da computer fisico appena installato o da una macchina virtuale.

Esistono molte analogie tra contenitori e macchine virtuali, come illustrato nella figura 8-3.

![](containerized-microservices-images/containersvsvirtualmachines.png "Approccio per la scalabilità delle applicazioni di microservizi")

**Figura 8-3**: Confronto tra macchine virtuali e contenitori

Un contenitore esegue un sistema operativo, dispone di un file system ed è possibile accedervi tramite una rete come se fosse una macchina virtuale o fisica. Tuttavia, la tecnologia e i concetti usati dai contenitori sono molto diversi dalle macchine virtuali. Le macchine virtuali includono le applicazioni, le dipendenze richieste e un sistema operativo guest completo. I contenitori includono l'applicazione e le relative dipendenze, ma condividono il sistema operativo con altri contenitori, in esecuzione come processi isolati sul sistema operativo host (a parte i contenitori di Hyper-V che vengono eseguiti all'interno di una macchina virtuale speciale per ogni contenitore). Di conseguenza, i contenitori condividono le risorse e in genere richiedono un minor numero di risorse rispetto alle macchine virtuali.

Il vantaggio di un approccio di sviluppo e distribuzione orientato ai contenitori è la possibilità di eliminare la maggior parte dei problemi derivanti da configurazioni dell'ambiente incoerenti e dai problemi che si verificano. I contenitori consentono inoltre di velocizzare le funzionalità di scalabilità verticale dell'applicazione creando istanze di nuovi contenitori in base alle esigenze.

I concetti chiave per la creazione e l'uso dei contenitori sono i seguenti:

- Host contenitore: Macchina virtuale o fisica configurata per ospitare i contenitori. L'host contenitore eseguirà uno o più contenitori.
- Immagine del contenitore: Un'immagine è costituita da un'Unione di filesystem sovrapposti sovrapposti tra loro ed è la base di un contenitore. Un'immagine non ha lo stato e non cambia mai mentre viene distribuita in ambienti diversi.
- Contenitore Un contenitore è un'istanza di runtime di un'immagine.
- Immagine del sistema operativo del contenitore: I contenitori vengono distribuiti dalle immagini. L'immagine del sistema operativo del contenitore è il primo livello in un numero potenzialmente elevato di livelli di immagine che costituiscono un contenitore. Un sistema operativo contenitore non è modificabile e non può essere modificato.
- Repository contenitore: Ogni volta che viene creata un'immagine contenitore, l'immagine e le relative dipendenze vengono archiviate in un repository locale. Queste immagini possono essere riutilizzate più volte nell'host contenitore. Le immagini del contenitore possono essere archiviate anche in un registro di sistema pubblico o privato, ad esempio l' [Hub Docker](https://hub.docker.com/), in modo che possano essere usate in diversi host contenitore.

Le aziende stanno sempre adottando contenitori quando implementano applicazioni basate su microservizi e Docker è diventato l'implementazione standard del contenitore che è stata adottata dalla maggior parte delle piattaforme software e dai fornitori di cloud.

L'applicazione di riferimento eShopOnContainers USA Docker per ospitare quattro microservizi back-end in contenitori, come illustrato nella figura 8-4.

![](containerized-microservices-images/microservicesarchitecture.png "eShopOnContainers riferimento a microservizi back-end dell'applicazione")

**Figura 8-4**: microservizi back-end dell'applicazione di riferimento per eShopOnContainers

L'architettura dei servizi back-end nell'applicazione di riferimento è scomposta in più sottosistemi autonomi sotto forma di collaborazione tra microservizi e contenitori. Ogni microservizio fornisce un'unica area di funzionalità: un servizio di identità, un servizio di catalogo, un servizio di ordinamento e un servizio basket.

Ogni microservizio dispone di un proprio database, in modo che sia completamente separato dagli altri microservizi. Laddove necessario, la coerenza tra i database di diversi microservizi viene eseguita usando gli eventi a livello di applicazione. Per altre informazioni, vedere [comunicazione tra microservizi](#communication_between_microservices).

Per ulteriori informazioni sull'applicazione di riferimento, vedere [microservizi .NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservizi .NET. Architettura per le applicazioni .NET incluse in contenitori).

<a name="communication_between_client_and_microservices" />

## <a name="communication-between-client-and-microservices"></a>Comunicazione tra client e microservizi

L'app per dispositivi mobili eShopOnContainers comunica con i microservizi back-end in contenitori usando la comunicazione *da client a microservizio diretta* , come illustrato nella figura 8-5.

![](containerized-microservices-images/directclienttomicroservicecommunication.png "Approccio per la scalabilità delle applicazioni di microservizi")

**Figura 8-5**: Comunicazione da client a microservizio diretta

Con la comunicazione da client a microservizio diretta, l'app per dispositivi mobili effettua richieste a ogni microservizio direttamente tramite il relativo endpoint pubblico, con una porta TCP diversa per ogni microservizio. In produzione, l'endpoint viene in genere mappato al servizio di bilanciamento del carico del microservizio, che distribuisce le richieste tra le istanze disponibili.

> [!TIP]
> Si consiglia di usare la comunicazione del gateway API. La comunicazione da client a microservizio diretta può presentare svantaggi quando si compila un'applicazione basata su microservizi di grandi dimensioni e complesse, ma è più che adeguata per una piccola applicazione. Quando si progetta un'applicazione basata su microservizi di grandi dimensioni con decine di microservizi, è consigliabile usare la comunicazione del gateway API. Per altre informazioni, vedere [microservizi .NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservizi .NET. Architettura per le applicazioni .NET incluse in contenitori).

<a name="communication_between_microservices" />

## <a name="communication-between-microservices"></a>Comunicazione tra microservizi

Un'applicazione basata su microservizi è un sistema distribuito, potenzialmente in esecuzione su più computer. Ogni istanza del servizio è in genere un processo. I servizi devono pertanto interagire utilizzando un protocollo di comunicazione tra processi, ad esempio HTTP, TCP, Advance Message Queueing Protocol (AMQP) o protocolli binari, a seconda della natura di ogni servizio.

I due approcci comuni per la comunicazione da microservizio a microservizio sono la comunicazione REST basata su HTTP durante l'esecuzione di query per i dati e la messaggistica asincrona leggera durante la comunicazione di aggiornamenti tra più microservizi.

La comunicazione asincrona basata su eventi è fondamentale durante la propagazione delle modifiche tra più microservizi. Con questo approccio, un microservizio pubblica un evento quando si verifica una situazione rilevante, ad esempio, quando aggiorna un'entità di business. Altri microservizi sottoscrivono questi eventi. Quindi, quando un microservizio riceve un evento, aggiorna le proprie entità di business, che a loro volta possono comportare la pubblicazione di più eventi. Questa funzionalità di pubblicazione-sottoscrizione viene in genere realizzata con un bus di eventi.

Un bus di eventi consente la comunicazione di pubblicazione-sottoscrizione tra microservizi, senza richiedere che i componenti siano consapevoli in modo esplicito gli uni dagli altri, come illustrato nella figura 8-6.

![](containerized-microservices-images/eventbus.png "Pubblicazione-sottoscrizione con un bus di eventi")

**Figura 8-6:** Pubblicazione-sottoscrizione con un bus di eventi

Dal punto di vista dell'applicazione, il bus di eventi è semplicemente un canale di pubblicazione-sottoscrizione esposto tramite un'interfaccia. Tuttavia, il modo in cui viene implementato il bus di eventi può variare. Ad esempio, un'implementazione del bus di eventi può usare RabbitMQ, il bus di servizio di Azure o altri bus di servizio, ad esempio NServiceBus e MassTransit. La figura 8-7 Mostra come viene usato un bus di eventi nell'applicazione di riferimento eShopOnContainers.

![](containerized-microservices-images/microservicesarchitecturewitheventbus.png "Comunicazione asincrona basata su eventi nell'applicazione di riferimento")

**Figura 8-7:** Comunicazione asincrona basata su eventi nell'applicazione di riferimento

Il bus di eventi eShopOnContainers, implementato usando RabbitMQ, offre funzionalità di pubblicazione-sottoscrizione asincrona uno-a-molti. Ciò significa che, dopo la pubblicazione di un evento, possono essere presenti più Sottoscrittori in ascolto dello stesso evento. La figura 8-9 illustra questa relazione.

![](containerized-microservices-images/eventdrivencommunication.png "Comunicazione uno-a-molti")

**Figura 8-9**: Comunicazione uno-a-molti

Questo approccio di comunicazione uno-a-molti usa gli eventi per implementare transazioni di business che si estendono su più servizi, garantendo la coerenza finale tra i servizi. Una transazione finale coerente è costituita da una serie di passaggi distribuiti. Pertanto, quando il microservizio profilo utente riceve il comando UpdateUser, aggiorna i dettagli dell'utente nel database e pubblica l'evento UserUpdated nel bus di eventi. Sia il microservizio basket che il microservizio degli ordini hanno sottoscritto la ricezione di questo evento e, in risposta, aggiornano le informazioni sull'acquirente nei rispettivi database.

> [!NOTE]
> Il bus di eventi eShopOnContainers, implementato usando RabbitMQ, deve essere usato solo come modello di verifica. Per i sistemi di produzione, è necessario prendere in considerazione le implementazioni del bus di eventi.

Per informazioni sull'implementazione del bus di eventi, [vedere microservizi .NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservizi .NET. Architettura per le applicazioni .NET incluse in contenitori).

## <a name="summary"></a>Riepilogo

I microservizi offrono un approccio per lo sviluppo e la distribuzione di applicazioni, adatti ai requisiti di agilità, scalabilità e affidabilità delle applicazioni cloud moderne. Uno dei vantaggi principali dei microservizi è che è possibile scalare orizzontalmente in modo indipendente, il che significa che è possibile ridimensionare un'area funzionale specifica che richiede una maggiore potenza di elaborazione o larghezza di banda di rete per supportare la richiesta, senza inutilmente ridimensionare le aree di applicazione che non sta riscontrando un aumento della domanda.

Un contenitore è un ambiente operativo isolato, controllato dalle risorse e portabile, in cui un'applicazione può essere eseguita senza toccare le risorse di altri contenitori o l'host. Le aziende stanno sempre adottando contenitori quando implementano applicazioni basate su microservizi e Docker è diventato l'implementazione standard del contenitore che è stata adottata dalla maggior parte delle piattaforme software e dai fornitori di cloud.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
