---
title: Le impostazioni del progetto sono necessari per il debugger?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: asb3993
ms.author: amburns
ms.openlocfilehash: 67c4b51a518f5c7dba6ae372dbd9206dd3ef8e9f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919989"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>Le impostazioni del progetto sono necessari per il debugger?

Affinché il debugger a funzionare come previsto (passaggi dei punti di interruzione, visualizzare i registri di debug e così via), visualizzazione di informazioni di debug e di strumentazione per sviluppatori deve entrambi essere abilitata.

Seguire questi passaggi per verificare le impostazioni di ambiente:

## <a name="visual-studio"></a>Visual Studio
1. Aprire le opzioni di progetto
2. Passare a **compilazione > Avanzate...** Impostare le informazioni di Debug **completo**
3. Impostazioni per ogni piattaforma:
   - Passare a **opzioni Android > Opzioni di debug**. Segni di graduazione di **abilitare strumentazione Developer** casella.
   - Passare a **compilazione iOS > Opzioni di debug**. Segni di graduazione di **Attiva debug** casella.

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac
1. Aprire le opzioni di progetto
2. Passare a **compilazione > compilatore > Opzioni generali**. Impostare le informazioni di Debug **completo**
3. Impostazioni per ogni piattaforma:
  - Passare a **compilazione > Android compilazione > Opzioni di debug**. Segni di graduazione di **abilitare strumentazione Developer** casella.
  - Passare a **compilazione > iOS Debug**. Segni di graduazione di **Attiva debug** casella.

