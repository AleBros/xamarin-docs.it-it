---
title: Registrar Xamarin.Mac
description: Questo documento descrive lo scopo del registrar Xamarin.Mac e le relative configurazioni di utilizzo dinamiche, statiche e parziali (ibride).
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 11/10/2017
ms.openlocfilehash: 991d9b2d911b5aa4ac07225fd1df34877451df49
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017316"
---
# <a name="xamarinmac-registrar"></a>Registrar Xamarin.Mac

_Questo documento descrive lo scopo del registrar Xamarin.Mac e le diverse configurazioni di utilizzo._

## <a name="overview"></a>Panoramica

Xamarin.Mac colma il gap tra il runtime gestito (.NET) e il runtime di Cocoa, consentendo alle classi gestite di chiamare le classi Objective-C non gestite e di essere richiamate quando si verificano gli eventi. Il lavoro richiesto per la formulazione di questa "magia" viene gestito dal registrar e, in generale, è nascosto dalla visualizzazione.

Le implicazioni in termini di prestazioni di questa registrazione, in particolare il tempo di avvio dell'applicazione, e la comprensione di un po' di ciò che accade "dietro le quinte" a volte possono essere utili.

## <a name="configurations"></a>Configurazioni

Fondamentalmente il processo del registrar all'avvio può essere suddiviso in due categorie:

- Esegue l'analisi di tutte le classi gestite per quelle che derivano da NSObject e raccoglie un elenco di elementi da esporre al runtime di Objective-C.
- Registrare queste informazioni con il runtime di Objective-C.

Nel tempo sono state create tre diverse configurazioni del registrar per coprire diversi casi d'uso. Ognuna presenta diverse conseguenze per la compilazione e il tempo di esecuzione:

- **Registrar dinamico** : durante l'avvio, usare la reflection .NET per analizzare ogni tipo caricato, determinare l'elenco degli elementi pertinenti e informare il runtime nativo. Questa opzione consente di aggiungere zero tempo alla compilazione ma è molto costosa da calcolare durante l'avvio (fino a più secondi).
- **Registrar statico** : durante la compilazione, calcolare il set di elementi da registrare e generare codice Objective-C per gestire la registrazione. Questo codice viene richiamato durante l'avvio per registrare rapidamente tutti gli elementi. Aggiunge una pausa significativa per la compilazione, ma può tagliare una quantità di tempo significativa dall'avvio dell'applicazione.
- **"Parziale" statico** , un approccio "ibrido" più recente che offre la maggior parte dei vantaggi di entrambi. Poiché le esportazioni da **Xamarin.Mac. dll** sono costanti, salvare una libreria pre-calcolata per gestire la registrazione e collegarla in. Utilizzare la reflection per gestire le librerie utente, ma poiché le librerie utente esportano molto meno tipi che le associazioni della piattaforma spesso sono piuttosto rapide. Un tempo di compilazione trascurabile influisce negativamente sulla maggior parte dei "costi" della dinamica.

Attualmente, static parziale è l'impostazione predefinita per la configurazione di debug e static è l'impostazione predefinita per le configurazioni di rilascio.

Esistono alcuni scenari:

- Plug-in caricati dopo l'avvio con le classi derivanti da NSObject
- Istanze della classe create dinamicamente che derivano da NSObject

dove il registrar non è in grado di stabilire che è necessario registrare un tipo all'avvio. Viene fornito il metodo `ObjCRuntime.Runtime.RegisterAssembly` per informare il registrar che dispone di tipi aggiuntivi da considerare.
