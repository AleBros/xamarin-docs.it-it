---
title: "Perché l'app iOS 9 ha esito negativo con: System. Exception: non è stato possibile effettuare il marshalling dell'oggetto Objective-C?"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 1bb67eaa884e523e96ef1015daaa6b959ea1512d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031098"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Perché l'app iOS 9 ha esito negativo con: System. Exception: non è stato possibile effettuare il marshalling dell'oggetto Objective-C?

È possibile che venga visualizzato un errore nel formato seguente:

> System. Exception: non è stato possibile effettuare il marshalling dell'oggetto Objective-C... Impossibile trovare un'istanza gestita esistente per questo oggetto...

Le modifiche alle API in iOS 9 richiedono che venga usato un costruttore di callback quando si chiama il codice non gestito, perché l'API sottostante ora lo prevede. Utilizzare la riga seguente per aggiungere il costruttore di callback alla classe: 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>Passaggi successivi

Per ulteriore assistenza, per contattarci o se il problema persiste anche dopo aver utilizzato le informazioni sopra riportate, vedere [quali sono le opzioni di supporto disponibili per Novell?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché su come archiviare un nuovo bug se necessario . 
