---
title: Dati e servizi cloud
description: Le applicazioni xamarin. Forms possono utilizzare servizi web implementati tramite un'ampia gamma di tecnologie e in questa guida esaminerà come eseguire questa operazione.
ms.prod: xamarin
ms.assetid: 0601D9D0-C8D2-4C3B-A749-A340BDBF64A4ß
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 6e67820fa83ddea46f934b4eaedde2c6334f9cc6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61327506"
---
# <a name="data--cloud-services"></a>Dati e servizi cloud

_Le applicazioni xamarin. Forms possono utilizzare servizi web implementati tramite un'ampia gamma di tecnologie e in questa guida esaminerà come eseguire questa operazione._

Per un'introduzione all'uso del servizio web multipiattaforma nella piattaforma Xamarin, vedere [Introduzione ai servizi Web](~/cross-platform/data-cloud/web-services/index.md).

## <a name="understanding-the-samplexamarin-formsdata-cloudwalkthroughmd"></a>[Informazioni sull'esempio](~/xamarin-forms/data-cloud/walkthrough.md)

Questo articolo fornisce una procedura dettagliata dell'applicazione di esempio xamarin. Forms che illustra come comunicare con i servizi web diversi. Gli argomenti trattati includono l'anatomia di applicazione, le pagine, modello di data e richiamando operazioni del servizio web.

## <a name="consuming-web-servicesxamarin-formsdata-cloudconsumingindexmd"></a>[Utilizzo di servizi Web](~/xamarin-forms/data-cloud/consuming/index.md)

Questa guida viene illustrato come comunicare con servizi web diversi per offrire creare, leggere, aggiornare ed eliminare la funzionalità (CRUD) a un'applicazione xamarin. Forms. Gli argomenti trattati includono comunicando [servizi ASMX](consuming/asmx.md), [servizi WCF](consuming/wcf.md), [servizi REST](consuming/rest.md), e [Azure Mobile Apps](consuming/azure.md).

## <a name="authenticating-access-to-web-servicesxamarin-formsdata-cloudauthenticationindexmd"></a>[Autenticazione dell'accesso ai servizi Web](~/xamarin-forms/data-cloud/authentication/index.md)

Questa guida illustra come integrare servizi di autenticazione in un'applicazione xamarin. Forms per consentire agli utenti di condividere un back-end mantenendo solo l'accesso ai propri dati. Gli argomenti trattati comprendono [usando l'autenticazione di base con un servizio REST](authentication/rest.md), [utilizzando il componente AUTH per l'autenticazione con provider di identità OAuth](authentication/oauth.md)e l'uso dell'autenticazione integrata meccanismi offerti da [App per dispositivi mobili di Azure](authentication/azure.md).

## <a name="synchronizing-data-with-web-servicessyncindexmd"></a>[Sincronizzazione dei dati con i servizi Web](sync/index.md)

Questo articolo illustra come aggiungere funzionalità di sincronizzazione offline per un'applicazione xamarin. Forms. La sincronizzazione offline consente agli utenti di interagire con un'applicazione per dispositivi mobili, visualizzazione, aggiunta o modifica dei dati, anche se non c'è una connessione di rete. Le modifiche vengono archiviate in un database locale e quando il dispositivo è online, le modifiche possono essere sincronizzate con il servizio web.

## <a name="sending-push-notificationspush-notificationsindexmd"></a>[Invio di notifiche push](push-notifications/index.md)

Questo articolo illustra come aggiungere notifiche push a un'applicazione xamarin. Forms. Hub di notifica di Azure forniscono un'infrastruttura push scalabile per l'invio di notifiche push mobili da qualsiasi back-end per tutte le piattaforme per dispositivi mobili, eliminando la complessità di un back-end di comunicare con diversi platform notification System.

## <a name="storing-files-in-the-cloudstorageindexmd"></a>[Archiviazione di file nel cloud](storage/index.md)

Questo articolo illustra come usare xamarin. Forms per archiviare dati di testo e binari in archiviazione di Azure e come accedere ai dati. Archiviazione di Azure è una soluzione di archiviazione cloud scalabile che può essere usata per archiviare dati non strutturati e strutturati.

## <a name="searching-data-in-the-cloudsearchindexmd"></a>[Ricerca di dati nel cloud](search/index.md)

Questo articolo illustra come usare la libreria di ricerca di Microsoft Azure per integrare ricerca di Azure in un'applicazione xamarin. Forms. Ricerca di Azure è un servizio cloud che offre funzionalità per i dati caricati di query e indicizzazione. Questa operazione rimuove i requisiti di infrastruttura e le complessità di algoritmo di ricerca tradizionalmente associate all'implementazione di funzionalità di ricerca in un'applicazione.

## <a name="storing-data-in-a-document-databasecosmosdbindexmd"></a>[Archiviazione dei dati in un database di documenti](cosmosdb/index.md)

Questa guida illustra come usare la libreria client .NET Standard di Azure Cosmos DB per l'integrazione di un database di documenti di Azure Cosmos DB in un'applicazione xamarin. Forms. Un database di documenti di Azure Cosmos DB è un database NoSQL che fornisce l'accesso a bassa latenza per i documenti JSON, che offre un servizio di database veloce, a disponibilità elevata e scalabile per le applicazioni che richiedono scalabilità e semplicità e la replica globale.

## <a name="adding-intelligence-with-cognitive-servicescognitive-servicesindexmd"></a>[Aggiunta di funzionalità di Intelligence con Servizi cognitivi](cognitive-services/index.md)

Questa guida illustra come usare alcune delle API servizi cognitivi Microsoft in un'applicazione xamarin. Forms. Servizi cognitivi sono un set di API, SDK e servizi disponibili per gli sviluppatori a rendere più intelligenti le applicazioni tramite l'aggiunta di funzionalità, ad esempio il riconoscimento facciale, vocale e language Understanding Intelligent Service.
