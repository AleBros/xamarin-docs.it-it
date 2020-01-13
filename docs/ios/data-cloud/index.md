---
title: Servizi di dati e cloud nelle app Xamarin.iOS
description: Questo documento contiene collegamenti a guide che descrivono come usare i dati locali, iCloud e CloudKit in un'app Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2017
ms.openlocfilehash: e9f910804f3da9d173206d51c59e1d7ddd9b90a6
ms.sourcegitcommit: bad1ab3f78d7f94d48511666626b54f8ba155689
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663512"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>Servizi di dati e cloud nelle app Xamarin.iOS

## <a name="data-accessiosdata-clouddataindexmd"></a>[Accesso ai dati](~/ios/data-cloud/data/index.md)

Per la maggior parte delle applicazioni è necessario salvare i dati sul dispositivo localmente. A meno che la quantità di dati non sia leggermente ridotta, questo in genere richiede un database e un livello dati nell'applicazione per gestire l'accesso al database. iOS dispone del motore di database SQLite "built in" e l'accesso ai dati viene semplificato dalla piattaforma Novell, che viene fornita con la provider di dati SQLite.

## <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

L'API di archiviazione iCloud consente alle applicazioni di salvare i documenti utente e i dati specifici dell'applicazione in una posizione centrale e di accedere a tali elementi da tutti i dispositivi dell'utente.

## <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

Il Framework CloudKit semplifica lo sviluppo di applicazioni che accedono a iCloud. Questo include il recupero dei dati delle applicazioni e dei diritti degli asset, nonché la possibilità di archiviare in modo sicuro le informazioni sull'applicazione. Questo kit offre agli utenti un livello di anonimità consentendo l'accesso alle applicazioni con gli ID iCloud senza condividere le informazioni personali.
