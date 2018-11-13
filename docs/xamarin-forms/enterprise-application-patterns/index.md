---
title: Modelli di applicazioni aziendali tramite e-book di xamarin. Forms
description: Questo eBook fornisce indicazioni sull'architettura per lo sviluppo di applicazioni aziendali di xamarin. Forms flessibili, gestibili e testabili.
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: f972a32f8daf920f2121e5aa56923c0f3a7f808a
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528442"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>Modelli di applicazioni aziendali tramite e-book di xamarin. Forms

_Indicazioni sull'architettura per lo sviluppo di applicazioni aziendali di xamarin. Forms flessibili, gestibili e testabili_

![](images/cover-sml.png "Modelli di applicazioni aziendali tramite e-book di xamarin. Forms")

Questo e-book offre indicazioni su come implementare il modello Model-View-ViewModel (MVVM), inserimento di dipendenze, navigazione, convalida e gestione della configurazione, mantenendo il regime di controllo libero. Inoltre, è inoltre disponibile materiale sussidiario su come eseguire l'autenticazione e autorizzazione con IdentityServer, accedono ai dati dai microservizi in contenitori e gli unit test.

## <a name="prefaceprefacemd"></a>[Prefazione](preface.md)

In questo capitolo viene illustrato lo scopo e ambito della Guida, e che i destinatari.

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

Gli sviluppatori di App aziendali affrontano numerose sfide che possono modificare l'architettura dell'app durante lo sviluppo. Pertanto, è importante compilare un'app in modo che può essere modificato o esteso nel corso del tempo. Progettazione per l'adattabilità tali può essere difficile, ma in genere comporta il partizionamento di un'app in componenti discreti e loosely coupled che possono essere facilmente integrati tra loro in un'app.

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

Il modello Model-View-ViewModel (MVVM) consente di separare correttamente la logica di business e la presentazione di un'applicazione dalla relativa interfaccia utente (UI). Mantenere una netta separazione tra logica dell'applicazione e l'interfaccia utente consente di risolvere numerosi problemi di sviluppo e rendono più semplice da testare, gestire e sviluppare un'applicazione. Viene anche notevolmente può migliorare le opportunità di riutilizzo di codice e consente agli sviluppatori le finestre di progettazione dell'interfaccia utente più facilmente per collaborare e quando si sviluppano le rispettive parti di un'app.

## <a name="dependency-injectiondependency-injectionmd"></a>[Inserimento dipendenze](dependency-injection.md)

Inserimento di dipendenze consente la separazione di tipi concreti dal codice che dipende da questi tipi. In genere Usa un contenitore che include un elenco delle registrazioni e i mapping tra le interfacce e tipi astratti e i tipi concreti che implementano o estendono questi tipi.

I contenitori di dipendenze di ridurre l'accoppiamento tra gli oggetti, fornendo una funzionalità per creare un'istanza di istanze della classe e gestirne la durata in base alla configurazione del contenitore. Durante la creazione di oggetti, il contenitore inserisce tutte le dipendenze che richiede l'oggetto al suo interno. Se queste dipendenze non sono ancora state create, il contenitore viene creato e risolve prima di tutto le relative dipendenze.

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[Comunicazioni tra componenti poco accoppiati](communicating-between-loosely-coupled-components.md)

Xamarin. Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe implementa la pubblicazione-sottoscrizione modello, che consente la comunicazione basata su messaggi tra i componenti che sono poco pratici di collegamento dai riferimenti oggetto e il tipo. Questo meccanismo consente i server di pubblicazione e sottoscrittori di comunicare senza che sia un riferimento tra loro, contribuendo a ridurre le dipendenze tra componenti, consentendo anche di componenti a essere sviluppate e testate in modo indipendente.

## <a name="navigationnavigationmd"></a>[Navigazione](navigation.md)

Xamarin. Forms include il supporto per la navigazione tra le pagine, che in genere comporta dall'interazione dell'utente con l'interfaccia utente o dall'app stessa, in seguito a modifiche di stato interno basato su logica. Tuttavia, navigazione può essere complessa da implementare nelle App che usano il modello MVVM.

