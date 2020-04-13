---
title: Dove è possibile impostare i percorsi di Android SDK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 11/16/2017
ms.openlocfilehash: 8685be4bb1cc45ff04dc8d9f7d8e64e7b1483b60
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027030"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>Dove è possibile impostare i percorsi di Android SDK?

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

In Visual Studio passare a **Strumenti > Opzioni > Impostazioni di Xamarin > Android** per visualizzare e impostare il percorso di Android SDK:In Visual Studio, navigate to Tools > Options > Xamarin > Android Settings to view and set the Android SDK location:

[![Scheda Posizioni di esempio in Preferenze](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

Il percorso predefinito per ogni percorso è il seguente:

- Posizione del kit di sviluppo Java: 

    **C:\\Programmi\\\\Java jdk1.8.0_131**

- Posizione di Android SDK: 

    **C:\\Programmi\\(x86)\\Android Android-sdk**

- Posizione NDK Android: 

    **C:\\\\ProgramData\\Microsoft AndroidNDK64\\android-ndk-r13b**

Si noti che il numero di versione del codice NDK può variare. Ad esempio, anziché **android-ndk-r13b**, potrebbe essere una versione precedente, ad esempio **android-ndk-r10e**.

Per impostare il percorso di Android SDK, immettere il percorso completo della directory di Android SDK nella casella **Percorso di Android SDK.** È possibile passare al percorso di Android SDK in Esplora file, copiare il percorso dalla barra degli indirizzi e incollare il percorso nella casella **Percorso di Android SDK.**
Ad esempio, se il percorso dell'SDK di Android è **in\\C:\\Users nome utente\\AppData\\Local\\Android\\Sdk**, cancellare il percorso precedente nella casella percorso di Android **SDK** , incollare il percorso e fare clic su **OK**.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

In Visual Studio per Mac passare a **Preferenze > Progetti > SDK percorsi > Android**. Nella pagina **Android,** fai clic sulla scheda **Percorsi** per visualizzare e impostare il percorso dell'SDK:

[![Scheda Posizioni di esempio in Preferenze](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

Il percorso predefinito per ogni percorso è il seguente:

- Posizione di Android SDK: 

    **/Libreria/Sviluppatore/Xamarin/android-sdk-macosx**

- Posizione NDK Android: 

    **/Libreria/Sviluppatore/Xamarin/android-ndk/android-ndk-r14b**

- Percorso Java SDK (JDK): 

    **/usr (informazioni in inglese)**

Si noti che il numero di versione del codice NDK può variare. Ad esempio, invece di **android-ndk-r14b**, potrebbe essere una versione precedente, ad esempio **android-ndk-r10e**.

Per impostare il percorso di Android SDK, immettere il percorso completo della directory di Android SDK nella casella **Percorso di Android SDK.** È possibile selezionare la cartella SDK di Android nel Finder, premere **CTRL e&#8984;I** per visualizzare le informazioni della cartella, fare clic e trascinare il percorso a destra di **Dove:**, copiarlo, quindi incollarlo nella casella **Posizione Android SDK** nella scheda **Posizioni.** Ad esempio, se il percorso dell'SDK di Android si trova in **/Library/Developer/Android/Sdk**, cancellare il percorso precedente nella casella **Percorso di Android SDK,** incollare il percorso e fare clic su **OK**.

-----
