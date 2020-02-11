---
title: Come si raccolgono gli stack di chiamate correnti del processo di Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 9ed79b2273758b8051a96169d4c9b53870de1fb1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013030"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>Come si raccolgono gli stack di chiamate correnti del processo di Visual Studio?

Quando l'interfaccia utente grafica si blocca (si blocca, blocca) in Visual Studio, una parte importante delle informazioni di diagnostica da raccogliere è il set di stack di chiamate di tutti i thread del processo di Visual Studio. Per salvare queste informazioni per un'istanza bloccata di Visual Studio, è possibile usare una seconda istanza di Visual Studio:

1. Avviare una seconda istanza di Visual Studio (una nuova finestra).

2. Chiudere eventuali soluzioni aperte nella nuova istanza di Visual Studio.

3. Selezionare **Debug > Connetti a processo**.

   ![](vs-callstack-images/image1.png "Select Debug > Attach to Process")

4. Selezionare l'istanza bloccata originale di `devenv.exe` dall'elenco dei **processi disponibili**.

5. Selezionare **Debug > Interrompi tutto**.

   ![](vs-callstack-images/image2.png "Select Debug > Break All")

6. Selezionare **Debug > Salva dump con nome**.

   ![](vs-callstack-images/image3.png "Select Debug > Save Dump As")

7. Modificare **Salva come tipo** in **minidump (\*. dmp)** . Questa operazione produrrà un file molto più piccolo del **minidump con heap**e l'heap non è in genere rilevante per la diagnosi dei blocchi.

   ![](vs-callstack-images/image4.png "This will produce a much smaller file than Minidump with Heap, and the heap is usually not relevant for diagnosing freezes")

8. Salvare il file dump. Se si invia il file online, è possibile comprimerlo per ridurne le dimensioni.
