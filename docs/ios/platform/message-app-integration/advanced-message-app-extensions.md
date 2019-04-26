---
title: Estensioni dell'App messaggi avanzate in xamarin. IOS
description: Questo articolo illustra le tecniche avanzate per l'utilizzo di estensioni di App di messaggio in una soluzione xamarin. IOS che si integra con l'app di messaggi e presenta nuove funzionalità all'utente.
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: baceb59116dd907918b34eca4f44293051190954
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61155569"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>Estensioni dell'App messaggi avanzate in xamarin. IOS

_Questo articolo illustra le tecniche avanzate per l'utilizzo di estensioni di App di messaggio in una soluzione xamarin. IOS che si integra con l'app di messaggi e presenta nuove funzionalità all'utente._


Nuovo ai dispositivi iOS 10, un'estensione dell'App messaggi si integra con il **messaggi** app e presenta nuove funzionalità per l'utente. L'estensione può inviare testo, le etichette, i file multimediali e i messaggi interattivi.

## <a name="about-message-app-extensions"></a>Informazioni sulle estensioni App messaggi

Come indicato in precedenza, un'estensione dell'App messaggi si integra con il **messaggi** app e presenta nuove funzionalità per l'utente. L'estensione può inviare testo, le etichette, i file multimediali e i messaggi interattivi. Sono disponibili due tipi di estensione per App i messaggi:

- **Pack adesivo** -contiene una raccolta di adesivi che l'utente può aggiungere a un messaggio. È possibile creare pacchetti di adesivi senza scrivere alcun codice.
- **iMessage App** -possono presentare un'interfaccia utente personalizzata nell'app messaggi per la selezione di adesivi, immettendo il testo, inclusi i file multimediali (con conversioni del tipo facoltativo) e creazione, modifica e l'invio di messaggi di interazione.

Le estensioni delle App messaggio offrono tre tipi di contenuto principali:

- **I messaggi interattivi** -sono un tipo di contenuto del messaggio personalizzato che genera un'app, quando l'utente tocca il messaggio, l'app verrà avviata in primo piano.
- **Adesivi** -sono immagini generate dall'app che può essere incluso nei messaggi inviati tra gli utenti. Vedere la [Ice cream generatore](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) app di esempio per un esempio di implementazione di un'app di Service Pack adesivo.
- **Altro contenuto supportato** -l'app può fornire contenuto, ad esempio foto, video, testo o collegamenti di tipo che è sempre stato supportato da app Messages.

Nuovo ai dispositivi iOS 10, l'app messaggio include ora un proprio Store di App predefinito, dedicato. Qualsiasi App che includono le estensioni delle App messaggio verrà visualizzata e innalzate di livello in questo archivio. Nuovi messaggi del pannello delle App verranno visualizzati le app che sono state scaricate da Store di App di messaggi per fornire accesso rapido agli utenti.

