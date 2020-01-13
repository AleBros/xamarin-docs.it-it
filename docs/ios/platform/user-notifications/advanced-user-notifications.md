---
title: Notifiche utente avanzate in Xamarin.iOS
description: Questo articolo esamina in maniera più approfondita il framework delle notifiche utente, introdotto in iOS 10. Vengono illustrate le notifiche utente, l'interfaccia di notifica utente, gli allegati multimediali, le interfacce utente personalizzate e altro ancora.
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: 75ee2c4df9c7f54fa0200272ebc1e9682622ab31
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031438"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Notifiche utente avanzate in Xamarin.iOS

Una novità di iOS 10, il Framework di notifica utente consente la distribuzione e la gestione di notifiche locali e remote. Usando questo Framework, un'estensione app o app può pianificare il recapito delle notifiche locali specificando un set di condizioni, ad esempio la posizione o l'ora del giorno.

## <a name="about-user-notifications"></a>Informazioni sulle notifiche utente

Il nuovo Framework di notifica utente consente la distribuzione e la gestione di notifiche locali e remote. Usando questo Framework, un'estensione app o app può pianificare il recapito delle notifiche locali specificando un set di condizioni, ad esempio la posizione o l'ora del giorno.

Inoltre, l'app o l'estensione può ricevere e potenzialmente modificare le notifiche locali e remote quando vengono recapitate al dispositivo iOS dell'utente.

Il nuovo Framework dell'interfaccia utente di notifica utente consente a un'app o all'estensione dell'app di personalizzare l'aspetto delle notifiche locali e remote quando vengono presentate all'utente.

Questo Framework fornisce i metodi seguenti che un'app può recapitare le notifiche a un utente:

- **Avvisi visivi** , in cui la notifica viene spostata verso il basso dalla parte superiore dello schermo come banner.
- **Audio e vibrazioni** : possono essere associati a una notifica.
- **Icona dell'app promuovere presso** -dove l'icona dell'app visualizza una notifica che mostra che il nuovo contenuto è disponibile. Come il numero di messaggi di posta elettronica non letti.

Inoltre, a seconda del contesto corrente dell'utente, esistono diversi modi in cui verrà presentata una notifica:

- Se il dispositivo è sbloccato, la notifica verrà visualizzata come banner nella parte superiore della schermata.
- Se il dispositivo è bloccato, la notifica verrà visualizzata nella schermata di blocco dell'utente.
- Se l'utente ha perso una notifica, può aprire il centro notifiche e visualizzare eventuali notifiche in attesa disponibili.

Un'app Xamarin.iOS include due tipi di notifiche utente che è in grado di inviare:

- **Notifiche locali** : vengono inviate dalle app installate localmente nel dispositivo degli utenti.
- **Notifiche remote** : vengono inviate da un server remoto e presentate all'utente o attivano un aggiornamento in background del contenuto dell'app.

Per ulteriori informazioni, consultare la documentazione relativa alle [notifiche utente avanzate](~/ios/platform/user-notifications/enhanced-user-notifications.md) .

## <a name="the-new-user-notification-interface"></a>Nuova interfaccia di notifica utente

Le notifiche utente in iOS 10 sono presentate con una nuova progettazione dell'interfaccia utente che fornisce più contenuto, ad esempio titolo, sottotitolo e allegati multimediali facoltativi che possono essere visualizzati nella schermata di blocco, come banner nella parte superiore del dispositivo o nel centro notifiche.

Indipendentemente dalla posizione in cui viene visualizzata una notifica utente in iOS 10, viene presentato lo stesso aspetto e con le stesse funzionalità e funzionalità.

In iOS 8 Apple ha introdotto notifiche di utilità pratica, in cui lo sviluppatore può alleghiare azioni personalizzate a una notifica e consentire all'utente di intervenire su una notifica senza dover avviare l'app. In iOS 9, Apple ha migliorato le notifiche di utilità pratica con risposta rapida che consente all'utente di rispondere a una notifica con una voce di testo.

Poiché le notifiche degli utenti sono parte integrante dell'esperienza utente in iOS 10, Apple ha altre notifiche di utilità pratica espanse per supportare 3D touch, in cui l'utente preme una notifica e viene visualizzata un'interfaccia utente personalizzata per offrire un'interazione avanzata con la notifica.

