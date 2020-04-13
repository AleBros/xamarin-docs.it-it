---
title: Xamarin.Forms e servizi Web
description: In questa guida viene illustrato come comunicare con diversi servizi Web per fornire funzionalità di creazione, lettura, aggiornamento ed eliminazione (CRUD) a un'applicazione Xamarin.Forms. Gli argomenti trattati includono la comunicazione con i servizi ASMX, i servizi WCF e i servizi REST.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: 799a0a97f7c1a212b10dc62f8b3e7bd2cf2060c8
ms.sourcegitcommit: bf3b5925018e1bd6a00505e9f37500761b66809d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80395451"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Forms e servizi Web

## <a name="introduction"></a>[Introduzione](introduction.md)

In questo articolo viene fornita una procedura dettagliata dell'applicazione di esempio Xamarin.Forms che illustra come comunicare con diversi servizi Web. Gli argomenti trattati includono l'anatomia dell'applicazione, le pagine, il modello di dati e la chiamata delle operazioni del servizio Web.

## <a name="consume-an-aspnet-web-service-asmx"></a>[Utilizzare un servizio Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

ASP.NET Web Services (ASMX) consentono di creare servizi Web che inviano messaggi tramite HTTP utilizzando il protocollo SOAP (Simple Object Access Protocol). SOAP è un protocollo indipendente dalla piattaforma e dal linguaggio per la creazione e l'accesso ai servizi Web. I consumer di un servizio ASMX non devono sapere nulla sulla piattaforma, sul modello a oggetti o sul linguaggio di programmazione utilizzato per implementare il servizio. Hanno solo bisogno di capire come inviare e ricevere messaggi SOAP. In questo articolo viene illustrato come utilizzare un servizio Web ASMX da un'applicazione Xamarin.Forms.

## <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>[Utilizzo di un servizio Web Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF è il framework unificato di Microsoft per la creazione di applicazioni orientate ai servizi. Consente agli sviluppatori di creare applicazioni distribuite sicure, affidabili, transazionali e interoperabili. Esistono differenze tra ASP.NET Web Services (ASMX) e WCF, ma è importante comprendere che WCF supporta le stesse funzionalità fornite da ASMX, ovvero i messaggi SOAP su HTTP. In questo articolo viene illustrato come utilizzare un servizio SOAP WCF da un'applicazione Xamarin.Forms.This article demonstrates how to consume an WCF SOAP service from a Xamarin.Forms application.

## <a name="consume-a-restful-web-service"></a>[Utilizzo di un servizio Web RESTfulConsume a RESTful Web Service](~/xamarin-forms/data-cloud/web-services/rest.md)

Representational State Transfer (REST) è uno stile architettonico per la creazione di servizi Web. Le richieste REST vengono effettuate tramite HTTP utilizzando gli stessi verbi HTTP utilizzati dai browser Web per recuperare pagine Web e inviare dati ai server. In questo articolo viene illustrato come utilizzare un servizio Web RESTful da un'applicazione Xamarin.Forms.
