---
title: Perché Visual Studio non include il progetto libreria di riferimento nella compilazione?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: d7aeac2f433e8fdf231f5887f1537f15e2bd1976
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341308"
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>Perché Visual Studio non include il progetto libreria di riferimento nella compilazione?

Visual Studio Usa la **Configuration Manager** per determinare quali progetti in una soluzione vengono inclusi automaticamente in una determinata configurazione build o distribuzione.

Alcuni modelli generati con un progetto libreria di riferimento include già la libreria di riferimento inclusa nella configurazione. ma in caso contrario, dovrà essere impostato manualmente.

## <a name="how-to-use-the-configuration-manager"></a>Come usare Gestione configurazione

1. Apri **compilazione > Configuration Manager**
2. Selezionare la configurazione da personalizzare, ad esempio **Debug | iPhone**
3. Selezionare le caselle di controllo per i progetti da includere.

> [!NOTE]
> Grigio caselle vengono gestite automaticamente e non richiede alcuna modifica.

Screencast di questi passaggi: [http://screencast.com/t/zLoQOpEn](http://screencast.com/t/zLoQOpEn)
