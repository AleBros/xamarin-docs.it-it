---
title: Ambiente
ms.topic: article
ms.prod: xamarin
ms.assetid: 9801644A-89BB-4491-AD28-7F3B97D2CD62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 7489c2fe38e8433811c5f298296baebacf1c0727
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
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

