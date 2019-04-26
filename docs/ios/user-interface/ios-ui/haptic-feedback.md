---
title: Feedback Aptico in xamarin. IOS
description: Questo documento descrive come fornire l'implementazione del feedback aptico in un'app xamarin. IOS. Viene descritto UIImpactFeedbackGenerator UINotificationFeedbackGenerator e UISelectionFeedbackGenerator.
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: b2c381c59ba1574e80babc2c7e68535a3deffe35
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384619"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>Feedback Aptico in xamarin. IOS

<a name="Overview" />

## <a name="overview"></a>Panoramica

Sull'iPhone 7 e iPhone 7 Plus, Apple ha incluso nuove risposte aptico che forniscono altri modi per fisicamente coinvolgere l'utente. Implementazione del Feedback aptico (noto anche semplicemente come Haptics) usa il senso di tocco, tramite forza, vibrazioni o movimento, nella progettazione dell'interfaccia utente. Usare queste nuove opzioni tattili commenti e suggerimenti per rafforzare le relative azioni e ottenere l'attenzione dell'utente.

Gli argomenti seguenti vengono descritti nel dettaglio:

- [Sul Feedback Aptico](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>Sul Feedback Aptico

Diversi elementi dell'interfaccia utente predefiniti forniscono già del feedback aptico quali controlli di selezione, commutatori e i dispositivi di scorrimento. iOS 10 è ora aggiunta la possibilità di generare a livello di programmazione haptics usando una sottoclasse concreta del `UIFeedbackGenerator` classe.

Lo sviluppatore può utilizzare uno dei seguenti `UIFeedbackGenerator` alle sottoclassi di trigger a livello di programmazione del feedback aptico:

- `UIImpactFeedbackGenerator` -Usare il generatore di commenti e suggerimenti per integrare un'azione o attività, ad esempio la presentazione di un "thud" quando si scorre una visualizzazione nella posizione corretta oppure se sono in conflitto di due oggetti sullo schermo.
- `UINotificationFeedbackGenerator` -Usare il generatore di commenti e suggerimenti per le notifiche, ad esempio un tipo di completamento, errore o qualsiasi altra azione di avviso.
- `UISelectionFeedbackGenerator` -Usare il generatore di commenti e suggerimenti per una selezione attiva la modifica, ad esempio il prelievo di un elemento da un elenco.

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

Usare il generatore di commenti e suggerimenti per integrare un'azione o attività, ad esempio la presentazione di un "thud" quando si scorre una visualizzazione nella posizione corretta oppure se sono in conflitto di due oggetti sullo schermo.

Usare il codice seguente ai commenti e suggerimenti impatto di trigger:

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

Quando lo sviluppatore crea una nuova istanza della `UIImpactFeedbackGenerator` classe forniscono un `UIImpactFeedbackStyle` specificando l'attendibilità del feedback come:

- `Heavy`
- `Medium`
- `Light`

Il `Prepare` metodo di `UIImpactFeedbackGenerator` viene chiamato per informare il sistema che del feedback aptico sta per verificarsi in modo che è possibile ridurre al minimo la latenza.

Il `ImpactOccurred` metodo attiva quindi del feedback aptico.

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

Usare il generatore di commenti e suggerimenti per le notifiche, ad esempio un tipo di completamento, errore o qualsiasi altra azione di avviso.

Usare il codice seguente ai commenti e suggerimenti notifica trigger:

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

Una nuova istanza di `UINotificationFeedbackGenerator` classe viene creata e la relativa `Prepare` metodo viene chiamato per informare il sistema che del feedback aptico sta per verificarsi in modo che è possibile ridurre al minimo la latenza.

Il `NotificationOccurred` viene chiamato per attivare del feedback aptico con un determinato `UINotificationFeedbackType` di:

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

Usare il generatore di commenti e suggerimenti per una selezione attiva la modifica, ad esempio il prelievo di un elemento da un elenco.

Usare il codice seguente per il feedback della selezione di trigger:

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

Una nuova istanza di `UISelectionFeedbackGenerator` classe viene creata e la relativa `Prepare` metodo viene chiamato per informare il sistema che del feedback aptico sta per verificarsi in modo che è possibile ridurre al minimo la latenza.

Il `SelectionChanged` metodo attiva quindi del feedback aptico.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i nuovi tipi di feedback aptico disponibile in iOS 10 e come implementarli in xamarin. IOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
