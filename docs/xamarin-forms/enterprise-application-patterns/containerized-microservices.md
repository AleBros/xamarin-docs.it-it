---
title: Microservizi in contenitori
description: In questo capitolo illustra come usare i microservizi e contenitori per compilare agile, scalabile e applicazioni cloud moderne affidabile.
ms.prod: xamarin
ms.assetid: 5872ad92-04e0-4f1a-9691-79d5602f5683
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 33be84bc17f72c8b70d117a0742b001f1f763d3d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300758"
---
# <a name="containerized-microservices"></a>Microservizi in contenitori

Sviluppo di applicazioni client-server ha comportato una particolare attenzione alla creazione di applicazioni a più livelli che usano tecnologie specifiche in ogni livello. Tali applicazioni sono spesso detta *monolitica* applicazioni e vengono compresse nell'hardware pre-in scala per carichi di picco. Gli svantaggi principali di questo approccio di sviluppo sono l'accoppiamento stretto fra i componenti all'interno di ogni livello, che i singoli componenti non possono essere facilmente ridimensionati e il costo del test. Un semplice aggiornamento può avere effetti imprevisti sul resto del livello, e in modo che una modifica a un componente dell'applicazione richiede il livello di intero ritestata in relazione e ridistribuita.

In particolare destano preoccupazione nell'era del cloud, è che i singoli componenti non possono essere facilmente ridimensionati. Un'applicazione monolitica include funzionalità specifiche del dominio e in genere viene divisa per livelli di funzionalità come front-end, la logica di business e archiviazione dei dati. Un'applicazione monolitica viene ridimensionata clonando l'intera applicazione in più computer, come illustrato nella figura 8-1.

![](containerized-microservices-images/monolithicapp.png "Approccio alla scalabilità dell'applicazione monolitica")

**Figura 8-1**: Approccio alla scalabilità dell'applicazione monolitica

## <a name="microservices"></a>Microservizi

I Microservizi offrono un approccio diverso per lo sviluppo di applicazioni e la distribuzione, un approccio che è adatto per la flessibilità, scalabilità e i requisiti di affidabilità delle applicazioni cloud moderne. Un'applicazione di microservizi è scomposto in componenti indipendenti che interagiscono per offrire la funzionalità complessiva dell'applicazione. Il microservizio del termine evidenzia che applicazioni devono essere composte da servizi sufficientemente ridotto in modo da riflettere preoccupazioni indipendenti, in modo che ogni microservizio implementa una singola funzione. Inoltre, ogni microservizio dispone di contratti ben definiti in modo che altri microservizi possono comunicare e condividere dati con esso. Esempi tipici dei microservizi includono carrelli acquisti, elaborazione di inventario, acquistano sottosistemi e l'elaborazione dei pagamenti.

I Microservizi possono scalare orizzontalmente in modo indipendente rispetto alle applicazioni monolitiche gigante che scalare insieme. Ciò significa che un'area funzionale specifica, che richiede più l'elaborazione dell'alimentazione o rete della larghezza di banda per supportare la domanda, può essere ridimensionata anziché inutilmente la scalabilità orizzontale altre aree dell'applicazione. Figura 8-2 illustra questo approccio, in cui i microservizi sono distribuiti e ridimensionati in modo indipendente, la creazione di istanze dei servizi tra più computer.

![](containerized-microservices-images/microservicesapp.png "Approccio di scalabilità dell'applicazione di Microservizi")

**Figura 8-2**: Approccio di scalabilità dell'applicazione di Microservizi

È possibile quasi istantanei, consentendo un'applicazione in grado di adattarsi a carichi di Microservizio scalabilità orizzontale. Ad esempio, un singolo microservizio nella funzionalità basata sul web di un'applicazione potrebbe essere il microservizio solo nell'applicazione che è necessario scalare orizzontalmente per gestire il traffico in ingresso aggiuntivo.

Il modello classico per la scalabilità dell'applicazione è avere un livello di bilanciamento del carico, senza stato con un archivio dati esterno condiviso per archiviare i dati persistenti. I microservizi con stato gestiscono i propri dati permanenti, in genere archiviandolo in locale nei server in cui vengono posizionati, per evitare il sovraccarico di rete l'accesso e la complessità di cross servizio operazioni. Ciò consente un'elaborazione più veloce possibile di dati e può eliminare la necessità di sistemi di memorizzazione nella cache. Inoltre, i microservizi con stato scalabili in genere partizionare i dati tra le istanze, per gestire la velocità effettiva di dimensioni e il trasferimento dei dati oltre il quale può supportare un singolo server.

