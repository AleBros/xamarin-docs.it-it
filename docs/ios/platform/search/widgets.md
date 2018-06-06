---
title: Ricerca e Home schermata Widget i miglioramenti in iOS 10
description: Questo documento vengono descritti i miglioramenti che Apple ha tentato di widget nel iOS 10, inclusi gli aggiornamenti alla ricerca e widget schermata iniziale.
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 8383f167f8c85459e996b83368281fa243317948
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788111"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>Ricerca e Home schermata Widget i miglioramenti in iOS 10

_Questo articolo vengono descritti i miglioramenti che Apple apportate al sistema di Widget in iOS 10._

Apple ha introdotto numerosi miglioramenti al sistema di Widget per assicurarti che il widget eccezionale su eventuali sfondo che esiste nel nuovo iOS 10 schermata di blocco. Inoltre, ora contengono widget un [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) proprietà che consente agli sviluppatori di descrivere la quantità di contenuto è disponibile e consente all'utente di espandere e comprimere il contenuto.

Widget (noto anche come oggi estensioni) sono un tipo speciale di iOS estensione che visualizzano una piccola quantità di informazioni utili o esporre la funzionalità specifiche dell'app in modo tempestivo. Ad esempio, l'applicazione di notizie dispone di un widget che mostra i titoli superiore e l'app del calendario fornisce due widget diversi: uno per visualizzare gli eventi e attuali uno per visualizzare eventi futuri.

Widget sono altamente personalizzabili e possono contenere elementi dell'interfaccia utente, ad esempio testo, immagini, pulsanti, e così via. Inoltre, lo sviluppatore è possibile personalizzare ulteriormente il layout di loro widget.

