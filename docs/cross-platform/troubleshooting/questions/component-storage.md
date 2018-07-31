---
title: Dove vengono archiviati i componenti sul mio computer?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 4152c8ef7eeba3748d9244e27e48f3f9a2c0019b
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350719"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>Dove vengono archiviati i componenti sul mio computer?

Quando si installa un componente Xamarin in un progetto di App, si ottiene inserito in due posizioni:

1. In una cartella di componenti al livello radice della cartella della soluzione. Se si rimuove il componente da tutti i progetti nella soluzione, si verranno rimossi da questa cartella anche.

2. Una copia viene archiviata anche nei percorsi seguenti:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Pertanto, per rimuovere completamente un componente dal sistema, eliminarlo da soluzioni/progetti e dalla cartella della cache precedente.
