---
title: Nozioni fondamentali sull'estensione dell'app Message in Xamarin.iOS
description: Questo articolo illustra come includere un'estensione di app per i messaggi in una soluzione Xamarin.iOS che si integra con l'app Messages e presenta nuove funzionalità all'utente.
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 51a89533390eb1be8c1f36e0121229fb5a942279
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031668"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Nozioni fondamentali sull'estensione dell'app Message in Xamarin.iOS

_Questo articolo illustra come includere un'estensione di app per i messaggi in una soluzione Xamarin.iOS che si integra con l'app Messages e presenta nuove funzionalità all'utente._

Una novità di iOS 10, un'estensione di app per **i messaggi si** integra con l'app Messages e presenta nuove funzionalità all'utente. L'estensione può inviare testo, adesivi, file multimediali e messaggi interattivi.

## <a name="about-message-app-extensions"></a>Informazioni sulle estensioni dell'app messaggio

Come indicato in precedenza, un'estensione di app per i **messaggi** si integra con l'app Messages e presenta nuove funzionalità all'utente. L'estensione può inviare testo, adesivi, file multimediali e messaggi interattivi. Sono disponibili due tipi di estensione dell'app per i messaggi:

- **Sticker pack** : contiene una raccolta di adesivi che l'utente può aggiungere a un messaggio. È possibile creare pacchetti di adesivi senza scrivere codice.
- **app iMessage** : può presentare un'interfaccia utente personalizzata all'interno dell'app Messages per la selezione di adesivi, l'immissione di testo, inclusi i file multimediali (con conversioni di tipi facoltative) e la creazione, la modifica e l'invio di messaggi di interazione.

Le estensioni delle app per i messaggi forniscono tre tipi di contenuto principali:

- **Messaggi interattivi** : tipo di contenuto del messaggio personalizzato generato da un'app. quando l'utente tocca il messaggio, l'app verrà avviata in primo piano.
- **Adesivi** : immagini generate dall'app che possono essere incluse nei messaggi inviati tra gli utenti.
- **Altro contenuto supportato** : l'app può fornire contenuti come foto, video, testo o collegamenti ad altri tipi di contenuto che sono sempre stati supportati dall'app Messages.

Una novità di iOS 10, l'app Message include ora un proprio App Store dedicato, integrato. Tutte le app che includono le estensioni delle app di messaggio verranno visualizzate e innalzate in questo archivio. Il cassetto della nuova app Messages Visualizza le app che sono state scaricate dall'App Store messages per fornire accesso rapido agli utenti.

Inoltre, una novità di iOS 10, Apple ha aggiunto l'attribuzione di app inline che consente all'utente di individuare facilmente un'app. Se, ad esempio, un utente invia contenuto a un altro da un'app che il secondo utente non ha installato (ad esempio un adesivo), il nome dell'app mittente viene elencato sotto il contenuto della cronologia del messaggio. Se l'utente tocca il nome dell'app, l'App Store del messaggio viene aperta e l'app selezionata nell'archivio.

Le estensioni delle app di messaggio sono simili alle app iOS esistenti che lo sviluppatore ha familiarità con la creazione e avranno accesso a tutti i Framework e le funzionalità standard di un'app iOS standard. Esempio:

- Hanno accesso all'acquisto in-app.
- Hanno accesso a Apple Pay.
- Hanno accesso all'hardware del dispositivo, ad esempio la fotocamera.

Le estensioni delle app per i messaggi sono supportate solo in iOS 10. Tuttavia, il contenuto inviato da queste estensioni è visualizzabile nei dispositivi watchos e macOS. La nuova _pagina recenti_ aggiunta a watchos 3 Mostra gli adesivi recenti che sono stati inviati dal telefono, inclusi quelli delle estensioni delle app di messaggio e consentono all'utente di inviare tali adesivi dall'orologio.

## <a name="about-the-messages-framework"></a>Informazioni sul Framework dei messaggi

Una novità di iOS 10, il Framework dei messaggi fornisce l'interfaccia tra l'estensione delle app del messaggio e l'app del messaggio sul dispositivo iOS dell'utente. Quando l'utente avvia un'app dall'app messaggi, questo Framework consente l'individuazione dell'app e fornisce i dati e il contesto necessari per il layout dell'interfaccia utente.

