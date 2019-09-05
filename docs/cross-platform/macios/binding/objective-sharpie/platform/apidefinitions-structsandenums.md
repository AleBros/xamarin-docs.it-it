---
title: File ApiDefinitions & StructsAndEnums
description: Questo documento descrive i file ApiDefinitions.cs e StructsAndEnums.cs generati da Objective Sharpie. Questi file vengono quindi usati per accedere al codice Objective-C da C#.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 950f9149744cb8aa2abaed60ccefb416405ab110
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290785"
---
# <a name="apidefinitions--structsandenums-files"></a>File ApiDefinitions & StructsAndEnums

Quando Objective è stato eseguito correttamente, genera `Binding/ApiDefinitions.cs` file e. `Binding/StructsAndEnums.cs`
Questi due file vengono aggiunti a un progetto di binding in Visual Studio per Mac o passati direttamente agli `btouch` strumenti `bmac` o per produrre l'associazione finale.

In *alcuni* casi, questi file generati potrebbero essere tutti necessari, ma più spesso lo sviluppatore dovrà modificare manualmente i file generati per risolvere eventuali problemi che non possono essere gestiti automaticamente dallo strumento (ad esempio quelli contrassegnati con un [ `Verify`attributo](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Alcuni dei passaggi successivi includono:

- **Regolazione dei nomi**: In alcuni casi è opportuno modificare i nomi dei metodi e delle classi in modo che corrispondano alle linee guida di progettazione .NET Framework.
- **Metodi o proprietà**: L'euristica usata da Objective Sharpe talvolta sceglierà un metodo da trasformare in una proprietà. A questo punto, è possibile decidere se questo è il comportamento previsto.
- **Associare eventi**: È possibile collegare le classi alle classi delegate e generare automaticamente gli eventi per tali classi.
- **Associare le notifiche**: Non è possibile estrarre il contratto API delle notifiche dai file di intestazione puri. questa operazione richiederà un viaggio alla documentazione dell'API. Se si desiderano notifiche fortemente tipizzate, sarà necessario aggiornare il risultato.
- **Cura API**: A questo punto, è possibile scegliere di fornire costruttori aggiuntivi, aggiungere metodi (per consentire la sintassi C# di inizializzazione per costruzione), eseguire l'overload degli operatori e implementare interfacce personalizzate sul file di definizioni aggiuntive.

Vedere la descrizione dell' [associazione di un'API](~/cross-platform/macios/binding/objective-c-libraries.md) per vedere come questi file rientrano nel processo di binding, come illustrato nel diagramma seguente:

![](apidefinitions-structsandenums-images/binding-flowchart.png "Il processo di associazione viene illustrato in questo diagramma")

Per ulteriori informazioni sul contenuto di questi file, vedere il [riferimento ai tipi di binding](~/cross-platform/macios/binding/binding-types-reference.md) .