Quando viene visualizzata l'interfaccia utente di notifica utente personalizzata, se l'utente interagisce con le azioni associate alla notifica, l'interfaccia utente personalizzata può essere immediatamente aggiornata per fornire commenti e suggerimenti relativi a ciò che è stato modificato.

Una novità di iOS 10, l'API dell'interfaccia utente di notifica utente consente a un'app Xamarin.iOS di sfruttare facilmente le nuove funzionalità dell'interfaccia utente di notifica utente.

## <a name="adding-media-attachments"></a>Aggiunta di allegati multimediali

Uno degli elementi più comuni che vengono condivisi tra gli utenti è la foto, quindi iOS 10 ha aggiunto la possibilità di associare un elemento multimediale, ad esempio una foto, direttamente a una notifica, in cui verrà presentato e prontamente disponibile all'utente insieme al resto del Conte della notifica NT.

Tuttavia, a causa delle dimensioni necessarie per l'invio di un'immagine piccola, la connessione a un payload di notifica remoto diventa poco pratica. Per gestire questa situazione, lo sviluppatore può usare la nuova estensione del servizio in iOS 10 per scaricare l'immagine da un'altra origine, ad esempio un archivio dati CloudKit, e collegarla al contenuto della notifica prima che venga visualizzata all'utente.

Affinché una notifica remota venga modificata da un'estensione del servizio, il payload deve essere contrassegnato come modificabile. Esempio:

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

Esaminare i seguenti Cenni preliminari sul processo:

