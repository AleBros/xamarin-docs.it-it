---
title: Obiettivo Sharpie verificare gli attributi
description: Questo documento descrive l'attributo [Verify] generato da Sharpie obiettivo. L'attributo [Verify] evidenzia per gli sviluppatori in cui è necessario verificare manualmente output dell'obiettivo Sharpie.
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 96e5bafc14c2d3aba03ccc137151a83ee8afeef9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780711"
---
# <a name="objective-sharpie-verify-attributes"></a>Obiettivo Sharpie verificare gli attributi

È spesso che associazioni prodotte dall'obiettivo Sharpie verranno annotate con il `[Verify]` attributo. Questi attributi indicano che è necessario _verificare_ che Sharpie obiettivo è la cosa corretta confrontando l'associazione con la dichiarazione originale di C/Objective-C (che verrà fornita in un commento sopra la dichiarazione di associazione).

Verifica è consigliata per _tutti_ associato dichiarazioni, ma è più probabile _obbligatorio_ per le dichiarazioni annotate con il `[Verify]` attributo. Infatti, in molti casi, non c'è sufficiente metadati nel codice sorgente nativo originale per dedurre come produrre più di un'associazione. Potrebbe essere necessario fare riferimento a documentazione o commenti del codice all'interno dei file di intestazione per prendere la decisione migliore di associazione.

Dopo avere verificato che il binding è correggere o correzione in modo che sia corretto, _rimuovere_ il `[Verify]` attributo dell'associazione.

> [!IMPORTANT]
> `[Verify]` attributi causano intenzionalmente errori di compilazione c# in modo che è necessario verificare l'associazione. È necessario rimuovere il `[Verify]` attributo dopo avere esaminato (e possibilmente corretti) il codice.

## <a name="verify-hints-reference"></a>Verificare il riferimento a hint

L'argomento hint fornito all'attributo può essere incrociato a cui fa riferimento alla documentazione riportata di seguito. Documentazione per qualsiasi prodotto `[Verify]` attributi verranno forniti nella console anche dopo l'associazione è stata completata.

|`[Verify]` Hint|Descrizione|
|---|---|
|InferredFromPreceedingTypedef|Il nome di questa dichiarazione è stata dedotta per convenzione comune dall'immediatamente precedente `typedef` nel codice sorgente nativo originale. Verificare che il nome derivato sia corretto di questa convenzione è ambigua.|
|ConstantsInterfaceAssociation|Non è piuttosto complesso per determinare con quale interfaccia Objective-C può essere associata una dichiarazione di variabile extern. Le istanze di questi elementi vengono associate come `[Field]` proprietà in un'interfaccia parziale in un'interfaccia concreta quasi by per produrre un'API intuitiva, probabilmente eliminando 'Costanti' interfaccia completamente.|
|MethodToProperty|Un metodo Objective-C associato come una proprietà c# a causa di convenzione, ad esempio che non accettano parametri e restituisce un valore (restituito non void). Spesso i metodi come questi devono essere associati come proprietà per esporre un'API coloro, ma in alcuni casi in cui possono verificarsi falsi positivi e l'associazione deve essere effettivamente un metodo.|
|StronglyTypedNSArray|Nativo `NSArray*` associati come `NSObject[]`. È possibile digitare più fortemente matrice nell'associazione in base alle aspettative impostato tramite la documentazione dell'API (ad esempio, i commenti nel file di intestazione) o esaminando il contenuto della matrice tramite il test. Ad esempio, un NSArray * contenente solo NSNumber * instancescan associati come `NSNumber[]` anziché `NSObject[]`.|

È possibile ricevere rapidamente la documentazione per l'utilizzo di hint di `sharpie verify-docs` strumento, ad esempio:

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```

