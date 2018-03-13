---
title: "In cui è possibile impostare la località di Android SDK?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 11/16/2017
ms.openlocfilehash: 0113cc15bf1de5e0e668b05c2b0288a6ead141b5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>In cui è possibile impostare la località di Android SDK?

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

In Visual Studio, passare a **strumenti > Opzioni > Xamarin > Impostazioni Android** per visualizzare e impostare il percorso di Android SDK:

[![Scheda indirizzi di esempio nelle preferenze](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

Il percorso predefinito per ogni percorso è la seguente:

- Java Development Kit percorso: 

    **C:\\i file di programma\\Java\\jdk1.8.0_131**

- Percorso di Android SDK: 

    **C:\\Programmi (x86)\\Android\\android-sdk**

- Percorso di Android NDK: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android-ndk-r13b**

Si noti che può variare il numero di versione di NDK. Ad esempio, invece di **android-ndk-r13b**, potrebbe trattarsi di una versione precedente, ad esempio **android-ndk-r10e**.

Per impostare il percorso di Android SDK, immettere il percorso completo della directory Android SDK nel **percorso di Android SDK** casella. Passare al percorso di Android SDK in Esplora File, copiare il percorso dalla barra degli indirizzi e incollare il percorso nel **percorso di Android SDK** casella.
Ad esempio, se il percorso di Android SDK è a **c:\\utenti\\username\\AppData\\locale\\Android\\Sdk**, cancellare il percorso precedente nel  **Percorso di Android SDK** , incollare in questo percorso e scegliere **OK**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

In Visual Studio per Mac, passare a **Preferenze > progetti > percorsi SDK > Android**. Nel **Android** pagina, fare clic su di **percorsi** scheda per visualizzare e impostare il percorso del SDK:

[![Scheda indirizzi di esempio nelle preferenze](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

Il percorso predefinito per ogni percorso è la seguente:

- Percorso di Android SDK: 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Percorso di Android NDK: 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Percorso di Java SDK (JDK): 

    **/usr**

Si noti che può variare il numero di versione di NDK. Ad esempio, invece di **android-ndk-r14b**, potrebbe trattarsi di una versione precedente, ad esempio **android-ndk-r10e**.

Per impostare il percorso di Android SDK, immettere il percorso completo della directory Android SDK nel **percorso di Android SDK** casella. È possibile selezionare la cartella di Android SDK in Finder, premere **CTRL +&#8984;+ I** per visualizzare le informazioni di cartella, fare clic e trascinare il percorso a destra del **in:**, copiare, quindi incollarlo il **Android SDK Percorso** casella il **percorsi** scheda. Ad esempio, se il percorso di Android SDK è a **~/Library/Developer/Android/Sdk**, cancellare il percorso precedente nel **percorso di Android SDK** , incollare in questo percorso e scegliere **OK**.

-----
