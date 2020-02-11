---
title: Parte 2 - Architettura
description: Questo documento descrive i modelli di architettura utili per la creazione di applicazioni multipiattaforma. Vengono illustrati i tipici livelli dell'applicazione (livello dati, livello di accesso ai dati e così via) e i modelli comuni di software per dispositivi mobili (MVVM, MVC e così via)
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: 84a06e23ec7125892701762ab5bad7b86a8faf90
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030269"
---
# <a name="part-2---architecture"></a>Parte 2 - Architettura

Un principio fondamentale della creazione di app multipiattaforma consiste nel creare un'architettura che si presta a una massimizzazione della condivisione del codice tra le diverse piattaforme. La conformità ai seguenti principi di programmazione orientata a oggetti consente di creare un'applicazione ben progettata:

- **Incapsulamento** : garantire che le classi e anche i livelli architetturali espongano solo un'API minima che esegua le funzioni obbligatorie e nasconda i dettagli di implementazione. A livello di classe, ciò significa che gli oggetti si comportano come "black box" e che l'utilizzo di codice non deve necessariamente essere in grado di eseguire le attività. A livello di architettura, significa implementare modelli come la facciata che favoriscono un'API semplificata che orchestra le interazioni più complesse per conto del codice in livelli più astratti. Questo significa che il codice dell'interfaccia utente (ad esempio) dovrebbe essere responsabile solo della visualizzazione delle schermate e dell'accettazione dell'input utente. e non interagiscono mai direttamente con il database. Analogamente, il codice di accesso ai dati deve solo leggere e scrivere nel database, ma non interagire direttamente con i pulsanti o le etichette.
- **Separazione delle responsabilità** : assicurarsi che ogni componente (a livello di architettura e di classe) abbia uno scopo chiaro e ben definito. Ogni componente deve eseguire solo le proprie attività definite ed esporre tale funzionalità tramite un'API accessibile alle altre classi che devono utilizzarlo.
- **Polimorfismo** : la programmazione in un'interfaccia (o classe astratta) che supporta più implementazioni significa che il codice di base può essere scritto e condiviso tra le diverse piattaforme, continuando comunque a interagire con le funzionalità specifiche della piattaforma.

Il risultato naturale è un'applicazione modellata dopo il mondo reale o le entità astratte con livelli logici distinti. La separazione del codice in livelli facilita la comprensione, il test e la gestione delle applicazioni. È consigliabile che il codice in ogni livello sia fisicamente separato (nelle directory o persino in progetti distinti per le applicazioni di grandi dimensioni), oltre che per separare logicamente (usando gli spazi dei nomi).

 <a name="Typical_Application_Layers" />

## <a name="typical-application-layers"></a>Livelli di applicazione tipici

In questo documento e nei case study si fa riferimento ai sei livelli di applicazione seguenti:

