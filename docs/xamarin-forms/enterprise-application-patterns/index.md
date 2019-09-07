---
title: Modelli di applicazioni aziendali con l'eBook Novell. Forms
description: Questo eBook fornisce indicazioni sull'architettura per lo sviluppo di applicazioni aziendali Novell. Forms adattabili, gestibili e testabili.
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: a26fdd931f539da990e21166eec361fd1702de9c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760209"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>Modelli di applicazioni aziendali con l'eBook Novell. Forms

_Linee guida per l'architettura per lo sviluppo di applicazioni aziendali Novell. Forms adattabili, gestibili e testabili_

![](images/cover-sml.png "Modelli di applicazioni aziendali con l'eBook Novell. Forms")

Questo eBook fornisce indicazioni su come implementare il modello MVVM (Model-View-ViewModel), l'inserimento delle dipendenze, la navigazione, la convalida e la gestione della configurazione, mantenendo l'accoppiamento libero. Inoltre, sono disponibili indicazioni per l'esecuzione dell'autenticazione e dell'autorizzazione con IdentityServer, l'accesso ai dati da microservizi in contenitori e il testing unità.

## <a name="prefaceprefacemd"></a>[Prefazione](preface.md)

In questo capitolo vengono illustrati lo scopo e l'ambito della guida e i destinatari.

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

Gli sviluppatori di app aziendali affrontano diverse esigenze che possono alterare l'architettura dell'app durante lo sviluppo. È quindi importante compilare un'app in modo che possa essere modificata o estesa nel tempo. La progettazione di tale adattamento può essere difficile, ma in genere comporta il partizionamento di un'app in componenti discreti, a regime di controllo libero, che possono essere facilmente integrati in un'app.

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

Il modello MVC (Model-View-ViewModel) consente di separare nettamente la logica di business e di presentazione di un'applicazione dalla relativa interfaccia utente (UI). Il mantenimento di una netta separazione tra la logica dell'applicazione e l'interfaccia utente consente di risolvere numerosi problemi di sviluppo e può semplificare la verifica, la manutenzione e l'evoluzione di un'applicazione. Può anche migliorare significativamente le opportunità di riutilizzo del codice e consente agli sviluppatori e ai progettisti di interfaccia utente di collaborare più facilmente durante lo sviluppo delle rispettive parti di un'app.

## <a name="dependency-injectiondependency-injectionmd"></a>[Inserimento dipendenze](dependency-injection.md)

L'inserimento di dipendenze consente di separare i tipi concreti dal codice che dipende da questi tipi. USA in genere un contenitore che include un elenco di registrazioni e mapping tra interfacce e tipi astratti e i tipi concreti che implementano o estendono questi tipi.

I contenitori di inserimento delle dipendenze riducono l'accoppiamento tra oggetti fornendo una struttura per creare istanze di classi e gestirne la durata in base alla configurazione del contenitore. Durante la creazione degli oggetti, il contenitore inserisce tutte le dipendenze necessarie per l'oggetto. Se tali dipendenze non sono ancora state create, il contenitore crea e risolve prima le dipendenze.

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[Comunicazioni tra componenti poco accoppiati](communicating-between-loosely-coupled-components.md)

La classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) di Xamarin.Forms implementa il modello di pubblicazione-sottoscrizione, consentendo la comunicazione basata su messaggi tra componenti che non è pratico collegare con riferimenti a oggetti e tipi. Questo meccanismo consente agli editori e ai sottoscrittori di comunicare senza avere un riferimento reciproco, contribuendo a ridurre le dipendenze tra i componenti, consentendo allo stesso tempo di sviluppare e testare i componenti in modo indipendente.

## <a name="navigationnavigationmd"></a>[Navigazione](navigation.md)

Novell. Forms include il supporto per la navigazione tra le pagine, che in genere risulta dall'interazione dell'utente con l'interfaccia utente o dall'app stessa, in seguito a modifiche dello stato guidate dalla logica interna. Tuttavia, la navigazione può essere complessa da implementare nelle app che usano il modello MVVM.

