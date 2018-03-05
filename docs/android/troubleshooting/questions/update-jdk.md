---
title: Come aggiornare la versione di Java Development Kit (JDK)?
description: In questo articolo viene illustrato come aggiornare la versione di Java Development Kit (JDK) in Windows e Mac.
ms.topic: article
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 126d4b2eaf1c2408704c0f2d11c2f88a7c25f406
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Come aggiornare la versione di Java Development Kit (JDK)?

_In questo articolo viene illustrato come aggiornare la versione di Java Development Kit (JDK) in Windows e Mac._

## <a name="overview"></a>Panoramica

Xamarin utilizza Java Development Kit (JDK) per l'integrazione con Android SDK per la compilazione di App per Android e in esecuzione la finestra di progettazione Android. Le versioni più recenti di Android SDK (API 24 e versioni successive) richiedono JDK 8 (1,8). Se non è stato ancora aggiornato per JDK 8, seguire questi passaggi per installare e abilitare:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Download di JDK 8 (1.8) dal [sito Web Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Schermata di JDK scaricare pagina del sito Web Oracle](update-jdk-images/image1.png)

2.  Selezione della versione a 64 bit per consentire il rendering dei [controlli personalizzati](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols) nella finestra di progettazione Android di Xamarin:

    ![Selezionare il pacchetto JDK di Windows x64 da scaricare dalla pagina di download JDK](update-jdk-images/image2.png)

3.  Eseguire il .exe e installare il **gli strumenti di sviluppo**:

    ![Installare gli strumenti di sviluppo del programma di installazione di JDK](update-jdk-images/image3.png)

4.  Aprire Visual Studio e l'aggiornamento di **Java Development Kit percorso** per puntare al nuovo JDK in **strumenti > Opzioni > Xamarin > Impostazioni Android > percorso Kit di sviluppo Java > Modifica**:

    ![Impostazione del percorso per il pacchetto JDK nella pagina Impostazioni Android IDE](update-jdk-images/image4.png)

Assicurarsi di riavviare Visual Studio dopo l'aggiornamento del percorso.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1.  Download di JDK 8 (1.8) dal [sito Web Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Schermata di JDK scaricare pagina del sito Web Oracle](update-jdk-images/image1.png)

2.  Aprire il file. dmg ed eseguire il programma di installazione con estensione pkg:

    ![In esecuzione il programma di installazione JDK in Mac OS](update-jdk-images/image5.png)

Mac OS imposterà automaticamente la nuova versione JDK come impostazione predefinita, aggiornando **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. È quindi possibile verificare che il **SDK Java (JDK)** percorso viene impostato sul valore predefinito previsto **in /usr.** in **Visual Studio per Mac > Preferenze > progetti > percorsi SDK > Android > Java (JDK) SDK > percorso**:

![Impostazione della posizione di JDK nella pagina percorso di Android SDK](update-jdk-images/image6.png)

-----

