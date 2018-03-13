---
title: Notifiche utente avanzato
description: "In questo articolo richiede un'analisi approfondita relativa al nuovo framework notifiche utente e le modalità di usufruire appieno di esso in un'app xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: e20c9f068fe92e0dd711e0c20d31d713a0d03f0d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="advanced-user-notifications"></a>Notifiche utente avanzato

_In questo articolo richiede un'analisi approfondita relativa al nuovo framework notifiche utente e le modalità di usufruire appieno di esso in un'app xamarin._

Nuovo a 10, la notifica all'utente consente di framework per la gestione delle notifiche locali e remote e il recapito di iOS. Usando questo framework, un'app o l'estensione dell'app può pianificare il recapito delle notifiche locale specificando un set di condizioni, ad esempio percorso o l'ora del giorno.

## <a name="about-user-notifications"></a>Informazioni sulle notifiche utente

Il nuovo framework di notifica utente consente per la gestione delle notifiche locali e remote e il recapito. Usando questo framework, un'app o l'estensione dell'App può pianificare il recapito delle notifiche locale specificando un set di condizioni, ad esempio percorso o l'ora del giorno.

Inoltre, l'app o l'estensione può ricevere (e potenzialmente modificare) le notifiche locali e remote come vengano recapitati a dispositivo iOS dell'utente.

Il nuovo framework di interfaccia utente di notifica utente consente a un'app o l'estensione dell'App per personalizzare l'aspetto delle notifiche locali e remote quando sono presentati all'utente.

Questo framework fornisce le seguenti che un'app è possibile recapitare le notifiche a un utente:

- **Avvisi Visual** , in cui la notifica esegue il roll verso il basso dalla parte superiore della schermata come un'intestazione.
- **Audio e alle vibrazioni** -può essere associato a una notifica.
- **Icona di App Badging** : se l'icona dell'applicazione consente di visualizzare una notifica che indica che il nuovo contenuto è disponibile. Ad esempio il numero di messaggi di posta elettronica non letta.

Inoltre, a seconda del contesto corrente dell'utente, esistono diversi modi in cui verrà visualizzata una notifica:

- Se il dispositivo è sbloccato, la notifica viene continuata verso il basso dalla parte superiore della schermata come banner.
- Se il dispositivo è bloccato, la notifica verrà visualizzata nella schermata di blocco dell'utente.
- Se l'utente venga persa una notifica, possono aprire il centro notifiche e visualizzare le notifiche di attesa disponibili, non esiste.

Un'app xamarin è disponibili due tipi di notifiche utente che è in grado di inviare:

- **Le notifiche locale** -queste vengono inviate da applicazioni installate localmente nel dispositivo dell'utente.
- **Le notifiche remote** - vengono inviati da un server remoto e non è presentato all'utente o un aggiornamento in background di contenuto dell'applicazione attiva.

Per ulteriori informazioni, vedere il nostro [avanzata notifiche utente](~/ios/platform/user-notifications/enhanced-user-notifications.md) documentazione.

## <a name="the-new-user-notification-interface"></a>La nuova interfaccia notifica utente

Le notifiche utente in iOS 10 vengono visualizzate una nuova progettazione dell'interfaccia utente che fornisce una maggiore quantità di contenuto, ad esempio un titolo, sottotitolo e allegati multimediali facoltativo che può essere visualizzato nella schermata di blocco, come un'intestazione nella parte superiore del dispositivo o nel centro notifiche.

Indipendentemente da dove una notifica all'utente viene visualizzata in iOS 10, viene visualizzato con lo stesso aspetto e comportamento e le stesse caratteristiche e funzionalità.

In iOS 8, Apple ha introdotto le notifiche utilizzabili in cui lo sviluppatore può collegare azioni personalizzate a una notifica e consentire all'utente di eseguire azioni su una notifica senza la necessità di avviare l'app. In iOS 9, Apple migliorate utilizzabili notifiche con risposta rapida che consente all'utente di rispondere a una notifica con la voce di testo.

Poiché le notifiche utente sono una parte integrante più l'esperienza utente in iOS 10, Apple ha esteso ulteriormente le notifiche utilizzabili supportano 3D Touch, in cui l'utente preme una notifica e un'interfaccia utente personalizzata è visualizzato per fornire una notevole interazione con la notifica.

