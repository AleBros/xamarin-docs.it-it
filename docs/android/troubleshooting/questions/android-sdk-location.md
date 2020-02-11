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
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027030"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>Dove è possibile impostare i percorsi di Android SDK?

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In Visual Studio passare a **strumenti > opzioni > Xamarin > le impostazioni Android** per visualizzare e impostare il percorso Android SDK:

[![scheda percorsi di esempio in Preferenze](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

Il percorso predefinito per ogni percorso è il seguente:

- Percorso di Java Development Kit: 

    **C:\\Program Files\\Java\\JDK 1.8.0 _131**

- Percorso Android SDK: 

    **C:\\Programmi (x86)\\Android\\android-sdk**

- Percorso NDK Android: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\Android-NDK-r13b**

Si noti che il numero di versione di NDK può variare. Ad esempio, anziché **Android-NDK-r13b**, potrebbe trattarsi di una versione precedente, ad esempio **Android-NDK-r10e**.

Per impostare il percorso di Android SDK, immettere il percorso completo della directory Android SDK nella casella **Android SDK percorso** . È possibile passare alla posizione Android SDK in Esplora file, copiare il percorso dalla barra degli indirizzi e incollare il percorso nella casella **percorso Android SDK** .
Ad esempio, se il percorso del Android SDK è **C:\\users\\username\\AppData\\Local\\Android\\SDK**, deselezionare il percorso precedente nella casella percorso **Android SDK** , incollare il percorso , quindi fare clic su **OK**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In Visual Studio per Mac passare a **preferenze > progetti > percorsi SDK > Android**. Nella pagina **Android** fare clic sulla scheda **percorsi** per visualizzare e impostare il percorso dell'SDK:

[![scheda percorsi di esempio in Preferenze](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

Il percorso predefinito per ogni percorso è il seguente:

- Percorso Android SDK: 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Percorso NDK Android: 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Percorso di Java SDK (JDK): 

    **/usr**

Si noti che il numero di versione di NDK può variare. Ad esempio, anziché **Android-NDK-r14b**, potrebbe trattarsi di una versione precedente, ad esempio **Android-NDK-r10e**.

Per impostare il percorso di Android SDK, immettere il percorso completo della directory Android SDK nella casella **Android SDK percorso** . È possibile selezionare la cartella Android SDK nel Finder, premere **&#8984;CTRL + + I** per visualizzare le informazioni sulla cartella, fare clic e trascinare il percorso a destra di **Where:** , copy, quindi incollarlo nella casella **Android SDK location** nella scheda **locations (percorsi** ). Se ad esempio il percorso del Android SDK è **~/Library/Developer/Android/SDK**, deselezionare il percorso precedente nella casella percorso **Android SDK** , incollare il percorso e fare clic su **OK**.

-----
