---
title: Notifiche utente avanzate in xamarin. IOS
description: Questo articolo viene approfondito il framework per le notifiche utente, introdotto in iOS 10. Viene descritto notifiche utente, interfaccia utente di notifica, gli allegati multimediali, le interfacce utente personalizzate e altro ancora.
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 4472654064812142e3281374754ace0042b542bf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61089248"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Notifiche utente avanzate in xamarin. IOS

Novità di iOS 10, la notifica all'utente consente a framework per il recapito e la gestione delle notifiche locali e remote. Uso di questo framework, un'app o l'estensione dell'app può pianificare il recapito di notifiche locali specificando un set di condizioni, ad esempio posizione o l'ora del giorno.

## <a name="about-user-notifications"></a>Informazioni sulle notifiche utente

Il nuovo framework di notifica all'utente consente il recapito e la gestione delle notifiche locali e remote. Uso di questo framework, un'app o l'estensione dell'App può pianificare il recapito di notifiche locali specificando un set di condizioni, ad esempio posizione o l'ora del giorno.

Inoltre, l'app o l'estensione può ricevere (e potenzialmente modificare) le notifiche locali e remote quando vengono recapitate al dispositivo iOS dell'utente.

Il nuovo framework di interfaccia utente di notifica utente consente a un'app o l'estensione dell'App per personalizzare l'aspetto delle notifiche locali e remote quando sono presentati all'utente.

Questo framework offre le seguenti modalità che un'app può recapitare le notifiche a un utente:

- **Avvisi Visual** , in cui la notifica esegue il roll verso il basso dalla parte superiore dello schermo sotto forma di banner.
- **File audio e alle vibrazioni** -può essere associato a una notifica.
- **Badge sull'icona di app** : se l'icona dell'app Visualizza una notifica che mostra che diventano disponibili nuovi contenuti. Ad esempio il numero di messaggi di posta elettronica non letta.

Inoltre, a seconda del contesto corrente dell'utente, esistono diversi modi in cui verrà visualizzata una notifica:

- Se il dispositivo è sbloccato, la notifica viene continuata verso il basso dalla parte superiore dello schermo sotto forma di banner.
- Se il dispositivo è bloccato, la notifica verrà visualizzata nella schermata di blocco dell'utente.
- Se l'utente ha perso una notifica, possono aprire il centro notifiche e visualizzare le notifiche di attesa disponibili, non esiste.

Un'app xamarin. IOS include due tipi di notifiche utente che è in grado di inviare:

- **Le notifiche locali** -questi vengono inviati dall'App installate in locale nel dispositivo dell'utente.
- **Notifiche remote** : vengono inviati da un server remoto e non presentata all'utente o attiva un aggiornamento in background di contenuto dell'app.

Per altre informazioni, vedere la [notifiche utente avanzato](~/ios/platform/user-notifications/enhanced-user-notifications.md) documentazione.

## <a name="the-new-user-notification-interface"></a>La nuova interfaccia notifica utente

Notifiche utente in iOS 10 vengono visualizzate una nuova progettazione dell'interfaccia utente che fornisce altri contenuti, ad esempio un titolo, sottotitolo e allegati multimediali facoltativo che può essere visualizzato nella schermata di blocco, come Banner nella parte superiore del dispositivo o nel centro notifiche.

Indipendentemente da dove viene visualizzata una notifica utente in iOS 10, viene presentato con lo stesso aspetto e comportamento e le stesse caratteristiche e funzionalità.

In iOS 8, Apple ha introdotto le notifiche di utilità pratica in cui lo sviluppatore può collegare azioni personalizzate a una notifica e consentire all'utente di agire su una notifica senza avviare l'app. In iOS 9, Apple migliorate le notifiche di utilità pratica con risposta rapida che consente all'utente di rispondere a una notifica con l'immissione di testo.

Poiché le notifiche utente sono una parte più integrante dell'esperienza utente in iOS 10, Apple ha ampliato ulteriormente le notifiche di utilità pratica per supportare funzionalità Touch 3D, in cui l'utente preme su una notifica e un'interfaccia utente personalizzata viene visualizzato per fornire elevata interazione con la notifica.

