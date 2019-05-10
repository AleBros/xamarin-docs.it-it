---
title: ApiDefinitions e StructsAndEnums file
description: Questo documento descrive i file ApiDefinitions.cs e StructsAndEnums.cs Sharpie descrive come viene generato l'errore. Questi file vengono quindi usati per accedere al codice Objective-C da C#.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 3b991f6105c6053f473b049d195aaef63cbcdd57
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977671"
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions e StructsAndEnums file

Quando l'obiettivo Sharpie è stata eseguita correttamente, viene generato `Binding/ApiDefinitions.cs` e `Binding/StructsAndEnums.cs` file.
Questi due file vengono aggiunti a un progetto di associazione in Visual Studio per Mac o passati direttamente al `btouch` o `bmac` strumenti per produrre l'associazione finale.

Nelle *alcuni* casi potrebbero essere sufficiente, i file generati ma più spesso lo sviluppatore avrà bisogno di modificare manualmente questi file per risolvere eventuali problemi che non può essere gestite automaticamente dallo strumento (ad esempio con i flag generati con un [ `Verify` attributo](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Alcuni dei passaggi successivi:

- **Modificare i nomi**: In alcuni casi è opportuno modificare i nomi dei metodi e classi in modo che corrispondano linee guida di progettazione di .NET Framework.
- **Metodi o proprietà**: Approccio euristico utilizzato dall'obiettivo Sharpie talvolta sceglierà un metodo può essere trasformato in una proprietà. A questo punto, è possibile decidere se questo è il comportamento previsto o meno.
- **Associare gli eventi**: È possibile collegare le classi con le classi di delegati e automaticamente generare eventi per quelle.
- **Associare le notifiche**: Non è possibile estrarre il contratto API delle notifiche dai file di intestazione pura, questa operazione richiede una corsa alla documentazione dell'API. Se si desidera che le notifiche fortemente tipizzate, è necessario aggiornare il risultato.
- **Cura dell'API**: A questo punto, è possibile scegliere di fornire costruttori aggiuntivi, aggiungere i metodi (per consentire C# initialize-sul-costruzione sintassi), operatore di overload e implementare interfacce personalizzate per il file di definizioni aggiuntive.

Vedere le [associazione di un'API](~/cross-platform/macios/binding/objective-c-libraries.md) descrizione da visualizzare come questi file si inserisce nel processo di associazione, come illustrato nel diagramma seguente:

![](apidefinitions-structsandenums-images/binding-flowchart.png "Nella figura seguente viene illustrato il processo di associazione")

Vedere la [riferimento ai tipi di associazione](~/cross-platform/macios/binding/binding-types-reference.md) per altre informazioni sul contenuto di questi file.