Una volta avviata l'app, l'utente interagisce con esso per creare nuovi contenuti da condividere tramite un messaggio. L'app usa quindi il Framework messages per trasferire il contenuto appena creato nell'app Messages per l'elaborazione.

Le estensioni del Framework dei messaggi e delle app per le app di messaggistica sono basate sulle tecnologie di estensioni delle app iOS preesistenti. Per altre informazioni sulle estensioni dell'app, vedere la [Guida alla programmazione dell'estensione app](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)di Apple.

A differenza di altri punti di estensione forniti da Apple in tutto il sistema, lo sviluppatore non deve fornire un'app host per le estensioni delle app per i messaggi perché l'app del messaggio stessa funge da contenitore. Tuttavia, lo sviluppatore ha la possibilità di includere l'estensione per le app per i messaggi all'interno di un'app iOS nuova o esistente e di spedirla insieme al bundle.

Se le estensioni delle app del messaggio sono incluse in un bundle dell'app iOS, l'icona dell'app verrà visualizzata nella schermata iniziale del dispositivo e nel cassetto dell'app messaggio dall'app messaggi. Se non è incluso in un bundle dell'app, l'estensione per le app di messaggio verrà visualizzata solo nel cassetto dell'app del messaggio.

Anche se le estensioni delle app di messaggio non sono incluse in un bundle dell'app host, lo sviluppatore dovrà fornire un'icona dell'app nel bundle dell'estensione app del messaggio, in quanto si tratta dell'icona che verrà visualizzata in altre parti del sistema, ad esempio il cassetto o le impostazioni dell'app Message. , per l'estensione.

## <a name="about-stickers"></a>Informazioni sugli adesivi

Apple ha progettato gli adesivi come un nuovo modo per consentire agli utenti di iMessage di comunicare consentendo l'invio inline degli adesivi come qualsiasi altro contenuto del messaggio oppure possono essere collegati alle bolle di messaggio precedenti all'interno della conversazione.

Che cosa sono gli adesivi?

- Sono immagini fornite dall'estensione delle app per i messaggi.
- Possono essere immagini animate o statiche.
- Forniscono un nuovo modo per condividere il contenuto dell'immagine dall'interno di un'app.

Esistono due modi per creare adesivi:

1. È possibile creare estensioni di app per i messaggi di un adesivo Pack dall'interno di Xcode senza includere codice. Sono necessari solo gli asset per gli adesivi e le icone dell'app.
2. Creando un'estensione per le app di messaggio standard che fornisce adesivi dal codice tramite il Framework dei messaggi.

### <a name="creating-sticker-packs"></a>Creazione di pacchetti adesivi

Gli sticker Pack vengono creati da un modello speciale all'interno di Xcode e forniscono semplicemente un set statico di asset di immagini che possono essere usati come adesivi. Come indicato in precedenza, non richiedono alcun codice, lo sviluppatore trascina semplicemente i file di immagine nella cartella sticker Pack all'interno del catalogo asset degli adesivi.

Per includere un'immagine in un pacchetto adesivo, è necessario che soddisfi i requisiti seguenti:

- Le immagini devono essere in formato PNG, APNG, GIF o JPEG. Apple suggerisce di usare solo i formati PNG e APNG quando si forniscono asset adesivo.
- Gli adesivi animati supportano solo i formati APNG e GIF.
- Le immagini adesive dovrebbero fornire uno sfondo trasparente perché possono essere posizionate sopra le bolle del messaggio nella conversazione da parte dell'utente.
- I singoli file di immagine devono essere inferiori a 500 KB.
- Le immagini non possono essere più piccole di 100x100 punti o più grandi di 206 x 206 punti.

> [!IMPORTANT]
> Le immagini adesive devono sempre essere fornite con la risoluzione `@3x` nell'intervallo di pixel da 300 x 300 a 618 x 618. Il sistema genererà automaticamente le versioni `@2x` e `@1x` in fase di esecuzione, come richiesto.

Apple suggerisce di testare le risorse dell'immagine adesiva su diversi sfondi colorati, ad esempio bianco, nero, rosso, giallo e multicolore, e su foto, per assicurarsi che risultino il migliore in tutte le situazioni possibili.

Gli sticker Pack possono fornire adesivi in una delle tre dimensioni disponibili:

