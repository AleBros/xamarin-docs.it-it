---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Confronto tra le attività comuni
description: Questo documento vengono confrontati come eseguire varie attività comuni in WPF e xamarin. Forms. Analizza i pulsanti, i timer, le dimensioni dei caratteri, un URI di apertura e visualizzazione di un foglio di azione.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 2991e74ec59e3c43c665941706c2d9ac94bfb9ad
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269595"
---
# <a name="common-tasks-comparison"></a>Confronto tra le attività comuni

| Attività | WPF | Xamarin.Forms |
|--- |--- |--- |
|Visualizzare un messaggio sullo schermo con pulsanti|`MessageBox`|`Page.DisplayAlert`|
|Crea un timer|Classe `DispatcherTimer`|`Device.StartTimer` metodo statico|
|Ottenere una dimensione di tipo di carattere predefinito|`SystemFonts` classe statica|`Device.GetNamedSize` metodo statico|
|Aprire un URI/URL|`Process.Start`|`Device.OpenUri`|
|Visualizzare il foglio di azione (elenco di pulsanti)|N/D|`Page.DisplayActionSheet`|
