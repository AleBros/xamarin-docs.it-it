---
title: Miglioramenti ai widget della schermata iniziale e di ricerca in iOS 10
description: Questo documento descrive i miglioramenti apportati da Apple ai widget in iOS 10, inclusi gli aggiornamenti ai widget per la ricerca e la schermata iniziale.
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: ca6ccce934b32fa0d7e48cd8f295d9acefe6e121
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031504"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>Miglioramenti ai widget della schermata iniziale e di ricerca in iOS 10

_Questo articolo descrive i miglioramenti apportati da Apple al sistema widget in iOS 10._

Apple ha introdotto diversi miglioramenti al sistema widget per garantire che i widget risultino ottimali in qualsiasi sfondo presente nella nuova schermata di blocco di iOS 10. Inoltre, i widget contengono ora una proprietà [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) che consente allo sviluppatore di descrivere la quantità di contenuto disponibile e consente all'utente di espandere e comprimere il contenuto.

I widget (noti anche come estensioni odierne) sono un tipo speciale di estensione iOS che visualizza una piccola quantità di informazioni utili o espone le funzionalità specifiche dell'app in modo tempestivo. Ad esempio, l'app News include un widget che mostra i primi titoli e l'app Calendar fornisce due widget diversi: uno per visualizzare gli eventi attuali e uno per mostrare gli eventi imminenti.

I widget sono altamente personalizzabili e possono contenere elementi dell'interfaccia utente, ad esempio testo, immagini, pulsanti e così via. Inoltre, lo sviluppatore può personalizzare ulteriormente il layout dei widget.

