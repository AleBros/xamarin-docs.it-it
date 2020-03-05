---
title: "Errore di compilazione Android: l'attività LinkAssemblies non è riuscita in modo imprevisto"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: a90c56102f77e3f64d9333eec03b025d24888977
ms.sourcegitcommit: a3b7e016fb25584dbf57bae89b64a9f98031e7c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "78291585"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Errore di compilazione Android: l'attività LinkAssemblies non è riuscita in modo imprevisto

È possibile che venga visualizzato un messaggio di errore `The "LinkAssemblies" task failed unexpectedly` quando si compila un progetto Novell. Android che usa i moduli. Questo errore si verifica quando il linker è attivo (in genere in una build di *rilascio* per ridurre le dimensioni del pacchetto dell'app); il problema si verifica perché le destinazioni Android non vengono aggiornate al Framework più recente. (Altre informazioni: [piattaforme supportate per Novell. Forms](~/get-started/supported-platforms.md#android-platform-support))

La risoluzione di questo problema consiste nel verificare che siano disponibili le versioni di Android SDK supportate più recenti e impostare il **Framework di destinazione** sulla piattaforma installata più recente. Si consiglia inoltre di impostare la versione di **Android di destinazione** sull'ultima piattaforma installata e la **versione minima di Android** sull'API 19 o successiva. Questa operazione è considerata la configurazione supportata.

## <a name="setting-in-visual-studio-for-mac"></a>Impostazione in Visual Studio per Mac

1. Fare clic con il pulsante destro del mouse sul progetto Android e scegliere **Opzioni** dal menu.
2. Nella finestra di dialogo **Opzioni progetto** passare a **Compila > generale**.
3. Imposta la **versione di compilazione usando Android: (Framework di destinazione)** per la piattaforma installata più recente.
4. Nella finestra di dialogo **Opzioni progetto** passare a **Compila > applicazione Android**.
5. Impostare la **versione minima di Android** sul livello API 19 o versione successiva e la **versione di Android di destinazione** sull'ultima piattaforma installata scelta in (3).

## <a name="setting-in-visual-studio"></a>Impostazione in Visual Studio

1. Fare clic con il pulsante destro del mouse sul progetto Android e scegliere **appropriate** nel menu.
2. Nelle proprietà del progetto passare a **applicazione**.
3. Imposta la **versione di compilazione usando Android: (Framework di destinazione)** per la piattaforma installata più recente.
4. Nelle proprietà del progetto passare a **manifesto Android**.
5. Impostare la **versione minima di Android** sul livello API 19 o versione successiva e la **versione di Android di destinazione** sull'ultima piattaforma installata scelta in (3).

Dopo aver aggiornato le impostazioni, pulire e ricompilare il progetto per assicurarsi che le modifiche vengano selezionate.
