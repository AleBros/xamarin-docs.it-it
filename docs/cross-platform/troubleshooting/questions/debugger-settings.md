---
title: Quali impostazioni di progetto sono richieste per il debugger?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: e7a383f899fab0400104493fa89b125788d610aa
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831328"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>Quali impostazioni di progetto sono richieste per il debugger?

Affinché il debugger a funzionare come previsto (passaggi dei punti di interruzione, visualizzare i log di debug e così via), visualizzazione di informazioni di debug e strumentazione per sviluppatori deve essere entrambi abilitata.

Seguire questa procedura per verificare le impostazioni di ambiente:

## <a name="visual-studio"></a>Visual Studio
1. Aprire le opzioni di progetto
2. Passare a **compilazione > Avanzate...** Impostare le informazioni di Debug **completo**
3. Impostazioni per ogni piattaforma:
   - Passare a **opzioni Android > Opzioni di debug**. Segni di graduazione il **abilitare la strumentazione per sviluppatori** casella.
   - Passare a **Debug iOS > debug e strumentazione**. Segni di graduazione il **Abilita debug** casella.

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac
1. Aprire le opzioni di progetto
2. Passare a **compilazione > compilatore > Opzioni generali**. Impostare le informazioni di Debug **completo**
3. Impostazioni per ogni piattaforma:
    - Passare a **compilazione > compilazione Android > Opzioni di debug**. Segni di graduazione il **abilitare la strumentazione per sviluppatori** casella.
    - Passare a **compilazione > Debug iOS**. Segni di graduazione il **Abilita debug** casella.

