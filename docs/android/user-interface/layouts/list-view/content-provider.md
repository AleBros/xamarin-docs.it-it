---
title: Uso di un ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: c99b3d64f4f5033240c086af8677d31485a44f01
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028928"
---
# <a name="using-a-contentprovider-with-xamarinandroid"></a>Uso di ContentProvider con Xamarin.Android

CursorAdapter può essere usato anche per visualizzare i dati da un ContentProvider.
ContentProviders consentono di accedere ai dati esposti da altre applicazioni, inclusi i dati di sistema Android, ad esempio contatti, supporti e informazioni sul calendario.

Il modo migliore per accedere a un ContentProvider è con una CursorLoader che usa LoaderManager. LoaderManager è stato introdotto in Android 3,0 (livello API 11, Honeycomb) per spostare le attività di blocco dal thread principale e l'uso di un CursorLoader consente di caricare i dati in un thread prima di essere associati a un controllo ListView per la visualizzazione.

Per ulteriori informazioni, vedere [Introduzione a ContentProviders](~/android/platform/content-providers/index.md) .
