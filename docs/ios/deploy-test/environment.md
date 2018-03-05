---
title: Ambiente
ms.topic: article
ms.prod: xamarin
ms.assetid: 67BFD4E1-276C-4B9F-9BD8-A5218D2BD529
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ef5cfa2a9eee0d5d01922e5b7b3f89a209396c56
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="environment"></a>Ambiente

L'*ambiente di esecuzione* è il set di variabili di ambiente che influenzano l'esecuzione del programma. Le variabile di ambiente possono essere impostate temporaneamente nelle proprietà del progetto oppure definitivamente specificando argomenti aggiuntivi per lo strumento di creazione dei pacchetti mtouch.

## <a name="temporary-environment-variables"></a>Variabili di ambiente temporanee

Le variabili di ambiente temporanee vengono impostate nella finestra **Proprietà**/**Opzioni** del progetto nella sezione **Esegui > Generale**. Queste variabili di ambiente sono attive solo quando l'applicazione viene avviata usando Visual Studio per Mac. Se l'app viene avviata manualmente toccandola, queste variabili di ambiente non vengono impostate.

## <a name="permanent-environment-variables"></a>Variabili di ambiente permanenti

Le variabili di ambiente permanenti vengono impostate specificando argomenti aggiuntivi per lo strumento di creazione dei pacchetti mtouch. Queste variabili di ambiente vengono compilate nel file eseguibile e verranno impostate anche se l'app non viene avviata da Xamarin Studio.

## <a name="example"></a>Esempio

```csharp
# log all exceptions to the device log
--setenv:MONO_TRACE=E:all

# to pass multipe environment variables, use --setenv multiple times
--setenv:MONO_TRACE=E:all --setenv:GC_DONT_GC=1
```

