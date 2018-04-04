---
title: Modelli di applicazione aziendale con xamarin. Forms eBook
description: Architettura per lo sviluppo di applicazioni aziendali di xamarin. Forms flessibili, gestibili e testabili
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: b7f69e987eb4f628dfaf5422bc1ce52108d90634
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>Modelli di applicazione aziendale con xamarin. Forms eBook

_Architettura per lo sviluppo di applicazioni aziendali di xamarin. Forms flessibili, gestibili e testabili_

![](images/cover-sml.png "Modelli di applicazione aziendale con xamarin. Forms eBook")

Questo eBook vengono fornite indicazioni su come implementare il modello Model-View-ViewModel (MVVM), inserimento di dipendenze, navigazione, convalida e la gestione della configurazione, mantenendo regime di controllo. Inoltre, è inoltre disponibile materiale sussidiario su come eseguire l'autenticazione e autorizzazione con IdentityServer, accesso ai dati da microservizi nei contenitori e gli unit test.

## <a name="prefaceprefacemd"></a>[Prefazione](preface.md)

In questo capitolo illustra lo scopo e ambito della Guida, e che i destinatari.

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

Gli sviluppatori di applicazioni aziendali devono affrontare sfide che è possono modificare l'architettura dell'app durante lo sviluppo. Pertanto, è importante compilare un'app in modo che può essere modificata o estesa nel tempo. Progettazione di questo tipo può essere adattato può essere difficile, ma in genere implica il partizionamento di un'app in componenti discreti, regime di controllo che possono essere facilmente integrati insieme in un'app.

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

Il modello Model-View-ViewModel (MVVM) consente di separare correttamente la logica di business e la presentazione di un'applicazione dalla relativa interfaccia utente (UI). Mantenere una netta separazione tra la logica dell'applicazione e l'interfaccia utente consente di risolvere numerosi problemi di sviluppo e può rendere più facile da testare, gestire e sviluppare un'applicazione. Può migliorare notevolmente anche la possibilità di riutilizzare codice e consente agli sviluppatori e progettisti dell'interfaccia utente più collaborano facilmente quando si sviluppano le rispettive parti di un'app.

## <a name="dependency-injectiondependency-injectionmd"></a>[Inserimento dipendenze](dependency-injection.md)

Inserimento di dipendenze consente la separazione di tipi concreti dal codice che dipende da questi tipi. In genere Usa un contenitore che include un elenco di mapping tra le interfacce e tipi astratti e le registrazioni e i tipi concreti che implementano o estendono questi tipi.

I contenitori di dipendenze di ridurre l'accoppiamento tra oggetti fornendo una struttura per creare un'istanza di istanze di classe e gestire la durata in base alla configurazione del contenitore. Durante la creazione di oggetti, il contenitore inserisce tutte le dipendenze che richiede l'oggetto al suo interno. Se queste dipendenze non è sono ancora create, il contenitore crea e consente di risolvere innanzitutto le relative dipendenze.

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[Comunicazioni tra componenti poco accoppiati](communicating-between-loosely-coupled-components.md)

Di xamarin. Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe implementa la pubblicazione-modello, consentendo la comunicazione basata su messaggi tra i componenti che sono poco pratico da collegare con riferimenti di oggetto e il tipo di sottoscrizione. Questo meccanismo consente i server di pubblicazione e sottoscrittori di comunicare senza un riferimento a altro, contribuendo a ridurre le dipendenze tra componenti, consentendo anche componenti sviluppati e testati in modo indipendente.

## <a name="navigationnavigationmd"></a>[Navigazione](navigation.md)

Xamarin. Forms include il supporto per lo spostamento di pagina, che comporta in genere dall'interazione dell'utente con l'interfaccia utente o dall'app, in seguito alle modifiche di stato interno basata sulla logica. Tuttavia, navigazione può essere complessa da implementare nelle App che usano il modello MVVM.

