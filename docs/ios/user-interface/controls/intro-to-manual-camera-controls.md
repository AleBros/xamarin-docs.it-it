---
title: Manuale della fotocamera in xamarin. IOS
description: Questo documento viene descritto come framework iOS AVFoundation utilizzabile con xamarin. IOS per abilitare i controlli dispositivo foto/video manuale. Fotocamera manuale controlli consentono all'utente di controllo lo stato attivo, del bianco e impostazioni di esposizione.
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: a0f605a38117df87a03801c3b9d86b0b7361c232
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790825"
---
# <a name="manual-camera-controls-in-xamarinios"></a>Manuale della fotocamera in xamarin. IOS

I controlli di fotocamera manuale, fornito dal `AVFoundation Framework` in iOS 8, consentire a un'applicazione per dispositivi mobili assumere il controllo completo della fotocamera del dispositivo iOS. Questo livello di controllo con granularità fine è utilizzabile per creare applicazioni professionali fotocamera livello e fornire le composizioni artista modificando i parametri della camera durante la creazione di un'immagine o un video.

Questi controlli possono rivelarsi utili quando si sviluppano applicazioni di scientifiche o industriale, in cui i risultati sono meno pensati per la correttezza o di un vantaggio dell'immagine e più pensati per evidenziare alcune funzionalità o un elemento dell'immagine verrà portata.

## <a name="avfoundation-capture-objects"></a>Oggetti AVFoundation Capture