Quando viene visualizzata l'interfaccia utente personalizzata notifica utente, se l'utente interagisce con eventuali azioni associate alla notifica, è possibile aggiornare immediatamente l'interfaccia utente personalizzata per fornire commenti e suggerimenti da cosa è stata modificata.

Nuovo in iOS 10, l'API dell'interfaccia utente di notifica utente consente a un'app xamarin facilmente in modo da sfruttare queste nuove funzionalità dell'interfaccia utente notifica utente.

## <a name="adding-media-attachments"></a>Aggiunta di allegati multimediali

Uno degli elementi più comuni che ottengano condivisi tra gli utenti è foto, in modo iOS 10 aggiunta la possibilità di collegare un elemento multimediale (ad esempio una foto) direttamente a una notifica, in cui sarà disponibile immediatamente disponibili per l'utente insieme al resto del Crete della notifica NT.

Tuttavia, a causa delle dimensioni per l'invio anche una piccola immagine, collegarlo a un Payload di notifica remoto diventa poco pratica. Per gestire questa situazione, lo sviluppatore può utilizzare la nuova estensione servizio in iOS 10 per scaricare l'immagine da un'altra origine (ad esempio, un archivio dati CloudKit) e collegarlo al contenuto della notifica prima che venga visualizzato all'utente.

Per una notifica remoto deve essere modificato dall'estensione del servizio, il payload deve essere contrassegnato come modificabile. Ad esempio:

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

Guardare la panoramica del processo seguente:

