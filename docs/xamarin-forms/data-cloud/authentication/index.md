---
title: Autenticazione dell'accesso ai servizi Web
description: Questa guida illustra come integrare servizi di autenticazione in un'applicazione xamarin. Forms per consentire agli utenti di condividere un back-end mantenendo solo l'accesso ai propri dati.
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: d598a9b3de31ea6823530f911c3544bf3cebb37f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331182"
---
# <a name="authenticating-access-to-web-services"></a>Autenticazione dell'accesso ai servizi Web

_Questa guida illustra come integrare servizi di autenticazione in un'applicazione xamarin. Forms per consentire agli utenti di condividere un back-end mantenendo solo l'accesso ai propri dati. Gli argomenti trattati includono l'uso di autenticazione di base con un servizio REST, tramite il componente AUTH per l'autenticazione con provider di identità OAuth, e usando i meccanismi di autenticazione predefiniti offerti da diversi provider._

## <a name="authenticating-a-restful-web-servicerestmd"></a>[L'autenticazione di un servizio Web RESTful](rest.md)

HTTP supporta l'uso di diversi meccanismi di autenticazione per controllare l'accesso alle risorse. L'autenticazione di base fornisce l'accesso alle risorse solo ai client che hanno le credenziali corrette. Questo articolo illustra come usare l'autenticazione di base per proteggere l'accesso alle risorse del servizio web RESTful.

## <a name="authenticating-users-with-an-identity-provideroauthmd"></a>[Autenticazione degli utenti con un Provider di identità](oauth.md)

AUTH è una versione di SDK multipiattaforma per l'autenticazione degli utenti e i propri account di archiviazione. Include gli autenticatori di OAuth che forniscono il supporto per l'utilizzo del provider di identità, ad esempio Google, Microsoft, Facebook e Twitter. Questo articolo illustra come usare AUTH per gestire il processo di autenticazione in un'applicazione xamarin. Forms.

## <a name="authenticating-users-with-azure-mobile-appsazuremd"></a>[Autenticazione degli utenti con App per dispositivi mobili di Azure](azure.md)

Le App per dispositivi mobili di Azure usare un'ampia gamma di provider di identità esterni per supportare l'autenticazione e autorizzazione degli utenti dell'applicazione. Quindi, è possibile impostare autorizzazioni sulle tabelle per limitare l'accesso solo agli utenti autenticati. Questo articolo illustra come usare App per dispositivi mobili di Azure per gestire il processo di autenticazione in un'applicazione xamarin. Forms.

## <a name="authenticating-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[Autenticazione degli utenti con Azure Active Directory B2C](azure-ad-b2c.md)

Azure Active B2C di Directory è una soluzione di gestione identità cloud per applicazioni rivolte agli utenti web e mobili. Questo articolo illustra come usare Microsoft Authentication Library (MSAL) e Azure Active Directory B2C per l'integrazione di gestione delle identità degli utenti in un'applicazione xamarin. Forms.

## <a name="integrating-azure-active-directory-b2c-with-azure-mobile-appsazure-ad-b2c-mobile-appmd"></a>[Integrazione di Azure Active Directory B2C con App per dispositivi mobili di Azure](azure-ad-b2c-mobile-app.md)

Azure Active B2C di Directory è utilizzabile per gestire il flusso di lavoro di autenticazione per App per dispositivi mobili di Azure. Con questo approccio, l'esperienza di gestione di identità sia definita completamente nel cloud e può essere modificato senza modificare il codice dell'applicazione per dispositivi mobili. Questo articolo illustra come usare Azure Active Directory B2C per fornire l'autenticazione e autorizzazione a un'istanza di App per dispositivi mobili di Azure con xamarin. Forms.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione ai servizi Web](~/cross-platform/data-cloud/web-services/index.md)
- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
