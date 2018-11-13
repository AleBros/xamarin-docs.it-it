---
title: watchOS risoluzione dei problemi
description: Questo documento vengono descritti problemi noti e soluzioni per lo sviluppo di watchOS con Xamarin. Vengono descritte le immagini con problemi di aggiunta manuale di file di controller di interfaccia, l'avvio di un'app da riga di comando e altro ancora.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 7bfb0cad8bbc025bc5dba39abe3699ae77ee83ba
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526481"
---
# <a name="watchos-troubleshooting"></a>watchOS risoluzione dei problemi

Questa pagina contiene informazioni aggiuntive e soluzioni alternative per funzionalità ancora in fase di sviluppo. Alcune di queste soluzioni alternative valide solo per i rilasci di anteprima.

- [Problemi noti](#knownissues)

- [Rimozione del canale alfa da immagini icona](#noalpha)

- [Aggiunta manuale di file di Controller di interfaccia](#add) di Interface Builder di Xcode.

- [L'avvio di WatchApp dalla riga di comando](#command_line).

<a name="knownissues" />

## <a name="known-issues"></a>Problemi noti

### <a name="general"></a>Generale

<a name="deploy" />

- Nelle versioni precedenti di Visual Studio per Mac mostrano in modo errato uno del **AppleCompanionSettings** icone come da 88 x 88 pixel; che comporta un **mancante icona errore** se si prova a inviare per l'App Store.
    Questa icona deve essere pixel 87 x 87 (29 unità relative **@3x** schermate Retina). Non è possibile risolvere questo problema in Visual Studio per Mac - modificare l'asset di immagine in Xcode o modificare manualmente il **Contents.json** file (in modo che corrispondano [in questo esempio](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

- Se il progetto di estensione Watch **Info. plist > ID Bundle di WKApp** non è [impostate correttamente](~/ios/watchos/get-started/project-references.md) in modo che corrispondano dell'App per le espressioni di controllo **ID Bundle**, il debugger non riuscirà a connettersi e Visual Attesa con il messaggio di Studio per Mac *"In attesa di connessione del debugger"*.

- Il debug è supportato **notifiche** modalità, ma potrebbe non essere affidabile. Nuovo tentativo in alcuni casi funzionerà. Verificare che l'App Watch **Info. plist** `WKCompanionAppBundleIdentifier` è impostato per corrispondere all'identificatore del bundle dell'app per iOS/contenitore padre (ie. è in esecuzione su iPhone).

- iOS Designer non mostra le frecce di punto di ingresso per i controller di interfaccia di riepilogo o una notifica.

- Non è possibile aggiungere due `WKNotificationControllers` di uno storyboard.
    Soluzione alternativa: I `notificationCategory` elemento nello storyboard XML viene sempre inserito con lo stesso `id`. Per risolvere questo problema è possibile aggiungere i controller di notifica di due (o più), aprire il file storyboard in un editor di testo e quindi modificare manualmente il `id` elemento sia univoco.

    [![](troubleshooting-images/duplicate-id-sml.png "Aprire storyboard di file in un editor di testo e modificare manualmente l'elemento id per essere univoco")](troubleshooting-images/duplicate-id.png#lightbox)

- Si potrebbe essere visualizzato un errore "l'applicazione non è stata compilata" durante il tentativo di avviare l'app. Si verifica dopo un **Pulisci** quando il progetto di avvio è impostato per il progetto di estensione di espressioni di controllo.
    La correzione consiste nel selezionare **compilazione > Ricompila tutto** e quindi avviare nuovamente l'app.

### <a name="visual-studio"></a>Visual Studio

Supporto di iOS Designer per Watch Kit *richiede* la soluzione è configurato correttamente. Se non sono impostati i riferimenti al progetto (vedere [come impostare i riferimenti](~/ios/watchos/get-started/project-references.md)) nell'area di progettazione non funzionerà correttamente.

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>Rimozione del canale alfa da immagini icona

Le icone non devono contenere un canale alfa (il canale alfa definisce aree trasparenti di un'immagine), in caso contrario, l'app verrà rifiutata durante l'invio di App Store con un errore simile al seguente:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

È facile rimuovere il canale alfa su Mac OS X tramite il **Preview** app:

1. Aprire l'immagine dell'icona nella **Preview** e quindi scegliere **File > Esporta**.

2. Finestra di dialogo visualizzata includerà un' **Alpha** casella di controllo se è presente un canale alfa.

    ![](troubleshooting-images/remove-alpha-sml.png "Finestra di dialogo visualizzata includerà una casella di controllo alfa se è presente un canale alfa")

3. *Untick* il **Alpha** casella di controllo e **salvare** il file nella posizione corretta.

4. L'immagine dell'icona dovrebbe ora superati i controlli di convalida di Apple.


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>Aggiunta manuale di file di Controller di interfaccia

> [!IMPORTANT]
> Il supporto di WatchKit di Xamarin include la progettazione di espressioni di controllo degli storyboard in iOS designer (in Visual Studio per Mac e Visual Studio), che non richiedono che i passaggi descritti di seguito. Semplicemente offrono un controller di interfaccia un nome di classe in Visual Studio per Mac proprietà di riempimento e il C# verranno creati automaticamente i file di codice.


*Se* si usa Xcode Interface Builder, seguire questi passaggi per creare nuovi controller di interfaccia per l'app watch e abilitare la sincronizzazione con Xcode in modo che gli Outlet e le azioni sono disponibili in C#:

1. Aprire l'app watch **Interface.storyboard** nelle **Interface Builder Xcode**.
    
    ![](troubleshooting-images/add-6.png "Aprire storyboard di Interface Builder Xcode")

2. Trascinare una nuova `InterfaceController` nello storyboard:

    ![](troubleshooting-images/add-1.png "Un InterfaceController")

3. È ora possibile trascinare i controlli nel controller di interfaccia (ad es. le etichette e pulsanti) ma non è possibile creare Outlet o azioni, in quanto è presente alcun **h** file di intestazione. La procedura seguente causerà la necessaria **h** file di intestazione da creare.

    ![](troubleshooting-images/add-2.png "Un pulsante nel layout")

4. Chiudere lo storyboard e tornare a Visual Studio per Mac. Creare un nuovo oggetto C# file **MyInterfaceController.cs** (o qualsiasi nome desiderato) nella **guardare l'estensione dell'app** progetto (non app watch stesso in cui lo storyboard è). Aggiungere il codice seguente (aggiornare lo spazio dei nomi, classname e il nome del costruttore):

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

5. Creare una nuova C# file **MyInterfaceController.designer.cs** nel **guardare l'estensione dell'app** del progetto e aggiungere il codice seguente. Assicurarsi di aggiornare lo spazio dei nomi, il nome della classe e il `Register` attributo:

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
    
    Suggerimento: È possibile (facoltativamente) impostare questo file di un nodo figlio del primo file trascinandolo l'altra C# file in Visual Studio per Mac soluzione riempimento. Quindi risulterà simile al seguente:
    
    ![](troubleshooting-images/add-5.png "Il riquadro della soluzione")

6. Selezionare **compilazione > compila tutto** in modo che la sincronizzazione di Xcode riconosca la nuova classe (tramite il `Register` attributo) che è stato usato.

7. Aprire nuovamente lo storyboard facendo clic sul file storyboard app watch e selezionando **Apri con > Interface Builder Xcode**:

    ![](troubleshooting-images/add-6.png "Aprire storyboard di Interface Builder")

8. Selezionare il nuovo controller di interfaccia e assegnargli il nome della classe che è definito in precedenza, ad es. `MyInterfaceController`.
Se tutto ha funzionato correttamente, verrà visualizzato automaticamente nel **classe:** nell'elenco a discesa ed è possibile selezionarlo da tale posizione.

    ![](troubleshooting-images/add-4.png "L'impostazione di una classe personalizzata")

9. Scegliere il **Assistente dell'Editor** visualizzazione in Xcode (l'icona con due cerchi sovrapposti) che è possibile visualizzare il codice side-by-side e lo storyboard:

    ![](troubleshooting-images/add-7.png "L'elemento della barra degli strumenti Assistente dell'Editor")

    Quando lo stato attivo nel riquadro del codice, verificare che è esaminiamo il **h** file di intestazione e, se non fare doppio clic nella barra di navigazione e selezionare il file corretto (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "Selezionare MyInterfaceController")

10. È ora possibile creare Outlet e azioni da **Ctrl + trascinare** dallo storyboard nel **h** file di intestazione.

    ![](troubleshooting-images/add-9.png "Creazione di Outlet e azioni")

    Quando si rilascia il trascinamento, si verrà richiesto di scegliere se creare un outlet o un'azione e sceglie il relativo nome:

    ![](troubleshooting-images/add-a.png "L'uscita e una finestra di dialogo azione")

11. Dopo aver salvato le modifiche dello storyboard e Xcode sia chiuso, tornare a Visual Studio per Mac. Rileverà le modifiche ai file di intestazione e aggiungere automaticamente il codice per il **. designer.cs** file:


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


È ora possibile fare riferimento al controllo (o implementare l'azione) in C#.


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>All'avvio dell'App Watch dalla riga di comando

> [!IMPORTANT]
> È possibile avviare l'App Watch in modalità normale app per impostazione predefinita e anche in **Glance** oppure **notifica** modalità usano [i parametri di esecuzione personalizzata](~/ios/watchos/get-started/installation.md#custommodes) in Visual Studio per Mac e Visual Studio.


È anche possibile usare la riga di comando per controllare il simulatore iOS. È lo strumento da riga di comando utilizzato per avviare l'App watch **mtouch**.

Ecco un esempio completo (eseguito come una singola riga nel terminale):

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

Il parametro è necessario aggiornare in modo da riflettere l'app è `launchsimwatch`:

### <a name="--launchsimwatch"></a>-launchsimwatch

Il percorso completo per il bundle dell'app principale *per l'app iOS che contiene l'app e l'estensione*.

> [!NOTE]
> Il percorso è necessario specificare sia per il *file con estensione App dell'applicazione iPhone*, ovvero quella che verrà distribuita nel simulatore iOS e che contiene sia l'estensione di espressioni di controllo e l'app watch.

Esempio:

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>Modalità di notifica

Per testare l'app [ **notifica** modalità](~/ios/watchos/platform/notifications.md), impostare il `watchlaunchmode` parametro `Notification` e fornire un percorso di un file JSON che contiene un payload di notifica di test.

Il parametro di payload *obbligatorio* per la modalità di notifica.

Ad esempio, aggiungere questi argomenti per il comando mtouch:

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>Altri argomenti

Gli argomenti rimanenti vengono spiegati di seguito:

### <a name="--sdkroot"></a>--sdkroot

Obbligatorio. Specifica il percorso di Xcode (6.2 o versione successiva).

Esempio:

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>-dispositivo

Il dispositivo di simulatore per l'esecuzione. Questo valore può essere specificato in due modi, utilizzando il valore udid del dispositivo specifico, o una combinazione del tipo di runtime e dispositivo.

I valori esatti varia tra i computer e possono essere sottoposti a query usando Apple **simctl** strumento:

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
