---
title: Nozioni di estensione dell'App messaggi in xamarin. IOS
description: Questo articolo mostra come include un'estensione dell'App messaggi in una soluzione xamarin. IOS che si integra con l'app di messaggi e presenta nuove funzionalità all'utente.
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7bd4a87843852e940da96f688371ddbecbf7e0b4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105779"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Nozioni di estensione dell'App messaggi in xamarin. IOS

_Questo articolo mostra come include un'estensione dell'App messaggi in una soluzione xamarin. IOS che si integra con l'app di messaggi e presenta nuove funzionalità all'utente._

Nuovo ai dispositivi iOS 10, un'estensione dell'App messaggi si integra con il **messaggi** app e presenta nuove funzionalità per l'utente. L'estensione può inviare testo, le etichette, i file multimediali e i messaggi interattivi.

## <a name="about-message-app-extensions"></a>Informazioni sulle estensioni App messaggi

Come indicato in precedenza, un'estensione dell'App messaggi si integra con il **messaggi** app e presenta nuove funzionalità per l'utente. L'estensione può inviare testo, le etichette, i file multimediali e i messaggi interattivi. Sono disponibili due tipi di estensione per App i messaggi:

- **Pack adesivo** -contiene una raccolta di adesivi che l'utente può aggiungere a un messaggio. È possibile creare pacchetti di adesivi senza scrivere alcun codice.
- **iMessage App** -possono presentare un'interfaccia utente personalizzata nell'app messaggi per la selezione di adesivi, immettendo il testo, inclusi i file multimediali (con conversioni del tipo facoltativo) e creazione, modifica e l'invio di messaggi di interazione.

Le estensioni delle App messaggio offrono tre tipi di contenuto principali:

- **I messaggi interattivi** -sono un tipo di contenuto del messaggio personalizzato che genera un'app, quando l'utente tocca il messaggio, l'app verrà avviata in primo piano.
- **Adesivi** -sono immagini generate dall'app che può essere incluso nei messaggi inviati tra gli utenti.
- **Altro contenuto supportato** : l'app può fornire contenuto, ad esempio foto, video, testo o collegamenti a contenuti con altri tipi che sono sempre supportati dall'app per i messaggi.

Nuovo ai dispositivi iOS 10, l'app messaggio include ora un proprio Store di App predefinito, dedicato. Qualsiasi App che includono le estensioni delle App messaggio verrà visualizzata e innalzate di livello in questo archivio. Nuovi messaggi del pannello delle App verranno visualizzati le app che sono state scaricate da Store di App di messaggi per fornire accesso rapido agli utenti.

