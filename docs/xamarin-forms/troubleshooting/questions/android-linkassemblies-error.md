---
title: Errore di compilazione Android-LinkAssemblies l'attività non è riuscita
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: de6e0b66cac688955d27ba2d0165d5a059d36c38
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Errore di compilazione Android-LinkAssemblies l'attività non è riuscita

Si potrebbe visualizzare un messaggio di errore `The "LinkAssemblies" task failed unexpectedly` quando la compilazione di un progetto xamarin che utilizza form. Ciò si verifica quando il linker è attivo (in genere su un *versione* compilazione per ridurre le dimensioni del pacchetto dell'applicazione); e si verifica perché il dispositivo Android non vengono aggiornato per il framework più recente. (Ulteriori informazioni: [xamarin. Forms per i requisiti di Android](~/xamarin-forms/get-started/installation.md#android))

La soluzione per questo problema consiste nell'assicurarsi di avere le ultime versioni supportate di Android SDK e impostare il **Framework di destinazione** a **utilizzare più recente installato platform**. È inoltre consigliabile impostare il **destinazione Android versione** per **versione Framework di destinazione utilizzare** e **versione minima di Android** a 15 API o versione successiva. Questa viene considerata la configurazione supportata.

## <a name="setting-in-visual-studio-for-mac"></a>Impostazione in Visual Studio per Mac

1.  Fare clic sul progetto Android.
2.  Passare a **compilazione > generale > Framework di destinazione**.
3.  Impostare il **Framework di destinazione: utilizzare più recente installato platform**.
4.  In opzioni del progetto, passare a **compilazione > applicazione Android**.
5.  Impostare il **minimo Android versione** a livello di API 15 o versione successiva & il **versione di destinazione Android** per **automatico - versione di framework di destinazione utilizzare**.

## <a name="setting-in-visual-studio"></a>Impostazione in Visual Studio

1.  Fare clic sul progetto Android.
2.  Passare a **applicazione** nelle opzioni del progetto.
3.  Impostare il **compila con la versione Android** & **versione di destinazione Android** impostazioni **piattaforma più recente di utilizzare** / **utilizzo Compila con la versione SDK**.
4.  Impostare il **minimo Android di destinazione** impostando su API 15 o versioni successive.

Dopo avere aggiornato, tali impostazioni, pulire e ricompilare il progetto per verificare che le modifiche vengono prelevate.
