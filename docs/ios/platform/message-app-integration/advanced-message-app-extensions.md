---
title: Estensioni avanzate dell'app per i messaggi in Novell. iOS
description: Questo articolo illustra le tecniche avanzate per l'uso delle estensioni dell'app per i messaggi in una soluzione Novell. iOS che si integra con l'app Messages e presenta nuove funzionalità all'utente.
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: bf6853ef94ff0be5382bab02192070a20884c31e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769689"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>Estensioni avanzate dell'app per i messaggi in Novell. iOS

_Questo articolo illustra le tecniche avanzate per l'uso delle estensioni dell'app per i messaggi in una soluzione Novell. iOS che si integra con l'app Messages e presenta nuove funzionalità all'utente._

Una novità di iOS 10, un'estensione di app per **i messaggi si** integra con l'app Messages e presenta nuove funzionalità all'utente. L'estensione può inviare testo, adesivi, file multimediali e messaggi interattivi.

## <a name="about-message-app-extensions"></a>Informazioni sulle estensioni dell'app messaggio

Come indicato in precedenza, un'estensione di app per i **messaggi** si integra con l'app Messages e presenta nuove funzionalità all'utente. L'estensione può inviare testo, adesivi, file multimediali e messaggi interattivi. Sono disponibili due tipi di estensione dell'app per i messaggi:

- **Sticker pack** : contiene una raccolta di adesivi che l'utente può aggiungere a un messaggio. È possibile creare pacchetti di adesivi senza scrivere codice.
- **app iMessage** : può presentare un'interfaccia utente personalizzata all'interno dell'app Messages per la selezione di adesivi, l'immissione di testo, inclusi i file multimediali (con conversioni di tipi facoltative) e la creazione, la modifica e l'invio di messaggi di interazione.

Le estensioni delle app per i messaggi forniscono tre tipi di contenuto principali:

- **Messaggi interattivi** : tipo di contenuto del messaggio personalizzato generato da un'app. quando l'utente tocca il messaggio, l'app verrà avviata in primo piano.
- **Adesivi** : immagini generate dall'app che possono essere incluse nei messaggi inviati tra gli utenti. Vedere l'app di esempio [Ice Cream Builder](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder) per un esempio di implementazione di un'app di sticker Pack.
- **Altro contenuto supportato** : l'app può fornire contenuti come foto, video, testo o collegamenti del tipo che è sempre stato supportato dall'app Messages.

Una novità di iOS 10, l'app Message include ora un proprio App Store dedicato, integrato. Tutte le app che includono le estensioni delle app di messaggio verranno visualizzate e innalzate in questo archivio. Il cassetto della nuova app Messages Visualizza le app che sono state scaricate dall'App Store messages per fornire accesso rapido agli utenti.

Inoltre, una novità di iOS 10, Apple ha aggiunto l'attribuzione di app inline che consente all'utente di individuare facilmente un'app. Se, ad esempio, un utente invia contenuto a un altro da un'app che il secondo utente non ha installato (ad esempio un adesivo), il nome dell'app mittente viene elencato sotto il contenuto della cronologia del messaggio. Se l'utente tocca il nome dell'app, l'App Store del messaggio viene aperta e l'app selezionata nell'archivio.

Le estensioni delle app di messaggio sono simili alle app iOS esistenti che lo sviluppatore ha familiarità con la creazione e avranno accesso a tutti i Framework e le funzionalità standard di un'app iOS standard. Ad esempio:

- Hanno accesso all'acquisto in-app.
- Hanno accesso a Apple Pay.
- Hanno accesso all'hardware del dispositivo, ad esempio la fotocamera.

Le estensioni delle app per i messaggi sono supportate solo in iOS 10. Tuttavia, il contenuto inviato da queste estensioni è visualizzabile nei dispositivi watchos e macOS. La nuova _pagina recenti_ aggiunta a watchos 3 Mostra gli adesivi recenti che sono stati inviati dal telefono, inclusi quelli delle estensioni delle app di messaggio e consentono all'utente di inviare tali adesivi dall'orologio.

## <a name="about-interactive-messages"></a>Informazioni sui messaggi interattivi

I messaggi interattivi presentano una bolla di messaggio personalizzata e sono forniti da un'estensione dell'app per i messaggi. Consentono all'utente di creare contenuto interattivo dei messaggi, inserirlo nel campo di input del messaggio e inviarlo.

