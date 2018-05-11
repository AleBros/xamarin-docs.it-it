---
title: Esempio reale utilizzando un progetto Xcode
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 4e30b07c10de439df8e1ec1706845150e8c54a41
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="real-world-example-using-an-xcode-project"></a>Esempio reale utilizzando un progetto Xcode


**Questo esempio viene utilizzato il [libreria POP da Facebook](https://github.com/facebook/pop).**

Nella versione 3.0, obiettivo Sharpie supporta nuovi progetti Xcode come input. Questi progetti specificano i file di intestazione corretti e i flag del compilatore necessarie per compilare la libreria nativa e pertanto è necessario associarlo troppo. Obiettivo Sharpie selezionerà il primo _destinazione_ e la configurazione predefinita di un progetto, se non richiesto per procedere diversamente.

Prima di obiettivo Sharpie tenta di analizzare i file di progetto e di intestazione, è necessario compilarlo. I progetti presentano spesso le fasi di compilazione che verranno correttamente alla struttura dei file di intestazione per l'utilizzo esterno e l'integrazione, pertanto è consigliabile creare sempre l'intero progetto prima di eseguirne l'associazione.

<pre>$ <b>git clone https://github.com/facebook/pop.git</b>
Cloning into 'pop'...
   <em>(more git clone output)</em>

$ <b>cd pop</b>
$ <b>sharpie bind pop.xcodeproj -sdk iphoneos9.0</b></pre>

