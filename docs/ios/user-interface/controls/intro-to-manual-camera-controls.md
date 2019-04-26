---
title: Controlli della fotocamera manuali in xamarin. IOS
description: Questo documento descrive come possa usare il framework AVFoundation iOS con xamarin. IOS per abilitare i controlli della fotocamera manuali. Controlli della fotocamera manuali consentono all'utente di impostazioni l'esposizione, bilanciare il bianco e focus del controllo.
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c61b3fee9009afb86ccd3fd0e16d7812a8e90feb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384308"
---
# <a name="manual-camera-controls-in-xamarinios"></a>Controlli della fotocamera manuali in xamarin. IOS

I controlli della fotocamera manuali, fornito dal `AVFoundation Framework` in iOS 8, consentire a un'applicazione per dispositivi mobili assumere il controllo completo della fotocamera del dispositivo iOS. Questo livello di controllo con granularità fine è utilizzabile per creare applicazioni professionali e a livello della fotocamera e fornire le composizioni artista modificando i parametri della fotocamera durante la creazione di un'immagine o un video.

Questi controlli possono essere utili anche quando si sviluppano applicazioni scientifiche o industriale, in cui i risultati sono meno pensati per la correttezza o bellezza dell'immagine e più pensati per evidenziare alcune funzionalità o un elemento dell'immagine verrà eseguita.

## <a name="avfoundation-capture-objects"></a>Oggetti AVFoundation Capture

