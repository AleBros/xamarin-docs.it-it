---
title: Introduzione
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: a4f1f6ba820221be7553405f570911d3dc66a657
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="introduction"></a>Introduzione

Indipendentemente dalla piattaforma, gli sviluppatori di applicazioni aziendali devono affrontare sfide:

-   Requisiti di App che possono cambiare nel tempo.
-   Nuove opportunità di business e problemi.
-   Feedback durante lo sviluppo che possa influire in modo significativo l'ambito e i requisiti dell'app è in corso.

A questi, è importante compilare App che possono essere facilmente modificate o estesi nel tempo. Progettazione di questo tipo può essere adattato può essere difficile poiché richiede un'architettura che consente di singole parti dell'app sviluppate e testate in isolamento senza influire sul resto dell'app in modo indipendente.

Molte applicazioni aziendali sono sufficientemente complesse da richiedere più di uno sviluppatore. Può trattarsi di una vera sfida per decidere come progettare un'applicazione in modo che più gli sviluppatori possono operare in modo efficace in diverse parti dell'app in modo indipendente, assicurando che le parti collaborano con facilità quando è integrato nell'app.

L'approccio tradizionale alla progettazione e creazione di un'app comporta cosa viene considerato un *monolitico* app, in cui componenti sono strettamente collegati con nessun una netta separazione tra di essi. In genere, questo approccio monolitico comporta le app che sono difficili ed efficiente mantenere, poiché può essere difficile da risolvere i bug senza interrompere gli altri componenti nell'app, e può essere difficile per aggiungere nuove funzionalità o per sostituire le funzionalità esistenti.

Queste sfide un rimedio efficace consiste nel partizionare un'app in componenti discreti, regime di controllo che possono essere facilmente integrati insieme in un'app. Tale approccio presenta numerosi vantaggi:

-   Consente a singole funzionalità sviluppato, testato, esteso e gestita da persone o team diversi.
-   Promuove il riutilizzo e una netta separazione delle problematiche tra le funzionalità dell'app orizzontale, ad esempio l'autenticazione e accesso ai dati e le funzionalità verticale, ad esempio la funzionalità di business specifico dell'applicazione. In questo modo le dipendenze e le interazioni tra i componenti di app per essere gestiti più facilmente.
-   Consente di mantenere una separazione dei ruoli, consentendo di persone, o team diversi, per concentrarsi su un'attività specifica o parte delle funzionalità in base alla loro competenza. In particolare, fornisce una separazione tra l'interfaccia utente e la logica di business dell'app più chiara.

Tuttavia, esistono molti problemi che devono essere risolti durante il partizionamento di un'app in componenti discreti, regime di controllo. Sono inclusi:

