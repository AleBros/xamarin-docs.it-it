---
title: In quale posizione sono archiviati i componenti nel computer?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: 7725dbff994ffcef9734ad07c6b506064d9c5b2b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285046"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>In quale posizione sono archiviati i componenti nel computer?

Ogni volta che si installa un componente Novell in un progetto di app, questo viene inserito in due posizioni:

1. In una cartella Components a livello di radice della cartella della soluzione. Se si rimuove il componente da tutti i progetti della soluzione, verrà rimosso anche da questa cartella.

2. Una copia viene inoltre archiviata nei percorsi seguenti:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Quindi, per rimuovere completamente un componente dal sistema, eliminarlo dai progetti o dalle soluzioni e dalla cartella della cache riportata sopra.
