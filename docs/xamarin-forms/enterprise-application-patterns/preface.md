---
title: ''
description: Questo capitolo offre una prefazione ai modelli di applicazioni aziendali che usano Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b587bd8cacda9024103757a585c11fba2ed09fda
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130832"
---
# <a name="preface-to-enterprise-app-development"></a>Introduzione allo sviluppo di app aziendali

Questo eBook fornisce informazioni aggiuntive sulla creazione di app aziendali multipiattaforma con Xamarin.Forms . Xamarin.Formsè un toolkit di interfaccia utente multipiattaforma che consente agli sviluppatori di creare facilmente layout di interfaccia utente nativi che possono essere condivisi tra piattaforme, tra cui iOS, Android e la piattaforma UWP (Universal Windows Platform) (UWP). Offre una soluzione completa per le app business to Employee (B2E), business to business (B2B) e business to consumer (B2C), offrendo la possibilità di condividere il codice tra tutte le piattaforme di destinazione e di ridurre il costo totale di proprietà (TCO).

La guida fornisce indicazioni sull'architettura per lo sviluppo di app aziendali adattabili, gestibili e testabili Xamarin.Forms . Vengono fornite informazioni aggiuntive su come implementare MVVM, inserimento di dipendenze, navigazione, convalida e gestione della configurazione, mantenendo un accoppiamento libero. Inoltre, sono disponibili indicazioni per l'esecuzione dell'autenticazione e dell'autorizzazione con IdentityServer, l'accesso ai dati da microservizi in contenitori e il testing unità.

La guida viene fornita con il codice sorgente per l' [app per dispositivi mobili eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)e il codice sorgente per l' [app di riferimento eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers). L'app per dispositivi mobili eShopOnContainers è un'app aziendale multipiattaforma sviluppata con Xamarin.Forms , che si connette a una serie di microservizi in contenitori noti come app di riferimento eShopOnContainers. Tuttavia, l'app per dispositivi mobili eShopOnContainers può essere configurata in modo da utilizzare i dati di servizi fittizi per coloro che desiderano evitare di distribuire i microservizi in contenitori.

## <a name="whats-left-out-of-this-guides-scope"></a>Elementi lasciati dall'ambito di questa guida

Questa guida è rivolta ai lettori che hanno già familiarità con Xamarin.Forms . Per un'introduzione dettagliata a Xamarin.Forms , vedere la [ Xamarin.Forms documentazione](~/xamarin-forms/index.yml)e [creazione di app per dispositivi Xamarin.Forms mobili con ](https://aka.ms/xamebook).

La guida è complementare ai [microservizi .NET: architettura per le applicazioni .NET in contenitori](https://aka.ms/microservicesebook), che è incentrata sullo sviluppo e la distribuzione di microservizi in contenitori. Altre guide che vale la pena leggere includono l' [architettura e lo sviluppo di applicazioni Web moderne con ASP.NET Core e Microsoft Azure](https://aka.ms/WebAppEbook), il ciclo di vita [delle applicazioni Docker in contenitori con la piattaforma e gli strumenti Microsoft](https://aka.ms/dockerlifecycleebook)e [la piattaforma e gli strumenti Microsoft per lo sviluppo di app mobili](https://aka.ms/MobAppDev/StndPDF).

## <a name="who-should-use-this-guide"></a>Chi deve usare questa guida

I destinatari di questa guida sono principalmente sviluppatori e architetti che vogliono imparare a progettare e implementare app aziendali multipiattaforma usando Xamarin.Forms .

Un pubblico secondario è il responsabile decisionale tecnico che desidera ricevere una panoramica dell'architettura e della tecnologia prima di decidere quale approccio selezionare per lo sviluppo di app aziendali multipiattaforma usando Xamarin.Forms .

## <a name="how-to-use-this-guide"></a>Come usare questa guida

Questa guida è incentrata sulla creazione di app aziendali multipiattaforma con Xamarin.Forms . Di conseguenza, deve essere letto completamente per fornire una base per comprendere tali app e le relative considerazioni tecniche. La guida, insieme all'app di esempio, può anche fungere da punto di partenza o riferimento per la creazione di una nuova app aziendale. Usare l'app di esempio associata come modello per la nuova app o per vedere come organizzare le parti componenti di un'app. Quindi, fare riferimento a questa guida per informazioni sull'architettura.

È possibile trasmettere questa guida ai membri del team per garantire una conoscenza comune dello sviluppo di app aziendali multipiattaforma usando Xamarin.Forms . L'uso di un set comune di termini e dei principi sottostanti consentirà di garantire un'applicazione coerente di modelli e procedure di architettura.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (esempio)](https://github.com/dotnet-architecture/eShopOnContainers)
