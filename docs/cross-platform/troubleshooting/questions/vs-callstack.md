---
title: In che modo è possibile raccogliere gli stack di chiamate corrente del processo di Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: asb3993
ms.author: amburns
ms.date: 03/30/2017
ms.openlocfilehash: e81c28f0610a0df2e4fe06349685ef5e0744071a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>In che modo è possibile raccogliere gli stack di chiamate corrente del processo di Visual Studio?

Quando l'interfaccia utente grafica Blocca (si blocca, blocco) in Visual Studio, una parte importante delle informazioni di diagnostica da raccogliere è il set degli stack di chiamate da tutti i thread del processo di Visual Studio. Per salvare queste informazioni per un'istanza bloccata di Visual Studio, è possibile utilizzare una seconda istanza di Visual Studio:

1. Avviare una seconda istanza (una nuova finestra) di Visual Studio.

2. Chiudere eventuali soluzioni aperte nella nuova istanza di Visual Studio.

3. Selezionare **Debug > Connetti a processo**.

  ![](vs-callstack-images/image1.png "Selezionare Debug > Connetti a processo")

4. Selezionare l'istanza bloccato originale di `devenv.exe` dall'elenco dei **processi disponibili**.

5. Selezionare **Debug > Interrompi tutto**.

  ![](vs-callstack-images/image2.png "Selezionare Debug > Interrompi tutto")

6. Selezionare **Debug > Salva Dump con nome**.

  ![](vs-callstack-images/image3.png "Selezionare Debug > Salva Dump con nome")

7. Change **Salva come tipo** alla **Minidump (\*dmp)**. Questa sintassi produrrà un file molto più piccolo rispetto a **Minidump con Heap**, e l'heap non è in genere rilevante per la diagnosi di blocco.

  ![](vs-callstack-images/image4.png "Questa sintassi produrrà un file molto più piccolo rispetto a Minidump con Heap e l'heap in genere non è pertinente per la diagnosi di blocco")

8. Salvare il file di dump. Se l'invio di file online, è possibile comprimere in modo da ridurre le dimensioni.