Se la disattivazione video o ancora di immagini di uso della fotocamera in un dispositivo iOS, il processo usato per l'acquisizione delle immagini è in gran parte lo stesso. Questo vale per le applicazioni che usano i controlli della fotocamera automatizzato predefinito o quelle che sfruttano i vantaggi dei nuovi controlli della fotocamera manuali:

 [![](intro-to-manual-camera-controls-images/image1.png "Cenni preliminari sugli oggetti acquisire AVFoundation")](intro-to-manual-camera-controls-images/image1.png#lightbox)

Input viene acquisito da un' `AVCaptureDeviceInput` in un `AVCaptureSession` per mezzo di un `AVCaptureConnection`. Il risultato è l'output come un'immagine fissa o come un flusso video. L'intero processo è controllato da un `AVCaptureDevice`.

## <a name="manual-controls-provided"></a>Controlli manuali forniti

Usa le nuove API fornite da iOS 8, l'applicazione può assumere il controllo delle funzionalità della fotocamera seguenti:

-  **Manuale dello stato attivo** – consentendo all'utente finale di assumere il controllo dello stato attivo direttamente, un'applicazione può fornire maggiore controllo sull'immagine impiegato.
-  **Manuale dell'esposizione** – fornendo l'esposizione di controllare manualmente, un'applicazione può fornire una maggiore libertà per gli utenti e consentire loro di ottenere un aspetto con stili.
-  **Manuale del bianco** – saldo vuoti vengono usato per adeguare il colore in un'immagine, spesso in modo che appaia realistico. Differenti sorgenti di luce hanno le temperature di colore diverso e le impostazioni di fotocamera usate per acquisire un'immagine è adattati per compensare le differenze. Anche in questo caso, consentendo il controllo utente sull'equilibrio bianco, gli utenti possono apportare modifiche che non possono essere eseguite automaticamente.


iOS 8 fornisce le estensioni e miglioramenti a iOS esistente le API per fornire questo controllo con granularità fine sull'immagine del processo di acquisizione.

## <a name="bracketed-capture"></a>Acquisizione tra parentesi quadre

Tra parentesi quadre di acquisizione è basato sulle impostazioni dai controlli della fotocamera manuali descritti in precedenza e consente all'applicazione acquisire un momento nel tempo, in un'ampia gamma di modi diversi.

Semplificando, racchiusi tra parentesi quadre acquisizione rappresenta un burst di acquisizione immagini scattate con un'ampia gamma di impostazioni dall'immagine di immagine.

## <a name="requirements"></a>Requisiti

Per completare la procedura descritta in questo articolo sono necessari i seguenti:

-  **Xcode 7 + e iOS 8 o versione superiore** – Apple Xcode 7 e iOS 8 o le API più recenti devono essere installati e configurati nel computer dello sviluppatore.
-  **Visual Studio per Mac** : la versione più recente di Visual Studio per Mac deve essere installata e configurata sul dispositivo dell'utente.
-  **iOS 8 dispositivo** : un dispositivo iOS in esecuzione la versione più recente di iOS 8. Non è possibile testare le funzionalità di fotocamera nel simulatore iOS.


## <a name="general-av-capture-setup"></a>Programma di installazione generale AV acquisizione

Quando si registra un video in un dispositivo iOS, è presente un codice di configurazione generale che è sempre obbligatorio. Questa sezione illustra la configurazione minima necessaria per registrare video dalla fotocamera del dispositivo iOS e visualizzare tale video in tempo reale in un `UIImageView`.

### <a name="output-sample-buffer-delegate"></a>Delegato del Buffer di output campione

Una delle prime cose necessarie risulteranno un delegato per monitorare il buffer di Output di esempio e visualizzare un'immagine raccolti dal buffer in cui un `UIImageView` nell'interfaccia utente dell'applicazione.

La routine seguente verrà monitorare il Buffer di esempio e aggiornare l'interfaccia utente:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using AVFoundation;
using CoreVideo;
using CoreMedia;
using CoreGraphics;

namespace ManualCameraControls
{
    public class OutputRecorder : AVCaptureVideoDataOutputSampleBufferDelegate
    {
        #region Computed Properties
        public UIImageView DisplayView { get; set; }
        #endregion

        #region Constructors
        public OutputRecorder ()
        {

        }
        #endregion

        #region Private Methods
        private UIImage GetImageFromSampleBuffer(CMSampleBuffer sampleBuffer) {

            // Get a pixel buffer from the sample buffer
            using (var pixelBuffer = sampleBuffer.GetImageBuffer () as CVPixelBuffer) {
                // Lock the base address
                pixelBuffer.Lock (0);

                // Prepare to decode buffer
                var flags = CGBitmapFlags.PremultipliedFirst | CGBitmapFlags.ByteOrder32Little;

                // Decode buffer - Create a new colorspace
                using (var cs = CGColorSpace.CreateDeviceRGB ()) {

                    // Create new context from buffer
                    using (var context = new CGBitmapContext (pixelBuffer.BaseAddress,
                        pixelBuffer.Width,
                        pixelBuffer.Height,
                        8,
                        pixelBuffer.BytesPerRow,
                        cs,
                        (CGImageAlphaInfo)flags)) {

                        // Get the image from the context
                        using (var cgImage = context.ToImage ()) {

                            // Unlock and return image
                            pixelBuffer.Unlock (0);
                            return UIImage.FromImage (cgImage);
                        }
                    }
                }
            }
        }
        #endregion

        #region Override Methods
        public override void DidOutputSampleBuffer (AVCaptureOutput captureOutput, CMSampleBuffer sampleBuffer, AVCaptureConnection connection)
        {
            // Trap all errors
            try {
                // Grab an image from the buffer
                var image = GetImageFromSampleBuffer(sampleBuffer);

                // Display the image
                if (DisplayView !=null) {
                    DisplayView.BeginInvokeOnMainThread(() => {
                        // Set the image
                        if (DisplayView.Image != null) DisplayView.Image.Dispose();
                        DisplayView.Image = image;

                        // Rotate image to the correct display orientation
                        DisplayView.Transform = CGAffineTransform.MakeRotation((float)Math.PI/2);
                    });
                }

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            }
            catch(Exception e) {
                // Report error
                Console.WriteLine ("Error sampling buffer: {0}", e.Message);
            }
        }
        #endregion
    }
}
```

Con questa routine sul posto, la `AppDelegate` può essere modificato per aprire una sessione di acquisizione AV per registrare un feed video in tempo reale.

### <a name="creating-an-av-capture-session"></a>Creazione di una sessione di acquisizione AV

La sessione di acquisizione AV viene utilizzata per controllare la registrazione del video in tempo reale dalla fotocamera del dispositivo iOS ed è obbligatorio per ottenere a video in un'applicazione iOS. Poiché l'esempio `ManualCameraControl` applicazione di esempio Usa la sessione di acquisizione in diverse posizioni, saranno presenti il `AppDelegate` e resi disponibili per l'intera applicazione.

Eseguire il comando seguente per modificare l'applicazione `AppDelegate` e aggiungere il codice necessario:


1. Fare doppio clic il `AppDelegate.cs` file in Esplora soluzioni per aprirlo e modificarlo.
1. Aggiungere le seguenti istruzioni using all'inizio del file:
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    ```

1. Aggiungere le seguenti variabili private e le proprietà calcolate per la `AppDelegate` classe:
    
    ```
    #region Private Variables
    private NSError Error;
    #endregion
    
    #region Computed Properties
    public override UIWindow Window {get;set;}
    public bool CameraAvailable { get; set; }
    public AVCaptureSession Session { get; set; }
    public AVCaptureDevice CaptureDevice { get; set; }
    public OutputRecorder Recorder { get; set; }
    public DispatchQueue Queue { get; set; }
    public AVCaptureDeviceInput Input { get; set; }
    #endregion
    ```
  
1. L'override del metodo finito e modificarlo come segue:
    
    ```
    public override void FinishedLaunching (UIApplication application)
    {
        // Create a new capture session
        Session = new AVCaptureSession ();
        Session.SessionPreset = AVCaptureSession.PresetMedium;
    
        // Create a device input
        CaptureDevice = AVCaptureDevice.DefaultDeviceWithMediaType (AVMediaType.Video);
        if (CaptureDevice == null) {
            // Video capture not supported, abort
            Console.WriteLine ("Video recording not supported on this device");
            CameraAvailable = false;
            return;
        }
    
        // Prepare device for configuration
        CaptureDevice.LockForConfiguration (out Error);
        if (Error != null) {
            // There has been an issue, abort
            Console.WriteLine ("Error: {0}", Error.LocalizedDescription);
            CaptureDevice.UnlockForConfiguration ();
            return;
        }
    
        // Configure stream for 15 frames per second (fps)
        CaptureDevice.ActiveVideoMinFrameDuration = new CMTime (1, 15);
    
        // Unlock configuration
        CaptureDevice.UnlockForConfiguration ();
    
        // Get input from capture device
        Input = AVCaptureDeviceInput.FromDevice (CaptureDevice);
        if (Input == null) {
            // Error, report and abort
            Console.WriteLine ("Unable to gain input from capture device.");
            CameraAvailable = false;
            return;
        }
    
        // Attach input to session
        Session.AddInput (Input);
    
        // Create a new output
        var output = new AVCaptureVideoDataOutput ();
        var settings = new AVVideoSettingsUncompressed ();
        settings.PixelFormatType = CVPixelFormatType.CV32BGRA;
        output.WeakVideoSettings = settings.Dictionary;
    
        // Configure and attach to the output to the session
        Queue = new DispatchQueue ("ManCamQueue");
        Recorder = new OutputRecorder ();
        output.SetSampleBufferDelegate (Recorder, Queue);
        Session.AddOutput (output);
    
        // Let tabs know that a camera is available
        CameraAvailable = true;
    }
    ```  
  
1. Salvare le modifiche apportate al file.


Con questo codice, i controlli della fotocamera manuali può essere facilmente implementati per test e sperimentazione.

## <a name="manual-focus"></a>Manuale dello stato attivo

Consentendo all'utente finale di eseguire direttamente i controlli dello stato attivo, un'applicazione può fornire più artisticamente dotati controllare l'immagine ottenuta.

Ad esempio, una Fotografa professionista possibile sfumare lo stato attivo di un'immagine da ottenere un [effetto Bokeh](https://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "Un effetto Bokeh")](intro-to-manual-camera-controls-images/image2.png#lightbox)

In alternativa, creare un [effetto di eseguire il Pull dello stato attivo](http://www.mediacollege.com/video/camera/focus/pull.html), ad esempio:

[![](intro-to-manual-camera-controls-images/image3.png "L'effetto di Pull messa a fuoco")](intro-to-manual-camera-controls-images/image3.png#lightbox)

Per un writer di applicazioni mediche o un data Scientist, l'applicazione potrebbe essere necessario a livello di programmazione spostino trasparente per gli esperimenti. In entrambi i casi la nuova API consente all'utente finale o l'applicazione possa controllare lo stato attivo al momento l'immagine viene eseguita.

### <a name="how-focus-works"></a>Come funziona lo stato attivo

Prima di discutere i dettagli di controllare lo stato attivo in un'applicazione IOS 8. Diamo un'occhiata al funzionamento dello stato attivo in un dispositivo iOS:

[![](intro-to-manual-camera-controls-images/image4.png "Funzionamento della messa a fuoco in un dispositivo iOS")](intro-to-manual-camera-controls-images/image4.png#lightbox)

Light immette l'obiettivo della fotocamera sul dispositivo iOS ed è incentrato su un sensore. La distanza della sezione dai controlli sensore dove è il punto focale (l'area in cui le immagini verranno visualizzate le tecniche più eccelse), in relazione al sensore. L'obiettivo a una distanza maggiore è dal sensore, distanza oggetti sembrano tecniche più eccelse e la più prossimo, accanto agli oggetti sembrano tecniche più eccelse.

In un dispositivo iOS, l'obiettivo è spostato più vicini a o diverso da, il sensore magneti e springs. Di conseguenza, la posizione esatta della sezione è possibile, perché possono variare da un dispositivo a altro e può dipendere dai parametri, ad esempio l'orientamento del dispositivo o l'età del dispositivo e spring.

### <a name="important-focus-terms"></a>Occorre prestare particolarmente attenzione termini

Quando si lavora con lo stato attivo, esistono alcune condizioni che lo sviluppatore deve avere familiarità con:

-  **Profondità del campo** : distanza tra gli oggetti più vicini e più lontana messa a fuoco. 
-  **Macro** -si tratta di estremità dello spettro messa a fuoco ed è la distanza minima in corrispondenza del quale è possibile concentrarsi obiettivo.
-  **Infinito** – si tratta di estremità dello spettro di messa a fuoco ed è la distanza più lontana in corrispondenza del quale è possibile concentrarsi obiettivo.
-  **Distanza Hyperfocal** – questo è il punto nello spettro di messa a fuoco in cui l'oggetto più lontano nel frame è solo all'estremità di destinazione dello stato attivo. In altre parole, si tratta della posizione focale che ottimizza la profondità del campo. 
-  **Sezione posizione** – questo è ciò che controlla tutte le opzioni sopra indicate altre condizioni. Questa è la distanza della sezione del sensore e in tal modo il controller dello stato attivo.


Con questi termini e della Knowledge Base presente, i nuovi controlli lo stato attivo manuale può essere implementati correttamente in un'applicazione iOS 8.

### <a name="existing-focus-controls"></a>Controlli esistenti messa a fuoco

iOS 7 e versioni precedenti, fornite esistente controlli lo stato attivo tramite `FocusMode`proprietà come:

-   `AVCaptureFocusModeLocked` -Lo stato attivo è bloccato in un punto singolo messa a fuoco.
-   `AVCaptureFocusModeAutoFocus` -La fotocamera esegue lo sweep obiettivo in tutti i punti focali fino a quando non individua sharp messa a fuoco e quindi rimane in quella posizione.
-   `AVCaptureFocusModeContinuousAutoFocus` -La fotocamera refocuses ogni volta che rileva una condizione di fuori di fuoco.


I controlli esistenti anche fornito un punto di interesse tramite impostabile il`FocusPointOfInterest` proprietà, in modo che l'utente può toccare per concentrarsi su una determinata area. L'applicazione può inoltre tenere traccia del movimento lens monitorando il `IsAdjustingFocus` proprietà.

Inoltre, ha fornito limitazione dell'intervallo di `AutoFocusRangeRestriction` proprietà come:

-   `AVCaptureAutoFocusRangeRestrictionNear` : Limita il autofocus alla profondità nelle vicinanze. È utile nelle situazioni, ad esempio la scansione di un codice a matrice o un codice a barre.
-   `AVCaptureAutoFocusRangeRestrictionFar` : Limita il autofocus alla profondità distanti. È utile nelle situazioni in cui gli oggetti che risultano irrilevanti sono nel campo visivo (ad esempio, una cornice della finestra).


Infine è disponibile il `SmoothAutoFocus` proprietà che rallenta l'algoritmo di messa a fuoco automatica e i passaggi incrementi più piccoli per evitare di spostare gli elementi durante la registrazione video.

### <a name="new-focus-controls-in-ios-8"></a>Nuovi controlli lo stato attivo in iOS 8

Oltre alle funzionalità già fornita da iOS 7 e versioni successive, le funzionalità seguenti sono ora disponibili per controllare lo stato attivo in iOS 8:

-  Controllo completo manuale della posizione lente quando si bloccano lo stato attivo.
-  Osservazione di chiave-valore della posizione dell'obiettivo in qualsiasi modalità messa a fuoco.


Per implementare le funzionalità indicate sopra, il `AVCaptureDevice` classe è stata modificata per includere una sola lettura `LensPosition` proprietà utilizzata per ottenere la posizione corrente dell'obiettivo della fotocamera.

Per sfruttare il controllo manuale della posizione dell'obiettivo, il dispositivo di acquisizione deve essere in modalità messa a fuoco bloccato. Esempio:

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

Il `SetFocusModeLocked` metodo del dispositivo di acquisizione viene utilizzato per regolare la posizione della sezione della fotocamera. Per ricevere una notifica quando le modifiche siano effettive, è possibile fornire una routine di callback facoltativi. Esempio:

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

Come illustrato nel codice precedente, il dispositivo di acquisizione deve essere bloccato per la configurazione prima di poter apportare una modifica della posizione dell'obiettivo. I valori validi della posizione dell'ottica sono compresi tra 0,0 e 1,0.

### <a name="manual-focus-example"></a>Esempio di messa a fuoco manuale

Con il codice di configurazione generale AV acquisire sul posto, un `UIViewController` possono essere aggiunti a uno Storyboard dell'applicazione e configurato nel modo seguente:

[![](intro-to-manual-camera-controls-images/image5.png "Una classe UIViewController possono essere aggiunti alle applicazioni Storyboard e configurata come illustrato di seguito")](intro-to-manual-camera-controls-images/image5.png#lightbox)

La vista contiene gli elementi principali seguenti:

-  Oggetto `UIImageView` che verrà visualizzato il feed video.
-  Oggetto `UISegmentedControl` che cambierà la modalità messa a fuoco da automatico a bloccato.
-  Oggetto `UISlider` che mostra e aggiorna la posizione corrente dell'obiettivo.


Eseguire il comando seguente per wireup il controller di visualizzazione per il controllo dello stato attivo manuale:


1. Aggiungere quanto segue usando istruzioni:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Aggiungere le seguenti variabili private:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```  
  
1. Aggiungere proprietà calcolate seguenti:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Eseguire l'override di `ViewDidLoad` (metodo) e aggiungere il codice seguente:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Position.BeginInvokeOnMainThread(() =>{
                Position.Value = ThisApp.Input.Device.LensPosition;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto focus and start monitoring position
                Position.Enabled = false;
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.ContinuousAutoFocus;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Stop auto focus and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;
                Automatic = false;
                Position.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Position.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. Eseguire l'override di `ViewDidAppear` (metodo) e aggiungere il comando seguente per avviare la registrazione quando viene caricata la visualizzazione:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Con la fotocamera in modalità automatica, il dispositivo di scorrimento sposterà automaticamente come la fotocamera consente di regolare lo stato attivo:

    [![](intro-to-manual-camera-controls-images/image6.png "Il dispositivo di scorrimento sposterà automaticamente come la fotocamera consente di regolare lo stato attivo in questa app di esempio")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. Toccare il segmento bloccato e trascinare il cursore di posizione per regolare la posizione dell'obiettivo manualmente:

    [![](intro-to-manual-camera-controls-images/image7.png "Modificare manualmente la posizione dell'obiettivo")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. Arrestare l'applicazione.


Il codice precedente ha illustrato come monitorare la posizione dell'obiettivo durante la fotocamera è in modalità automatica o usare un dispositivo di scorrimento per controllare la posizione dell'obiettivo quando è in modalità bloccata.

## <a name="manual-exposure"></a>Esposizione manuale

L'esposizione si intende la luminosità di un'immagine in relazione la luminosità di origine e viene determinato dalla quantità luce raggiunge il sensore, per informazioni su come long e dal livello di miglioramento del sensore (mapping ISO). Fornendo l'esposizione di controllare manualmente, un'applicazione può fornire una maggiore libertà per l'utente finale e consentire loro di ottenere un aspetto con stili.

Usa il controllo dell'esposizione manuale, l'utente può richiedere un'immagine da estremamente chiaro a scuro e moody:

[![](intro-to-manual-camera-controls-images/image8.png "Un esempio di un'immagine che mostra l'esposizione da estremamente chiaro a scuro e moody")](intro-to-manual-camera-controls-images/image8.png#lightbox)

Anche in questo caso, questa operazione può essere eseguita automaticamente tramite il controllo a livello di codice per applicazioni scientifiche o tramite i controlli manuali forniti dall'interfaccia utente delle applicazioni. In entrambi i casi, la nuova iOS 8 API dell'esposizione forniscono controllo con granularità fine sulle impostazioni di esposizione della camera.

### <a name="how-exposure-works"></a>Come funziona l'esposizione

Prima di discutere i dettagli del controllo dell'esposizione in un'applicazione IOS 8. Diamo un'occhiata come funziona l'esposizione:

[![](intro-to-manual-camera-controls-images/image9.png "Come funziona l'esposizione")](intro-to-manual-camera-controls-images/image9.png#lightbox)

I tre elementi di base che si uniscono per controllare l'esposizione sono:

-  **Velocità dell'otturatore** – si tratta della lunghezza di tempo in cui il ridimensionamento è aperta per consentire a luce sul sensore di fotocamera. Più è breve l'ora che di ridimensionamento è visualizzato, minore è la luce è consentire e di nitida dell'immagine è (meno sfocatura movimento). Più è lunga il ridimensionamento è aperto, è consentire la luce più in e più di movimento sfocatura che si verifica.
-  **Mapping di ISO** – questo è un termine preso in prestito da fotografia di film e si riferisce al livello di riservatezza delle sostanze chimiche nel film alla luce. I valori ISO limitati nei film hanno minore livello di dettaglio e la riproduzione dei colori più preciso; valori bassi di ISO su sensori digitali sono meno rumore sensore ma meno la luminosità. Maggiore è questo valore ISO, luminoso l'immagine, ma con ulteriori rumore sensore. "ISO" su un sensore digitale è una misura della [guadagno electronic](https://en.wikipedia.org/wiki/Gain), non una funzionalità fisica. 
-  **Apertura dell'obiettivo** : queste sono le dimensioni di apertura dell'obiettivo. In tutti i dispositivi iOS dell'obiettivo è stato risolto, in modo che solo due valori che possono essere utilizzati per regolare l'esposizione siano velocità e ISO.


### <a name="how-continuous-auto-exposure-works"></a>Modalità continua automaticamente l'esposizione Works

Prima di apprendimento come funziona l'esposizione manuale, è una buona idea per comprendere come continua esposizione automatica funziona in un dispositivo iOS.

[![](intro-to-manual-camera-controls-images/image10.png "Come funziona l'esposizione automatica continua in un dispositivo iOS")](intro-to-manual-camera-controls-images/image10.png#lightbox)

Prima di tutto è il blocco, l'esposizione automatica ha il compito di calcolare l'esposizione ideale e continuamente alimentata analisi delle statistiche. Usa queste informazioni per calcolare la combinazione ottima di ISO e tempo di esposizione per ottenere la scena anche acceso. Questo ciclo è detto del ciclo di Always Encrypted.

### <a name="how-locked-exposure-works"></a>Bloccato come funziona l'esposizione

Successivamente, si esaminerà come bloccato l'esposizione funziona in dispositivi iOS di proprietà.

[![](intro-to-manual-camera-controls-images/image11.png "Come bloccato l'esposizione funziona in dispositivi iOS")](intro-to-manual-camera-controls-images/image11.png#lightbox)

Anche in questo caso, è necessario il blocco di esposizione automatica che sta tentando di calcolare i valori di durata e iOS ottimale. In questa modalità, tuttavia, il blocco di Always Encrypted è disconnesso dal motore di analisi delle statistiche.

### <a name="existing-exposure-controls"></a>Controllo dell'esposizione esistente

iOS 7 e versioni successive, specificare i seguenti controlli esistenti dell'esposizione tramite il `ExposureMode` proprietà:

-   `AVCaptureExposureModeLocked` : Esegue il campionamento della scena una sola volta e Usa tali valori in tutta la scena.
-   `AVCaptureExposureModeContinuousAutoExposure` : Esegue il campionamento della scena in modo continuo per garantire che anche è acceso.


Il `ExposurePointOfInterest` può essere utilizzato per un tocco per esporre la scena selezionando un oggetto di destinazione da esporre in, e l'applicazione è possibile monitorare il `AdjustingExposure` proprietà da visualizzare quando viene modificato l'esposizione.

### <a name="new-exposure-controls-in-ios-8"></a>Nuovi controlli di visibilità in iOS 8

Oltre alle funzionalità già fornita da iOS 7 e versioni successive, le funzionalità seguenti sono ora disponibili per controllare l'esposizione in iOS 8:

-  Esposizione personalizzato completamente manuale.
-  Get, Set e coppie chiave-valore osservare IOS e velocità (durata).


Per implementare le funzionalità indicate sopra, un nuovo `AVCaptureExposureModeCustom` modalità è stata aggiunta. Una volta la fotocamera in modalità personalizzata, il codice seguente è utilizzabile per regolare la durata dell'esposizione e ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

Nelle modalità Auto e bloccato, l'applicazione può modificare la deviazione della routine l'esposizione automatica usando il codice seguente:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

Gli intervalli delle impostazioni minime e massime dipendono dal dispositivo che viene eseguita l'applicazione, in modo che non si deve mai essere hardcoded. Per ottenere gli intervalli di valori minimo e massimo, utilizzare le proprietà seguenti:

-   `CaptureDevice.MinExposureTargetBias` 
-   `CaptureDevice.MaxExposureTargetBias` 
-   `CaptureDevice.ActiveFormat.MinISO` 
-   `CaptureDevice.ActiveFormat.MaxISO` 
-   `CaptureDevice.ActiveFormat.MinExposureDuration` 
-   `CaptureDevice.ActiveFormat.MaxExposureDuration` 


Come illustrato nel codice precedente, il dispositivo di acquisizione deve essere bloccato per la configurazione prima di poter apportare una modifica di esposizione.

### <a name="manual-exposure-example"></a>Esempio di esposizione manuale

Con il codice di configurazione generale AV acquisire sul posto, un `UIViewController` possono essere aggiunti a uno Storyboard dell'applicazione e configurato nel modo seguente:

[![](intro-to-manual-camera-controls-images/image12.png "Una classe UIViewController possono essere aggiunti alle applicazioni Storyboard e configurata come illustrato di seguito")](intro-to-manual-camera-controls-images/image12.png#lightbox)

La vista contiene gli elementi principali seguenti:

-  Oggetto `UIImageView` che verrà visualizzato il feed video.
-  Oggetto `UISegmentedControl` che cambierà la modalità messa a fuoco da automatico a bloccato.
-  Quattro `UISlider` controlli che mostra e aggiorna l'Offset, durata, ISO e distorsione.


Eseguire il comando seguente per wireup il controller di visualizzazione per il controllo dell'esposizione manuale:


1. Aggiungere quanto segue usando istruzioni:
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Aggiungere le seguenti variabili private:

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```  
  
1. Aggiungere proprietà calcolate seguenti:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Eseguire l'override di `ViewDidLoad` (metodo) e aggiungere il codice seguente:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Set min and max values
        Offset.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Offset.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        Duration.MinValue = 0.0f;
        Duration.MaxValue = 1.0f;
    
        ISO.MinValue = ThisApp.CaptureDevice.ActiveFormat.MinISO;
        ISO.MaxValue = ThisApp.CaptureDevice.ActiveFormat.MaxISO;
    
        Bias.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Bias.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Offset.BeginInvokeOnMainThread(() =>{
                Offset.Value = ThisApp.Input.Device.ExposureTargetOffset;
            });
    
            Duration.BeginInvokeOnMainThread(() =>{
                var newDurationSeconds = CMTimeGetSeconds(ThisApp.Input.Device.ExposureDuration);
                var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration), ExposureMinimumDuration);
                var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
                var p = (newDurationSeconds - minDurationSeconds) / (maxDurationSeconds - minDurationSeconds);
                Duration.Value = (float)Math.Pow(p, 1.0f/ExposureDurationPower);
            });
    
            ISO.BeginInvokeOnMainThread(() => {
                ISO.Value = ThisApp.Input.Device.ISO;
            });
    
            Bias.BeginInvokeOnMainThread(() => {
                Bias.Value = ThisApp.Input.Device.ExposureTargetBias;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto exposure and start monitoring position
                Duration.Enabled = false;
                ISO.Enabled = false;
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.ContinuousAutoExposure;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Lock exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Locked;
                Automatic = false;
                Duration.Enabled = false;
                ISO.Enabled = false;
                break;
            case 2:
                // Custom exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Custom;
                Automatic = false;
                Duration.Enabled = true;
                ISO.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Duration.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Calculate value
            var p = Math.Pow(Duration.Value,ExposureDurationPower);
            var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration),ExposureMinimumDuration);
            var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
            var newDurationSeconds = p * (maxDurationSeconds - minDurationSeconds) +minDurationSeconds;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(CMTime.FromSeconds(p,1000*1000*1000),ThisApp.CaptureDevice.ISO,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        ISO.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(ThisApp.CaptureDevice.ExposureDuration,ISO.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        Bias.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            // if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetExposureTargetBias(Bias.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. Eseguire l'override di `ViewDidAppear` (metodo) e aggiungere il comando seguente per avviare la registrazione quando viene caricata la visualizzazione:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Con la fotocamera in modalità Auto, i dispositivi di scorrimento sposterà automaticamente come la fotocamera viene modificata l'esposizione:

    [![](intro-to-manual-camera-controls-images/image13.png "I dispositivi di scorrimento sposterà automaticamente come la fotocamera viene modificata l'esposizione")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. Toccare il segmento bloccato e trascinare il cursore di deviazione per regolare la deviazione dell'esposizione automatica manualmente:

    [![](intro-to-manual-camera-controls-images/image14.png "Modificare manualmente la deviazione dell'esposizione automatica")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. Toccare il segmento personalizzato e trascinare i dispositivi di scorrimento per controllare manualmente l'esposizione alla durata e ISO:

    [![](intro-to-manual-camera-controls-images/image15.png "Trascinare i dispositivi di scorrimento di durata e ISO per controllare l'esposizione manualmente")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. Arrestare l'applicazione.


Il codice precedente ha illustrato come monitorare le impostazioni dell'esposizione quando la fotocamera è in modalità automatica e su come usare i dispositivi di scorrimento per controllare l'esposizione quando è nelle modalità bloccato o personalizzato.

## <a name="manual-white-balance"></a>Manuale del bianco

Controlli del bianco consentono agli utenti di bilanciare i colosr in un'immagine per renderli più realistico. Differenti sorgenti di luce dispongono delle temperature di colore diverso, ed è necessario modificare le impostazioni di fotocamera usate per acquisire un'immagine per compensare la modifica di tali differenze. Anche in questo caso, consentendo il controllo utente sull'equilibrio bianco è possibile apportare modifiche professionale che le routine automatiche sono in grado di ottenere effetti artistici alle.

[![](intro-to-manual-camera-controls-images/image16.png "Un'immagine di esempio che mostra le regolazioni del bianco manuale")](intro-to-manual-camera-controls-images/image16.png#lightbox)

Ad esempio, ora legale ha un cast bluastro, mentre le luci incandescenza tungsten hanno una tonalità-giallo, più frequente. (Confusione, "accesso sporadico" colori hanno la temperatura del colore superiore rispetto a colori "caldo". La temperatura del colore è una misura fisica, non uno percettiva).

Il mente umana è in grado di compensazione alle differenze di temperatura del colore, ma si tratta di qualcosa che non è possibile una fotocamera. La fotocamera funziona assegnando una priorità maggiore di colore nella gamma delle opposta per regolare le differenze di colore.

La nuova iOS 8 API l'esposizione consente all'applicazione di assumere il controllo del processo e fornisce il controllo con granularità fine sulle impostazioni del bianco della fotocamera.

### <a name="how-white-balance-works"></a>Funzionamento del saldo come bianca

Prima di discutere i dettagli del controllo del bianco in un'applicazione IOS 8. Diamo un'occhiata vuoti come saldo works:

Nello studio di percezione di colore, il [CIE 1931 RGB dei colori spazio e lo spazio colore XYZ CIE 1931](https://en.wikipedia.org/wiki/CIE_1931_color_space) sono il primo matematicamente definiti gli spazi colore. Sono stati creati dalla Commissione internazionale in illuminazione (CIE) in 1931.

[![](intro-to-manual-camera-controls-images/image17.png "La spazio colori RGB CIE 1931 e CIE 1931 XYZ spazio colore")](intro-to-manual-camera-controls-images/image17.png#lightbox)

Il grafico precedente mostra tutti i colori visibili all'occhio umano, da blu a verde brillante a rosso chiaro avanzato. Qualsiasi punto del diagramma può essere tracciato con un valore di X e Y, come illustrato nel grafico precedente.

Come si può vedere nel grafico, i valori X e Y possono essere tracciati sul grafico che sarebbe di fuori dell'intervallo di visione artificiale umani e di conseguenza questi colori non possono essere riprodotti da una fotocamera.

La curva minore nel grafico precedente viene chiamata il [luogo Planckian](https://en.wikipedia.org/wiki/Planckian_locus), che esprime la temperatura di colore (in gradi kelvin), i valori più alti sul lato blu (caldo) e i numeri di basso sul lato rosso (refrigeratore). Queste sono utili in situazioni di illuminazione tipico.

Nelle condizioni di illuminazione mista, saranno necessario le regolazioni del bianco si discostano dal luogo Planckian per apportare le modifiche necessarie. In queste situazioni che sarà necessario la regolazione per essere spostati in verde o rosso/magenta CIE sul lato di scalabilità.

i dispositivi iOS di compensare tonalità di colore promuovendo il miglioramento di colori opposti. Ad esempio, se una scena dispone di una quantità eccessiva blu, il guadagno rossa verrà essere incrementato per compensare la modifica. Questi valori vengono calibrati per dispositivi specifici in modo che siano dipendenti dal dispositivo di guadagno.

### <a name="existing-white-balance-controls"></a>Controlli esistenti del bianco

iOS 7 e versioni successive fornito i seguenti controlli di saldo vuoto esistenti tramite `WhiteBalanceMode` proprietà:

-   `AVCapture WhiteBalance ModeLocked` : Esegue il campionamento della scena in una sola volta e l'utilizzo di tali valori in tutta la scena.
-   `AVCapture WhiteBalance ModeContinuousAutoExposure` : Esegue il campionamento della scena in modo continuo per garantire che sia anche bilanciato.


E l'applicazione è possibile monitorare il `AdjustingWhiteBalance` proprietà da visualizzare quando viene modificato l'esposizione.

### <a name="new-white-balance-controls-in-ios-8"></a>Nuovi controlli di bilanciare il nero in iOS 8

Oltre alle funzionalità già fornita da iOS 7 e versioni successive, le funzionalità seguenti sono ora disponibili a controllo del bianco in iOS 8:

-  Ottiene il controllo manuale del dispositivo RGB.
-  Get, Set e osservare chiave-valore acquisisce il dispositivo RGB.
-  Supporto per bilanciamento usando una carta di grigio.
-  Routine di conversione da e verso gli spazi colore indipendente dal dispositivo.


Per implementare le funzionalità indicate sopra, il `AVCaptureWhiteBalanceGain` struttura è stato aggiunto con i membri seguenti:

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


Il miglioramento massimo equilibrio bianco è attualmente quattro (4) e può essere predisposto il `MaxWhiteBalanceGain` proprietà. In modo che i valori validi sono compresi da uno (1) `MaxWhiteBalanceGain` (4) attualmente.

Il `DeviceWhiteBalanceGains` proprietà può essere utilizzata per osservare i valori correnti. Usare `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` per regolare il saldo riesce a ottenere la fotocamera è in modalità bloccata bilanciare il bianco.

#### <a name="conversion-routines"></a>Routine di conversione

Routine di conversione sono stati aggiunti ai dispositivi iOS 8 per facilitare la conversione verso e da, gli spazi colore indipendente dal dispositivo. Per implementare le routine di conversione, il `AVCaptureWhiteBalanceChromaticityValues` struttura è stato aggiunto con i membri seguenti:

-   `X` -è un valore compreso tra 0 e 1.
-   `Y` -è un valore compreso tra 0 e 1.


Un `AVCaptureWhiteBalanceTemperatureAndTintValues` struttura è stata aggiunta anche con i membri seguenti:

-   `Temperature` -Mobile valore punto espresso in gradi Kelvin.
-   `Tint` -è un offset da verde o magenta da 0 a 150 con i valori positivi verso la direzione di verde e negativo verso la magenta.


Usare la `CaptureDevice.GetTemperatureAndTintValues`e il `CaptureDevice.GetDeviceWhiteBalanceGains`metodi per la conversione tra temperatura e tint, cromatiche e RGB ottenere gli spazi colore.

> [!NOTE]
> Le routine di conversione sono più accurate il valore da convertire è vicino per il luogo Planckian.




#### <a name="gray-card-support"></a>Supporto di Smart Card grigio

Apple Usa il termine World grigio per fare riferimento il supporto di Smart Card grigio integrato in iOS 8. Consente all'utente di concentrarsi su una scheda grigio fisico che include almeno il 50% del centro del frame e lo usa per bilanciare i vuoti. Lo scopo della scheda grigio è per ottenere white visualizzato neutro.

Ciò può essere implementata in un'applicazione da cui viene richiesto all'utente di inserire una scheda fisica grigia davanti la fotocamera, il monitoraggio di `GrayWorldDeviceWhiteBalanceGains` proprietà e in attesa fino a quando i valori liquidati.

L'applicazione verrebbe quindi bloccare i guadagni di bilanciamento per il `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` usando i valori dal metodo di `GrayWorldDeviceWhiteBalanceGains` proprietà a cui applicare le modifiche.

Il dispositivo di acquisizione deve essere bloccato per la configurazione prima di poter apportare una modifica di bilanciare il bianco.

### <a name="manual-white-balance-example"></a>Esempio di bilanciare il bianco manuale

Con il codice di configurazione generale AV acquisire sul posto, un `UIViewController` possono essere aggiunti a uno Storyboard dell'applicazione e configurato nel modo seguente:

[![](intro-to-manual-camera-controls-images/image18.png "Una classe UIViewController possono essere aggiunti alle applicazioni Storyboard e configurata come illustrato di seguito")](intro-to-manual-camera-controls-images/image18.png#lightbox)

La vista contiene gli elementi principali seguenti:

-  Oggetto `UIImageView` che verrà visualizzato il feed video.
-  Oggetto `UISegmentedControl` che cambierà la modalità messa a fuoco da automatico a bloccato.
-  Due `UISlider` controlli che mostra e aggiorna la temperatura e Tint.
-  Oggetto `UIButton` usato come esempio uno spazio di Smart Card Gray (grigio World) e impostare il saldo vuoti utilizzando tali valori.


Eseguire il comando seguente per wireup il controller di visualizzazione per il controllo di saldi White manuale:


1. Aggiungere quanto segue usando istruzioni:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Aggiungere le seguenti variabili private:

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    #endregion
    ```
  
1. Aggiungere proprietà calcolate seguenti:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Aggiungere il metodo privato seguente per impostare il nuovo white bilanciare la temperatura e tinta:

    ```csharp
    #region Private Methods
    void SetTemperatureAndTint() {
        // Grab current temp and tint
        var TempAndTint = new AVCaptureWhiteBalanceTemperatureAndTintValues (Temperature.Value, Tint.Value);

        // Convert Color space
        var gains = ThisApp.CaptureDevice.GetDeviceWhiteBalanceGains (TempAndTint);

        // Set the new values
        if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
            gains = NomralizeGains (gains);
            ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
            ThisApp.CaptureDevice.UnlockForConfiguration ();
        }
    }
    
    AVCaptureWhiteBalanceGains NomralizeGains (AVCaptureWhiteBalanceGains gains)
    {
        gains.RedGain = Math.Max (1, gains.RedGain);
        gains.BlueGain = Math.Max (1, gains.BlueGain);
        gains.GreenGain = Math.Max (1, gains.GreenGain);

        float maxGain = ThisApp.CaptureDevice.MaxWhiteBalanceGain;
        gains.RedGain = Math.Min (maxGain, gains.RedGain);
        gains.BlueGain = Math.Min (maxGain, gains.BlueGain);
        gains.GreenGain = Math.Min (maxGain, gains.GreenGain);

        return gains;
    }
    #endregion
    ```   
  
1. Eseguire l'override di `ViewDidLoad` (metodo) e aggiungere il codice seguente:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Temperature.MinValue = 1000f;
        Temperature.MaxValue = 10000f;

        Tint.MinValue = -150f;
        Tint.MaxValue = 150f;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Convert color space
            var TempAndTint = ThisApp.CaptureDevice.GetTemperatureAndTintValues (ThisApp.CaptureDevice.DeviceWhiteBalanceGains);

            // Update slider positions
            Temperature.BeginInvokeOnMainThread (() => {
                Temperature.Value = TempAndTint.Temperature;
            });

            Tint.BeginInvokeOnMainThread (() => {
                Tint.Value = TempAndTint.Tint;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (sender, e) => {
            // Lock device for change
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {

                // Take action based on the segment selected
                switch (Segments.SelectedSegment) {
                case 0:
                // Activate auto focus and start monitoring position
                    Temperature.Enabled = false;
                    Tint.Enabled = false;
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.ContinuousAutoWhiteBalance;
                    SampleTimer.Start ();
                    Automatic = true;
                    break;
                case 1:
                // Stop auto focus and allow the user to control the camera
                    SampleTimer.Stop ();
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.Locked;
                    Automatic = false;
                    Temperature.Enabled = true;
                    Tint.Enabled = true;
                    break;
                }

                // Unlock device
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };

        // Monitor position changes
        Temperature.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        Tint.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        GrayCardButton.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Get gray card values
            var gains = ThisApp.CaptureDevice.GrayWorldDeviceWhiteBalanceGains;

            // Set the new values
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
                ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };
    }
    ``` 
  
1. Eseguire l'override di `ViewDidAppear` (metodo) e aggiungere il comando seguente per avviare la registrazione quando viene caricata la visualizzazione:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Salvare le modifiche di codice ed eseguire l'applicazione.
1. Con la fotocamera in modalità Auto, i dispositivi di scorrimento sposterà automaticamente come la fotocamera consente di regolare del bianco:

    [![](intro-to-manual-camera-controls-images/image19.png "I dispositivi di scorrimento sposterà automaticamente come la fotocamera consente di regolare del bianco")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. Toccare il segmento bloccato e trascinare i dispositivi di scorrimento per regolare manualmente il saldo bianco Temp e tinta:

    [![](intro-to-manual-camera-controls-images/image20.png "Trascinare i dispositivi di scorrimento per regolare manualmente il saldo bianco Temp e tinta")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. Con il segmento bloccato ancora selezionato, posizionare una scheda fisica grigio in primo piano della fotocamera e toccare il pulsante di Smart Card grigio per regolare del bianco a tutto il mondo grigio:

    [![](intro-to-manual-camera-controls-images/image21.png "Toccare il pulsante di Smart Card grigio per regolare del bianco a tutto il mondo grigio")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. Arrestare l'applicazione.

Il codice precedente ha illustrato come monitorare le impostazioni del bianco quando la fotocamera è in modalità automatica o usare i dispositivi di scorrimento per controllare il saldo bianco quando è in modalità bloccata.

## <a name="bracketed-capture"></a>Acquisizione tra parentesi quadre

Tra parentesi quadre di acquisizione è basato sulle impostazioni dai controlli della fotocamera manuali descritti in precedenza e consente all'applicazione acquisire un momento nel tempo, in un'ampia gamma di modi diversi.

Semplificando, racchiusi tra parentesi quadre acquisizione rappresenta un burst di acquisizione immagini scattate con un'ampia gamma di impostazioni dall'immagine di immagine.

[![](intro-to-manual-camera-controls-images/image22.png "Come funziona acquisizione di racchiusi tra parentesi quadre")](intro-to-manual-camera-controls-images/image22.png#lightbox)

Usa racchiusi tra parentesi quadre di acquisizione in iOS 8, un'applicazione può preimpostato una serie di controlli della fotocamera manuali, inviare un comando singolo e avere nella scena corrente restituiscono una serie di immagini per ognuno dei set di impostazioni manuali.

### <a name="bracketed-capture-basics"></a>Nozioni di base di acquisizione tra parentesi quadre

Anche in questo caso, racchiusi tra parentesi quadre acquisizione rappresenta un burst di acquisizione immagini scattate con impostazioni diverse dall'immagine di immagine. I tipi di racchiusi tra parentesi quadre Capture disponibili sono:

-  **Auto tra parentesi quadre l'esposizione** , in cui tutte le immagini hanno una quantità diverse di distorsione.
-  **Tra parentesi quadre l'esposizione manuale** , in cui tutte le immagini hanno una velocità (durata) e ISO diverse quantità.
-  **Tra parentesi quadre Burst semplice** : una serie di immagini ancora impiegato in rapida successione.


### <a name="new-bracketed-capture-controls-in-ios-8"></a>Nuovo racchiusi tra parentesi quadre acquisire i controlli in iOS 8

Tutti i comandi tra parentesi quadre Capture vengono implementati nel `AVCaptureStillImageOutput` classe. Usare il `CaptureStillImageBracket`metodo per ottenere una serie di immagini con la determinata matrice di impostazioni.

Sono state implementate due nuove classi per gestire le impostazioni:

-   `AVCaptureAutoExposureBracketedStillImageSettings` – E presenta una proprietà, `ExposureTargetBias`, usato per impostare la deviazione di una parentesi di esposizione automatica. 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings` – Ha due proprietà, `ExposureDuration` e `ISO`, usato per impostare la velocità e ISO per una parentesi quadra esposizione manuale. 


### <a name="bracketed-capture-controls-dos-and-donts"></a>Acquisizione tra parentesi quadre Controlla operazioni consigliate e sconsigliate

#### <a name="dos"></a>Operazioni consigliate

Di seguito è riportato un elenco di elementi che devono essere eseguite durante l'acquisizione di racchiusi tra parentesi quadre dei controlli in iOS 8:

-  Preparare l'app per la situazione peggiore acquisizione chiamando il `PrepareToCaptureStillImageBracket` (metodo).
-  Si supponga che i buffer di esempio intende provengono dallo stesso pool condiviso.
-  Per rilasciare la memoria allocata da una precedente chiamata di preparazione, chiamare `PrepareToCaptureStillImageBracket` nuovamente e lo invia a una matrice di un oggetto.


#### <a name="donts"></a>Regole di base

Di seguito è riportato un elenco di elementi che non deve essere eseguita durante l'acquisizione di racchiusi tra parentesi quadre dei controlli in iOS 8:

-  Non combinare tipi di impostazioni racchiusi tra parentesi quadre acquisire in una sola acquisizione.
-  Non richiedere più di `MaxBracketedCaptureStillImageCount` immagini in una sola acquisizione.


### <a name="bracketed-capture-details"></a>Dettagli di acquisizione tra parentesi quadre

I dettagli seguenti dovrebbero prendere in considerazione quando si lavora con racchiusi tra parentesi quadre acquisire in iOS 8:

-  Tra parentesi quadre le impostazioni di eseguire temporaneamente l'override di `AVCaptureDevice` impostazioni.
-  Flash e ancora immagine stabilizzazione vengono ignorate.
-  Tutte le immagini devono usare lo stesso formato di output (jpeg, png e così via)
-  Anteprima video può eliminare i frame.
-  Acquisizione tra parentesi quadre è supportato in tutti i dispositivi compatibili con iOS 8.


Con queste informazioni in considerazione, esaminiamo un esempio di utilizzo tra parentesi quadre acquisire in iOS 8.

### <a name="bracket-capture-example"></a>Esempio di acquisizione tra parentesi quadre

Con il codice di configurazione generale AV acquisire sul posto, un `UIViewController` possono essere aggiunti a uno Storyboard dell'applicazione e configurato nel modo seguente:

[![](intro-to-manual-camera-controls-images/image23.png "Una classe UIViewController possono essere aggiunti alle applicazioni Storyboard e configurata come illustrato di seguito")](intro-to-manual-camera-controls-images/image23.png#lightbox)

La vista contiene gli elementi principali seguenti:

-  Oggetto `UIImageView` che verrà visualizzato il feed video.
-  Tre `UIImageViews` che verranno visualizzati i risultati dell'acquisizione.
-  Oggetto `UIScrollView` per ospitare il feed video e visualizzazioni dei risultati.
-  Oggetto `UIButton` usato per richiedere un'acquisizione racchiusi tra parentesi quadre con alcune impostazioni predefinite.


Eseguire il comando seguente per wireup il controller di visualizzazione per l'acquisizione racchiusi tra parentesi quadre:


1. Aggiungere quanto segue usando istruzioni:

    ```csharp
    using System;
    using System.Drawing;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using CoreImage;
    ```  
  
1. Aggiungere le seguenti variabili private:

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```    
  
1. Aggiungere proprietà calcolate seguenti:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```  
  
1. Aggiungere il metodo privato seguente per compilare le viste di immagine di output richiesto:

    ```csharp
    #region Private Methods
    private UIImageView BuildOutputView(nint n) {
    
        // Create a new image view controller
        var imageView = new UIImageView (new CGRect (CameraView.Frame.Width * n, 0, CameraView.Frame.Width, CameraView.Frame.Height));
    
        // Load a temp image
        imageView.Image = UIImage.FromFile ("Default-568h@2x.png");
    
        // Add a label
        UILabel label = new UILabel (new CGRect (0, 20, CameraView.Frame.Width, 24));
        label.TextColor = UIColor.White;
        label.Text = string.Format ("Bracketed Image {0}", n);
        imageView.AddSubview (label);
    
        // Add to scrolling view
        ScrollView.AddSubview (imageView);
    
        // Return new image view
        return imageView;
    }
    #endregion
    ```  
  
1. Eseguire l'override di `ViewDidLoad` (metodo) e aggiungere il codice seguente:
    
    ```
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Setup scrolling area
        ScrollView.ContentSize = new SizeF (CameraView.Frame.Width * 4, CameraView.Frame.Height);
    
        // Add output views
        Output.Add (BuildOutputView (1));
        Output.Add (BuildOutputView (2));
        Output.Add (BuildOutputView (3));
    
        // Create preset settings
        var Settings = new AVCaptureBracketedStillImageSettings[] {
            AVCaptureAutoExposureBracketedStillImageSettings.Create(-2.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(0.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(2.0f)
        };
    
        // Wireup capture button
        CaptureButton.TouchUpInside += (sender, e) => {
            // Reset output index
            OutputIndex = 0;
    
            // Tell the camera that we are getting ready to do a bracketed capture
            ThisApp.StillImageOutput.PrepareToCaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings,async (bool ready, NSError err) => {
                // Was there an error, if so report it
                if (err!=null) {
                    Console.WriteLine("Error: {0}",err.LocalizedDescription);
                }
            });
    
            // Ask the camera to snap a bracketed capture
            ThisApp.StillImageOutput.CaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings, (sampleBuffer, settings, err) =>{
                // Convert raw image stream into a Core Image Image
                var imageData = AVCaptureStillImageOutput.JpegStillToNSData(sampleBuffer);
                var image = CIImage.FromData(imageData);
    
                // Display the resulting image
                Output[OutputIndex++].Image = UIImage.FromImage(image);
    
                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            });
        };
    }
    ```
    
  
1. Eseguire l'override di `ViewDidAppear` (metodo) e aggiungere il codice seguente:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
        }
    }
    
    ```  
    
1. Salvare le modifiche di codice ed eseguire l'applicazione.
1. Frame di una scena e toccare il pulsante di acquisizione tra parentesi quadre:

    [![](intro-to-manual-camera-controls-images/image24.png "Frame di una scena e toccare il pulsante di acquisizione tra parentesi quadre")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. Scorrere verso destra a sinistra per visualizzare le tre immagini usate da acquisire racchiusi tra parentesi quadre:

    [![](intro-to-manual-camera-controls-images/image25.png "Scorrere verso destra a sinistra per visualizzare le tre immagini usate da racchiusi tra parentesi quadre di acquisizione")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. Arrestare l'applicazione.


Il codice precedente ha illustrato come configurare ed evitare un'esposizione automatica tra parentesi quadre acquisire in iOS 8.

## <a name="summary"></a>Riepilogo

In questo articolo abbiamo coperto un'introduzione ai nuovi controlli della fotocamera manuali fornito da iOS 8 e ha spiegato le basi di cosa fanno e sul relativo funzionamento. È stato fornito esempi di messa a fuoco manuale, l'esposizione di manuale e saldo White manuale. Infine, è stato assegnato un esempio di creare un racchiusi tra parentesi di cattura tramite i controlli della fotocamera manuali descritte in precedenza

## <a name="related-links"></a>Collegamenti correlati

- [ManualCameraControls (esempio)](https://developer.xamarin.com/samples/monotouch/ManualCameraControls)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
