---
title: Fornire feedback tattile in Xamarin.iOS
description: Questo documento descrive come fornire commenti e suggerimenti tattili in un'app Xamarin.iOS. Vengono illustrati UIImpactFeedbackGenerator, UINotificationFeedbackGenerator e UISelectionFeedbackGenerator.
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 156af7a5336ac091c0202e38a3a59a32846e281a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003351"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>Fornire feedback tattile in Xamarin.iOS

<a name="Overview" />

## <a name="overview"></a>Panoramica

In iPhone 7 e iPhone 7 Plus, Apple ha incluso nuove risposte tattili che offrono ulteriori modalità di coinvolgimento fisico dell'utente. Il feedback tattile (spesso definito semplicemente come haptics) usa il senso di tocco (via forza, vibrazioni o movimento) nella progettazione dell'interfaccia utente. Usare queste nuove opzioni di feedback tattile per attirare l'attenzione dell'utente e rafforzare le azioni.

Gli argomenti seguenti vengono descritti nel dettaglio:

- [Informazioni sul feedback tattile](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>Informazioni sul feedback tattile

Molti elementi dell'interfaccia utente incorporati forniscono già Commenti tattili, ad esempio Picker, commutatori e dispositivi di scorrimento. iOS 10 aggiunge ora la possibilità di attivare haptics a livello di codice usando una sottoclasse concreta della classe `UIFeedbackGenerator`.

Lo sviluppatore può usare una delle sottoclassi `UIFeedbackGenerator` seguenti per attivare il feedback tattile a livello di codice:

- `UIImpactFeedbackGenerator`: usare questo generatore di commenti per completare un'azione o un'attività, ad esempio la presentazione di un "tonfo" quando una visualizzazione viene spostata sul posto o se due oggetti sullo schermo sono in conflitto.
- `UINotificationFeedbackGenerator`: usare questo generatore di commenti per le notifiche, ad esempio il completamento di un'azione, l'errore o qualsiasi altro tipo di avviso.
- `UISelectionFeedbackGenerator`: usare questo generatore di commenti per una selezione che cambia attivamente, ad esempio selezionando un elemento da un elenco.

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

Usare questo generatore di commenti per integrare un'azione o un'attività, ad esempio la presentazione di un "tonfo" quando una visualizzazione viene spostata sul posto o se due oggetti sullo schermo sono in conflitto.

Usare il codice seguente per attivare il feedback sull'effetto:

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

Quando lo sviluppatore crea una nuova istanza della classe `UIImpactFeedbackGenerator`, fornisce una `UIImpactFeedbackStyle` che specifica il livello di attendibilità del feedback come:

- `Heavy`
- `Medium`
- `Light`

Viene chiamato il metodo `Prepare` della `UIImpactFeedbackGenerator` per informare il sistema che il feedback tattile sta per essere eseguito, in modo da ridurre al minimo la latenza.

Il metodo `ImpactOccurred` attiva quindi il feedback tattile.

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

Usare questo generatore di feedback per le notifiche, ad esempio il completamento di un'azione, l'errore o qualsiasi altro tipo di avviso.

Usare il codice seguente per attivare il feedback delle notifiche:

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

Viene creata una nuova istanza della classe `UINotificationFeedbackGenerator` e viene chiamato il relativo metodo `Prepare` per informare il sistema che il feedback tattile sta per essere eseguito, in modo da ridurre al minimo la latenza.

Il `NotificationOccurred` viene chiamato per attivare il feedback tattile con una `UINotificationFeedbackType` specificata di:

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

Usare questo generatore di commenti per una selezione che cambia attivamente, ad esempio selezionando un elemento da un elenco.

Usare il codice seguente per attivare il feedback di selezione:

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

Viene creata una nuova istanza della classe `UISelectionFeedbackGenerator` e viene chiamato il relativo metodo `Prepare` per informare il sistema che il feedback tattile sta per essere eseguito, in modo da ridurre al minimo la latenza.

Il metodo `SelectionChanged` attiva quindi il feedback tattile.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i nuovi tipi di feedback tattili disponibili in iOS 10 e come implementarli in Xamarin.iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
