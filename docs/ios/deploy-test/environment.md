---
title: Ambiente di esecuzione per le app Xamarin.iOS
description: Questo documento descrive come configurare le variabili di ambiente temporanee e permanenti per un'app Xamarin.iOS. Queste variabili possono essere specificate nelle proprietà di un progetto o come argomenti aggiuntivi per lo strumento di creazione dei pacchetti mtouch.
ms.prod: xamarin
ms.assetid: 9801644A-89BB-4491-AD28-7F3B97D2CD62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/05/2017
ms.openlocfilehash: 5296f03cae28e1025c760004c520a2b415ec493d
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351584"
---
# <a name="execution-environment-for-xamarinios-apps"></a>Ambiente di esecuzione per le app Xamarin.iOS

L'*ambiente di esecuzione* è il set di variabili di ambiente che influenzano l'esecuzione del programma. Le variabile di ambiente possono essere impostate temporaneamente nelle proprietà del progetto oppure definitivamente specificando argomenti aggiuntivi per lo strumento di creazione dei pacchetti mtouch.

## <a name="temporary-environment-variables"></a>Variabili di ambiente temporanee

Le variabili di ambiente temporanee vengono impostate nella finestra **Proprietà**/**Opzioni** del progetto nella sezione **Esegui > Generale**. Queste variabili di ambiente sono attive solo quando l'applicazione viene avviata usando Visual Studio per Mac. Se l'app viene avviata manualmente toccandola, queste variabili di ambiente non vengono impostate.

## <a name="permanent-environment-variables"></a>Variabili di ambiente permanenti

Le variabili di ambiente permanenti vengono impostate specificando argomenti aggiuntivi per lo strumento di creazione dei pacchetti mtouch. Queste variabili di ambiente vengono compilate nel file eseguibile e verranno impostate anche se l'app non viene avviata da Visual Studio per Mac.

## <a name="example"></a>Esempio

```csharp
# log all exceptions to the device log
--setenv:MONO_TRACE=E:all

# to pass multipe environment variables, use --setenv multiple times
--setenv:MONO_TRACE=E:all --setenv:GC_DONT_GC=1
```

