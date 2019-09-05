---
title: Microsoft Azure e Novell
description: Questo documento contiene collegamenti alla documentazione relativa a Servizi connessi in Visual Studio per Mac, app per dispositivi mobili di Azure, autenticazione Active Directory e WebAPI.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: conceptdev
ms.author: crdun
ms.date: 10/09/2017
ms.openlocfilehash: 0979a0b65cc3d5b4944dadaf67aaa14cf1b3cf73
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287594"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure e Novell

[![](images/evolve-mikej-azure-sml.png "Le funzionalità di app Azure Services sono facili da aggiungere alle app Novell, tra cui l'archiviazione dei dati cloud e le notifiche push multipiattaforma")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evoluzione 2016: Sviluppo di app connesse tramite Azure e Novell](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Servizi connessi in Visual Studio per Mac

La nuova funzionalità [servizi connessi](connected-services.md) di Visual Studio per Mac consente agli sviluppatori di aggiungere in modo rapido e semplice le funzionalità di Azure alle applicazioni per dispositivi mobili dall'IDE. Attualmente disponibile per i test nel canale alfa.

## <a name="azure-app-services"></a>Servizi di app Azure

È disponibile una raccolta di [documentazione di app per dispositivi mobili di Azure](~/cross-platform/data-cloud/mobile-apps.md) che guida l'utente nel processo di implementazione del [client mobile di Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Novell offre anche pacchetti NuGet di messaggistica di Azure per [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) e [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) che consentono di implementare notifiche push tra piattaforme diverse.

È possibile configurare le app nel [portale dei servizi app Azure](https://portal.azure.com/) per accedere ad app per dispositivi mobili, API Web, archiviazione e molto altro ancora. Scopri in [che modo i servizi app sono diversi](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) e guarda [questi video da Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Autenticazione Active Directory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) può essere usato per accedere agli utenti nelle app Novell tramite il [componente Novell. auth](https://www.nuget.org/packages/Xamarin.Auth/).
Le app possono quindi accedere a servizi aggiuntivi come Office 365.

## <a name="webapi"></a>WebAPI

L'API Web di Microsoft espone un'interfaccia simile a REST che può essere facilmente utilizzata dalle applicazioni Novell.
Puoi creare facilmente un [sito Web di Azure](https://trywebsites.azurewebsites.net/) e creare un'app basata su WebAPI per la connessione alle app Novell.


### <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Introduzione ai servizi Web](~/cross-platform/data-cloud/web-services/index.md)

Questa esercitazione illustra come integrare le tecnologie REST, WCF e servizi Web SOAP con le applicazioni per dispositivi mobili Novell. Esamina diverse implementazioni del servizio, valuta gli strumenti e le librerie disponibili per integrarli e fornisce modelli di esempio per l'utilizzo dei dati del servizio. Infine, viene fornita una panoramica di base della creazione di un servizio Web RESTful per l'utilizzo con un'applicazione per dispositivi mobili Novell.

## <a name="samples"></a>Esempi

Oltre agli esempi di [documentazione](https://github.com/xamarin/mobile-samples/tree/master/Azure), le applicazioni complete seguenti illustrano le diverse funzionalità di Azure incorporate nelle app Novell:

- App [sport](https://github.com/xamarin/Sport) -friendly Tracking Sports-League che usa l'archiviazione dei dati & notifiche push.
- [Moments](https://github.com/pierceboggan/Moments) : condivisione foto immediata che usa archiviazione di Azure per le immagini.
- [Novell CRM](https://github.com/xamarin/app-crm) : usa l'API Web per il back-end.
- [Webshoppe](https://github.com/jamesmontemagno/MyShoppe) -app per dispositivi mobili di Azure.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) : esempio per la [serie di architetture](https://www.microsoft.com/net/learn/architecture) di eBook.
- [Dedriving](https://azure.microsoft.com/campaigns/mydriving/) : esempio di Azure + sacco dalla Build 2016.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Azure PCL ( @paulbatumper) (esempio)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Portale di Azure](https://azure.microsoft.com/)
- [Client mobile per Novell (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
