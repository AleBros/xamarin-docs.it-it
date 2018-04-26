---
title: Microsoft Azure
description: Codice di esempio e documentazione di download per Azure.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/09/2017
ms.openlocfilehash: ab449a58cc87699b97a1ade7721a08f771c4f55d
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2018
---
# <a name="microsoft-azure"></a>Microsoft Azure

_Codice di esempio e documentazione di download per Azure._

[ ![](images/evolve-mikej-azure-sml.png "Le funzionalità di servizi App Azure sono facili da aggiungere per le app Xamarin, inclusa l'archiviazione di dati cloud e le notifiche push multipiattaforma")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evoluzione 2016: Sviluppo di App connesse tramite Azure e Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Connessi in Visual Studio per Mac

Il nuovo [servizi connessi](connected-services.md) funzionalità di Visual Studio per Mac consente agli sviluppatori di aggiungere rapidamente e facilmente funzionalità di Azure per applicazioni per dispositivi mobili all'interno dell'IDE. Attualmente è disponibile per il test del canale alfa.


## <a name="azure-app-services"></a>Servizi App di Azure

È una raccolta di [documentazione di App mobili di Azure](~/cross-platform/data-cloud/mobile-apps.md) che in modo semplificato il processo di implementazione di [Client mobili di Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Xamarin offre inoltre un NuGet di messaggistica di Azure pacchetti per [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) e [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) per facilitare l'implementazione di notifiche push su piattaforme diverse.

Configurare le app nel [portale dei servizi di App di Azure](https://portal.azure.com/) per accedere alle App per dispositivi mobili, API Web, archiviazione e molto altro ancora. Informazioni su [come servizi app sono diversi](http://azure.microsoft.com/updates/whats-new-with-azure-app-service/) e guardare [questi video Microsoft](http://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Autenticazione di Active Directory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) può essere usato per gli utenti di account di accesso in App Xamarin mediante i [Xamarin.Auth componente](https://www.nuget.org/packages/Xamarin.Auth/).
Le applicazioni possono quindi accedere a servizi aggiuntivi, come Office 365.

## <a name="webapi"></a>WebAPI

API Web di Microsoft espone un'interfaccia REST simili che può essere facilmente utilizzata dalle applicazioni di Xamarin.
Può facilmente spin-up un [sito Web di Azure](https://trywebsites.azurewebsites.net/) e compilare un'app basata su WebAPI per la connessione per le app Xamarin.


###  <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Introduzione a servizi Web](~/cross-platform/data-cloud/web-services/index.md)

Questa esercitazione viene illustrato come integrare REST, SOAP e WCF web tecnologie del servizio con Xamarin applicazioni per dispositivi mobili. Esamina le diverse implementazioni del servizio, valuta disponibili strumenti e librerie per integrarli e fornisce modelli di esempio per l'utilizzo di dati del servizio. Infine, fornisce una panoramica generale di creazione di un servizio web REST per l'utilizzo con un'applicazione per dispositivi mobili con Xamarin.

## <a name="samples"></a>Esempi

Oltre al [esempi della documentazione](https://github.com/xamarin/mobile-samples/tree/master/Azure), le applicazioni complete seguenti illustrano diverse caratteristiche di Azure incorporate in App Xamarin:

- [Sport](https://github.com/xamarin/Sport) – sportiva descrittivo rilevamento app che usa le notifiche push & di archiviazione di dati.
- [Minuti](https://github.com/pierceboggan/Moments) – foto immediata di condivisione che utilizza l'archiviazione di Azure per le immagini.
- [Xamarin CRM](https://github.com/xamarin/app-crm) : utilizza l'API Web per il server back-end.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -App per dispositivi mobili di Azure.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) : per il [serie architettura](https://www.microsoft.com/net/learn/architecture) di eBook.
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) – Azure + IoT di esempio da compilazione 2016.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di libreria di classi Portabile Azure (da @paulbatum) (esempio)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Portale di Azure](http://azure.microsoft.com/)
- [Client per dispositivi mobili di Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
