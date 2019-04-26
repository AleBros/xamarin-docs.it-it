---
title: "Errore di compilazione Android: LinkAssemblies l'attività non è riuscita"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: f517aaa770fa7b2f1463954638f0afc95168bf65
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250741"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Errore di compilazione Android: LinkAssemblies l'attività non è riuscita

Si può vedere un messaggio di errore `The "LinkAssemblies" task failed unexpectedly` quando la compilazione di un progetto xamarin. Android che utilizza form. Ciò si verifica quando il linker è attivo (in genere in un *rilascio* compilazione per ridurre le dimensioni del pacchetto dell'app); e ciò avviene perché gli obiettivi di Android non sono aggiornati per il framework più recente. (Altre informazioni: [Xamarin. Forms per i requisiti di Android](~/get-started/requirements.md#android))

La soluzione per questo problema consiste nell'assicurarsi di disporre le ultime versioni supportate di Android SDK e impostare il **Framework di destinazione** per la piattaforma installata più recente. È inoltre consigliabile impostare il **la versione Android di destinazione** per la piattaforma installata più recente e il **versione minima di Android** su API 19 o superiore. Questa viene considerata la configurazione supportata.

## <a name="setting-in-visual-studio-for-mac"></a>Impostazione in Visual Studio per Mac

1.  Fare clic con il pulsante destro sul progetto Android e selezionare **opzioni** nel menu di scelta.
2.  Nel **opzioni progetto** finestra di dialogo passa alla **compilazione > Generale**.
3.  Impostare il **la compilazione con la versione di Android: (Framework di destinazione)**  per la piattaforma installata più recente.
4.  Nel **opzioni progetto** finestra di dialogo passa alla **compilazione > applicazione Android**.
5.  Impostare il **Minimum Android version** a livello API 19 o superiore e il **Target Android version** per la piattaforma installata più recente si è scelto in (3).

## <a name="setting-in-visual-studio"></a>Impostazione in Visual Studio

1.  Fare clic con il pulsante destro sul progetto Android e selezionare **mostrarle** nel menu di scelta.
2.  Nelle proprietà del progetto, passare a **applicazione**.
3.  Impostare il **la compilazione con la versione di Android: (Framework di destinazione)**  per la piattaforma installata più recente.
4.  Nelle proprietà del progetto, passare a **manifesto Android**.
5.  Impostare il **Minimum Android version** a livello API 19 o superiore e il **Target Android version** per la piattaforma installata più recente si è scelto in (3).

Dopo avere aggiornato queste impostazioni, per pulire e ricompilare il progetto per assicurarsi che le modifiche vengono prelevate.