[![](widgets-images/widgets01.png "Example widgets")](widgets-images/widgets01.png#lightbox)

Esistono due punti principali che un utente può visualizzare e interagire con i widget di un'app:

- **La schermata di ricerca** consente agli utenti di aggiungere i widget che risultano più utili per la schermata di ricerca. È possibile accedere alla schermata di ricerca scorrendo a destra le schermate Home e di blocco.
- **Schermata iniziale** : dalla schermata iniziale, l'utente può usare 3D Touch per aprire l'elenco delle azioni rapide applicando la pressione sull'icona dell'app. I widget di un'app verranno visualizzati sopra l'elenco delle azioni rapide. Per ulteriori informazioni, vedere la documentazione [introduttiva a 3D Touch](~/ios/platform/3d-touch.md) .

## <a name="widgets-developer-suggestions"></a>Suggerimenti per sviluppatori widget

Idealmente, lo sviluppatore deve sempre provare e progettare i widget che l'utente vorrà aggiungere alle schermate di ricerca. A tal fine, Apple presenta i suggerimenti seguenti:

- **Creare un'esperienza straordinaria e** intuitiva: si vuole che i widget forniscano informazioni concise sugli aggiornamenti di stato o consentono loro di eseguire rapidamente attività semplici. Questo consente di fornire la giusta quantità di informazioni e l'interattività di un elemento essenziale. Quando possibile, consentire all'utente di eseguire un'attività specifica con un solo tocco. Inoltre, poiché i widget non supportano la panoramica o lo scorrimento, è necessario prenderne in considerazione la progettazione del widget.
- Per **visualizzare rapidamente il contenuto** , i widget sono progettati per essere visualizzati in modo rapido, quindi l'utente non deve mai attendere il caricamento del contenuto dopo la visualizzazione di un widget. I widget devono memorizzare il contenuto nella cache in locale in modo che possano visualizzare il contenuto recente mentre il caricamento in background è stato aggiornato.
- **Fornire spaziatura interna e margini appropriati** -i widget non devono mai apparire affollati, quindi evitare di estendere il contenuto ai bordi della visualizzazione di un widget. Tra i bordi e il contenuto deve essere sempre presente un margine ampio in pixel. Apple suggerisce anche di usare l'icona dell'app visualizzata nella parte superiore del widget come guida di allineamento. Se il widget presenta un layout di griglia, assicurarsi che vi sia una spaziatura corretta tra gli elementi nella griglia e provare a limitare il numero di elementi a quattro max.
- **Usare layout adattabili** : la larghezza di un widget può variare in base al dispositivo in cui è in esecuzione e all'orientamento del dispositivo. L'altezza di un widget può variare anche a seconda che venga visualizzata in uno stato compresso (impostazione predefinita) o espanso (non supportato da tutti i widget). Un widget compresso è costituito da un'altezza di circa due righe e da una metà standard della tabella iOS. Lo sviluppatore può richiedere le dimensioni per un widget espanso, ma dovrebbe essere idealmente minore dell'altezza dello schermo. Nello stato compresso, il widget dovrebbe visualizzare solo le informazioni essenziali, autonome. Se espanso, il widget dovrebbe visualizzare informazioni supplementari che migliorano il contenuto primario visualizzato nello stato compresso. I widget visualizzati nell'elenco delle azioni rapide saranno in stato compresso.
- **Non personalizzare lo sfondo del widget. i** widget vengono visualizzati su uno sfondo chiaro e sfocato fornito dal sistema. Questa operazione viene eseguita per promuovere la coerenza tra i widget e migliorare la leggibilità del contenuto. Evitare di usare un'immagine come sfondo del widget perché potrebbe scontrarsi con le immagini di blocco e della schermata iniziale dell'utente.
- **Usare il tipo di carattere del sistema in grigio nero o scuro** : quando si Visualizza il testo in un widget, il tipo di carattere del sistema funziona meglio. Il tipo di carattere deve trovarsi in un colore grigio nero o scuro per distinguersi dallo sfondo del widget chiaro e sfocato.
- **Fornire l'accesso alle app quando appropriato** . i widget devono funzionare sempre separatamente dalla loro app. Tuttavia, se è necessaria una funzionalità più approfondita, il widget dovrebbe essere in grado di avviare l'app per visualizzare o modificare informazioni specifiche. Non includere mai un pulsante "Apri app", semplicemente consentire all'utente di toccare il contenuto e non aprire mai un'app di terze parti.
- **Selezionare un nome di widget chiaro e conciso** . l'icona dell'app e il nome del widget vengono sempre visualizzati sul contenuto del widget. Apple suggerisce di usare il nome dell'app per il widget principale e un nome chiaro e conciso per tutti gli altri. Quando si specifica un titolo personalizzato per il widget, è preferibile usare il nome dell'app, ad esempio le mappe vicine, i ristoranti Maps e così via.
- **Informa quando l'autenticazione aggiunge valore** : se sono disponibili funzionalità o informazioni aggiuntive solo quando l'utente è autenticato e connesso, presentarlo all'utente. Ad esempio, un'app ride sharing può indicare "accedi a book ride".
- **Selezionare un widget Elenco azioni rapide** : se l'app fornisce più di un widget, lo sviluppatore deve scegliere quello da presentare quando l'utente Visualizza l'elenco di azioni rapide applicando la pressione all'icona dell'app usando il tocco 3D.

Per altre informazioni sull'uso dei widget, vedere l' [Introduzione alle estensioni](~/ios/platform/extensions.md), [Introduzione alla documentazione di 3D Touch](~/ios/platform/3d-touch.md) e [Guida alla programmazione delle estensioni](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)per le app di Apple.

## <a name="working-with-vibrancy"></a>Uso di vitalità

Vividity garantisce che il testo di un widget rimanga leggibile quando viene visualizzato sullo sfondo chiaro e sfocato del widget (fornito dal sistema). Prima di iOS 10, lo sviluppatore utilizzava un [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) per la vivacità del widget. Esempio:

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

Questa funzionalità è deprecata in iOS 10 e deve essere sostituita da un [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect). Esempio:

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>Uso dei widget compressi ed espansi

Novità di iOS 10, i widget contengono ora una proprietà [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) che consente allo sviluppatore di descrivere la quantità di contenuto disponibile e consente all'utente di espandere e comprimere il contenuto.

Quando un widget viene inizialmente visualizzato, si trova in uno stato compresso. Un widget compresso è costituito da un'altezza di circa due righe e da una metà standard della tabella iOS. Lo sviluppatore può richiedere le dimensioni per un widget espanso, ma dovrebbe essere idealmente minore dell'altezza dello schermo.

Nello stato compresso, il widget dovrebbe visualizzare solo le informazioni essenziali, autonome. Se espanso, il widget dovrebbe visualizzare informazioni supplementari che migliorano il contenuto primario visualizzato nello stato compresso. Ad esempio, l'app Weather Mostra le condizioni meteo correnti quando viene compressa e aggiunge la previsione oraria quando viene espansa.

I widget visualizzati nell'elenco delle azioni rapide saranno in stato compresso. Se l'app fornisce più di un widget, lo sviluppatore deve scegliere quello da presentare quando l'utente Visualizza l'elenco delle azioni rapide applicando la pressione all'icona dell'app usando il tocco 3D.

L'esempio seguente è costituito da una semplice estensione di oggi (widget) che gestisce gli stati compresso ed espanso:

```csharp
using System;
using NotificationCenter;
using Foundation;
using UIKit;
using CoreGraphics;

namespace MonkeyAbout
{
    public partial class TodayViewController : UIViewController, INCWidgetProviding
    {
        protected TodayViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Tell widget it can be expanded
            ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);

            // Get the maximum size
            var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
        }

        [Export ("widgetPerformUpdateWithCompletionHandler:")]
        public void WidgetPerformUpdate (Action<NCUpdateResult> completionHandler)
        {
            // Take action based on the display mode
            switch (ExtensionContext.GetWidgetActiveDisplayMode()) {
            case NCWidgetDisplayMode.Compact:
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                Content.Text = "Gorilla!!!!";
                break;
            }

            // Report results
            // If an error is encoutered, use NCUpdateResultFailed
            // If there's no update required, use NCUpdateResultNoData
            // If there's an update, use NCUpdateResultNewData
            completionHandler (NCUpdateResult.NewData);
        }

        [Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
        public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
        {
            // Take action based on the display mode
            switch (activeDisplayMode) {
            case NCWidgetDisplayMode.Compact:
                PreferredContentSize = maxSize;
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                PreferredContentSize = new CGSize (0, 200);
                Content.Text = "Gorilla!!!!";
                break;
            }
        }

    }
}
```

Esaminare in dettaglio il codice specifico della modalità di visualizzazione widget. Per informare il sistema che questo widget supporta lo stato espanso, USA:

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

Per ottenere la modalità di visualizzazione corrente del widget, USA:

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

Per ottenere le dimensioni massime per lo stato compresso o espanso, USA:

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

Per gestire lo stato (modalità di visualizzazione), la modifica USA:

```csharp
[Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
{
    // Take action based on the display mode
    switch (activeDisplayMode) {
    case NCWidgetDisplayMode.Compact:
        PreferredContentSize = maxSize;
        Content.Text = "Let's Monkey About!";
        break;
    case NCWidgetDisplayMode.Expanded:
        PreferredContentSize = new CGSize (0, 200);
        Content.Text = "Gorilla!!!!";
        break;
    }
}
```

Oltre a impostare la dimensione richiesta per ogni stato (compresso o espanso), viene aggiornato anche il contenuto visualizzato in modo che corrisponda alla nuova dimensione.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto i miglioramenti apportati da Apple al sistema widget in iOS 10 e ha illustrato come implementarli in Xamarin.iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Introduzione alle estensioni](~/ios/platform/extensions.md)
- [Introduzione a 3D touch](~/ios/platform/3d-touch.md)
- [Guida alla programmazione delle estensioni dell'app](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
