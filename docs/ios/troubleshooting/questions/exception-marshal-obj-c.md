---
title: "Perché l'app iOS 9 restituisce l'errore: System.Exception: Failed to marshal the Objective-C object (System.Exception: marshalling oggetto Objective-C non riuscito)?"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 3dbb4d9132d5d94e4533704730e95002b5aec0be
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832267"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Perché l'app iOS 9 restituisce l'errore: System.Exception: Failed to marshal the Objective-C object (System.Exception: marshalling oggetto Objective-C non riuscito)?

Potrebbe essere visualizzato un errore di questo modulo:

> System.Exception: Non è riuscito a effettuare il marshalling dell'oggetto Objective-C... Impossibile trovare un'istanza gestita esistente per questo oggetto...

Modifiche apportate all'API in iOS 9 richiede l'utilizzo di un costruttore di callback quando la chiamata a codice non gestito, come l'API sottostante a questo punto si aspetta. Usare la riga seguente per aggiungere il costruttore di callback alla classe: 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>Fasi successive

Per ulteriore assistenza, contattare il supporto tecnico o se il problema rimane anche dopo che usano le informazioni sopra riportate, vedi [le opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché come archiviare un nuovo bug se necessario. 