[![](advanced-user-notifications-images/extension02.png "Aggiunta di allegati multimediali processo")](advanced-user-notifications-images/extension02.png#lightbox)

Dopo la notifica remoto viene recapitata al dispositivo (tramite APNs), l'estensione del servizio può quindi scaricare l'immagine necessaria tramite qualsiasi mezzo desiderato (ad esempio un `NSURLSession`) e dopo aver ricevuto l'immagine, è possibile modificare il contenuto della notifica e la visualizzazione l'utente.

Di seguito è riportato un esempio di come questo processo potrebbe essere gestito nel codice:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyNotification
{
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Constructors
        public NotificationService ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            // Get file URL
            var attachementPath = request.Content.UserInfo.ObjectForKey (new NSString ("my-attachment"));
            var url = new NSUrl (attachementPath.ToString ());

            // Download the file
            var localURL = new NSUrl ("PathToLocalCopy");

            // Create attachment
            var attachmentID = "image";
            var options = new UNNotificationAttachmentOptions ();
            NSError err;
            var attachment = UNNotificationAttachment.FromIdentifier (attachmentID, localURL, options , out err);

            // Modify contents
            var content = request.Content.MutableCopy() as UNMutableNotificationContent;
            content.Attachments = new UNNotificationAttachment [] { attachment };

            // Display notification
            contentHandler (content);
        }

        public override void TimeWillExpire ()
        {
            // Handle service timing out
        }
        #endregion
    }
}
```

Quando viene ricevuta la notifica da APNs, l'indirizzo personalizzato dell'immagine viene letto dal contenuto e file viene scaricato dal server. Un `UNNotificationAttachement` viene creato con un ID univoco e il percorso locale dell'immagine (come un `NSUrl`). Viene creata una copia modificabile di contenuto di notifica e vengono aggiunti gli allegati di supporto. Infine, la notifica viene visualizzata all'utente chiamando il `contentHandler`.

Dopo aver aggiunto un allegato a una notifica, il sistema assume lo spostamento e la gestione del file.

Oltre alle notifiche remoto presentato in precedenza, allegati multimediali sono supportati anche dalle notifiche locale, in cui il `UNNotificationAttachement` viene creato e collegato alla notifica e il relativo contenuto.

Notifica in iOS 10 supporta gli allegati di supporto di immagini (statico e gif), audio o video e il sistema visualizzerà automaticamente la corretta dell'interfaccia utente personalizzata per ognuno di questi tipi di allegati quando la notifica viene presentata all'utente.

> [!NOTE]
> **Nota:** deve prestare attenzione per ottimizzare la dimensione dei supporti e il tempo necessario per scaricare il supporto dal server remoto (o per assemblare il supporto per le notifiche locale) come sistema impone limiti strict sia quando si esegue il servizio dell'applicazione Estensione. Si consideri ad esempio l'invio di una versione ridimensionata verso il basso dell'immagine o un clip piccoli di un video a essere presentati nella notifica.

## <a name="creating-custom-user-interfaces"></a>Creazione di interfacce utente personalizzate

Per creare un'interfaccia utente personalizzata per le notifiche utente, lo sviluppatore deve aggiungere un'estensione di contenuto di notifica (nuovo in iOS 10) per la soluzione dell'app.

L'estensione di contenuto di notifica consente allo sviluppatore di aggiungere le visualizzazioni per l'interfaccia utente di notifica e disegnare qualsiasi contenuto desiderato. Tuttavia, i widget dell'interfaccia utente interattivi (ad esempio i pulsanti o i campi di testo) sono **non** supportata dall'interfaccia utente di notifica e non devono essere aggiunti a una progettazione dell'interfaccia utente personalizzata.

Per supportare l'interazione dell'utente con una notifica all'utente, azioni personalizzate deve essere create, registrate con il sistema e connesso alla notifica prima che venga pianificata con il sistema. L'estensione di contenuto di notifica verrà chiamato per gestire l'elaborazione di tali azioni. Vedere il [utilizzo di azioni di notifica](~/ios/platform/user-notifications/enhanced-user-notifications.md) sezione il [avanzata notifiche utente](~/ios/platform/user-notifications/enhanced-user-notifications.md) documento per ulteriori informazioni sulle azioni personalizzate.

Quando una notifica all'utente con un'interfaccia utente personalizzata viene presentata all'utente, avrà gli elementi seguenti:

[![](advanced-user-notifications-images/customui01.png "Una notifica all'utente con gli elementi di un'interfaccia utente personalizzata")](advanced-user-notifications-images/customui01.png#lightbox)

Se l'utente interagisce con le azioni personalizzate (riportati di seguito la notifica), l'interfaccia utente possono essere aggiornato per informare l'utente come la cosa è successo quando vengono richiamati una determinata azione.

### <a name="adding-a-notification-content-extension"></a>Aggiunta di un'estensione di contenuto di notifica

Per implementare un'interfaccia di notifica utente personalizzato in un'app xamarin. IOS, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic sul nome della soluzione nel **soluzione riempimento** e selezionare **Aggiungi** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **estensioni** > **notifica contenuto estensioni** e fare clic su di **Avanti** pulsante: 

    [![](advanced-user-notifications-images/notify01.png "Selezionare le estensioni di contenuto di notifica")](advanced-user-notifications-images/notify01.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic su di **Avanti** pulsante: 

    [![](advanced-user-notifications-images/notify02.png "Immettere un nome per l'estensione")](advanced-user-notifications-images/notify02.png#lightbox)
5. Regolare il **nome progetto** e/o **Nome soluzione** se necessario, fare clic su di **crea** pulsante: 

    [![](advanced-user-notifications-images/notify03.png "Modificare il nome del progetto e/o il nome della soluzione")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic sul nome della soluzione nel **Esplora** e selezionare **Aggiungi** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **estensioni** > **notifica contenuto estensioni**: 

    [![](advanced-user-notifications-images/notify01w.png "Selezionare le estensioni di contenuto di notifica")](advanced-user-notifications-images/notify01w.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic su di **OK** pulsante.

-----

Quando l'estensione di contenuto di notifica viene aggiunto alla soluzione, verranno creati nel progetto dell'estensione tre file:

1. `NotificationViewController.cs` -Questo è il controller di visualizzazione principale per l'estensione di contenuto di notifica.
2. `MainInterface.storyboard` -In cui lo sviluppatore definisce il layout di interfaccia utente visibile per l'estensione di contenuto di notifica nella finestra di progettazione iOS.
3. `Info.plist` : Controlla la configurazione dell'estensione di contenuto di notifica.

Il valore predefinito `NotificationViewController.cs` file avrà un aspetto simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

        }
        #endregion
    }
}
```

