---
title: Nozioni di base dell'estensione App messaggio
description: "In questo articolo mostra come include un messaggio dell'estensione App in una soluzione xamarin. IOS che si integra con l'app di messaggi e presenta nuove funzionalità all'utente."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 7da0f52816d4efc102e830411589e0ac38a73574
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="message-app-extension-basics"></a>Nozioni di base dell'estensione App messaggio

_In questo articolo mostra come include un messaggio dell'estensione App in una soluzione xamarin. IOS che si integra con l'app di messaggi e presenta nuove funzionalità all'utente._

Nuovo in iOS 10, un messaggio dell'estensione App si integra con il **messaggi** nuove funzionalità app e visualizza all'utente. L'estensione può inviare il testo, adesivi, file multimediali e messaggi interattivi.

## <a name="about-message-app-extensions"></a>Informazioni sulle estensioni App messaggio

Come descritto in precedenza, un messaggio dell'estensione App si integra con il **messaggi** nuove funzionalità app e visualizza all'utente. L'estensione può inviare il testo, adesivi, file multimediali e messaggi interattivi. Sono disponibili due tipi di messaggio dell'estensione dell'App:

- **Pack adesivo** -contiene una raccolta di adesivi che l'utente può aggiungere a un messaggio. È possibile creare pacchetti di codice senza scrivere alcun codice.
- **iMessage App** -possono presentare un'interfaccia utente personalizzata all'interno dell'app di messaggi per la selezione adesivi, immissione di testo, inclusi i file di supporto (con le conversioni di tipo facoltativi) e creazione, modifica e l'invio di messaggi di interazione.

Le estensioni App messaggio forniscono tre tipi di contenuto principali:

- **Messaggi interattivi** -sono un tipo di contenuto del messaggio personalizzato che genera un'app, quando l'utente tocca nel messaggio, l'app verrà avviata in primo piano.
- **Adesivi** -sono immagini generate dall'applicazione che può essere incluso nei messaggi inviati tra gli utenti.
- **Altro contenuto supportato** : l'applicazione può visualizzare il contenuto, ad esempio foto, video, testo o tipi di collegamenti a tutti gli altri contenuti sono sempre supportati dall'app messaggi.

Nuovo in iOS 10, l'app messaggio include ora il proprio archivio di applicazione predefinito, dedicato. Qualsiasi App che includono le estensioni App messaggio verrà visualizzata e innalzate di livello nell'archivio. Il nuovo pannello App messaggi visualizzerà le app che sono state scaricate dall'App Store di messaggi per fornire l'accesso rapido agli utenti.