In questo capitolo viene `NavigationService` presentata una classe, che viene utilizzata per eseguire l'esplorazione del primo modello di visualizzazione dai modelli di visualizzazione. L'inserimento della logica di navigazione nelle classi del modello di visualizzazione significa che la logica può essere esercitata tramite test automatizzati. Inoltre, il modello di visualizzazione può implementare la logica per controllare la navigazione per assicurarsi che vengano applicate determinate regole business.

## <a name="validationvalidationmd"></a>[Convalida](validation.md)

Tutte le app che accettano input dagli utenti devono assicurarsi che l'input sia valido. Senza convalida, un utente può fornire dati che causano l'esito negativo dell'app. La convalida impone regole business e impedisce a un utente malintenzionato di inserire dati dannosi.

Nel contesto del modello MVC (Model-View-ViewModel), sarà spesso necessario un modello di visualizzazione o un modello per eseguire la convalida dei dati e segnalare eventuali errori di convalida alla visualizzazione in modo che l'utente possa correggerli.

## <a name="configuration-managementconfiguration-managementmd"></a>[Gestione della configurazione](configuration-management.md)

Le impostazioni consentono la separazione dei dati che configura il comportamento di un'app dal codice, consentendo la modifica del comportamento senza ricompilare l'app. Le impostazioni dell'app sono dati che un'app crea e gestisce e le impostazioni utente sono le impostazioni personalizzabili di un'app che influiscono sul comportamento dell'app e non richiedono una riregolazione frequente.

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[Microservizi in contenitori](containerized-microservices.md)

I microservizi offrono un approccio per lo sviluppo e la distribuzione di applicazioni, adatti ai requisiti di agilità, scalabilità e affidabilità delle applicazioni cloud moderne. Uno dei vantaggi principali dei microservizi è che è possibile scalare orizzontalmente in modo indipendente, il che significa che è possibile ridimensionare un'area funzionale specifica che richiede una maggiore potenza di elaborazione o larghezza di banda di rete per supportare la richiesta, senza inutilmente ridimensionare le aree di applicazione che non sta riscontrando un aumento della domanda.

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[Autenticazione e autorizzazione](authentication-and-authorization.md)

Sono disponibili molti approcci per l'integrazione di autenticazione e autorizzazione in un'app Novell. Forms che comunica con un'applicazione Web MVC ASP.NET. In questo caso, l'autenticazione e l'autorizzazione vengono eseguite con un microservizio di identità in contenitori che usa IdentityServer 4. IdentityServer è un framework open source di OpenID Connect e OAuth 2,0 per ASP.NET Core che si integra con ASP.NET Core identità per eseguire bearer token l'autenticazione.

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[Accesso ai dati remoti](accessing-remote-data.md)

Molte soluzioni moderne basate sul Web utilizzano i servizi Web, ospitati da server Web, per fornire funzionalità per le applicazioni client Remote. Le operazioni esposte da un servizio Web costituiscono un'API Web e le app client devono essere in grado di utilizzare l'API Web senza conoscere il modo in cui vengono implementati i dati o le operazioni esposte dall'API.

## <a name="unit-testingunit-testingmd"></a>[Testing unità](unit-testing.md)

Il testing di modelli e modelli di visualizzazione da applicazioni MVVM è identico a quello di altre classi ed è possibile usare gli stessi strumenti e tecniche. Tuttavia, esistono alcuni modelli tipici per modellare e visualizzare le classi di modelli, che possono trarre vantaggio da specifiche tecniche di unit test.

## <a name="feedback"></a>Commenti e suggerimenti

Questo progetto include un sito della community in cui è possibile pubblicare domande e fornire commenti e suggerimenti. Il sito della community si trova in [GitHub](https://github.com/dotnet-architecture/eShopOnContainers). In alternativa, è possibile inviare un messaggio di posta elettronica a [dotnet-architecture-ebooks-feedback@service.microsoft.com](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com)commenti e suggerimenti sull'eBook.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
