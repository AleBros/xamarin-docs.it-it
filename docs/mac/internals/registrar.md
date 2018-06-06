---
title: Xamarin.Mac registrar
description: Questo documento descrive lo scopo del registrar Xamarin.Mac e relativo dinamico, statico e parziale statico (ibrido) le configurazioni di utilizzo.
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: b6e971e608c8b9228523222cebc4d6dac9395def
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792420"
---
# <a name="xamarinmac-registrar"></a>Xamarin.Mac registrar

_Questo documento descrive lo scopo del registrar di Xamarin.Mac e le relative configurazioni diverse di utilizzo._

## <a name="overview"></a>Panoramica

Xamarin.Mac colma il divario tra ambiente gestito (.NET) e di runtime del Cocoa, consentendo di classi gestite chiamare classi Objective-C non gestite ed essere richiamato quando si verificano eventi. Il lavoro necessario per eseguire questa "chiave" viene gestito dal programma di registrazione e in generale, è, nascoste dalla visualizzazione.

Esistono implicazioni sulle prestazioni di questa registrazione, in particolare all'avvio dell'applicazione del computer, e può talvolta essere utile comprendere un po' di ciò che accade "dietro le quinte".

## <a name="configurations"></a>Configurazioni

Fondamentalmente, il processo del programma di registrazione all'avvio può essere suddivisa in due categorie:

- Analisi di ogni classe gestita per quelle che derivano da NSObject e raccogliere un elenco di elementi per essere esposti al runtime Objective-C.
- Registrare le informazioni con il runtime Objective-C.

Nel corso del tempo, le tre configurazioni di altro registrar sono state create per coprire diversi casi d'uso. Ognuno presenta compilazione differente e conseguenze tempo di esecuzione:

- **Programma di registrazione dinamica** : durante l'avvio, usare la reflection .NET per ogni tipo caricato di analisi, determinare l'elenco degli elementi pertinenti e informare il runtime nativo. Questa opzione aggiunge portarli alla compilazione, ma è molto costosa di calcolo durante l'avvio (fino a più secondi).
- **Programma di registrazione statico** : durante la compilazione, il set di elementi da registrare e generare il codice Objective-C per gestire la registrazione di calcolo. Questo codice viene richiamato durante l'avvio per registrare rapidamente tutti gli elementi. Aggiunge che una pausa significativa di compilazione, ma può essere tagliati una quantità significativa di tempo dall'avvio dell'applicazione.
- **Statico "Parziale"** : un approccio "ibrido" più recente che rende disponibile la maggior parte dei vantaggi di entrambi. Poiché le esportazioni dalla **Xamarin.Mac.dll** costanti, salvare una raccolta di pre-calcolata per gestire la registrazione e che nel collegamento. Usare la reflection per gestire le librerie utente, ma come librerie utente Esporta tipi molto meno che le associazioni di piattaforma si tratta spesso piuttosto veloce. Un neglectable impatto del tempo di compilazione e riduce la maggior parte del "costo" dinamico.

Oggi statico parziale è il valore predefinito per la configurazione di Debug e statica è il valore predefinito per le configurazioni di rilascio.

Esistono alcuni scenari:

- I plug-in caricato dopo l'avvio con le classi che derivano da NSObject
- Creati dinamicamente istanze di classi che derivano da NSObject

Se il programma di registrazione è in grado di sapere che è necessario registrare un tipo all'inizio. Il `ObjCRuntime.Runtime.RegisterAssembly` metodo è fornito per il programma di registrazione che indicano che contiene i tipi aggiuntivi da considerare.
