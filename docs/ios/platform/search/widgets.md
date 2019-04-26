---
title: Ricerca e schermata Home miglioramenti apportati Widget in iOS 10
description: Questo documento vengono descritti i miglioramenti che Apple ha apportato al widget in iOS 10, inclusi gli aggiornamenti a schermata iniziale widget e ricerca.
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f693b480fff141c177ed135ced60afd65abd77de
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082171"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>Ricerca e schermata Home miglioramenti apportati Widget in iOS 10

_Questo articolo illustra i miglioramenti di che Apple ha apportato al sistema di Widget in iOS 10._

Apple ha introdotto numerosi miglioramenti al sistema Widget per garantire che i widget aspetto eccezionali su qualsiasi dello sfondo che esiste nel nuovo iOS 10 schermata di blocco. Inoltre, i widget includono ora un [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) proprietà che consente agli sviluppatori di descrivere la quantità di contenuto è disponibile e consente all'utente di espandere e comprimere il contenuto.

Widget (noto anche come oggi estensioni) sono un tipo speciale di iOS estensione che visualizzano una piccola quantità di informazioni utili o esporre funzionalità specifiche dell'app in modo tempestivo. Ad esempio, l'app di notizie ha un proprio widget che mostra i primi titoli e l'app del calendario offre due diversi widget: uno per la visualizzazione oggi gli eventi e uno per visualizzare gli eventi imminenti.

I widget sono altamente personalizzabili e possono contenere elementi dell'interfaccia utente, ad esempio testo, immagini, pulsanti, e così via. Inoltre, lo sviluppatore può personalizzare ulteriormente il layout dei loro widget.

