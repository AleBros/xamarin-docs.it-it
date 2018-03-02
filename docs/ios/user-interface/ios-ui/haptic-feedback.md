---
title: Inviare commenti e suggerimenti tattili
description: In questo articolo vengono illustrati i nuovi tipi di commenti e suggerimenti tattili disponibile in iOS 10 e come implementarli in xamarin. IOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 9f4eb989fe0c91471c9473c512c4befd36e4ace2
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="providing-haptic-feedback"></a>Inviare commenti e suggerimenti tattili

_In questo articolo vengono illustrati i nuovi tipi di commenti e suggerimenti tattili disponibile in iOS 10 e come implementarli in xamarin. IOS._

<a name="Overview" />

## <a name="overview"></a>Panoramica

L'iPhone 7 e iPhone 7 Plus, Apple ha incluso nuove risposte tattili che forniscono altri modi di stabilire fisicamente l'utente. Feedback tattili (noto anche semplicemente come Haptics) utilizza il senso di tocco (tramite force, vibrazioni o movimento) nella progettazione dell'interfaccia utente. Usare queste nuove opzioni commenti e suggerimenti tattili per ottenere l'attenzione dell'utente e rafforzare le relative azioni.

Gli argomenti seguenti vengono descritti nel dettaglio:

- [Sui commenti tattili](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>Sui commenti tattili

Alcuni elementi dell'interfaccia utente predefiniti già commenti tattili, ad esempio le selezioni, commutatori e i dispositivi di scorrimento. iOS 10 verrà aggiunta la possibilità di trigger a livello di programmazione haptics utilizzando una sottoclasse concreta la `UIFeedbackGenerator` classe.

Lo sviluppatore può utilizzare uno dei seguenti `UIFeedbackGenerator` sottoclassi a livello di codice trigger tattili commenti e suggerimenti:

- `UIImpactFeedbackGenerator` -Utilizzare il generatore di commenti e suggerimenti per integrare un'azione o attività, ad esempio la presentazione di un "thud" quando si scorre una visualizzazione in posizione o se sono in conflitto di due oggetti sullo schermo.
- `UINotificationFeedbackGenerator` -Utilizzare il generatore di commenti e suggerimenti per le notifiche, ad esempio un tipo di completamento, errore o qualsiasi altra azione di avviso.
- `UISelectionFeedbackGenerator` -Utilizzare il generatore di commenti e suggerimenti per una selezione attivamente la modifica, ad esempio un elemento da un elenco di selezione.

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

Utilizzare il generatore di commenti e suggerimenti per integrare un'azione o attività, ad esempio la presentazione di un "thud" quando si scorre una visualizzazione in posizione o se sono in conflitto di due oggetti sullo schermo.

Utilizzare il seguente codice al trigger impatto feedback:

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

Quando lo sviluppatore crea una nuova istanza di `UIImpactFeedbackGenerator` classe forniscono un `UIImpactFeedbackStyle` specificando il livello di attendibilità di feedback come:

- `Heavy`
- `Medium`
- `Light`

Il `Prepare` metodo il `UIImpactFeedbackGenerator` viene chiamato per informare il sistema di commenti e suggerimenti tattili sono per verificarsi in modo che è possibile ridurre al minimo la latenza.

Il `ImpactOccurred` metodo attiva quindi tattili commenti e suggerimenti.

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

Utilizzare il generatore di commenti e suggerimenti per le notifiche, ad esempio un tipo di completamento, errore o qualsiasi altra azione di avviso.

Utilizzare il seguente codice al trigger notifica feedback:

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

Una nuova istanza di `UINotificationFeedbackGenerator` classe viene creata e il relativo `Prepare` metodo viene chiamato per informare il sistema di commenti e suggerimenti tattili sono per verificarsi in modo che è possibile ridurre al minimo la latenza.

Il `NotificationOccurred` viene chiamato per attivare tattili commenti e suggerimenti con un determinato `UINotificationFeedbackType` di:

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

Utilizzare il generatore di commenti e suggerimenti per una selezione attivamente la modifica, ad esempio un elemento da un elenco di selezione.

Utilizzare il seguente codice al feedback selezione trigger:

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

Una nuova istanza di `UISelectionFeedbackGenerator` classe viene creata e il relativo `Prepare` metodo viene chiamato per informare il sistema di commenti e suggerimenti tattili sono per verificarsi in modo che è possibile ridurre al minimo la latenza.

Il `SelectionChanged` metodo attiva quindi tattili commenti e suggerimenti.

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati i nuovi tipi di commenti e suggerimenti tattili disponibile in iOS 10 e come implementarli in xamarin. IOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
