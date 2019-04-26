---
title: Introduzione a tvOS 12
description: Questo documento offre un elevato livello di informazioni sulle funzionalità nuove e aggiornate di tvOS 12 per versione di anteprima di Xamarin che attualmente fornisce le associazioni c#.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: f7fb8cc379a070b848c5154c9c1d4fbfc8186266
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932542"
---
# <a name="introduction-to-tvos-12"></a>Introduzione a tvOS 12

Questo documento fornisce una panoramica generale delle nuove e aggiornate tvOS 12.

Per iniziare a creare App tvOS 12 con Xamarin, esaminiamo il [Guida introduttiva](~/ios/platform/introduction-to-ios12/get-started.md).

## <a name="tvuikit"></a>TVUIKit

tvOS 12 include TVUIKit, un set di API che consentono agli sviluppatori di tvOS di utilizzare controlli comuni di tvOS, ad esempio viste poster, pulsanti titolo, le visualizzazioni di smart card e Monogramma viste. tvOS 12 introduce anche una proprietà che consenta le etichette scorrere il testo troppo lungo per essere completamente visibili.

## <a name="password-autofill"></a>Riempimento automatico delle password

Con tvOS 12, gli utenti possono usare i propri dispositivi iOS per accedere a un'app tvOS con un singolo tocco. Questa opzione è abilitata tramite una combinazione di `UITextContentType` campi di utilizzo per specificare nome utente e password, domini associati per stabilire una relazione tra un'app per iOS e un'app tvOS e ambienti di messa a fuoco preferito per selezionare un elemento per ricevere lo stato attivo dopo che un utente fornisce un nome utente e password.

## <a name="focus-engine-enhancements"></a>Miglioramenti apportati al motore messa a fuoco

tvOS 12 consente tutte le app, indipendentemente dal modo in cui vengono visualizzati, per interagire con il motore di messa a fuoco. Tramite le interazioni dell'utente con il remoti per Siri, il motore di messa a fuoco è utilizzabile con qualsiasi app per selezionare un elemento, indicativi di possibili stato attivo cambia e naturalmente aggiornare lo stato attivo. Questa opzione è abilitata nelle applicazioni personalizzate tramite dell'UIKit `IUIFocusItemContainer` interfaccia, il `UIFocusMovementHint` (classe), il `IUIFocusItemScrollableContainer` interfaccia e altri metodi e classi correlate.

## <a name="vision-framework"></a>Framework Vision

Il framework Vision include un rilevamento volti migliorate che consentono di rilevare i visi in vari orientamenti. Inoltre, le revisioni richieste ora utilizzabile per selezionare una revisione dell'algoritmo di framework Vision specifica.

## <a name="natural-language-framework"></a>Framework di linguaggio naturale

Il framework del linguaggio naturale consente alle applicazioni di eseguire vari tipi di analisi della lingua. Ad esempio, può essere utilizzato per identificare le parti del discorso e determinare la lingua rappresentata da un blocco di testo.

## <a name="deprecations"></a>Deprecati

Con tvOS 12, Apple ha deprecato OpenGL ES [incoraggiano gli sviluppatori](https://developer.apple.com/tvos/whats-new/) adottare bare Metal.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS – per sviluppatori di Apple (Apple)](https://developer.apple.com/tvos/)
- [Novità in tvOS 12 (Apple) (video)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [TV (Apple)](https://www.apple.com/tv/)