---
title: Esempio reale usando un progetto Xcode
description: Questo documento descrive come usare un progetto Xcode come input diretto per Sharpie obiettivo, semplificando il processo di creazione di C# binding a codice Objective-C.
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: ccfc2f1760d8971e2d824cf65344fa2a5e158c12
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978361"
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

