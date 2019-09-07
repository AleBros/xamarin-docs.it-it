---
title: Attributi di verifica dell'obiettivo Sharpie
description: Questo documento descrive l'attributo [verify] generato da Objective Sharpie. L'attributo [verify] evidenzia gli sviluppatori in cui è necessario verificare manualmente l'output dell'obiettivo Sharpie.
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: conceptdev
ms.author: crdun
ms.date: 01/15/2016
ms.openlocfilehash: eb4a992255fa26be1e83f27f93755e0fd8bda0a4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765681"
---
# <a name="objective-sharpie-verify-attributes"></a>Attributi di verifica dell'obiettivo Sharpie

Spesso si noterà che i binding prodotti da Objective Sharpie verranno annotati con l' `[Verify]` attributo. Questi attributi indicano che è necessario _verificare_ che l'obiettivo Sharpie abbia fatto la corretta operazione confrontando l'associazione con la dichiarazione c/Objective-c originale (che verrà fornita in un commento sopra la dichiarazione associata).

La verifica è consigliata per _tutte le_ dichiarazioni associate, ma è probabilmente _necessaria_ per le dichiarazioni annotate `[Verify]` con l'attributo. Questo è dovuto al fatto che in molte situazioni i metadati non sono sufficienti nel codice sorgente nativo originale per dedurre il modo migliore per produrre un'associazione. Potrebbe essere necessario fare riferimento a documenti o commenti di codice all'interno dei file di intestazione per ottenere la migliore decisione di associazione.

Una volta verificato che il binding è corretto o che è stato corretto, _rimuovere_ l' `[Verify]` attributo dall'associazione.

> [!IMPORTANT]
> `[Verify]`gli attributi provocano C# intenzionalmente errori di compilazione in modo da forzare la verifica dell'associazione. È necessario rimuovere l' `[Verify]` attributo quando è stato esaminato (ed eventualmente corretto) il codice.

## <a name="verify-hints-reference"></a>Riferimento per la verifica degli hint

È possibile fare riferimento a un argomento hint fornito all'attributo con la documentazione seguente. La documentazione relativa a `[Verify]` tutti gli attributi prodotti verrà fornita sulla console anche dopo il completamento dell'associazione.

|`[Verify]`Suggerimento|Descrizione|
|---|---|
|InferredFromPreceedingTypedef|Il nome di questa dichiarazione è stato dedotto dalla convenzione comune di precedente `typedef` immediatamente nel codice sorgente nativo originale. Verificare che il nome dedotto sia corretto perché la convenzione è ambigua.|
|ConstantsInterfaceAssociation|Non esiste un modo sciocco per determinare con quale interfaccia Objective-C è possibile associare una dichiarazione di variabile extern. Le istanze di queste sono associate `[Field]` come proprietà in un'interfaccia parziale in un'interfaccia concreta quasi in modo da produrre un'API più intuitiva, eliminando probabilmente l'interfaccia "costanti".|
|MethodToProperty|Un metodo Objective-C è stato associato come C# proprietà a causa di una convenzione, ad esempio l'assenza di parametri e la restituzione di un valore (restituito non void). Spesso i metodi come questi devono essere associati come proprietà per la superficie di un'API più gradevole, ma talvolta possono verificarsi falsi positivi e l'associazione deve essere effettivamente un metodo.|
|StronglyTypedNSArray|Un oggetto `NSArray*` nativo è stato `NSObject[]`associato come. Potrebbe essere possibile digitare più fortemente la matrice nell'associazione in base alle aspettative impostate tramite la documentazione dell'API (ad esempio, i commenti nel file di intestazione) o esaminando il contenuto della matrice tramite test. Ad esempio, un NSArray * contenente solo NSNumber * instancescan viene associato come `NSNumber[]` `NSObject[]`anziché.|

È anche possibile ricevere rapidamente la documentazione per un suggerimento usando `sharpie verify-docs` lo strumento, ad esempio:

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```
