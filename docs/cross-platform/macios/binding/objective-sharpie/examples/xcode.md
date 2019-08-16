---
title: Esempio reale con un progetto Xcode
description: Questo documento descrive come usare un progetto Xcode come input diretto per Objective Sharpe, semplificando il processo di creazione C# dei binding nel codice Objective-C.
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 083bebd093a8db92b0e64ba11d13bd32da88f604
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521938"
---
# <a name="real-world-example-using-an-xcode-project"></a>Esempio reale con un progetto Xcode

**Questo esempio usa la [libreria pop di Facebook](https://github.com/facebook/pop).**

Novità della versione 3,0, Objective Sharpie supporta i progetti Xcode come input. Questi progetti specificano i file di intestazione e i flag del compilatore corretti necessari per compilare la libreria nativa, quindi è necessario associarlo. L'obiettivo Sharpie selezionerà la prima _destinazione_ e la relativa configurazione predefinita di un progetto, se non viene richiesto di eseguire altre operazioni.

Prima che l'obiettivo Sharpie tenti di analizzare i file di progetto e di intestazione, è necessario compilarlo. I progetti hanno spesso fasi di compilazione che strutturano correttamente i file di intestazione per l'uso e l'integrazione esterna, pertanto è preferibile compilare sempre il progetto completo prima di provare a associarlo.

```
$ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   (more git clone output)

$ cd pop
$ sharpie bind pop.xcodeproj -sdk iphoneos9.0
```
