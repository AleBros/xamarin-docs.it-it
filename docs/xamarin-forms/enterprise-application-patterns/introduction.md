---
title: Introduzione allo sviluppo di App aziendali
description: In questo capitolo viene fornita un'introduzione allo sviluppo di app aziendali e introduce l'app per dispositivi mobili di eShopOnContainers.
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 4fbb4047b95fd70f829cd79e4ea26b2958273297
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831171"
---
# <a name="introduction-to-enterprise-app-development"></a>Introduzione allo sviluppo di App aziendali

Indipendentemente dalla piattaforma, gli sviluppatori di App aziendali affrontano numerose sfide:

-   Requisiti di App che possono cambiare nel tempo.
-   Nuove opportunità e sfide.
-   Feedback continuo durante lo sviluppo che possa influire in modo significativo i requisiti dell'app e l'ambito.

Con questi aspetti, è importante creare App che possono essere facilmente modificate o estesi nel corso del tempo. Progettazione per l'adattabilità tali può essere difficile poiché richiede un'architettura che consente a singole parti dell'app per essere sviluppate e testate in isolamento senza alcun effetto sul resto dell'app in modo indipendente.

Molte App aziendali sono sufficientemente complesse da richiedere più di uno sviluppatore. Può essere una sfida significativa per decidere come progettare un'app in modo che più sviluppatori possono lavorare in modo efficace in diverse parti dell'app in modo indipendente, assicurando che i componenti si riuniscono senza problemi dopo l'integrazione in app.

L'approccio tradizionale alla progettazione e creazione di un'app per i risultati in cosa si intende un *monolitica* app, in cui i componenti sono strettamente collegati non netta separazione tra di essi. In genere, questo approccio monolitico lead per le app che sono difficili e inefficiente. per mantenere, perché può essere difficile risolvere i bug senza interrompere altri componenti nell'app e può essere difficile per aggiungere nuove funzionalità o per sostituire le funzionalità esistenti.

Un rimedio efficace per questi problemi consiste nel partizionare un'app in componenti discreti e loosely coupled che possono essere facilmente integrati tra loro in un'app. Questo approccio offre diversi vantaggi:

-   Consente a singole funzionalità per essere sviluppati, testati, esteso e gestita da persone o team diversi.
-   Promuove una netta separazione delle problematiche tra le funzionalità dell'app orizzontale, ad esempio l'autenticazione e accesso ai dati e le funzionalità verticale, ad esempio funzionalità di business specifici di app e il riutilizzo. In questo modo le dipendenze e le interazioni tra componenti dell'app da gestire più facilmente.
-   Consente di mantenere una separazione dei ruoli, consentendo a persone diverse o i team, per concentrarsi su un'attività specifica o parte della funzionalità in base alla propria esperienza. In particolare, fornisce una separazione più netta tra l'interfaccia utente e la logica di business dell'app.

Esistono tuttavia molti problemi che devono essere risolti durante il partizionamento di un'app in componenti discreti e loosely coupled. Sono inclusi:

