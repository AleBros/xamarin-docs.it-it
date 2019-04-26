---
title: Perché non è possibile connettere la build di versione Android a Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: cd27d5c884086cd0fade4364851039fd0cd915a0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945461"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Perché non è possibile connettere la build di versione Android a Internet?

## <a name="cause"></a>Causa

La causa più comune di questo problema è che il **INTERNET** autorizzazione viene automaticamente incluso in una build di debug, ma deve essere impostato manualmente per una build di rilascio. Poiché l'autorizzazione Internet viene utilizzato per consentire al debugger di connettersi al processo, come descritto per "DebugSymbols" [qui](~/android/deploy-test/building-apps/build-process.md).


## <a name="fix"></a>Correggi

Per risolvere il problema, è possibile richiedere l'autorizzazione Internet nel manifesto Android. Questa operazione può essere eseguita tramite l'editor del manifesto o sourcecode del manifesto:

-   Correggere nell'Editor: Nel progetto Android, passare a **proprietà -> androidmanifest. Xml -> autorizzazioni necessarie** e controllare **Internet**

-   Risolvere in Sourcecode: Aprire il AndroidManifest in un editor di origine e aggiungere il tag di autorizzazione all'interno di `<Manifest>` tag:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
