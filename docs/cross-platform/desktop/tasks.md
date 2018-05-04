---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Confronto tra le attività comuni
ms.technology: xamarin-crossplatform
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 84b3edc1a8cbc9ad642d94b457321786fae5fe70
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="common-tasks-comparison"></a>Confronto tra le attività comuni

| Attività | WPF | Xamarin.Forms |
|--- |--- |--- |
|Un messaggio vengono visualizzate sullo schermo con pulsanti|`MessageBox`|`Page.DisplayAlert`|
|Crea un timer|Classe `DispatcherTimer`|`Device.StartTimer` metodo statico|
|Ottenere una dimensione di tipo di carattere predefinito|`SystemFonts` classe statica|`Device.GetNamedSize` metodo statico|
|Aprire un URI/URL|`Process.Start`|`Device.OpenUri`|
|Visualizzare il foglio di azione (elenco dei pulsanti)|N/D|`Page.DisplayActionSheet`|