- **Livello dati** : persistenza dei dati non volatili, probabilmente un database SQLite ma potrebbe essere implementato con file XML o qualsiasi altro meccanismo appropriato.
- **Livello di accesso ai dati** : wrapper per il livello dati che fornisce accesso CRUD (create, Read, Update, Delete) ai dati senza esporre i dettagli di implementazione al chiamante. Ad esempio, il DAL può contenere istruzioni SQL per eseguire query o aggiornare i dati, ma non è necessario che il codice di riferimento lo conosca.
- **Livello aziendale** : (talvolta denominato livello della logica di business o BLL) contiene le definizioni delle entità di business (modello) e la logica di business. Modello di facciata candidato per le aziende.
- **Livello di accesso** ai servizi: usato per accedere ai servizi nel cloud: da servizi Web complessi (REST, JSON, WCF) al semplice recupero di dati e immagini da server remoti. Incapsula il comportamento di rete e fornisce un'API semplice che deve essere utilizzata dai livelli dell'applicazione e dell'interfaccia utente.
- **Livello dell'applicazione** : codice generalmente specifico della piattaforma (non condiviso in genere tra piattaforme) o codice specifico dell'applicazione (non in genere riutilizzabile). Un valido test che indica se inserire il codice nel livello dell'applicazione rispetto al livello dell'interfaccia utente è (a) per determinare se la classe dispone di controlli di visualizzazione effettivi o (b) se può essere condivisa tra più schermate o dispositivi (ad esempio, iPhone e iPad.
- **Livello dell'interfaccia utente** : il livello per l'utente, contiene schermate, widget e controller che li gestiscono.

Un'applicazione potrebbe non contenere necessariamente tutti i livelli, ad esempio il livello di accesso al servizio non esiste in un'applicazione che non accede alle risorse di rete. Un'applicazione molto semplice può unire il livello dati e il livello di accesso ai dati perché le operazioni sono estremamente semplici.

 <a name="Common_Mobile_Software_Patterns" />

## <a name="common-mobile-software-patterns"></a>Modelli comuni di software per dispositivi mobili

I modelli sono un modo consolidato per acquisire soluzioni ricorrenti ai problemi più comuni. Esistono alcuni modelli chiave utili per comprendere la creazione di applicazioni per dispositivi mobili gestibili e comprensibili.

- **Model, View, ViewModel (MVVM)** : il modello Model-View-ViewModel è molto diffuso con i Framework che supportano l'associazione dati, ad esempio Xamarin.Forms. È stata diffusa da SDK abilitati per XAML come Windows Presentation Foundation (WPF) e Silverlight; dove il ViewModel funge da passaggio tra i dati (modello) e l'interfaccia utente (visualizzazione) tramite data binding e comandi.
- **Modello, visualizzazione, controller (MVC)** : modello comune e spesso non compreso, MVC viene usato più spesso quando si compilano interfacce utente e fornisce una separazione tra la definizione effettiva di una schermata dell'interfaccia utente (visualizzazione), il motore dietro il quale gestisce l'interazione ( E i dati che popolano il controller (Model). Il modello è effettivamente un componente completamente facoltativo e pertanto il nucleo della comprensione di questo modello è la visualizzazione e il controller. MVC è un approccio comune per le applicazioni iOS.
- **Facciata aziendale** , noto anche come modello di Manager, fornisce un punto di ingresso semplificato per il lavoro complesso. Ad esempio, in un'applicazione di rilevamento delle attività potrebbe essere presente una classe `TaskManager` con metodi quali `GetAllTasks()`, `GetTask(taskID)`, `SaveTask (task)` e così via. La classe `TaskManager` fornisce una facciata ai processi interni di salvataggio/recupero degli oggetti attività.
- **Singleton** : il modello singleton fornisce una modalità in cui può esistere una sola istanza di un oggetto specifico. Ad esempio, quando si usa SQLite nelle applicazioni per dispositivi mobili, si vuole solo un'istanza del database. L'uso del modello singleton è un modo semplice per garantire questo problema.
- **Provider** : modello coniato da Microsoft (probabilmente simile a strategia o inserimento di dipendenze di base) per incoraggiare il riutilizzo del codice tra le applicazioni Silverlight, WPF e Windows Form. Il codice condiviso può essere scritto in base a un'interfaccia o a una classe astratta e le implementazioni concrete specifiche della piattaforma vengono scritte e passate quando viene usato il codice.
- **Async** : da non confondere con la parola chiave async, il modello asincrono viene usato quando è necessario eseguire un lavoro a esecuzione prolungata senza mantenere l'interfaccia utente o l'elaborazione corrente. Nella sua forma più semplice, il modello asincrono descrive semplicemente che le attività a esecuzione prolungata devono essere avviate in un altro thread (o un'astrazione di thread simile, ad esempio un'attività) mentre il thread corrente continua a elaborare ed è in attesa di una risposta dal processo in background , quindi aggiorna l'interfaccia utente quando vengono restituiti i dati e o lo stato.

Ogni modello verrà esaminato in modo più dettagliato, in quanto l'uso pratico viene illustrato nel case study. Wikipedia offre descrizioni più dettagliate dei modelli [MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel), [MVC](https://en.wikipedia.org/wiki/Model–view–controller), [Facade](https://en.wikipedia.org/wiki/Facade_pattern), [singleton](https://en.wikipedia.org/wiki/Singleton_pattern), [strategia](https://en.wikipedia.org/wiki/Strategy_pattern) e [provider](https://en.wikipedia.org/wiki/Provider_model) (e di [modelli di progettazione](https://en.wikipedia.org/wiki/Design_Patterns) in genere).
