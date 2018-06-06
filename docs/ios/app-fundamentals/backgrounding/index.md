---
title: Backgrounding in xamarin. IOS
description: L'elaborazione o backgrounding di sfondo è il processo di consentendo alle applicazioni di eseguire le attività in background mentre un'altra applicazione è in esecuzione in primo piano. Questa guida viene utilizzato come un'introduzione all'elaborazione in iOS in background.
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b22f3ef3276129f7f46c23cc1d06666f151f5ac4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783542"
---
# <a name="backgrounding-in-xamarinios"></a>Backgrounding in xamarin. IOS

_L'elaborazione o backgrounding di sfondo è il processo di consentendo alle applicazioni di eseguire le attività in background mentre un'altra applicazione è in esecuzione in primo piano. Questa guida viene utilizzato come un'introduzione all'elaborazione in iOS in background._

Backgrounding in applicazioni per dispositivi mobili è fondamentalmente diverso rispetto al tradizionale concetto multitasking sul desktop. Computer desktop hanno un'ampia varietà di risorse disponibili per un'applicazione, tra cui area dello schermo, potenza e memoria. Le applicazioni sono in grado di eseguire side-by-side e rimangono ad alte prestazioni e utilizzabile. In un dispositivo mobile, le risorse sono molto più limitate. È difficile visualizzare più di un'applicazione di piccole dimensioni e che esegue più applicazioni alla massima velocità sarebbe svuotare la batteria. Backgrounding è un costante compromesso tra fornendo applicazioni le risorse per l'esecuzione di attività in background che necessari per le prestazioni migliori e mantenere reattiva l'applicazione foregrounded e il dispositivo. IOS e Android dispongono del provisioning per backgrounding, ma gestiti in modi molto diversi.

In iOS, backgrounding è riconosciuto come uno stato dell'applicazione e le app vengono spostate da e verso lo stato di sfondo in base al comportamento dell'app e l'utente. iOS offre inoltre diverse opzioni per l'associazione di un'app per l'esecuzione in background, tra cui chiedere il sistema operativo per periodo di tempo per completare un'attività importante, che opera come un tipo di applicazione in background necessarie noti, e l'aggiornamento del contenuto di un'applicazione in designato intervalli.

In questa Guida e procedure dettagliate di accompagnamento, verrà illustrato come eseguire attività dell'applicazione in background. Si verrà illustrano i concetti chiave e le procedure consigliate e quindi passare attraverso la creazione di un'applicazione reale che riceve gli aggiornamenti di percorso in background.

## <a name="contents"></a>Sommario

1.  [Introduzione all'elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1.  [Demo del ciclo di vita dell'applicazione](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1.  [Tecniche di elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1.  [Procedure dettagliate: Elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1.  [Linee guida per l'elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>Riepilogo

In questa Guida, abbiamo introdotto i diversi modi di eseguire l'elaborazione in background in iOS. È coperto iOS stati dell'applicazione ed esaminare il ruolo rivestito nel ciclo di vita dell'applicazione iOS backgrounding. Inoltre, è stato descritto come è stato possibile registrare le singole attività o intere applicazioni funzionino in background in iOS. Infine, abbiamo rafforzato approfondimento di backgrounding in iOS per la creazione di applicazioni che eseguono aggiornamenti in background.



## <a name="related-links"></a>Collegamenti correlati

- [Backgrounding in Android](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (esempio)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
- [Posizione (esempio)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Trasferimento in Background semplice (esempio)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
- [Esecuzione in Background di iOS](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
