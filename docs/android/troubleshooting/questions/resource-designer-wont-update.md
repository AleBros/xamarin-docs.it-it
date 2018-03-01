---
title: "Il file cs Android non verrà aggiornate."
ms.topic: article
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/19/2017
ms.openlocfilehash: b169bcc64af15de3d87bfb7f8059b4251f1a3ad9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Il file cs Android non verrà aggiornate.

> [!NOTE]
> **Nota:** questo problema è stato risolto in Xamarin Studio 5.1.4 e versioni successive. Tuttavia, se il problema si verifica in Visual Studio per Mac, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.

Un bug in 5.1 Xamarin.Studio danneggiato in precedenza i file con estensione csproj completamente o parzialmente, eliminando il codice xml nel file con estensione csproj. Si verificherebbe un importante parte di Android compilare il sistema (ad esempio, l'aggiornamento di Android cs) esito negativo. A partire dal 5.1.4 stabile rilasciare il 15 luglio, il bug è stato risolto; ma in molti casi è necessario ripristinare manualmente, come illustrato di seguito il file di progetto.


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Due approcci per la correzione di file di progetto

### <a name="either"></a>È possibile procedere in due modi:

1) Creare un nuovo progetto di applicazione di xamarin, è possibile impostare tutte le proprietà di progetto in modo che corrisponda il progetto precedente e aggiungere tutte le risorse, eseguire il file di origine e così via nel progetto.

### <a name="or"></a>OR

2) Creare una copia di backup di file con estensione csproj del progetto originale, quindi aprirlo in un editor di testo e aggiungere nuovamente gli elementi mancanti da un file con estensione csproj generato correttamente.

### <a name="if-this-does-not-solve-the-problem"></a>Se non viene risolto il problema

Dopo aver sperimentato con questi elementi, è possibile notare che dopo aggiungere nuovamente gli elementi e ricompilare il progetto, aggiornare il file cs, ma quindi ancora potrebbe essere necessario chiudere e riaprire la soluzione per ottenere il completamento del codice per riconoscere i nuovi tipi di contenuto in cs. 
