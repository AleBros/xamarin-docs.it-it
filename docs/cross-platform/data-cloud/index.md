---
title: Microsoft Azure e Xamarin
description: Questo documento contiene collegamenti alla documentazione sui servizi connessi in Visual Studio per Mac, azure Mobile Apps, Active Directory Authentication e WebAPI.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: davidortinau
ms.author: daortin
ms.date: 10/09/2017
ms.openlocfilehash: 2b6dfeb0de0fac59556280609dbf870c23a9298b
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388525"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure e Xamarin

[![](images/evolve-mikej-azure-sml.png "Azure App Services features are easy to add to Xamarin apps, including cloud data storage and cross-platform push notifications")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolve 2016: Sviluppo di app connesse tramite Azure e Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Servizi connessi in Visual Studio per Mac

La nuova funzionalità [Servizi connessi](connected-services.md) di Visual Studio per Mac consente agli sviluppatori di aggiungere rapidamente e facilmente funzionalità di Azure alle applicazioni per dispositivi mobili dall'IDE. Attualmente disponibile per il test nel canale Alpha.

## <a name="azure-app-services"></a>Servizi app di Azure

È disponibile una raccolta di [documentazione](~/cross-platform/data-cloud/mobile-apps.md) di App per dispositivi mobili di Azure che guida l'utente nel processo di implementazione del [client di Azure per dispositivi mobili.](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
Xamarin offre anche pacchetti NuGet di messaggistica di Azure per [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) e [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) per facilitare l'implementazione delle notifiche push tra le piattaforme.

Configurare le app nel portale di [Servizi app](https://portal.azure.com/) di Azure per accedere ad app per dispositivi mobili, API Web, archiviazione e molto altro ancora. Informazioni su [come i servizi app sono diversi](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) e guarda questi video di [Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Active Directory Authentication

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) può essere usato per accedere gli utenti alle app Xamarin.Azure Active Directory can be used to login users to Xamarin apps. Le app possono quindi accedere a servizi aggiuntivi come Office 365.

## <a name="webapi"></a>WebAPI

L'API Web di Microsoft espone un'interfaccia simile a REST che può essere facilmente utilizzata dalle applicazioni Xamarin.
È possibile creare facilmente un sito Web di Azure e creare un'app basata su WebAPI per connettersi alle app Xamarin.You can easily spin-up an [Azure Website](https://trywebsites.azurewebsites.net/) and build a WebAPI-based app to connect to Xamarin apps.

### <a name="introduction-to-web-services"></a>[Introduzione ai servizi Web](~/cross-platform/data-cloud/web-services/index.md)

In questa esercitazione viene illustrato come integrare le tecnologie dei servizi Web REST, WCF e SOAP con le applicazioni per dispositivi mobili Xamarin. Esamina varie implementazioni del servizio, valuta gli strumenti e le librerie disponibili per integrarli e fornisce modelli di esempio per l'utilizzo dei dati del servizio. Infine, fornisce una panoramica di base della creazione di un servizio Web RESTful per l'utilizzo con un'applicazione mobile Xamarin.

## <a name="samples"></a>Esempi

Oltre agli esempi di [documentazione,](https://github.com/xamarin/mobile-samples/tree/master/Azure)le applicazioni complete seguenti illustrano varie funzionalità di Azure incorporate nelle app Xamarin:

- [Sport](https://github.com/xamarin/Sport) – amichevole sport-league tracking app che utilizza l'archiviazione dei dati & notifiche push.
- [Momenti:](https://github.com/pierceboggan/Moments) condivisione di foto istantanea che usa Archiviazione di Azure per le immagini.
- [Xamarin CRM:](https://github.com/xamarin/app-crm) utilizza l'API Web per il back-end.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) - App per dispositivi mobili di Azure.MyShoppe - Azure Mobile Apps.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) – esempio per la serie di ebook [Architecture.](https://www.microsoft.com/net/learn/architecture)
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) – Azure : esempio IoT di Build 2016.MyDriving – Azure - IoT sample from Build 2016.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di LIBRERIA @paulbatumdi classi Porta oggetti (per) (esempio)Azure PCL Example (by) (sample)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure portal](https://azure.microsoft.com/)
- [Client mobile per Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
