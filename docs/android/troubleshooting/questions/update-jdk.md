---
title: Come si aggiorna la versione di Java Development Kit (JDK)?
description: Questo articolo illustra come aggiornare la versione di Java Development Kit (JDK) in Windows e Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: 0f7499551db7d86d7978b9c3e1f562a2f054c202
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019525"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Come si aggiorna la versione di Java Development Kit (JDK)?

_Questo articolo illustra come aggiornare la versione di Java Development Kit (JDK) in Windows e Mac._

## <a name="overview"></a>Panoramica

Novell. Android usa Java Development Kit (JDK) per l'integrazione con i Android SDK per la creazione di app Android e l'esecuzione di Android designer. Le versioni più recenti del Android SDK (API 24 e versioni successive) richiedono JDK 8 (1,8). In alternativa, è possibile installare [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). Microsoft Mobile OpenJDK sostituirà infine JDK 8 per lo sviluppo di Novell. Android.

Per eseguire l'aggiornamento a Microsoft Mobile OpenJDK, vedere [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). Per eseguire l'aggiornamento a JDK 8, seguire questa procedura:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Scaricare JDK 8 (1,8) dal [sito Web Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Screenshot della pagina di download di JDK nel sito Web Oracle](update-jdk-images/image1.png)

2. Selezionare la versione a 64 bit per consentire il rendering di [controlli personalizzati](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/vs/xamarin.vs_4/xamarin.vs_4.2/index.md#androiddesignercustomcontrols) in Novell Android designer:

    ![Selezione del pacchetto JDK Windows x64 da scaricare dalla pagina di download di JDK](update-jdk-images/image2.png)

3. Eseguire il file con estensione exe e installare gli **strumenti di sviluppo**:

    ![Installazione degli strumenti di sviluppo nel programma di installazione di JDK](update-jdk-images/image3.png)

4. Aprire Visual Studio e aggiornare il **percorso di Java Development Kit** in modo che punti al nuovo JDK in **strumenti > opzioni > Novell > Android Settings > percorso di Java Development Kit**:

    [impostazione del percorso di![per JDK nella pagina delle impostazioni di Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Assicurarsi di riavviare Visual Studio dopo aver aggiornato il percorso.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Scaricare JDK 8 (1,8) dal [sito Web Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Screenshot della pagina di download di JDK nel sito Web Oracle](update-jdk-images/image1.png)

2. Aprire il file con estensione DMG ed eseguire il programma di installazione con estensione pkg:

    ![Esecuzione del programma di installazione di JDK in macOS](update-jdk-images/image5.png)

Mac OS imposterà automaticamente la nuova versione JDK come impostazione predefinita tramite l'aggiornamento di **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. È quindi possibile verificare che il percorso **Java SDK (JDK)** sia impostato sul valore predefinito previsto di **/usr** in **Visual Studio per Mac > Preferenze > Progetti > percorsi sdk > percorso di Android > > Java SDK (JDK)**:

[![impostazione del percorso JDK nella scheda percorsi Android](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----