Anche nuovi in iOS 10, Apple ha aggiunto App Attribution Inline che consente all'utente di individuare facilmente un'app. Ad esempio, se un utente invia il contenuto a un altro da un'app che l'utente 2 ° non è installato (ad esempio, un'etichetta applicata, ad esempio), il nome dell'app per l'invio è elencato sotto il contenuto nella cronologia del messaggio. Se l'utente tocca dell'app il nome, Store di App il messaggio è possibile aprire e l'app selezionata nell'archivio.

Le estensioni delle App messaggio sono simili alle App iOS esistenti che lo sviluppatore abbia familiarità con la creazione e avranno accesso a tutti i framework di standard e le funzionalità di un'app per iOS standard. Ad esempio:

- Hanno accesso a acquisto In-App.
- Hanno accesso a Apple Pay.
- Hanno accesso a hardware, ad esempio la fotocamera del dispositivo.

Messaggio le estensioni delle App sono supportate solo in iOS 10, tuttavia, il contenuto che inviano queste estensioni è visualizzabile nei dispositivi macOS e watchOS. Il nuovo _Recents Page_ aggiunto a watchOS 3, verranno visualizzati adesivi recenti che sono stati inviati dal telefono, comprese quelle di messaggio le estensioni delle App, e consentire all'utente inviare tali adesivi dall'orologio.

## <a name="about-the-messages-framework"></a>Sul Framework di messaggi

Nuovo ai dispositivi iOS 10, il framework di messaggi fornisce l'interfaccia tra l'estensione per le app messaggio e l'app di messaggio nel dispositivo iOS dell'utente. Quando l'utente avvia un'App da all'interno dell'app messaggi, questo framework consente all'app di essere individuati e fornisce i dati e il contesto necessario per il layout dell'interfaccia utente.

Dopo che l'app viene avviata, l'utente interagisce con esso per creare nuovo contenuto da condividere tramite un messaggio. L'app Usa quindi il framework di messaggi per trasferire il contenuto appena creato per l'app Messages per l'elaborazione.

Il framework di messaggi e messaggi App estensioni si avvalgono di tecnologie le estensioni dell'App per iOS pre-esistenti. Per altre informazioni sulle estensioni di App, vedi di Apple [Guida alla programmazione di estensione App](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

A differenza di altri punti di estensione che Apple ha fornito in tutto il sistema, lo sviluppatore non dovrà fornire una host di app per le estensioni App messaggio poiché la stessa app messaggio funge da contenitore. Tuttavia, lo sviluppatore ha la possibilità di, inclusa l'estensione di App di messaggio all'interno di un'app di iOS nuovi o esistenti e distribuirlo con l'aggregazione.

Se le estensioni App del messaggio è incluso nel bundle dell'app iOS, l'icona dell'app verrà visualizzato nella schermata iniziale del dispositivo e nel messaggio del pannello delle App all'interno dell'app messaggi. Se non è incluso in un bundle dell'app, verrà visualizzato solo l'estensione per le app messaggio nel messaggio del pannello delle app.

Anche se le estensioni App del messaggio non è incluso in un bundle dell'app host, lo sviluppatore dovrà fornire un'icona di app in bundle dell'estensione App messaggio, poiché questa è l'icona che verrà visualizzato in altre parti del sistema, ad esempio le impostazioni o il pannello delle App messaggi , per l'estensione.

## <a name="about-stickers"></a>Sulle adesivi

Apple progettato adesivi come un nuovo modo per gli utenti per iMessage comunicare, consentendo a adesivi da inviare inline come qualsiasi altro contenuto di messaggio o possono essere collegati a fumetti per messaggi precedenti all'interno della conversazione.

Quali sono le etichette?

- Essi sono immagini che fornisce l'estensione per le App messaggi.
- Possono essere statiche o immagini.
- Forniscono un nuovo modo per condividere il contenuto di immagine all'interno di un'app.

Esistono due modi per creare le etichette:

1. È possibile creare un messaggio Pack adesivo le estensioni di App all'interno di Xcode senza includere alcun codice. Tutto ciò che è necessario sia le risorse necessarie per le icone dell'app e gli adesivi.
2. Tramite la creazione di un'estensione di App di messaggio standard che fornisce le etichette dal codice tramite il framework di messaggi.

### <a name="creating-sticker-packs"></a>La creazione di pacchetti di adesivi

Pack adesivo vengono creati da un modello speciale all'interno di Xcode ed è sufficiente fornire un set statico di asset di immagini che possono essere utilizzati come adesivi. Come indicato in precedenza, non richiedono alcun codice, lo sviluppatore trascina semplicemente i file di immagine nella cartella adesivo Pack interno il catalogo di Asset adesivi.

Per un'immagine da includere in un pacchetto di adesivi, deve soddisfare i requisiti seguenti:

- Le immagini devono essere in un formato APNG, PNG, GIF o JPEG. Usando solo i formati PNG e APNG quando si fornisce gli asset adesivo suggerite da Apple.
- Adesivi animati supportano solo i formati APNG e GIF.
- Le immagini adesivo devono fornire uno sfondo trasparente poiché possono essere posizionati su fumetti per messaggi nella conversazione dall'utente.
- I singoli file di immagine devono essere inferiore a 500kb.
- Le immagini non possono essere inferiore a 100 x 100 punti o superiori a cui punta 206 x 206.

> [!IMPORTANT]
> Immagini adesivo devono sempre essere fornite al `@3x` risoluzione nell'intervallo di 300 x 300 per 618 x 618 pixel. Il sistema genererà automaticamente le `@2x` e `@1x` versioni in fase di esecuzione in base alle esigenze.

Gli asset delle immagini adesivo contro varie diversi colori di sfondo colorati (ad esempio bianco, nero, rosso, giallo e multicolori) e più foto, per garantire che vengano visualizzate le migliori in tutte le possibili situazioni di test suggerite da Apple.

Pack adesivo può fornire le etichette in una delle tre dimensioni disponibili:

- **Piccola** : 100 x 100 punti.
- **Medium** - 136 x 136 punti. Questa è la dimensione predefinita.
- **Grandi** - 206 x 206 punti.

Usare Xcode Attributes Inspector per impostare le dimensioni per l'intero pacchetto di codice e fornire solo gli asset delle immagini che corrispondano alla dimensione richiesta, per ottenere risultati ottimali nel Browser adesivo all'interno dell'app messaggi.

Per altre informazioni, vedere la [Ice cream generatore](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) app e Apple [messaggi riferimento](https://developer.apple.com/reference/messages).

## <a name="creating-a-custom-sticker-experience"></a>Creazione di un'esperienza personalizzata adesivi

Se l'app richiede più controllo o flessibilità rispetto a quella fornita da un pacchetto di codice, può includere un'estensione dell'App messaggi e offrire un'esperienza di adesivi personalizzato agli adesivi tramite il framework di messaggi.

Quali sono i vantaggi della creazione di un'esperienza di adesivi personalizzato?

1. Consente all'app personalizzare la modalità di visualizzazione adesivi per gli utenti dell'app. Ad esempio, per le etichette presente in un formato diverso dal layout di griglia standard oppure su uno sfondo colorato diversi.
2. Consente le etichette in modo dinamico da creare dal codice anziché verrà incluso come asset immagini statiche.
3. Consente gli asset delle immagini degli adesivi da scaricare in modo dinamico dal server web per gli sviluppatori senza dover rilasciare una nuova versione per l'App Store.
4. Consente l'accesso della fotocamera del dispositivo per creare le etichette in immediatamente.
5. Consente gli acquisti In-App in modo che l'utente può acquistare ulteriori adesivi all'interno dell'app.

Per la creazione di un'esperienza di adesivi personalizzato, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Avviare Visual Studio per Mac.
2. Aprire la soluzione per aggiungere un'estensione dell'App al messaggio. 
3. Selezionare **iOS** > **estensioni** > **estensione per iMessage** e fare clic su di **successivo** pulsante: 

    [![](intro-to-message-app-extensions-images/message01.png "Selezionare l'estensione per iMessage")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. Immettere un **Extension Name** e fare clic sui **successivo** pulsante: 

    [![](intro-to-message-app-extensions-images/message02.png "Immettere un nome di estensione")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. Scegliere il **Create** per compilare l'estensione: 

    [![](intro-to-message-app-extensions-images/message03.png "Fare clic sul pulsante Crea")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avviare Visual Studio.
2. Aprire la soluzione per aggiungere un'estensione dell'App messaggi.
3. Selezionare * * estensioni iOS > (iOS) di estensione per iMessage * * e fare clic sui **successivo** pulsante:

    [![Selezionare per IMessage per estensione (iOS)](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. Immettere un **Name** e fare clic sui **OK** pulsante

-----

Per impostazione predefinita, il `MessagesViewController.cs` file verrà aggiunto alla soluzione. Questo è il punto di ingresso principale nell'estensione ed eredita dal `MSMessageAppViewController` classe.

Il framework di messaggi fornisce classi per la presentazione adesivi disponibili all'utente:

- `MSStickerBrowserViewController` : Controlla la vista che verranno presentati agli adesivi in. È anche conforme al `IMSStickerBrowserViewDataSource` interfaccia da restituire il numero di etichetta e l'etichetta per un indice specifico di browser.
- `MSStickerBrowserView` -Si tratta della visualizzazione che verranno visualizzati agli adesivi disponibili in.
- `MSStickerSize` -Decide le dimensioni della cella singola per la griglia di adesivi presentati nella vista del browser.

### <a name="creating-a-custom-sticker-browser"></a>Creazione di un Browser personalizzato adesivi

Lo sviluppatore di personalizzare ulteriormente l'esperienza degli adesivi per l'utente fornendo un Browser adesivo personalizzato (`MSMessageAppBrowserViewController`) nell'estensione di App di messaggio. Il Browser adesivo personalizzata viene modificato il modo in cui le etichette vengono presentate all'utente quando si seleziona un'etichetta applicata da includere nel flusso di messaggi.

Seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nel **riquadro della soluzione**, fare clic sul nome del progetto dell'estensione e selezionare **Add** > **nuovo File...**   >  **iOS | Apple Watch** > **Controller di interfaccia**.
2. Immettere `StickerBrowserViewController` per il **Name** e fare clic sui **New** pulsante: 

    [![](intro-to-message-app-extensions-images/browser01.png "Immettere nome StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. Aprire il `StickerBrowserViewController.cs` file per la modifica.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nel **Esplora soluzioni**, fare clic sul nome del progetto dell'estensione e selezionare **Add** > **nuovo File...**   >  **iOS | Apple Watch** > **Controller di interfaccia**.
2. Immettere `StickerBrowserViewController` per il **Name** e fare clic sui **New** pulsante: 

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "Immettere nome StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. Aprire il `StickerBrowserViewController.cs` file per la modifica.

-----

Rendere il `StickerBrowserViewController.cs` simile al seguente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MonkeyStickers
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MSStickerSize stickerSize) : base (stickerSize)
        {
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            ...
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();
        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            return Stickers[(int)index];
        }
        #endregion
    }
}
```

Esaminare il codice precedente in modo dettagliato. Archiviazione vengono creati per gli adesivi che fornisce l'estensione:

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

Ed esegue l'override di due metodi per il `MSStickerBrowserViewController` classe che fornisce dati per il browser da questo archivio dei dati:

```csharp
public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
{
    return Stickers.Count;
}

public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
{
    return Stickers[(int)index];
}
```

Il `CreateSticker` metodo ottiene il percorso di un asset di immagine dal bundle dell'estensione e lo usa per creare una nuova istanza di un `MSSticker` da questo asset, che viene aggiunto alla raccolta:

```csharp
private void CreateSticker (string assetName, string localizedDescription)
{

    // Get path to asset
    var path = NSBundle.MainBundle.PathForResource (assetName, "png");
    if (path == null) {
        Console.WriteLine ("Couldn't create sticker {0}.", assetName);
        return;
    }

    // Build new sticker
    var stickerURL = new NSUrl (path);
    NSError error = null;
    var sticker = new MSSticker (stickerURL, localizedDescription, out error);
    if (error == null) {
        // Add to collection
        Stickers.Add (sticker);
    } else {
        // Report error
        Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
    }
}
```

Il `LoadSticker` metodo viene chiamato da `ViewDidLoad` per creare un'etichetta applicata dall'asset immagine denominata (inclusi nel bundle dell'app) e aggiungerlo alla raccolta di adesivi.

Per implementare il Browser adesivo personalizzato, modificare il `MessagesViewController.cs` file e renderlo simile al seguente:

```csharp
using System;
using UIKit;
using Messages;

namespace MonkeyStickers
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public StickerBrowserViewController BrowserViewController { get; set;}
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();


            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);
        }
        #endregion
    }
}
```

In modo dettagliato come ad esempio questo codice, Crea risorsa di archiviazione per il browser personalizzato:

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

E nel `ViewDidLoad` metodo, crea un'istanza e si configura un nuovo browser:

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

Aggiunge quindi il browser alla visualizzazione e visualizzarla:

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>Un'ulteriore personalizzazione degli adesivi

Un'ulteriore personalizzazione adesivo è possibile includendo solo due classi nell'estensione di App di messaggio:

- `MSStickerView`
- `MSSticker`

Usa i metodi sopra indicati, l'estensione può supportare selezione adesivi che non si basa sul metodo adesivo Browser standard. Inoltre, la visualizzazione degli adesivi può essere alternata tra due modalità di visualizzazione diverse:

- **Compact** -si tratta della modalità predefinita in cui la visualizzazione degli adesivi occupa il 25% nella parte inferiore della visualizzazione messaggio.
- **Espanso** -l'adesivo visualizzazione riempie l'intera visualizzazione messaggio.

In questa vista adesivo possono essere alternata tra le modalità a livello di codice o manualmente dall'utente.

Esaminiamo l'esempio seguente di gestire il passaggio tra le due modalità di visualizzazione diverse. Due controller di visualizzazione diversi sarà necessario per ogni stato. Il `StickerBrowserViewController` gestisce i **Compact** visualizzazione e ha un aspetto analogo al seguente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MessageExtension
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set; }
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MessagesViewController messagesAppViewController, MSStickerSize stickerSize) : base (stickerSize)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("add", "Add New Sticker");
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();

        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            // Wanting to add a new sticker?
            if (index == 0) {
                // Yes, ask controller to present add sticker interface
                MessagesAppViewController.AddNewSticker ();
                return null;
            } else {
                // No, return existing sticker
                return Stickers [(int)index];
            }
        }
        #endregion
    }
}
```

Il `AddStickerViewController` gestirà i **espansa** adesivo della visualizzazione e l'aspetto simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;
using Messages;

namespace MessageExtension
{
    public class AddStickerViewController : UIViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set;}
        public MSSticker NewSticker { get; set;}
        #endregion

        #region Constructors
        public AddStickerViewController (MessagesViewController messagesAppViewController)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Override Method
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Build interface to create new sticker
            var cancelButton = new UIButton (UIButtonType.RoundedRect);
            cancelButton.TouchDown += (sender, e) => {
                // Cancel add new sticker
                MessagesAppViewController.CancelAddNewSticker ();
            };
            View.AddSubview (cancelButton);

            var doneButton = new UIButton (UIButtonType.RoundedRect);
            doneButton.TouchDown += (sender, e) => {
                // Add new sticker to collection
                MessagesAppViewController.AddStickerToCollection (NewSticker);
            };
            View.AddSubview (doneButton);
            
            ...
        }
        #endregion
    }
}
```

Il `MessageViewController` implementa questi controller di visualizzazione per gestire lo stato richiesto:

```csharp
using System;
using UIKit;
using Messages;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public bool IsAddingSticker { get; set;}
        public StickerBrowserViewController BrowserViewController { get; set; }
        public AddStickerViewController AddStickerController { get; set;}
        #endregion

        #region Constructors
        protected MessagesViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Public Methods
        public void PresentStickerBrowser ()
        {
            // Is the Add sticker view being displayed?
            if (IsAddingSticker) {
                // Yes, remove it from view
                AddStickerController.RemoveFromParentViewController ();
                AddStickerController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);

            // Save mode
            IsAddingSticker = false;
        }

        public void PresentAddSticker ()
        {
            // Is the sticker browser being displayed?
            if (!IsAddingSticker) {
                // Yes, remove it from view
                BrowserViewController.RemoveFromParentViewController ();
                BrowserViewController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (AddStickerController);
            AddStickerController.DidMoveToParentViewController (this);
            View.AddSubview (AddStickerController.View);

            // Save mode
            IsAddingSticker = true;
        }

        public void AddNewSticker ()
        {
            // Switch to expanded view mode
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void CancelAddNewSticker ()
        {
            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }

        public void AddStickerToCollection (MSSticker sticker)
        {
            // Add sticker to collection
            BrowserViewController.Stickers.Add (sticker);

            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (this, MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Create new Add controller and configure it as well
            AddStickerController = new AddStickerViewController (this);
            AddStickerController.View.Frame = View.Frame;

            // Initially present the sticker browser
            PresentStickerBrowser ();
        }

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
        #endregion
    }
}
```

Quando l'utente richiede di aggiungere una nuova etichetta alla loro raccolta disponibile, un nuovo `AddStickerViewController` diventa il controller visibile e la visualizzazione degli adesivi entra la **espansa** Vista:

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

Quando l'utente sceglie un adesivo per aggiungere, viene aggiunto per la propria raccolta disponibile e il **Compact** viene richiesto di visualizzazione:

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
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

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato includere un'estensione dell'App messaggi in una soluzione xamarin. IOS che si integra con il **messaggi** presenti nuove funzionalità per l'utente e app. Illustrato utilizzando l'estensione per l'invio di testo, le etichette, i file multimediali e i messaggi interattivi.



## <a name="related-links"></a>Collegamenti correlati

- [Generatore ice cream (esempio)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Riferimento dei messaggi](https://developer.apple.com/reference/messages)
- [Guida per programmatori dell'estensione App](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