Se l'esecuzione di video o ancora immagini utilizzando la fotocamera in un dispositivo iOS, il processo utilizzato per acquisire tali immagini è in gran parte lo stesso. Questo vale per le applicazioni che utilizzano i controlli di fotocamera automatizzata predefinito o quelle che sfruttano i nuovi controlli fotocamera manuale:

 [![](intro-to-manual-camera-controls-images/image1.png "Panoramica sugli oggetti acquisire AVFoundation")](intro-to-manual-camera-controls-images/image1.png#lightbox)

Input viene eseguito da un `AVCaptureDeviceInput` in un `AVCaptureSession` per mezzo di un `AVCaptureConnection`. Il risultato è l'output come immagine fissa o come un flusso video. L'intero processo è controllato da un `AVCaptureDevice`.

## <a name="manual-controls-provided"></a>Controlli manuali forniti

Utilizzando le nuove API fornite da iOS 8, l'applicazione può assumere il controllo delle funzionalità di fotocamera seguenti:

-  **Messa a fuoco manuale** – consentendo di controllare lo stato attivo direttamente all'utente finale, un'applicazione può fornire maggiore controllo sull'immagine eseguita.
-  **Manuale dell'esposizione** – fornendo controllare manualmente l'esposizione di dati, un'applicazione può maggiore libertà di fornire agli utenti e consentire loro di ottenere un aspetto con stili.
-  **Bilanciamento manuale** – bilanciamento viene utilizzato per modificare il colore di un'immagine, spesso in modo che appaia realistici. Sorgenti di luce diverse hanno diverse temperature di colore e le impostazioni di fotocamera usate per acquisire un'immagine viene regolato per compensare le differenze. Nuovamente, consentendo il controllo utente tramite il bilanciamento del bianco, gli utenti possono apportare modifiche che non possono essere eseguite automaticamente.


iOS 8 fornisce le estensioni e miglioramenti per le API per fornire questa immagine di un controllo accurato di iOS esistente processo di acquisizione.

## <a name="bracketed-capture"></a>Acquisizione tra parentesi

Racchiusi tra parentesi quadre di acquisizione è basato sulle impostazioni dai controlli fotocamera manuale presentato in precedenza e consente all'applicazione di acquisire un determinato momento, in molti modi diversi.

Per acquisire racchiusi tra parentesi quadre è un burst di immagini scattate con una serie di impostazioni dall'immagine in immagine.

## <a name="requirements"></a>Requisiti

Per completare la procedura illustrata in questo articolo è necessario quanto segue:

-  **Xcode 7 + e iOS 8 o versione successiva** – Xcode 7 e iOS 8 o le API più recente di Apple devono essere installato e configurato nel computer dello sviluppatore.
-  **Visual Studio per Mac** : la versione più recente di Visual Studio per Mac deve essere installata e configurata sul dispositivo dell'utente.
-  **iOS 8 dispositivo** : un dispositivo iOS in esecuzione la versione più recente di iOS 8. Non è possibile testare le funzionalità di fotocamera nel simulatore iOS.


## <a name="general-av-capture-setup"></a>Programma di installazione di acquisizione AV generale

Registrazione video in un dispositivo iOS è codice impostazioni generali che è sempre obbligatorio. In questa sezione illustra la configurazione minima necessaria per registrare video dalla fotocamera del dispositivo iOS e visualizzare il video in tempo reale in un `UIImageView`.

### <a name="output-sample-buffer-delegate"></a>Delegato del Buffer di output esempio

Uno dei primi aspetti necessario deve essere un delegato per monitorare il buffer di Output di esempio e visualizzare un'immagine afferrata dal buffer in un `UIImageView` nell'interfaccia utente dell'applicazione.

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

La sessione di acquisizione AV viene utilizzata per controllare la registrazione del video in tempo reale da dispositivo foto/video del dispositivo iOS e per ottenere video in un'applicazione iOS è necessario. Poiché l'esempio `ManualCameraControl` applicazione di esempio Usa la sessione di acquisizione in varie posizioni, verrà configurata nel `AppDelegate` e resi disponibili per l'intera applicazione.

Eseguire le operazioni seguenti per modificare l'applicazione `AppDelegate` e aggiungere il codice necessario:


1. Fare doppio clic su di `AppDelegate.cs` file in Esplora soluzioni per aprirlo e modificarlo.
1. Aggiungere la seguente utilizzando le istruzioni nella parte superiore del file:
    
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
  
1. Override del metodo finito e modificarla in:
    
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


Con questo codice, i controlli di fotocamera manuale può essere implementati facilmente per sperimentazione e il test.

## <a name="manual-focus"></a>Messa a fuoco manuale

Consentendo di eseguire controlli dell'elemento attivo direttamente all'utente finale, un'applicazione può fornire più artistico l'immagine del controllo.

Ad esempio, una fotografia professionale possibile attenuare lo stato attivo di un'immagine per ottenere un [Bokeh effetto](http://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "Un effetto Bokeh")](intro-to-manual-camera-controls-images/image2.png#lightbox)

In alternativa, creare un [lo stato attivo per effettuare il Pull effetto](http://www.mediacollege.com/video/camera/focus/pull.html), ad esempio:

[![](intro-to-manual-camera-controls-images/image3.png "L'effetto di Pull lo stato attivo")](intro-to-manual-camera-controls-images/image3.png#lightbox)

Per un writer di applicazioni mediche o gli esperti, l'applicazione potrebbe voler a livello di codice spostarsi obiettivo per esperimenti. In entrambi i casi la nuova API consente all'utente finale o l'applicazione per assumere il controllo dello stato attivo al momento l'immagine viene eseguita.

### <a name="how-focus-works"></a>Funzionamento di messa a fuoco

Prima di esaminare i dettagli di controllare lo stato attivo in un'applicazione IOS 8. Esaminiamo un rapido funzionamento lo stato attivo in un dispositivo iOS:

[![](intro-to-manual-camera-controls-images/image4.png "Il funzionamento dello stato attivo in un dispositivo iOS")](intro-to-manual-camera-controls-images/image4.png#lightbox)

Chiaro immette l'obiettivo della fotocamera del dispositivo iOS e stato attivo si trova in un sensore. La distanza dell'obiettivo del sensore controlla dove è il punto focale (l'area in cui l'immagine verrà visualizzata la straordinarie), in relazione al sensore. Distanza l'obiettivo è il sensore, gli oggetti di distanza sembrano straordinarie e sembra straordinarie più vicina, accanto agli oggetti.

In un dispositivo iOS, l'obiettivo è spostato più vicino a o, più lontano, il sensore grado attirare l'attenzione e springs. Di conseguenza, la posizione esatta dell'obiettivo è impossibile, perché possono variare tra le periferiche e può dipendere dai parametri, ad esempio l'orientamento del dispositivo o l'età del dispositivo e molla.

### <a name="important-focus-terms"></a>Termini importanti dello stato attivo

Quando si gestiscono lo stato attivo, esistono alcune condizioni che lo sviluppatore deve acquisire familiarità con:

-  **Profondità del campo** : la distanza tra gli oggetti più prossimo e più lontano messa a fuoco. 
-  **Macro** -si tratta di estremità dello spettro lo stato attivo e la distanza minima in cui è possibile concentrarsi obiettivo.
-  **Infinito** : si tratta all'estremità dello spettro lo stato attivo e la distanza più lontana in corrispondenza del quale è possibile concentrarsi obiettivo.
-  **Distanza Hyperfocal** – questo è il punto nello spettro lo stato attivo in cui l'oggetto più lontano nel frame è solo all'estremità di destinazione dello stato attivo. In altre parole, si tratta della posizione focale che ottimizza la profondità di campo. 
-  **Obiettivo posizione** – è quali elementi vengono controllati tutti gli elementi indicati altri termini. Questa è la distanza dell'obiettivo del sensore e in tal modo il controller dello stato attivo.


I termini e delle informazioni presenti, i nuovi controlli di messa a fuoco manuale può essere implementati correttamente in un'applicazione iOS 8.

### <a name="existing-focus-controls"></a>Controlli esistenti lo stato attivo

iOS 7 e versioni precedenti, fornite tramite i controlli lo stato attivo esistenti `FocusMode`proprietà come:

-   `AVCaptureFocusModeLocked` -Lo stato attivo è bloccato in un punto singolo stato attivo.
-   `AVCaptureFocusModeAutoFocus` – La fotocamera di esegue lo sweep dell'obiettivo tramite tutti i punti centrali fino a quando non viene trovato lo stato attivo acuto e rimane in quella.
-   `AVCaptureFocusModeContinuousAutoFocus` – La fotocamera refocuses ogni volta che rileva una condizione di fuori di fuoco.


I controlli esistenti anche fornito un punto di interesse tramite impostabile il`FocusPointOfInterest` proprietà, in modo che l'utente può toccare per concentrarsi su una determinata area. L'applicazione può inoltre tenere traccia del movimento obiettivo monitorando il `IsAdjustingFocus` proprietà.

Inoltre, limitazione dell'intervallo è stato fornito dal `AutoFocusRangeRestriction` proprietà come:

-   `AVCaptureAutoFocusRangeRestrictionNear` : Consente di limitare il autofocus profondità nelle vicinanze. È utile nelle situazioni, ad esempio la scansione di un codice a matrice o un codice a barre.
-   `AVCaptureAutoFocusRangeRestrictionFar` : Consente di limitare il autofocus profondità distante. Utili in situazioni in cui gli oggetti che sono noti come irrilevante nella visualizzazione del campo (ad esempio, una cornice della finestra).


Infine è disponibile il `SmoothAutoFocus` proprietà che rallenta l'algoritmo di automaticamente lo stato attivo e i passaggi di piccoli incrementi per evitare di spostare gli elementi durante la registrazione video.

### <a name="new-focus-controls-in-ios-8"></a>Nuovi controlli lo stato attivo in iOS 8

Oltre alle funzionalità già fornita da iOS 7 e versioni successive, le funzionalità seguenti sono ora disponibili per controllare lo stato attivo in iOS 8:

-  Controllo completo manuale della posizione lente quando si bloccano lo stato attivo.
-  Osservazione chiave-valore della posizione dell'obiettivo in qualsiasi modalità messa a fuoco.


Per implementare le funzionalità indicate sopra, il `AVCaptureDevice` classe è stata modificata per includere una sola lettura `LensPosition` proprietà utilizzata per ottenere la posizione corrente dell'obiettivo della fotocamera.

Per assumere il controllo manuale della posizione dell'obiettivo, il dispositivo di acquisizione deve essere in modalità di messa a fuoco bloccato. Esempio:

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

Il `SetFocusModeLocked` metodo del dispositivo di acquisizione viene utilizzato per regolare la posizione dell'obiettivo della fotocamera. Per ricevere una notifica quando la modifica abbia effetto, è possibile fornire una routine di callback facoltativo. Esempio:

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

Come illustrato nel codice precedente, è necessario bloccare il dispositivo di acquisizione per la configurazione prima di apportare una modifica della posizione dell'obiettivo. I valori validi della posizione dell'ottica sono compresi tra 0,0 e 1,0.

### <a name="manual-focus-example"></a>Esempio di messa a fuoco manuale

Con il codice di installazione di acquisire AV generale sul posto, un `UIViewController` possono essere aggiunti allo Storyboard dell'applicazione e a cui è configurata come segue:

[![](intro-to-manual-camera-controls-images/image5.png "Un UIViewController possono essere aggiunti alle applicazioni Storyboard e configurate come illustrato di seguito")](intro-to-manual-camera-controls-images/image5.png#lightbox)

La vista contiene gli elementi principali seguenti:

-  Oggetto `UIImageView` che verrà visualizzato il feed video.
-  Oggetto `UISegmentedControl` che verrà modificare la modalità di messa a fuoco da automatico a bloccato.
-  Oggetto `UISlider` che visualizzare e aggiornare la posizione corrente dell'obiettivo.


Eseguire il comando seguente per transito per il controller di visualizzazione per il controllo dello stato attivo manuale:


1. Aggiungere le seguenti istruzioni using:

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
  
1. Aggiungere le seguenti proprietà calcolata:

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
  
1. Eseguire l'override di `ViewDidAppear` (metodo) e aggiungere il comando seguente per avviare la registrazione quando viene caricata la vista:

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
  
1. Con la fotocamera in modalità automatica, il dispositivo di scorrimento verrà spostati automaticamente quando la fotocamera consente di modificare lo stato attivo:

    [![](intro-to-manual-camera-controls-images/image6.png "Il dispositivo di scorrimento verrà spostati automaticamente quando la fotocamera consente di modificare lo stato attivo in questa app di esempio")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. Toccare il segmento bloccato e trascinare il cursore di posizione per regolare la posizione dell'obiettivo manualmente:

    [![](intro-to-manual-camera-controls-images/image7.png "Modificare manualmente la posizione dell'obiettivo")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. Arrestare l'applicazione.


Il codice precedente è stato illustrato come monitorare la posizione dell'obiettivo quando la fotocamera è in modalità automatica o utilizzare un dispositivo di scorrimento per controllare la posizione dell'obiettivo quando è in modalità bloccato.

## <a name="manual-exposure"></a>Esposizione manuale

L'esposizione si intende la luminosità di un'immagine relativo alla luminosità di origine e viene determinato dalla quantità luce raggiunge il sensore, come tempo e il livello di guadagno del sensore (mapping ISO). Fornendo controllare manualmente l'esposizione di dati, un'applicazione può fornire maggiore libertà per l'utente finale e consentire loro di ottenere un aspetto con stili.

Utilizzo dei controlli di esposizione manuale, l'utente può richiedere un'immagine da estremamente chiaro a scuro e moody:

[![](intro-to-manual-camera-controls-images/image8.png "Un esempio di un'immagine che mostra esposizione estremamente chiaro a scuro e moody")](intro-to-manual-camera-controls-images/image8.png#lightbox)

Nuovamente, questa operazione può essere eseguita automaticamente utilizzando il controllo a livello di codice per le applicazioni di scientifiche o tramite i controlli manuali forniti dall'interfaccia utente di applicazioni. In entrambi i casi, nuovo iOS 8 esposizione API offrono un controllo granulare sulle impostazioni di esposizione della camera.

### <a name="how-exposure-works"></a>Funzionamento di esposizione

Prima di esaminare i dettagli per il controllo dell'esposizione in un'applicazione IOS 8. Esaminiamo un rapido funzionamento esposizione:

[![](intro-to-manual-camera-controls-images/image9.png "Funzionamento di esposizione")](intro-to-manual-camera-controls-images/image9.png#lightbox)

I tre elementi di base che collaborano per controllare l'esposizione sono:

-  **Velocità dell'otturatore** – questo è il periodo di tempo che il ridimensionamento è aperto per consentire di luce sul sensore della fotocamera. Il più breve il tempo che di ridimensionamento è aperto, minore è la luce è consentono e l'immagine di nitida è (meno effetto movimento). Più è lunga il ridimensionamento è aperto, consente di più luce in e più di movimento sfocatura che si verifica.
-  **Mapping di ISO** – questo è un termine presa in prestito dalla fotografia film e si intende la sensibilità del film alla luce di prodotti chimici. Valori di bassa ISO in film hanno meno granulosità e la riproduzione dei colori più precisa; valori bassi di ISO su sensori digitali sono meno rumore sensore ma minore luminosità. Maggiore è il valore ISO, luminoso dell'immagine, ma con più rumore sensore. "ISO" su un sensore digitale è una misura di [guadagno electronic](http://en.wikipedia.org/wiki/Gain), non è una funzionalità fisica. 
-  **Apertura dell'obiettivo** : si tratta della dimensione dell'apertura dell'obiettivo. In tutti i dispositivi iOS dell'obiettivo è fissa, in modo che solo due valori che possono essere usati per regolare l'esposizione siano velocità e ISO.


### <a name="how-continuous-auto-exposure-works"></a>Modalità continua automaticamente l'esposizione Works

Prima di apprendimento come funziona l'esposizione manuale, è una buona idea per comprendere come continua esposizione automatica funziona in un dispositivo iOS.

[![](intro-to-manual-camera-controls-images/image10.png "Come funziona l'esposizione continua automaticamente in un dispositivo iOS")](intro-to-manual-camera-controls-images/image10.png#lightbox)

Per primo è il blocco di esposizione automatica, è il processo di calcolo esposizione ideale e continuamente alimentata analisi delle statistiche. Usa queste informazioni per calcolare la combinazione ottimale di ISO e velocità per ottenere la scena anche accesa. Questo ciclo viene considerato il ciclo AE.

### <a name="how-locked-exposure-works"></a>Funzionamento di esposizione come bloccato

Successivamente, si esamineranno bloccato come funziona l'esposizione nei dispositivi iOS.

[![](intro-to-manual-camera-controls-images/image11.png "Come bloccato esposizione funziona in dispositivi iOS")](intro-to-manual-camera-controls-images/image11.png#lightbox)

Nuovamente, è necessario il blocco di esposizione automatica che sta tentando di calcolare i valori di durata e di iOS ottimale. In questa modalità, tuttavia, il blocco AE è disconnesso dal motore di analisi delle statistiche.

### <a name="existing-exposure-controls"></a>Controllo dell'esposizione esistente

iOS 7 e versioni successive, specificare i seguenti controlli di esposizione esistenti tramite la `ExposureMode` proprietà:

-   `AVCaptureExposureModeLocked` : Esempi di scena una sola volta e Usa tali valori in tutta la scena.
-   `AVCaptureExposureModeContinuousAutoExposure` : Esempi di scena in modo continuo per garantire anche è acceso.


Il `ExposurePointOfInterest` può essere usato per toccare per esporre la scena selezionando un oggetto di destinazione espongano e l'applicazione è possibile monitorare il `AdjustingExposure` proprietà per visualizzare quando viene modificata l'esposizione.

### <a name="new-exposure-controls-in-ios-8"></a>Nuovi controlli di esposizione in iOS 8

Oltre alle funzionalità già fornita da iOS 7 e versioni successive, le funzionalità seguenti sono ora disponibili per controllare l'esposizione di iOS 8:

-  Esposizione personalizzato completamente manuale.
-  Get, Set e chiave-valore osservare IOS e velocità (durata).


Per implementare le funzionalità indicate sopra, un nuovo `AVCaptureExposureModeCustom` modalità è stato aggiunto. Quando in camera è la modalità personalizzata, il codice seguente è utilizzabile per regolare la durata dell'esposizione e ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

Nelle modalità Auto e bloccato, l'applicazione può modificare la deviazione della routine esposizione automatico utilizzando il codice seguente:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

Gli intervalli delle impostazioni minime e massime dipendono dal dispositivo che è in esecuzione l'applicazione, pertanto non si devono mai essere hardcoded. Per ottenere gli intervalli di valori minimo e massimo, utilizzare le proprietà seguenti:

-   `CaptureDevice.MinExposureTargetBias` 
-   `CaptureDevice.MaxExposureTargetBias` 
-   `CaptureDevice.ActiveFormat.MinISO` 
-   `CaptureDevice.ActiveFormat.MaxISO` 
-   `CaptureDevice.ActiveFormat.MinExposureDuration` 
-   `CaptureDevice.ActiveFormat.MaxExposureDuration` 


Come illustrato nel codice precedente, è necessario bloccare il dispositivo di acquisizione per la configurazione prima di apportare una modifica nell'esposizione.

### <a name="manual-exposure-example"></a>Esempio di esposizione manuale

Con il codice di installazione di acquisire AV generale sul posto, un `UIViewController` possono essere aggiunti allo Storyboard dell'applicazione e a cui è configurata come segue:

[![](intro-to-manual-camera-controls-images/image12.png "Un UIViewController possono essere aggiunti alle applicazioni Storyboard e configurate come illustrato di seguito")](intro-to-manual-camera-controls-images/image12.png#lightbox)

La vista contiene gli elementi principali seguenti:

-  Oggetto `UIImageView` che verrà visualizzato il feed video.
-  Oggetto `UISegmentedControl` che verrà modificare la modalità di messa a fuoco da automatico a bloccato.
-  Quattro `UISlider` i controlli che verranno visualizzare e aggiornare l'Offset, la durata, ISO e distorsione.


Eseguire il comando seguente per transito per il controller di visualizzazione per il controllo dell'esposizione manuale:


1. Aggiungere le seguenti istruzioni using:
    
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
  
1. Aggiungere le seguenti proprietà calcolata:

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
  
1. Eseguire l'override di `ViewDidAppear` (metodo) e aggiungere il comando seguente per avviare la registrazione quando viene caricata la vista:

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
  
1. Con la fotocamera in modalità Auto, i dispositivi di scorrimento vengono spostati automaticamente quando la fotocamera consente di regolare l'esposizione:

    [![](intro-to-manual-camera-controls-images/image13.png "I dispositivi di scorrimento vengono spostati automaticamente quando la fotocamera consente di regolare l'esposizione")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. Toccare il segmento bloccato e trascinare il cursore di distorsione per regolare manualmente la distorsione dell'esposizione automatico:

    [![](intro-to-manual-camera-controls-images/image14.png "Modificare manualmente la distorsione dell'esposizione automatico")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. Toccare il segmento personalizzato e trascinare i dispositivi di scorrimento per controllare manualmente l'esposizione durata e ISO:

    [![](intro-to-manual-camera-controls-images/image15.png "Trascinare i cursori di durata e ISO manualmente l'esposizione di controllo")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. Arrestare l'applicazione.


Il codice precedente ha illustrato come monitorare le impostazioni di esposizione quando la fotocamera è in modalità automatica e come utilizzare i dispositivi di scorrimento per controllare l'esposizione di dati quando è nelle modalità bloccato o personalizzata.

## <a name="manual-white-balance"></a>Manuale del bianco

Bilanciamento controlli consentono di bilanciare i colosr in un'immagine per renderle più realistica. Sorgenti di luce diversi hanno temperature di colore diverso, ed è necessario modificare le impostazioni di fotocamera usate per acquisire un'immagine per compensare le differenze. Nuovamente, consentendo il controllo utente tramite il bilanciamento del bianco possono apportare modifiche professionale che le routine automatiche sono in grado di ottenere effetti artistici.

[![](intro-to-manual-camera-controls-images/image16.png "Un'immagine di esempio che mostra le regolazioni manuali bilanciamento")](intro-to-manual-camera-controls-images/image16.png#lightbox)

Legale è, ad esempio, un cast rossi, mentre le luci incandescente tungsteno hanno una tonalità per la preparazione, -giallo. (Confusione, "a freddi" colori hanno temperature superiori di colore maggiore dei colori "medio". Colore temperature sono una misura fisica, non uno percettiva).

Mente umana è in grado di compensazione per le differenze di temperatura del colore, ma si tratta di qualcosa che non è possibile eseguire una fotocamera. La fotocamera funziona incrementando colore spettro opposto per regolare le differenze di colore.

Il nuovo iOS 8 API esposizione consente all'applicazione di assumere il controllo del processo e offrono un controllo granulare sulle impostazioni del bianco della camera.

### <a name="how-white-balance-works"></a>Funzionamento del saldo come bianco

Prima di esaminare i dettagli del controllo del bianco in un'applicazione IOS 8. Esaminiamo ora rapido bianco come bilanciare works:

Nello studio di percezione di colore, la [CIE 1931 RGB dei colori spazio e lo spazio colore CIE 1931 XYZ](http://en.wikipedia.org/wiki/CIE_1931_color_space) sono il primo definito matematicamente spazi colore. Sono stati creati dalla Commissione internazionale in illuminazione (CIE) in 1931.

[![](intro-to-manual-camera-controls-images/image17.png "La spazio colori RGB CIE 1931 e CIE 1931 XYZ spazio colore")](intro-to-manual-camera-controls-images/image17.png#lightbox)

Il grafico sopra riportato mostra tutti i colori visibili all'occhio umano, da blu a verde brillante in colore rosso scuro. Qualsiasi punto del diagramma può essere tracciato con un valore di X e Y, come illustrato nel grafico precedente.

Come viene visualizzata nel grafico, sono presenti valori X e Y che possono essere tracciati sul grafico che sarà compreso nell'intervallo della visione risorse umane e di conseguenza questi colori non possono essere riprodotti da una fotocamera.

La curva inferiore nel grafico precedente viene chiamata il [Planckian luogo](http://en.wikipedia.org/wiki/Planckian_locus), che esprime la temperatura di colore (in gradi kelvin), con i valori più alti sul lato blu (hotter) e i numeri inferiori sul lato rosso (spazio). Questi sono utili in situazioni di illuminazione tipico.

In condizioni di illuminazione mista, saranno necessario le regolazioni del bianco deviare dal luogo Planckian per apportare le modifiche necessarie. In questi casi che la regolazione dovrà essere spostato il verde o rosso/magenta CIE sul lato ridimensionare.

i dispositivi iOS compensano tonalità di colore incrementando il miglioramento di colore opposto. Ad esempio, se una scena ha troppo blu, il miglioramento rosso verrà essere incrementato per compensare la modifica. Questi valori sono calibrati per specifici dispositivi in modo che siano dipendenti dal dispositivo di miglioramento.

### <a name="existing-white-balance-controls"></a>Controlli esistenti del bianco

iOS 7 e versioni successive forniti i seguenti controlli bilanciamento esistenti tramite `WhiteBalanceMode` proprietà:

-   `AVCapture WhiteBalance ModeLocked` : Esempi di scena una sola volta e l'utilizzo di tali valori in tutta la scena.
-   `AVCapture WhiteBalance ModeContinuousAutoExposure` : Esempi di scena in modo continuo per garantire che sia ben equilibrato.


E l'applicazione è possibile monitorare il `AdjustingWhiteBalance` proprietà per visualizzare quando viene modificata l'esposizione.

### <a name="new-white-balance-controls-in-ios-8"></a>Nuovi controlli di saldo nero in iOS 8

Oltre alle funzionalità già fornita da iOS 7 e versioni successive, le funzionalità seguenti sono ora disponibili al controllo del bianco in iOS 8:

-  Ottiene il controllo completamente manuale del dispositivo RGB.
-  Get, Set e osservare chiave-valore RGB dispositivo ottiene.
-  Supporto per il bilanciamento con una scheda di grigio.
-  Routine di conversione verso e da spazi colore indipendente dal dispositivo.


Per implementare le funzionalità indicate sopra, il `AVCaptureWhiteBalanceGain` struttura è stata aggiunta con i membri seguenti:

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


Il miglioramento del bianco massimo è quattro (4) e può essere predisposto il `MaxWhiteBalanceGain` proprietà. Pertanto, nell'intervallo valido è da uno (1) `MaxWhiteBalanceGain` (4) attualmente.

Il `DeviceWhiteBalanceGains` proprietà può essere utilizzata per osservare i valori correnti. Utilizzare `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` per regolare il bilanciamento riceve quando la fotocamera è in modalità bilanciamento bianco bloccato.

#### <a name="conversion-routines"></a>Routine di conversione

Routine di conversione sono state aggiunte per iOS 8 per facilitare la conversione verso e da spazi colore indipendente dal dispositivo. Per implementare le routine di conversione, il `AVCaptureWhiteBalanceChromaticityValues` struttura è stata aggiunta con i membri seguenti:

-   `X` -è un valore compreso tra 0 e 1.
-   `Y` -è un valore compreso tra 0 e 1.


Un `AVCaptureWhiteBalanceTemperatureAndTintValues` struttura è stata aggiunta anche con i membri seguenti:

-   `Temperature` -Mobile punto valore in gradi Kelvin.
-   `Tint` -è un offset da verde o magenta da 0 a 150 con i valori positivi verso la direzione di verde e negativo verso il magenta.


Utilizzare il `CaptureDevice.GetTemperatureAndTintValues`e `CaptureDevice.GetDeviceWhiteBalanceGains`metodi per la conversione tra la temperatura e tonalità, cromatiche e RGB ottengono spazi colore.

> [!NOTE]
> Le routine di conversione sono più accurate il valore da convertire è vicino al luogo Planckian.




#### <a name="gray-card-support"></a>Supporto di Smart Card grigio

Apple viene utilizzato il termine grigio World per fare riferimento per il supporto di Smart Card grigio integrato in iOS 8. Consente all'utente di concentrarsi su una scheda fisica di grigio che copre almeno il 50% del centro del frame e utilizza quello per bilanciare i vuoti. Lo scopo della scheda grigio è per ottenere bianco visualizzato neutro.

Questo può essere implementato da chiedere conferma all'utente di inserire una scheda fisica grigio davanti della fotocamera in un'applicazione di monitoraggio di `GrayWorldDeviceWhiteBalanceGains` proprietà e in attesa fino a quando i valori pagare gli verso il basso.

L'applicazione sarebbe quindi bloccare il miglioramento del bianco per la `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` metodo utilizzando i valori di `GrayWorldDeviceWhiteBalanceGains` proprietà a cui applicare le modifiche.

Il dispositivo di acquisizione deve essere bloccato per la configurazione prima di apportare una modifica nel bilanciamento.

### <a name="manual-white-balance-example"></a>Esempio manuale del bianco

Con il codice di installazione di acquisire AV generale sul posto, un `UIViewController` possono essere aggiunti allo Storyboard dell'applicazione e a cui è configurata come segue:

[![](intro-to-manual-camera-controls-images/image18.png "Un UIViewController possono essere aggiunti alle applicazioni Storyboard e configurate come illustrato di seguito")](intro-to-manual-camera-controls-images/image18.png#lightbox)

La vista contiene gli elementi principali seguenti:

-  Oggetto `UIImageView` che verrà visualizzato il feed video.
-  Oggetto `UISegmentedControl` che verrà modificare la modalità di messa a fuoco da automatico a bloccato.
-  Due `UISlider` i controlli che verranno visualizzare e aggiornare la temperatura e tonalità.
-  Oggetto `UIButton` utilizzato per uno spazio di Smart Card grigio (grigio World) di esempio e impostare il bilanciamento utilizzando tali valori.


Eseguire il comando seguente per transito per il controller di visualizzazione per il controllo di saldi vuoti manuale:


1. Aggiungere le seguenti istruzioni using:

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
  
1. Aggiungere le seguenti proprietà calcolata:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Aggiungere il metodo privato seguente per impostare il nuovo white bilanciare temperatura e Tint:

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
  
1. Eseguire l'override di `ViewDidAppear` (metodo) e aggiungere il comando seguente per avviare la registrazione quando viene caricata la vista:

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
1. Con la fotocamera in modalità Auto, i dispositivi di scorrimento vengono spostati automaticamente quando la fotocamera regola bilanciamento bianco:

    [![](intro-to-manual-camera-controls-images/image19.png "I dispositivi di scorrimento vengono spostati automaticamente quando la fotocamera regola di bilanciamento del bianco")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. Toccare il segmento bloccato e trascinare i dispositivi di scorrimento per regolare manualmente il saldo bianco Temp e Tint:

    [![](intro-to-manual-camera-controls-images/image20.png "Trascinare i dispositivi di scorrimento per regolare manualmente il saldo bianco Temp e Tint")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. Con il segmento bloccato ancora selezionato, posizionare una scheda fisica grigio in primo piano della fotocamera e toccare il pulsante di Smart Card grigio per regolare del bianco al mondo grigio:

    [![](intro-to-manual-camera-controls-images/image21.png "Toccare il pulsante di Smart Card grigio per regolare del bianco al mondo grigio")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. Arrestare l'applicazione.

Il codice precedente è stato illustrato come monitorare le impostazioni del bianco quando la fotocamera è in modalità automatica oppure utilizzare dispositivi di scorrimento per controllare il saldo bianco quando è in modalità bloccato.

## <a name="bracketed-capture"></a>Acquisizione tra parentesi

Racchiusi tra parentesi quadre di acquisizione è basato sulle impostazioni dai controlli fotocamera manuale presentato in precedenza e consente all'applicazione di acquisire un determinato momento, in molti modi diversi.

Per acquisire racchiusi tra parentesi quadre è un burst di immagini scattate con una serie di impostazioni dall'immagine in immagine.

[![](intro-to-manual-camera-controls-images/image22.png "Funzionamento di acquisire racchiusi tra parentesi quadre")](intro-to-manual-camera-controls-images/image22.png#lightbox)

Utilizza racchiusi tra parentesi quadre di acquisizione in iOS 8, un'applicazione può predefinito di una serie di controlli della fotocamera manuale, eseguire un unico comando e avere la scena corrente restituire una serie di immagini per ognuno dei set di impostazioni manuali.

### <a name="bracketed-capture-basics"></a>Nozioni fondamentali di acquisizione tra parentesi

Nuovamente, racchiusi tra parentesi quadre Capture è un burst di immagini scattate con impostazioni diverse dall'immagine in immagine. I tipi di racchiusi tra parentesi quadre Capture disponibili sono:

-  **Auto tra parentesi quadre esposizione** , in cui tutte le immagini presentano una quantità diverse di distorsione.
-  **Parentesi esposizione manuale** , in cui tutte le immagini presentano un diverse velocità (durata) e ISO quantità.
-  **Tra parentesi quadre potenziamento semplice** : una serie di immagini fisse ottenute in rapida successione.


### <a name="new-bracketed-capture-controls-in-ios-8"></a>Nuovo racchiusi tra parentesi quadre acquisire i controlli in iOS 8

Tutti i comandi di acquisire racchiusi tra parentesi quadre sono implementati nel `AVCaptureStillImageOutput` classe. Utilizzare il `CaptureStillImageBracket`metodo per ottenere una serie di immagini con la matrice di impostazioni.

Due nuove classi sono state implementate per gestire le impostazioni:

-   `AVCaptureAutoExposureBracketedStillImageSettings` – Contiene una proprietà, `ExposureTargetBias`, utilizzato per impostare la deviazione di una parentesi di esposizione automatica. 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings` – Presenta due proprietà, `ExposureDuration` e `ISO`, utilizzato per impostare la velocità e ISO di una parentesi di esposizione manuale. 


### <a name="bracketed-capture-controls-dos-and-donts"></a>Acquisizione tra parentesi quadre controlla-azioni consentite e non consentite

#### <a name="dos"></a>Operazioni

Di seguito è riportato un elenco di elementi che deve essere eseguita quando si utilizza Capture racchiusi tra parentesi quadre i controlli in iOS 8:

-  Preparare l'applicazione per la situazione peggiore acquisizione chiamando il `PrepareToCaptureStillImageBracket` metodo.
-  Si supponga che i buffer di esempio intende provenire dallo stesso pool condiviso.
-  Per rilasciare la memoria allocata da una precedente chiamata di preparazione, chiamare `PrepareToCaptureStillImageBracket` nuovamente e inviarlo a una matrice di un oggetto.


#### <a name="donts"></a>Non consentite

Di seguito è riportato un elenco di elementi che non deve essere eseguita quando si utilizza Capture racchiusi tra parentesi quadre i controlli in iOS 8:

-  Non mescolare i tipi di impostazioni racchiusi tra parentesi quadre acquisire in una singola acquisizione.
-  Non richiedere più di `MaxBracketedCaptureStillImageCount` immagini in una singola acquisizione.


### <a name="bracketed-capture-details"></a>Dettagli di acquisizione tra parentesi

I dettagli seguenti devono essere prese in considerazione quando si lavora con racchiusi tra parentesi quadre Capture in iOS 8:

-  Sostituiscono temporanea delle impostazioni tra parentesi il `AVCaptureDevice` impostazioni.
-  Flash e ancora immagine stabilizzazione vengono ignorate.
-  Tutte le immagini devono usare lo stesso formato di output (jpeg, png e così via)
-  Anteprima video può eliminare i frame.
-  Acquisizione tra parentesi è supportato in tutti i dispositivi compatibili con iOS 8.


Con queste informazioni in considerazione, diamo un'occhiata a un esempio di utilizzo racchiusi tra parentesi quadre acquisire in iOS 8.

### <a name="bracket-capture-example"></a>Esempio di acquisizione tra parentesi quadre

Con il codice di installazione di acquisire AV generale sul posto, un `UIViewController` possono essere aggiunti allo Storyboard dell'applicazione e a cui è configurata come segue:

[![](intro-to-manual-camera-controls-images/image23.png "Un UIViewController possono essere aggiunti alle applicazioni Storyboard e configurate come illustrato di seguito")](intro-to-manual-camera-controls-images/image23.png#lightbox)

La vista contiene gli elementi principali seguenti:

-  Oggetto `UIImageView` che verrà visualizzato il feed video.
-  Tre `UIImageViews` che visualizzerà i risultati dell'acquisizione.
-  Oggetto `UIScrollView` per ospitare il feed video e visualizzazioni dei risultati.
-  Oggetto `UIButton` consentono di acquisire un racchiusi tra parentesi quadre con alcune impostazioni predefinite.


Eseguire il comando seguente per transito per il controller di visualizzazione per acquisire racchiusi tra parentesi quadre:


1. Aggiungere le seguenti istruzioni using:

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
  
1. Aggiungere le seguenti proprietà calcolata:

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
1. Frame una scena e toccare il pulsante di acquisizione tra parentesi quadre:

    [![](intro-to-manual-camera-controls-images/image24.png "Frame una scena e toccare il pulsante di acquisizione tra parentesi quadre")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. Scorri rapidamente verso destra a sinistra per visualizzare le tre immagini scattate da Capture racchiusi tra parentesi quadre:

    [![](intro-to-manual-camera-controls-images/image25.png "Scorri rapidamente verso destra a sinistra per visualizzare le tre immagini adottate racchiusi tra parentesi quadre di acquisizione")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. Arrestare l'applicazione.


Il codice precedente è stato illustrato come configurare ed eseguire un'esposizione automaticamente racchiusi tra parentesi quadre acquisire in iOS 8.

## <a name="summary"></a>Riepilogo

In questo articolo abbiamo coperto un'introduzione ai nuovi controlli fotocamera manuale fornito da iOS 8 e analizzate le nozioni di base di cosa fanno e come funzionano. Esempi di messa a fuoco manuale, esposizione manuale e bilanciamento manuale attuale è disponibile. Infine, è stato assegnato un esempio di creare un racchiusi tra parentesi quadre Capture utilizzando i controlli di fotocamera manuale descritta in precedenza

## <a name="related-links"></a>Collegamenti correlati

- [ManualCameraControls (esempio)](https://developer.xamarin.com/samples/monotouch/ManualCameraControls)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
