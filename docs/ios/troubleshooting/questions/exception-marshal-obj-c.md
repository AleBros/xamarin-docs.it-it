---
title: "Il motivo per cui esegue l'app iOS 9 e il con: System. Exception: Impossibile effettuare il marshalling dell'oggetto Objective-C?"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: dfe1918d271946eb96d1f57d32c533a075f6d0bd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103361"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Il motivo per cui esegue l'app iOS 9 e il con: System. Exception: Impossibile effettuare il marshalling dell'oggetto Objective-C?

Potrebbe essere visualizzato un errore di questo modulo:

> System. Exception: Impossibile effettuare il marshalling dell'oggetto Objective-C... Impossibile trovare un'istanza gestita esistente per questo oggetto...

Modifiche apportate all'API in iOS 9 richiede l'utilizzo di un costruttore di callback quando la chiamata a codice non gestito, come l'API sottostante a questo punto si aspetta. Usare la riga seguente per aggiungere il costruttore di callback alla classe: 

`public foo (IntPtr handle) : base (handle) ` 

### <a name="next-steps"></a>Passaggi successivi

Per ulteriore assistenza, contattare il supporto tecnico o se il problema rimane anche dopo che usano le informazioni sopra riportate, vedi [le opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché come archiviare un nuovo bug se necessario. 
