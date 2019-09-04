---
title: Risoluzione dei problemi di watchos
description: Questo documento illustra i problemi noti e le soluzioni alternative per lo sviluppo di watchos con Novell. Descrive le immagini con problemi, aggiungendo manualmente i file del controller di interfaccia, avviando un'app Watch dalla riga di comando e altro ancora.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 1777bfe627d0dc579169c954f7799348befbf6b8
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227020"
---
# <a name="watchos-troubleshooting"></a>Risoluzione dei problemi di watchos

Questa pagina contiene informazioni aggiuntive e soluzioni alternative per i problemi che possono verificarsi.

- [Problemi noti](#knownissues)

- [Rimozione del canale alfa dalle immagini icona](#noalpha)

- [Aggiunta manuale di file del controller di interfaccia](#add) per Xcode Interface Builder.

- [Avvio di WatchApp dalla riga di comando](#command_line).

<a name="knownissues" />

## <a name="known-issues"></a>Problemi noti

### <a name="general"></a>Generale

<a name="deploy" />

- Le versioni precedenti di Visual Studio per Mac visualizzano erroneamente una delle icone **AppleCompanionSettings** come 88x88 pixel; Se si tenta di inviare all'App Store, viene restituito un **errore di icona mancante** .
    Questa icona deve essere 87x87 pixel (29 unità per **@3x** le schermate retina). Non è possibile risolvere questo problema in Visual Studio per Mac: modificare l'asset di immagine in Xcode o modificare manualmente il file **Contents. JSON** (in modo che corrisponda a [questo esempio](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

- Se il file **info. plist > ID bundle WKApp** del progetto di estensione Watch non è [impostato correttamente](~/ios/watchos/get-started/project-references.md) in modo da corrispondere all' **ID bundle**dell'app Watch, il debugger non riuscirà a connettersi e Visual Studio per Mac aspetterà il messaggio *"in attesa del debugger Connetti "* .

- Il debug è supportato nella modalità di **notifica** , ma può essere inaffidabile. Il nuovo tentativo può a volte funzionare. Verificare che il file **info. plist** `WKCompanionAppBundleIdentifier` dell'app Watch sia impostato in modo che corrisponda all'identificatore del bundle dell'app padre/contenitore iOS, ovvero quello che viene eseguito nell'iPhone.

- iOS designer non Mostra le frecce EntryPoint per l'occhiata o i controller di interfaccia di notifica.

- Non è possibile aggiungere `WKNotificationControllers` due a uno storyboard.
    Soluzione alternativa: L' `notificationCategory` elemento nel codice XML dello storyboard viene sempre inserito con lo `id`stesso oggetto. Per ovviare a questo problema, è possibile aggiungere due o più controller di notifica, aprire il file storyboard in un editor di testo e quindi modificare `id` manualmente l'elemento in modo che sia univoco.

    [![](troubleshooting-images/duplicate-id-sml.png "Apertura del file storyboard in un editor di testo e modifica manuale dell'elemento ID in modo che sia univoco")](troubleshooting-images/duplicate-id.png#lightbox)

- È possibile che venga visualizzato l'errore "l'applicazione non è stata compilata" durante il tentativo di avviare l'app. Questo problema si verifica dopo un oggetto **pulito** quando il progetto di avvio è impostato sul progetto di estensione Watch.
    La correzione consiste nel selezionare **compila > Ricompila tutto** , quindi riavviare l'app.

### <a name="visual-studio"></a>Visual Studio

Il supporto di iOS designer per Watch Kit *richiede* la configurazione corretta della soluzione. Se i riferimenti al progetto non sono impostati (vedere [How to set References](~/ios/watchos/get-started/project-references.md)), l'area di progettazione non funzionerà correttamente.

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>Rimozione del canale alfa dalle immagini icona

Le icone non devono contenere un canale alfa (il canale alfa definisce aree trasparenti di un'immagine); in caso contrario, l'app verrà rifiutata durante l'invio dell'app Store con un errore simile al seguente:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

È facile rimuovere il canale alfa in Mac OS X usando l'app di **Anteprima** :

1. Aprire l'immagine dell'icona in **Anteprima** , quindi scegliere **file > Esporta**.

2. La finestra di dialogo visualizzata includerà una casella di controllo **Alpha** se è presente un canale alfa.

    ![](troubleshooting-images/remove-alpha-sml.png "La finestra di dialogo visualizzata includerà una casella di controllo Alpha se è presente un canale alfa")

3. *Deseleziona* la casella di controllo **Alpha** e **Salva** il file nel percorso corretto.

4. L'immagine dell'icona dovrebbe ora passare i controlli di convalida di Apple.


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>Aggiunta manuale di file del controller di interfaccia

> [!IMPORTANT]
> Il supporto WatchKit di Novell include la progettazione degli storyboard Watch in iOS designer (sia in Visual Studio per Mac che in Visual Studio), che non richiede i passaggi descritti di seguito. È sufficiente assegnare al controller di interfaccia un nome di classe nel riquadro delle proprietà C# Visual Studio per Mac e i file di codice verranno creati automaticamente.


*Se* si usa Xcode Interface Builder, seguire questa procedura per creare nuovi controller di interfaccia per l'app Watch e abilitare la sincronizzazione con Xcode in modo che gli Outlet e le azioni C#siano disponibili in:

1. Aprire l'app Watch ' s **Interface. Storyboard** in **Xcode Interface Builder**.

    ![](troubleshooting-images/add-6.png "Apertura dello storyboard in Xcode Interface Builder")

2. Trascinare un nuovo `InterfaceController` nello storyboard:

    ![](troubleshooting-images/add-1.png "InterfaceController")

3. È ora possibile trascinare i controlli sul controller di interfaccia (ad esempio, etichette e pulsanti, ma non è ancora possibile creare Outlet o azioni, perché non è presente alcun file di intestazione **. h** . I passaggi seguenti determineranno la creazione del file di intestazione **. h** obbligatorio.

    ![](troubleshooting-images/add-2.png "Un pulsante nel layout")

4. Chiudere lo storyboard e tornare a Visual Studio per Mac. Creare un nuovo C# file **MyInterfaceController.cs** (o il nome desiderato) nel progetto di **estensione Watch app** (non l'app Watch in cui si trova lo storyboard). Aggiungere il codice seguente (aggiornamento dello spazio dei nomi, NomeClasse e nome del costruttore):

    ```csharp
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
    ```

5. Creare un altro C# nuovo file **MyInterfaceController.designer.cs** nel progetto di **estensione Watch app** e aggiungere il codice seguente. Assicurarsi di aggiornare lo spazio dei nomi, la classname `Register` e l'attributo:

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

    > [!TIP]
    > È possibile (facoltativamente) rendere questo file un nodo figlio del primo file trascinandolo sull'altro C# file nel riquadro della soluzione di Visual Studio per Mac. Verrà visualizzato come segue:

    ![](troubleshooting-images/add-5.png "Riquadro della soluzione")

6. Selezionare **Compila > Compila tutto** in modo che la sincronizzazione Xcode riconosca la nuova classe `Register` (tramite l'attributo) usata.

7. Riaprire lo storyboard facendo clic con il pulsante destro del mouse sul file dello storyboard dell'app Watch e selezionando **Apri con > Xcode Interface Builder**:

    ![](troubleshooting-images/add-6.png "Apertura dello storyboard in Interface Builder")

8. Selezionare il nuovo controller di interfaccia e assegnargli il NomeClasse definito in precedenza, ad esempio. `MyInterfaceController`.
    Se tutto funziona correttamente, dovrebbe essere visualizzato automaticamente nell'elenco a discesa **classe:** ed è possibile selezionarlo da questa posizione.

    ![](troubleshooting-images/add-4.png "Impostazione di una classe personalizzata")

9. Scegliere la visualizzazione dell' **editor di assistente** in Xcode (l'icona con due cerchi sovrapposti), in modo che sia possibile visualizzare lo storyboard e il codice side-by-Side:

    ![](troubleshooting-images/add-7.png "Elemento della barra degli strumenti dell'editor di assistente")

    Quando lo stato attivo si trova nel riquadro del codice, assicurarsi di esaminare il file di intestazione con **estensione h** e, in caso contrario, fare clic con il pulsante destro del mouse sulla barra di navigazione e selezionare il file corretto (**MyInterfaceController. h**)

    ![](troubleshooting-images/add-8.png "Seleziona MyInterfaceController")

10. È ora possibile creare Outlet e azioni premendo **Ctrl + trascina** dallo storyboard nel file di intestazione con **estensione h** .

    ![](troubleshooting-images/add-9.png "Creazione di Outlet e azioni")

    Quando si rilascia il trascinamento, verrà richiesto di scegliere se creare un outlet o un'azione e scegliere il nome:

    ![](troubleshooting-images/add-a.png "L'Outlet e una finestra di dialogo di azione")

11. Una volta salvate le modifiche dello storyboard e Xcode è chiuso, tornare a Visual Studio per Mac. Verranno rilevate le modifiche apportate al file di intestazione e verrà automaticamente aggiunto il codice al file con **estensione designer.cs** :

    ```csharp
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
    ```

È ora possibile fare riferimento al controllo (o implementare l'azione) C#in.


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>Avvio dell'app Watch dalla riga di comando

> [!IMPORTANT]
> Per impostazione predefinita, è possibile avviare l'app Watch in modalità app normale e anche in modalità Glance o di **notifica** usando [parametri di esecuzione personalizzati](~/ios/watchos/get-started/installation.md#custommodes) in Visual Studio per Mac e Visual Studio.


È anche possibile usare la riga di comando per controllare il simulatore iOS. Lo strumento da riga di comando usato per avviare le app Watch è **mTouch**.

Di seguito è riportato un esempio completo (eseguito come una singola riga nel terminale):

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

Il parametro che è necessario aggiornare per riflettere l'app è `launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

Il percorso completo del bundle dell'app principale *per l'app iOS che contiene l'app e l'estensione Watch*.

> [!NOTE]
> Il percorso da fornire è per il *file app. app iPhone*, ovvero quello che verrà distribuito al simulatore iOS e che contiene sia l'estensione del controllo che l'app Watch.

Esempio:

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>Modalità di notifica

Per testare la modalità di [ **notifica** ](~/ios/watchos/platform/notifications.md)dell'app, impostare `watchlaunchmode` il parametro `Notification` su e fornire un percorso a un file JSON contenente un payload di notifica di prova.

Il parametro payload è *obbligatorio* per la modalità di notifica.

Aggiungere ad esempio gli argomenti seguenti al comando mTouch:

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>Altri argomenti

Gli argomenti rimanenti sono illustrati di seguito:

### <a name="--sdkroot"></a>--sdkroot

Richiesto. Specifica il percorso di Xcode (6,2 o versione successiva).

Esempio:

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--dispositivo

Dispositivo del simulatore da eseguire. Questo può essere specificato in due modi, usando il UDID di un dispositivo specifico o usando una combinazione di runtime e tipo di dispositivo.

I valori esatti variano tra i computer e possono essere sottoposti a query usando lo strumento **simctl** di Apple:

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

Esempio:

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**Runtime e tipo di dispositivo**

Esempio:

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)