I Microservizi supportano anche gli aggiornamenti indipendenti. Questo accoppiamento libero tra i microservizi offre un'evoluzione di applicazioni rapido e affidabile. La natura distribuita, indipendente supporta gli aggiornamenti in sequenza, in cui solo un subset delle istanze di un singolo microservizio verrà aggiornato in qualsiasi momento. Pertanto, se viene rilevato un problema, un aggiornamento difettoso può essere eseguito il rollback, prima di aggiornano tutte le istanze con la configurazione o codice non corretto. Allo stesso modo, i microservizi usano in genere il controllo delle versioni dello schema, in modo che i client di visualizzare una versione coerente quando vengono applicati gli aggiornamenti, indipendentemente dal fatto che microservizio istanza comunicata con.

Di conseguenza, le applicazioni di microservizi offrono molti vantaggi rispetto alle applicazioni monolitiche:

-   Ogni microservizio è relativamente piccolo, semplice da gestire e sviluppare.
-   Ogni microservizio può essere sviluppato e distribuito indipendentemente dagli altri servizi.
-   Ogni microservizio può essere scalato orizzontalmente in modo indipendente. Ad esempio, un servizio di catalogo o servizio carrello della spesa potrebbe essere necessario essere scalata orizzontalmente a più di un servizio di gestione degli ordini. Pertanto, l'infrastruttura risulta utilizzerà in modo più efficiente le risorse quando la scalabilità orizzontale.
-   Ogni microservizio consente di isolare i problemi. Ad esempio, se si verifica un problema in un servizio influisce solo su tale servizio. Gli altri servizi possono continuare a gestire le richieste.
-   Ogni microservizio può usare le tecnologie più recenti. Poiché i microservizi sono autonome ed esecuzione side-by-side, le tecnologie e i framework più recente è utilizzabile, anziché dover usare un framework precedente potrebbe essere utilizzato da un'applicazione monolitica.

Tuttavia, una soluzione basata su microservizi presenta anche potenziali svantaggi:

-   Scelta di come partizionare un'applicazione in microservizi può essere difficile, poiché ogni microservizio deve essere completamente autonomo, end-to-end, compresa la responsabilità per le relative origini dati.
-   Gli sviluppatori devono implementare la comunicazione tra servizi, che aggiunge complessità e la latenza per l'applicazione.
-   Le transazioni atomiche tra più microservizi in genere non sono possibili. Di conseguenza, i requisiti aziendali devono prevedere la coerenza finale tra microservizi.
-   In ambiente di produzione, è presente una complessità operativa nella distribuzione e gestione di un sistema compromesso di molti servizi indipendenti.
-   Comunicazione da client a microservizio diretta può rendere difficile per il refactoring dei contratti dei microservizi. Ad esempio, nel corso del tempo come il sistema è partizionato nei servizi potrebbe essere necessario modificare. Un singolo servizio potrebbe essere suddivisa in due o più servizi e potrebbero unire due servizi. Quando i client comunicano direttamente con i microservizi, queste operazioni di refactoring possono interrompere la compatibilità con le app client.

## <a name="containerization"></a>Creazione di contenitori

La containerizzazione è un approccio allo sviluppo del software in cui un'applicazione e la relativa serie con controllo delle versioni delle dipendenze, più la configurazione dell'ambiente astratti come file manifesto della distribuzione, sono riuniti insieme come un'immagine del contenitore, testata come unità, e distribuita in un sistema operativo host.

Un contenitore è un, resource, portabile e controllato ambiente operativo isolato, in cui è possibile eseguire un'applicazione senza modificare le risorse di altri contenitori o l'host. Pertanto, un contenitore è simile e agisce come un computer fisico appena installato o una macchina virtuale.

Esistono molte analogie tra i contenitori e le macchine virtuali, come illustrato nella figura 8-3.

![](containerized-microservices-images/containersvsvirtualmachines.png "Approccio di scalabilità dell'applicazione di Microservizi")

**Figura 8-3**: Confronto tra le macchine virtuali e contenitori

Un contenitore esegue un sistema operativo, dispone di un file system e sono accessibili in rete come se fosse una macchina virtuale o fisica. Tuttavia, la tecnologia e i concetti usati dai contenitori sono molto diversi dalle macchine virtuali. Le macchine virtuali includono le applicazioni, le dipendenze necessarie e un sistema operativo guest completo. I contenitori includono l'applicazione e le relative dipendenze, ma condividono il sistema operativo con altri contenitori in esecuzione come processi isolati nel sistema operativo host (a parte i contenitori di Hyper-V eseguiti all'interno di una speciale macchina virtuale per ogni contenitore). Di conseguenza, i contenitori condividono le risorse e in genere richiedono meno risorse rispetto alle macchine virtuali.

