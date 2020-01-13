---
title: Introduzione allo sviluppo di app aziendali
description: Questo capitolo fornisce un'introduzione allo sviluppo di app aziendali e introduce l'app per dispositivi mobili eShopOnContainers.
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9bde1140f6590daa4b1d40a8b56edec314bfc66d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70760229"
---
# <a name="introduction-to-enterprise-app-development"></a>Introduzione allo sviluppo di app aziendali

Indipendentemente dalla piattaforma, gli sviluppatori di app aziendali affrontano diverse esigenze:

- Requisiti dell'app che possono cambiare nel tempo.
- Nuove opportunità e problemi aziendali.
- Feedback continuo durante lo sviluppo che può influire in modo significativo sull'ambito e sui requisiti dell'app.

Tenendo conto di queste considerazioni, è importante creare app che possono essere facilmente modificate o estese nel tempo. La progettazione di tale adattamento può essere difficile perché richiede un'architettura che consente di sviluppare e testare le singole parti dell'app in modo indipendente, senza influire sul resto dell'app.

Molte app aziendali sono sufficientemente complesse da richiedere più di uno sviluppatore. Può trattarsi di una sfida significativa per decidere come progettare un'app in modo che più sviluppatori possano lavorare in modo efficace su diverse parti dell'app in modo indipendente, garantendo al tempo stesso che i componenti si trovino in modo uniforme quando sono integrati nell'app.

L'approccio tradizionale alla progettazione e alla creazione di un'app comporta la definizione di un'app *monolitica* , in cui i componenti sono strettamente collegati senza alcuna netta separazione tra di essi. In genere, questo approccio monolitico conduce ad app difficili e inefficienti da gestire, perché può essere difficile risolvere i bug senza compromettere altri componenti nell'app e può essere difficile aggiungere nuove funzionalità o sostituire le funzionalità esistenti.

Un rimedio efficace per queste difficoltà consiste nel partizionare un'app in componenti discreti, a regime di controllo libero, che possono essere facilmente integrati in un'app. Questo approccio offre diversi vantaggi:

- Consente di sviluppare, testare, estendere e gestire singole funzionalità da utenti o team diversi.
- Promuove il riutilizzo e una netta separazione dei problemi tra le funzionalità orizzontali dell'app, ad esempio l'autenticazione e l'accesso ai dati, e le funzionalità verticali, ad esempio funzionalità aziendali specifiche dell'app. Ciò consente di gestire più facilmente le dipendenze e le interazioni tra i componenti dell'app.
- Consente di mantenere la separazione dei ruoli consentendo a singoli utenti, o team, di concentrarsi su un'attività specifica o una parte delle funzionalità in base alle proprie competenze. In particolare, fornisce una separazione più pulita tra l'interfaccia utente e la logica di business dell'app.

Tuttavia, esistono molti problemi che devono essere risolti durante il partizionamento di un'app in componenti discreti, a regime di controllo libero. tra cui:

- Decidere come fornire una netta separazione dei problemi tra i controlli dell'interfaccia utente e la relativa logica. Una delle decisioni più importanti per la creazione di un'app aziendale Xamarin.Forms consiste nel decidere se inserire la logica di business nei file code-behind o se creare una netta separazione dei problemi tra i controlli dell'interfaccia utente e la relativa logica, per rendere più semplice l'app gestibile e testabile. Per altre informazioni, vedere [Model-View-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md).
- Determinare se usare un contenitore di inserimento delle dipendenze. I contenitori di inserimento delle dipendenze riducono l'accoppiamento delle dipendenze tra gli oggetti fornendo una struttura per costruire istanze di classi con le relative dipendenze inserite e gestirne la durata in base alla configurazione del contenitore. Per altre informazioni, vedere [inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).
- La scelta tra la piattaforma fornita dagli eventi e la comunicazione basata su messaggi a regime di controllo libero tra i componenti che risultano poco pratici per il collegamento in base a riferimenti a oggetti e tipi. Per altre informazioni, vedere Introduzione alla [comunicazione tra componenti](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)a regime di controllo libero.
- Decidere come spostarsi tra le pagine, inclusa la modalità di richiamo dello spostamento e la posizione in cui deve risiedere la logica di navigazione. Per altre informazioni, vedere [Navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md).
- Determinazione della modalità di convalida dell'input dell'utente per la correttezza. La decisione deve includere come convalidare l'input dell'utente e come inviare una notifica all'utente sugli errori di convalida. Per ulteriori informazioni, vedere [convalida](~/xamarin-forms/enterprise-application-patterns/validation.md).
- Decidere come eseguire l'autenticazione e come proteggere le risorse con l'autorizzazione. Per ulteriori informazioni, vedere [autenticazione e autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md).
- Determinazione della modalità di accesso ai dati remoti dai servizi Web, incluso come recuperare in modo affidabile i dati e come memorizzare nella cache i dati. Per ulteriori informazioni, vedere [accesso ai dati remoti](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).
- Decidere come testare l'app. Per ulteriori informazioni, vedere [unit testing](~/xamarin-forms/enterprise-application-patterns/unit-testing.md).

