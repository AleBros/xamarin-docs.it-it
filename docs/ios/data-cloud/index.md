---
title: Dati e servizi Cloud
description: Guide per la stabilizzazione e la distribuzione
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/13/2017
ms.openlocfilehash: 81aebe5fd7431e578b75c5b61e1d2c92ce546909
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="data-and-cloud-services"></a>Dati e servizi Cloud


##  <a name="data-accessiosdata-clouddataindexmd"></a>[Accesso ai dati](~/ios/data-cloud/data/index.md)

La maggior parte delle applicazioni presentano un requisito per salvare i dati nel dispositivo locale. A meno che la quantità di dati sia in modo semplice di piccole dimensioni, è in genere necessario un database e un livello di dati nell'applicazione per gestire l'accesso al database. iOS è il motore di database Sqlite "incorporato" e accedere ai dati viene semplificata dalla piattaforma di Xamarin che viene fornito con il Provider di dati di SQLite.

##  <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

L'API di archiviazione iCloud in iOS 5 consente di salvare i documenti e i dati specifici dell'applicazione in una posizione centrale e accedere a tali elementi da tutti i dispositivi dell'utente.

##  <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

Il framework CloudKit semplifica lo sviluppo di applicazioni iCloud tale accesso. Sono inclusi il recupero di dati dell'applicazione e i diritti di asset, nonché la possibilità di archiviare in modo sicuro le informazioni dell'applicazione. Questo kit offre agli utenti un livello di anonimato consentendo l'accesso alle applicazioni con i relativi ID iCloud senza condividere le informazioni personali.