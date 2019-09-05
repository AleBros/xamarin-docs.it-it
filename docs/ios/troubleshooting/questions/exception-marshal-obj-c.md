---
title: "Perché l'app iOS 9 restituisce l'errore: System.Exception: Failed to marshal the Objective-C object (System.Exception: marshalling oggetto Objective-C non riuscito)?"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 62a63dc5156d1acf9ad6ca15029978131c151726
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290477"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Perché l'app iOS 9 restituisce l'errore: System.Exception: Failed to marshal the Objective-C object (System.Exception: marshalling oggetto Objective-C non riuscito)?

È possibile che venga visualizzato un errore nel formato seguente:

> System.Exception: Non è stato possibile effettuare il marshalling dell'oggetto Objective-C... Impossibile trovare un'istanza gestita esistente per questo oggetto...

Le modifiche alle API in iOS 9 richiedono che venga usato un costruttore di callback quando si chiama il codice non gestito, perché l'API sottostante ora lo prevede. Utilizzare la riga seguente per aggiungere il costruttore di callback alla classe: 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>Fasi successive

Per ulteriore assistenza, per contattarci o se il problema persiste anche dopo aver utilizzato le informazioni sopra riportate, vedere [quali sono le opzioni di supporto disponibili per Novell?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché su come archiviare un nuovo bug se necessario . 
