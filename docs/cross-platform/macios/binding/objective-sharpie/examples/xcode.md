---
title: Esempio reale usando un progetto Xcode
description: Questo documento descrive come usare un progetto Xcode come input diretto per Sharpie obiettivo, semplificando il processo di creazione di C# binding a codice Objective-C.
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 05c55dc7cd20de2d216d1f267ea5a73631748a0a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61265257"
---
# <a name="real-world-example-using-an-xcode-project"></a>Esempio reale usando un progetto Xcode

**Questo esempio Usa la [libreria POP proveniente da Facebook](https://github.com/facebook/pop).**

Nella versione 3.0, nuovo obiettivo Sharpie supporta progetti Xcode come input. Questi progetti specificano i file di intestazione corretti e flag del compilatore necessari per compilare la libreria nativa e in questo modo, è necessario associarlo troppo. Obiettivo Sharpie selezionerà il primo _destinazione_ e la relativa configurazione predefinita di un progetto se non richiesto per procedere diversamente.

Prima di obiettivo Sharpie tenta di analizzare i file di progetto e dell'intestazione, è necessario compilare la soluzione. I progetti hanno spesso le fasi di compilazione che verranno strutturare correttamente i file di intestazione per l'utilizzo esterno e l'integrazione, quindi è consigliabile venga sempre compilato il progetto completo prima di provare a eseguirne l'associazione.

<pre>$ <b>git clone https://github.com/facebook/pop.git</b>
Cloning into 'pop'...
   <em>(more git clone output)</em>

$ <b>cd pop</b>
$ <b>sharpie bind pop.xcodeproj -sdk iphoneos9.0</b></pre>

## <a name="related-links"></a>Collegamenti correlati

- [Corsi di Xamarin University: Compila una libreria di binding Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Corsi di Xamarin University: Creazione di una libreria di binding Objective-C con Sharpie obiettivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)