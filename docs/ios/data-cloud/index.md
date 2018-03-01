---
title: Dati e servizi Cloud
description: Guide per la stabilizzazione e la distribuzione
ms.topic: article
ms.prod: xamarin
ms.assetid: 92B35AB1-7AB7-3D3B-DB31-CC971E0B43AE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/13/2017
ms.openlocfilehash: 5814936289164f14a07b33c219ad1fd01b00473b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="data-and-cloud-services"></a>Dati e servizi Cloud


##  <a name="data-accessiosdata-clouddataindexmd"></a>[Accesso ai dati](~/ios/data-cloud/data/index.md)

La maggior parte delle applicazioni presentano un requisito per salvare i dati nel dispositivo locale. A meno che la quantità di dati sia in modo semplice di piccole dimensioni, è in genere necessario un database e un livello di dati nell'applicazione per gestire l'accesso al database. iOS è il motore di database Sqlite "incorporato" e accedere ai dati viene semplificata dalla piattaforma di Xamarin che viene fornito con il Provider di dati di SQLite.

##  <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

L'API di archiviazione iCloud in iOS 5 consente di salvare i documenti e i dati specifici dell'applicazione in una posizione centrale e accedere a tali elementi da tutti i dispositivi dell'utente.

##  <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

Il framework CloudKit semplifica lo sviluppo di applicazioni iCloud tale accesso. Sono inclusi il recupero di dati dell'applicazione e i diritti di asset, nonché la possibilità di archiviare in modo sicuro le informazioni dell'applicazione. Questo kit offre agli utenti un livello di anonimato consentendo l'accesso alle applicazioni con i relativi ID iCloud senza condividere le informazioni personali.