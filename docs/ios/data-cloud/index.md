---
title: I dati e servizi Cloud in App xamarin. IOS
description: Questo documento include collegamenti alle guide che illustrano come lavorare con dati locali, iCloud e CloudKit in un'app xamarin. IOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 21a6c1c0c0ceb5596a056f0818dec39041808504
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117147"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>I dati e servizi Cloud in App xamarin. IOS

##  <a name="data-accessiosdata-clouddataindexmd"></a>[Accesso ai dati](~/ios/data-cloud/data/index.md)

La maggior parte delle applicazioni hanno alcuni requisiti per salvare i dati nel dispositivo in locale. A meno che la quantità di dati non sarebbero incredibilmente piccola, ciò richiede in genere un database e un livello di dati nell'applicazione per gestire l'accesso al database. iOS è il motore di database Sqlite "incorporato" e accedere ai dati è semplificato della piattaforma Xamarin, fornito con il Provider di dati di SQLite.

##  <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

L'API di archiviazione iCloud in iOS 5 consente di salvare i documenti dell'utente e i dati specifici dell'applicazione in una posizione centrale e accedere a tali elementi da tutti i dispositivi dell'utente.

##  <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

Il framework CloudKit semplifica lo sviluppo di applicazioni iCloud tale accesso. Sono inclusi il recupero dei dati dell'applicazione e i diritti di asset, nonché la possibilità di archiviare in modo sicuro le informazioni sull'applicazione. Questo kit fornisce agli utenti un livello di anonimato consentendo l'accesso alle applicazioni con relativi ID iCloud senza condivisione delle informazioni personali.