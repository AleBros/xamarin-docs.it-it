---
title: Come si raccolgono gli stack di chiamate correnti del processo di Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: asb3993
ms.author: amburns
ms.date: 03/30/2017
ms.openlocfilehash: e81c28f0610a0df2e4fe06349685ef5e0744071a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61159162"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>Come si raccolgono gli stack di chiamate correnti del processo di Visual Studio?

Quando l'interfaccia utente grafica Blocca ad eventuali blocchi, si blocca, in Visual Studio, una parte importante delle informazioni di diagnostica da raccogliere è il set degli stack di chiamate da tutti i thread del processo di Visual Studio. Per salvare queste informazioni per un'istanza bloccata di Visual Studio, è possibile usare una seconda istanza di Visual Studio:

1. Avviare una seconda istanza (una nuova finestra) di Visual Studio.

2. Chiudere eventuali soluzioni aperte nella nuova istanza di Visual Studio.

3. Selezionare **Debug > Connetti a processo**.

  ![](vs-callstack-images/image1.png "Scegliere Debug > Connetti a processo")

4. Selezionare l'istanza bloccata originale di `devenv.exe` dall'elenco dei **processi disponibili**.

5. Selezionare **Debug > Interrompi tutto**.

  ![](vs-callstack-images/image2.png "Scegliere Debug > Interrompi tutto")

6. Selezionare **Debug > Salva Dump con nome**.

  ![](vs-callstack-images/image3.png "Scegliere Debug > Salva Dump con nome")

7. Change **Salva come tipo** al **Minidump (\*dmp)**. Questo modo verrà generato un file molto più piccolo rispetto **Minidump con Heap**, e l'heap non è in genere rilevante per la diagnosi di si blocca.

  ![](vs-callstack-images/image4.png "Questo modo verrà generato un file molto più piccolo rispetto a Minidump con Heap e l'heap non è in genere rilevante per la diagnosi di si blocca")

8. Salvare il file di dump. Se inviare il file online, è possibile comprimere in modo da ridurre le dimensioni.
