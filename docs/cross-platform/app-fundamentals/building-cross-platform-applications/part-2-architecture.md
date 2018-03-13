---
title: Parte 2 - architettura
ms.topic: article
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: 25cd374ef2b3026f5ac7242ee076c8593ce806da
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="part-2---architecture"></a>Parte 2 - architettura

Un principio fondamentale di compilazione di App multipiattaforma consiste nel creare un'architettura che si presta a una maximization di codice condiviso tra le piattaforme. Rispettare i principi di programmazione orientata ai servizi di oggetto seguenti consente di compilare un'applicazione ben strutturata:

-   **Incapsulamento** : per garantire che le classi e i livelli architetturali anche espongono un'API minima che esegue le necessarie funzioni e consente di nascondere i dettagli di implementazione. A un livello di classe, ciò significa che gli oggetti si comportano come 'neri' e che utilizza codice non è necessario sapere come eseguite le attività. A livello di architettura, la relativa implementazione di modelli come facciata che incoraggiano un'API semplificata che coordina le interazioni più complesse per conto di codice nei livelli più astratti. Ciò significa che il codice dell'interfaccia utente, ad esempio, deve solo essere responsabile per la visualizzazione delle schermate e accettare l'input dell'utente; e non interagisce direttamente con il database. Analogamente il codice di accesso ai dati deve solo leggere e scrivere nel database, ma mai interagire direttamente con i pulsanti o etichette.
-   **Separazione delle responsabilità** : verificare che ogni componente (sia a livello dell'architettura e livello di classe) ha lo scopo chiaro e ben definito. Ogni componente deve eseguire solo le attività definite ed esporre la funzionalità tramite un'API che è accessibile alle altre classi che dovranno usare.
-   **Polimorfismo** : programmazione a un'interfaccia (o una classe astratta) che supporta più metodi di implementazioni di codice di base può essere scritta e condivisi tra le piattaforme, mentre ancora l'interazione con le funzionalità specifiche della piattaforma.


Il risultato naturale è un'applicazione modellata reali o entità astratta con livelli logici separati. La separazione di codice nelle applicazioni di verificare i livelli più facile da comprendere, testare e gestire. È consigliabile che il codice in ogni livello sia fisicamente separato (nella directory o progetti separati anche per le applicazioni di dimensioni molto grandi), nonché separati logicamente (utilizzando gli spazi dei nomi).

 <a name="Typical_Application_Layers" />


## <a name="typical-application-layers"></a>Livelli di una tipica applicazione

In questo documento e case study facciamo riferimento ai seguenti livelli di sei applicazione:

-   **Livello dati** : persistenza di dati Non volatile, potrebbe essere un database SQLite, ma potrebbe essere implementato con i file XML o qualsiasi altro meccanismo appropriato.
-   **Livello accesso dati** – Wrapper attorno al livello dati che fornisce creazione, lettura, aggiornamento, l'accesso ai dati senza l'esposizione dei dettagli di implementazione per il chiamante eliminazione (CRUD). Ad esempio, DAL può contenere istruzioni SQL per eseguire una query o aggiornare i dati, ma il codice con riferimenti non è necessario esserne a conoscenza.
-   **Livello business** – (detto anche il livello di logica di Business o BLL) contiene le definizioni di entità di business (modello di) e logica di business. Candidato per il modello di facciata Business.
-   **Livello di accesso servizio** : utilizzato per accedere ai servizi nel cloud: dai servizi web complesse (WCF REST, JSON,) per il recupero semplice dei dati e immagini da server remoti. Incapsula il comportamento di rete e fornisce una semplice API deve essere utilizzato dai livelli di applicazione e dell'interfaccia utente.
-   **Livello di applicazione** : il codice è in genere specifico della piattaforma (in genere non vengono condiviso tra le piattaforme) o codice che è specifico dell'applicazione (non riutilizzabile). Se per l'inserimento di codice nel livello dell'applicazione e il livello di interfaccia utente è () per determinare se la classe dispone di tutti i controlli di visualizzazione effettivo o (b) se questa classe potrebbe essere condivisa tra più schermate o dispositivi (ad es. iPhone e iPad).
-   **Livello dell'interfaccia utente** : il livello di utenti finali, contiene le schermate, widget e i controller di gestione.


Un'applicazione potrebbe non contiene necessariamente tutti i livelli, ad esempio il livello di accesso del servizio non è più presente in un'applicazione che non accedono a risorse di rete. Un'applicazione molto semplice potrebbe unire il livello di dati e il livello di accesso ai dati perché le operazioni sono estremamente base.

 <a name="Common_Mobile_Software_Patterns" />


## <a name="common-mobile-software-patterns"></a>Modelli comuni di Software per dispositivi mobili

I modelli sono un modo stabilito per l'acquisizione periodiche soluzioni ai problemi più comuni. Sono presenti alcuni modelli chiave che sono utili per comprendere nella creazione di applicazioni per dispositivi mobili gestibile/comprensibile.

-   **Modello, visualizzazione, ViewModel (MVVM)** : The Model-View-ViewModel modello è comune con Framework che supportano l'associazione dati, ad esempio xamarin. Forms. La nota da SDK abilitato XAML Windows Presentation Foundation (WPF) e Silverlight; in cui l'elemento ViewModel funge da intermediario tra i dati (modello) e l'interfaccia utente (visualizzazione) tramite l'associazione dati e i comandi.
-   **Modello, visualizzazione Controller (MVC)** : un modello comune e apprezzato, MVC vengono spesso usati durante la compilazione di interfacce utente e fornisce una separazione tra la definizione di una schermata dell'interfaccia utente (visualizzazione), il motore sottostante che gestisce effettiva interazione (Controller) e i dati che viene compilato (modello). Il modello è effettivamente una parte facoltativa e, pertanto il nucleo di comprendere questo modello si trova nella visualizzazione e Controller. MVC è un approccio comune per le applicazioni iOS.
-   **La facciata business** : anche noto come modello di gestione, fornisce un punto di ingresso semplificato per operazioni complesse. Ad esempio, in un'applicazione di rilevamento di attività, potrebbe essere un `TaskManager` classe con metodi, ad esempio `GetAllTasks()` , `GetTask(taskID)` , `SaveTask (task)` e così via. La `TaskManager` classe fornisce un aspetto per i componenti interni di effettivamente salvataggio e recupero di oggetti di attività.
-   **Singleton** – criterio Singleton di fornisce un modo in cui solo una singola istanza di un oggetto specifico possono essere mai presenti. Ad esempio, quando si utilizza SQLite applicazioni per dispositivi mobili, si desidera solo un'istanza del database. Usando il modello Singleton è un modo semplice per garantire tale requisito.
-   **Provider** : un modello ha coniato da Microsoft (probabilmente simile a strategia o inserimento di dipendenze di base) a promuovere il riutilizzo del codice tra le applicazioni Silverlight, WPF e Windows Form. Il codice condiviso può essere scritti su un'interfaccia o una classe astratta e implementazioni concrete specifico della piattaforma vengono scritte e passate quando il codice viene utilizzato.
-   **Async** : non deve essere confusa con la parola chiave Async, la modalità asincrona modello viene utilizzato quando l'attività a esecuzione prolungata deve essere eseguito senza compromettere l'interfaccia utente o di elaborazione corrente. Nella sua forma più semplice, il modello asincrono descrive semplicemente che l'attività a esecuzione prolungata deve essere avviata in un altro thread oppure un'astrazione di thread simile, ad esempio un'attività mentre il thread corrente continua a elaborare e attende una risposta dal processo in background e quindi aggiorna l'interfaccia utente quando vengono restituiti i dati e o stato.


Ognuno dei modelli verrà esaminato in dettaglio come illustrato nel case study relativi utilizzo pratico. Wikipedia con altre descrizioni di [MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel), [MVC](https://en.wikipedia.org/wiki/Model–view–controller), [facciata](http://en.wikipedia.org/wiki/Facade_pattern), [Singleton](http://en.wikipedia.org/wiki/Singleton_pattern), [strategia](http://en.wikipedia.org/wiki/Strategy_pattern)e [Provider](http://en.wikipedia.org/wiki/Provider_model) modelli (e di [modelli di progettazione](http://en.wikipedia.org/wiki/Design_Patterns) in genere).
