---
title: Xamarin.Forms e i servizi Web
description: Questa guida illustra come comunicare con servizi web diversi per offrire creare, leggere, aggiornare ed eliminare la funzionalità (CRUD) a un'applicazione Xamarin.Forms. Gli argomenti trattati includono la comunicazione con i servizi ASMX, servizi WCF, servizi REST.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: 1cf2714191528c5619b4f877bcb43e80464c44d1
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659008"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Forms e i servizi Web

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

Questo articolo fornisce una procedura dettagliata dell'applicazione di esempio Xamarin.Forms che illustra come comunicare con i servizi web diversi. Gli argomenti trattati includono l'anatomia di applicazione, le pagine, modello di data e richiamando operazioni del servizio web.

## <a name="consume-an-aspnet-web-service-asmxxamarin-formsdata-cloudweb-servicesasmxmd"></a>[Usare un servizio Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

Servizi Web ASP.NET (ASMX) offrono la possibilità di compilare servizi web che inviano messaggi su HTTP utilizzando SOAP Simple Object Access Protocol (). SOAP è un protocollo indipendente dalla piattaforma e indipendenti dal linguaggio per la creazione e accesso ai servizi web. I consumer di un servizio ASMX non sono necessario conoscere il linguaggio sul modello a oggetti, piattaforma o linguaggio di programmazione usato per implementare il servizio. Devono solo per informazioni su come inviare e ricevere messaggi SOAP. Questo articolo illustra come usare un servizio web ASMX da un'applicazione Xamarin.Forms.

## <a name="consume-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudweb-serviceswcfmd"></a>[Utilizzare un servizio Web di Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF è un framework unificato di Microsoft per la compilazione di applicazioni orientate ai servizi. Consente agli sviluppatori di compilare applicazioni distribuite sicure, affidabili, transazionali e interoperabile. Esistono differenze tra servizi Web ASP.NET (ASMX) e WCF, ma è importante comprendere che WCF supporta le stesse funzionalità che fornisce ASMX, ovvero i messaggi SOAP su HTTP. Questo articolo illustra come usare un servizio WCF SOAP da un'applicazione Xamarin.Forms.

## <a name="consume-a-restful-web-servicexamarin-formsdata-cloudweb-servicesrestmd"></a>[Utilizzare un servizio Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)

Representational State Transfer (REST) è uno stile architetturale per la creazione di servizi web. Richieste REST vengono effettuate tramite HTTP utilizzando gli stessi verbi HTTP che utilizzano i web browser per recuperare le pagine web e per inviare dati ai server. Questo articolo viene illustrato come utilizzare un servizio web RESTful da un'applicazione Xamarin.Forms.