[![](widgets-images/widgets01.png "Widget di esempio")](widgets-images/widgets01.png#lightbox)

Esistono due posizioni principali che un utente può visualizzare e interagire con i widget di un'app:

- **Schermata di ricerca** -gli utenti possono aggiungere il widget sono maggiormente utili per la schermata di ricerca. La schermata di ricerca è accessibile scorrendo rapidamente verso destra nelle schermate Home sia il blocco.
- **La schermata Home** -dalla schermata iniziale, l'utente può utilizzare 3D Touch per aprire l'elenco di azioni rapide applicando pressione sull'icona dell'applicazione. Widget di un'app verrà visualizzato sopra l'elenco di azioni rapide. Consultare il nostro [introduzione 3D Touch](~/ios/platform/3d-touch.md) documentazione per ulteriori informazioni.

## <a name="widgets-developer-suggestions"></a>Suggerimenti per sviluppatori di widget

Idealmente, lo sviluppatore deve sempre provare e progettare widget che l'utente desidera aggiungere gli schermi di ricerca. A tal fine, Apple ha i suggerimenti seguenti:

- **Creare un'eccellente, esperienza concise** -dell'utente desidera widget che forniscono informazioni concise breve degli aggiornamenti di stato o consentire loro di eseguire semplici operazioni più rapidamente. Rende la giusta quantità di informazioni e l'interattività un essenziali. Quando possibile, consentire all'utente di eseguire un'attività specifica con un unico tocco. Inoltre, poiché widget non supportano la panoramica o lo scorrimento, questo dovrà essere prese in considerazione nella progettazione del Widget.
- **Mostra contenuto rapidamente** -widget sono progettati per essere concise, in modo che l'utente deve essere in attesa per il contenuto da caricare quando viene visualizzato un Widget. Widget devono memorizzare nella cache il contenuto localmente in modo da poter dimostrare contenuto recente durante il caricamento di contenuto aggiornato in background.
- **Specificare i margini e spaziatura interna appropriato** -widget deve mai essere talmente, pertanto è consigliabile evitare l'estensione di contenuto per i bordi della visualizzazione di un Widget. Deve esistere sempre un margine wide pixel diversi tra i bordi e il contenuto. Apple vengono inoltre suggerite facendo clic sull'icona dell'app, visualizzata nella parte superiore del Widget, come una Guida di allineamento. Se il Widget presenta un layout di griglia, verificare che sia presente corretto riempimento tra gli elementi nella griglia e provare a limitare il numero di elementi al massimo quattro.
- **Utilizzare layout adattabile** -larghezza del Widget A variano a seconda del dispositivo in cui viene eseguito, l'orientamento del dispositivo. Altezza di un Widget può variare in base anche se viene visualizzato nello stato compresso (impostazione predefinita) o espansa (non supportato da tutti i widget). Un Widget compresso con un'altezza di righe della tabella standard iOS circa due e mezzo. Lo sviluppatore può richiedere le dimensioni per un Widget espanso ma idealmente deve essere inferiore all'altezza dello schermo. Nello stato compresso, il Widget deve visualizzare informazioni essenziali solo autonome. Quando espanso, il Widget deve visualizzare informazioni aggiuntive che migliora il principale contenuto visualizzato nello stato compresso. Nell'elenco di azioni rapide widget sarà solo nello stato compresso.
- **Non personalizzare sfondo del Widget** -widget vengono visualizzati su uno sfondo chiaro, sfocato fornito dal sistema. Questa operazione viene eseguita per promuovere la coerenza tra widget e migliorare la leggibilità del contenuto. Evitare di utilizzare un'immagine come sfondo Widget potrebbe essere in conflitto con sfondi di blocco e schermata iniziale dell'utente.
- **Utilizzare il carattere di sistema in nero o grigio scuro** : quando la visualizzazione di testo in un Widget, il carattere di sistema è più adatta. Il tipo di carattere deve essere un colore grigio scuro o di colore nero in risalto sullo sfondo della Widget chiaro, sfocato.
- **Fornire App accesso quando appropriato** -Widget deve sempre possono essere usate separatamente dall'applicazione, tuttavia, se è necessaria la funzionalità più approfondita, il Widget deve essere in grado di avviare l'app per visualizzare o modificare una parte specifica di informazioni. Un pulsante "Apri app", non includere mai semplicemente consentire all'utente di toccare il contenuto stesso e mai aperta una 3rd party app.
- **Selezionare un chiaro, conciso nome Widget** -icona dell'applicazione e il nome del Widget vengono sempre visualizzati sul contenuto del Widget. Apple suggerisce l'utilizzo dell'App per il Widget primario e un nome chiaro e conciso per tutte le altre che fornisce. Quando si specifica un titolo del Widget personalizzati, deve essere preceduti con il nome dell'applicazione (ad esempio mappe nelle vicinanze, mappe ristoranti e così via).
- **Che indicano quando l'autenticazione aggiunge valore** : se la funzionalità aggiuntiva o informazioni è disponibile solo quando l'utente viene autenticato e ha effettuato l'accesso, presentare all'utente. Ad esempio, un interscambio la pronuncia "Accedi al marcia libro" app di condivisione.
- **Selezionare un Widget di elenco azione rapida** -se l'applicazione fornisce più di un Widget, lo sviluppatore deve scegliere quello per presentare quando l'utente visualizza l'elenco di azioni rapide applicando pressione sull'icona dell'applicazione tramite tocco 3D.

Per ulteriori informazioni sull'uso di widget, consultare il nostro [Introduzione alle estensioni](~/ios/platform/extensions.md), [introduzione 3D Touch](~/ios/platform/3d-touch.md) Apple e la documentazione [Guida per programmatori App estensione](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html).

## <a name="working-with-vibrancy"></a>Utilizzo di vitalità

Vitalità assicura che il testo di un Widget rimanga leggibile quando presentati nella luce del Widget, sfondo sfocato (fornito dal sistema). Prima di iOS 10, lo sviluppatore utilizzerebbe una [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) per vitalità del Widget. Ad esempio:

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

Questo è deprecato in iOS 10 e deve essere sostituito con un [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect). Ad esempio:

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>Operazioni con i widget di espandere e comprimere

Nuovo in iOS 10, widget contengono ora un [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) proprietà che consente agli sviluppatori di descrivere la quantità di contenuto è disponibile e consente all'utente di espandere e comprimere il contenuto.

Quando un Widget viene inizialmente visualizzato, è in uno stato compresso. Un Widget compresso con un'altezza di righe della tabella standard iOS circa due e mezzo. Lo sviluppatore può richiedere le dimensioni per un Widget espanso ma idealmente deve essere inferiore all'altezza dello schermo. 

Nello stato compresso, il Widget deve visualizzare informazioni essenziali solo autonome. Quando espanso, il Widget deve visualizzare informazioni aggiuntive che migliora il principale contenuto visualizzato nello stato compresso. Ad esempio, Weather app Mostra meteorologiche quando compresso e aggiunge l'orario previsione quando viene espanso.

Nell'elenco di azioni rapide widget sarà solo nello stato compresso. Se l'applicazione fornisce più di un Widget, lo sviluppatore deve scegliere quello per presentare quando l'utente visualizza l'elenco di azioni rapide applicando pressione sull'icona dell'applicazione tramite tocco 3D.

L'esempio seguente è un semplice oggi dell'estensione di (il Widget) che gestisce gli stati compressa ed espansa:

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

Esaminare il codice specifico di Widget modalità di visualizzazione in modo dettagliato. Per informare il sistema che questo Widget supporta lo stato espanso, Usa:

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

Per ottenere la modalità di visualizzazione del Widget, Usa:

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

Per ottenere la dimensione massima per lo stato espanso o compresso, Usa:

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

Oltre a impostare la dimensione richiesta per ogni stato (compresso o espanso), viene anche aggiornato il contenuto visualizzato affinché corrisponda alla nuova dimensione.

## <a name="summary"></a>Riepilogo

In questo articolo ha interessati i miglioramenti che Apple apportate al sistema di Widget in iOS 10 e illustrato come implementarli in xamarin. IOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Introduzione alle estensioni](~/ios/platform/extensions.md)
- [Introduzione a 3D Touch](~/ios/platform/3d-touch.md)
- [Guida per programmatori di estensione dell'App](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