[![](advanced-user-notifications-images/extension02.png "Adding Media Attachments process")](advanced-user-notifications-images/extension02.png#lightbox)

Dopo che la notifica remota è stata recapitata al dispositivo (tramite APNs), l'estensione del servizio può quindi scaricare l'immagine richiesta con qualsiasi mezzo desiderato (ad esempio, un `NSURLSession`) e dopo aver ricevuto l'immagine, può modificare il contenuto della notifica e visualizzarlo in utente.

Di seguito è riportato un esempio di come questo processo può essere gestito nel codice:

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

Quando la notifica viene ricevuta da APNs, l'indirizzo personalizzato dell'immagine viene letto dal contenuto e il file viene scaricato dal server. Viene quindi creata una `UNNotificationAttachement` con un ID univoco e il percorso locale dell'immagine, come `NSUrl`. Viene creata una copia modificabile del contenuto della notifica e vengono aggiunti allegati multimediali. Infine, la notifica viene visualizzata all'utente chiamando il `contentHandler`.

Una volta aggiunto un allegato a una notifica, il sistema acquisisce lo spostamento e la gestione del file.

Oltre alle notifiche remote presentate in precedenza, gli allegati multimediali sono supportati anche dalle notifiche locali, in cui il `UNNotificationAttachement` viene creato e collegato alla notifica insieme al relativo contenuto.

La notifica in iOS 10 supporta allegati multimediali di immagini (statiche e gif), audio o video e il sistema visualizzerà automaticamente l'interfaccia utente personalizzata corretta per ognuno di questi tipi di allegati quando la notifica viene presentata all'utente.

> [!NOTE]
> È necessario prestare attenzione per ottimizzare le dimensioni dei supporti e il tempo necessario per scaricare il file multimediale dal server remoto (o per assemblare il supporto per le notifiche locali), perché il sistema impone limiti rigidi a entrambi durante l'esecuzione dell'estensione del servizio dell'app. Si consideri, ad esempio, l'invio di una versione ridotta dell'immagine o una piccola clip di un video da presentare nella notifica.

## <a name="creating-custom-user-interfaces"></a>Creazione di interfacce utente personalizzate

Per creare un'interfaccia utente personalizzata per le notifiche utente, lo sviluppatore deve aggiungere un'estensione per il contenuto delle notifiche (nuovo in iOS 10) alla soluzione dell'app.

L'estensione per il contenuto delle notifiche consente allo sviluppatore di aggiungere le proprie visualizzazioni all'interfaccia utente di notifica ed estrarre i contenuti desiderati. A partire da iOS 12, le estensioni di contenuto delle notifiche supportano controlli dell'interfaccia utente interattivi, ad esempio pulsanti e dispositivi di scorrimento. Per ulteriori informazioni, vedere la documentazione [interattiva sulle notifiche in iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) .

Per supportare l'interazione dell'utente con una notifica utente, le azioni personalizzate devono essere create, registrate con il sistema e collegate alla notifica prima che venga pianificata con il sistema. L'estensione per il contenuto delle notifiche verrà chiamata per gestire l'elaborazione di tali azioni. Per ulteriori informazioni sulle azioni personalizzate, vedere la sezione [utilizzo delle azioni di notifica](~/ios/platform/user-notifications/enhanced-user-notifications.md) del documento [notifiche utente avanzate](~/ios/platform/user-notifications/enhanced-user-notifications.md) .

Quando viene presentata all'utente una notifica dell'utente con un'interfaccia utente personalizzata, avrà gli elementi seguenti:

[![](advanced-user-notifications-images/customui01.png "A User Notification with a Custom UI elements")](advanced-user-notifications-images/customui01.png#lightbox)

Se l'utente interagisce con le azioni personalizzate (presentate sotto la notifica), l'interfaccia utente può essere aggiornata per fornire commenti e suggerimenti degli utenti in merito al momento in cui è stata richiamata una determinata azione.

### <a name="adding-a-notification-content-extension"></a>Aggiunta di un'estensione per il contenuto delle notifiche

Per implementare un'interfaccia utente personalizzata per le notifiche utente in un'app Xamarin.iOS, seguire questa procedura:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic con il pulsante destro del mouse sul nome della soluzione nel **riquadro della soluzione** e scegliere **Aggiungi** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **Extensions** > le **estensioni di contenuto della notifica** e fare clic sul pulsante **Avanti** : 

    [![](advanced-user-notifications-images/notify01.png "Select Notification Content Extensions")](advanced-user-notifications-images/notify01.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic sul pulsante **Avanti** : 

    [![](advanced-user-notifications-images/notify02.png "Enter a Name for the extension")](advanced-user-notifications-images/notify02.png#lightbox)
5. Modificare il **nome del progetto** e/o il **nome della soluzione** , se necessario, e fare clic sul pulsante **Crea** : 

    [![](advanced-user-notifications-images/notify03.png "Adjust the Project Name and/or Solution Name")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic con il pulsante destro del mouse sul nome della soluzione nel **Esplora soluzioni** e scegliere **Aggiungi > nuovo progetto.**
3. Selezionare **Visual C# > iOS Extensions > estensione**per il contenuto della notifica:

    [![](advanced-user-notifications-images/notify01.w157-sml.png "Select Notification Content Extensions")](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic sul pulsante **OK** .

-----

Quando l'estensione del contenuto della notifica viene aggiunta alla soluzione, verranno creati tre file nel progetto dell'estensione:

1. `NotificationViewController.cs`: il controller di visualizzazione principale per l'estensione del contenuto della notifica.
2. `MainInterface.storyboard`-dove lo sviluppatore dispone l'interfaccia utente visibile per l'estensione del contenuto delle notifiche in iOS designer.
3. `Info.plist`: controlla la configurazione dell'estensione per il contenuto della notifica.

Il file di `NotificationViewController.cs` predefinito è simile al seguente:

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

Il metodo `DidReceiveNotification` viene chiamato quando la notifica viene espansa dall'utente in modo che l'estensione di contenuto della notifica possa popolare l'interfaccia utente personalizzata con il contenuto della `UNNotification`. Per l'esempio precedente, un'etichetta è stata aggiunta alla visualizzazione, esposta al codice con il nome `label` e viene utilizzata per visualizzare il corpo della notifica.

### <a name="setting-the-notification-content-extensions-categories"></a>Impostazione delle categorie dell'estensione del contenuto delle notifiche

Il sistema deve essere informato su come trovare l'estensione per il contenuto delle notifiche dell'app in base alle categorie specifiche a cui risponde. Procedere come descritto di seguito:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare doppio clic sul file di `Info.plist` dell'estensione nel **riquadro della soluzione** per aprirlo per la modifica.
2. Passare alla visualizzazione di **origine** .
3. Espandere la chiave `NSExtension`.
4. Aggiungere la chiave di `UNNotificationExtensionCategory` come **stringa** di tipo con il valore della categoria a cui appartiene l'estensione (in questo esempio ' evento-invito): 

    [![](advanced-user-notifications-images/customui02.png "Add the UNNotificationExtensionCategory key")](advanced-user-notifications-images/customui02.png#lightbox)
5. Salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare doppio clic sul file di `Info.plist` dell'estensione nel **Esplora soluzioni** per aprirlo per la modifica.
2. Espandere la chiave `NSExtension`.
3. Aggiungere la chiave di `UNNotificationExtensionCategory` come **stringa** di tipo con il valore della categoria a cui appartiene l'estensione (in questo esempio ' evento-invito): 

    [![](advanced-user-notifications-images/customui02w.png "Add the UNNotificationExtensionCategory key")](advanced-user-notifications-images/customui02w.png#lightbox)
4. Salvare le modifiche.

-----

Le categorie di estensione del contenuto delle notifiche (`UNNotificationExtensionCategory`) utilizzano gli stessi valori di categoria utilizzati per registrare le azioni di notifica. Nel caso in cui l'App utilizzerà la stessa interfaccia utente per più categorie, passare il `UNNotificationExtensionCategory` alla **matrice** di tipi e fornire tutte le categorie necessarie. Esempio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](advanced-user-notifications-images/customui03.png "Notification Content Extension Categories")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui03w.png "Notification Content Extension Categories")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>Nascondere il contenuto di notifica predefinito

Nel caso in cui l'interfaccia utente di notifica personalizzata visualizzi lo stesso contenuto della notifica predefinita (titolo, sottotitolo e corpo visualizzato automaticamente nella parte inferiore dell'interfaccia utente di notifica), queste informazioni predefinite possono essere nascoste aggiungendo il `UNNotificationExtensionDefaultContentHidden` chiave per la chiave di `NSExtensionAttributes` come tipo **booleano** con un valore `YES` nel file di `Info.plist` dell'estensione:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](advanced-user-notifications-images/customui04.png "Finding default information")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui04w.png "Finding default information")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>Progettazione dell'interfaccia utente personalizzata

Per progettare l'interfaccia utente personalizzata dell'estensione del contenuto di notifica, fare doppio clic sul file `MainInterface.storyboard` per aprirlo per la modifica in iOS designer, trascinare gli elementi necessari per compilare l'interfaccia desiderata, ad esempio `UILabels` e `UIImageViews`.

> [!NOTE]
> A partire da iOS 12, un'estensione per il contenuto delle notifiche può includere controlli interattivi, ad esempio pulsanti e campi di testo. Per ulteriori informazioni, vedere la documentazione [interattiva sulle notifiche in iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) .

Una volta che l'interfaccia utente è stata disposta e i C# controlli necessari esposti al codice, aprire il`NotificationViewController.cs`per la modifica e modificare il metodo`DidReceiveNotification`per popolare l'interfaccia utente quando l'utente espande la notifica. Esempio:

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

### <a name="setting-the-content-area-size"></a>Impostazione delle dimensioni dell'area di contenuto

Per modificare le dimensioni dell'area di contenuto visualizzata per l'utente, il codice seguente imposta la proprietà `PreferredContentSize` nel metodo `ViewDidLoad` sulle dimensioni desiderate. Questa dimensione può essere regolata anche applicando vincoli alla visualizzazione in iOS designer, che consente allo sviluppatore di scegliere il metodo più adatto.

Poiché il sistema di notifica è già in esecuzione prima che venga richiamata l'estensione del contenuto della notifica, l'area di contenuto verrà avviata con dimensioni massime e verrà animata fino alla dimensione richiesta quando viene presentata all'utente.

Per eliminare questo effetto, modificare il file di `Info.plist` per l'estensione e impostare la chiave di `UNNotificationExtensionInitialContentSizeRatio` della chiave di `NSExtensionAttributes` su tipo **numero** con un valore che rappresenta il rapporto desiderato. Esempio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](advanced-user-notifications-images/customui05.png "The UNNotificationExtensionInitialContentSizeRatio key")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui05w.png "The UNNotificationExtensionInitialContentSizeRatio key")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>Uso di allegati multimediali nell'interfaccia utente personalizzata

Poiché gli allegati multimediali (come illustrato nella sezione [aggiunta di allegati multimediali](#adding-media-attachments) precedente) fanno parte del payload di notifica, è possibile accedervi e visualizzarli nell'estensione del contenuto della notifica esattamente come si farebbe nell'interfaccia utente di notifica predefinita.

Se, ad esempio, l'interfaccia utente personalizzata precedente include un `UIImageView` esposto al C# codice, è possibile usare il codice seguente per popolarlo da con l'allegato multimediale:

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

Poiché l'allegato multimediale è gestito dal sistema, è esterno al sandbox dell'app. L'estensione deve informare il sistema che vuole accedere al file chiamando il metodo `StartAccessingSecurityScopedResource`. Quando l'estensione viene eseguita con il file, deve chiamare il `StopAccessingSecurityScopedResource` per rilasciare la connessione.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Aggiunta di azioni personalizzate a un'interfaccia utente personalizzata

È possibile utilizzare i pulsanti azione personalizzata per aggiungere interattività a un'interfaccia utente di notifica personalizzata. Per ulteriori informazioni sulle azioni personalizzate, vedere la sezione [utilizzo delle azioni di notifica](~/ios/platform/user-notifications/enhanced-user-notifications.md) del documento [notifiche utente avanzate](~/ios/platform/user-notifications/enhanced-user-notifications.md) .

Oltre alle azioni personalizzate, l'estensione per il contenuto delle notifiche può rispondere alle azioni predefinite seguenti:

- **Azione predefinita** : quando l'utente tocca una notifica per aprire l'app e visualizzare i dettagli della notifica specificata.
- **Ignora azione** : questa azione viene inviata all'app quando l'utente ignora una determinata notifica.

Le estensioni di contenuto delle notifiche hanno anche la possibilità di aggiornare l'interfaccia utente quando l'utente richiama una delle azioni personalizzate, ad esempio la visualizzazione di una data accettata quando l'utente tocca il pulsante **accetta** azione personalizzata. Inoltre, le estensioni per il contenuto delle notifiche possono indicare al sistema di ritardare la chiusura dell'interfaccia utente di notifica, in modo che l'utente possa visualizzare l'effetto dell'azione prima che la notifica venga chiusa.

Questa operazione viene eseguita implementando una seconda versione del metodo `DidReceiveNotification` che include un gestore di completamento. Esempio:

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

Aggiungendo il gestore `Server.PostEventResponse` al metodo `DidReceiveNotification` dell'estensione del contenuto della notifica, l'estensione *deve* gestire tutte le azioni personalizzate. L'estensione può anche inviare le azioni personalizzate all'app contenitore modificando il `UNNotificationContentExtensionResponseOption`. Esempio:

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Uso dell'azione di input di testo nell'interfaccia utente personalizzata

A seconda della progettazione dell'app e della notifica, potrebbe essere necessario che l'utente immetta il testo nella notifica, ad esempio la risposta a un messaggio. Un'estensione per il contenuto delle notifiche ha accesso all'azione di input di testo incorporata come avviene per una notifica standard.

Esempio:

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

Questo codice crea una nuova azione di input di testo e la aggiunge alla categoria dell'estensione, nel metodo `MakeExtensionCategory`. Nel metodo di override `DidReceive`, gestisce l'immissione di testo da parte dell'utente con il codice seguente:

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

Se la progettazione chiama per aggiungere pulsanti personalizzati al campo di input di testo, aggiungere il codice seguente per includerli:

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

Quando l'azione di commento viene attivata dall'utente, è necessario attivare sia il controller di visualizzazione che il campo di input di testo personalizzato:

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

Questo articolo ha esaminato in modo avanzato l'uso del nuovo Framework di notifica utente in un'app Xamarin.iOS. È stato spiegato come aggiungere allegati multimediali sia alla notifica locale che a quella remota ed è stato analizzato utilizzando la nuova interfaccia utente di notifica utente per creare interfacce utente di notifica personalizzate.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Guida di riferimento a UserNotifications Framework](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guida alla programmazione di notifiche locali e remote](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