[![](widgets-images/widgets01.png "Widget di esempio")](widgets-images/widgets01.png#lightbox)

Esistono due posizioni principali che un utente può visualizzare e interagire con i widget di un'app:

- **Schermata di ricerca** -gli utenti possono aggiungere i widget sono maggiormente utili per la schermata di ricerca. Schermata di ricerca è accessibile tramite scorrimento rapido verso destra in schermate Home sia il blocco.
- **La schermata Home** -dalla schermata iniziale, l'utente può usare funzionalità Touch 3D per aprire l'elenco di azioni rapide applicando un utilizzo elevato per l'icona dell'app. Widget di un'app verrà visualizzato sopra l'elenco di azioni rapide. Vedere la [Introduzione a 3D Touch](~/ios/platform/3d-touch.md) per altre informazioni.

## <a name="widgets-developer-suggestions"></a>Suggerimenti per gli sviluppatori di widget

In teoria, lo sviluppatore deve sempre prova e la progettazione widget che l'utente dovrà aggiungere per le schermate di ricerca. A tale scopo, Apple ha i suggerimenti seguenti:

- **Creare un'eccellente, esperienza Glanceable** -dell'utente desidera widget che forniscono informazioni in breve, glanceable degli aggiornamenti di stato o consentire loro di eseguire semplici attività rapidamente. In questo modo, che fornisce la giusta quantità di informazioni e l'interattività un essenziali. Quando possibile, consentire all'utente di eseguire un'attività specifica con un singolo tocco. Inoltre, poiché i widget non supportano la panoramica o lo scorrimento, questo dovrà essere prese in considerazione nella progettazione del Widget.
- **Mostrare rapidamente contenuto** -widget sono progettati per essere glanceable, pertanto l'utente non dovrebbe avere mai in attesa per il contenuto da caricare quando viene visualizzato un Widget. Widget devono memorizzare nella cache il contenuto localmente in modo da poter dimostrare contenuto recente durante il caricamento di contenuto aggiornato in background.
- **Specificare i margini e spaziatura interna appropriata** -widget deve aspetto affollati mai, pertanto, evitare di estendere il contenuto ai bordi della visualizzazione del Widget. Deve esistere sempre un ampio margine di diverse in pixel tra il bordo e il contenuto. Usando l'icona dell'app, visualizzato nella parte superiore del Widget, una Guida di allineamento è inoltre suggerite da Apple. Se il Widget presenta un layout di griglia, assicurarsi che vi sia corretto riempimento tra gli elementi nella griglia e provare a limitare il numero di elementi al massimo quattro.
- **Uso dei layout adattabile** -larghezza del Widget A variano in base all'orientamento del dispositivo e il dispositivo è alimentato. Altezza del Widget possa anche variano a seconda se viene visualizzata nello stato compresso (impostazione predefinita) o espansa (non supportato da tutti i widget). Un Widget compresso ha un'altezza delle righe di tabella di circa due e mezzo standard di iOS. Lo sviluppatore può richiedere la dimensione per un Widget espanso, ma deve idealmente essere inferiore all'altezza dello schermo. Nello stato compresso, il Widget deve presentare le informazioni essenziali solo, autonome. Quando espansa, il Widget deve mostrare informazioni supplementari che migliorano il principale contenuto visualizzato nello stato compresso. Widget visualizzato nell'elenco di azioni rapide sarà solo stavu compressi.
- **Non personalizzare in Background del Widget** -widget vengono visualizzati su uno sfondo chiaro, sfocato fornito dal sistema. Questa operazione viene eseguita per garantire la coerenza tra i widget e migliorare la leggibilità dei propri contenuti. Evitare di usare un'immagine come sfondo Widget perché potrebbe essere in conflitto con sfondi di blocco e schermata iniziale dell'utente.
- **Usare il carattere di sistema in nero o in grigio scuro** : la visualizzazione di testo in un Widget, il carattere di sistema è più adatta. Il tipo di carattere deve essere un colore grigio scuro o nero per mettere in risalto rispetto allo sfondo Widget leggero e sfocato.
- **Fornire App l'accesso quando appropriato** -Widget deve sempre essere gestito separatamente dalla propria app, tuttavia, se è necessaria una maggiore funzionalità, il Widget deve essere in grado di avviare l'app per visualizzare o modificare un'informazione specifica. Non includere mai un pulsante "Apri l'app", è sufficiente consentire all'utente di toccare il contenuto stesso e mai aperta una 3rd party app.
- **Selezionare un chiaro, conciso nome del Widget** -l'icona dell'app e il nome del Widget vengono sempre visualizzate sul contenuto del Widget. Usando il nome dell'applicazione per il Widget primario e un nome chiaro e conciso per tutti gli altri che fornisce suggerite da Apple. Quando si fornisce un titolo di Widget personalizzato, dovrà essere preceduti con il nome dell'app (ad esempio, le mappe nelle vicinanze, ai ristoranti di mappe e così via).
- **Indicano quando l'autenticazione aggiunge valore** : se l'opzione funzionalità aggiuntive o informazioni è disponibile solo quando l'utente viene autenticato e connesso, presentare all'utente. Ad esempio, una giostra app di condivisione potrebbe indicare "Accedi a rani libro".
- **Selezionare un Widget di elenco azioni rapide** -se l'app offre più di un Widget, lo sviluppatore deve scegliere quello in cui presentare quando l'utente visualizza l'elenco di azioni rapide applicando un utilizzo elevato per l'icona dell'app usando Touch 3D.

Per altre informazioni sull'uso di widget, vedere la [Introduzione alle estensioni](~/ios/platform/extensions.md), [Introduzione a 3D Touch](~/ios/platform/3d-touch.md) documentazione e Apple [Guida per programmatori App estensione](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html).

## <a name="working-with-vibrancy"></a>Utilizzo di vitalità

Vitalità garantisce che il testo del Widget rimane leggibile in chiaro del Widget, sfocato in background (fornita dal sistema). Prima di iOS 10, lo sviluppatore utilizzerebbe una [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) per vitalità del Widget. Ad esempio:

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

Questo è deprecato in iOS 10 e deve essere sostituito con un [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o una [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect). Ad esempio:

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>Utilizzo di espansi e comprimere i widget

Nuovo ai dispositivi iOS 10, i widget includono ora un [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) proprietà che consente agli sviluppatori di descrivere la quantità di contenuto è disponibile e consente all'utente di espandere e comprimere il contenuto.

Quando un Widget viene inizialmente visualizzato, è in uno stato compresso. Un Widget compresso ha un'altezza delle righe di tabella di circa due e mezzo standard di iOS. Lo sviluppatore può richiedere la dimensione per un Widget espanso, ma deve idealmente essere inferiore all'altezza dello schermo. 

Nello stato compresso, il Widget deve presentare le informazioni essenziali solo, autonome. Quando espansa, il Widget deve mostrare informazioni supplementari che migliorano il principale contenuto visualizzato nello stato compresso. Ad esempio, l'app meteo illustra le condizioni meteo correnti quando compresso e aggiunge l'orario di prevedere se viene espansa.

Widget visualizzato nell'elenco di azioni rapide sarà solo stavu compressi. Se l'app offre più di un Widget, lo sviluppatore deve scegliere quello in cui presentare quando l'utente visualizza l'elenco di azioni rapide applicando un utilizzo elevato per l'icona dell'app usando Touch 3D.

L'esempio seguente è un semplice oggi dell'estensione di (il Widget) che gestisce gli stati espansi e compressi:

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

Esaminare il codice specifico di modalità di visualizzazione Widget in modo dettagliato. Per informare il sistema che questo Widget supporta lo stato espanso, Usa:

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

Per ottenere modalità di visualizzazione corrente del Widget, Usa:

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

Per ottenere la dimensione massima per lo stato compressi o espansi, Usa:

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

E per gestire il modifica dello stato (modalità di visualizzazione), Usa:

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

Oltre a impostare le dimensioni richieste per ogni stato (compresso o espanso), aggiorna anche il contenuto visualizzato affinché corrisponda alla nuova dimensione.

## <a name="summary"></a>Riepilogo

Questo articolo ha trattati i miglioramenti di che Apple ha apportato al sistema di Widget in iOS 10 e illustrato come implementarli in xamarin. IOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Introduzione alle estensioni](~/ios/platform/extensions.md)
- [Introduzione a 3D Touch](~/ios/platform/3d-touch.md)
- [Guida per programmatori dell'estensione App](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
