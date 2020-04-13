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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019517"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Il file Android Resource.designer.cs non verrà aggiornato

> [!NOTE]
> Questo problema è stato risolto in Xamarin Studio 5.1.4 e versioni successive. Tuttavia, se il problema si verifica in Visual Studio per Mac, presentare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output completo del log di compilazione.

Un bug in Xamarin.Studio 5.1 danneggiato in precedenza i file csproj eliminando parzialmente o completamente il codice xml nel file con estensione csproj. Ciò causerebbe l'esito negativo di parti importanti del sistema di compilazione Android (ad esempio l'aggiornamento del Resource.designer.cs Android). A partire dalla versione stabile 5.1.4 del 15 luglio, questo bug è stato risolto; ma in molti casi il file di progetto deve essere riparato manualmente, come descritto di seguito.

## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Due possibili approcci per la correzione del file di progetto

**In entrambi i casi:**

1. Creare un nuovo progetto di applicazione Xamarin.Android, impostare tutte le proprietà del progetto in modo che corrispondano al progetto precedente e aggiungere tutte le risorse, i file di origine e così via nel progetto.

   **OR**

2. Creare una copia di backup del file con estensione csproj del progetto originale, quindi aprirlo in un editor di testo e aggiungere nuovamente gli elementi mancanti da un file con estensione csproj generato in modo pulito.

### <a name="if-this-does-not-solve-the-problem"></a>Se questo non risolve il problema

Dopo aver sperimentato con questi elementi, è possibile notare che dopo aver aggiunto nuovamente gli elementi e ricompilazione del progetto, il file di Resource.designer.cs verrebbe aggiornato, ma quindi potrebbe essere comunque necessario chiudere e riaprire la soluzione per ottenere il completamento del codice per riconoscere i nuovi tipi contenuti in Resource.designer.cs. 