-   Decidere come fornire una netta separazione delle problematiche tra i controlli dell'interfaccia utente e la relativa logica. Uno dei più importanti decisioni durante la creazione di un'applicazione aziendale di xamarin. Forms è se si desidera inserire la logica di business nel file code-behind, o se si desidera creare una netta separazione delle problematiche tra i controlli dell'interfaccia utente e la loro logica, per rendere l'app più gestibili e testabili. Per ulteriori informazioni, vedere [Model-View-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md).
-   Determinare se utilizzare un contenitore dell'inserimento di dipendenza. I contenitori di dipendenze ridotta la dipendenza accoppiamento tra oggetti fornendo una struttura per costruire le istanze di classi con le relative dipendenze inseriti e gestiscono la durata in base alla configurazione del contenitore. Per ulteriori informazioni, vedere [Dependency Injection](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).
-   Scelta tra gli eventi della piattaforma fornita e fortemente accoppiato comunicazione basata su messaggi tra i componenti che sono poco pratico da riferimenti oggetto e il tipo di collegamento. Per ulteriori informazioni, vedere Introduzione a [la comunicazione tra Loosely Coupled componenti](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).
-   Decidere come spostarsi tra le pagine, inclusa la modalità di navigazione, richiamare e in cui deve risiedere la logica di spostamento. Per altre informazioni, vedere [Navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md).
-   Determinare come convalidare l'input dell'utente siano corretti. La decisione deve includere come convalidare l'input dell'utente e come inviare notifiche utente sugli errori di convalida. Per ulteriori informazioni, vedere [convalida](~/xamarin-forms/enterprise-application-patterns/validation.md).
-   Decidere come eseguire l'autenticazione e come proteggere le risorse con l'autorizzazione. Per ulteriori informazioni, vedere [autenticazione e autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md).
-   Determinare la modalità di accesso remoto ai dati dal web dei servizi, incluso come recuperare in modo affidabile i dati e come memorizzare nella cache di dati. Per ulteriori informazioni, vedere [l'accesso a dati remoti](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).
-   Decidere come testare l'app. Per ulteriori informazioni, vedere [Unit test](~/xamarin-forms/enterprise-application-patterns/unit-testing.md).

Questa guida vengono fornite informazioni aggiuntive su questi aspetti e si concentra sui modelli di core e architettura per la creazione di un'app aziendale multipiattaforma con xamarin. Forms. Le linee guida mira a consentire di produrre codice flessibile, gestibile e testabile, risolvendo i comuni scenari di sviluppo app di enterprise xamarin. Forms e separando i problemi di presentazione, logica di presentazione e le entità tramite il supporto per il Model-View-ViewModel modello MVVM ().

## <a name="sample-application"></a>Applicazione di esempio

Questa guida include un'applicazione di esempio, eShopOnContainers, che è un negozio online che include le funzionalità seguenti:

-   L'autenticazione e autorizzazione per un servizio back-end.
-   Esplorazione di un catalogo di shirts tazze caffè e altri elementi di marketing.
-   Consente di filtrare il catalogo.
-   Ordinamento degli elementi dal catalogo.
-   Visualizzazione cronologia degli ordini dell'utente.
-   Configurazione delle impostazioni.

### <a name="sample-application-architecture"></a>Architettura di applicazione di esempio

Figura 1-1 fornisce una panoramica generale dell'architettura dell'applicazione di esempio.

![](introduction-images/architecture.png "architettura di alto livello eShopOnContainers")

**Figura 1-1**: architettura di alto livello eShopOnContainers

L'applicazione di esempio sono disponibili tre applicazioni client:

-   Un'applicazione MVC sviluppate con ASP.NET Core.
-   Un'applicazione a pagina singola (SPA) sviluppate con 2 angolare e Typescript. Questo approccio per le applicazioni web evita un round trip al server con ogni operazione di esecuzione.
-   Un'app per dispositivi mobili sviluppate con xamarin. Forms, che supporta la piattaforma UWP (Universal Windows), iOS e Android.

Per informazioni sulle applicazioni web, vedere [sviluppo e lo sviluppo di moderne applicazioni Web con ASP.NET Core e Microsoft Azure](http://aka.ms/WebAppEbook).

L'applicazione di esempio include i seguenti servizi back-end:

-   Un microservizio identità, che utilizza ASP.NET Identity Core e IdentityServer.
-   Microservizio un catalogo, che è una creazione guidata dai dati, leggere, aggiornare ed eliminare il servizio (CRUD) che utilizza un database di SQL Server mediante EntityFramework Core.
-   Un microservizio ordinamento, che è un servizio basato su dominio che utilizza modelli di progettazione basati su dominio.
-   Un microservizio carrello, che è un servizio CRUD basati sui dati che usa la Cache Redis.

Questi servizi back-end vengono implementati come microservizi mediante ASP.NET MVC di base e vengono distribuiti come contenitori univoci all'interno di un singolo host Docker. Questi servizi back-end vengono detti collettivamente come il eShopOnContainers fanno riferimento l'applicazione. Le applicazioni client comunicare con i servizi back-end tramite un'interfaccia web Representational State Transfer (REST). Per ulteriori informazioni su microservizi e Docker, vedere [Microservizi contenitore](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md).

Per informazioni sull'implementazione dei servizi back-end, vedere [Microservizi .NET: architettura per le applicazioni .NET contenitore](https://aka.ms/microservicesebook).

### <a name="mobile-app"></a>App per dispositivi mobili

Questa guida è incentrata sulla creazione di applicazioni enterprise multipiattaforma con xamarin. Forms e Usa l'app mobile eShopOnContainers come esempio. Figura 1-2 mostra le pagine dall'app mobile eShopOnContainers che forniscono le funzionalità descritte in precedenza.

[![](introduction-images/screenshots.png "L'app mobile eShopOnContainers")](introduction-images/screenshots-large.png#lightbox "l'app mobile eShopOnContainers")

**Figura 1-2**: l'app mobile eShopOnContainers

L'app mobile utilizza i servizi di back-end forniti dall'applicazione di riferimento eShopOnContainers. Tuttavia, possono essere configurata per utilizzare dati da servizi fittizi per chi desidera evitare la distribuzione dei servizi back-end.

L'app mobile eShopOnContainers Usa le seguenti funzionalità di xamarin. Forms:

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
-   Renderer personalizzato
-   MessagingCenter
-   Controlli personalizzati

Per ulteriori informazioni su questa funzionalità, vedere il [documentazione di xamarin. Forms](~/xamarin-forms/index.yml), e [la creazione di App per dispositivi mobili con xamarin. Forms](https://aka.ms/xamebook).

Inoltre, gli unit test vengono forniti per alcune classi nell'app mobile eShopOnContainers.

#### <a name="mobile-app-solution"></a>Soluzione dell'App per dispositivi mobili

La soluzione dell'app mobile eShopOnContainers organizza il codice sorgente e altre risorse in progetti. Tutti i progetti usare cartelle per organizzare codice sorgente e altre risorse in categorie. Nella tabella seguente sono illustrati i progetti che costituiscono l'app mobile eShopOnContainers:

|Progetto|Descrizione|
|--- |--- |
|eShopOnContainers.Core|Questo progetto è il progetto di libreria (PCL) di classi portabile che contiene l'interfaccia utente condivisa e il codice condiviso.|
|eShopOnContainers.Droid|Questo progetto contiene codice specifico Android ed è il punto di ingresso per l'app Android.|
|eShopOnContainers.iOS|Questo progetto contiene codice specifico di iOS e il punto di ingresso per l'app iOS.|
|eShopOnContainers.UWP|Questo progetto contiene codice specifico della piattaforma UWP (Universal Windows) e il punto di ingresso per l'app di Windows.|
|eShopOnContainers.TestRunner.Droid|Questo progetto è Android test runner per il progetto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.iOS|Questo progetto è il test runner di iOS per il progetto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.Windows|Questo progetto è una piattaforma Windows Universal tramite test runner per il progetto eShopOnContainers.UnitTests.|
|eShopOnContainers.UnitTests|Questo progetto contiene unit test per il progetto eShopOnContainers.Core.|

Le classi dall'app mobile eShopOnContainers possono essere riutilizzate in qualsiasi app xamarin. Forms con senza alcuna modifica.

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers.Core Project

Il progetto di libreria di classi Portabile eShopOnContainers.Core contiene le cartelle seguenti:

|Cartella|Descrizione|
|--- |--- |
|Animations|Contiene classi che consentono di animazioni deve essere utilizzato in XAML.|
|comportamenti|Contiene i comportamenti che vengono esposte per visualizzare le classi.|
|Controlli|Contiene controlli personalizzati utilizzati dall'applicazione.|
|Convertitori di tipi|Contiene i convertitori di valori che si applicano la logica personalizzata a un'associazione.|
|Effetti|Contiene il `EntryLineColorEffect` (classe), che consente di modificare il colore del bordo di specifico `Entry` controlli.|
|Eccezioni|Contiene l'oggetto personalizzato `ServiceAuthenticationException`.|
|Estensioni|Contiene metodi di estensione per il `VisualElement` e `IEnumerable` classi.|
|Helper|Contiene le classi di supporto per l'app.|
|Modelli|Contiene le classi di modello per l'app.|
|Proprietà|Contiene `AssemblyInfo.cs`, un file di metadati di assembly .NET.|
|Servizi|Contiene le interfacce e classi che implementano i servizi forniti all'app.|
|Trigger|Contiene il `BeginAnimation` trigger, viene utilizzato per richiamare un'animazione in XAML.|
|Convalide|Contiene le classi coinvolte nella convalida dei dati di input.|
|ViewModel|Contiene la logica dell'applicazione che viene esposto a pagine.|
|Visualizzazioni|Contiene le pagine per l'app.|

##### <a name="platform-projects"></a>Progetti di piattaforma

I progetti di piattaforma contengono implementazioni effetto, le implementazioni di renderer personalizzato e altre risorse specifiche della piattaforma.

## <a name="summary"></a>Riepilogo

Gli strumenti di sviluppo di app per dispositivi mobili multipiattaforma con Xamarin e piattaforme offrono una soluzione completa per client mobili B2E B2B e B2C App, fornendo la possibilità di condividere il codice tra tutte le piattaforme di destinazione (iOS, Android e Windows) e contribuire a ridurre il costo totale di proprietà. App è possono condividere il codice di logica app e l'interfaccia utente, mantenendo l'aspetto della piattaforma nativa.

Gli sviluppatori di applicazioni aziendali devono affrontare sfide che è possono modificare l'architettura dell'app durante lo sviluppo. Pertanto, è importante compilare un'app in modo che può essere modificata o estesa nel tempo. Progettazione di questo tipo può essere adattato può essere difficile, ma in genere implica il partizionamento di un'app in componenti discreti, regime di controllo che possono essere facilmente integrati insieme in un'app.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
