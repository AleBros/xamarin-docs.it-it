---
title: Perché non è possibile connettere build di versione Android a Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: cd27d5c884086cd0fade4364851039fd0cd915a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117908"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Perché non è possibile connettere build di versione Android a Internet?

## <a name="cause"></a>Causa

La causa più comune di questo problema è che il **INTERNET** autorizzazione viene automaticamente incluso in una build di debug, ma deve essere impostato manualmente per una build di rilascio. Poiché l'autorizzazione Internet viene utilizzato per consentire al debugger di connettersi al processo, come descritto per "DebugSymbols" [qui](~/android/deploy-test/building-apps/build-process.md).


## <a name="fix"></a>Correzione

Per risolvere il problema, è possibile richiedere l'autorizzazione Internet nel manifesto Android. Questa operazione può essere eseguita tramite l'editor del manifesto o sourcecode del manifesto:

-   Correggere nell'Editor: nel progetto Android, passare a **proprietà -> androidmanifest. Xml -> autorizzazioni necessarie** e controllare **Internet**

-   Correzione in Sourcecode: aprire il AndroidManifest in un editor di origine e aggiungere il tag di autorizzazione all'interno di `<Manifest>` tag:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
