---
title: Quali impostazioni di progetto sono richieste per il debugger?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: a3677117e30538c4df8c9b797acce0be602c3b59
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106084"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>Quali impostazioni di progetto sono richieste per il debugger?

Affinché il debugger funzioni come previsto (hit punti di interruzione, Visualizza log di debug e così via), è necessario abilitare entrambi gli strumenti per la visualizzazione di informazioni di debug e strumentazione per sviluppatori.

Per verificare le impostazioni dell'ambiente, attenersi alla procedura seguente:

## <a name="visual-studio"></a>Visual Studio

1. Aprire le opzioni del progetto
2. Vai a **compilazione > avanzate...** Impostare le informazioni di debug su **full**
3. Impostazioni per ogni piattaforma:
   - Passare a **Opzioni Android > opzioni di debug**. Seleziona la casella **Abilita strumentazione per sviluppatori** .
   - Passare a **debug iOS > debug & strumentazione**. Seleziona la casella **Abilita debug** .

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Aprire le opzioni del progetto
2. Passare a **compila > compilatore > opzioni generali**. Impostare le informazioni di debug su **full**
3. Impostazioni per ogni piattaforma:
    - Passare a **build > Android build > opzioni di debug**. Seleziona la casella **Abilita strumentazione per sviluppatori** .
    - Passare a **Build > debug iOS**. Seleziona la casella **Abilita debug** .