Il `DidReceiveNotification` viene chiamato durante la notifica viene espanso dall'utente in modo che l'estensione di contenuto di notifica è possibile popolare l'interfaccia utente personalizzata con il contenuto del `UNNotification`. Per l'esempio precedente, un'etichetta è stato aggiunto alla vista, esposta al codice con il nome `label` e viene utilizzato per visualizzare il corpo della notifica.

### <a name="setting-the-notification-content-extensions-categories"></a>Impostazione categorie dell'estensione di contenuto notifica

Il sistema deve essere informati su come trovare l'estensione di contenuto di notifica dell'app in base che risponda alle categorie specifiche. Seguire questa procedura:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic sull'estensione `Info.plist` file nel **soluzione riempimento** per aprirlo e modificarlo.
2. Passare il **origine** visualizzazione.
3. Espandere il `NSExtension` chiave.
4. Aggiungere il `UNNotificationExtensionCategory` chiave come tipo **stringa** con il valore della categoria a cui appartiene l'estensione (in questo esempio "evento invito): 

    [![](advanced-user-notifications-images/customui02.png "Aggiungere la chiave UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02.png#lightbox)
5. Salvare le modifiche.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic sull'estensione `Info.plist` file nel **Esplora** per aprirlo e modificarlo.
3. Espandere il `NSExtension` chiave.
4. Aggiungere il `UNNotificationExtensionCategory` chiave come tipo **stringa** con il valore della categoria a cui appartiene l'estensione (in questo esempio "evento invito): 

    [![](advanced-user-notifications-images/customui02w.png "Aggiungere la chiave UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02w.png#lightbox)
5. Salvare le modifiche.

-----

Notifica alle categorie di estensione contenuti (`UNNotificationExtensionCategory`) utilizzare gli stessi valori di categoria che consentono di registrare le azioni di notifica. Nel caso in cui l'app userà la stessa interfaccia utente per più categorie, passare il `UNNotificationExtensionCategory` al tipo di **matrice** e fornire tutte le categorie necessarie. Ad esempio:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](advanced-user-notifications-images/customui03.png "Categorie di estensione di contenuto di notifica")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](advanced-user-notifications-images/customui03w.png "Categorie di estensione di contenuto di notifica")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>Nascondere il contenuto di notifica predefinito

Nel caso in cui l'interfaccia utente notifica personalizzata verrà visualizzando il contenuto stesso come il valore predefinito di notifica (titolo, sottotitolo e corpo visualizzato automaticamente nella parte inferiore dell'interfaccia utente di notifica), queste informazioni predefinite possono essere nascosti tramite l'aggiunta di `UNNotificationExtensionDefaultContentHidden`chiave per la `NSExtensionAttributes` chiave come tipo **booleano** con un valore di `YES` l'estensione `Info.plist` file:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](advanced-user-notifications-images/customui04.png "Ricerca di informazioni predefinite")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](advanced-user-notifications-images/customui04w.png "Ricerca di informazioni predefinite")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>Progettare l'interfaccia utente personalizzata

Per progettare l'interfaccia utente personalizzata dell'estensione di contenuto notifica, fare doppio clic su di `MainInterface.storyboard` file per aprirlo e modificarlo in iOS, progettazione trascinare gli elementi che è necessario compilare l'interfaccia desiderata (ad esempio `UILabels` e `UIImageViews`).

> [!NOTE]
> **Nota:** l'interfaccia utente di notifica _non_ supportano controlli interattivi, ad esempio i campi di testo o i pulsanti in un'estensione di contenuto di notifica. Anche se possono essere aggiunti allo storyboard, l'utente non sarà in grado di interagire con essi. Per aggiungere l'interazione dell'utente a un'interfaccia utente di notifica personalizzata, utilizzare le azioni personalizzate.

Una volta è disposto l'interfaccia utente e i controlli necessari esposta al codice c#, aprire il `NotificationViewController.cs` per la modifica e modificare il `DidReceiveNotification` metodo per popolare l'interfaccia utente quando l'utente lo espande la notifica. Ad esempio:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

        }
        #endregion
    }
}
```

### <a name="setting-the-content-area-size"></a>Impostazione delle dimensioni dell'Area del contenuto

Per regolare le dimensioni dell'area del contenuto visualizzato all'utente, il codice riportato di seguito è l'impostazione di `PreferredContentSize` proprietà il `ViewDidLoad` metodo fino alle dimensioni desiderate. Impossibile regolata anche la dimensione tramite l'applicazione di vincoli per la visualizzazione nella finestra di progettazione iOS, viene lasciato agli sviluppatori di scegliere il metodo adatto alle loro.

Poiché la notifica di sistema è già in esecuzione prima della notifica viene richiamato l'estensione di contenuto, l'area del contenuto verrà inizialmente full dimensioni e animato fino alle dimensioni richieste quando presentato all'utente.

Per evitare questo effetto, è possibile modificare il `Info.plist` file per l'estensione e impostare il `UNNotificationExtensionInitialContentSizeRatio` la chiave del `NSExtensionAttributes` chiave nel tipo **numero** con un valore che rappresenta il rapporto desiderato. Ad esempio:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](advanced-user-notifications-images/customui05.png "La chiave UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](advanced-user-notifications-images/customui05w.png "La chiave UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>Utilizzo degli allegati di supporto nell'interfaccia utente personalizzata

Perché allegati multimediali (come illustrato nel [aggiunta di allegati multimediali](#Adding-Media-Attachments) sezione precedente) sono parte del Payload di notifica, accessibili e visualizzati nell'estensione di contenuto di notifica, esattamente come si troveranno nel valore predefinito Notifica dell'interfaccia utente.

Ad esempio, se l'interfaccia utente personalizzata precedente è incluso un `UIImageView` che è stato esposto al codice c#, il codice seguente può essere utilizzato per da popolare con l'allegato di supporto:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }
        #endregion
    }
}
```

