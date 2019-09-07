---
title: Quali impostazioni di progetto sono richieste per il debugger?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: 343f8d37d77726d2cdc06a74c44e476af00dde27
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765151"
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
