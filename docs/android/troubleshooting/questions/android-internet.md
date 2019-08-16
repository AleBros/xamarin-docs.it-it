---
title: Perché non è possibile connettere la build di versione Android a Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: dc84ecc0ee3a71cc4e1d4233f4d6d5f22f597b07
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523490"
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