Questa guida fornisce indicazioni su questi problemi e si concentra sui modelli e sull'architettura di base per la creazione di un'app aziendale multipiattaforma con Xamarin.Forms. Il materiale sussidiario mira a produrre codice adattabile, gestibile e testabile, risolvendo gli scenari comuni di sviluppo delle app Xamarin.Forms e scegliendo le problematiche relative alla presentazione, alla logica di presentazione e alle entità tramite il supporto del Modello MVC (Model-View-ViewModel).

## <a name="sample-application"></a>Applicazione di esempio

Questa guida include un'applicazione di esempio, eShopOnContainers, che è un negozio online che include le funzionalità seguenti:

- Autenticazione e autorizzazione per un servizio back-end.
- Esplorazione di un catalogo di camicie, tazze da caffè e altri elementi di marketing.
- Filtrare il catalogo.
- Ordinamento degli elementi dal catalogo.
- Visualizzazione della cronologia degli ordini dell'utente.
- Configurazione delle impostazioni.

### <a name="sample-application-architecture"></a>Architettura dell'applicazione di esempio

La figura 1-1 fornisce una panoramica di alto livello dell'architettura dell'applicazione di esempio.

![](introduction-images/architecture.png "eShopOnContainers high-level architecture")

**Figura 1-1**: architettura di alto livello di eShopOnContainers

L'applicazione di esempio viene fornita con tre app client:

- Applicazione MVC sviluppata con ASP.NET Core.
- Applicazione a pagina singola (SPA) sviluppata con angolari 2 e typescript. Questo approccio per le applicazioni Web consente di evitare l'esecuzione di un round trip al server con ciascuna operazione.
- App per dispositivi mobili sviluppata con Xamarin.Forms, che supporta iOS, Android e il piattaforma UWP (Universal Windows Platform) (UWP).

