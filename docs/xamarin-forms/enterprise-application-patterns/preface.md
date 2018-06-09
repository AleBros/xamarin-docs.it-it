---
title: Prefisso per lo sviluppo di App aziendali
description: Questo capitolo vengono fornite un prefisso per i modelli di applicazione Enterprise con xamarin. Forms.
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: fd085d2fb12e82233f6d3be2e2773a84539f837b
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242368"
---
# <a name="preface-to-enterprise-app-development"></a>Prefisso per lo sviluppo di App aziendali

Questo eBook fornisce informazioni aggiuntive sulla creazione di applicazioni enterprise multipiattaforma con xamarin. Forms. Xamarin. Forms è un toolkit dell'interfaccia utente multipiattaforma che consente agli sviluppatori di creare facilmente utente nativi layout dell'interfaccia che può essere condivisa tra le piattaforme, tra cui iOS, Android e la piattaforma UWP (Universal Windows). Fornisce una soluzione completa per Business dipendente (B2E), Business to Business (B2B) e di Business per le app Consumer (B2C), fornendo la possibilità di condividere il codice per tutte le piattaforme di destinazione e contribuendo a ridurre il costo totale di proprietà (TCO).

La Guida fornisce indicazioni relative all'architettura per lo sviluppo di applicazioni aziendali di xamarin. Forms flessibili, gestibili e testabili. Vengono fornite istruzioni su come implementare MVVM, inserimento di dipendenze, navigazione, convalida e la gestione della configurazione, mantenendo regime di controllo. Inoltre, è inoltre disponibile materiale sussidiario su come eseguire l'autenticazione e autorizzazione con IdentityServer, accesso ai dati da microservizi nei contenitori e gli unit test.

La Guida viene fornito con il codice sorgente per il [app per dispositivi mobili eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)e codice sorgente per il [eShopOnContainers riferimento app](https://github.com/dotnet-architecture/eShopOnContainers). L'app mobile eShopOnContainers è un'app multipiattaforma con enterprise sviluppata usando xamarin. Forms, che si connette a una serie di microservizi nei contenitori noti come il eShopOnContainers fa riferimento l'app. Tuttavia, l'app mobile eShopOnContainers può essere configurata per utilizzare dati da servizi fittizi per chi desidera evitare la distribuzione di microservizi nei contenitori.

## <a name="whats-left-out-of-this-guides-scope"></a>Ciò che viene lasciato dall'ambito della presente Guida

Questa guida è rivolta lettori che hanno già familiarità con xamarin. Forms. Per informazioni dettagliate a xamarin. Forms, vedere il [documentazione di xamarin. Forms](~/xamarin-forms/index.yml), e [la creazione di App per dispositivi mobili con xamarin. Forms](https://aka.ms/xamebook).

La Guida è complementare ad [Microservizi .NET: architettura per le applicazioni .NET contenitore](https://aka.ms/microservicesebook), che illustra lo sviluppo e distribuzione di microservizi nei contenitori. Includono altre guide vale la pena lettura [sviluppo e lo sviluppo di moderne applicazioni Web con ASP.NET Core e Microsoft Azure](http://aka.ms/WebAppEbook), [contenitore Docker ciclo di vita applicazione con Microsoft Platform e strumenti](http://aka.ms/dockerlifecycleebook), e [piattaforma Microsoft e strumenti per lo sviluppo di App per dispositivi mobili](http://aka.ms/MobAppDev/StndPDF).

## <a name="who-should-use-this-guide"></a>Chi dovrebbe utilizzare questa Guida

I destinatari di questa guida sono principalmente gli sviluppatori e progettisti che desiderano informazioni su come progettare e implementare App enterprise multipiattaforma con xamarin. Forms.

Un gruppo di destinatari secondario è responsabili tecnici che vorresti ricevere una panoramica dell'architettura e tecnologia prima di decidere l'approccio da selezionare per lo sviluppo di app aziendali multipiattaforma con xamarin. Forms.

## <a name="how-to-use-this-guide"></a>Come usare questa Guida

Questa guida è incentrata sulla creazione di applicazioni enterprise multipiattaforma con xamarin. Forms. Di conseguenza, devono essere letti nella sua interezza per fornire una base di informazioni su tali applicazioni e le considerazioni tecniche. La Guida, insieme a un'applicazione di esempio, può anche fungere da un punto iniziale o un riferimento per la creazione di nuove applicazioni enterprise. Usare l'app di esempio associato come modello per la nuova app o per informazioni su come organizzare i componenti di un'app. Quindi, a fare riferimento a questa guida per indicazioni sull'architettura.

È possibile inoltrare questa guida per i membri del team al fine di garantire una comprensione comune di sviluppo di app enterprise multipiattaforma con xamarin. Forms. Tutti gli utenti lavorano su un set comune di terminologia e sottostante principi che consentono di garantire un'applicazione coerenza di modelli di architettura e procedure consigliate.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
