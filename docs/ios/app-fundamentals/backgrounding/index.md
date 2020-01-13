---
title: In background in Xamarin.iOS
description: L'elaborazione in background o in background è il processo che consente alle applicazioni di eseguire le attività in background mentre un'altra applicazione viene eseguita in primo piano. Questa guida funge da introduzione all'elaborazione in background in iOS.
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: e51b6c7ffe0b0ae027e527d959979e558afa6325
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005059"
---
# <a name="backgrounding-in-xamarinios"></a>In background in Xamarin.iOS

_L'elaborazione in background o in background è il processo che consente alle applicazioni di eseguire le attività in background mentre un'altra applicazione viene eseguita in primo piano. Questa guida funge da introduzione all'elaborazione in background in iOS._

Il background nelle applicazioni per dispositivi mobili è fondamentalmente diverso dal concetto tradizionale di multitasking sul desktop. I computer desktop hanno un'ampia gamma di risorse disponibili per un'applicazione, tra cui la proprietà dello schermo, l'alimentazione e la memoria. Le applicazioni possono essere eseguite side-by-side e rimanere efficienti e utilizzabili. In un dispositivo mobile, le risorse sono molto più limitate. È difficile mostrare più di un'applicazione in uno schermo di piccole dimensioni e l'esecuzione di più applicazioni a velocità intera scaricherebbe la batteria. Il background è un compromesso costante tra la concessione delle risorse alle applicazioni per l'esecuzione delle attività in background necessarie per garantire prestazioni ottimali e la reattività dell'applicazione in primo piano e del dispositivo. IOS e Android hanno provisioning per il background, ma lo gestiscono in modi molto diversi.

In iOS, lo sfondo viene riconosciuto come uno stato dell'applicazione e le app vengono spostate in e fuori dallo stato in background a seconda del comportamento dell'app e dell'utente. iOS offre inoltre diverse opzioni per il cablaggio di un'app per l'esecuzione in background, ad esempio per chiedere al sistema operativo il tempo necessario per il completamento di un'attività importante, il funzionamento di un tipo di applicazione nota necessaria in background e l'aggiornamento del contenuto di un'applicazione in designato intervalli.

In questa guida vengono descritte le procedure dettagliate per eseguire le attività dell'applicazione in background. Vengono illustrati i concetti chiave e le procedure consigliate, quindi viene illustrata la creazione di un'app reale che riceve gli aggiornamenti della posizione in background.

## <a name="contents"></a>Contenuti

1. [Introduzione all'elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1. [Demo del ciclo di vita dell'applicazione](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1. [Tecniche di elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1. [Procedure dettagliate: Elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1. [Linee guida per l'elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>Riepilogo

In questa guida sono stati introdotti i diversi modi di eseguire l'elaborazione in background in iOS. Sono stati analizzati gli Stati delle applicazioni iOS ed è stato esaminato il ruolo in background del ciclo di vita dell'applicazione iOS. Si è inoltre appreso come è possibile registrare singole attività o intere applicazioni per operare in background in iOS. Infine, abbiamo rafforzato la conoscenza del background su iOS creando applicazioni che eseguono aggiornamenti in background.

## <a name="related-links"></a>Collegamenti correlati

- [Background in Android](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/lifecycledemo)
- [Località (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/location)
- [Trasferimento in background semplice (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplebackgroundtransfer)
- [Esecuzione in background iOS](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
