---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Confronto tra le attività comuni
description: Questo documento viene illustrato come eseguire diverse attività comuni in WPF e xamarin. Forms. Analizza i pulsanti, timer, le dimensioni dei caratteri, apertura di un URI e la visualizzazione di un foglio di azione.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 2991e74ec59e3c43c665941706c2d9ac94bfb9ad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780476"
---
# <a name="common-tasks-comparison"></a>Confronto tra le attività comuni

| Attività | WPF | Xamarin.Forms |
|--- |--- |--- |
|Un messaggio vengono visualizzate sullo schermo con pulsanti|`MessageBox`|`Page.DisplayAlert`|
|Crea un timer|Classe `DispatcherTimer`|`Device.StartTimer` metodo statico|
|Ottenere una dimensione di tipo di carattere predefinito|`SystemFonts` classe statica|`Device.GetNamedSize` metodo statico|
|Aprire un URI/URL|`Process.Start`|`Device.OpenUri`|
|Visualizzare il foglio di azione (elenco dei pulsanti)|N/D|`Page.DisplayActionSheet`|
