---
title: File ApiDefinitions & StructsAndEnums
description: Questo documento descrive i file ApiDefinitions.cs e StructsAndEnums.cs generati da Objective Sharpie. Questi file vengono quindi usati per accedere al codice Objective-C da C#.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 870733554f3f69b7a0cd9b35c1b89e24c62b264d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016206"
---
# <a name="apidefinitions--structsandenums-files"></a>File ApiDefinitions & StructsAndEnums

Quando Objective è stato eseguito correttamente, genera `Binding/ApiDefinitions.cs` e `Binding/StructsAndEnums.cs` file.
Questi due file vengono aggiunti a un progetto di binding in Visual Studio per Mac o passati direttamente agli strumenti `btouch` o `bmac` per produrre l'associazione finale.

In *alcuni* casi, questi file generati potrebbero essere tutti necessari, ma più spesso lo sviluppatore dovrà modificare manualmente i file generati per risolvere eventuali problemi che non possono essere gestiti automaticamente dallo strumento (ad esempio quelli contrassegnati con un [`Verify` attributo](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Alcuni dei passaggi successivi includono:

- **Regolazione dei nomi**: a volte è opportuno modificare i nomi dei metodi e delle classi in modo che corrispondano alle linee guida di progettazione .NET Framework.
- **Metodi o proprietà**: l'euristica usata da Objective Sharpy a volte sceglierà un metodo da trasformare in una proprietà. A questo punto, è possibile decidere se questo è il comportamento previsto.
- **Associare eventi**: è possibile collegare le classi con le classi delegate e generare automaticamente gli eventi per tali classi.
- **Associare le notifiche**: non è possibile estrarre il contratto API delle notifiche dai file di intestazione puri. questa operazione richiederà un viaggio alla documentazione dell'API. Se si desiderano notifiche fortemente tipizzate, sarà necessario aggiornare il risultato.
- **Cura dell'API**: a questo punto, è possibile scegliere di fornire costruttori aggiuntivi, aggiungere metodi (per consentire la sintassi C# di inizializzazione della costruzione), eseguire l'overload degli operatori e implementare interfacce personalizzate nel file delle definizioni aggiuntive.

Vedere la descrizione dell' [associazione di un'API](~/cross-platform/macios/binding/objective-c-libraries.md) per vedere come questi file rientrano nel processo di binding, come illustrato nel diagramma seguente:

![](apidefinitions-structsandenums-images/binding-flowchart.png "The binding process is shown in this diagram")

Per ulteriori informazioni sul contenuto di questi file, vedere il [riferimento ai tipi di binding](~/cross-platform/macios/binding/binding-types-reference.md) .
