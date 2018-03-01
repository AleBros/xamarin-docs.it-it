---
title: "Perché non mia app iOS 9 avrà esito negativo con: System. Exception: Impossibile effettuare il marshalling dell'oggetto Objective-C?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: baba2526eefa1b69d47da47b73ea0bd417ecdc57
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Perché non mia app iOS 9 avrà esito negativo con: System. Exception: Impossibile effettuare il marshalling dell'oggetto Objective-C?

Viene visualizzato un errore di questo form:

> System. Exception: Impossibile effettuare il marshalling dell'oggetto Objective-C... Impossibile trovare un'istanza gestita esistente per questo oggetto...

Modifiche all'API di iOS 9 richiede l'utilizzo di un costruttore di callback quando la chiamata a codice non gestito, come l'API sottostante ora si aspetta. Per aggiungere il costruttore di callback alla classe, utilizzare la seguente riga: 

`public foo (IntPtr handle) : base (handle) ` 

### <a name="next-steps"></a>Passaggi successivi

Per ulteriore assistenza, contattare Microsoft, o se il problema rimane anche dopo l'utilizzo di queste informazioni, vedere [quali opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, i suggerimenti, nonché come Se necessario, archiviare un nuovo bug. 
