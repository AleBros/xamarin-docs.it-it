---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Confronto tra le attività comuni
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: b4d45ae61c5d7a7093d51c4440d11dd883c157a4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="common-tasks-comparison"></a>Confronto tra le attività comuni

| Attività | WPF | Xamarin.Forms |
|--- |--- |--- |
|Un messaggio vengono visualizzate sullo schermo con pulsanti|`MessageBox`|`Page.DisplayAlert`|
|Crea un timer|Classe `DispatcherTimer`|`Device.StartTimer` metodo statico|
|Ottenere una dimensione di tipo di carattere predefinito|`SystemFonts` classe statica|`Device.GetNamedSize` metodo statico|
|Aprire un URI/URL|`Process.Start`|`Device.OpenUri`|
|Visualizzare il foglio di azione (elenco dei pulsanti)|N/D|`Page.DisplayActionSheet`|
