---
title: Microsoft Azure e Xamarin
description: Questo documento include documentazione sui servizi connessi di Visual Studio per Mac, le App per dispositivi mobili di Azure, l'autenticazione di Active Directory e API Web.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: asb3993
ms.author: amburns
ms.date: 10/09/2017
ms.openlocfilehash: dd211fecad0bff58cb9ff6c6a99ae6a15c60eb7b
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674980"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure e Xamarin

[![](images/evolve-mikej-azure-sml.png "Funzionalità di servizi App di Azure sono facili da aggiungere all'app di Xamarin, tra cui archivio dati cloud e le notifiche push multipiattaforma")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evoluzione 2016: Sviluppo di App connesse tramite Azure e Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Servizi connessi in Visual Studio per Mac

Il nuovo [servizi connessi](connected-services.md) funzionalità di Visual Studio per Mac consente agli sviluppatori di aggiungere rapidamente e facilmente funzionalità di Azure per le applicazioni per dispositivi mobili da all'interno dell'IDE. Attualmente disponibile per il test del canale alfa.

## <a name="azure-app-services"></a>Servizi App di Azure

È una raccolta di [documentazione di Azure Mobile Apps](~/cross-platform/data-cloud/mobile-apps.md) che descrive il processo di implementazione di [Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Xamarin offre inoltre un NuGet per la messaggistica di Azure pacchetti relativi [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) e [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) per facilitare l'implementazione di notifiche push tra le piattaforme.

Configurare le app nel [portale di Azure App Services](https://portal.azure.com/) per accedere alle App per dispositivi mobili, API Web, archiviazione e altro ancora. Scopri [quali servizi app sono le differenze](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) guardare [questi video di Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Autenticazione di Active Directory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) può essere utilizzato per l'accesso degli utenti nelle app di Xamarin tramite i [componente AUTH](https://www.nuget.org/packages/Xamarin.Auth/).
Le app possono quindi accedere a servizi aggiuntivi, ad esempio Office 365.

## <a name="webapi"></a>WebAPI

API Web di Microsoft espone un'interfaccia simile a REST che può essere facilmente utilizzata da applicazioni Xamarin.
È possibile facilmente spin-up un' [sito Web di Azure](https://trywebsites.azurewebsites.net/) e creare un'app basata su WebAPI per connettersi all'App Xamarin.


###  <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Introduzione a servizi Web](~/cross-platform/data-cloud/web-services/index.md)

Questa esercitazione descrive come integrare REST, SOAP e WCF web tecnologie del servizio con le applicazioni per dispositivi mobili Xamarin. Esamina le diverse implementazioni del servizio, valuta gli strumenti disponibili e le librerie di integrazione e fornisce modelli di esempio per l'utilizzo del servizio dati. Infine, fornisce una panoramica di base della creazione di un servizio web RESTful per l'utilizzo con un'applicazione per dispositivi mobili Xamarin.

## <a name="samples"></a>Esempi

Oltre al [esempi di documentazione](https://github.com/xamarin/mobile-samples/tree/master/Azure), le applicazioni complete seguenti illustrano varie funzionalità di Azure incorporata nelle app di Xamarin:

- [Sport](https://github.com/xamarin/Sport) – descrittivo Lega sportiva-rilevamento delle app che usa le notifiche push e archiviazione dei dati.
- [Istanti](https://github.com/pierceboggan/Moments) – foto immediata di condivisione che Usa archiviazione di Azure per le immagini.
- [Xamarin CRM](https://github.com/xamarin/app-crm) : Usa l'API Web per il back-end.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -App per dispositivi mobili di Azure.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) : esempio per il [serie architettura](https://www.microsoft.com/net/learn/architecture) di eBook.
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) – Azure e IoT di esempio di Build 2016.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di libreria di classi Portabile di Azure (da @paulbatum) (esempio)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Portale di Azure](https://azure.microsoft.com/)
- [Client per dispositivi mobili per Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
