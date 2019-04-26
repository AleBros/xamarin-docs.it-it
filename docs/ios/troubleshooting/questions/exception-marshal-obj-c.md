---
title: "Perché l'app iOS 9 restituisce l'errore: System.Exception: Failed to marshal the Objective-C object (System.Exception: marshalling oggetto Objective-C non riuscito)?"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: dfe1918d271946eb96d1f57d32c533a075f6d0bd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421957"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Perché l'app iOS 9 restituisce l'errore: System.Exception: Failed to marshal the Objective-C object (System.Exception: marshalling oggetto Objective-C non riuscito)?

Potrebbe essere visualizzato un errore di questo modulo:

> System.Exception: Non è riuscito a effettuare il marshalling dell'oggetto Objective-C... Impossibile trovare un'istanza gestita esistente per questo oggetto...

Modifiche apportate all'API in iOS 9 richiede l'utilizzo di un costruttore di callback quando la chiamata a codice non gestito, come l'API sottostante a questo punto si aspetta. Usare la riga seguente per aggiungere il costruttore di callback alla classe: 

`public foo (IntPtr handle) : base (handle) ` 

### <a name="next-steps"></a>Passaggi successivi

Per ulteriore assistenza, contattare il supporto tecnico o se il problema rimane anche dopo che usano le informazioni sopra riportate, vedi [le opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché come archiviare un nuovo bug se necessario. 
