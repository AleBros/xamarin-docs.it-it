---
title: Introduzione a tvOS 12
description: Questo documento fornisce una panoramica generale delle funzionalità nuove e aggiornate in tvOS 12 per le quali la versione di anteprima di Novell fornisce C# attualmente associazioni.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 94fea1786497d04602ea6cf06d875206cf69eb3e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030559"
---
# <a name="introduction-to-tvos-12"></a>Introduzione a tvOS 12

In questo documento viene fornita una panoramica di alto livello delle tvOS 12 nuove e aggiornate.

Per iniziare a creare app tvOS 12 con Novell, vedere la [Guida introduttiva](~/ios/platform/introduction-to-ios12/get-started.md).

## <a name="tvuikit"></a>TVUIKit

tvOS 12 include TVUIKit, un set di API che consentono agli sviluppatori di tvOS di usare controlli tvOS comuni, ad esempio visualizzazioni poster, pulsanti didascalia, visualizzazioni di schede e visualizzazioni Monogram. tvOS 12 introduce anche una proprietà che consente alle etichette di scorrere il testo troppo lungo per essere completamente visibile.

## <a name="password-autofill"></a>Riempimento automatico password

Con tvOS 12, gli utenti possono usare i propri dispositivi iOS per accedere a un'app tvOS con un solo tocco. Questa funzionalità è abilitata tramite una combinazione di `UITextContentType` utilizzo per specificare i campi nome utente e password, i domini associati per stabilire una relazione tra un'app per iOS e un'app tvOS e gli ambienti di messa a fuoco preferiti per selezionare un elemento per ricevere lo stato attivo dopo un utente fornisce un nome utente e una password.

## <a name="focus-engine-enhancements"></a>Miglioramenti del motore di messa a fuoco

tvOS 12 consente a tutte le app, indipendentemente dalla modalità di rendering, di interagire con il motore di messa a fuoco. Attraverso le interazioni di un utente con Siri remote, il motore di messa a fuoco può essere usato con qualsiasi app per selezionare un elemento, suggerire le possibili modifiche allo stato attivo e naturalmente aggiornare lo stato attivo. Questa funzionalità è abilitata nelle applicazioni personalizzate tramite l'interfaccia `IUIFocusItemContainer` di UIKit, la classe `UIFocusMovementHint`, l'interfaccia `IUIFocusItemScrollableContainer` e altri metodi e classi correlati.

## <a name="vision-framework"></a>Framework visione artificiale

Il Framework di visione include un rilevatore viso migliorato che può rilevare visi in diversi orientamenti. Inoltre, è ora possibile usare le revisioni delle richieste per selezionare una revisione specifica dell'algoritmo del Framework di visione.

## <a name="natural-language-framework"></a>Framework del linguaggio naturale

Il Framework del linguaggio naturale consente alle applicazioni di eseguire vari tipi di analisi del linguaggio. Ad esempio, può essere usato per identificare parti di sintesi vocale e determinare la lingua rappresentata da un blocco di testo.

## <a name="deprecations"></a>Elementi deprecati

Con tvOS 12, Apple ha deprecato OpenGL ES, [incoraggiando gli sviluppatori](https://developer.apple.com/tvos/whats-new/) ad adottare metal.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS-Apple Developer (Apple)](https://developer.apple.com/tvos/)
- [Novità di tvOS 12 (Apple) (video)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [TV (Apple)](https://www.apple.com/tv/)
