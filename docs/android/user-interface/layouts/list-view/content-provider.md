---
title: Uso di un ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 142541dcc35b55e43b54eeb729c486ac9fc88b54
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510065"
---
# <a name="using-a-contentprovider-with-xamarinandroid"></a>Uso di ContentProvider con Novell. Android

CursorAdapter può essere usato anche per visualizzare i dati da un ContentProvider.
ContentProviders consentono di accedere ai dati esposti da altre applicazioni, inclusi i dati di sistema Android, ad esempio contatti, supporti e informazioni sul calendario.

Il modo migliore per accedere a un ContentProvider è con una CursorLoader che usa LoaderManager. LoaderManager è stato introdotto in Android 3,0 (livello API 11, Honeycomb) per spostare le attività di blocco dal thread principale e l'uso di un CursorLoader consente di caricare i dati in un thread prima di essere associati a un controllo ListView per la visualizzazione.

Per ulteriori informazioni, vedere [Introduzione a ContentProviders](~/android/platform/content-providers/index.md) .

