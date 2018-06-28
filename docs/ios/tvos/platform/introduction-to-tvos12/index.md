---
title: Introduzione a tvOS 12
description: Questo documento offre un livello elevato di panoramica delle funzionalità nuove e aggiornate in tvOS 12 per versione di anteprima di Xamarin che attualmente fornisce associazioni di c#.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 03841306ba54e511dbf2f2b86a7c17e9f4669bcd
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067317"
---
# <a name="introduction-to-tvos-12"></a>Introduzione a tvOS 12

![Anteprima](~/media/shared/preview.png)

> [!WARNING]
> Supporto 12 tvOS di Xamarin è attualmente in anteprima, vale a dire che può contenere bug, non completare funzionalità, e può cambiare. Usarlo per esperimenti solo.

> [!NOTE]
> - Rivedere le [Guida introduttiva](~/ios/platform/introduction-to-ios12/get-started.md) manuale per istruzioni su come iniziare a creare 12 App iOS e tvOS 12 con Xamarin.
> - Per altre informazioni, vedere la [note sulla versione](https://releases.xamarin.com/preview-release-xcode-10-beta/) versione di anteprima di Xamarin.

Questo documento fornisce una panoramica generale dei nuovi e aggiornati tvOS 12 funzionalità per l'anteprima di Xamarin quale versione fornisce attualmente c# associazioni.

## <a name="password-autofill"></a>Riempimento automatico di password

Con tvOS 12, gli utenti possono utilizzare i propri dispositivi iOS per accedere a un'app tvOS con un unico tocco. Questa opzione è abilitata tramite una combinazione di `UITextContentType` campi di utilizzo per specificare nome utente e password, domini associati per stabilire una relazione tra un'app per iOS e un'app tvOS e ambienti di messa a fuoco preferito per selezionare un elemento riceve lo stato attivo dopo che un utente fornisce un nome utente e una password.

## <a name="focus-engine-enhancements"></a>Miglioramenti apportati al motore di messa a fuoco

tvOS 12 consente tutte le app, indipendentemente dal modo in cui vengono visualizzati, per interagire con il motore lo stato attivo. Tramite le interazioni dell'utente con Siri remoto, il motore di messa a fuoco è utilizzabile con tutte le app per selezionare un elemento, accennare al modifiche possibili dello stato attivo e naturalmente aggiornare lo stato attivo. Questa opzione è abilitata in applicazioni personalizzate tramite del UIKit `IUIFocusItemContainer` interfaccia, il `UIFocusMovementHint` (classe), il `IUIFocusItemScrollableContainer` interfaccia e altri metodi e classi correlate.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS – Apple Developer (mela)](https://developer.apple.com/tvos/)
- [Novità in tvOS 12 (mela) (video)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [TV (mela)](https://www.apple.com/tv/)
- Anteprima di Xamarin [note sulla versione](https://releases.xamarin.com/preview-release-xcode-10-beta/)
