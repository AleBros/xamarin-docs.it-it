---
title: Motivo per cui Visual Studio non include il progetto libreria di cui viene fatto riferimento nelle compilazioni?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: cb9b3689ab6a12d99f9694583cd0fd50a6f5c72c
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>Motivo per cui Visual Studio non include il progetto libreria di cui viene fatto riferimento nelle compilazioni

Visual Studio Usa il **Configuration Manager** per determinare quali progetti in una soluzione vengono inclusi automaticamente in una determinata configurazione di compilazione o distribuzione.

Alcuni modelli generati con un progetto libreria di riferimento dispone già della libreria di cui viene fatto riferimento inclusa nella configurazione. ma in caso contrario dovrà essere eseguita manualmente.

## <a name="how-to-use-the-configuration-manager"></a>Come utilizzare Gestione configurazione

1. Apri **compilare > Configuration Manager**
2. Selezionare la configurazione da personalizzare, ad esempio **Debug | iPhone**
3. Selezionare le caselle di controllo per i progetti che si desidera includere.

> [!NOTE]
> Grigio finestre vengono gestiti automaticamente e non devono essere tutte le modifiche.

Screencast di questi passaggi: [http://screencast.com/t/zLoQOpEn](http://screencast.com/t/zLoQOpEn)