Quando viene visualizzata l'interfaccia utente notifica utente personalizzata, se l'utente interagisce con qualsiasi azione associati alla notifica, l'interfaccia utente personalizzata può essere immediatamente aggiornato per fornire commenti e suggerimenti per quanto riguarda ciò che è stato modificato.

Nuovo ai dispositivi iOS 10, l'API dell'interfaccia utente di notifica utente consente a un'app xamarin. IOS sfruttare facilmente i vantaggi di queste nuove funzionalità dell'interfaccia utente notifica utente.

## <a name="adding-media-attachments"></a>Aggiunta di allegati multimediali

Uno degli elementi più comuni che ottengano condivisi tra gli utenti è foto, in modo che iOS 10 aggiunta la possibilità di collegare un elemento multimediale (ad esempio, una foto) direttamente a una notifica, dove sarà presentata e immediatamente disponibili per l'utente insieme al resto del conte della notifica NT.

Tuttavia, a causa delle dimensioni per l'invio diventa impraticabile anche un'immagine piccola, collegarlo a un Payload di notifica remota. Per gestire questa situazione, lo sviluppatore può utilizzare la nuova estensione servizio in iOS 10 per scaricare l'immagine di un'altra origine (ad esempio, un archivio dati CloudKit) e collegarlo al contenuto della notifica prima che venga visualizzato all'utente.

Per una notifica remota deve essere modificato dall'estensione del servizio, il payload deve essere contrassegnato come modificabile. Ad esempio:

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

Dare un'occhiata nella panoramica del processo seguente:

