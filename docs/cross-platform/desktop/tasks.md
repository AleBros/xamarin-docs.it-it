---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Confronto tra attività comuni
description: Questo documento illustra come eseguire diverse attività comuni in WPF e in Novell. Forms. Esamina i pulsanti, i timer, le dimensioni del carattere, l'apertura di un URI e la visualizzazione di un foglio di azione.
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: fecd8ed774adbacf69e3b2db514a4698e71711d8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290323"
---
# <a name="common-tasks-comparison"></a>Confronto tra attività comuni

| Attività | WPF | Xamarin.Forms |
|--- |--- |--- |
|Visualizza un messaggio sullo schermo con i pulsanti|`MessageBox`|`Page.DisplayAlert`|
|Creazione di un timer|Classe `DispatcherTimer`|`Device.StartTimer`Metodo statico|
|Ottenere le dimensioni del carattere predefinite|`SystemFonts`classe statica|`Device.GetNamedSize`Metodo statico|
|Apri URI/URL|`Process.Start`|`Device.OpenUri`|
|Visualizza foglio azioni (elenco di pulsanti)|n/d|`Page.DisplayActionSheet`|