In questo capitolo viene presentato un `NavigationService` (classe), che consente di eseguire view model-first navigazione dalla visualizzazione di modelli. Inserire la logica di navigazione nella visualizzazione di classi del modello significa che la logica può essere usata tramite i test automatizzati. Inoltre, il modello di visualizzazione possa quindi implementare la logica per controllare il passaggio per garantire che vengano applicate alcune regole di business.

## <a name="validationvalidationmd"></a>[Convalida](validation.md)

Qualsiasi app che accetta l'input dagli utenti devono assicurarsi che l'input è valido. Senza la convalida, è possibile fornire i dati che fa sì che l'app avrà esito negativo. Convalida verranno applicate le regole di business e impedisce a un utente malintenzionato di inserimento di dati dannosi.

Nel contesto di Model-View-ViewModel (MVVM) modello, un modello di visualizzazione o modello sarà spesso necessario per eseguire la convalida dei dati e segnalare eventuali errori di convalida per la visualizzazione in modo che l'utente ne consente la correzione.

## <a name="configuration-managementconfiguration-managementmd"></a>[Gestione della configurazione](configuration-management.md)

Le impostazioni consentono la separazione dei dati che consente di configurare il comportamento di un'app dal codice, consentendo il comportamento possono essere modificati senza ricompilare l'app. Le impostazioni dell'App sono dati che un'app crea e gestisce e impostazioni utente sono le impostazioni personalizzabili di un'app che influiscono sul comportamento dell'app e non richiedono frequenti regolazione nuovamente.

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[Microservizi in contenitori](containerized-microservices.md)

I Microservizi offrono un approccio allo sviluppo di applicazioni e alla distribuzione che è adatto ai requisiti di flessibilità, scalabilità e affidabilità delle applicazioni cloud moderne. Uno dei principali vantaggi dei microservizi è che possono essere con scalabilità orizzontale in modo indipendente, il che significa che può essere ridimensionata una determinata area funzionale che richiede più l'elaborazione dell'alimentazione o rete della larghezza di banda per supportare la domanda, senza scalabilità inutilmente aree della l'applicazione in cui non si verifica un aumento della domanda.

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[Autenticazione e autorizzazione](authentication-and-authorization.md)

Esistono molti approcci per l'integrazione di autenticazione e autorizzazione in un'app xamarin. Forms che comunica con un'applicazione web ASP.NET MVC. In questo caso, l'autenticazione e autorizzazione vengono eseguiti con un microservizio di identità in contenitori che utilizza 4 IdentityServer. IdentityServer è un framework open source di OpenID Connect e OAuth 2.0 per ASP.NET Core che si integra con ASP.NET Core Identity per eseguire l'autenticazione del bearer token.

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[Accesso ai dati remoti](accessing-remote-data.md)

Molte soluzioni moderne basate sul web rendere l'uso dei servizi web, ospitate dai server web, per fornire funzionalità per client remoto delle applicazioni. Le operazioni che espone un servizio web costituiscono un'API web e App client devono essere in grado di usare l'API web senza conoscere come vengono implementate i dati o le operazioni che espone l'API.

## <a name="unit-testingunit-testingmd"></a>[Testing unità](unit-testing.md)

Test di modelli di visualizzazione dei modelli e dalle applicazioni MVVM è identico a un'altra classe di test e gli stessi strumenti e tecniche da utilizzare. Tuttavia, esistono alcuni modelli tipici al modello e le classi di modello di visualizzazione, che possono trarre vantaggio dalle tecniche di test di unità specifica.

## <a name="feedback"></a>Commenti e suggerimenti

Questo progetto dispone di un sito di community, in cui è possibile pubblicare domande e fornire commenti e suggerimenti. Il sito della community si trova sul [GitHub](https://github.com/dotnet-architecture/eShopOnContainers). In alternativa, commenti e suggerimenti sull'e-book possano essere inviati al [ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com).


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
