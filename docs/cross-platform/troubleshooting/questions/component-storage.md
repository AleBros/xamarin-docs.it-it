---
title: In cui sono archiviati i componenti nel computer?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: asb3993
ms.author: amburns
ms.openlocfilehash: a53045a6179a26b30d824976d11fd2769a84811e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919397"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>In cui sono archiviati i componenti nel computer?

Quando si installa un componente Xamarin in un progetto di App, si ottiene inserito in due posizioni:

1. In una cartella di componenti al livello radice della cartella della soluzione. Se si rimuove il componente da tutti i progetti nella soluzione, verranno ottenere rimossi da questa cartella.

2. È inoltre archiviata una copia nei percorsi seguenti:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Pertanto, per rimuovere completamente un componente dal sistema, eliminarlo da progetti/soluzioni e dalla cartella della cache.
