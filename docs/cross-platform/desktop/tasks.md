---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Confronto tra attività comuni
description: Questo documento illustra come eseguire diverse attività comuni in WPF e in Novell. Forms. Esamina i pulsanti, i timer, le dimensioni del carattere, l'apertura di un URI e la visualizzazione di un foglio di azione.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: d1f1430d8044f94c17a19d747334b5ed8a1441cf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016425"
---
# <a name="common-tasks-comparison"></a>Confronto tra attività comuni

| Attività | WPF | Xamarin.Forms |
|--- |--- |--- |
|Visualizza un messaggio sullo schermo con i pulsanti|`MessageBox`|`Page.DisplayAlert`|
|Creazione di un timer|Classe `DispatcherTimer`|Metodo statico `Device.StartTimer`|
|Ottenere le dimensioni del carattere predefinite|classe statica `SystemFonts`|Metodo statico `Device.GetNamedSize`|
|Apri URI/URL|`Process.Start`|`Device.OpenUri`|
|Visualizza foglio azioni (elenco di pulsanti)|N/D|`Page.DisplayActionSheet`|
