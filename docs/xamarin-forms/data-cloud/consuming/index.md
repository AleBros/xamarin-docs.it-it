---
title: Utilizzo di servizi Web
description: Questa guida viene illustrato come comunicare con servizi web diversi per offrire creare, leggere, aggiornare ed eliminare la funzionalità (CRUD) a un'applicazione xamarin. Forms. Gli argomenti trattati includono la comunicazione con i servizi ASMX, WCF services, servizi REST e le App per dispositivi mobili di Azure.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: a4c842ea7fd37ade9be0a9cb3e3ff7e50a6d1491
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61328190"
---
# <a name="consuming-web-services"></a>Utilizzo di servizi Web

Ques_to Guida viene illustrato come comunicare con servizi web diversi per offrire creare, leggere, aggiornare ed eliminare la funzionalità (CRUD) a un'applicazione xamarin. Forms. Gli argomenti trattati includono la comunicazione con i servizi ASMX, WCF services, servizi REST e le App per dispositivi mobili di Azure.

## <a name="consuming-an-aspnet-web-service-asmxxamarin-formsdata-cloudconsumingasmxmd"></a>[Utilizzo di un servizio Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)

Servizi Web ASP.NET (ASMX) offrono la possibilità di compilare servizi web che inviano messaggi su HTTP utilizzando SOAP Simple Object Access Protocol (). SOAP è un protocollo indipendente dalla piattaforma e indipendenti dal linguaggio per la creazione e accesso ai servizi web. I consumer di un servizio ASMX non sono necessario conoscere il linguaggio sul modello a oggetti, piattaforma o linguaggio di programmazione usato per implementare il servizio. Devono solo per informazioni su come inviare e ricevere messaggi SOAP. Questo articolo illustra come usare un servizio web ASMX da un'applicazione xamarin. Forms.

## <a name="consuming-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudconsumingwcfmd"></a>[Utilizzo di un servizio Web di Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md)

WCF è un framework unificato di Microsoft per la compilazione di applicazioni orientate ai servizi. Consente agli sviluppatori di compilare applicazioni distribuite sicure, affidabili, transazionali e interoperabile. Esistono differenze tra servizi Web ASP.NET (ASMX) e WCF, ma è importante comprendere che WCF supporta le stesse funzionalità che fornisce ASMX, ovvero i messaggi SOAP su HTTP. Questo articolo illustra come usare un servizio WCF SOAP da un'applicazione xamarin. Forms.

## <a name="consuming-a-restful-web-servicexamarin-formsdata-cloudconsumingrestmd"></a>[Utilizzo di un servizio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)

Representational State Transfer (REST) è uno stile architetturale per la creazione di servizi web. Richieste REST vengono effettuate tramite HTTP utilizzando gli stessi verbi HTTP che utilizzano i web browser per recuperare le pagine web e per inviare dati ai server. Questo articolo viene illustrato come utilizzare un servizio web RESTful da un'applicazione xamarin. Forms.

## <a name="consuming-an-azure-mobile-appxamarin-formsdata-cloudconsumingazuremd"></a>[Utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md)

Le App per dispositivi mobili di Azure consentono di sviluppare App con back-end scalabile ospitati nel servizio App di Azure con supporto per l'autenticazione per dispositivi mobili, la sincronizzazione offline e notifiche push. Questo articolo, che è applicabile solo alle App per dispositivi mobili di Azure che usano un back-end Node. js, illustra come eseguire una query, inserire, aggiornare ed eliminare i dati archiviati in una tabella in un'istanza di App per dispositivi mobili di Azure.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione ai servizi Web](~/cross-platform/data-cloud/web-services/index.md)
- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
