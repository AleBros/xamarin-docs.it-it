---
title: Come si aggiorna la versione di Java Development Kit (JDK)?
description: Questo articolo viene illustrato come aggiornare la versione di Java Development Kit (JDK) in Windows e Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 290a7deef4fdc10163bdb09881382f011b0dcd32
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670851"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Come si aggiorna la versione di Java Development Kit (JDK)?

_Questo articolo viene illustrato come aggiornare la versione di Java Development Kit (JDK) in Windows e Mac._

## <a name="overview"></a>Panoramica

Xamarin. Android Usa Java Development Kit (JDK) per l'integrazione con Android SDK per compilare le app Android ed eseguire Android designer. Le versioni più recenti di Android SDK (API 24 e versioni successive) richiedono JDK 8 (1.8). In alternativa, è possibile installare il [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). OpenJDK per dispositivi mobili Microsoft sostituirà JDK 8 per lo sviluppo di xamarin. Android.

Per aggiornare a OpenJDK per dispositivi mobili Microsoft, vedere [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). Per aggiornare JDK 8, seguire questa procedura:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Download di JDK 8 (1.8) dal [sito Web Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Screenshot del JDK download nel sito Web Oracle](update-jdk-images/image1.png)

2.  Selezionare la versione a 64 bit per consentire il rendering dei [controlli personalizzati](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols) nella finestra di progettazione di Xamarin. Android:

    ![Selezionare il pacchetto JDK di Windows x64 da scaricare dalla pagina di download del JDK](update-jdk-images/image2.png)

3.  Eseguire il .exe e installare il **strumenti di sviluppo**:

    ![Installare gli strumenti di sviluppo nel programma di installazione JDK](update-jdk-images/image3.png)

4.  Aprire Visual Studio e aggiornare il **percorso di Java Development Kit** in modo che punti al nuovo JDK in **strumenti > Opzioni > Xamarin > Impostazioni Android > percorso di Java Development Kit**:

    [![Impostazione del percorso per il JDK nella pagina Impostazioni Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Assicurarsi di riavviare Visual Studio dopo avere aggiornato la posizione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1.  Download di JDK 8 (1.8) dal [sito Web Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Screenshot del JDK download nel sito Web Oracle](update-jdk-images/image1.png)

2.  Aprire il file. dmg ed eseguire il programma di installazione con estensione pkg:

    ![Esegue il programma di installazione JDK in macOS](update-jdk-images/image5.png)

Mac OS imposterà automaticamente la nuova versione JDK come impostazione predefinita, aggiornando **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. È quindi possibile verificare che il **Java SDK (JDK)** percorso viene impostato sul valore predefinito previsto **/usr** sotto **Visual Studio per Mac > Preferenze > progetti > percorsi SDK > Android > percorsi > percorso di Java SDK (JDK)**:

[![Impostazione della posizione di JDK nella scheda percorsi di Android](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----

