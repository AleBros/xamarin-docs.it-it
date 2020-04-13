---
title: Perché non è possibile connettere la build di versione Android a Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 5996cfa3c0a18fc186ea862a2b3d7910594e1281
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027015"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Perché non è possibile connettere la build di versione Android a Internet?

## <a name="cause"></a>Causa

La causa più comune di questo problema è che l'autorizzazione **INTERNET** viene inclusa automaticamente in una build di debug, ma deve essere impostata manualmente per una build di rilascio. Ciò è dovuto al fatto che l'autorizzazione Internet viene utilizzata per consentire a un debugger di connettersi al processo, come descritto per "DebugSymbols" [qui](~/android/deploy-test/building-apps/build-process.md).

## <a name="fix"></a>Fix

Per risolvere il problema, è possibile richiedere l'autorizzazione Internet nel manifesto Android.To resolve the issue, you can require the Internet permission in the Android Manifest. Questa operazione può essere eseguita tramite l'editor del manifesto o il codice sorgente del manifesto:This can be done either through the manifest editor or the manifest's sourcecode:

- Correggere nell'editor: nel progetto Android, vai a **Proprietà -> AndroidManifest.xml -> autorizzazioni necessarie** e controlla **Internet**

- Correggere nel codice sorgente: apri AndroidManifest in un editor `<Manifest>` di origine e aggiungi il tag di autorizzazione all'interno dei tag:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
