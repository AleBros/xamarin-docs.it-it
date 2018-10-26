---
title: Il file Android Resource.designer.cs non verrà aggiornato.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/19/2017
ms.openlocfilehash: ba3c2b07e7f35bf9fd84d10b74d034a02ca6a73d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106416"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Il file Android Resource.designer.cs non verrà aggiornato.

> [!NOTE]
> Questo problema è stato risolto in Xamarin Studio 5.1.4 e versioni successive. Tuttavia, se il problema si verifica in Visual Studio per Mac, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.

Un bug nella versione 5.1 di xamarin Studio in precedenza danneggiato file con estensione csproj, completamente o parzialmente eliminando il codice xml nel file con estensione csproj. Questa situazione provocherebbe importante sistema (ad esempio, l'aggiornamento di Android Resource.designer.cs) di compilazione di parti di Android per avere esito negativo. Al momento della stesura di 5.1.4 stabile versione 15 luglio, questo bug è stato risolto; ma in molti casi è necessario ripristinare manualmente, come illustrato di seguito il file di progetto.


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Due possibili approcci per correggere il file di progetto

### <a name="either"></a>È possibile procedere in due modi:

1) Creare un nuovo progetto di applicazione xamarin. Android, è possibile impostare tutte le proprietà di progetto in modo che corrisponda il progetto precedente e aggiungere tutte le risorse, eseguire il file di origine e così via nel progetto.

### <a name="or"></a>OR

2) Creare una copia di backup di file con estensione csproj del progetto originale, quindi aprirlo in un editor di testo e aggiungere di nuovo gli elementi mancanti da un file con estensione csproj normalmente generato.

### <a name="if-this-does-not-solve-the-problem"></a>Se non viene risolto il problema

Dopo qualche esperimento con questi elementi, è possibile notare che dopo l'aggiunta nuovamente gli elementi e ricompilare il progetto, il file Resource.designer.cs aggiornerebbero, quindi potrebbe ancora essere necessario chiudere e riaprire la soluzione per ottenere il completamento del codice per riconoscere i nuovi tipi di contenuto in Resource.designer.cs. 
