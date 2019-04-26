---
title: Prefisso per lo sviluppo di App aziendali
description: In questo capitolo vengono un prefisso ai modelli di applicazione Enterprise con xamarin. Forms.
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: fd085d2fb12e82233f6d3be2e2773a84539f837b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61298870"
---
# <a name="preface-to-enterprise-app-development"></a>Prefisso per lo sviluppo di App aziendali

Questo eBook fornisce materiale sussidiario sulla creazione di App aziendali multipiattaforma con xamarin. Forms. Xamarin. Forms è un toolkit per interfacce utente multipiattaforma che consente agli sviluppatori di creare facilmente utente native layout di interfaccia che possono essere condivisi tra piattaforme, tra cui iOS, Android e Universal Windows Platform (UWP). Fornisce una soluzione completa per l'azienda-dipendente (B2E), Business to Business (B2B) e aziendali per le app Consumer (B2C), offre la possibilità di condividere codice tra tutte le piattaforme di destinazione e aiuta a ridurre il costo totale di proprietà (TCO).

La Guida fornisce indicazioni sull'architettura per lo sviluppo di App aziendali di xamarin. Forms flessibili, gestibili e testabili. Vengono fornite istruzioni su come implementare MVVM, inserimento di dipendenze, navigazione, convalida e gestione della configurazione, mantenendo il regime di controllo libero. Inoltre, è inoltre disponibile materiale sussidiario su come eseguire l'autenticazione e autorizzazione con IdentityServer, accedono ai dati dai microservizi in contenitori e gli unit test.

La Guida è disponibile il codice di origine per il [app per dispositivi mobili di eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)e codice sorgente per il [app di riferimento eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers). L'app per dispositivi mobili di eShopOnContainers è un'app multipiattaforma con enterprise sviluppata con xamarin. Forms, che si connette a una serie di microservizi in contenitori noti come app di riferimento eShopOnContainers. Tuttavia, l'app per dispositivi mobili di eShopOnContainers può essere configurato per utilizzare dati da servizi fittizi per coloro che desiderano evitare di distribuire i microservizi in contenitori.

## <a name="whats-left-out-of-this-guides-scope"></a>Ciò che resta fuori ambito della Guida

Questa guida è rivolta a lettori che hanno già familiari con xamarin. Forms. Per un'introduzione dettagliata a xamarin. Forms, vedere la [documentazione di xamarin. Forms](~/xamarin-forms/index.yml), e [creazione di App per dispositivi mobili con xamarin. Forms](https://aka.ms/xamebook).

Questa guida è complementare ad [Microservizi .NET: Architettura per applicazioni .NET in contenitori](https://aka.ms/microservicesebook), che è incentrato sullo sviluppo e distribuzione di microservizi in contenitori. Includono altre guide la pena di leggere [Architecting e lo sviluppo di applicazioni Web moderne con ASP.NET Core e Microsoft Azure](http://aka.ms/WebAppEbook), [in contenitori Docker Application Lifecycle con Microsoft Platform e degli strumenti](http://aka.ms/dockerlifecycleebook), e [piattaforma e strumenti per lo sviluppo di App per dispositivi mobili Microsoft](http://aka.ms/MobAppDev/StndPDF).

## <a name="who-should-use-this-guide"></a>Chi dovrebbe utilizzare questa Guida

I destinatari di questa guida sono prevalentemente sviluppatori e architetti che desiderano imparare a progettare e implementare le app aziendali multipiattaforma usando xamarin. Forms.

Un gruppo di destinatari secondario è responsabili delle decisioni tecniche che vogliono ricevere una panoramica dell'architettura e tecnologia prima di decidere l'approccio da selezionare per lo sviluppo di app aziendali multipiattaforma con xamarin. Forms.

## <a name="how-to-use-this-guide"></a>Come usare questa Guida

Questa guida è incentrata sulla creazione di App aziendali multipiattaforma con xamarin. Forms. Di conseguenza, devono essere letti nella loro interezza per fornire una base di informazioni su tali App e delle considerazioni tecniche. La Guida e le app di esempio, può anche essere usato come un punto di partenza o riferimento per la creazione di una nuova app aziendale. Usare l'app di esempio associata come modello per la nuova app o per informazioni su come organizzare i componenti di un'app. Quindi, fare riferimento a questa Guida, per indicazioni sull'architettura.

È possibile inoltrare questa guida per i membri del team al fine di garantire una comprensione comune di sviluppo di app aziendali multipiattaforma con xamarin. Forms. Facendo sì lavori da un set comune di termini e di principi sottostanti che contribuirà a garantire un'applicazione coerente di modelli e procedure architetturali.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
