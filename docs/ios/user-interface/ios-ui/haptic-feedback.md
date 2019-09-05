---
title: Fornire feedback tattile in Novell. iOS
description: Questo documento descrive come fornire commenti e suggerimenti tattili in un'app Novell. iOS. Vengono illustrati UIImpactFeedbackGenerator, UINotificationFeedbackGenerator e UISelectionFeedbackGenerator.
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 112ee17eab872f9265687869bec82e72f44e81da
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287085"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>Fornire feedback tattile in Novell. iOS

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

Molti elementi dell'interfaccia utente incorporati forniscono già Commenti tattili, ad esempio Picker, commutatori e dispositivi di scorrimento. iOS 10 aggiunge ora la possibilità di attivare haptics a livello di codice usando una sottoclasse `UIFeedbackGenerator` concreta della classe.

Lo sviluppatore può usare una delle sottoclassi `UIFeedbackGenerator` seguenti per attivare il feedback tattile a livello di codice:

- `UIImpactFeedbackGenerator`-Usare questo generatore di commenti per integrare un'azione o un'attività, ad esempio la presentazione di un "tonfo" quando una visualizzazione viene spostata sul posto o se due oggetti sullo schermo sono in conflitto.
- `UINotificationFeedbackGenerator`-Usare questo generatore di commenti per le notifiche, ad esempio il completamento di un'azione, l'errore o qualsiasi altro tipo di avviso.
- `UISelectionFeedbackGenerator`-Usare questo generatore di commenti per una selezione che cambia attivamente, ad esempio selezionando un elemento da un elenco.

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

Quando lo sviluppatore crea una nuova istanza della `UIImpactFeedbackGenerator` classe, fornisce un oggetto `UIImpactFeedbackStyle` che specifica l'attendibilità del feedback come:

- `Heavy`
- `Medium`
- `Light`

Il `Prepare` metodo`UIImpactFeedbackGenerator` di viene chiamato per informare il sistema che il feedback tattile sta per essere eseguito, in modo da ridurre al minimo la latenza.

Il `ImpactOccurred` metodo attiva quindi il feedback tattile.

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

Viene creata una nuova istanza `UINotificationFeedbackGenerator` della classe e viene chiamato `Prepare` il relativo metodo per informare il sistema che il feedback tattile sta per essere eseguito, in modo da ridurre al minimo la latenza.

Viene `NotificationOccurred` chiamato per attivare il feedback tattile con un `UINotificationFeedbackType` dato di:

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

Viene creata una nuova istanza `UISelectionFeedbackGenerator` della classe e viene chiamato `Prepare` il relativo metodo per informare il sistema che il feedback tattile sta per essere eseguito, in modo da ridurre al minimo la latenza.

Il `SelectionChanged` metodo attiva quindi il feedback tattile.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i nuovi tipi di feedback tattili disponibili in iOS 10 e come implementarli in Novell. iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