Anche nuovi in iOS 10, Apple ha aggiunto Inline App Attribution che consente all'utente di individuare facilmente un'app. Ad esempio, se un utente invia il contenuto a un altro da un'app che l'utente 2 non è installato (ad esempio, un'etichetta applicata ad esempio), il nome dell'applicazione mittente è elencato sotto il contenuto della cronologia del messaggio. Se l'utente tocca l'app il nome, è possibile aprire l'archivio di App messaggi e l'app selezionata nell'archivio.

Le estensioni app di messaggio sono simili alle App iOS esistenti che lo sviluppatore è familiarità con la creazione e avranno accesso a tutti i Framework standard e le funzionalità di un'app iOS standard. Ad esempio:

- Dispongono di accesso per l'acquisto In-App.
- Hanno accesso a pagamento di Apple.
- Hanno accesso al dispositivo hardware, ad esempio la fotocamera.

Estensioni per App i messaggi sono supportate solo in iOS 10, tuttavia, il contenuto che inviano queste estensioni è visibile nei dispositivi watchOS e macOS. Il nuovo _Recents Page_ aggiunto watchOS 3, verrà visualizzare adesivi recenti che sono stati inviati dal telefono, comprese quelle di estensioni di App di messaggio, e consentire all'utente di inviare tali adesivi dall'orologio.

## <a name="about-the-messages-framework"></a>Sul Framework di messaggi

Nuovo in iOS 10, il framework di messaggi fornisce l'interfaccia tra l'estensione di App di messaggio e il messaggio app nel dispositivo iOS dell'utente. Quando l'utente avvia un'App da all'interno dell'applicazione di messaggi, questo framework consente all'app di essere individuati e fornisce i dati e il contesto necessario per il layout dell'interfaccia utente di.

Una volta che viene avviata l'app, l'utente interagisce con la creazione di nuovo contenuto da condividere tramite un messaggio. L'app Usa quindi il framework di messaggi per il trasferimento del contenuto appena creato per l'app di messaggi per l'elaborazione.

Il framework di messaggi e il messaggio App estensioni si avvalgono di tecnologie di estensioni dell'App di iOS preesistente. Per ulteriori informazioni sulle estensioni di App, vedere Apple [Guida per programmatori estensione App](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

A differenza di altri punti di estensione che Apple ha fornito in tutto il sistema, lo sviluppatore non dovrà fornire un'applicazione host per le estensioni App messaggio poiché l'app di messaggio funge da contenitore. Tuttavia, lo sviluppatore ha la possibilità di inclusa l'estensione App messaggio all'interno di un'app iOS nuovo o esistente e distribuirlo con il pacchetto.

Se le estensioni App messaggio incluso nel bundle di un'app iOS, sull'icona dell'applicazione verrà visualizzato nella schermata iniziale del dispositivo e nel messaggio App cassetto all'interno dell'app di messaggi. Se non è incluso in un bundle dell'app, l'estensione per le app messaggio verrà visualizzato solo nel cassetto App messaggio.

Anche se le estensioni App del messaggio non è incluso in un bundle di app di host, lo sviluppatore dovrà fornire un'icona di app in pacchetto dell'estensione App messaggio, poiché questa è l'icona che verrà visualizzato in altre parti del sistema, ad esempio le impostazioni o messaggio App cassetto , per l'estensione.

## <a name="about-stickers"></a>Sulle etichette

Apple progettata adesivi come un nuovo modo per gli utenti iMessage comunicare consentendo adesivi da inviare inline come qualsiasi altro contenuto del messaggio o può essere collegati a bolle messaggio precedente all'interno della conversazione.

Quali sono adesivi?

- Sono le immagini che fornisce l'estensione di App di messaggio.
- Possono essere statiche o immagini.
- Forniscono un nuovo modo per condividere il contenuto di immagine all'interno di un'app.

Esistono due modi per creare adesivi:

1. È possibile creare estensioni di App un adesivo Pack messaggio all'interno di Xcode senza includere alcun codice. È necessario solo le risorse per le etichette e le icone dell'app.
2. Tramite la creazione di un'estensione di App di messaggio standard che fornisce adesivi dal codice tramite il framework di messaggi.

### <a name="creating-sticker-packs"></a>Creazione di pacchetti di codice

È sufficiente fornire un set statico di asset di immagine che può essere utilizzato come adesivi adesivo Pack perché vengono create da un modello speciale all'interno di Xcode. Come descritto in precedenza, non richiedono alcun codice, lo sviluppatore trascina semplicemente i file di immagine nella cartella adesivo Pack nel catalogo di Asset adesivi.

Per un'immagine da includere in un pacchetto di codice, deve soddisfare i requisiti seguenti:

- Le immagini devono essere in formato PNG, APNG, GIF o JPEG. Apple consiglia di usare solo i formati di file PNG e APNG quando si specifica asset adesivo.
- Adesivi animati supportano solo i formati APNG e GIF.
- Immagini adesivo devono fornire uno sfondo trasparente, perché possono essere posizionati su bolle di messaggio nella conversazione dall'utente.
- I singoli file di immagine devono essere inferiore a 500kb.
- Le immagini non possono essere inferiore a 100 x 100 punti o superiori che punta 206 x 206.

> [!IMPORTANT]
> **Nota:** immagini adesivo devono sempre essere specificate nel `@3x` risoluzione nell'intervallo 300 x 300 per 618 x 618 pixel. Il sistema genera automaticamente il `@2x` e `@1x` versioni in fase di esecuzione in base alle esigenze.

Apple suggerisce gli asset di immagini adesivo contro varie sfondi diversi (ad esempio bianco, nero, rosso, giallo e multi-colorate) e su foto, per garantire che vengano visualizzate le migliori in tutte le possibili situazioni di test.

Pacchetti di codice possono fornire adesivi in uno dei tre formati disponibili:

- **Piccola** - 100 x 100 punti.
- **Supporto** - 136 x 136 punti. Questa è la dimensione predefinita.
- **Grande** - 206 x 206 punti.

Utilizzare controllo del Xcode di attributi per impostare le dimensioni per l'intero pacchetto di codice e fornire solo gli asset delle immagini che corrispondano alla dimensione richiesta, per ottenere risultati ottimali nel Browser adesivo all'interno dell'app di messaggi.

Per ulteriori informazioni, vedere il nostro [gelato generatore](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) app e Apple [messaggi riferimento](https://developer.apple.com/reference/messages).

## <a name="creating-a-custom-sticker-experience"></a>Creazione di un'esperienza di etichetta personalizzata

Se l'applicazione richiede più controllo o flessibilità rispetto a quella fornita da un pacchetto di codice, può includere un'estensione di App di messaggio e fornire gli adesivi tramite il framework di messaggi per un'esperienza di etichetta personalizzata.

Quali sono i vantaggi della creazione di un'esperienza di codice personalizzato?

1. Consente all'app di personalizzare la modalità di visualizzazione delle etichette per gli utenti dell'app. Ad esempio, per adesivi presenti in un formato diverso dal layout di griglia standard o su uno sfondo di colore diverso.
2. Consente di adesivi da creare in modo dinamico dal codice anziché inclusa come asset immagine statica.
3. Consente gli asset delle immagini adesivo da scaricare in modo dinamico dal server web per gli sviluppatori senza dover rilasciare una nuova versione all'App Store.
4. Consente l'accesso della fotocamera del dispositivo per creare adesivi il volo.
5. Consente di acquisti In-App in modo l'utente è possibile acquistare ulteriori adesivi all'interno dell'app.

Per la creazione di un'esperienza di codice personalizzato, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Avviare Visual Studio per Mac.
2. Aprire la soluzione per aggiungere un'estensione App messaggio. 
3. Selezionare **iOS** > **estensioni** > **iMessage estensione** e fare clic su di **Avanti** pulsante: 

    [ ![](intro-to-message-app-extensions-images/message01.png "Selezionare l'estensione iMessage")](intro-to-message-app-extensions-images/message01.png)
4. Immettere un **nome estensione** e fare clic su di **Avanti** pulsante: 

    [ ![](intro-to-message-app-extensions-images/message02.png "Immettere un nome di estensione")](intro-to-message-app-extensions-images/message02.png)
5. Fare clic su di **crea** per compilare l'estensione: 

    [ ![](intro-to-message-app-extensions-images/message03.png "Fare clic sul pulsante Crea")](intro-to-message-app-extensions-images/message03.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Avviare Visual Studio.
2. Aprire la soluzione per aggiungere un'estensione App messaggio. 
3. Selezionare **iOS** > **estensioni** > **iMessage estensione** e fare clic su di **Avanti** pulsante: 

    [ ![](intro-to-message-app-extensions-images/message01w.png "Selezionare l'estensione iMessage")](intro-to-message-app-extensions-images/message01.png)
4. Immettere un **nome estensione** e fare clic su di **OK** pulsante

-----

Per impostazione predefinita, il `MessagesViewController.cs` file verrà aggiunto alla soluzione. Questo è il punto di ingresso principale nell'estensione ed eredita dalla `MSMessageAppViewController` classe.

Il framework di messaggi fornisce classi per la presentazione adesivi disponibili all'utente:

- `MSStickerBrowserViewController` : Controlla la vista che verranno visualizzati l'adesivi in. Inoltre è conforme al `IMSStickerBrowserViewDataSource` interfaccia per restituire il numero di etichetta e l'etichetta per un indice specificato del browser.
- `MSStickerBrowserView` -Si tratta della visualizzazione che verranno visualizzati gli adesivi disponibili in.
- `MSStickerSize` -Decide le dimensioni della cella singola per la griglia di adesivi presentati nella vista del browser.

### <a name="creating-a-custom-sticker-browser"></a>Creazione di un Browser di etichetta personalizzata

Lo sviluppatore può personalizzare ulteriormente l'esperienza di codice per l'utente, fornendo un Browser di etichetta personalizzata (`MSMessageAppBrowserViewController`) nell'estensione di App di messaggio. Il Browser adesivo personalizzato viene modificato come adesivi vengono presentati all'utente quando si seleziona un adesivo da includere nel flusso di messaggi.

Seguire questa procedura:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **soluzione riempimento**, fare clic sul nome del progetto di estensione e selezionare **Aggiungi** > **nuovo File...**   >  **iOS | Apple Watch** > **interfaccia Controller**.
2. Immettere `StickerBrowserViewController` per il **nome** e fare clic su di **New** pulsante: 

    [ ![](intro-to-message-app-extensions-images/browser01.png "Immettere il nome StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.png)
3. Aprire il `StickerBrowserViewController.cs` file per la modifica.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Nel **Esplora**, fare clic sul nome del progetto di estensione e selezionare **Aggiungi** > **nuovo File...**   >  **iOS | Apple Watch** > **interfaccia Controller**.
2. Immettere `StickerBrowserViewController` per il **nome** e fare clic su di **New** pulsante: 

    [ ![](intro-to-message-app-extensions-images/browser01w.png "Immettere il nome StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.png)
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

Dare un'occhiata al codice precedente in modo dettagliato. Viene creata l'archiviazione per adesivi che fornisce l'estensione:

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

Ed esegue l'override di due metodi per la `MSStickerBrowserViewController` classe che fornisce dati per il browser dall'archivio dati:

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

Il `CreateSticker` metodo ottiene il percorso di un asset di immagine dal pacchetto dell'estensione e viene utilizzato per creare una nuova istanza di un `MSSticker` da questo asset, che viene aggiunto alla raccolta:

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

Il `LoadSticker` metodo viene chiamato da `ViewDidLoad` per creare un'etichetta applicata dall'asset di immagini denominate (incluso nel bundle dell'app) e aggiungerlo alla raccolta di adesivi.

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

Osservare il codice in dettaglio, viene creata l'archiviazione per il browser personalizzato:

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

E il `ViewDidLoad` metodo, crea un'istanza e si configura un nuovo browser:

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

Aggiunge quindi il browser per la vista per visualizzarlo:

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>Ulteriore personalizzazione adesivo

Ulteriore personalizzazione di codice è possibile includendo solo due classi nell'estensione App messaggio:

- `MSStickerView`
- `MSSticker`

L'utilizzo dei metodi sopra l'estensione può supportare selezione adesivo che non si basano sul metodo adesivo Browser standard. Inoltre, la visualizzazione di codice può essere disattivata tra due modalità di visualizzazione diverse:

- **Compact** -questa è la modalità predefinita in cui la vista adesivo occupa il 25% della visualizzazione dei messaggi.
- **Espanso** -l'adesivo visualizzazione riempie l'intera vista di messaggio.

Questa vista adesivo è possibile passare da una modalità a livello di codice o manualmente dall'utente.

Esaminare l'esempio seguente di gestire il passaggio tra le due modalità di visualizzazione diverse. Due controller di visualizzazione diversi sarà necessario per ogni stato. Il `StickerBrowserViewController` handle di **Compact** visualizzazione e ha un aspetto simile alla seguente:

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

Il `AddStickerViewController` gestirà la **espansa** adesivo visualizzazione e l'aspetto simile al seguente:

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

Il `MessageViewController` implementa questi controller di visualizzazione per gestire lo stato della richiesto:

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

Quando l'utente richiede di aggiungere una nuova etichetta per la raccolta disponibile, un nuovo `AddStickerViewController` diventa visibile controller e la visualizzazione adesivo immette il **espansa** Vista:

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

Quando l'utente sceglie un adesivo per aggiungere, aggiungerlo alla propria raccolta disponibile e **Compact** viene richiesto di visualizzazione:

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
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

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati include un'estensione di App di messaggio in una soluzione xamarin. IOS che si integra con il **messaggi** app e presenti nuove funzionalità per l'utente. Descritta utilizzando l'estensione per inviare messaggi interattivi, adesivi, file multimediali e testo.



## <a name="related-links"></a>Collegamenti correlati

- [Generatore di gelato (esempio)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Riferimento di messaggi](https://developer.apple.com/reference/messages)
- [Guida per programmatori di estensione dell'App](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