-   Prima di decidere come fornire una netta separazione delle problematiche tra i controlli dell'interfaccia utente e la loro logica. Una delle decisioni più importanti quando si crea un'app di enterprise di xamarin. Forms è se si desidera inserire la logica di business nel file code-behind, o se si desidera creare una netta separazione delle problematiche tra i controlli dell'interfaccia utente e la logica, per rendere l'app più gestibili e testabili. Per altre informazioni, vedere [Model-View-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md).
-   Che determina se utilizzare un contenitore di inserimento delle dipendenze. I contenitori di dipendenze ridurre la dipendenza accoppiamento tra gli oggetti, fornendo una funzionalità di creazione di istanze di classi con le relative dipendenze inserite e gestiscono la durata in base alla configurazione del contenitore. Per altre informazioni, vedere [inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).
-   Scelta tra gli eventi della piattaforma fornita e debolmente accoppiato comunicazione basata su messaggi tra i componenti che sono poco pratici di collegamento dai riferimenti oggetto e il tipo. Per altre informazioni, vedere Introduzione a [comunicazione tra Loosely Coupled componenti](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).
-   Prima di decidere come spostarsi tra pagine, incluse le procedure richiamare riquadro di spostamento e in cui deve risiedere logica di navigazione. Per altre informazioni, vedere [Navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md).
-   Determinare la modalità di convalida dell'input utente per la correttezza. La decisione deve includere come convalidare l'input dell'utente e come inviare una notifica all'utente gli errori di convalida. Per altre informazioni, vedere [convalida](~/xamarin-forms/enterprise-application-patterns/validation.md).
-   Per decidere come eseguire l'autenticazione e come proteggere le risorse con l'autorizzazione. Per altre informazioni, vedere [autenticazione e autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md).
-   Determinare la modalità di accesso ai dati remoti da web dei servizi, tra cui come recuperare in modo affidabile i dati e come memorizzare nella cache dei dati. Per altre informazioni, vedere [l'accesso ai dati remoti](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).
-   Prima di decidere come testare l'app. Per altre informazioni, vedere [Unit Testing](~/xamarin-forms/enterprise-application-patterns/unit-testing.md).

Questa guida fornisce informazioni aggiuntive su questi problemi e illustra i modelli di base e l'architettura per la creazione di un'app aziendali multipiattaforma con xamarin. Forms. Il materiale sussidiario mira a consentire di produrre codice adattabile, gestibile e testabile, risolvendo comuni scenari di sviluppo delle app aziendali xamarin. Forms e separando i problemi di presentazione, logica di presentazione e le entità tramite il supporto per il Modello Model-View-ViewModel (MVVM).

## <a name="sample-application"></a>Applicazione di esempio

Questa guida include un'applicazione di esempio eShopOnContainers, che è un negozio online che include le funzionalità seguenti:

-   Autenticazione e autorizzazione in un servizio back-end.
-   Esplorazione di un catalogo di shirts, tazze da caffè e altri elementi di marketing.
-   Applicazione di filtri del catalogo.
-   Ordinamento degli elementi dal catalogo.
-   Visualizzazione cronologia degli ordini dell'utente.
-   Configurazione delle impostazioni.

### <a name="sample-application-architecture"></a>Architettura dell'applicazione di esempio

Figura 1-1 fornisce una panoramica generale dell'architettura dell'applicazione di esempio.

![](introduction-images/architecture.png "architettura di alto livello di eShopOnContainers")

**Figura 1-1**: architettura di alto livello di eShopOnContainers

L'applicazione di esempio sono disponibili tre App client:

-   Sviluppo di un'applicazione MVC con ASP.NET Core.
-   Un applicazione a pagina singola (SPA) sviluppato con Typescript e Angular 2. Questo approccio per le applicazioni web consente di evitare l'esecuzione di un round trip al server con ogni operazione.
-   Un'app per dispositivi mobili sviluppate con xamarin. Forms, che supporta iOS, Android e Universal Windows Platform (UWP).

Per informazioni sulle applicazioni web, vedere [Architecting e lo sviluppo di applicazioni Web moderne con ASP.NET Core e Microsoft Azure](https://aka.ms/WebAppEbook).

L'applicazione di esempio include i seguenti servizi di back-end:

-   Un microservizio di identità, che usa ASP.NET Core Identity e IdentityServer.
-   Un microservizio catalogo, che è una creazione guidata dai dati, leggere, aggiornare ed eliminare un servizio (CRUD) che utilizza un database di SQL Server usando Entity Framework Core.
-   Un microservizio degli ordini, ovvero un servizio basato su dominio che utilizza schemi progettuali basati su dominio.
-   Un microservizio basket, che è un servizio CRUD basate sui dati che usa la Cache Redis.

Questi servizi back-end vengono implementati come microservizi usando ASP.NET Core MVC e vengono distribuiti come contenitori univoci all'interno di un singolo host Docker. Questi servizi back-end vengono definiti collettivamente come applicazione di riferimento eShopOnContainers. Le app client comunicano con i servizi back-end tramite un'interfaccia web Representational State Transfer (REST). Per altre informazioni sui microservizi e Docker, vedere [Microservizi in contenitori](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md).

Per informazioni sull'implementazione dei servizi di back-end, vedere [Microservizi .NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservizi .NET. Architettura per le applicazioni .NET incluse in contenitori).

### <a name="mobile-app"></a>App per dispositivi mobili

Questa guida è incentrata sulla creazione di App aziendali multipiattaforma con xamarin. Forms e Usa l'app per dispositivi mobili di eShopOnContainers come esempio. La figura 1-2 mostra le pagine dell'App per dispositivi mobili di eShopOnContainers che forniscono le funzionalità descritte in precedenza.

[![](introduction-images/screenshots.png "L'app per dispositivi mobili di eShopOnContainers")](introduction-images/screenshots-large.png#lightbox "l'app per dispositivi mobili di eShopOnContainers")

**La figura 1-2**: L'app per dispositivi mobili di eShopOnContainers

L'app per dispositivi mobili utilizza i servizi back-end forniti dall'applicazione di riferimento eShopOnContainers. Tuttavia, può essere configurato per utilizzare dati da servizi fittizi per coloro che desiderano evitare di distribuire i servizi back-end.

L'app per dispositivi mobili di eShopOnContainers esercitarsi con le seguenti funzionalità di xamarin. Forms:

-   XAML
-   Controlli
-   Associazioni
-   Convertitori di tipi
-   Stili
-   Animations
-   Comandi:
-   comportamenti
-   Trigger
-   Effetti
-   Renderer personalizzati
-   MessagingCenter
-   Controlli personalizzati

Per altre informazioni su questa funzionalità, vedere la [documentazione di xamarin. Forms](~/xamarin-forms/index.yml), e [creazione di App per dispositivi mobili con xamarin. Forms](https://aka.ms/xamebook).

Inoltre, gli unit test vengono forniti per alcune delle classi nell'app per dispositivi mobili di eShopOnContainers.

#### <a name="mobile-app-solution"></a>Soluzione di App per dispositivi mobili

La soluzione di app per dispositivi mobili di eShopOnContainers consente di organizzare il codice sorgente e altre risorse in progetti. Tutti i progetti utilizzare cartelle per organizzare il codice sorgente e altre risorse in categorie. Nella tabella seguente vengono illustrati i progetti che costituiscono l'app per dispositivi mobili di eShopOnContainers:

|Progetto|DESCRIZIONE|
|--- |--- |
|eShopOnContainers.Core|Questo progetto è il progetto di libreria (PCL) di classi portabile che contiene il codice condiviso e l'interfaccia utente condivisa.|
|eShopOnContainers.Droid|Questo progetto contiene codice specifico di Android ed è il punto di ingresso per l'app Android.|
|eShopOnContainers.iOS|Questo progetto contiene il codice specifico di iOS ed è il punto di ingresso per l'app iOS.|
|eShopOnContainers.UWP|Questo progetto contiene codice specifico di Universal Windows Platform (UWP) e il punto di ingresso per l'app di Windows.|
|eShopOnContainers.TestRunner.Droid|Questo progetto è Android test runner per il progetto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.iOS|Questo progetto è il test runner iOS per il progetto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.Windows|Questo progetto è il test runner (Universal Windows Platform) per il progetto eShopOnContainers.UnitTests.|
|eShopOnContainers.UnitTests|Questo progetto contiene unit test per il progetto eShopOnContainers.Core.|

Le classi dall'app per dispositivi mobili eShopOnContainers possono essere riusate in tutte le app xamarin. Forms con una minima o senza alcuna modifica.

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers.Core Project

Il progetto di libreria di classi Portabile eShopOnContainers.Core contiene le cartelle seguenti:

|Cartella|Descrizione|
|--- |--- |
|Animations|Contiene classi che consentono le animazioni devono essere usati in XAML.|
|comportamenti|Contiene i comportamenti che vengono esposte per visualizzare le classi.|
|Controlli|Contiene controlli personalizzati usati dall'app.|
|Convertitori di tipi|Contiene i convertitori di valori che si applicano per la logica personalizzata a un'associazione.|
|Effetti|Contiene il `EntryLineColorEffect` classe, che consente di modificare il colore del bordo di specifico `Entry` controlli.|
|Eccezioni|Contiene l'oggetto personalizzato `ServiceAuthenticationException`.|
|Estensioni|Contiene metodi di estensione per il `VisualElement` e `IEnumerable` classi.|
|Helper|Contiene le classi helper per l'app.|
|Modelli|Contiene le classi di modello per l'app.|
|Properties|Contiene `AssemblyInfo.cs`, un file di metadati di assembly .NET.|
|Servizi|Contiene le interfacce e classi che implementano i servizi forniti all'app.|
|Trigger|Contiene il `BeginAnimation` trigger, che viene utilizzato per richiamare un'animazione in XAML.|
|Convalide|Contiene le classi coinvolte nella convalida dell'input di dati.|
|ViewModel|Contiene la logica dell'applicazione che viene esposta alle pagine.|
|Visualizzazioni|Contiene le pagine per l'app.|

##### <a name="platform-projects"></a>Progetti di piattaforma

I progetti di piattaforma contengono le implementazioni effetti, le implementazioni di renderer personalizzato e ad altre risorse specifiche della piattaforma.

## <a name="summary"></a>Riepilogo

Piattaforme e strumenti di sviluppo di app per dispositivi mobili multipiattaforma di Xamarin offrono una soluzione completa per client mobili B2E, B2B e B2C le App e offre la possibilità di condividere codice tra tutte le piattaforme di destinazione (iOS, Android e Windows) e per aiutarti a ridurre la costo totale di proprietà. Le app possono condividere il codice di logica app e dell'interfaccia utente, mantenendo l'aspetto della piattaforma nativa.

Gli sviluppatori di App aziendali affrontano numerose sfide che possono modificare l'architettura dell'app durante lo sviluppo. Pertanto, è importante compilare un'app in modo che può essere modificato o esteso nel corso del tempo. Progettazione per l'adattabilità tali può essere difficile, ma in genere comporta il partizionamento di un'app in componenti discreti e loosely coupled che possono essere facilmente integrati tra loro in un'app.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
