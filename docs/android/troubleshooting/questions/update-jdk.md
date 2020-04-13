---
title: Come si aggiorna la versione di Java Development Kit (JDK)?
description: In questo articolo viene illustrato come aggiornare la versione Java Development Kit (JDK) su Windows e Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: 0f7499551db7d86d7978b9c3e1f562a2f054c202
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019525"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Come si aggiorna la versione di Java Development Kit (JDK)?

_In questo articolo viene illustrato come aggiornare la versione Java Development Kit (JDK) su Windows e Mac._

## <a name="overview"></a>Panoramica

Xamarin.Android utilizza Java Development Kit (JDK) per l'integrazione con Android SDK per la creazione di applicazioni Android e l'esecuzione della finestra di progettazione Android. Le versioni più recenti di Android SDK (API 24 e versioni successive) richiedono JDK 8 (1.8). In alternativa, è possibile installare [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). Microsoft Mobile OpenJDK alla fine sostituirà JDK 8 per lo sviluppo di Xamarin.Android.

Per eseguire l'aggiornamento a Microsoft Mobile OpenJDK, vedere [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). Per eseguire l'aggiornamento a JDK 8, attenersi alla seguente procedura:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Scarica JDK 8 (1.8) dal [sito Web Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Screenshot della pagina di download di JDK sul sito Web Oracle](update-jdk-images/image1.png)

2. Scegli la versione a 64 bit per consentire il rendering dei [controlli personalizzati](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/vs/xamarin.vs_4/xamarin.vs_4.2/index.md#androiddesignercustomcontrols) nella finestra di progettazione Xamarin Android:

    ![Selezione del pacchetto JDK di Windows x64 da scaricare dalla pagina di download di JDK](update-jdk-images/image2.png)

3. Eseguire il file .exe e installare gli strumenti di **sviluppo**:

    ![Installazione degli strumenti di sviluppo nel programma di installazione JDK](update-jdk-images/image3.png)

4. Aprire Visual Studio e aggiornare il percorso del kit di **sviluppo Java** in modo che punti al nuovo JDK in Strumenti > Opzioni > **Xamarin > Impostazioni Android > Percorso del kit**di sviluppo Java :

    [![Impostazione del percorso per JDK nella pagina Impostazioni Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Assicurarsi di riavviare Visual Studio dopo aver aggiornato il percorso.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Scarica JDK 8 (1.8) dal [sito Web Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Screenshot della pagina di download di JDK sul sito Web Oracle](update-jdk-images/image1.png)

2. Aprire il file .dmg ed eseguire il programma di installazione .pkg:

    ![Esecuzione del programma di installazione JDK su macOS](update-jdk-images/image5.png)

Mac OS imposterà automaticamente la nuova versione di JDK come predefinita aggiornando **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. È quindi possibile verificare che il percorso **Java SDK (JDK)** sia impostato sul valore predefinito previsto **/usr** in **Visual Studio per Mac > Preferenze > Projects > SDK Locations > Android > Locations > Java SDK (JDK) Location**:

[![Impostazione della posizione JDK nella scheda Posizioni Android](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----