[![](advanced-message-app-extensions-images/interactive01.png "Creazione di contenuto interattivo del messaggio")](advanced-message-app-extensions-images/interactive01.png#lightbox)

L'utente ricevente può rispondere a un messaggio interattivo toccando la bolla dei messaggi nella cronologia dei messaggi per caricare l'estensione dell'app del messaggio che l'ha creata. L'estensione verrà avviata a schermo intero e consentirà all'utente di comporre una risposta e di inviarla di nuovo all'utente di origine.

[![](advanced-message-app-extensions-images/interactive02.png "Estensione avviata a schermo intero")](advanced-message-app-extensions-images/interactive02.png#lightbox)

Gli argomenti seguenti verranno trattati in dettaglio di seguito:

- Cenni preliminari sull'API messages
- Il ciclo di vita dell'estensione
- Composizione di un messaggio
- Invio di un messaggio

## <a name="messages-api-overview"></a>Cenni preliminari sull'API messages

Quando viene richiamato dall'utente, un'estensione dell'app per i messaggi verrà visualizzata nella parte inferiore della cronologia dei messaggi in modalità di visualizzazione compatta:

[![](advanced-message-app-extensions-images/interactive03.png "Cenni preliminari sull'API messages")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. L' `MSMessageAppViewController` oggetto nell'estensione dell'app Message è la classe principale che viene chiamata quando la visualizzazione dell'estensione viene visualizzata all'utente.
2. La conversazione viene presentata all'utente come `MSConversation` istanza di oggetto.
3. La `MSMessage` classe rappresenta una bolla di messaggio specificata nella conversazione.
4. `MSSession`Controlla la modalità di invio di un messaggio.
5. `MSMessageTemplateLayout`Controlla la modalità di visualizzazione del messaggio

## <a name="the-extension-lifecycle"></a>Il ciclo di vita dell'estensione

Esaminare il processo di attivazione di un'estensione dell'app per i messaggi:

[![](advanced-message-app-extensions-images/interactive04.png "Il processo di un'estensione dell'app per i messaggi diventa attivo")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. Quando viene avviata un'estensione, ad esempio dal cassetto dell'app, l'app Message avvierà un processo.
2. Viene `DidBecomeActive` chiamato il metodo e viene passato `MSConversation` un oggetto che rappresenta la conversazione in cui è in esecuzione l'estensione dell'app per i messaggi.
3. Poiché l'estensione si basa su un oggetto `UIViewController` `ViewWillAppear` e `ViewDidAppear` vengono chiamati.

Esaminare quindi il processo di disattivazione dell'estensione dell'app per i messaggi:

[![](advanced-message-app-extensions-images/interactive05.png "Il processo di un'estensione dell'app per i messaggi diventa disattivato")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. Quando l'estensione dell'app del messaggio viene disattivata, `ViewWillDisappear` il metodo verrà chiamato per primo.
2. Viene quindi `ViewDidDisappear` chiamato il metodo.
3. Viene `WillResignActive` chiamato il metodo e viene passato `MSConversation` un oggetto che rappresenta la conversazione in cui è in esecuzione l'estensione dell'app per i messaggi. A questo punto, la connessione tra l'app messaggi e l'estensione sta per essere rilasciata.
4. In un secondo momento, il processo viene terminato dall'app Messages.

Poiché un'estensione è un processo di breve durata, viene terminata in modo aggressivo dal sistema per conservare l'elaborazione e la potenza della batteria. Lo sviluppatore deve tenere presente questo aspetto durante la progettazione e l'implementazione di un'estensione di app per i messaggi.

## <a name="composing-a-message"></a>Composizione di un messaggio

Quando l'estensione dell'app per i messaggi viene eseguita in un processo e ha visualizzato l'interfaccia utente, è possibile usare il codice seguente per comporre un nuovo messaggio:

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

Questo codice crea un nuovo `MSMessage` oggetto e imposta diverse proprietà, `Url`ad esempio. Mentre il messaggio può essere creato solo in iOS, può essere inviato sia a iOS che a macOS per essere visualizzato.

Se l'utente fa clic sulla bolla del messaggio nella conversazione in macOS, il Mac tenterà di aprire l'indirizzo specificato nell'URL nel Web browser. Di conseguenza, il sito Web dello sviluppatore dovrebbe essere in grado di mostrare una rappresentazione del messaggio nel Web browser nei computer basati su macOS.

La `AccessibilityLabel` proprietà viene utilizzata dalle utilità per la lettura dello schermo per leggere la trascrizione della conversazione con l'utente. La `Layout` proprietà specifica il modo in cui il messaggio verrà visualizzato, attualmente `MSMessageTemplateLayout` solo è supportato e avrà un aspetto simile al seguente:

[![](advanced-message-app-extensions-images/interactive06.png "Modello MSMessageTemplateLayout")](advanced-message-app-extensions-images/interactive06.png#lightbox)

La `Image` proprietà dell'oggetto `MSMessageTemplateLayout` fornisce contenuto per il corpo principale di MessageBubble sullo schermo. La `MediaFileUrl` proprietà fornisce anche il contenuto per il corpo della bolla del messaggio, ma consente il contenuto non supportato da `UIImage` , ad esempio un file video con ciclo in background. Se vengono specificate `Image` entrambe `MediaFileUrl` le proprietà e, la `Image` proprietà avrà la precedenza. `MediaFileUrl` Supporta i formati multimediali PNG, JPEG, gif e video (in qualsiasi formato che possono essere riprodotti dal framework di Media Player).

La dimensione media consigliata è 300 x 300 pixel alla risoluzione 3x. Sono accettati anche asset di dimensioni leggermente maggiori e minori e Apple suggerisce test con alcune dimensioni diverse per ottenere risultati ottimali. L'app Message effettuerà il campionamento e la scalabilità del supporto come richiesto.

Quando le risorse vengono inviate al ricevitore, qualsiasi supporto collegato verrà automaticamente transcodificato dall'app Messages per ottimizzarne il trasferimento in rete. Per questo motivo, Apple sconsiglia di includere il testo nel supporto collegato al messaggio, perché i supporti verranno ridimensionati e compressi per la trasmissione, rendendo potenzialmente illeggibile il testo.

Le `ImageTitle` proprietà `ImageSubtitle` e forniscono una descrizione per il supporto presentato nella bolla del messaggio. Queste proprietà verranno inviate come testo al dispositivo ricevente, in cui verranno visualizzate in modo nitido nell'angolo inferiore sinistro dell'immagine.

Le `Caption`proprietà `SubCaption` ,e`TrailingSubcaption`descrivono ulteriormente l'immagine e ne verrà eseguito il rendering in una sezione sotto l'immagine. `TrailingCaption` Impostando tutte queste proprietà su `null` , viene creata una bolla di messaggio senza l'area della didascalia:

[![](advanced-message-app-extensions-images/interactive07.png "Bolla del messaggio senza l'area della didascalia")](advanced-message-app-extensions-images/interactive07.png#lightbox)

L'ultima cosa da notare è che l'app Messages trarrà l'icona dell'estensione dell'app Message nell'angolo superiore sinistro della bolla del messaggio.

## <a name="sending-a-message"></a>Invio di un messaggio

Una volta che è stato composto, il codice seguente può essere usato per inviarlo: `MSMessage`

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

La `ActiveConversation` proprietà dell'oggetto `MSMessagesAppViewController` conterrà la conversazione corrente in cui è stata avviata l'estensione dell'app per i messaggi.

Chiamare il `InsertMessage` `MSConversation` di per includere il messaggio nella conversazione e gestire gli eventuali errori che potrebbero verificarsi. Se il messaggio viene incluso correttamente, la bolla del messaggio verrà visualizzata nel campo di input.

Inoltre, l'estensione può inviare tipi diversi di dati alla conversazione, ad esempio:

- **Text** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **Allegati** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **Adesivi**  -  dove`sticker`è un oggetto `MSSticker`.`ActiveConversation.InsertSticker (sticker, (obj) => {...});`

Una volta che il nuovo contenuto si trova nel campo di input, l'utente è in grado di inviare il messaggio toccando il pulsante di **invio** blu (così come per qualsiasi messaggio tipico). Non esiste alcun modo per l'estensione dell'app per i messaggi di inviare automaticamente il contenuto, questo processo è completamente sotto il controllo dell'utente.

## <a name="handling-the-compact-and-expanded-modes"></a>Gestione delle modalità compatta ed espansa

Un'estensione dell'app messaggio può essere visualizzata in una delle due modalità di visualizzazione diverse:

[![](advanced-message-app-extensions-images/interactive08.png "Un'estensione dell'app messaggio visualizzata in due modalità di visualizzazione diverse: Compatta & espansa")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** : questa è la modalità predefinita in cui l'estensione dell'app Message occupa il 25% inferiore della visualizzazione messaggio. In modalità Compact, l'app non ha accesso alla tastiera, allo scorrimento orizzontale o ai riconoscitori del movimento di scorrimento. L'app ha accesso al campo di input e le chiamate a `InsertMessage` verranno immediatamente visualizzate all'utente.
- **Espanso** : l'estensione dell'app per i messaggi compila l'intera visualizzazione messaggio. Non ha accesso al campo di input, ma ha accesso alla tastiera, allo scorrimento orizzontale e ai riconoscitori dei movimenti di scorrimento.

Un'estensione dell'app di messaggio può essere cambiata a livello di codice o manualmente dall'utente in qualsiasi momento e deve rispondere immediatamente a qualsiasi modifica in modalità di visualizzazione.

Esaminare l'esempio seguente di gestione del passaggio tra le due diverse modalità di visualizzazione. Per ogni stato saranno necessari due diversi controller di visualizzazione. Gestisce la visualizzazione **compatta** e l'oggetto `AddStickerViewController` gestirà la visualizzazione **espansa:** `StickerBrowserViewController`

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

Viene `DidTransition` eseguito l'override del metodo per gestire il cambio tra le due modalità:

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

Facoltativamente, è possibile che l'app abbia `WillTransition` usato il metodo per gestire la modifica della modalità di visualizzazione prima che venga presentata all'utente (come avviene nell'esempio di gelatiera per gelato). Per ulteriori informazioni, vedere la documentazione relativa alla [personalizzazione dell'adesivo](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) .

## <a name="replying-to-a-message"></a>Risposta a un messaggio

Quando si risponde a un messaggio, è necessario che un'estensione dell'app per i messaggi venga gestita in due casi:

[![](advanced-message-app-extensions-images/interactive09.png "Estensione dell'app per i messaggi nelle modalità inattive e attive")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- L' **estensione è inattiva** : nella trascrizione del messaggio è presente una delle bolle di messaggio dell'estensione dell'app messaggio che l'utente può toccare per attivare le estensioni e continuare la conversazione interattiva.
- L' **estensione è attiva** : l'utente può toccare la bolla del messaggio dell'estensione dell'app messaggio nella trascrizione del messaggio per accedere alla modalità di visualizzazione espansa e continuare il processo interattivo dal punto in cui sono state interrotte.

### <a name="the-extension-is-inactive"></a>L'estensione è inattiva

Quando una bolla di messaggio viene toccata dall'utente nella trascrizione dei messaggi e l'estensione dell'app per i messaggi è inattiva, si verifica il processo seguente:

[![](advanced-message-app-extensions-images/interactive10.png "Gestione di una bolla di messaggio inattiva")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. L'utente tocca la bolla del messaggio dell'estensione.
2. Quando viene avviata un'estensione, l'app Message avvierà un processo.
3. Viene `DidBecomeActive` chiamato il metodo e viene passato `MSConversation` un oggetto che rappresenta la conversazione in cui è in esecuzione l'estensione dell'app per i messaggi.
4. Poiché l'estensione si basa su un oggetto `UIViewController` `ViewWillAppear` e `ViewDidAppear` vengono chiamati.

Al termine del processo, l'estensione dell'app Message verrà visualizzata nella modalità di visualizzazione espansa.

### <a name="the-extension-is-active"></a>L'estensione è attiva

Quando una bolla di messaggio viene toccata dall'utente nella trascrizione dei messaggi e l'estensione dell'app per i messaggi è attiva, si verifica il processo seguente:

[![](advanced-message-app-extensions-images/interactive11.png "Gestione di una bolla di messaggio attiva")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. L'utente tocca la bolla del messaggio dell'estensione.
2. Poiché l'estensione dell'app per i messaggi è già `WillTransition` attiva, `MSMessagesAppViewController` viene chiamato il metodo di per gestire il cambio dal Compact alla modalità di visualizzazione espansa.
3. Il `DidSelectMessage` metodo `MSConversation` di `MSMessagesAppViewController` viene chiamato e viene passato a eacuiappartienela`MSMessage` bolla del messaggio.
4. Il `DidTransition` metodo`MSMessagesAppViewController` di viene chiamato per gestire il cambio da Compact alla modalità di visualizzazione espansa.

Nuovamente, al termine del processo, l'estensione dell'app per i messaggi verrà visualizzata nella modalità di visualizzazione espansa.

### <a name="accessing-the-selected-message"></a>Accesso al messaggio selezionato

In entrambi i casi, quando l'utente tocca una bolla di messaggio appartenente all'estensione dell'app per i messaggi, sarà necessario accedere `MSMessage` all'oggetto che è stato toccato `SelectedMessage` utilizzando la proprietà `MSConversation`dell'oggetto.

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

Il messaggio selezionato dovrebbe essere visualizzato nell'interfaccia utente dell'estensione dell'app del messaggio e l'utente deve essere autorizzato a comporre una risposta.

## <a name="removing-partially-completed-messages"></a>Rimozione di messaggi parzialmente completati

Nel processo di invio dei diversi passaggi di una conversazione interattiva tra i due utenti nella conversazione, le bolle del messaggio parzialmente completate possono iniziare a ingombrare la trascrizione del messaggio:

[![](advanced-message-app-extensions-images/interactive12.png "Le bolle del messaggio parzialmente completate possono ingombrare la trascrizione dei messaggi")](advanced-message-app-extensions-images/interactive12.png#lightbox)

Al contrario, l'estensione dell'app del messaggio deve comprimere le bolle del messaggio precedente in un commento conciso nella trascrizione del messaggio:

[![](advanced-message-app-extensions-images/interactive13.png "Compressione delle bolle di messaggio precedenti nella trascrizione dei messaggi")](advanced-message-app-extensions-images/interactive13.png#lightbox)

Questa operazione viene gestita utilizzando `MSSession` un per comprimere tutti i passaggi esistenti. Quindi, `DidSelectMessage` è possibile modificare `MSMessagesAppViewController` il metodo della classe in modo che abbia un aspetto simile al seguente:

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

Se il messaggio selezionato dispone già di un'uscita `MSSession`, viene usato in caso contrario viene `MSSession` creato un nuovo oggetto. La `SummaryText` proprietà`MSMessage` di viene usata per aggiungere una didascalia ai passaggi precedenti compressi. Se la `SummaryText` proprietà è impostata su `null`, i passaggi precedenti nella conversazione verranno rimossi completamente dalla trascrizione della conversazione.

## <a name="advanced-message-api-features"></a>Funzionalità avanzate dell'API messaggio

Con le funzionalità di base della nuova API dei messaggi descritta in dettaglio sopra, esaminare alcune delle funzionalità più avanzate integrate da Apple nel Framework.

Per prima cosa, esistono diversi altri metodi di override `MSMessagesAppViewController` nella classe che forniscono un accesso più approfondito alla conversazione:

- `DidStartSendingMessage`: Viene chiamato quando l'utente tocca il pulsante Invia. Questo non significa che il messaggio è stato effettivamente recapitato al destinatario, solo che il processo di invio è stato avviato.
- `DidCancelSendingMessage`-Questo errore si verifica quando l'utente tocca il pulsante *X* nell'angolo superiore destro della bolla del messaggio nella trascrizione della conversazione.
- `DidReceiveMessage`-Questo metodo viene chiamato quando l'estensione dell'app per i messaggi è attiva, un nuovo messaggio è stato ricevuto da uno dei partecipanti alla conversazione.

### <a name="group-conversations"></a>Conversazioni di gruppo

È possibile usare un'estensione di app per i messaggi mentre gli utenti sono interessati a conversazioni di gruppo, con 3 o più persone, e questo deve essere preso in considerazione durante la progettazione e l'implementazione di un'estensione di app per i messaggi.

Si osservi l'interazione seguente in una conversazione di gruppo con tre utenti:

[![](advanced-message-app-extensions-images/interactive14.png "Interazione in una conversazione di gruppo con tre utenti")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. L'utente 1 Invia un messaggio interattivo di gruppo chiedendo all'utente 2 e all'utente 3 di scegliere una guarnizione di hamburger.
2. L'utente 2 sceglie i pomodori.
3. L'utente 3 sceglie i cetrioli.
4. Le scelte dell'utente 2 e dell'utente 3 vengono restituite all'utente 1 quasi allo stesso tempo. Di conseguenza, la scelta dell'utente 2 viene compressa in una riga di riepilogo e non è disponibile. Questo caso potrebbe anche essere stato capovolto, con la scelta dell'utente 2 visualizzata e la compressione dell'utente 3.

In entrambi i casi, questo comportamento è indesiderato perché l'utente 1 dovrebbe essere in grado di accedere alle opzioni di User 2 e User 3. Per gestire questa situazione, Apple suggerisce che l'estensione dell'app per i messaggi archivia lo stato del messaggio nel cloud e usa la proprietà URL di `MSMessage` (che viene inviata tra gli utenti) per accedere a questo stato.

Quando l'utente invia un messaggio, viene generato un token di sessione che viene inserito nel cloud con lo stato corrente del messaggio. Quando un utente tocca una bolla di messaggio nella trascrizione della conversazione, il token di sessione viene usato per recuperare lo stato della sessione corrente dal cloud.

### <a name="sender-identifiers"></a>Identificatori del mittente

Per esaminare l'accesso all'identificatore del mittente di un messaggio, eseguire l'esempio di una conversazione di gruppo riportata sopra:

[![](advanced-message-app-extensions-images/interactive15.png "Gruppi di conversazioni che inviano gli identificatori")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. Anche in questo caso, l'utente 1 Invia un messaggio interattivo di gruppo chiedendo all'utente 2 e all'utente 3 di scegliere una guarnizione di hamburger.
2. L'utente 3 Seleziona i cetrioli.
3. La scelta dell'utente 3 arriva nuovamente all'utente 1 e l'utente 2 non ha ancora risposto.
4. Poiché Apple è molto interessato alla privacy dell'utente, l'estensione dell'app per i messaggi è nota solo su un `NSUUID`identificatore univoco (come) a cui viene assegnato ogni partecipante alla conversazione. Sul dispositivo locale è noto solo l'identificatore dell'utente corrente.
5. Dispone di una `SenderIdentifier` proprietà che corrisponde a uno degli utenti nell'elenco dei partecipanti noto all'estensione. `MSMessage`
6. Ogni dispositivo utente ha una propria copia dell'elenco dei partecipanti, dove di nuovo è noto solo l'identificatore dell'utente locale.
7. Quando viene inviato un messaggio, la `SenderIdentifier` relativa proprietà è nota anche come quella dell'utente locale.

Gli identificatori del mittente possono essere utilizzati nei modi seguenti:

- Esaminando l'elenco dei partecipanti, l'estensione può ottenere il numero di utenti nella conversazione.
- Quando l'estensione riceve un messaggio da un utente, può tenere traccia dell'identificatore del mittente. Se riceve un altro messaggio con lo stesso identificatore del mittente, l'estensione ne sa che appartiene allo stesso utente.
- Possono essere usati per identificare un utente specifico nella conversazione.

L'identificatore del mittente può essere usato in uno qualsiasi dei campi di testo `MSMessageTemplateLayout` di mediante l'aggiunta di un segno di dollaro`$`(). Ad esempio:

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

Quando l'app Messages Visualizza una bolla di messaggio con questo tipo di formattazione, sostituirà `$uuid...` con il nome del contatto del partecipante alla conversazione che ha inviato il messaggio.

L'identificatore del mittente è univoco in ogni dispositivo, quindi esaminando di nuovo il diagramma precedente, si noti che il dispositivo dell'utente 1 e l'utente 3 hanno un identificatore di mittente univoco diverso per ogni partecipante alla conversazione.

Gli identificatori del mittente hanno come ambito l'installazione dell'estensione dell'app per i messaggi. Quindi, se un utente disinstalla e reinstalla l'estensione dell'app del messaggio, verranno generati nuovi identificatori del mittente per la nuova installazione.

Per accedere agli identificatori del mittente, l'estensione può utilizzare il codice seguente:

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

I messaggi interattivi generati da un'estensione per l'app Message verranno distribuiti sulle piattaforme Apple seguenti:

- watchos 3
- macOS Sierra
- iOS 10

Delle tre piattaforme, solo iOS 10 consentirà all'utente di generare un messaggio interattivo. In MacOS Sierra, se l'utente fa clic su una bolla di messaggio interattiva, l'URL associato `MSMessage` a verrà aperto in Safari e verrà visualizzata una rappresentazione del messaggio.

In watchos l'app Messages può inviare un messaggio interattivo a un dispositivo iOS collegato in cui l'utente può comporre una risposta.

La nuova API messages supporta il fallback se il messaggio interattivo viene ricevuto nelle piattaforme Apple precedenti:

- watchos 2 +
- OS X 10.11 +
- iOS 9 +

Verranno recapitati in un formato di fallback come due messaggi distinti:

- Una sarà l'immagine fornita dal layout del modello.
- L'altro sarà l'URL fornito in `MSMessage`.

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato tecniche avanzate per l'uso delle estensioni dell'app per i messaggi in una soluzione Novell. iOS che si integra con l'app **messages** e presenta nuove funzionalità all'utente.

## <a name="related-links"></a>Collegamenti correlati

- [Ice Cream Builder (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [Riferimento ai messaggi](https://developer.apple.com/reference/messages)
- [Guida alla programmazione delle estensioni dell'app](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
