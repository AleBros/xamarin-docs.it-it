---
title: watchOS risoluzione dei problemi
description: Questo documento vengono illustrati i problemi noti e soluzioni alternative per watchOS lo sviluppo con Xamarin. Vengono descritte le immagini con problemi, aggiunta manuale di file di controller di interfaccia, avviare un'app di espressioni di controllo dalla riga di comando e molto altro.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 4e84028336669738c40da9e37cd22f32ba11dfc1
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791765"
---
# <a name="watchos-troubleshooting"></a>watchOS risoluzione dei problemi

Questa pagina contiene informazioni aggiuntive e soluzioni alternative per le funzionalità in fase di sviluppo. Alcune di queste soluzioni alternative applicabili solo per i rilasci di anteprima.

- [Problemi noti](#knownissues)

- [Rimuovere il canale alfa immagini icona](#noalpha)

- [Aggiunta manuale di file di Controller di interfaccia](#add) per il generatore di interfaccia di Xcode.

- [Avvio di WatchApp dalla riga di comando](#command_line).

<a name="knownissues" />

## <a name="known-issues"></a>Problemi noti

### <a name="general"></a>Generale

<a name="deploy" />

- In modo non corretto mostrano una delle versioni precedenti di Visual Studio per Mac l'il **AppleCompanionSettings** icone come pixel 88 x 88; determinando un **mancante icona errore** se si tenta di inviare all'App Archivio.
    Questa icona deve essere 87 x 87 pixel (29 unità per **@3x** schermate Retina). Non è possibile risolvere questo problema in Visual Studio per Mac - modificare la risorsa di immagine in Xcode o modificare manualmente il **Contents.json** file (in modo che corrisponda [in questo esempio](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

- Se il progetto di estensione di espressioni di controllo **Info. plist > ID Bundle WKApp** non [impostato correttamente](~/ios/watchos/get-started/project-references.md) per corrispondente all'applicazione di espressioni di controllo **ID Bundle**, il debugger avrà esito negativo per la connessione e Visual Con il messaggio rimarrà in attesa Studio per Mac *"In attesa del debugger"*.

- Il debug è supportato in **notifiche** modalità, ma potrebbero non essere affidabili. Nuovo tentativo talvolta funzionerà. Verificare che l'applicazione di espressioni di controllo **Info. plist** `WKCompanionAppBundleIdentifier` è impostata in corrispondenza l'identificatore bundle dell'app/contenitore padre (ie. è in esecuzione su iPhone).

- finestra di progettazione iOS non sono presenti frecce di punto di ingresso per i controller di interfaccia di riepilogo o una notifica.

- Non è possibile aggiungere due `WKNotificationControllers` di uno storyboard.
    Soluzione alternativa: Il `notificationCategory` viene sempre inserito elemento dello storyboard XML con lo stesso `id`. Per risolvere questo problema, è possibile aggiungere due (o più) controller di notifica, aprire il file storyboard in un editor di testo e modificare manualmente il `id` elemento sia univoco.

    [![](troubleshooting-images/duplicate-id-sml.png "Aprire lo storyboard di file in un editor di testo e modificare manualmente l'elemento id per essere univoco")](troubleshooting-images/duplicate-id.png#lightbox)

- Potrebbe essere visualizzato un errore "l'applicazione non è stato compilato" durante il tentativo di avviare l'app. Questo errore si verifica dopo un **Pulisci** quando il progetto di avvio è impostato per il progetto di estensione delle espressioni di controllo.
    Per risolvere il problema consiste nel selezionare **compilazione > Ricompila tutto** e quindi avviare nuovamente l'app.

### <a name="visual-studio"></a>Visual Studio

La finestra di progettazione iOS supportano per espressioni di controllo Kit *richiede* la soluzione sia configurato correttamente. Se non sono riferimenti del progetto (vedere [come impostare i riferimenti](~/ios/watchos/get-started/project-references.md)) nell'area di progettazione non funzionerà correttamente.

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>Rimuovere il canale alfa immagini icona

Le icone non devono contenere un canale alfa (il canale alfa definisce aree trasparenti di un'immagine), in caso contrario verrà rifiutato l'app durante l'invio allo Store di App con un errore simile al seguente:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

È facile rimuovere il canale alfa su Mac OS X tramite il **anteprima** app:

1. Aprire l'immagine dell'icona in **anteprima** e quindi scegliere **File > Esporta**.

2. Finestra di dialogo visualizzata includerà un **alfa** casella di controllo se è presente un canale alfa.

    ![](troubleshooting-images/remove-alpha-sml.png "Finestra di dialogo visualizzata includerà una casella di controllo alfa, se è presente un canale alfa")

3. *Untick* il **alfa** casella di controllo e **salvare** il file nel percorso corretto.

4. L'immagine dell'icona ora superati i controlli di convalida di Apple.


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>Aggiunta manuale di file di Controller di interfaccia

> [!IMPORTANT]
> Il supporto di Xamarin WatchKit include progettazione storyboard espressioni di controllo in Progettazione iOS (in Visual Studio per Mac e Visual Studio), che non richiede i passaggi descritti di seguito. Semplicemente offrono un controller di interfaccia di un nome di classe in Visual Studio per Mac proprietà riempimento e verranno creati automaticamente i file di codice c#.


*Se* si utilizza il generatore di interfaccia Xcode, seguire questi passaggi per creare nuovi controller di interfaccia per l'applicazione di espressioni di controllo e abilitare la sincronizzazione con Xcode in modo che le prese e le azioni disponibili in c#:

1. Aprire l'app di espressioni di controllo **Interface.storyboard** in **Xcode interfaccia generatore**.
    
    ![](troubleshooting-images/add-6.png "Aprire lo storyboard in Xcode interfaccia generatore")

2. Trascinare una nuova `InterfaceController` nello storyboard:

    ![](troubleshooting-images/add-1.png "Un InterfaceController")

3. È possibile trascinare i controlli nel controller di interfaccia (ad es. le etichette e i pulsanti) ma non è possibile creare azioni o prese ancora, poiché non esiste alcun **h** file di intestazione. La procedura seguente causerà la **h** file di intestazione da creare.

    ![](troubleshooting-images/add-2.png "Un pulsante del layout")

4. Chiudere lo storyboard e tornare a Visual Studio per Mac. Creare un nuovo file c# **MyInterfaceController.cs** (o qualsiasi nome desiderato) nei **guardare l'estensione dell'app** progetto (non l'applicazione di espressioni di controllo se stesso in cui lo storyboard è). Aggiungere il codice seguente (aggiornare lo spazio dei nomi, classname e il nome del costruttore):

        using System;
        using WatchKit;
        using Foundation;
        
        namespace WatchAppExtension  // remember to update this
        {
            public partial class MyInterfaceController // remember to update this
            : WKInterfaceController
            {
                public MyInterfaceController // remember to update this
                (IntPtr handle) : base (handle)
                {
                }
                public override void Awake (NSObject context)
                {
                    base.Awake (context);
                    // Configure interface objects here.
                    Console.WriteLine ("{0} awake with context", this);
                }
                public override void WillActivate ()
                {
                    // This method is called when the watch view controller is about to be visible to the user.
                    Console.WriteLine ("{0} will activate", this);
                }
                public override void DidDeactivate ()
                {
                    // This method is called when the watch view controller is no longer visible to the user.
                    Console.WriteLine ("{0} did deactivate", this);
                }
            }
        }

5. Creare un altro nuovo file c# **MyInterfaceController.designer.cs** nel **guardare l'estensione dell'app** del progetto e aggiungere il codice seguente. Assicurarsi di aggiornare lo spazio dei nomi, classe e `Register` attributo:

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;
    
    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```
    
    Suggerimento: È possibile (facoltativamente) impostare questo file di un nodo figlio del primo file mediante trascinamento in altri file c# in Visual Studio per Mac soluzione riempimento. Verrà quindi visualizzato simile al seguente:
    
    ![](troubleshooting-images/add-5.png "Il riquadro soluzione")

6. Selezionare **compilazione > Build All** in modo che la sincronizzazione di Xcode riconoscerà la nuova classe (tramite il `Register` attributo) che è stato usato.

7. Aprire nuovamente lo storyboard facendo clic sul file di storyboard dell'applicazione di espressioni di controllo e selezionando **Apri con > Xcode interfaccia generatore**:

    ![](troubleshooting-images/add-6.png "Aprire lo storyboard in Generatore di interfaccia")

8. Selezionare il nuovo controller di interfaccia e assegnargli il NomeClasse che è definito in precedenza, ad esempio. `MyInterfaceController`.
Se tutti gli elementi ha funzionato correttamente, viene visualizzato automaticamente nel **classe:** nell'elenco a discesa e selezionarlo da qui.

    ![](troubleshooting-images/add-4.png "L'impostazione di una classe personalizzata")

9. Scegliere il **Assistente Editor** visualizzazione in Xcode (l'icona con due cerchi sovrapposti) che è possibile visualizzare lo storyboard e il codice side-by-side:

    ![](troubleshooting-images/add-7.png "L'elemento della barra degli strumenti Editor Assistente")

    Quando lo stato attivo nel riquadro del codice, verificare che si sta esamina il **h** , file di intestazione e, se non fare clic nella barra di navigazione e selezionare il file corretto (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "Selezionare MyInterfaceController")

10. È ora possibile creare i punti vendita e le azioni da **Ctrl + trascinare** dallo storyboard nel **h** file di intestazione.

    ![](troubleshooting-images/add-9.png "Creazione di punti vendita e le azioni")

    Quando si rilascia il trascinamento, verrà richiesto di scegliere se creare un punto o un'azione e scegliere il nome:

    ![](troubleshooting-images/add-a.png "L'uscita e una finestra di dialogo di azione")

11. Dopo aver salvato le modifiche di storyboard e Xcode viene chiusa, tornare a Visual Studio per Mac. Rileverà le modifiche al file di intestazione e automaticamente aggiungere codice per il **. designer.cs** file:


        [Register ("MyInterfaceController")]
        partial class MyInterfaceController
        {
            [Outlet]
            WatchKit.WKInterfaceButton myButton { get; set; }
        
            void ReleaseDesignerOutlets ()
            {
                if (myButton != null) {
                    myButton.Dispose ();
                    myButton = null;
                }
            }
        }


È ora possibile fare riferimento al controllo (o implementare l'azione) in c#.


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>Avvio dell'App di espressioni di controllo dalla riga di comando

> [!IMPORTANT]
> È possibile avviare l'applicazione di espressioni di controllo in modalità normale app per impostazione predefinita, nonché in **riepilogo** o **notifica** le modalità tramite [parametri di esecuzione personalizzato](~/ios/watchos/get-started/installation.md#custommodes) in Visual Studio per Mac e Visual Studio.


È inoltre possibile utilizzare la riga di comando per controllare il simulatore iOS. Lo strumento da riga di comando utilizzato per avviare le app di espressioni di controllo è **mtouch**.

Di seguito è riportato un esempio completo (eseguito come una singola riga in terminal):

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

Il parametro è necessario aggiornare in modo da riflettere l'app è `launchsimwatch`:

### <a name="--launchsimwatch"></a>-launchsimwatch

Il percorso completo per il bundle dell'app principale *dell'app per iOS che contiene l'applicazione di espressioni di controllo e l'estensione*.

> [!NOTE]
> È necessario fornire sia per il *file app applicazione iPhone*, ovvero quello che verrà distribuito al simulatore iOS, che contiene le estensioni di espressioni di controllo e app di espressioni di controllo.

Esempio:

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>Modalità di notifica

Per testare l'app [ **notifica** modalità](~/ios/watchos/platform/notifications.md), impostare il `watchlaunchmode` parametro `Notification` e fornire un percorso di un file JSON che contiene un payload di notifica di test.

Il parametro di payload è *obbligatorio* per la modalità di notifica.

Ad esempio, è possibile aggiungere gli argomenti per il comando mtouch:

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>Altri argomenti

Gli argomenti rimanenti vengono spiegati di seguito:

### <a name="--sdkroot"></a>-sdkroot

Obbligatorio. Specifica il percorso a Xcode (6.2 o versione successiva).

Esempio:

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>-dispositivo

Il dispositivo simulatore da eseguire. Questo valore può essere specificato in due modi, tramite udid di un dispositivo specifico, o una combinazione di tipo di runtime e dispositivo.

I valori esatti varia tra computer e possono essere eseguite utilizzando Apple **simctl** strumento:

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

Esempio:

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**Tipo di runtime e dispositivo**

Esempio:

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
