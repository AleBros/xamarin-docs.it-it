---
title: I dati e servizi Cloud in App xamarin. IOS
description: Questo documento include collegamenti alle guide che illustrano come lavorare con dati locali, iCloud e CloudKit in un'app xamarin. IOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 930ff3df8266053ab5de09c01d82d8d583ffac81
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865560"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>I dati e servizi Cloud in App xamarin. IOS

## <a name="data-accessiosdata-clouddataindexmd"></a>[Accesso ai dati](~/ios/data-cloud/data/index.md)

La maggior parte delle applicazioni hanno alcuni requisiti per salvare i dati nel dispositivo in locale. A meno che la quantità di dati non sarebbero incredibilmente piccola, ciò richiede in genere un database e un livello di dati nell'applicazione per gestire l'accesso al database. iOS è il motore di database Sqlite "incorporato" e accedere ai dati è semplificato della piattaforma Xamarin, fornito con il Provider di dati di SQLite.

## <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

L'API di archiviazione iCloud in iOS 5 consente di salvare i documenti dell'utente e i dati specifici dell'applicazione in una posizione centrale e accedere a tali elementi da tutti i dispositivi dell'utente.

## <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

Il framework CloudKit semplifica lo sviluppo di applicazioni iCloud tale accesso. Sono inclusi il recupero dei dati dell'applicazione e i diritti di asset, nonché la possibilità di archiviare in modo sicuro le informazioni sull'applicazione. Questo kit fornisce agli utenti un livello di anonimato consentendo l'accesso alle applicazioni con relativi ID iCloud senza condivisione delle informazioni personali.
