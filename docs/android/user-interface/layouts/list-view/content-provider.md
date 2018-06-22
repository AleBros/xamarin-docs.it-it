---
title: Utilizzando un provider di contenuti
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: b9b6340d4aaf386c7b4be8ebf366589582771be2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763293"
---
# <a name="using-a-contentprovider"></a>Utilizzando un provider di contenuti

CursorAdapters consente inoltre di visualizzare i dati da un provider di contenuti.
ContentProviders consentono di accedere ai dati esposti da altre applicazioni (inclusi i dati di sistema Android come contatti, informazioni di supporto e al calendario).

Il modo migliore per accedere a un provider di contenuti è con un CursorLoader utilizzando il LoaderManager. LoaderManager è stata introdotta in Android 3.0 (API livello 11, Nido d'API) per spostare le attività di blocco esterno del thread principale e l'utilizzo di un CursorLoader consente i caricamento dei dati in un thread prima di essere associato a un controllo ListView per la visualizzazione.

Fare riferimento a [Introduzione a ContentProviders](~/android/platform/content-providers/index.md) per ulteriori informazioni.

