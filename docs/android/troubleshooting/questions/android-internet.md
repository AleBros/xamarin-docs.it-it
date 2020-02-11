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
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027015"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Perché non è possibile connettere la build di versione Android a Internet?

## <a name="cause"></a>Causa

La causa più comune di questo problema è che l'autorizzazione **Internet** viene inclusa automaticamente in una build di debug, ma deve essere impostata manualmente per una build di rilascio. Questo perché l'autorizzazione Internet viene usata per consentire a un debugger di connettersi al processo, come descritto per "DebugSymbols" [qui](~/android/deploy-test/building-apps/build-process.md).

## <a name="fix"></a>Correggi

Per risolvere il problema, è possibile richiedere l'autorizzazione Internet nel manifesto Android. Questa operazione può essere eseguita tramite l'editor del manifesto o il codice sorgente del manifesto:

- Correzione nell'Editor: nel progetto Android passare a **Properties-> file AndroidManifest. XML-> le autorizzazioni necessarie** e controllare **Internet**

- Correzione in codice sorgente: aprire il file AndroidManifest in un editor di origine e aggiungere il tag di autorizzazione all'interno dei tag `<Manifest>`:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
