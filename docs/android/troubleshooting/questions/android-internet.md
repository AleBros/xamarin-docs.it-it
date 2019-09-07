---
title: Perché non è possibile connettere la build di versione Android a Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 162378c00f3e20574d04dc373fcc492a9407b88d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761036"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Perché non è possibile connettere la build di versione Android a Internet?

## <a name="cause"></a>Causa

La causa più comune di questo problema è che l'autorizzazione **Internet** viene inclusa automaticamente in una build di debug, ma deve essere impostata manualmente per una build di rilascio. Questo perché l'autorizzazione Internet viene usata per consentire a un debugger di connettersi al processo, come descritto per "DebugSymbols" [qui](~/android/deploy-test/building-apps/build-process.md).

## <a name="fix"></a>Correzione

Per risolvere il problema, è possibile richiedere l'autorizzazione Internet nel manifesto Android. Questa operazione può essere eseguita tramite l'editor del manifesto o il codice sorgente del manifesto:

- Correzione nell'Editor: Nel progetto Android passare a **Properties-> file AndroidManifest. XML-> le autorizzazioni necessarie** e controllare **Internet**

- Correzione nel codice sorgente: Aprire file AndroidManifest in un editor di origine e aggiungere il tag di autorizzazione all' `<Manifest>` interno dei tag:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