- **Piccoli** punti 100 x 100.
- **Media** -136 x 136 punti. Si tratta delle dimensioni predefinite.
- **Grandi** -206 x 206 punti.

Usare il controllo attributi di Xcode per impostare le dimensioni per l'intero pacchetto di adesivi e fornire solo asset di immagine corrispondenti alle dimensioni richieste, per ottenere risultati ottimali nel browser sticker all'interno dell'app Messages.

Per altre informazioni, vedere l'app del [Generatore di ghiaccio](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder) e la Guida di riferimento ai [messaggi](https://developer.apple.com/reference/messages)di Apple.

## <a name="creating-a-custom-sticker-experience"></a>Creazione di un'esperienza adesiva personalizzata

Se l'app richiede un maggiore controllo o flessibilità rispetto a quanto fornito da un adesivo Pack, può includere un'estensione di app per i messaggi e fornire gli adesivi tramite il Framework dei messaggi per un'esperienza adesiva personalizzata.

Quali sono i vantaggi della creazione di un'esperienza adesiva personalizzata?

1. Consente all'app di personalizzare la modalità di visualizzazione degli adesivi per gli utenti dell'app. Ad esempio, per presentare gli adesivi in un formato diverso dal layout di griglia standard o su uno sfondo colorato diverso.
2. Consente di creare dinamicamente adesivi dal codice anziché essere inclusi come asset di immagini statiche.
3. Consente di scaricare dinamicamente le risorse dell'immagine adesiva dal server Web dello sviluppatore senza dover rilasciare una nuova versione nell'App Store.
4. Consente l'accesso alla fotocamera del dispositivo per creare adesivi in tempo reale.
5. Consente acquisti in-app, in modo che l'utente possa acquistare altri adesivi dall'interno dell'app.

