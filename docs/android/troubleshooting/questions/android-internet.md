---
title: "Perché la compilazione di Android versione non può connettersi a Internet?"
ms.topic: article
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: c37a417b84db4028ccea3848a6c152b8ff8d8ead
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Perché la compilazione di Android versione non può connettersi a Internet?

## <a name="cause"></a>Causa

La causa più comune di questo problema è che il **INTERNET** autorizzazione viene inclusa automaticamente in una build di debug, ma è necessario impostare manualmente per una build di rilascio. In questo modo viene utilizzato l'autorizzazione di Internet per consentire al debugger di connettersi al processo, come descritto per "DebugSymbols" [qui](~/android/deploy-test/building-apps/build-process.md).


## <a name="fix"></a>Correzione

Per risolvere il problema, è possibile richiedere l'autorizzazione di Internet nel manifesto dell'Android. Questa operazione può essere eseguita tramite l'editor del manifesto o sourcecode del manifesto:

-   Correggere nell'Editor: nel progetto Android, passare a **proprietà -> AndroidManifest.xml -> autorizzazioni obbligatorie** e controllare **Internet**

-   Correggere Sourcecode: aprire il AndroidManifest in un editor di origine e aggiungere il tag di autorizzazione all'interno di `<Manifest>` tag:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
