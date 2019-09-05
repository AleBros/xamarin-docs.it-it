---
title: Esempio reale con un progetto Xcode
description: Questo documento descrive come usare un progetto Xcode come input diretto per Objective Sharpe, semplificando il processo di creazione C# dei binding nel codice Objective-C.
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: conceptdev
ms.author: crdun
ms.date: 01/15/2016
ms.openlocfilehash: e460994994c1383f29028be7b13cec216f2d12f7
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290656"
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
