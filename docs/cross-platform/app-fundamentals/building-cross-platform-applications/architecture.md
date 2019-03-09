---
title: Parte 2 - Architettura
description: Questo documento descrive i modelli di architettura utili per la compilazione di applicazioni multipiattaforma. Vengono illustrati i livelli di una tipica applicazione (livello dati, il livello di accesso ai dati, e così via) e i modelli comuni di software per dispositivi mobili (MVVM, MVC e così via)
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: cfb2bddceea7717ac87bd7a78fd9cd45e8b93144
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670441"
---
# <a name="part-2---architecture"></a>Parte 2 - Architettura

Un principio fondamentale della creazione di App multipiattaforma consiste nel creare un'architettura che si presta a una massimizzazione della condivisione del codice tra piattaforme. Adesione ai principi della programmazione orientata a oggetti di oggetto seguenti consente di compilare un'applicazione ben strutturata:

-   **Incapsulamento** : per garantire che le classi e i livelli di architettura anche espongono un'API minima che esegue le necessarie funzioni e consente di nascondere i dettagli di implementazione. A livello di classe, ciò significa che gli oggetti si comportano come "black box" e che utilizza codice non è necessario sapere come vengono eseguire le proprie attività. A livello di architettura, significa che implementazione dei modelli, ad esempio facciata che favoriscono un'API semplificata che Orchestra le interazioni più complesse per conto di codice in livelli più astratti. Ciò significa che il codice dell'interfaccia utente, ad esempio, deve solo essere responsabile per visualizzare schermate e accettare l'input dell'utente; e mai interagire direttamente con il database. Allo stesso modo il codice di accesso ai dati deve solo leggere e scrivere nel database, ma mai interagire direttamente con i pulsanti o etichette.
-   **Separazione delle responsabilità** : assicurarsi che ogni componente (sia a livello dell'architettura e livello di classe) ha lo scopo chiaro e ben definito. Ogni componente deve eseguire solo le attività definitive ed esporre tale funzionalità tramite un'API accessibile alle altre classi che dovranno usare.
-   **Polimorfismo** – la programmazione in un'interfaccia (o classe astratta) che supporta più mezzi di implementazioni che codice core può essere scritta e condivisi tra piattaforme, durante l'interazione ancora con funzionalità specifiche della piattaforma.


Il risultato naturale è un'applicazione modellata reali o entità astratte con livelli logici separati. Separazione di codice in livelli Rendi le applicazioni più facile da comprendere, testare e gestire. È consigliabile che il codice in ogni livello sia fisicamente separato (per le directory o persino separati i progetti per applicazioni molto grandi), nonché logicamente separati (con spazi dei nomi).

 <a name="Typical_Application_Layers" />


## <a name="typical-application-layers"></a>Livelli di applicazione tipico

In questo documento e i case study costituiscono i livelli sei applicazione seguenti:

-   **Livello dati** – persistenza dei dati Non-volatile, potrebbe essere un database SQLite, ma potrebbe essere implementato con i file XML o qualsiasi altro meccanismo adatto.
-   **Livello di accesso ai dati** – Wrapper per il livello di dati che fornisce creazione, lettura, aggiornamento, l'accesso di eliminazione (CRUD) ai dati senza esporre i dettagli di implementazione al chiamante. Ad esempio, DAL può contenere istruzioni SQL per eseguire una query o aggiornare i dati ma il codice con riferimenti non necessario esserne a conoscenza.
-   **Livello di business** – (detto anche il livello di logica di Business o BLL) contiene le definizioni di entità di business (il modello) e la logica di business. Candidato per schema facciata Business.
-   **Livello di accesso ai servizi** – viene utilizzata per accedere ai servizi nel cloud: dal servizio web complesse (REST, JSON, WCF) al semplice recupero dei dati e immagini da server remoti. Incapsula il comportamento di rete e fornisce una semplice API devono essere utilizzate dai livelli applicazione e dell'interfaccia utente.
-   **Livello dell'applicazione** : il codice che è in genere specifici della piattaforma (in genere non vengono condivisi tra le piattaforme) o il codice è specifico dell'applicazione (non a livello generale riutilizzabile). È un test utile per verificare se si desidera inserire codice nel livello dell'applicazione e il livello UI (a) per determinare se la classe dispone di tutti i controlli effettivamente visualizzato o (b) se può essere condiviso tra più schermate o dispositivi (ad es. iPhone e iPad).
-   **Livello dell'interfaccia utente** : il livello agli utenti finali, contiene le schermate, i widget e i controller che gestiscono tali.


Un'applicazione potrebbe non contenere necessariamente tutti i livelli, ad esempio il livello di accesso del servizio non esiste in un'applicazione che non accede alle risorse di rete. Un'applicazione molto semplice potrebbe eseguire il merge di livello dati e il livello di accesso ai dati perché le operazioni sono estremamente base.

 <a name="Common_Mobile_Software_Patterns" />


## <a name="common-mobile-software-patterns"></a>Modelli comuni di Software per dispositivi mobili

I modelli sono un modo stabilito per l'acquisizione periodiche soluzioni alle problematiche comuni. Sono disponibili alcuni modelli principali che sono utili per comprendere nella creazione di applicazioni per dispositivi mobili gestibile/comprensibile.

-   **Model, View, ViewModel (MVVM)** : The Model-View-ViewModel modello è più diffuso con Framework che supportano il data binding, ad esempio xamarin. Forms. Che era molto diffusa dagli SDK basate su XAML, ad esempio Windows Presentation Foundation (WPF) e Silverlight; in cui l'elemento ViewModel funge da intermediario tra i dati (modello) e l'interfaccia utente (visualizzazione) tramite data binding e i comandi.
-   **Model, View, Controller (MVC)** : un modello comune e apprezzato, MVC vengono spesso usati durante la creazione di interfacce utente e fornisce una separazione tra la definizione di una schermata dell'interfaccia utente (visualizzazione), il motore sottostante che gestisce effettiva interazione (Controller) e i dati che viene popolato (modello). Il modello è effettivamente una parte è facoltativa e, pertanto, il nucleo di comprendere questo modello si trova nella visualizzazione e Controller. MVC è un approccio comune per le applicazioni iOS.
-   **La facciata business** : noto anche come modello di gestione, fornisce un punto di ingresso semplificato per lavoro complesso. Ad esempio, in un'applicazione di rilevamento delle attività, potrebbe essere un' `TaskManager` classe con metodi, ad esempio `GetAllTasks()` , `GetTask(taskID)` , `SaveTask (task)` e così via. Il `TaskManager` classe fornisce un'interfaccia per i meccanismi interni di effettivamente salvataggio e recupero di oggetti attività.
-   **Singleton** – modello Singleton The fornisce un modo in cui può mai esistere una sola istanza di un oggetto specifico. Ad esempio, quando si usa SQLite nelle applicazioni per dispositivi mobili, si desidera solo mai un'istanza del database. Usando il modello Singleton è un modo semplice per garantire che questo.
-   **Provider** : un modello coniato da Microsoft (probabilmente simile alla strategia o inserimento di dipendenze di base) a promuovere il riutilizzo del codice tra le applicazioni Silverlight, WPF e Windows Form. Il codice condiviso può essere scritti in un'interfaccia o classe astratta e implementazioni concrete specifiche della piattaforma vengono scritte e passate quando viene usato il codice.
-   **Async** – non deve essere confusa con la parola chiave Async, la modalità asincrona modello viene usato quando lavoro con esecuzione prolungata deve essere eseguito senza compromettere l'interfaccia utente o di elaborazione corrente. Nella sua forma più semplice, il modello asincrono descrive semplicemente che l'attività a esecuzione prolungata deve essere avviata in un altro thread oppure un'astrazione di thread simile, ad esempio un'attività mentre il thread corrente continua a elaborare e attende una risposta dal processo in background e quindi aggiorna l'interfaccia utente quando vengono restituiti i dati e o stato.


Ognuno dei modelli verrà esaminata in dettaglio come illustrato nei case study relativo utilizzo pratico. Wikipedia con altre descrizioni del [MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel), [MVC](https://en.wikipedia.org/wiki/Model–view–controller), [facciata](https://en.wikipedia.org/wiki/Facade_pattern), [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern), [strategia](https://en.wikipedia.org/wiki/Strategy_pattern)e [Provider](https://en.wikipedia.org/wiki/Provider_model) pattern (e del [schemi progettuali](https://en.wikipedia.org/wiki/Design_Patterns) a livello generale).
