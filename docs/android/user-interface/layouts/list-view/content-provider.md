---
title: Uso di un ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d1ec628de3481820f320a5a8e6ef88fcbaab75a6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122114"
---
# <a name="using-a-contentprovider"></a>Uso di un ContentProvider

Cursoradapter è anche utilizzabile per visualizzare i dati di un ContentProvider.
ContentProviders consentono di accedere ai dati esposti da altre applicazioni (inclusi i dati di sistema di Android, ad esempio contatti, informazioni multimediali e calendario).

Il modo migliore per accedere a un ContentProvider è con un CursorLoader usando il LoaderManager. LoaderManager è stata introdotta in Android 3.0 (API livello 11, Honeycomb) per spostare le attività di blocco disattivato il thread principale e l'uso di un CursorLoader consente i caricamento dei dati in un thread prima di essere associato a un ListView per la visualizzazione.

Fare riferimento a [Introduzione a ContentProviders](~/android/platform/content-providers/index.md) per altre informazioni.

