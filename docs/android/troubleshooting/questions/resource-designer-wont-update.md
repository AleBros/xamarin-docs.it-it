---
title: Il file Android Resource.designer.cs non verrà aggiornato
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/19/2017
ms.openlocfilehash: 4683bbaa5aa48c7b5de5fb9a87a4cd3fbc0aeada
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019517"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Il file Android Resource.designer.cs non verrà aggiornato

> [!NOTE]
> Questo problema è stato risolto in Xamarin Studio 5.1.4 e versioni successive. Tuttavia, se il problema si verifica in Visual Studio per Mac, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output del log di compilazione completo.

Un bug in Xamarin.Studio 5,1 danneggiava in precedenza i file. csproj eliminando parzialmente o completamente il codice XML nel file con estensione csproj. In questo modo, le parti importanti del sistema di compilazione Android, ad esempio l'aggiornamento di Android Resource.designer.cs, avranno esito negativo. A partire dalla versione stabile di 5.1.4 il 15 luglio, questo bug è stato risolto. in molti casi, tuttavia, il file di progetto deve essere ripristinato manualmente, come descritto di seguito.

## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Due possibili approcci per la correzione del file di progetto

**Uno**

1. Creare un nuovo progetto di applicazione Xamarin.Android, impostare tutte le proprietà del progetto in modo che corrispondano al progetto precedente e aggiungere tutte le risorse, i file di origine e così via nel progetto.

   **OPPURE**

2. Eseguire una copia di backup del file con estensione csproj del progetto originale, aprirlo in un editor di testo e aggiungere di nuovo gli elementi mancanti da un file con estensione csproj generato in modo corretto.

### <a name="if-this-does-not-solve-the-problem"></a>Se il problema persiste,

Dopo aver sperimentato questi elementi, è possibile notare che dopo l'aggiunta degli elementi e la ricompilazione del progetto, il file Resource.designer.cs viene aggiornato, ma potrebbe essere comunque necessario chiudere e riaprire la soluzione per ottenere il completamento del codice da riconoscere nuovi tipi contenuti in Resource.designer.cs. 