[![](advanced-user-notifications-images/extension02.png "Gli allegati multimediali processo di aggiunta")](advanced-user-notifications-images/extension02.png#lightbox)

Una volta remoto e viene recapitata al dispositivo (tramite il servizio APN), l'estensione del servizio può quindi scaricare le immagini necessarie in alcun modo desiderato (ad esempio un `NSURLSession`) e dopo aver ricevuto l'immagine, è possibile modificare il contenuto della notifica e la visualizzazione si tratta all'utente.

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

Quando la notifica viene ricevuta da APNs, l'indirizzo dell'immagine personalizzata viene letto dal contenuto e file viene scaricato dal server. Un' `UNNotificationAttachement` viene creato con un ID univoco e il percorso locale dell'immagine (come un `NSUrl`). Viene creata una copia modificabile di contenuto per le notifiche e gli allegati multimediali vengono aggiunti. Infine, viene visualizzata la notifica all'utente chiamando il `contentHandler`.

Dopo aver aggiunto un allegato a una notifica, il sistema subentra lo spostamento e la gestione del file.

Oltre a ricevere le notifiche Remote presentata nell'esempio precedente, gli allegati multimediali sono supportati anche dalle notifiche locali, in cui il `UNNotificationAttachement` viene creato e collegato alla notifica insieme ai relativi contenuti.

Notifica in iOS 10 supporta gli allegati multimediali delle immagini (statico e gif), audio o video e il sistema visualizzerà automaticamente l'interfaccia utente personalizzata corretta per ognuno di questi tipi di allegati durante la notifica viene presentata all'utente.

> [!NOTE]
> Dovrebbe prestare attenzione a ottimizzare sia le dimensioni di supporti e il tempo che necessario per scaricare il supporto dal server remoto (o per assemblare il supporto per le notifiche locali) come sistema impone limiti strict sia quando si esegue l'estensione del servizio dell'app. Si consideri ad esempio l'invio di una versione ridimensionata verso il basso dell'immagine o una clip di piccole dimensioni di un video da presentare nella notifica.

## <a name="creating-custom-user-interfaces"></a>Creazione di interfacce utente personalizzate

Per creare un'interfaccia utente personalizzata per le notifiche utente, lo sviluppatore deve aggiungere un'estensione per contenuto notifiche (nuova in iOS 10) alla soluzione dell'app.

L'estensione per contenuto notifiche consente allo sviluppatore di aggiungere le proprie visualizzazioni all'interfaccia utente di notifica e disegnare qualsiasi contenuto desiderato. A partire da iOS 12, le estensioni del contenuto di notifica supporta i controlli dell'interfaccia utente interattivi, quali pulsanti e i dispositivi di scorrimento. Per altre informazioni, vedere la [interattive notifiche in iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) documentazione.

Per supportare l'interazione dell'utente con una notifica all'utente, azioni personalizzate deve essere create, registrate con il sistema e associate alla notifica prima che venga pianificata nel sistema. L'estensione per contenuto notifiche verrà chiamato per gestire l'elaborazione di queste azioni. Vedere le [uso di azioni di notifica](~/ios/platform/user-notifications/enhanced-user-notifications.md) sezione del [notifiche utente avanzato](~/ios/platform/user-notifications/enhanced-user-notifications.md) documento per altri dettagli sulle azioni personalizzate.

Quando una notifica all'utente con un'interfaccia utente personalizzata viene presentata all'utente, avrà gli elementi seguenti:

[![](advanced-user-notifications-images/customui01.png "Una notifica all'utente con un elementi dell'interfaccia utente personalizzata")](advanced-user-notifications-images/customui01.png#lightbox)

Se l'utente interagisce con le azioni personalizzate (riportati di seguito la notifica), l'interfaccia utente può essere aggiornato per informare l'utente come la cosa è successo quando è richiamato una determinata azione.

### <a name="adding-a-notification-content-extension"></a>Aggiunta di un'estensione per contenuto notifiche

Per implementare un'interfaccia utente notifica utente personalizzata in un'app xamarin. IOS, effettuare le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic sul nome della soluzione nel **riquadro della soluzione** e selezionare **Add** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **estensioni** > **estensioni contenuto notifica** e fare clic su di **successivo** pulsante: 

    [![](advanced-user-notifications-images/notify01.png "Selezionare le estensioni del contenuto di notifica")](advanced-user-notifications-images/notify01.png#lightbox)
4. Immettere un **Name** per l'estensione e fare clic sui **successivo** pulsante: 

    [![](advanced-user-notifications-images/notify02.png "Immettere un nome per l'estensione")](advanced-user-notifications-images/notify02.png#lightbox)
5. Modificare il **nome progetto** e/o **Nome soluzione** se necessario e fare clic sui **crea** pulsante: 

    [![](advanced-user-notifications-images/notify03.png "Modificare il nome del progetto e/o il nome della soluzione")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic sul nome della soluzione nel **Esplora soluzioni** e selezionare **Aggiungi > Nuovo progetto...** .
3. Selezionare **Visual C# > estensioni iOS > estensione per contenuto notifiche**:

    [![](advanced-user-notifications-images/notify01.w157-sml.png "Selezionare le estensioni del contenuto di notifica")](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. Immettere un **Name** per l'estensione e fare clic sui **OK** pulsante.

-----

Quando l'estensione per contenuto notifiche viene aggiunto alla soluzione, tre file verranno creati nel progetto dell'estensione:

1. `NotificationViewController.cs` -Si tratta del controller di visualizzazione principale per l'estensione per contenuto notifiche.
2. `MainInterface.storyboard` -In cui lo sviluppatore definisce il layout di interfaccia utente visibile per l'estensione per contenuto notifiche in iOS Designer.
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

Il `DidReceiveNotification` metodo viene chiamato quando la notifica viene espanso dall'utente in modo che l'estensione per contenuto notifiche possibile popolare l'UI personalizzata con il contenuto del `UNNotification`. Nell'esempio precedente, un'etichetta è stato aggiunto alla vista, esposta al codice con il nome `label` e consente di visualizzare il corpo della notifica.

### <a name="setting-the-notification-content-extensions-categories"></a>Impostazione delle categorie di estensione per contenuto le notifiche

Il sistema deve essere informati su come trovare l'estensione per contenuto notifiche dell'app basato su che risponda alle categorie specifiche. Seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare doppio clic dell'estensione `Info.plist` del file nei **riquadro della soluzione** per aprirlo e modificarlo.
2. Passare al **origine** visualizzazione.
3. Espandere il `NSExtension` chiave.
4. Aggiungere il `UNNotificationExtensionCategory` chiave come tipo **stringa** con il valore della categoria a cui appartiene l'estensione (in questo esempio '-invito a un evento): 

    [![](advanced-user-notifications-images/customui02.png "Aggiungere la chiave UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02.png#lightbox)
5. Salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare doppio clic dell'estensione `Info.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
3. Espandere il `NSExtension` chiave.
4. Aggiungere il `UNNotificationExtensionCategory` chiave come tipo **stringa** con il valore della categoria a cui appartiene l'estensione (in questo esempio '-invito a un evento): 

    [![](advanced-user-notifications-images/customui02w.png "Aggiungere la chiave UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02w.png#lightbox)
5. Salvare le modifiche.

-----

Notifica alle categorie di estensione contenuti (`UNNotificationExtensionCategory`) usino gli stessi valori di categoria che vengono usati per registrare le azioni di notifica. Nel caso in cui l'app userà la stessa interfaccia utente per più categorie, passare il `UNNotificationExtensionCategory` al tipo **matrice** e fornire tutte le categorie necessari. Ad esempio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](advanced-user-notifications-images/customui03.png "Categorie di estensione per contenuto notifiche")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui03w.png "Categorie di estensione per contenuto notifiche")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>Se si nasconde il contenuto della notifica predefinito

Nel caso in cui l'interfaccia utente di notifica personalizzato venga visualizzato il contenuto stesso come il valore predefinito di notifica (titolo, sottotitolo e corpo visualizzate automaticamente nella parte inferiore dell'interfaccia utente di notifica), queste informazioni predefinite possono essere nascosti aggiungendo il `UNNotificationExtensionDefaultContentHidden`chiave per il `NSExtensionAttributes` principali come tipo **booleano** con il valore `YES` dell'estensione `Info.plist` file:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](advanced-user-notifications-images/customui04.png "Ricerca di informazioni predefinite")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui04w.png "Ricerca di informazioni predefinite")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>Progettare l'interfaccia utente personalizzata

Per progettare un'interfaccia utente personalizzata dell'estensione per contenuto le notifiche, fare doppio clic il `MainInterface.storyboard` file per aprirlo e modificarlo in iOS Designer, trascinare gli elementi che occorre per creare l'interfaccia desiderata (ad esempio `UILabels` e `UIImageViews`).

> [!NOTE]
> A partire da iOS 12, un'estensione per contenuto notifiche può includere controlli interattivi quali pulsanti e campi di testo. Per altre informazioni, vedere la [interattive notifiche in iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) documentazione.

Dopo che l'interfaccia utente è stato disposto e i controlli necessari esposti a C# code, aprire il `NotificationViewController.cs` per la modifica e modificare le `DidReceiveNotification` metodo per popolare l'interfaccia utente quando l'utente espande la notifica. Ad esempio:

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

### <a name="setting-the-content-area-size"></a>Impostare le dimensioni dell'area del contenuto

Per regolare le dimensioni dell'area del contenuto visualizzato all'utente, il codice seguente è l'impostazione di `PreferredContentSize` proprietà nel `ViewDidLoad` metodo fino alle dimensioni desiderate. Questa dimensione potrebbe essere modificata anche applicando i vincoli per la visualizzazione in iOS Designer, è affidata allo sviluppatore di selezionare il metodo adatto alle proprie esigenze.

Perché inizi la notifica di sistema è già in esecuzione prima della notifica viene richiamato l'estensione di contenuto, l'area di contenuto full ridimensionato e animare fino alla dimensione richiesta quando viene visualizzata all'utente.

Per eliminare questo effetto, modificare il `Info.plist` file per l'estensione e impostare il `UNNotificationExtensionInitialContentSizeRatio` la chiave delle `NSExtensionAttributes` chiave al tipo **numero** con un valore che rappresenta il rapporto desiderato. Ad esempio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](advanced-user-notifications-images/customui05.png "The UNNotificationExtensionInitialContentSizeRatio key")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui05w.png "The UNNotificationExtensionInitialContentSizeRatio key")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>Usando gli allegati multimediali nell'interfaccia utente personalizzata

Poiché gli allegati multimediali (come illustrato nel [aggiunta di allegati multimediali](#adding-media-attachments) sezione precedente) fanno parte del Payload della notifica, possono essere accessibili e visualizzati nell'estensione per contenuto notifiche esattamente come si troveranno nel valore predefinito Notifica dell'interfaccia utente.

Se, ad esempio, inclusa l'interfaccia utente personalizzata precedente un `UIImageView` che sono state esposte al C# di codice, il seguente codice può essere utilizzato per popolare il database da con il file multimediale allegato:

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

Poiché il file multimediale allegato è gestito dal sistema, è di fuori di sandbox dell'app. L'estensione deve fornire informazioni al sistema che deve accedere al file chiamando la `StartAccessingSecurityScopedResource` (metodo). Quando l'estensione viene eseguita con il file, è necessario chiamare il `StopAccessingSecurityScopedResource` per rilasciare la connessione.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Aggiunta di azioni personalizzate per un'interfaccia utente personalizzata

Pulsanti di azione personalizzato utilizzabile per aggiungere interattività a un'interfaccia utente notifica personalizzata. Vedere le [uso di azioni di notifica](~/ios/platform/user-notifications/enhanced-user-notifications.md) sezione del [notifiche utente avanzato](~/ios/platform/user-notifications/enhanced-user-notifications.md) documento per altri dettagli sulle azioni personalizzate.

Oltre alle azioni personalizzate, l'estensione per contenuto notifiche può rispondere a anche le azioni predefinite seguenti:

- **Azione predefinita** -si tratta di quando l'utente tocca una notifica per aprire l'app e visualizzare i dettagli di notifica specifica.
- **Elimina azione** -questa azione viene inviata all'app quando l'utente chiude una notifica specifica.

Estensioni contenuto notifica hanno anche la possibilità di aggiornare l'interfaccia utente quando l'utente richiama una delle azioni personalizzate, ad esempio che mostra una data come accettato quando l'utente tocca il **Accept** pulsante di azione personalizzata. Inoltre, le estensioni del contenuto di notifica può indicare al sistema di ritardo di licenziamento dell'interfaccia utente di notifica in modo che l'utente può visualizzare l'effetto del loro azione prima che la notifica viene chiuso.

Questa operazione viene eseguita mediante l'implementazione di una seconda versione del `DidReceiveNotification` metodo che include un gestore di completamento. Ad esempio:

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

Aggiungendo il `Server.PostEventResponse` gestore per il `DidReceiveNotification` metodo per l'estensione per contenuto notifiche, l'estensione *deve* gestire tutte le azioni personalizzate. L'estensione può anche inoltrare le azioni personalizzate con l'app contenitore modificando il `UNNotificationContentExtensionResponseOption`. Ad esempio:

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Utilizzo con l'azione di input di testo nell'interfaccia utente personalizzata

A seconda della progettazione dell'app e la notifica, talvolta potrebbe essere che richiedono all'utente di immettere testo nella notifica (ad esempio per rispondere a un messaggio). Un'estensione per contenuto notifiche ha accesso all'operazione di input di testo integrato proprio come una notifica standard.

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

Questo codice crea una nuova azione di input di testo e lo aggiunge alla categoria dell'estensione (nelle `MakeExtensionCategory`) (metodo). Nel `DidReceive` override del metodo che gestisce l'utente di immettere testo con il codice seguente:

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

Se la progettazione chiama per l'aggiunta di pulsanti personalizzati per il campo di Input di testo, aggiungere il codice seguente per includerli:

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

Quando l'azione di commento viene attivato dall'utente, sia il controller di visualizzazione e il campo di input di testo personalizzato devono essere attivati:

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

## <a name="summary"></a>Riepilogo

Questo articolo è stato applicato uno sguardo avanzato di uso del nuovo framework di notifica all'utente in un'app xamarin. IOS. Che trattato gli allegati multimediali in locali e remoti notifica l'aggiunta ed coperto utilizzando la nuova interfaccia utente notifica utente per creare le interfacce utente personalizzate di notifica.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Riferimento a Framework di UserNotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guida alla programmazione di notifiche locali e remoti](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
