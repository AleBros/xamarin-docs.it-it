---
title: " Xamarin.Forms and Web Services" Description: "questa guida illustra come comunicare con servizi Web diversi per fornire funzionalità di creazione, lettura, aggiornamento ed eliminazione (CRUD) a un' Xamarin.Forms applicazione. Gli argomenti trattati includono la comunicazione con servizi ASMX, servizi WCF, servizi REST ".
ms. prod: Novell MS. AssetID: 8B360BDA-E4E3-4A3F-9004-0E35362F49F8 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 06/27/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-and-web-services"></a>Xamarin.Formse servizi Web

## <a name="introduction"></a>[Introduzione](introduction.md)

Questo articolo fornisce una procedura dettagliata dell' Xamarin.Forms applicazione di esempio che illustra come comunicare con diversi servizi Web. Gli argomenti trattati includono l'anatomia dell'applicazione, le pagine, il modello di dati e le operazioni di richiamo del servizio Web.

## <a name="consume-an-aspnet-web-service-asmx"></a>[Utilizzare un servizio Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

I servizi Web ASP.NET (ASMX) offrono la possibilità di creare servizi Web che inviano messaggi tramite HTTP usando Simple Object Access Protocol (SOAP). SOAP è un protocollo indipendente dalla piattaforma e indipendente dalla lingua per la creazione e l'accesso ai servizi Web. I consumer di un servizio ASMX non devono conoscere alcuna piattaforma, modello a oggetti o linguaggio di programmazione utilizzato per implementare il servizio. È sufficiente comprendere come inviare e ricevere messaggi SOAP. Questo articolo illustra come utilizzare un servizio Web ASMX da un' Xamarin.Forms applicazione.

## <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>[Utilizzare un servizio Web di Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF è il Framework unificato di Microsoft per la creazione di applicazioni orientate ai servizi. Consente agli sviluppatori di creare applicazioni distribuite sicure, affidabili, transazionali e interoperabili. Esistono differenze tra i servizi Web ASP.NET (ASMX) e WCF, ma è importante comprendere che WCF supporta le stesse funzionalità fornite da ASMX, ovvero messaggi SOAP su HTTP. In questo articolo viene illustrato come utilizzare un servizio SOAP WCF da un' Xamarin.Forms applicazione.

## <a name="consume-a-restful-web-service"></a>[Utilizzare un servizio Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)

REST (Representational State Transfer) è uno stile di architettura per la creazione di servizi Web. Le richieste REST vengono effettuate tramite HTTP usando gli stessi verbi HTTP usati dai Web browser per recuperare le pagine Web e inviare dati ai server. Questo articolo illustra come utilizzare un servizio Web RESTful da un' Xamarin.Forms applicazione.