Poiché l'allegato di supporti viene gestito dal sistema, è di fuori di sandbox dell'app. L'estensione deve informare il sistema che deve accedere al file tramite la chiamata di `StartAccessingSecurityScopedResource` metodo. Quando l'estensione viene eseguita con il file, è necessario chiamare il `StopAccessingSecurityScopedResource` per rilasciare la connessione.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Aggiunta di azioni personalizzate per un'interfaccia utente personalizzata

Come descritto in precedenza, l'interfaccia utente di notifica non supporta l'interazione dell'utente in modo controlli, ad esempio i campi di testo o pulsanti non sono supportati nella progettazione dell'interfaccia utente.

Il meccanismo standard di azioni personalizzate, invece, viene utilizzato per aggiungere interattività a un'interfaccia utente di notifica personalizzata. Vedere il [utilizzo di azioni di notifica](~/ios/platform/user-notifications/enhanced-user-notifications.md) sezione del [avanzata notifiche utente](~/ios/platform/user-notifications/enhanced-user-notifications.md) per ulteriori informazioni dettagliate sulle azioni personalizzate.

Oltre alle azioni personalizzate, l'estensione di contenuto di notifica può rispondere a nonché le azioni predefinite seguenti:

- **Azione predefinita** -si tratta di quando l'utente digita una notifica per aprire l'app e visualizzare i dettagli della notifica specificato.
- **Elimina azione** -questa azione viene inviata all'app quando l'utente chiude una notifica specificata.

Le estensioni di contenuto di notifica anche avere la possibilità di aggiornare l'interfaccia utente quando l'utente richiama una delle azioni personalizzate, ad esempio che illustra una data come accettato quando l'utente tocca il **Accept** pulsante di azione personalizzata. Inoltre, le estensioni del contenuto di notifica può indicare al sistema di ritardare il licenziamento dell'interfaccia utente di notifica in modo che l'utente può visualizzare l'effetto della loro azione prima che la notifica viene chiuso.

Questa operazione viene eseguita mediante l'implementazione di una versione di secondo il `DidReceiveNotification` metodo che include un gestore di completamento. Ad esempio:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;
using CoreGraphics;

namespace myApp {
    public class NotificationViewController : UIViewController, UNNotificationContentExtension {

        public override void ViewDidLoad() {
            base.ViewDidLoad();

            // Adjust the size of the content area
            var size = View.Bounds.Size
            PreferredContentSize = new CGSize(size.Width, size.Width/2);
        }

