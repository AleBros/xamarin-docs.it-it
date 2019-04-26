---
title: Dove è possibile impostare i percorsi di Android SDK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 11/16/2017
ms.openlocfilehash: c004fb7717f78750e7ac1e8dc1856a32ba808638
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227674"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>Dove è possibile impostare i percorsi di Android SDK?

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In Visual Studio, passare a **strumenti > Opzioni > Xamarin > Impostazioni Android** per visualizzare e impostare il percorso di Android SDK:

[![Scheda percorsi di esempio nelle preferenze](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

Il percorso predefinito per ogni percorso è come segue:

- Percorso di Java Development Kit: 

    **C:\\file di programma\\Java\\jdk1.8.0_131**

- Percorso di Android SDK: 

    **C:\\Programmi (x86)\\Android\\android-sdk**

- Percorso di Android NDK: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android-ndk-r13b**

Si noti che il numero di versione di NDK può variare. Ad esempio, invece di **ndk-android-r13b**, potrebbe essere una versione precedente, ad esempio **ndk-android-r10e**.

Per impostare il percorso di Android SDK, immettere il percorso completo della directory in Android SDK di **percorso di Android SDK** casella. È possibile passare al percorso di Android SDK in Esplora File, copiare il percorso dalla barra degli indirizzi e incollare il percorso nel **percorso di Android SDK** casella.
Ad esempio, se il percorso di Android SDK si trova **c:\\gli utenti\\username\\AppData\\locale\\Android\\Sdk**, cancellare il percorso precedente nel  **Percorso di Android SDK** casella, incollare in questo percorso e fare clic su **OK**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In Visual Studio per Mac, passare a **Preferenze > progetti > percorsi SDK > Android**. Nel **Android** pagina, fare clic sui **posizioni** pressione di tab per visualizzare e impostare il percorso di SDK:

[![Scheda percorsi di esempio nelle preferenze](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

Il percorso predefinito per ogni percorso è come segue:

- Percorso di Android SDK: 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Percorso di Android NDK: 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Percorso di Java SDK (JDK): 

    **/usr**

Si noti che il numero di versione di NDK può variare. Ad esempio, invece di **ndk-android-r14b**, potrebbe essere una versione precedente, ad esempio **ndk-android-r10e**.

Per impostare il percorso di Android SDK, immettere il percorso completo della directory in Android SDK di **percorso di Android SDK** casella. È possibile selezionare la cartella SDK Android nel Finder, premere **CTRL +&#8984;+ I** per visualizzare le informazioni di cartella, fare clic e trascinare il percorso a destra di **in cui:**, copiare, quindi incollarlo il **Android SDK Ubicazione** nella casella il **posizioni** scheda. Ad esempio, se il percorso di Android SDK si trova **~/Library/Developer/Android/Sdk**, cancellare il percorso precedente nel **percorso di Android SDK** casella, incollare in questo percorso e fare clic su **OK**.

-----