Per informazioni sulle applicazioni Web, vedere [architettura e sviluppo di applicazioni Web moderne con ASP.NET Core e Microsoft Azure](https://aka.ms/WebAppEbook).

L'applicazione di esempio include i servizi back-end seguenti:

- Un microservizio di identità, che usa ASP.NET Core Identity e IdentityServer.
- Un microservizio catalogo, ovvero un servizio di creazione, lettura, aggiornamento ed eliminazione (CRUD) basato sui dati che utilizza un database di SQL Server con EntityFramework core.
- Un microservizio degli ordini, ovvero un servizio basato su dominio che usa modelli di progettazione basati su dominio.
- Un microservizio basket, ovvero un servizio CRUD basato sui dati che usa la cache Redis.

Questi servizi back-end vengono implementati come microservizi usando ASP.NET Core MVC e vengono distribuiti come contenitori univoci all'interno di un singolo host docker. Complessivamente, questi servizi back-end vengono definiti applicazione di riferimento eShopOnContainers. Le app client comunicano con i servizi back-end tramite un'interfaccia Web REST (Representational State Transfer). Per altre informazioni sui microservizi e Docker, vedere [microservizi in contenitori](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md).

Per informazioni sull'implementazione dei servizi back-end, vedere [microservizi .NET: architettura per le applicazioni .NET in contenitori](https://aka.ms/microservicesebook).

### <a name="mobile-app"></a>App per dispositivi mobili

Questa guida è incentrata sulla creazione di app aziendali multipiattaforma con Xamarin.Forms e usa l'app per dispositivi mobili eShopOnContainers come esempio. La figura 1-2 Mostra le pagine dell'app per dispositivi mobili eShopOnContainers che forniscono la funzionalità descritta in precedenza.

[![](introduction-images/screenshots.png "The eShopOnContainers mobile app")](introduction-images/screenshots-large.png#lightbox "The eShopOnContainers mobile app")

**Figura 1-2**: app per dispositivi mobili eShopOnContainers

L'app per dispositivi mobili usa i servizi back-end forniti dall'applicazione di riferimento eShopOnContainers. Tuttavia, può essere configurato per utilizzare i dati di servizi fittizi per coloro che desiderano evitare di distribuire i servizi back-end.

L'app per dispositivi mobili eShopOnContainers esercita la seguente funzionalità di Xamarin.Forms:

- XAML
- Controlli
- Associazioni
- Convertitori
- Stili
- Animations
- Comandi
- comportamenti
- trigger
- Effetti
- Renderer personalizzati
- MessagingCenter
- Controlli personalizzati

Per altre informazioni su questa funzionalità, vedere la [documentazione di Xamarin.Forms](~/xamarin-forms/index.yml)e [creazione di app per dispositivi mobili con Xamarin.Forms](https://aka.ms/xamebook).

Vengono inoltre forniti unit test per alcune delle classi nell'app per dispositivi mobili eShopOnContainers.

#### <a name="mobile-app-solution"></a>Soluzione app per dispositivi mobili

La soluzione app per dispositivi mobili eShopOnContainers organizza il codice sorgente e altre risorse nei progetti. Tutti i progetti utilizzano le cartelle per organizzare il codice sorgente e altre risorse in categorie. La tabella seguente descrive i progetti che compongono l'app per dispositivi mobili eShopOnContainers:

|Progetto|Descrizione|
|--- |--- |
|eShopOnContainers. Core|Questo progetto è il progetto libreria di classi portabile (PCL) che contiene il codice condiviso e l'interfaccia utente condivisa.|
|eShopOnContainers. Droid|Questo progetto include codice specifico per Android ed è il punto di ingresso per l'app Android.|
|eShopOnContainers. iOS|Questo progetto include il codice specifico di iOS ed è il punto di ingresso per l'app iOS.|
|eShopOnContainers. UWP|Questo progetto include piattaforma UWP (Universal Windows Platform) codice specifico (UWP) ed è il punto di ingresso per l'app di Windows.|
|eShopOnContainers. TestRunner. Droid|Questo progetto è Android Test Runner per il progetto eShopOnContainers. UnitTests.|
|eShopOnContainers. TestRunner. iOS|Questo progetto è il test runner iOS per il progetto eShopOnContainers. UnitTests.|
|eShopOnContainers. TestRunner. Windows|Questo progetto è il piattaforma UWP (Universal Windows Platform) Test Runner per il progetto eShopOnContainers. UnitTests.|
|eShopOnContainers. UnitTests|Questo progetto contiene unit test per il progetto eShopOnContainers. Core.|

Le classi dall'app per dispositivi mobili eShopOnContainers possono essere riutilizzate in qualsiasi app Xamarin.Forms con modifiche minime o nulle.

##### <a name="eshoponcontainerscore-project"></a>Progetto eShopOnContainers. Core

Il progetto PCL eShopOnContainers. core contiene le cartelle seguenti:

|Cartella|Descrizione|
|--- |--- |
|Animations|Contiene classi che consentono l'utilizzo delle animazioni in XAML.|
|comportamenti|Contiene i comportamenti esposti alle classi di visualizzazione.|
|Controlli|Contiene i controlli personalizzati usati dall'app.|
|Convertitori|Contiene convertitori di valori che applicano la logica personalizzata a un'associazione.|
|Effetti|Contiene la classe `EntryLineColorEffect` utilizzata per modificare il colore del bordo di controlli `Entry` specifici.|
|Eccezioni|Contiene la `ServiceAuthenticationException` personalizzata.|
|Estensioni|Contiene metodi di estensione per le classi `VisualElement` e `IEnumerable`.|
|Aiutanti|Contiene le classi helper per l'app.|
|Modelli|Contiene le classi del modello per l'app.|
|Proprietà|Contiene `AssemblyInfo.cs`, un file di metadati dell'assembly .NET.|
|Servizi|Contiene le interfacce e le classi che implementano i servizi forniti all'app.|
|trigger|Contiene il trigger `BeginAnimation`, utilizzato per richiamare un'animazione in XAML.|
|Convalide|Contiene le classi necessarie per la convalida dell'input dei dati.|
|ViewModel|Contiene la logica dell'applicazione esposta alle pagine.|
|Visualizzazioni|Contiene le pagine per l'app.|

##### <a name="platform-projects"></a>Progetti della piattaforma

I progetti della piattaforma contengono implementazioni degli effetti, implementazioni di renderer personalizzate e altre risorse specifiche della piattaforma.

## <a name="summary"></a>Riepilogo

Gli strumenti e le piattaforme per lo sviluppo di app per dispositivi mobili multipiattaforma di Novell offrono una soluzione completa per le app client per dispositivi mobili B2E, B2B e B2C, che offrono la possibilità di condividere codice tra tutte le piattaforme di destinazione (iOS, Android e Windows) e di ridurre il costo totale di proprietà. Le app possono condividere l'interfaccia utente e il codice della logica dell'app, mantenendo al tempo stesso l'aspetto della piattaforma nativa.

Gli sviluppatori di app aziendali affrontano diverse esigenze che possono alterare l'architettura dell'app durante lo sviluppo. È quindi importante compilare un'app in modo che possa essere modificata o estesa nel tempo. La progettazione di tale adattamento può essere difficile, ma in genere comporta il partizionamento di un'app in componenti discreti, a regime di controllo libero, che possono essere facilmente integrati in un'app.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (esempio)](https://github.com/dotnet-architecture/eShopOnContainers)