        public void DidReceiveNotification(UNNotification notification) {

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = Content.UserInfo["location"] as string;
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments[0];
                if (attachment.Url.StartAccessingSecurityScopedResource()) {
                    EventImage.Image = UIImage.FromFile(attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch(response.ActionIdentifier){
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
    }
}
```

Aggiungendo il `Server.PostEventResponse` gestore per il `DidReceiveNotification` metodo di estensione di contenuto notifica, l'estensione *deve* gestire tutte le azioni personalizzate. L'estensione può inoltre inviare le azioni personalizzate per l'applicazione contenitore modificando il `UNNotificationContentExtensionResponseOption`. Ad esempio:

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Utilizzo con l'azione di Input di testo personalizzato dell'interfaccia utente

A seconda della progettazione dell'app e la notifica, potrebbe essere volte in cui l'utente deve immettere il testo nella notifica (ad esempio per rispondere a un messaggio). Un'estensione di contenuto di notifica ha accesso all'azione di input di testo incorporato proprio come una notifica standard.

Ad esempio:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Computed Properties
        // Allow to take input
        public override bool CanBecomeFirstResponder {
            get { return true; }
        }

        // Return the custom created text input view with the
        // required buttons and return here
        public override UIView InputAccessoryView {
            get { return InputView; }
        }
        #endregion

        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Private Methods
        private UNNotificationCategory MakeExtensionCategory ()
        {

            // Create Accept Action
            ...

            // Create decline Action
            ...

            // Create Text Input Action
            var commentID = "comment";
            var commentTitle = "Comment";
            var textInputButtonTitle = "Send";
            var textInputPlaceholder = "Enter comment here...";
            var commentAction = UNTextInputNotificationAction.FromIdentifier (commentID, commentTitle, UNNotificationActionOptions.None, textInputButtonTitle, textInputPlaceholder);

            // Create category
            var categoryID = "event-invite";
            var actions = new UNNotificationAction [] { acceptAction, declineAction, commentAction };
            var intentIDs = new string [] { };
            var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);

            // Return new category
            return category;

        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Is text input?
            if (response is UNTextInputNotificationResponse) {
                var textResponse = response as UNTextInputNotificationResponse;
                Server.Send (textResponse.UserText, () => {
                    // Close Notification
                    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
                });
            }

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch (response.ActionIdentifier) {
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
        #endregion
    }
}
```

Questo codice crea una nuova azione di input di testo e lo aggiunge alla categoria dell'estensione (nel `MakeExtensionCategory`) metodo. Nel `DidReceive` override del metodo gestisce l'immissione di testo con il codice seguente:

```csharp
// Is text input?
if (response is UNTextInputNotificationResponse) {
    var textResponse = response as UNTextInputNotificationResponse;
    Server.Send (textResponse.UserText, () => {
        // Close Notification
        completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
    });
}
```

Se il progetto richiede per l'aggiunta di pulsanti personalizzati al campo di Input di testo, aggiungere il codice seguente per includerli:

```csharp
// Allow to take input
public override bool CanBecomeFirstResponder {
    get {return true;}
}

// Return the custom created text input view with the
// required buttons and return here
public override UIView InputAccessoryView {
    get {return InputView;}
}
```

Quando l'azione di commento viene attivato dall'utente, è necessario attivare sia il controller di visualizzazione e il campo di input di testo personalizzato:

```csharp
// Update UI when the user interacts with the
// Notification
Server.PostEventResponse += (response) {
    // Take action based on the response
    switch(response.ActionIdentifier){
    ...
    case "comment":
        BecomeFirstResponder();
        TextField.BecomeFirstResponder();
        break;
    }

    // Close Notification
    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);

};
```

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato un aspetto avanzato in usando il nuovo framework di notifica all'utente in un'app xamarin. IOS. Che trattato Media per aggiungere allegati locali e remoti notifica ed coperto utilizzando l'interfaccia utente nuova notifica utente di creare interfacce utente personalizzate di notifica.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Riferimento di Framework UserNotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guida per programmatori notifica locali e remoti](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
