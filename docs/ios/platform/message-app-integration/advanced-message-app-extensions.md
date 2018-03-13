---
title: Estensioni App messaggio avanzate
description: "Questo articolo illustra le tecniche avanzate per l'utilizzo di estensioni per App i messaggi in una soluzione xamarin. IOS che si integra con l'app di messaggi e presenta nuove funzionalità all'utente."
ms.topic: article
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: fcfd1fd2ec9271bb5e8d9e09b43b7dc4cf3b3f12
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="advanced-message-app-extensions"></a>Estensioni App messaggio avanzate

_Questo articolo illustra le tecniche avanzate per l'utilizzo di estensioni per App i messaggi in una soluzione xamarin. IOS che si integra con l'app di messaggi e presenta nuove funzionalità all'utente._


Nuovo in iOS 10, un messaggio dell'estensione App si integra con il **messaggi** nuove funzionalità app e visualizza all'utente. L'estensione può inviare il testo, adesivi, file multimediali e messaggi interattivi.

## <a name="about-message-app-extensions"></a>Informazioni sulle estensioni App messaggio

Come descritto in precedenza, un messaggio dell'estensione App si integra con il **messaggi** nuove funzionalità app e visualizza all'utente. L'estensione può inviare il testo, adesivi, file multimediali e messaggi interattivi. Sono disponibili due tipi di messaggio dell'estensione dell'App:

- **Pack adesivo** -contiene una raccolta di adesivi che l'utente può aggiungere a un messaggio. È possibile creare pacchetti di codice senza scrivere alcun codice.
- **iMessage App** -possono presentare un'interfaccia utente personalizzata all'interno dell'app di messaggi per la selezione adesivi, immissione di testo, inclusi i file di supporto (con le conversioni di tipo facoltativi) e creazione, modifica e l'invio di messaggi di interazione.

Le estensioni App messaggio forniscono tre tipi di contenuto principali:

- **Messaggi interattivi** -sono un tipo di contenuto del messaggio personalizzato che genera un'app, quando l'utente tocca nel messaggio, l'app verrà avviata in primo piano.
- **Adesivi** -sono immagini generate dall'applicazione che può essere incluso nei messaggi inviati tra gli utenti. Vedere il nostro [gelato generatore](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) app di esempio per un esempio di implementazione di un'app Pack adesivo.
- **Altro contenuto supportato** -l'applicazione può visualizzare il contenuto, ad esempio foto, video, testo o i collegamenti di tipo sempre supportate dall'app messaggi.

Nuovo in iOS 10, l'app messaggio include ora il proprio archivio di applicazione predefinito, dedicato. Qualsiasi App che includono le estensioni App messaggio verrà visualizzata e innalzate di livello nell'archivio. Il nuovo pannello App messaggi visualizzerà le app che sono state scaricate dall'App Store di messaggi per fornire l'accesso rapido agli utenti.