Anche nuovi in iOS 10, Apple ha aggiunto App Attribution Inline che consente all'utente di individuare facilmente un'app. Ad esempio, se un utente invia il contenuto a un altro da un'app che l'utente 2 ° non è installato (ad esempio, un'etichetta applicata, ad esempio), il nome dell'app per l'invio è elencato sotto il contenuto nella cronologia del messaggio. Se l'utente tocca dell'app il nome, Store di App il messaggio è possibile aprire e l'app selezionata nell'archivio.

Le estensioni delle App messaggio sono simili alle App iOS esistenti che lo sviluppatore è familiare per la creazione e avranno accesso a tutti i framework di standard e le funzionalità di un'app per iOS standard. Ad esempio:

- Hanno accesso a acquisto In-App.
- Hanno accesso a Apple Pay.
- Hanno accesso a hardware, ad esempio la fotocamera del dispositivo.

Messaggio le estensioni delle App sono supportate solo in iOS 10, tuttavia, il contenuto che inviano queste estensioni è visualizzabile nei dispositivi macOS e watchOS. Il nuovo _Recents Page_ aggiunto a watchOS 3, verranno visualizzati adesivi recenti che sono stati inviati dal telefono, comprese quelle di messaggio le estensioni delle App, e consentire all'utente inviare tali adesivi dall'orologio.

## <a name="about-interactive-messages"></a>Informazioni sui messaggi interattivi

I messaggi interattivi presentano una bolla di messaggio personalizzata e vengono forniti da un'estensione dell'App messaggi. Essi consentono all'utente di creare messaggi interattivi contenuti, inserirla nel campo di Input del messaggio e inviarlo.

[![](advanced-message-app-extensions-images/interactive01.png "Creazione di contenuto del messaggio interattivo")](advanced-message-app-extensions-images/interactive01.png#lightbox)

L'utente ricevente può rispondere a un messaggio interattivo toccando relativo delle bolle messaggio nella cronologia di messaggio per caricare l'estensione dell'App messaggi che l'ha creata. L'estensione verrà avviata a schermo intero e consentire all'utente di comporre una risposta e inviarlo all'utente di origine.

[![](advanced-message-app-extensions-images/interactive02.png "L'estensione avviata a schermo intero")](advanced-message-app-extensions-images/interactive02.png#lightbox)


I seguenti argomenti verranno trattati in dettaglio di seguito:

- Panoramica dell'API dei messaggi
- Il ciclo di vita di estensione
- Il messaggio.
- Invia un messaggio

## <a name="messages-api-overview"></a>Panoramica dell'API dei messaggi

Quando viene richiamato dall'utente, verrà visualizzata un messaggio di estensione dell'App nella parte inferiore della cronologia del messaggio nella modalità di visualizzazione compatta:

[![](advanced-message-app-extensions-images/interactive03.png "Panoramica dell'API dei messaggi")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. Il `MSMessageAppViewController` oggetti nell'estensione di App di messaggio sono la classe principale che viene chiamata quando all'utente verrà aperta la visualizzazione dell'estensione.
2. La conversazione viene presentata all'utente come un `MSConversation` istanza dell'oggetto.
3. Il `MSMessage` classe rappresenta una bolla messaggio specificato nella conversazione.
4. `MSSession` Controlla come viene inviato un messaggio.
5. `MSMessageTemplateLayout` Controlla come viene visualizzato il messaggio

## <a name="the-extension-lifecycle"></a>Il ciclo di vita di estensione

Esaminiamo il processo di un'estensione dell'App messaggi diventa attivo:

[![](advanced-message-app-extensions-images/interactive04.png "Il processo di un'estensione dell'App messaggi diventa attivo")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. Quando viene avviata un'estensione (ad esempio dal pannello delle App), l'app messaggio avvierà un processo.
2. Il `DidBecomeActive` metodo viene chiamato e passato un `MSConversation` che rappresenta la conversazione in cui è in esecuzione l'estensione dell'App messaggi.
3. Poiché l'estensione è basato su un `UIViewController` entrambe `ViewWillAppear` e `ViewDidAppear` vengono chiamati.

Quindi, esaminiamo il processo di un messaggio dell'estensione dell'App sta diventando disattivata:

[![](advanced-message-app-extensions-images/interactive05.png "Il processo di un messaggio dell'estensione dell'App sta diventando disattivato")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. Quando viene disattivata l'estensione dell'App messaggi, il `ViewWillDisappear` metodo verrà chiamato prima di tutto.
2. Il `ViewDidDisappear` metodo verrà chiamato.
3. Il `WillResignActive` metodo viene chiamato e passato un `MSConversation` che rappresenta la conversazione in cui è in esecuzione l'estensione dell'App messaggi. A questo punto, la connessione tra l'app di messaggi e l'estensione sta per essere rilasciata.
4. Successivamente, il processo viene terminato dall'app per i messaggi.

Poiché un'estensione è un processo di breve durato, viene terminata in modo aggressivo dal sistema per risparmiare energia della batteria e l'elaborazione. Lo sviluppatore deve tenere a mente durante la progettazione e implementazione di un'estensione dell'App messaggi.

## <a name="composing-a-message"></a>Il messaggio.

Dopo che l'estensione dell'App messaggi è in esecuzione in un processo ed è visualizzata l'interfaccia utente, il codice seguente è utilizzabile per creare un nuovo messaggio:

```csharp
MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
{
    var components = new NSUrlComponents {
        QueryItems = iceCream.QueryItems
    };

    var layout = new MSMessageTemplateLayout {
        Image = iceCream.RenderSticker (true),
        Caption = caption
    };

    var message = new MSMessage (session ?? new MSSession()) {
        Url = components.Url,
        Layout = layout
    };

    return message;
}
```

Questo codice crea un nuovo `MSMessage` e imposta diverse proprietà (ad esempio `Url`). Mentre il messaggio può essere creato solo per iOS, possono essere inviato per iOS e macOS da visualizzare.

Se l'utente fa clic su una bolla messaggio nella conversazione in macOS, il Mac tenterà di aprire l'indirizzo specificato nell'URL nel web browser. Di conseguenza, sito Web per gli sviluppatori dovrebbe essere in grado di mostrare computer basati su alcuni rappresentazione del messaggio nel browser web in macOS.

Il `AccessibilityLabel` proprietà viene utilizzata per gli screen reader per leggere la trascrizione della conversazione all'utente. Il `Layout` proprietà specifica come il messaggio verrà visualizzato, attualmente solo il `MSMessageTemplateLayout` è supportato ed è simile a quanto segue:

[![](advanced-message-app-extensions-images/interactive06.png "Il modello MSMessageTemplateLayout")](advanced-message-app-extensions-images/interactive06.png#lightbox)

Il `Image` proprietà del `MSMessageTemplateLayout` fornisce contenuto per il corpo principale del MessageBubble sullo schermo. Il `MediaFileUrl` proprietà inoltre fornisce contenuto per il corpo della bolla messaggio, ma consente il contenuto che non è supportato da `UIImage` (ad esempio un file video che verrebbe ciclo in background). Se entrambi i `Image` e `MediaFileUrl` vengono fornite proprietà, il `Image` proprietà avrà la precedenza. Il `MediaFileUrl` supporta il formato PNG, JPEG, GIF e video (in qualsiasi formato che può essere riprodotto dal framework di Media Player) formati di file multimediali.

La dimensione media consigliato è 300 x 300 pixel con la risoluzione 3 x. Sono accettati anche asset leggermente maggiori e minori e suggerite da Apple con alcune dimensioni diverse per ottenere i migliori risultati di test. L'App di messaggio verranno sottocampionare e ridimensionare il supporto in base alle esigenze.

Quando gli asset vengono inviati al ricevitore, qualsiasi contenuto multimediale allegato verrà automaticamente transcodificati dall'app per i messaggi per l'ottimizzazione di trasferimento attraverso le reti. Per questo motivo, Apple, scoraggia tra cui testo nel file multimediale associato al messaggio, in quanto multimediali verranno ridotto e compresso per la trasmissione, quindi potenzialmente il rendering del testo illeggibili.

Il `ImageTitle` e `ImageSubtitle` proprietà forniscono una descrizione per il supporto presentate nel fumetto del messaggio. Queste proprietà verranno inviate come testo per il dispositivo di ricezione in cui verranno sottoposti nettamente a rendering nell'angolo inferiore sinistro dell'immagine.

Il `Caption`, `SubCaption`, `TrailingCaption` e `TrailingSubcaption` proprietà ulteriormente descrivono l'immagine e verrà eseguito il rendering in una sezione sotto l'immagine. Impostare tutte queste proprietà a `null` creerà una bolla messaggio senza Area della didascalia:

[![](advanced-message-app-extensions-images/interactive07.png "Una bolla messaggio senza Area della didascalia")](advanced-message-app-extensions-images/interactive07.png#lightbox)

L'ultimo aspetto da notare è che l'app Messages verrà disegnato sull'icona dell'estensione App messaggio nell'angolo superiore sinistro della bolla messaggio.

## <a name="sending-a-message"></a>Invia un messaggio

Una volta un `MSMessage` è stato composto, utilizzabile per inviare il codice seguente:

```csharp
public void SendMessage (MSMessage message)
{
    // Insert the message into the conversation
    ActiveConversation.InsertMessage (message, (error) => {
        // Did the message send successfully?
        if (error == null) {
            // Handle successful send
        } else {
            // Report Error
            Console.WriteLine ("Error: {0}", error);
        }
    };

}
```

Il `ActiveConversation` proprietà del `MSMessagesAppViewController` conterrà la conversazione corrente che il messaggio di estensione dell'App è stata avviata in.

Chiamare il `InsertMessage` del `MSConversation` per includere il messaggio nella conversazione e gestire eventuali errori che potrebbero verificarsi. Se il messaggio è incluso correttamente, verrà visualizzato il fumetto del messaggio nel campo di Input.

Inoltre, l'estensione è possibile inviare diversi tipi di dati per la conversazione, ad esempio:

- **Text** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **Allegati** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **Adesivi**  -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});` in cui `sticker` è un `MSSticker`.

Dopo aver aggiunto il nuovo contenuto del campo di Input, l'utente è in grado di inviare il messaggio, toccare il blu **inviare** pulsante (esattamente come farebbero qualsiasi messaggio tipico). Non c'è alcun modo per l'estensione di App di messaggio per l'invio automatico di contenuto, questo processo è completamente sotto il controllo dell'utente.

## <a name="handling-the-compact-and-expanded-modes"></a>Gestisce le modalità compatta ed espanse

Un'estensione dell'App messaggi può essere visualizzata in una delle due modalità di visualizzazione diverse:

[![](advanced-message-app-extensions-images/interactive08.png "Un'estensione dell'App messaggi visualizzati in due modalità di visualizzazione diverse: Compact & espanso")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -si tratta della modalità predefinita in cui l'estensione dell'App messaggi occupa il 25% nella parte inferiore della visualizzazione messaggio. In modalità ridotta, l'app non ha accesso alla tastiera, scorrimento orizzontale o i riconoscitori di movimento scorrimento rapido. Le app hanno accesso al campo di Input e le chiamate a `InsertMessage` verrà immediatamente visualizzata all'utente non esiste.
- **Espanso** -l'estensione per App i messaggi riempie l'intera visualizzazione messaggio. Non ha accesso al campo di Input, ma hanno accesso alla tastiera, scorrimento orizzontale e i riconoscitori di movimento scorrimento rapido.

Un'estensione dell'App messaggi possono essere alternata tra le modalità a livello di codice o manualmente dall'utente in qualsiasi momento e deve essere immediatamente reattiva a qualsiasi modifica nella visualizzazione modalità.

Esaminiamo l'esempio seguente di gestire il passaggio tra le due modalità di visualizzazione diverse. Due controller di visualizzazione diversi sarà necessario per ogni stato. Il `StickerBrowserViewController` gestisce il **Compact** visualizzazione e il `AddStickerViewController` gestirà il **espansa** visualizzazione:

```csharp
using System;

using Messages;
using Foundation;
using UIKit;

namespace MessagesExtension {
    public partial class MessagesViewController : MSMessagesAppViewController, IIceCreamsViewControllerDelegate, IBuildIceCreamViewControllerDelegate {
        public MessagesViewController (IntPtr handle) : base (handle)
        {
        }

        public override void WillBecomeActive (MSConversation conversation)
        {
            base.WillBecomeActive (conversation);

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, PresentationStyle);
        }

        public override void WillTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected an active converstation");

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, presentationStyle);
        }

        void PresentViewController (MSConversation conversation, MSMessagesAppPresentationStyle presentationStyle)
        {
            // Determine the controller to present.
            UIViewController controller;

            if (presentationStyle == MSMessagesAppPresentationStyle.Compact) {
                // Show a list of previously created ice creams.
                controller = InstantiateIceCreamsController ();
            } else {
                var iceCream = new IceCream (conversation.SelectedMessage);
                controller = iceCream.IsComplete ? InstantiateCompletedIceCreamController (iceCream) : InstantiateBuildIceCreamController (iceCream);
            }

            foreach (var child in ChildViewControllers) {
                child.WillMoveToParentViewController (null);
                child.View.RemoveFromSuperview ();
                child.RemoveFromParentViewController ();
            }

            AddChildViewController (controller);
            controller.View.Frame = View.Bounds;
            controller.View.TranslatesAutoresizingMaskIntoConstraints = false;
            View.AddSubview (controller.View);

            controller.View.LeftAnchor.ConstraintEqualTo (View.LeftAnchor).Active = true;
            controller.View.RightAnchor.ConstraintEqualTo (View.RightAnchor).Active = true;
            controller.View.TopAnchor.ConstraintEqualTo (View.TopAnchor).Active = true;
            controller.View.BottomAnchor.ConstraintEqualTo (View.BottomAnchor).Active = true;

            controller.DidMoveToParentViewController (this);
        }

        UIViewController InstantiateIceCreamsController ()
        {
            // Instantiate a `IceCreamsViewController` and present it.
            var controller = Storyboard.InstantiateViewController (IceCreamsViewController.StoryboardIdentifier) as IceCreamsViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an IceCreamsViewController from the storyboard");

            controller.Builder = this;
            return controller;
        }

        UIViewController InstantiateBuildIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (BuildIceCreamViewController.StoryboardIdentifier) as BuildIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an BuildIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            controller.Builder = this;

            return controller;
        }

        public UIViewController InstantiateCompletedIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (CompletedIceCreamViewController.StoryboardIdentifier) as CompletedIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an CompletedIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            return controller;
        }

        public void DidSelectAdd (IceCreamsViewController controller)
        {
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void Build (BuildIceCreamViewController controller, IceCreamPart iceCreamPart)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected a conversation");

            var iceCream = controller.IceCream;
            if (iceCream == null)
                throw new Exception ("Expected the controller to be displaying an ice cream");

            string messageCaption = string.Empty;
            var b = iceCreamPart as Base;
            var s = iceCreamPart as Scoops;
            var t = iceCreamPart as Topping;

            if (b != null) {
                iceCream.Base = b;
                messageCaption = "Let's build an ice cream";
            } else if (s != null) {
                iceCream.Scoops = s;
                messageCaption = "I added some scoops";
            } else if (t != null) {
                iceCream.Topping = t;
                messageCaption = "Our finished ice cream";
            } else {
                throw new Exception ("Unexpected type of ice cream part selected.");
            }

            // Create a new message with the same session as any currently selected message.
            var message = ComposeMessage (iceCream, messageCaption, conversation.SelectedMessage?.Session);

            // Add the message to the conversation.
            conversation.InsertMessage (message, null);

            // If the ice cream is complete, save it in the history.
            if (iceCream.IsComplete) {
                var history = IceCreamHistory.Load ();
                history.Append (iceCream);
                history.Save ();
            }

            Dismiss ();
        }

        MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
        {
            var components = new NSUrlComponents {
                QueryItems = iceCream.QueryItems
            };

            var layout = new MSMessageTemplateLayout {
                Image = iceCream.RenderSticker (true),
                Caption = caption
            };

            var message = new MSMessage (session ?? new MSSession()) {
                Url = components.Url,
                Layout = layout
            };

            return message;
        }
    }
}
```

Il `DidTransition` è sottoposto a override per gestire il cambio tra le due modalità:

```csharp
public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
{
    base.DidTransition (presentationStyle);

    // Take action based on style
    switch (presentationStyle) {
    case MSMessagesAppPresentationStyle.Compact:
        PresentStickerBrowser ();
        break;
    case MSMessagesAppPresentationStyle.Expanded:
        PresentAddSticker ();
        break;
    }
}
```

Facoltativamente, l'app sarebbe possibile utilizzare il `WillTransition` metodo per gestire la modifica di modalità di visualizzazione prima di essere presentati all'utente (come avviene nell'esempio precedente generatore Icecream). Per altre informazioni, vedere la [un'ulteriore personalizzazione degli adesivi](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) documentazione.

## <a name="replying-to-a-message"></a>Risposta a un messaggio

Esistono due casi che dovrà gestire durante la risposta a un messaggio di un'estensione dell'App messaggi:

[![](advanced-message-app-extensions-images/interactive09.png "L'estensione dell'App messaggi nelle modalità attivo e inattivo")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **L'estensione è inattivo** -vi è uno dei fumetti per messaggi dell'estensione App messaggio nella trascrizione messaggio che l'utente può toccare per attivare le estensioni e continuare la conversazione interattiva.
- **L'estensione è attivo** -l'utente può toccare messaggio bolle dell'estensione App messaggio nella trascrizione messaggio per passare alla modalità di visualizzazione espansa e continuare il processo interattivo da dove si era interrotto.

### <a name="the-extension-is-inactive"></a>L'estensione è inattivo

Quando una bolla messaggi toccando dall'utente nella finestra di trascrizione di messaggio e l'estensione dell'App messaggi è inattivo, verrà eseguito il processo seguente:

[![](advanced-message-app-extensions-images/interactive10.png "La gestione di una bolla messaggio inattivo")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. L'utente tocca a bolle di messaggio dell'estensione.
2. Quando un'estensione viene avviata, l'app messaggio avvierà un processo.
3. Il `DidBecomeActive` metodo viene chiamato e passato un `MSConversation` che rappresenta la conversazione in cui è in esecuzione l'estensione dell'App messaggi.
4. Poiché l'estensione è basato su un `UIViewController` entrambe `ViewWillAppear` e `ViewDidAppear` vengono chiamati.

Una volta completato il processo, l'estensione dell'App il messaggio verrà visualizzato nella modalità di visualizzazione espansa.

### <a name="the-extension-is-active"></a>L'estensione è attivo

Quando una bolla messaggi toccando dall'utente nella finestra di trascrizione di messaggio e l'estensione dell'App messaggi è attiva, verrà eseguito il processo seguente:

[![](advanced-message-app-extensions-images/interactive11.png "La gestione di una bolla messaggi attivi")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. L'utente tocca a bolle di messaggio dell'estensione.
2. Poiché l'estensione dell'App il messaggio è già attivo, il `WillTransition` metodo del `MSMessagesAppViewController` viene chiamato per gestire il passaggio da .NET Compact Framework per la modalità di visualizzazione espansa.
3. Il `DidSelectMessage` metodo per il `MSMessagesAppViewController` viene chiamato e passato il `MSMessage` e `MSConversation` a cui appartiene il fumetto del messaggio.
4. Il `DidTransition` metodo di `MSMessagesAppViewController` viene chiamato per gestire il passaggio da .NET Compact Framework per la modalità di visualizzazione espansa.

Anche in questo caso, una volta completato il processo, l'estensione dell'App il messaggio verrà visualizzato nella modalità di visualizzazione espansa.

### <a name="accessing-the-selected-message"></a>Accede al messaggio selezionato

In entrambi i casi, quando l'utente tocca una bolla di messaggi che appartengono all'estensione di App di messaggio, sarà necessario effettuare l'accesso per il `MSMessage` che è stato toccato usando la `SelectedMessage` proprietà del `MSConversation`.

Ad esempio:

```csharp
using System;
using Foundation;
using Messages;
using UIKit;


namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        ...

        #region Override Methods
        public override void DidSelectMessage (MSMessage message, MSConversation conversation)
        {
            base.DidSelectMessage (message, conversation);

            // Get selected message
            var selected = conversation.SelectedMessage;

            // Present the user interface to continue editing
            // the conversation
            ...
        }
        #endregion
    }
}
```

Il messaggio selezionato deve essere visualizzato nell'interfaccia utente dell'estensione App messaggio e l'utente dovrebbe essere autorizzato per comporre una risposta.

## <a name="removing-partially-completed-messages"></a>Rimozione parzialmente completato messaggi

In corso l'invio di diversi passaggi per una conversazione interattiva tra l'utente due nella conversazione, i fumetti per messaggi parzialmente completata è possibile iniziare a creare confusione la trascrizione del messaggio:

[![](advanced-message-app-extensions-images/interactive12.png "I fumetti per messaggi parzialmente completato può sovraccaricare la trascrizione del messaggio")](advanced-message-app-extensions-images/interactive12.png#lightbox)

Al contrario, l'estensione dell'App messaggi deve comprimere il precedente fumetti per messaggi in un breve commento nella trascrizione messaggio:

[![](advanced-message-app-extensions-images/interactive13.png "Comprimere i fumetti per messaggi precedenti nella trascrizione messaggio")](advanced-message-app-extensions-images/interactive13.png#lightbox)

Questa operazione viene gestita tramite un `MSSession` per comprimere tutti i passaggi esistenti. In modo che il `DidSelectMessage` metodo di `MSMessagesAppViewController` classe può essere modificata per essere simile al seguente:

```csharp
public override void DidSelectMessage (MSMessage message, MSConversation conversation)
{
    base.DidSelectMessage (message, conversation);

    MSSession session;
    var summary = "";

    // Get selected message
    var selected = conversation.SelectedMessage;

    // Does the selected message already have a session?
    if (selected.Session == null) {
        // No, create a new session
        session = new MSSession ();
        summary = "Let's build an ice cream";
    } else {
        // Yes, use the existing session
        session = selected.Session;
        summary = "I added some scoops";
    }

    // Create an instance of the message being composed
    var existingMessage = new MSMessage (session);
    message.SummaryText = summary;
    ...

    // Present the user interface to continue editing
    // the conversation
    ...
}
```

Se il messaggio selezionato dispone già di una chiusura `MSSession`, viene usato altrimenti una nuova `MSSession` viene creato. Il `SummaryText` proprietà del `MSMessage` viene usato per aggiungere una didascalia per i passaggi precedenti compressi. Se il `SummaryText` è impostata su `null`, i passaggi precedenti nella conversazione verranno rimossa completamente dalla trascrizione di conversazione.

## <a name="advanced-message-api-features"></a>Messaggio API funzionalità avanzate

Con le funzionalità di base della nuova API messaggio illustrato in dettaglio in precedenza, quindi esaminare alcune delle funzionalità più avanzate che Apple ha integrato in framework.

In primo luogo, esistono diversi altri metodi di override nel `MSMessagesAppViewController` classi che forniscono accesso più diretto per la conversazione:

- `DidStartSendingMessage` -Viene chiamato quando l'utente tocca il pulsante di invio. Ciò non significa che il messaggio sia stato effettivamente il recapito al destinatario, solo che sia stato avviato il processo di trasmissione.
- `DidCancelSendingMessage` -Ciò si verifica quando l'utente tocca il *X* pulsante nell'angolo superiore destro della bolla messaggio nella conversazione trascrizione.
- `DidReceiveMessage` -Questo metodo viene chiamato quando l'estensione dell'App messaggi è attiva un nuovo messaggio ricevuto da uno dei partecipanti alla conversazione.

### <a name="group-conversations"></a>Conversazioni di gruppo

Un'estensione dell'App messaggi utilizzabile anche se gli utenti sono coinvolti nelle conversazioni di gruppo (con 3 o più utenti singoli) e ciò sarà necessario prendere in considerazione durante la progettazione e implementazione di un'estensione dell'App messaggi.

Esaminiamo l'interazione seguente in una conversazione di gruppo con tre utenti:

[![](advanced-message-app-extensions-images/interactive14.png "Interazione in una conversazione di gruppo con tre utenti")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. L'utente 1 invia un messaggio interattivo di gruppo in cui viene chiesto utente 2 e 3 utente di scegliere un cliente rimane attualmente ai vertici.
2. L'utente 2 sceglie tomatoes.
3. Utente 3 sceglie sottaceti.
4. Opzioni utente 2 sia utente 3 arrivano al 1 utente quasi nello stesso momento. Di conseguenza, la scelta dell'utente 2 viene compressa in una riga di riepilogo e non è disponibile. In questo caso potrebbe avere anche capovolte, con la scelta dell'utente 2 di compresso riprodotti e User 3.

In entrambi i casi, questo comportamento è indesiderato 1 utente dovrebbe essere in grado di accedere sia utente 2 e User 3 opzioni. Per gestire questa situazione, Apple sta suggerendo che l'estensione dell'App messaggi archivia lo stato del messaggio nel cloud e usare la proprietà URL del `MSMessage` (che ottiene inviati tra gli utenti) per accedere a questo stato.

Quando l'utente invia un messaggio, un token di sessione viene generato e il push nel cloud con lo stato del messaggio corrente. Quando un utente tocca su una bolla di messaggio nella conversazione trascrizione, il token di sessione consente di recuperare lo stato della sessione corrente dal cloud.

### <a name="sender-identifiers"></a>Identificatori di mittente

Per discutere l'identificatore del mittente di un messaggio di accesso, eseguire l'esempio di una conversazione di gruppo in precedenza:

[![](advanced-message-app-extensions-images/interactive15.png "L'invio di identificatori di conversazione di gruppo")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. Anche in questo caso 1 utente invia un messaggio interattivo di gruppo in cui viene chiesto utente 2 e 3 utente di scegliere un cliente rimane attualmente ai vertici.
2. Utente 3 sceglie sottaceti.
3. Scelta dell'utente del 3 arriva all'utente 1 e 2 di utente non ha ancora risposto.
4. Perché Apple è molto interessata privacy dell'utente, l'estensione dell'App messaggi sa solo su un identificatore univoco (come un `NSUUID`) che viene assegnato a ogni partecipante alla conversazione. Nel dispositivo locale, è noto solo l'identificatore dell'utente corrente.
5. Il `MSMessage` ha un `SenderIdentifier` proprietà che corrisponde a uno dell'utente nell'elenco di partecipanti nota per l'estensione.
6. Ogni dispositivo utente ha una propria copia dell'elenco di partecipanti in cui anche in questo caso, è noto solo l'identificatore dell'utente locale.
7. Quando viene inviato un messaggio, relativo `SenderIdentifier` proprietà è nota anche a essere quella dell'utente locale.

Gli identificatori di mittente può essere utilizzati nei modi seguenti:

- Esaminando l'elenco di partecipanti l'estensione è possibile ottenere il numero di utenti nella conversazione.
- Quando l'estensione riceve un messaggio da un utente, è possibile tenere traccia dell'identificatore del mittente. Se riceve un altro messaggio con lo stesso identificatore mittente, l'estensione sa che è dello stesso utente.
- Possono essere utilizzati per identificare un utente specifico nella conversazione.

L'identificatore del mittente può essere usata in qualsiasi dei campi di testo il `MSMessageTemplateLayout` aggiungendo il prefisso con un segno di dollaro (`$`). Ad esempio:

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

Quando l'app messaggi Visualizza una bolla di messaggio con questo tipo di formattazione, che sostituirà il `$uuid...` con il nome del contatto del partecipante alla conversazione che ha inviato il messaggio.

L'identificatore del mittente è univoco in ogni dispositivo, quindi esaminando il diagramma precedente si noti di nuovo dispositivo di 1 utente e dispositivo dell'utente 3 ha un identificatore mittente univoci per ogni partecipante alla conversazione.

Gli identificatori di mittente hanno come ambiti l'installazione dell'estensione per App messaggi. Pertanto, se un utente Disinstalla e reinstalla l'estensione dell'App messaggi nuovi identificatori di mittente verrà generati per la nuova installazione.

Per accedere gli identificatori di mittente, l'estensione può utilizzare il codice seguente:

```csharp
public override void DidStartSendingMessage (MSMessage message, MSConversation conversation)
{
    base.DidStartSendingMessage (message, conversation);

    // Get the sender's participant ID
    var senderID = message.SenderParticipantIdentifier;

    // Get the local participant ID
    var localID = conversation.LocalParticipantIdentifier;

    // Get the remote participant IDs
    var remoteIDs = conversation.RemoteParticipantIdentifiers;
}
```

## <a name="supported-platforms"></a>Piattaforme supportate

I messaggi interattivi, generati da un'estensione dell'App messaggio venga recapitati con le seguenti piattaforme Apple:

- watchOS 3
- macOS Sierra
- iOS 10

Delle tre piattaforme, iOS solo 10 consentirà all'utente generare un messaggio interattivo. In macOS Sierra, se l'utente fa clic su una bolla messaggio interattivo, l'URL collegato al `MSMessage` verrà aperto in Safari e una rappresentazione del messaggio deve essere visualizzata lì.

App Messages watchOS, possibile handoff un messaggio interattivo in un dispositivo iOS collegato in cui l'utente può creare una risposta.

La nuova API di messaggi include il supporto di fallback se viene ricevuto il messaggio interattivo su piattaforme precedenti Apple:

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

Essi verrà recapitati in un formato di fallback come due messaggi distinti:

- Uno sarà l'immagine fornita dal Layout del modello.
- L'altro sarà costituito dall'URL fornito il `MSMessage`.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato le tecniche avanzate per l'utilizzo di estensioni di App di messaggio in una soluzione xamarin. IOS che si integra con il **messaggi** presenti nuove funzionalità per l'utente e app.


## <a name="related-links"></a>Collegamenti correlati

- [Generatore ice cream (esempio)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Riferimento dei messaggi](https://developer.apple.com/reference/messages)
- [Guida per programmatori dell'estensione App](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