Il vantaggio di un approccio orientato ai contenitori, sviluppo e la distribuzione è che elimina la maggior parte dei problemi che sono causati da impostazioni di ambiente non coerente e i problemi relativi. Inoltre, i contenitori consentono funzionalità di scalabilità verticale rapida dell'applicazione come istanze nuovi contenitori in base alle esigenze.

I concetti chiave durante la creazione e utilizzo dei contenitori sono:

-   Host contenitore: La macchina fisica o virtuale configurato ai contenitori host. L'host del contenitore verrà eseguito uno o più contenitori.
-   Immagine del contenitore: Un'immagine è costituito da un'unione di file System a più livelli impilati uno sopra l'altro e costituisce la base di un contenitore. Un'immagine non è stato e non cambia mai, che viene distribuito nei diversi ambienti.
-   Contenitore: Un contenitore è un'istanza di runtime di un'immagine.
-   Immagine del sistema operativo del contenitore: I contenitori vengono distribuiti dalle immagini. L'immagine del sistema operativo contenitore è il primo livello in molti potenziali livelli dell'immagine che costituiscono un contenitore. Un sistema operativo contenitore è immutabile e non può essere modificato.
-   Repository del contenitore: Ogni volta che viene creata un'immagine del contenitore, immagine e le relative dipendenze vengono archiviate in un repository locale. Queste immagini possono essere riusate più volte nell'host contenitore. Le immagini del contenitore possono essere archiviate anche in un registro pubblico o privato, ad esempio [Hub Docker](https://hub.docker.com/), in modo che possono essere usati in diversi host contenitore.

Le aziende stanno adottando sempre più contenitori quando implementare microservizi basati su applicazioni e Docker è diventato l'implementazione di contenitore standard che è stato adottato per la maggior parte delle piattaforme software e i fornitori di cloud.

L'applicazione di riferimento eShopOnContainers Usa Docker per ospitare quattro microservizi back-end in contenitori, come illustrato nella figura 8-4.

![](containerized-microservices-images/microservicesarchitecture.png "i microservizi back-end dell'applicazione di riferimento eShopOnContainers")

**Figura 8-4**: i microservizi back-end dell'applicazione di riferimento eShopOnContainers

L'architettura di servizi di back-end nell'applicazione di riferimento viene scomposto in più sottosistemi autonomi sotto forma di contenitori e microservizi in collaborazione. Ogni microservizio fornisce una singola area della funzionalità: un servizio di identità, un servizio di catalogo, un servizio di gestione degli ordini e un servizio carrello.

Ogni microservizio dispone del proprio database, in modo che possa essere completamente separato dagli altri microservizi. Se necessario, la coerenza tra i database di microservizi diversi viene eseguita mediante gli eventi a livello di applicazione. Per altre informazioni, vedere [comunicazione tra Microservizi](#communication_between_microservices).

Per altre informazioni sull'applicazione di riferimento, vedere [Microservizi .NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservizi .NET. Architettura per le applicazioni .NET incluse in contenitori).

<a name="communication_between_client_and_microservices" />

## <a name="communication-between-client-and-microservices"></a>Comunicazione tra Client e Microservizi

L'app per dispositivi mobili di eShopOnContainers comunica con i microservizi in contenitori back-end usando *diretti da client a microservizio* comunicazione, come illustrata nella figura 8-5.

![](containerized-microservices-images/directclienttomicroservicecommunication.png "Approccio di scalabilità dell'applicazione di Microservizi")

**Figura 8-5**: Comunicazione da client a microservizio diretta

Con la comunicazione da client a microservizio diretta, l'app per dispositivi mobili effettua richieste per ogni microservizio direttamente tramite il relativo endpoint pubblico, con una porta TCP diversa per ogni microservizio. Nell'ambiente di produzione, l'endpoint viene in genere eseguire il mapping a bilanciamento del carico del microservizio, che distribuisce le richieste tra le istanze disponibili.

> [!TIP]
> È consigliabile usare la comunicazione di gateway API. Comunicazione da client a microservizio diretta può presentare alcuni svantaggi quando la creazione di un microservizio grande e complesso basati su applicazione, ma è più che sufficiente per una piccola applicazione. Quando l'applicazione con decine di microservizi basata su progettazione di un microservizio di grandi dimensioni, è consigliabile usare la comunicazione di gateway API. Per altre informazioni, vedere [Microservizi .NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservizi .NET. Architettura per le applicazioni .NET incluse in contenitori).

<a name="communication_between_microservices" />

## <a name="communication-between-microservices"></a>Comunicazione tra Microservizi

Un'applicazione basate su microservizi è un sistema distribuito, potenzialmente in esecuzione su più computer. Ogni istanza del servizio è in genere un processo. Di conseguenza, i servizi devono interagire usando un protocollo di comunicazione interprocesso, ad esempio HTTP, TCP, AMQP Advanced Message Queuing Protocol () o i protocolli binari, a seconda della natura di ogni servizio.

I due approcci comuni per la comunicazione di microservizi a microservizio sono comunicazione REST basato su HTTP quando si eseguono query per i dati e messaggistica asincrona lightweight durante la comunicazione degli aggiornamenti tra più microservizi.

Messaggistica basata su evento comunicazione asincrona basata è critico quando la propagazione delle modifiche tra più microservizi. Con questo approccio, un microservizio pubblica un evento quando un elemento rilevanti si verifica, ad esempio, quando viene aggiornata un'entità di business. Altri microservizi sottoscrivono tali eventi. Quindi, quando un microservizio riceve un evento, aggiorna la propria entità di business, che a sua volta può causare più eventi da pubblicare. Questa pubblicazione-sottoscrizione funzionalità avviene in genere con un bus di eventi.

Un bus di eventi consente la comunicazione tra microservizi, senza che i componenti da tenere in considerazione in modo esplicito uno da altro, come illustrato nella figura 8-6 di pubblicazione-sottoscrizione.

![](containerized-microservices-images/eventbus.png "Pubblicazione-sottoscrizione con un bus di eventi")

**Figura 8-6:** Pubblicazione-sottoscrizione con un bus di eventi

Dal punto di vista dell'applicazione, il bus di eventi è semplicemente un publish-canale esposto tramite un'interfaccia di sottoscrizione. Tuttavia, la modalità di che implementazione del bus di eventi può variare. Ad esempio, un'implementazione del bus di eventi potrebbe usare RabbitMQ, Bus di servizio di Azure o altri bus di servizio, ad esempio NServiceBus e MassTransit. Figura 8-7 viene illustrato come utilizzare un bus di eventi nell'applicazione di riferimento eShopOnContainers.

![](containerized-microservices-images/microservicesarchitecturewitheventbus.png "Comunicazione asincrona basata su eventi nell'applicazione di riferimento")

**Figura 8-7:** Comunicazione asincrona basata su eventi nell'applicazione di riferimento

Il bus di eventi di eShopOnContainers, implementato tramite RabbitMQ, fornisce funzionalità di pubblicazione-sottoscrizione uno-a-molti asincrono. Ciò significa che dopo la pubblicazione di un evento, possono essere presenti più sottoscrittori in attesa dell'evento stesso. Figura 8-9 è illustrata questa relazione.

![](containerized-microservices-images/eventdrivencommunication.png "Comunicazione uno-a-molti")

**Figura 8-9**: Comunicazione uno-a-molti

Questo approccio di comunicazione uno-a-molti utilizza gli eventi per implementare le transazioni di business che si estendono su più servizi, garantendo la coerenza finale tra i servizi. Una transazione coerente con l'eventuale è costituito da una serie di passaggi distribuiti. Pertanto, quando il microservizio del profilo utente riceve il comando UpdateUser, aggiorna i dettagli dell'utente in un database e pubblica l'evento UserUpdated nel bus di eventi. Il microservizio basket sia il microservizio degli ordini sottoscritti per questo evento viene generato e nella risposta aggiornare le informazioni sull'acquirente nei rispettivi database.

> [!NOTE]
> Il bus di eventi di eShopOnContainers, implementato tramite RabbitMQ, dovrà essere utilizzato solo come un modello di verifica. Per i sistemi di produzione, è necessario considerare le implementazioni del bus di eventi alternativo.

Per informazioni sull'implementazione del bus di eventi, vedere [Microservizi .NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservizi .NET. Architettura per le applicazioni .NET incluse in contenitori).

## <a name="summary"></a>Riepilogo

I Microservizi offrono un approccio allo sviluppo di applicazioni e alla distribuzione che è adatto ai requisiti di flessibilità, scalabilità e affidabilità delle applicazioni cloud moderne. Uno dei principali vantaggi dei microservizi è che possono essere con scalabilità orizzontale in modo indipendente, il che significa che può essere ridimensionata una determinata area funzionale che richiede più l'elaborazione dell'alimentazione o rete della larghezza di banda per supportare la domanda, senza scalabilità inutilmente aree della l'applicazione in cui non si verifica un aumento della domanda.

Un contenitore è un, resource, portabile e controllato ambiente operativo isolato, in cui è possibile eseguire un'applicazione senza modificare le risorse di altri contenitori o l'host. Le aziende stanno adottando sempre più contenitori quando implementare microservizi basati su applicazioni e Docker è diventato l'implementazione di contenitore standard che è stato adottato per la maggior parte delle piattaforme software e i fornitori di cloud.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
