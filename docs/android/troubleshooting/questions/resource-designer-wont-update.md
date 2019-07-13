---
title: Il file Android Resource.designer.cs non verrà aggiornato
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/19/2017
ms.openlocfilehash: 65f7c6d8a573501ffec4aa1b8eaf28ff1e6479e8
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864229"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Il file Android Resource.designer.cs non verrà aggiornato

> [!NOTE]
> Questo problema è stato risolto in Xamarin Studio 5.1.4 e versioni successive. Tuttavia, se il problema si verifica in Visual Studio per Mac, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.

Un bug nella versione 5.1 di xamarin Studio in precedenza danneggiato file con estensione csproj, completamente o parzialmente eliminando il codice xml nel file con estensione csproj. Questa situazione provocherebbe importante sistema (ad esempio, l'aggiornamento di Android Resource.designer.cs) di compilazione di parti di Android per avere esito negativo. Al momento della stesura di 5.1.4 stabile versione 15 luglio, questo bug è stato risolto; ma in molti casi è necessario ripristinare manualmente, come illustrato di seguito il file di progetto.


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Due possibili approcci per correggere il file di progetto

**Entrambi:**

1. Creare un nuovo progetto di applicazione xamarin. Android, è possibile impostare tutte le proprietà di progetto in modo che corrisponda il progetto precedente e aggiungere tutte le risorse, eseguire il file di origine e così via nel progetto.

   **OPPURE**

2. Creare una copia di backup di file con estensione csproj del progetto originale, quindi aprirlo in un editor di testo e aggiungere di nuovo gli elementi mancanti da un file con estensione csproj normalmente generato.

### <a name="if-this-does-not-solve-the-problem"></a>Se non viene risolto il problema

Dopo qualche esperimento con questi elementi, è possibile notare che dopo l'aggiunta nuovamente gli elementi e ricompilare il progetto, il file Resource.designer.cs aggiornerebbero, quindi potrebbe ancora essere necessario chiudere e riaprire la soluzione per ottenere il completamento del codice per riconoscere i nuovi tipi di contenuto in Resource.designer.cs. 
