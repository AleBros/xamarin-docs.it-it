---
title: Le impostazioni del progetto sono necessarie per il debugger?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 646ef7f708be2de6a851ace25d69a7c2f0b18a83
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350807"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>Le impostazioni del progetto sono necessarie per il debugger?

Affinché il debugger a funzionare come previsto (passaggi dei punti di interruzione, visualizzare i log di debug e così via), visualizzazione di informazioni di debug e strumentazione per sviluppatori deve essere entrambi abilitata.

Seguire questa procedura per verificare le impostazioni di ambiente:

## <a name="visual-studio"></a>Visual Studio
1. Aprire le opzioni di progetto
2. Passare a **compilazione > Avanzate...** Impostare le informazioni di Debug **completo**
3. Impostazioni per ogni piattaforma:
   - Passare a **opzioni Android > Opzioni di debug**. Segni di graduazione il **abilitare la strumentazione per sviluppatori** casella.
   - Passare a **compilazione iOS > Opzioni di debug**. Segni di graduazione il **Abilita debug** casella.

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac
1. Aprire le opzioni di progetto
2. Passare a **compilazione > compilatore > Opzioni generali**. Impostare le informazioni di Debug **completo**
3. Impostazioni per ogni piattaforma:
  - Passare a **compilazione > compilazione Android > Opzioni di debug**. Segni di graduazione il **abilitare la strumentazione per sviluppatori** casella.
  - Passare a **compilazione > Debug iOS**. Segni di graduazione il **Abilita debug** casella.

