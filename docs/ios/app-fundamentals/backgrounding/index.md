---
title: Elaborazione in background in xamarin. IOS
description: In background di elaborazione o l'elaborazione in background in è il processo di consentire alle applicazioni di eseguire attività in background mentre un'altra applicazione è in esecuzione in primo piano. Questa guida serve come un'introduzione a iOS di elaborazione in background.
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2018
ms.openlocfilehash: a4f5112b6e77ab6e00453c19c766d1e905df1144
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122777"
---
# <a name="backgrounding-in-xamarinios"></a>Elaborazione in background in xamarin. IOS

_In background di elaborazione o l'elaborazione in background in è il processo di consentire alle applicazioni di eseguire attività in background mentre un'altra applicazione è in esecuzione in primo piano. Questa guida serve come un'introduzione a iOS di elaborazione in background._

Elaborazione in background in applicazioni per dispositivi mobili è fondamentalmente diverso il concetto tradizionale di multitasking sul desktop. Computer desktop hanno un'ampia gamma di risorse disponibili per un'applicazione, inclusi memoria, potenza e l'area dello schermo. Le applicazioni sono in grado di eseguire side-by-side e rimanere ad alte prestazioni e utilizzabile. In un dispositivo mobile, le risorse sono molto più limitate. È difficile da visualizzare più di un'applicazione su schermi di piccole dimensioni e diverse applicazioni in esecuzione alla massima velocità sarebbero esauriscono la batteria. Elaborazione in background in è un costante compromesso tra assegnare le risorse per eseguire le attività in background che necessarie per eseguire correttamente le applicazioni e mantenere reattiva l'applicazione foregrounded e il dispositivo. Dispongono del provisioning per l'elaborazione in background in iOS e Android, ma gestirla in modi molto diversi.

In iOS, elaborazione in background in viene riconosciuto come uno stato dell'applicazione e le app vengono spostate da e verso lo stato in background in base al comportamento dell'app e l'utente. iOS offre anche diverse opzioni per l'associazione di un'app per l'esecuzione in background, tra cui chiedere il sistema operativo per periodo di tempo per completare un'attività importante, funzionano come un tipo di applicazione in background necessarie noti, e aggiornamento del contenuto di un'applicazione a designato intervalli.

In questa Guida e procedure dettagliate di accompagnamento, verrà illustrato come eseguire attività dell'applicazione in background. Verranno riguardano i concetti chiave e le procedure consigliate e quindi passare attraverso la creazione di un'app reale che riceve aggiornamenti della posizione in background.

## <a name="contents"></a>Sommario

1.  [Introduzione all'elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1.  [Demo del ciclo di vita dell'applicazione](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1.  [Tecniche di elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1.  [Procedure dettagliate: Elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1.  [Linee guida per l'elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>Riepilogo

In questa Guida, abbiamo introdotto i diversi modi di eseguire l'elaborazione in background in iOS. È coperto iOS stati dell'applicazione ed esaminare il ruolo di elaborazione in background in viene riprodotta in iOS Application Lifecycle Management. Inoltre, è stato illustrato come è stato possibile registrare le singole attività o intere applicazioni per il funzionamento in background in iOS. Infine, abbiamo corroborata maggiore familiarità con elaborazione in background in IOS tramite la compilazione di applicazioni che eseguono gli aggiornamenti in background.



## <a name="related-links"></a>Collegamenti correlati

- [Elaborazione in background in Android](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (esempio)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
- [Location (esempio)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Trasferimento in Background semplice (esempio)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
- [l'esecuzione in Background di iOS](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
