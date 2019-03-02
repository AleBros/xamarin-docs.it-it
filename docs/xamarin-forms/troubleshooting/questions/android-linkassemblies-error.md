---
title: "Errore di compilazione Android: LinkAssemblies l'attività non è riuscita"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 552ad4d2de6e2560dc4301a9b5cc0ce6a5edb348
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57197111"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Errore di compilazione Android: LinkAssemblies l'attività non è riuscita

Si può vedere un messaggio di errore `The "LinkAssemblies" task failed unexpectedly` quando la compilazione di un progetto xamarin. Android che utilizza form. Ciò si verifica quando il linker è attivo (in genere in un *rilascio* compilazione per ridurre le dimensioni del pacchetto dell'app); e ciò avviene perché gli obiettivi di Android non sono aggiornati per il framework più recente. (Altre informazioni: [Xamarin. Forms per i requisiti di Android](~/get-started/requirements.md#android))

La soluzione per questo problema consiste nell'assicurarsi di disporre le ultime versioni supportate di Android SDK e impostare il **Framework di destinazione** al **Usa la piattaforma installata più recente**. È inoltre consigliabile impostare il **la versione Android di destinazione** a **versione Framework di destinazione usare** e il **versione minima di Android** per API 15 o versione successiva. Questa viene considerata la configurazione supportata.

## <a name="setting-in-visual-studio-for-mac"></a>Impostazione in Visual Studio per Mac

1.  Fare clic sul progetto Android.
2.  Passare a **compilazione > generale > Framework di destinazione**.
3.  Impostare il **Framework di destinazione: Usa la piattaforma installata più recente**.
4.  Ancora nelle opzioni del progetto, passare a **compilazione > applicazione Android**.
5.  Impostare il **Minimum Android version** a livello API 15 o successivo & la **Target Android version** a **automatico - versione di framework di destinazione usare**.

## <a name="setting-in-visual-studio"></a>Impostazione in Visual Studio

1.  Fare clic sul progetto Android.
2.  Passare a **applicazione** nelle opzioni del progetto.
3.  Impostare il **la compilazione con la versione di Android** & **Target Android version** impostazioni **Usa la piattaforma più recente** / **usare La compilazione con versione del SDK**.
4.  Impostare il **minima di Android di destinazione** impostando su API 19 o superiore.

Dopo avere aggiornato queste impostazioni, per pulire e ricompilare il progetto per assicurarsi che le modifiche vengono prelevate.