Anche nuovi in iOS 10, Apple ha aggiunto Inline App Attribution che consente all'utente di individuare facilmente un'app. Ad esempio, se un utente invia il contenuto a un altro da un'app che l'utente 2 non è installato (ad esempio, un'etichetta applicata ad esempio), il nome dell'applicazione mittente è elencato sotto il contenuto della cronologia del messaggio. Se l'utente tocca l'app il nome, è possibile aprire l'archivio di App messaggi e l'app selezionata nell'archivio.

Le estensioni app di messaggio sono simili alle App iOS esistenti che lo sviluppatore è familiare per la creazione e avranno accesso a tutti i Framework standard e le funzionalità di un'app iOS standard. Ad esempio:

- Dispongono di accesso per l'acquisto In-App.
- Hanno accesso a pagamento di Apple.
- Hanno accesso al dispositivo hardware, ad esempio la fotocamera.

Estensioni per App i messaggi sono supportate solo in iOS 10, tuttavia, il contenuto che inviano queste estensioni è visibile nei dispositivi watchOS e macOS. Il nuovo _Recents Page_ aggiunto watchOS 3, verrà visualizzare adesivi recenti che sono stati inviati dal telefono, comprese quelle di estensioni di App di messaggio, e consentire all'utente di inviare tali adesivi dall'orologio.

## <a name="about-interactive-messages"></a>Informazioni sui messaggi interattivi

I messaggi interattivi presentano una bolla di messaggio personalizzata e vengono forniti da un'estensione di App di messaggio. Essi consentono all'utente di creare messaggio contenuto interattivo, inserirla nel campo di Input del messaggio e inviarlo.

[![](advanced-message-app-extensions-images/interactive01.png "Creazione di contenuto del messaggio interattivo")](advanced-message-app-extensions-images/interactive01.png#lightbox)

Utente ricevente può rispondere a un messaggio interattiva se si tocca la bolla messaggio nella cronologia di messaggio per caricare l'estensione di App di messaggio che li ha creati. L'estensione verrà avviato a schermo intero e consentire all'utente di creare una risposta e inviarlo all'utente di origine.

[![](advanced-message-app-extensions-images/interactive02.png "L'estensione avviata a schermo intero")](advanced-message-app-extensions-images/interactive02.png#lightbox)


Verranno trattati i seguenti argomenti in dettaglio di seguito:

- Panoramica dell'API messaggi
- Il ciclo di vita di estensione
- Il messaggio.
- Invio di un messaggio

## <a name="messages-api-overview"></a>Panoramica dell'API messaggi

Quando viene richiamato dall'utente, verrà visualizzata un messaggio dell'estensione App nella parte inferiore della cronologia del messaggio nella modalità di visualizzazione compatta:

[![](advanced-message-app-extensions-images/interactive03.png "Panoramica dell'API messaggi")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. Il `MSMessageAppViewController` dell'oggetto nell'estensione App messaggio è la classe principale che viene chiamata quando viene aperta la visualizzazione dell'estensione per l'utente.
2. La conversazione viene presentata all'utente come un `MSConversation` istanza dell'oggetto.
3. La `MSMessage` classe rappresenta una bolla di messaggio specificato nella conversazione.
4. `MSSession` Controlla come viene inviato un messaggio.
5. `MSMessageTemplateLayout` Controlla come viene visualizzato il messaggio

## <a name="the-extension-lifecycle"></a>Il ciclo di vita di estensione

Esaminiamo il processo di un messaggio dell'estensione dell'App diventa attiva:

[![](advanced-message-app-extensions-images/interactive04.png "Il processo di un messaggio dell'estensione dell'App diventa attiva")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. All'avvio di un'estensione (ad esempio dal cassetto della App), l'app messaggio avvierà un processo.
2. Il `DidBecomeActive` metodo viene chiamato e passato un `MSConversation` che rappresenta la conversazione a cui il messaggio dell'estensione App è in esecuzione in.
3. Poiché l'estensione è basato su un `UIViewController` entrambi `ViewWillAppear` e `ViewDidAppear` vengono chiamati.

Successivamente, esaminiamo il processo di un messaggio dell'estensione dell'App diventando disattivato:

[![](advanced-message-app-extensions-images/interactive05.png "Il processo di un messaggio dell'estensione dell'App diventando disattivato")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. Quando viene disattivata l'estensione di App di messaggio, il `ViewWillDisappear` metodo verrà chiamato prima di tutto.
2. Il `ViewDidDisappear` metodo verrà chiamato.
3. Il `WillResignActive` metodo viene chiamato e passato un `MSConversation` che rappresenta la conversazione a cui il messaggio dell'estensione App è in esecuzione in. A questo punto, la connessione tra l'app di messaggi e l'estensione sta per essere rilasciata.
4. In un secondo momento, il processo viene terminato da app Messages.

Poiché un'estensione è un processo di breve durato, viene terminato in modo aggressivo dal sistema per conservare la potenza di elaborazione e batteria. Lo sviluppatore si deve tenere presente durante la progettazione e implementazione di un'estensione di App di messaggio.

## <a name="composing-a-message"></a>Il messaggio.

Quando è visualizzata l'interfaccia utente dell'estensione App il messaggio è in esecuzione in un processo, il codice seguente è utilizzabile per creare un nuovo messaggio:

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

Questo codice crea un nuovo `MSMessage` e vengono impostate diverse proprietà (ad esempio `Url`). Mentre il messaggio può essere creato solo per iOS, possono essere inviato per iOS e Mac OS da visualizzare.

Se l'utente fa clic su una bolla messaggio nella conversazione in macOS, Mac tenterà di aprire l'indirizzo specificato nell'URL nel web browser. Di conseguenza, il sito dello sviluppatore deve essere in grado di visualizzare alcune rappresentazione del messaggio nel web browser su macOS basato su macchine.

Il `AccessibilityLabel` proprietà è utilizzata dalle utilità per la trascrizione della conversazione all'utente. Il `Layout` proprietà specifica come il messaggio verrà visualizzato, attualmente solo il `MSMessageTemplateLayout` è supportato e simile al seguente:

[![](advanced-message-app-extensions-images/interactive06.png "Il modello MSMessageTemplateLayout")](advanced-message-app-extensions-images/interactive06.png#lightbox)

Il `Image` proprietà del `MSMessageTemplateLayout` fornisce il contenuto del corpo principale del MessageBubble sullo schermo. Il `MediaFileUrl` proprietà inoltre fornisce il contenuto del corpo del messaggio bolla, ma consente per il contenuto che non è supportato da `UIImage` (ad esempio un file video che verrebbe ciclo in background). Se entrambi i `Image` e `MediaFileUrl` vengono fornite proprietà, il `Image` proprietà avrà la precedenza. Il `MediaFileUrl` supporta PNG, JPEG, GIF e video (in qualsiasi formato che possa essere riprodotto dal framework di Media Player) formati di file multimediali.

La dimensione media consigliato è 300 x 300 pixel alla risoluzione 3 x. Vengono accettate anche leggermente superiore e inferiore di risorse e Apple suggerisce test con alcune dimensioni diverse per ottenere i migliori risultati. Il messaggio App verrà-esempio e ridimensionare il supporto in base alle esigenze.

Quando le risorse vengono inviate al destinatario, qualsiasi file multimediale associata verrà automaticamente transcodificato da app Messages per ottimizzare la loro dal trasferimento attraverso le reti. Per questo motivo, Apple sconsiglia incluso testo nel file multimediale associato al messaggio, in quanto supporto verrà ridotta e compressi per la trasmissione, pertanto potenzialmente il rendering del testo illeggibile.

Il `ImageTitle` e `ImageSubtitle` proprietà forniscono una descrizione per il supporto che viene presentato il cerchio di messaggio. Queste proprietà verranno inviate come testo per il dispositivo di ricezione in cui nettamente rendering nell'angolo inferiore sinistro dell'immagine.

Il `Caption`, `SubCaption`, `TrailingCaption` e `TrailingSubcaption` proprietà ulteriormente descrivono l'immagine e verrà eseguito il rendering in una sezione sotto l'immagine. Tutte queste proprietà per l'impostazione `null` creerà una bolla messaggio senza Area della didascalia:

[![](advanced-message-app-extensions-images/interactive07.png "Una bolla messaggio senza Area della didascalia")](advanced-message-app-extensions-images/interactive07.png#lightbox)

L'ultima operazione da notare è che l'app messaggi verrà disegnato sull'icona dell'estensione App messaggio nell'angolo superiore sinistro della bolla messaggio.

## <a name="sending-a-message"></a>Invio di un messaggio

Una volta un `MSMessage` è stato composto, il codice seguente può essere utilizzato per inviare:

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

Il `ActiveConversation` proprietà del `MSMessagesAppViewController` conterrà la conversazione corrente dell'estensione App il messaggio è stato avviato nel.

Chiamare il `InsertMessage` del `MSConversation` per includere il messaggio nella conversazione e gestire eventuali errori che potrebbero verificarsi. Se il messaggio è incluso correttamente, verrà visualizzato il fumetto del messaggio nel campo di Input.

Inoltre, l'estensione è possibile inviare diversi tipi di dati per la conversazione, ad esempio:

- **Text** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **Allegati** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **Adesivi**  -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});` in `sticker` è un `MSSticker`.

Una volta il nuovo contenuto del campo di Input, l'utente è in grado di inviare il messaggio toccando il blu **inviare** pulsante (come per qualsiasi messaggio tipico). Non è possibile per l'estensione di App di messaggio per l'invio automatico di contenuto, questo processo è completamente sotto il controllo dell'utente.

## <a name="handling-the-compact-and-expanded-modes"></a>Gestisce le modalità compatta ed espanse

Estensione dell'App un messaggio può essere visualizzata in una delle due modalità di visualizzazione diverse:

[![](advanced-message-app-extensions-images/interactive08.png "Estensione dell'App un messaggio visualizzato in due modalità di visualizzazione diverse: Compact & espansa")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -questa è la modalità predefinita in cui il messaggio dell'estensione App occupa il 25% della visualizzazione dei messaggi. In modalità ridotta, l'applicazione non ha accesso alla tastiera, lo scorrimento orizzontale o scorrere i riconoscitori di movimento. L'app abbiano accesso al campo di Input e le chiamate a `InsertMessage` verrà immediatamente visualizzata all'utente non esiste.
- **Espanso** -l'estensione dell'App messaggio riempie l'intera vista di messaggio. Non ha accesso al campo di Input, ma dispone dell'accesso alla tastiera, lo scorrimento orizzontale e scorrere i riconoscitori di movimento.

Estensione dell'App un messaggio è possibile passare da una modalità a livello di codice o manualmente dall'utente in qualsiasi momento e deve essere immediatamente risponde a una modifica nella visualizzazione modalità.

Esaminare l'esempio seguente di gestire il passaggio tra le due modalità di visualizzazione diverse. Due controller di visualizzazione diversi sarà necessario per ogni stato. Il `StickerBrowserViewController` handle il **Compact** Vista e `AddStickerViewController` gestirà la **espansa** Vista:

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

Il `DidTransition` viene eseguito l'override di metodo per gestire il passaggio tra le due modalità:

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

Facoltativamente, l'app sarebbe possibile utilizzare il `WillTransition` metodo per gestire la modifica di modalità di visualizzazione prima viene presentato all'utente (come avviene in questo esempio di generatore Icecream). Per ulteriori informazioni, vedere il nostro [ulteriore personalizzazione adesivo](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) documentazione.

## <a name="replying-to-a-message"></a>Risponde a un messaggio

Esistono due casi che dovrà gestire quando si risponde a un messaggio di un messaggio dell'estensione App:

[![](advanced-message-app-extensions-images/interactive09.png "L'estensione dell'App messaggio nelle modalità inattiva e attivo")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **L'estensione è inattivo** -è presente uno di bolle di messaggio dell'estensione App messaggio nella trascrizione messaggio che l'utente può toccare per attivare le estensioni e continuare la conversazione interattiva.
- **L'estensione è attivo** -l'utente può toccare a bolle di messaggio dell'estensione App messaggio nella trascrizione messaggio per attivare la modalità di visualizzazione espansa e continuare il processo interattivo da in cui sono stati interrotti.

### <a name="the-extension-is-inactive"></a>L'estensione è inattivo

Quando una bolla di messaggio vengono scelti dall'utente nella trascrizione del messaggio e l'estensione dell'App messaggio non è attivo, verrà eseguito il processo seguente:

[![](advanced-message-app-extensions-images/interactive10.png "La gestione di una bolla messaggio inattivo")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. L'utente tocca a bolle di messaggio dell'estensione.
2. Quando viene avviata un'estensione, l'app messaggio avvierà un processo.
3. Il `DidBecomeActive` metodo viene chiamato e passato un `MSConversation` che rappresenta la conversazione a cui il messaggio dell'estensione App è in esecuzione in.
4. Poiché l'estensione è basato su un `UIViewController` entrambi `ViewWillAppear` e `ViewDidAppear` vengono chiamati.

Una volta completato il processo, l'estensione dell'App messaggio verrà visualizzato nella modalità di visualizzazione espansa.

### <a name="the-extension-is-active"></a>L'estensione è attivo

Quando una bolla di messaggio vengono scelti dall'utente nella trascrizione del messaggio e l'estensione dell'App messaggio sia attivo, verrà eseguito il processo seguente:

[![](advanced-message-app-extensions-images/interactive11.png "La gestione di una bolla messaggio active")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. L'utente tocca a bolle di messaggio dell'estensione.
2. Poiché il messaggio dell'estensione App è già attivo, il `WillTransition` metodo il `MSMessagesAppViewController` viene chiamato per gestire il passaggio da .NET Compact Framework per la modalità di visualizzazione espansa.
3. Il `DidSelectMessage` metodo il `MSMessagesAppViewController` e viene passato il `MSMessage` e `MSConversation` bolla messaggio a cui appartiene.
4. Il `DidTransition` metodo il `MSMessagesAppViewController` viene chiamato per gestire il passaggio da .NET Compact Framework per la modalità di visualizzazione espansa.

Nuovamente, una volta completato il processo, l'estensione dell'App messaggio verrà visualizzato nella modalità di visualizzazione espansa.

### <a name="accessing-the-selected-message"></a>Accede al messaggio selezionato

In entrambi i casi, quando l'utente digita una bolla messaggio appartenente all'estensione di App di messaggio, sarà necessario accedere al `MSMessage` che viene toccato utilizzando il `SelectedMessage` proprietà del `MSConversation`.

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

Il messaggio selezionato deve essere visualizzato nell'interfaccia utente dell'estensione App messaggio e gli utenti devono essere autorizzati per comporre una risposta.

## <a name="removing-partially-completed-messages"></a>Rimozione parzialmente completato messaggi

Durante l'invio di diversi passaggi di una conversazione interattiva tra l'utente due nella conversazione, è possibile avviare le bolle messaggio parzialmente completato di riempire la trascrizione del messaggio:

[![](advanced-message-app-extensions-images/interactive12.png "Le bolle messaggio completata parzialmente può occupare la trascrizione del messaggio")](advanced-message-app-extensions-images/interactive12.png#lightbox)

Al contrario, il messaggio dell'estensione App deve comprimere le bolle messaggio precedente in un breve commento nella trascrizione messaggio:

[![](advanced-message-app-extensions-images/interactive13.png "Comprimere il precedente messaggio passa nella trascrizione messaggio")](advanced-message-app-extensions-images/interactive13.png#lightbox)

Questa operazione viene gestita utilizzando un `MSSession` per comprimere tutti i passaggi esistenti. Pertanto la `DidSelectMessage` metodo la `MSMessagesAppViewController` classe potrebbe essere modificata per essere simile al seguente:

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

Se il messaggio selezionato dispone già di un'uscita `MSSession`, viene utilizzato in caso contrario un nuovo `MSSession` viene creato. Il `SummaryText` proprietà del `MSMessage` viene utilizzato per aggiungere un titolo per i passaggi precedenti compressi. Se il `SummaryText` è impostata su `null`, i passaggi precedenti nella conversazione verranno rimossa completamente dalla trascrizione di conversazione.

## <a name="advanced-message-api-features"></a>Messaggio API funzionalità avanzate

Con le funzionalità di base dell'API di nuovo messaggio analizzate in dettaglio in precedenza, quindi esaminare alcune delle funzionalità più avanzate che Apple ha compilato nel framework di.

In primo luogo, esistono numerosi altri metodi di override di `MSMessagesAppViewController` classe che offrono un accesso alla conversazione:

- `DidStartSendingMessage` -Viene chiamato quando l'utente tocca il pulsante Invia. Ciò non significa che il messaggio sia stato effettivamente recapito al destinatario, solo che sia stato avviato il processo di trasmissione.
- `DidCancelSendingMessage` -Ciò si verifica quando l'utente tocca il *X* pulsante nell'angolo superiore destro della bolla messaggio nella conversazione trascrizione.
- `DidReceiveMessage` -Questo metodo viene chiamato quando l'estensione dell'App messaggio è attivo un un nuovo messaggio è stato ricevuto da uno dei partecipanti alla conversazione.

### <a name="group-conversations"></a>Conversazioni di gruppo

Estensione dell'App un messaggio può essere utilizzata mentre gli utenti coinvolti nelle conversazioni di gruppo (con 3 o più utenti) e questo sarà necessario prendere in considerazione durante la progettazione e implementazione di un'estensione di App di messaggio.

Esaminare l'interazione seguente in una conversazione di gruppo con tre utenti:

[![](advanced-message-app-extensions-images/interactive14.png "Interazione di una conversazione di gruppo con tre utenti")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. 1 utente invia un messaggio interattiva di gruppo in cui viene chiesto utente 2 e 3 utente di scegliere un cliente rimane arriva al massimo.
2. L'utente 2 sceglie tomatoes.
3. Utente 3 sceglie pickles.
4. Le opzioni utente 2 sia utente 3 arrivano al 1 utente quasi nello stesso momento. Di conseguenza, la scelta dell'utente 2 viene compresso in una riga di riepilogo e non è disponibile. In questo caso potrebbe avere anche stato capovolto, con la scelta dell'utente 2 del compresso viene visualizzato e l'utente 3.

In entrambi i casi, questo comportamento è indesiderato, come utente 1 deve essere in grado di accedere a opzioni utente 2 sia utente 3. Per gestire questa situazione, suggerisce che il messaggio dell'estensione App archivia lo stato del messaggio nel cloud e utilizzare la proprietà URL di Apple il `MSMessage` (che viene inviato tra gli utenti) per accedere a questo stato.

Quando l'utente invia un messaggio, un token di sessione viene generato e inserito nel cloud con lo stato del messaggio corrente. Quando un utente tocca su una bolla di messaggio nella conversazione trascrizione, il token di sessione viene utilizzato per recuperare lo stato della sessione corrente dal cloud.

### <a name="sender-identifiers"></a>Identificatori del mittente

Per discutere l'identificatore del mittente di un messaggio di accesso, eseguire l'esempio di una conversazione di gruppo specificata in precedenza:

[![](advanced-message-app-extensions-images/interactive15.png "L'invio di identificatori di conversazione di gruppo")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. Nuovamente, 1 utente invia un messaggio interattiva di gruppo in cui viene chiesto utente 2 e 3 utente di scegliere un cliente rimane arriva al massimo.
2. Utente 3 sceglie pickles.
3. Scelta dell'utente del 3 arriva all'utente 1 e 2 utente non ha risposto ancora.
4. Poiché Apple è molto interessati alla privacy dell'utente, il messaggio dell'estensione App conosce solo un identificatore univoco (come un `NSUUID`) che viene assegnato a ogni partecipante alla conversazione. Nel dispositivo locale, è noto solo l'identificatore dell'utente corrente.
5. Il `MSMessage` ha un `SenderIdentifier` proprietà corrispondente a uno dell'utente nell'elenco partecipante nota per l'estensione.
6. Ogni dispositivo dell'utente ha la propria copia dell'elenco partecipante dove nuovamente, è noto solo l'identificatore dell'utente locale.
7. Quando viene inviato un messaggio, il relativo `SenderIdentifier` proprietà è nota anche a quello dell'utente locale.

Gli identificatori di mittente possono essere utilizzati nei modi seguenti:

- Esaminando l'elenco di partecipanti l'estensione è possibile ottenere il numero di utenti nella conversazione.
- Quando l'estensione riceve un messaggio da un utente, è possibile tenere traccia dell'identificatore del mittente. Se riceve un altro messaggio con lo stesso identificatore mittente, l'estensione sa che è dello stesso utente.
- Possono essere utilizzati per identificare un utente specifico nella conversazione.

L'identificatore del mittente può essere usata in qualsiasi dei campi di testo di `MSMessageTemplateLayout` anteponendovi un segno di dollaro (`$`). Ad esempio:

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

Quando l'app messaggi Visualizza una bolla di messaggio con questo tipo di formattazione, che sostituirà il `$uuid...` con il nome del contatto del partecipante alla conversazione che ha inviato il messaggio.

L'identificatore del mittente è univoco in ogni dispositivo, in modo esaminando il diagramma precedente si noti il dispositivo di tale utente 1 e il dispositivo dell'utente 3 è un identificatore mittente univoci per ogni partecipante alla conversazione.

Gli identificatori di mittente hanno come ambiti per l'installazione dell'estensione App messaggio. Pertanto, se un utente Disinstalla e reinstalla il messaggio dell'estensione App nuovi identificatori di mittente verranno generati per la nuova installazione.

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

I messaggi interattivo generati da un'estensione di App di messaggio verrà recapitati nelle seguenti piattaforme Apple:

- watchOS 3
- macOS Sierra
- iOS 10

Delle tre piattaforme, solo iOS 10 consente all'utente generare un messaggio interattivo. In macOS Sierra, se l'utente fa clic su una bolla messaggio interattivi, l'URL collegato al `MSMessage` verrà aperta in Safari e una rappresentazione del messaggio deve essere visualizzata non esiste.

In watchOS, app Messages possibile passarlo a un messaggio interattivo a un dispositivo iOS collegato in cui l'utente può creare una risposta.

La nuova API di messaggi con supporto per il fallback se viene ricevuto il messaggio interattivo su piattaforme Apple precedenti:

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

Verranno recapitati in un formato fallback come due messaggi distinti:

- Uno sarà l'immagine fornita dal Layout del modello.
- L'altra risulterà l'URL di cui il `MSMessage`.

## <a name="summary"></a>Riepilogo

In questo articolo ha presentato tecniche avanzate per l'utilizzo di estensioni per App i messaggi in una soluzione xamarin. IOS che si integra con il **messaggi** app e presenti nuove funzionalità per l'utente.


## <a name="related-links"></a>Collegamenti correlati

- [Generatore di gelato (esempio)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Riferimento di messaggi](https://developer.apple.com/reference/messages)
- [Guida per programmatori di estensione dell'App](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