Questo capitolo illustra un `NavigationService` (classe), che viene utilizzato per eseguire sulla navigazione nella visualizzazione modello prima dalla visualizzazione di modelli. Inserire la logica di navigazione nella visualizzazione classi del modello indica che la logica può essere esercitata tramite test automatizzati. Inoltre, il modello di visualizzazione possa quindi implementare la logica per navigazione del controllo per assicurarsi che vengono applicate alcune regole di business.

## <a name="validationvalidationmd"></a>[Convalida](validation.md)

Qualsiasi app che accetta l'input dell'utente è necessario assicurarsi che l'input è valido. Senza convalida, l'utente può fornire dati che causano l'esito negativo dell'app. Convalida applica le regole di business e impedisce che un utente malintenzionato inserendo dati dannosi.

Nel contesto del modello-modello ViewModel (MVVM) di schema, un modello di visualizzazione o modello sarà spesso necessario per eseguire la convalida dei dati e segnalare gli errori di convalida per la visualizzazione in modo che l'utente può correggere questi ultimi.

## <a name="configuration-managementconfiguration-managementmd"></a>[Gestione della configurazione](configuration-management.md)

Le impostazioni consentono la separazione dei dati che consente di configurare il comportamento di un'app dal codice, il comportamento possono essere modificati senza ricompilare l'app. Le impostazioni dell'App sono dati di un'app vengono create e gestite e impostazioni utente sono le impostazioni personalizzabili di un'app che influiscono sul comportamento dell'app e non richiedono frequenti regolazione nuovamente.

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[Microservizi in contenitori](containerized-microservices.md)

Microservizi offrono un approccio di sviluppo di applicazioni e di distribuzione che è adatta ai requisiti di flessibilità, scalabilità e affidabilità delle applicazioni cloud moderne. Uno dei principali vantaggi di microservizi è che possono essere scalate orizzontalmente in modo indipendente, il che significa che un'area funzionale specifica può essere scalata che richiede l'elaborazione dell'alimentazione o rete larghezza di banda per supportare la domanda, senza ridimensionamento inutilmente aree della l'applicazione in cui non si verifica un aumento della domanda.

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[Autenticazione e autorizzazione](authentication-and-authorization.md)

Esistono molti approcci per l'integrazione di autenticazione e autorizzazione in un'app xamarin. Forms che comunica con un'applicazione web ASP.NET MVC. In questo caso, l'autenticazione e autorizzazione vengono eseguiti con un microservizio identità nei contenitori che utilizza 4 IdentityServer. IdentityServer è un framework open source di OpenID Connect e OAuth 2.0 per ASP.NET Core che si integra con ASP.NET Identity Core per eseguire l'autenticazione token di connessione.

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[Accesso ai dati remoti](accessing-remote-data.md)

Molte soluzioni moderne basate sul web utilizzo di servizi web ospitati in server web, per fornire funzionalità di client remoti applicazioni. Le operazioni che espone un servizio web costituiscano un'API web e applicazioni client devono essere in grado di utilizzare l'API web senza conoscere la modalità di implementazione delle operazioni che espone l'API o i dati.

## <a name="unit-testingunit-testingmd"></a>[Testing unità](unit-testing.md)

Testing di modelli e visualizzazione di modelli da applicazioni MVVM è identico per tutte le altre classi di test e gli stessi strumenti e tecniche possono essere utilizzate. Tuttavia, esistono alcuni modelli tipici di modello e le classi di modello di visualizzazione, che possono trarre vantaggio dalle tecniche di test unità specifica.

## <a name="feedback"></a>Commenti e suggerimenti

Questo progetto contiene un sito della community, in cui è possibile inserire domande e fornire commenti e suggerimenti. Sito della community si trova in [GitHub](https://github.com/dotnet-architecture/eShopOnContainers). In alternativa, commenti e suggerimenti su eBook possono essere inviato tramite posta elettronica [ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com).


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