Per creare un'esperienza adesiva personalizzata, seguire questa procedura:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Avviare Visual Studio per Mac.
2. Aprire la soluzione per aggiungere un'estensione dell'app Message a.
3. Selezionare **iOS** > **Extensions** > **estensione iMessage** , quindi fare clic sul pulsante **Avanti** :

    [![](intro-to-message-app-extensions-images/message01.png "Select iMessage Extension")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. Immettere un **nome di estensione** e fare clic sul pulsante **Avanti** :

    [![](intro-to-message-app-extensions-images/message02.png "Enter an Extension Name")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. Fare clic sul pulsante **Crea** per compilare l'estensione:

    [![](intro-to-message-app-extensions-images/message03.png "Click the Create button")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avviare Visual Studio.
2. Aprire la soluzione per aggiungere un'estensione di app per i messaggi.
3. Selezionare **estensioni ios > estensione IMessage (iOS)** e fare clic sul pulsante **Avanti** :

    [![selezionare l'estensione iMessage (iOS)](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. Immettere un **nome** e fare clic sul pulsante **OK**

-----

Per impostazione predefinita, il file di `MessagesViewController.cs` verrà aggiunto alla soluzione. Si tratta del punto di ingresso principale nell'estensione che eredita dalla classe `MSMessageAppViewController`.

Il Framework dei messaggi fornisce le classi per la presentazione degli adesivi disponibili all'utente:

- `MSStickerBrowserViewController`: controlla la visualizzazione in cui verranno presentati gli adesivi. È inoltre conforme all'interfaccia `IMSStickerBrowserViewDataSource` per restituire il numero di adesivi e l'adesivo per un determinato indice del browser.
- `MSStickerBrowserView`: questa è la visualizzazione in cui verranno visualizzati gli adesivi disponibili.
- `MSStickerSize`: decide le dimensioni delle singole celle per la griglia degli adesivi presentati nella visualizzazione del browser.

### <a name="creating-a-custom-sticker-browser"></a>Creazione di un browser personalizzato per gli adesivi

Lo sviluppatore può ulteriormente personalizzare l'esperienza adesiva per l'utente fornendo un browser personalizzato per gli adesivi (`MSMessageAppBrowserViewController`) nell'estensione dell'app per i messaggi. Il Visualizzatore adesivo personalizzato modifica il modo in cui gli adesivi vengono presentati all'utente quando seleziona un adesivo da includere nel flusso di messaggi.

Procedere come descritto di seguito:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **riquadro della soluzione**fare clic con il pulsante destro del mouse sul nome del progetto dell'estensione e scegliere **Aggiungi** > **nuovo file...**  > **iOS | Apple Watch** > **controller di interfaccia**.
2. Immettere `StickerBrowserViewController` per il **nome** e fare clic sul pulsante **nuovo** :

    [![](intro-to-message-app-extensions-images/browser01.png "Enter StickerBrowserViewController for the Name")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. Aprire il file di `StickerBrowserViewController.cs` per la modifica.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto dell'estensione e scegliere **Aggiungi** > **nuovo file...**  > **iOS | Apple Watch** > **controller di interfaccia**.
2. Immettere `StickerBrowserViewController` per il **nome** e fare clic sul pulsante **nuovo** :

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "Enter StickerBrowserViewController for the Name")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. Aprire il file di `StickerBrowserViewController.cs` per la modifica.

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

Esaminare il codice sopra riportato in dettaglio. Crea spazio di archiviazione per gli adesivi forniti dall'estensione:

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

Ed esegue l'override di due metodi della classe `MSStickerBrowserViewController` per fornire i dati per il browser da questo archivio dati:

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

Il metodo `CreateSticker` ottiene il percorso di un asset immagine dal bundle dell'estensione e lo usa per creare una nuova istanza di una `MSSticker` da questo asset, che aggiunge alla raccolta:

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

Il metodo `LoadSticker` viene chiamato da `ViewDidLoad` per creare un adesivo dall'asset immagine denominata (incluso nel bundle dell'app) e aggiungerlo alla raccolta di adesivi.

Per implementare il browser Sticker personalizzato, modificare il file di `MessagesViewController.cs` e renderlo simile al seguente:

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

Esaminando il codice in dettaglio, viene creata una risorsa di archiviazione per il browser personalizzato:

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

Nel metodo `ViewDidLoad`, viene creata un'istanza e viene configurato un nuovo browser:

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

Quindi aggiunge il browser alla visualizzazione per visualizzarlo:

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>Personalizzazione ulteriore adesivo

È possibile personalizzare ulteriormente gli adesivi includendo solo due classi nell'estensione dell'app Message:

- `MSStickerView`
- `MSSticker`

Usando i metodi precedenti, l'estensione può supportare la selezione di adesivi che non si basa sul metodo standard del browser sticker. Inoltre, la visualizzazione dell'adesivo può essere cambiata tra due modalità di visualizzazione diverse:

- **Compact** : questa è la modalità predefinita in cui la visualizzazione adesivo occupa il 25% inferiore della visualizzazione messaggio.
- **Espanso** : la visualizzazione adesiva riempie l'intera visualizzazione messaggio.

Questa vista adesiva può essere spostata tra le due modalità a livello di codice o manualmente dall'utente.

Esaminare l'esempio seguente di gestione del passaggio tra le due diverse modalità di visualizzazione. Per ogni stato saranno necessari due diversi controller di visualizzazione. Il `StickerBrowserViewController` gestisce la vista **compatta** e ha un aspetto simile al seguente:

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

Il `AddStickerViewController` gestirà la visualizzazione adesiva **espansa** e sarà simile alla seguente:

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

Il `MessageViewController` implementa i controller di visualizzazione per guidare lo stato richiesto:

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

Quando l'utente richiede di aggiungere un nuovo adesivo alla raccolta disponibile, viene creato un nuovo `AddStickerViewController` il controller visibile e la visualizzazione adesivo entra nella visualizzazione **espansa** :

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

Quando l'utente sceglie un adesivo da aggiungere, viene aggiunto alla raccolta disponibile e viene richiesta la visualizzazione **compatta** :

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
}
```

Viene eseguito l'override del metodo `DidTransition` per gestire il cambio tra le due modalità:

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

Questo articolo include un'estensione di app per i messaggi in una soluzione Xamarin.iOS che si integra con l'app **messages** e presenta nuove funzionalità all'utente. Viene analizzato utilizzando l'estensione per inviare testo, adesivi, file multimediali e messaggi interattivi.

## <a name="related-links"></a>Collegamenti correlati

- [Ice Cream Builder (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [Riferimento ai messaggi](https://developer.apple.com/reference/messages)
- [Guida alla programmazione delle estensioni dell'app](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
