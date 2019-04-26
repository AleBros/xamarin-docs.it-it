---
title: Uso di un ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d1ec628de3481820f320a5a8e6ef88fcbaab75a6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61189737"
---
# <a name="using-a-contentprovider"></a>Uso di un ContentProvider

Cursoradapter è anche utilizzabile per visualizzare i dati di un ContentProvider.
ContentProviders consentono di accedere ai dati esposti da altre applicazioni (inclusi i dati di sistema di Android, ad esempio contatti, informazioni multimediali e calendario).

Il modo migliore per accedere a un ContentProvider è con un CursorLoader usando il LoaderManager. LoaderManager è stata introdotta in Android 3.0 (API livello 11, Honeycomb) per spostare le attività di blocco disattivato il thread principale e l'uso di un CursorLoader consente i caricamento dei dati in un thread prima di essere associato a un ListView per la visualizzazione.

Fare riferimento a [Introduzione a ContentProviders](~/android/platform/content-providers/index.md) per altre informazioni.

