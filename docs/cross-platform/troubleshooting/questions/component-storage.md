---
title: In cui sono archiviati i componenti nel computer?
ms.topic: article
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 9549363d7aeb5ff7a5cfa79eb38443aaa80b7019
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="where-are-the-components-stored-on-my-machine"></a>In cui sono archiviati i componenti nel computer?

Quando si installa un componente Xamarin in un progetto di App, si ottiene inserito in due posizioni:

1. In una cartella di componenti al livello radice della cartella della soluzione. Se si rimuove il componente da tutti i progetti nella soluzione, verranno ottenere rimossi da questa cartella.

2. È inoltre archiviata una copia nei percorsi seguenti:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Pertanto, per rimuovere completamente un componente dal sistema, eliminarlo da progetti/soluzioni e dalla cartella della cache.
