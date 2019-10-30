---
title: Servizi di dati e cloud nelle app Novell. iOS
description: Questo documento contiene collegamenti a guide che descrivono come usare i dati locali, iCloud e CloudKit in un'app Novell. iOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2017
ms.openlocfilehash: 4b3bf1fcc6be7713c87b16ef9ec083189793b324
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008061"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>Servizi di dati e cloud nelle app Novell. iOS

## <a name="data-accessiosdata-clouddataindexmd"></a>[Accesso ai dati](~/ios/data-cloud/data/index.md)

Per la maggior parte delle applicazioni è necessario salvare i dati sul dispositivo localmente. A meno che la quantità di dati non sia leggermente ridotta, questo in genere richiede un database e un livello dati nell'applicazione per gestire l'accesso al database. iOS dispone del motore di database SQLite "built in" e l'accesso ai dati viene semplificato dalla piattaforma Novell, che viene fornita con la provider di dati SQLite.

## <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

L'API di archiviazione iCloud in iOS 5 consente alle applicazioni di salvare i documenti utente e i dati specifici dell'applicazione in una posizione centrale e di accedere a tali elementi da tutti i dispositivi dell'utente.

## <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

Il Framework CloudKit semplifica lo sviluppo di applicazioni che accedono a iCloud. Questo include il recupero dei dati delle applicazioni e dei diritti degli asset, nonché la possibilità di archiviare in modo sicuro le informazioni sull'applicazione. Questo kit offre agli utenti un livello di anonimità consentendo l'accesso alle applicazioni con gli ID iCloud senza condividere le informazioni personali.
