---
title: Registrar di xamarin. Mac
description: Questo documento descrive lo scopo del registrar di xamarin. Mac e dinamico, statico e parziale statica (ibrido) le configurazioni di utilizzo.
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 21e1a2c6ae5a9ad8b6acf520851ea92a340da887
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119409"
---
# <a name="xamarinmac-registrar"></a>Registrar di xamarin. Mac

_Questo documento descrive lo scopo del programma di registrazione di xamarin. Mac e le relative configurazioni di utilizzo diversi._

## <a name="overview"></a>Panoramica

Xamarin. Mac colma il divario tra il mondo gestito (.NET) e runtime di Cocoa, consentendo le classi gestite chiamare le classi di Objective-C non gestite ed essere richiamato quando si verificano eventi. Il lavoro necessario per eseguire questo comando "magic" è gestito dal registrar ed è, in generale, nascoste dalla visualizzazione.

Esistono implicazioni sulle prestazioni di questa registrazione, in particolare all'avvio dell'applicazione i tempi di attività e può talvolta essere utile comprendere un po' di ciò che accade "dietro le quinte".

## <a name="configurations"></a>Configurazioni

Fondamentalmente processo le registrar all'avvio può essere suddivisa in due categorie:

- L'analisi di tutte le classi gestite per coloro che deriva da NSObject e raccogliere un elenco di elementi possono essere esposti al runtime di Objective-C.
- Registrare queste informazioni con il runtime di Objective-C.

Nel corso del tempo, sono state create tre configurazioni di altro registrar per coprire diversi casi d'uso. Ognuno ha diverse build ed esegue conseguenze di tempo:

- **Programma di registrazione dinamica** : durante l'avvio, usare la reflection .NET per ogni tipo caricato di analisi, determinare l'elenco degli elementi pertinenti e informare il runtime nativo. Questa opzione aggiunge portarli alla compilazione, ma è molto costosa per il calcolo durante l'avvio (fino a più secondi).
- **Programma di registrazione statico** : durante la compilazione, il set di elementi da registrare e generare il codice Objective-C per gestire la registrazione di calcolo. Questo codice viene richiamato durante l'avvio da registrare rapidamente tutti gli elementi. Aggiunge che una pausa significativa a compilazione ma è possibile taglia una quantità significativa di tempo dall'avvio dell'applicazione.
- **Statica "Parziale"** : un approccio di "ibrido" più recente che offre la maggior parte dei vantaggi di entrambi. Poiché delle esportazioni **xamarin** sono costanti, salvare una raccolta di pre-calcolata per gestire il processo di registrazione e collegamento che in. Usare la reflection per librerie utente di gestire, ma come librerie utente esportare tipi molto meno che le associazioni di piattaforma spesso si tratta piuttosto veloce. Un neglectable impatto del tempo di compilazione e riduce la maggior parte di "costo" dinamico.

Oggi statico parziale è il valore predefinito per la configurazione di Debug e statica è il valore predefinito per le configurazioni di rilascio.

Esistono alcuni scenari:

- Plug-in di caricamento dopo l'avvio con le classi che derivano da NSObject
- Creati dinamicamente istanze della classe che deriva da NSObject

in cui il programma di registrazione non riesce a sapere che è necessario registrare un tipo all'inizio. Il `ObjCRuntime.Runtime.RegisterAssembly` metodo ha lo scopo di informare le registrar che dispone di tipi aggiuntivi da considerare.
