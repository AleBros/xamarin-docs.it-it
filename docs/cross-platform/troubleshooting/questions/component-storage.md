---
title: In quale posizione sono archiviati i componenti nel computer?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 9447cf903c8789078e66082e720eeecfa7bb3e0d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014234"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>In quale posizione sono archiviati i componenti nel computer?

Ogni volta che si installa un componente Xamarin in un progetto di app, questo viene inserito in due posizioni:

1. In una cartella Components a livello di radice della cartella della soluzione. Se si rimuove il componente da tutti i progetti della soluzione, verrà rimosso anche da questa cartella.

2. Una copia viene inoltre archiviata nei percorsi seguenti:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Quindi, per rimuovere completamente un componente dal sistema, eliminarlo dai progetti o dalle soluzioni e dalla cartella della cache riportata sopra.
