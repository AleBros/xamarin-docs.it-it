---
title: Perché Visual Studio non include il progetto libreria di riferimento nella compilazione?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: aba2e9dacd930c6302a96a8daf929eadab485922
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73012750"
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>Perché Visual Studio non include il progetto libreria di riferimento nella compilazione?

Visual Studio usa il **Configuration Manager** per determinare quali progetti di una soluzione vengono inclusi automaticamente in una configurazione di compilazione o distribuzione specifica.

Per alcuni modelli generati con un progetto di libreria a cui viene fatto riferimento sarà già inclusa la libreria a cui si fa riferimento. in caso contrario, sarà necessario impostarlo manualmente.

## <a name="how-to-use-the-configuration-manager"></a>Come utilizzare il Configuration Manager

1. Apri **> di compilazione Configuration Manager**
2. Selezionare la configurazione da personalizzare, ad esempio **debug | iPhone**
3. Selezionare le caselle di controllo per i progetti che si desidera includere.

> [!NOTE]
> Le caselle in grigio sono gestite automaticamente e non è necessario apportare alcuna modifica.

Screencast di questi passaggi: [https://screencast.com/t/zLoQOpEn](https://screencast.com/t/zLoQOpEn)
