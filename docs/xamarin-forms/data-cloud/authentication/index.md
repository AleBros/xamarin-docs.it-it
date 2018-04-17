---
title: Autenticazione dell'accesso ai servizi Web
description: Questa guida illustra come integrare servizi di autenticazione in un'applicazione di xamarin. Forms per consentire agli utenti di condividere un back-end durante l'accesso solo ai propri dati. Gli argomenti trattati includono l'autenticazione di base con un servizio REST, utilizzando il componente Xamarin.Auth effettuare l'autenticazione presso il provider di identità di OAuth, e utilizzando i meccanismi di autenticazione predefinito offerto da provider diversi.
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: bc34cf265885708fa6392936a8dbc9d82796e2fd
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="authenticating-access-to-web-services"></a>Autenticazione dell'accesso ai servizi Web

_Questa guida illustra come integrare servizi di autenticazione in un'applicazione di xamarin. Forms per consentire agli utenti di condividere un back-end durante l'accesso solo ai propri dati. Gli argomenti trattati includono l'autenticazione di base con un servizio REST, utilizzando il componente Xamarin.Auth effettuare l'autenticazione presso il provider di identità di OAuth, e utilizzando i meccanismi di autenticazione predefinito offerto da provider diversi._

## <a name="authenticating-a-restful-web-servicerestmd"></a>[L'autenticazione di un servizio Web RESTful](rest.md)

HTTP supporta l'utilizzo di diversi meccanismi di autenticazione per controllare l'accesso alle risorse. L'autenticazione di base fornisce l'accesso alle risorse per solo i client che hanno le credenziali corrette. In questo articolo viene illustrato come utilizzare l'autenticazione di base per proteggere l'accesso alle risorse del servizio web RESTful.

## <a name="authenticating-users-with-an-identity-provideroauthmd"></a>[L'autenticazione degli utenti con un Provider di identità](oauth.md)

Xamarin.Auth è un SDK multipiattaforma per l'autenticazione degli utenti e gli account di archiviazione. Include gli autenticatori OAuth che forniscono il supporto per l'utilizzo del provider di identità, ad esempio Google, Microsoft, Facebook e Twitter. In questo articolo viene illustrato come utilizzare Xamarin.Auth per gestire il processo di autenticazione in un'applicazione di xamarin. Forms.

## <a name="authenticating-users-with-azure-mobile-appsazuremd"></a>[L'autenticazione degli utenti con App per dispositivi mobili di Azure](azure.md)

App per dispositivi mobili Azure usare un'ampia gamma di provider di identità esterno per supportare l'autenticazione e autorizzazione degli utenti dell'applicazione. Quindi, è possibile impostare autorizzazioni sulle tabelle per limitare l'accesso solo agli utenti autenticati. In questo articolo viene illustrato come utilizzare App mobili di Azure per gestire il processo di autenticazione in un'applicazione di xamarin. Forms.

## <a name="authenticating-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[L'autenticazione degli utenti con Azure Active Directory B2C](azure-ad-b2c.md)

Azure Active B2C di Directory è una soluzione di gestione di identità cloud per applicazioni mobili e web per consumatori. In questo articolo viene illustrato come utilizzare Microsoft Authentication Library (MSAL) e Azure Active Directory B2C per integrare la gestione delle identità utente in un'applicazione di xamarin. Forms.

## <a name="integrating-azure-active-directory-b2c-with-azure-mobile-appsazure-ad-b2c-mobile-appmd"></a>[Integrazione di Azure Active Directory B2C con App per dispositivi mobili di Azure](azure-ad-b2c-mobile-app.md)

Per gestire il flusso di lavoro di autenticazione per App mobili di Azure, è possibile utilizzare Azure B2C Directory attiva. Con questo approccio, l'esperienza di gestione di identità è completamente definita nel cloud e può essere modificato senza modificare il codice dell'applicazione per dispositivi mobili. In questo articolo viene illustrato come usare Azure Active Directory B2C per fornire l'autenticazione e autorizzazione a un'istanza di Azure App per dispositivi mobili con xamarin. Forms.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione ai servizi Web](~/cross-platform/data-cloud/web-services/index.md)
- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
