---
title: Uso di un ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 5948d4b5db53db97c4e76cb7568c109b5faf90fe
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762205"
---
# <a name="using-a-contentprovider-with-xamarinandroid"></a>Uso di ContentProvider con Novell. Android

CursorAdapter può essere usato anche per visualizzare i dati da un ContentProvider.
ContentProviders consentono di accedere ai dati esposti da altre applicazioni, inclusi i dati di sistema Android, ad esempio contatti, supporti e informazioni sul calendario.

Il modo migliore per accedere a un ContentProvider è con una CursorLoader che usa LoaderManager. LoaderManager è stato introdotto in Android 3,0 (livello API 11, Honeycomb) per spostare le attività di blocco dal thread principale e l'uso di un CursorLoader consente di caricare i dati in un thread prima di essere associati a un controllo ListView per la visualizzazione.

Per ulteriori informazioni, vedere [Introduzione a ContentProviders](~/android/platform/content-providers/index.md) .
