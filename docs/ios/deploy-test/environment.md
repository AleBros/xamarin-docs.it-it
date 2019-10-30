---
title: Ambiente di esecuzione per le app Xamarin.iOS
description: Questo documento descrive come configurare le variabili di ambiente temporanee e permanenti per un'app Xamarin.iOS. Queste variabili possono essere specificate nelle proprietà di un progetto o come argomenti aggiuntivi per lo strumento di creazione dei pacchetti mtouch.
ms.prod: xamarin
ms.assetid: 9801644A-89BB-4491-AD28-7F3B97D2CD62
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: b2452c50fa978f3cabb718afe7330bf0fb701ce3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030236"
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
