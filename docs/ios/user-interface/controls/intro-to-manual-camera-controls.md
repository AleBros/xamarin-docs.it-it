---
title: Controlli manuali della fotocamera in Xamarin.iOS
description: Questo documento descrive come è possibile usare il Framework AVFoundation iOS con Xamarin.iOS per abilitare i controlli della fotocamera manuale. I controlli della fotocamera manuale consentono a un utente di controllare lo stato attivo, il saldo bianco e le impostazioni di esposizione.
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 1ae7e4c39a07ccfcb472f7add580bf5109166c3d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022052"
---
# <a name="manual-camera-controls-in-xamarinios"></a>Controlli manuali della fotocamera in Xamarin.iOS

I controlli della fotocamera manuale, forniti dal `AVFoundation Framework` in iOS 8, consentono a un'applicazione per dispositivi mobili di assumere il controllo completo sulla fotocamera di un dispositivo iOS. Questo livello di controllo con granularità fine può essere usato per creare applicazioni per la fotocamera a livello professionale e fornire composizioni degli artisti modificando i parametri della fotocamera durante l'acquisizione di un'immagine o un video ancora.

Questi controlli possono essere utili anche durante lo sviluppo di applicazioni scientifiche o industriali, in cui i risultati sono meno rivolti alla correttezza o alla bellezza dell'immagine e sono rivolti all'evidenziazione di una funzionalità o di un elemento dell'immagine eseguita.

## <a name="avfoundation-capture-objects"></a>Oggetti di acquisizione AVFoundation

Sia che si tratti di immagini video o ancora che usano la fotocamera in un dispositivo iOS, il processo usato per acquisire tali immagini è sostanzialmente lo stesso. Questo vale per le applicazioni che usano i controlli predefiniti della fotocamera automatizzata o quelli che sfruttano i nuovi controlli manuali della fotocamera:

 [![](intro-to-manual-camera-controls-images/image1.png "AVFoundation Capture Objects overview")](intro-to-manual-camera-controls-images/image1.png#lightbox)

L'input viene tratto da un `AVCaptureDeviceInput` in un `AVCaptureSession` tramite un `AVCaptureConnection`. Il risultato viene restituito come immagine ancora o come flusso video. L'intero processo è controllato da un `AVCaptureDevice`.

## <a name="manual-controls-provided"></a>Controlli manuali forniti

Usando le nuove API fornite da iOS 8, l'applicazione può assumere il controllo delle funzionalità della fotocamera seguenti:

- **Messa a fuoco manuale** : consentendo all'utente finale di assumere il controllo diretto dello stato attivo, un'applicazione può fornire maggiore controllo sull'immagine eseguita.
- **Esposizione manuale** : grazie al controllo manuale sull'esposizione, un'applicazione può offrire maggiore libertà agli utenti e consentire loro di ottenere un aspetto stilizzato.
- **Bilanciamento bianco manuale** : il bilanciamento bianco viene usato per modificare il colore in un'immagine, spesso per renderlo realistico. Diverse fonti di luce hanno temperature di colore diverse e le impostazioni della fotocamera usate per acquisire un'immagine vengono modificate per compensare tali differenze. Anche in questo caso, consentendo al controllo utente sul bilanciamento del bianco, gli utenti possono apportare modifiche che non possono essere eseguite automaticamente.

iOS 8 fornisce estensioni e miglioramenti alle API iOS esistenti per fornire questo controllo con granularità fine sul processo di acquisizione delle immagini.

## <a name="bracketed-capture"></a>Acquisizione tra parentesi quadre

L'acquisizione tra parentesi quadre si basa sulle impostazioni dei controlli della fotocamera manuali presentati sopra e consente all'applicazione di acquisire un attimo di tempo, in diversi modi.

In poche parole, l'acquisizione tra parentesi quadre è un impeto di immagini ancora acquisite con un'ampia gamma di impostazioni, dall'immagine all'immagine.

## <a name="requirements"></a>Requisiti

Per completare i passaggi illustrati in questo articolo, sono necessari gli elementi seguenti:

- **Xcode 7 + e iOS 8 o versioni successive** : è necessario installare e configurare le API di Apple Xcode 7 e iOS 8 o versioni successive nel computer dello sviluppatore.
- **Visual Studio per Mac** : la versione più recente di Visual Studio per Mac deve essere installata e configurata nel dispositivo utente.
- **dispositivo iOS 8** : un dispositivo iOS che esegue la versione più recente di iOS 8. Non è possibile testare le funzionalità della fotocamera nel simulatore iOS.

## <a name="general-av-capture-setup"></a>Configurazione di acquisizione AV generale

Quando si registra un video in un dispositivo iOS, è sempre necessario un codice di installazione generale. In questa sezione viene illustrata la configurazione minima necessaria per registrare video dalla fotocamera del dispositivo iOS e visualizzare il video in tempo reale in un `UIImageView`.

### <a name="output-sample-buffer-delegate"></a>Delegato buffer di esempio di output

Uno dei primi elementi necessari sarà un delegato per monitorare il buffer di output di esempio e visualizzare un'immagine catturata dal buffer a una `UIImageView` nell'interfaccia utente dell'applicazione.

Nella routine seguente viene monitorato il buffer di esempio e viene aggiornata l'interfaccia utente:

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

Con questa routine, il `AppDelegate` può essere modificato per aprire una sessione di acquisizione AV per registrare un feed video live.

### <a name="creating-an-av-capture-session"></a>Creazione di una sessione di acquisizione AV

La sessione di acquisizione AV viene usata per controllare la registrazione di video live dalla fotocamera del dispositivo iOS ed è necessario per ottenere video in un'applicazione iOS. Poiché l'esempio `ManualCameraControl` applicazione di esempio sta usando la sessione di acquisizione in diversi punti, verrà configurato nel `AppDelegate` e reso disponibile per l'intera applicazione.

Per modificare la `AppDelegate` dell'applicazione e aggiungere il codice necessario, eseguire le operazioni seguenti:

1. Fare doppio clic sul file `AppDelegate.cs` nel Esplora soluzioni per aprirlo per la modifica.
1. Aggiungere le istruzioni using seguenti all'inizio del file:

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
    ```

1. Aggiungere le variabili private e le proprietà calcolate seguenti alla classe `AppDelegate`:

    ```csharp
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

1. Eseguire l'override del metodo finished e modificarlo in:

    ```csharp
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

Con questo codice, i controlli della fotocamera manuale possono essere implementati facilmente per la sperimentazione e il test.

## <a name="manual-focus"></a>Stato attivo manuale

Consentendo all'utente finale di assumere direttamente i controlli dello stato attivo, un'applicazione può fornire un controllo più artistico sull'immagine eseguita.

Un fotografo professionale può, ad esempio, attenuare l'attenzione di un'immagine per ottenere un [effetto bokeh](https://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "A Bokeh Effect")](intro-to-manual-camera-controls-images/image2.png#lightbox)

In alternativa, creare un [effetto pull attivo](http://www.mediacollege.com/video/camera/focus/pull.html), ad esempio:

[![](intro-to-manual-camera-controls-images/image3.png "The Focus Pull Effect")](intro-to-manual-camera-controls-images/image3.png#lightbox)

Per gli scienziati o per un writer di applicazioni mediche, l'applicazione potrebbe voler spostare a livello di codice l'obiettivo per gli esperimenti. In entrambi i casi, la nuova API consente all'utente finale o all'applicazione di assumere il controllo dello stato attivo nel momento in cui viene acquisita l'immagine.

### <a name="how-focus-works"></a>Funzionamento dello stato attivo

Prima di esaminare i dettagli del controllo dello stato attivo in un'applicazione IOS 8. Esaminiamo brevemente il funzionamento dello stato attivo in un dispositivo iOS:

[![](intro-to-manual-camera-controls-images/image4.png "How focus works in an iOS device")](intro-to-manual-camera-controls-images/image4.png#lightbox)

Light passa alla fotocamera sul dispositivo iOS e si concentra su un sensore di immagine. La distanza della lente dal sensore controlla il punto in cui il punto focale (l'area in cui l'immagine verrà visualizzata più nitida) è, in relazione al sensore. Più lontano è la lente dal sensore, gli oggetti distance sembrano più nitidi e gli oggetti Near più vicini sembrano più nitidi.

In un dispositivo iOS, l'obiettivo viene spostato più vicino al sensore da magneti e molle. Di conseguenza, il posizionamento esatto della lente è impossibile, in quanto varia da dispositivo a dispositivo e può essere influenzato da parametri come l'orientamento del dispositivo o l'età del dispositivo e della molla.

### <a name="important-focus-terms"></a>Principali condizioni di interesse

Quando si ha a che fare con lo stato attivo, è necessario che lo sviluppatore abbia familiarità con quanto segue:

- **Depth of Field** : distanza tra gli oggetti con stato attivo più vicini e più lontani.
- **Macro** : si tratta dell'estremità vicina dello spettro di interesse ed è la distanza più vicina in cui l'obiettivo può essere attivo.
- **Infinity** : si tratta dell'estremità finale dello spettro di interesse ed è la distanza più lontana in cui l'obiettivo può essere attivo.
- **Distanza iperfocale** : si tratta del punto nello spettro di interesse in cui l'oggetto più lontano nel frame è solo alla fine dello stato attivo. In altre parole, si tratta della posizione focale che massimizza la profondità del campo.
- **Posizione della lente** : questa è la regola che controlla tutti gli altri termini precedenti. Si tratta della distanza dell'obiettivo dal sensore e quindi del controller dello stato attivo.

Con questi termini e conoscenze, i nuovi controlli di messa a fuoco manuali possono essere implementati correttamente in un'applicazione iOS 8.

### <a name="existing-focus-controls"></a>Controlli di stato attivo esistenti

iOS 7 e versioni precedenti forniscono i controlli di stato attivo esistenti tramite `FocusMode`proprietà come:

- `AVCaptureFocusModeLocked`: lo stato attivo è bloccato in un singolo punto di messa a fuoco.
- `AVCaptureFocusModeAutoFocus`: la fotocamera consente di spazzare l'obiettivo attraverso tutti i punti focali fino a quando non trova lo stato attivo e quindi rimane disponibile.
- `AVCaptureFocusModeContinuousAutoFocus`: la fotocamera viene riattivata ogni volta che viene rilevata una condizione di sfocatura.

I controlli esistenti forniscono anche un punto di interesse impostabile tramite la proprietà`FocusPointOfInterest`, in modo che l'utente possa toccare per concentrarsi su un'area specifica. L'applicazione può anche tenere traccia dello spostamento dell'obiettivo monitorando la proprietà `IsAdjustingFocus`.

Inoltre, la restrizione di intervallo è stata fornita dalla proprietà `AutoFocusRangeRestriction` come:

- `AVCaptureAutoFocusRangeRestrictionNear`: limita lo stato attivo alla profondità vicina. Utile in situazioni come la scansione di un codice a matrice o codice a barre.
- `AVCaptureAutoFocusRangeRestrictionFar`: limita la messa a fuoco automatica a profondità distanti. Utile nelle situazioni in cui gli oggetti noti come irrilevanti sono nel campo di visualizzazione, ad esempio una cornice della finestra.

Infine, è presente la proprietà `SmoothAutoFocus` che rallenta l'algoritmo di messa a fuoco automatica e ne esegue il passaggio con incrementi più piccoli per evitare di spostare gli artefatti durante la registrazione del video.

### <a name="new-focus-controls-in-ios-8"></a>Nuovi controlli di stato attivo in iOS 8

Oltre alle funzionalità già fornite da iOS 7 e versioni successive, sono ora disponibili le funzionalità seguenti per controllare lo stato attivo in iOS 8:

- Controllo manuale completo della posizione dell'obiettivo quando si blocca lo stato attivo.
- Osservazione chiave-valore della posizione della lente in qualsiasi modalità messa a fuoco.

Per implementare le funzionalità sopra indicate, la classe `AVCaptureDevice` è stata modificata in modo da includere una proprietà di `LensPosition` di sola lettura usata per ottenere la posizione corrente dell'obiettivo della fotocamera.

Per assumere il controllo manuale della posizione dell'obiettivo, è necessario che il dispositivo di acquisizione sia in modalità messa a fuoco bloccata. Esempio:

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

Il metodo `SetFocusModeLocked` del dispositivo di acquisizione viene usato per modificare la posizione dell'obiettivo della fotocamera. Una routine di callback facoltativa può essere fornita per ricevere una notifica quando la modifica ha effetto. Esempio:

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

Come illustrato nel codice precedente, il dispositivo di acquisizione deve essere bloccato per la configurazione prima che sia possibile apportare una modifica alla posizione dell'obiettivo. I valori di posizione della lente validi sono compresi tra 0,0 e 1,0.

### <a name="manual-focus-example"></a>Esempio di stato attivo manuale

Con il codice di installazione di acquisizione AV generale, è possibile aggiungere un `UIViewController` allo storyboard dell'applicazione e configurarlo come segue:

[![](intro-to-manual-camera-controls-images/image5.png "A UIViewController can be added to the applications Storyboard and configured as shown here")](intro-to-manual-camera-controls-images/image5.png#lightbox)

La vista contiene gli elementi principali seguenti:

- `UIImageView` che visualizzerà il feed video.
- `UISegmentedControl` che cambierà la modalità messa a fuoco da automatica a bloccata.
- `UISlider` che visualizzerà e aggiornerà la posizione corrente dell'obiettivo.

Eseguire le operazioni seguenti per collegare il controller di visualizzazione per il controllo dello stato attivo manuale:

1. Aggiungere le istruzioni using seguenti:

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

1. Aggiungere le variabili private seguenti:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```

1. Aggiungere le proprietà calcolate seguenti:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. Eseguire l'override del metodo `ViewDidLoad` e aggiungere il codice seguente:

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

1. Eseguire l'override del metodo `ViewDidAppear` e aggiungere quanto segue per avviare la registrazione quando viene caricata la vista:

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

1. Con la fotocamera in modalità auto, il dispositivo di scorrimento verrà spostato automaticamente mentre la fotocamera regola lo stato attivo:

    [![](intro-to-manual-camera-controls-images/image6.png "The slider will move automatically as the camera adjusts focus in this sample app")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. Toccare il segmento bloccato e trascinare il dispositivo di scorrimento posizione per modificare la posizione dell'obiettivo manualmente:

    [![](intro-to-manual-camera-controls-images/image7.png "Manually adjusting the lens position")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. Arrestare l'applicazione.

Il codice precedente ha illustrato come monitorare la posizione dell'obiettivo quando la fotocamera è in modalità automatica o usare un dispositivo di scorrimento per controllare la posizione dell'obiettivo quando è in modalità di blocco.

## <a name="manual-exposure"></a>Esposizione manuale

L'esposizione si riferisce alla luminosità di un'immagine rispetto alla luminosità di origine ed è determinata dalla quantità di luce che raggiunge il sensore, per quanto tempo e per il livello di guadagno del sensore (mapping ISO). Fornendo un controllo manuale sull'esposizione, un'applicazione può offrire maggiore libertà all'utente finale e consentire loro di ottenere un aspetto stilizzato.

Usando i controlli di esposizione manuali, l'utente può scattare un'immagine da un modo non realistico a quello oscuro e lunatico:

[![](intro-to-manual-camera-controls-images/image8.png "A sample an image showing exposure from unrealistically bright to dark and moody")](intro-to-manual-camera-controls-images/image8.png#lightbox)

Questa operazione può essere eseguita automaticamente tramite il controllo a livello di codice per le applicazioni scientifiche o tramite controlli manuali forniti dall'interfaccia utente delle applicazioni. In entrambi i casi, le nuove API di esposizione iOS 8 forniscono un controllo granulare sulle impostazioni di esposizione della fotocamera.

### <a name="how-exposure-works"></a>Funzionamento dell'esposizione

Prima di esaminare i dettagli del controllo dell'esposizione in un'applicazione IOS 8. Esaminiamo brevemente il funzionamento dell'esposizione:

[![](intro-to-manual-camera-controls-images/image9.png "How exposure works")](intro-to-manual-camera-controls-images/image9.png#lightbox)

I tre elementi di base che si uniscono per controllare l'esposizione sono:

- **Velocità dell'otturatore** : indica il periodo di tempo durante il quale l'otturatore è aperto per consentire la luce sul sensore della fotocamera. Più breve è il tempo di apertura dell'otturatore, minore è la luce e l'immagine è più nitida (meno sfocatura di movimento). Più a lungo l'otturatore è aperto, maggiore è la luce e maggiore è la sfocatura di movimento che si verifica.
- **Mapping ISO** : si tratta di un termine preso in prestito dalla fotografia del film e si riferisce alla sensibilità delle sostanze chimiche nel film alla luce. I valori ISO bassi nella pellicola hanno una riproduzione a colori meno granulare e più fine; i valori ISO bassi sui sensori digitali hanno meno rumore del sensore ma meno luminosità. Maggiore è il valore ISO, più luminoso è l'immagine, ma con maggiore rumore del sensore. "ISO" su un sensore digitale è una misura del [guadagno elettronico](https://en.wikipedia.org/wiki/Gain), non una funzionalità fisica.
- **Apertura** della lente: questa è la dimensione dell'apertura dell'obiettivo. In tutti i dispositivi iOS l'apertura della lente è fissa, quindi gli unici due valori che è possibile usare per regolare l'esposizione sono la velocità dell'otturatore e l'ISO.

### <a name="how-continuous-auto-exposure-works"></a>Funzionamento dell'esposizione automatica continua

Prima di apprendere come funziona l'esposizione manuale, è consigliabile comprendere il funzionamento dell'esposizione automatica continua in un dispositivo iOS.

[![](intro-to-manual-camera-controls-images/image10.png "How continuous auto exposure works in an iOS device")](intro-to-manual-camera-controls-images/image10.png#lightbox)

Il primo è il blocco di esposizione automatica, che ha il compito di calcolare l'esposizione ideale ed è costantemente alimentato statistiche di misurazione. Usa queste informazioni per calcolare la combinazione ottimale di ISO e velocità dell'otturatore per ottenere la scena ben illuminata. Questo ciclo viene definito ciclo AE.

### <a name="how-locked-exposure-works"></a>Funzionamento dell'esposizione bloccata

Verrà ora esaminata la modalità di funzionamento dell'esposizione bloccata nei dispositivi iOS.

[![](intro-to-manual-camera-controls-images/image11.png "How locked exposure works in iOS devices")](intro-to-manual-camera-controls-images/image11.png#lightbox)

Anche in questo caso, il blocco di esposizione automatico tenta di calcolare i valori di durata e di iOS ottimali. Tuttavia, in questa modalità il blocco AE è disconnesso dal motore delle statistiche di misurazione.

### <a name="existing-exposure-controls"></a>Controlli di esposizione esistenti

iOS 7 e versioni successive forniscono i controlli di esposizione esistenti seguenti tramite la proprietà `ExposureMode`:

- `AVCaptureExposureModeLocked`: esegue il campionamento della scena una volta e usa tali valori in tutta la scena.
- `AVCaptureExposureModeContinuousAutoExposure`: esegue il campionamento continuo della scena per assicurarsi che sia ben illuminata.

Il `ExposurePointOfInterest` può essere usato per toccare per esporre la scena selezionando un oggetto di destinazione su cui esporre il e l'applicazione può monitorare la proprietà `AdjustingExposure` per vedere quando è in corso la regolazione dell'esposizione.

### <a name="new-exposure-controls-in-ios-8"></a>Nuovi controlli di esposizione in iOS 8

Oltre alle funzionalità già fornite da iOS 7 e versioni successive, sono ora disponibili le funzionalità seguenti per controllare l'esposizione in iOS 8:

- Esposizione personalizzata completamente manuale.
- Get, set e Key-Value osservino IOS e la velocità dell'otturatore (durata).

Per implementare le funzionalità sopra riportate, è stata aggiunta una nuova modalità `AVCaptureExposureModeCustom`. Quando la fotocamera in è la modalità personalizzata, è possibile usare il codice seguente per modificare la durata dell'esposizione e l'ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

In modalità auto e bloccate, l'applicazione può regolare la distorsione della routine di esposizione automatica usando il codice seguente:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

Gli intervalli di impostazioni minime e massime dipendono dal dispositivo in cui è in esecuzione l'applicazione, quindi non dovrebbero mai essere hardcoded. Usare invece le proprietà seguenti per ottenere gli intervalli di valori minimo e massimo:

- `CaptureDevice.MinExposureTargetBias`
- `CaptureDevice.MaxExposureTargetBias`
- `CaptureDevice.ActiveFormat.MinISO`
- `CaptureDevice.ActiveFormat.MaxISO`
- `CaptureDevice.ActiveFormat.MinExposureDuration`
- `CaptureDevice.ActiveFormat.MaxExposureDuration`

Come illustrato nel codice precedente, il dispositivo di acquisizione deve essere bloccato per la configurazione prima che possa essere apportata una modifica all'esposizione.

### <a name="manual-exposure-example"></a>Esempio di esposizione manuale

Con il codice di installazione di acquisizione AV generale, è possibile aggiungere un `UIViewController` allo storyboard dell'applicazione e configurarlo come segue:

[![](intro-to-manual-camera-controls-images/image12.png "A UIViewController can be added to the applications Storyboard and configured as shown here")](intro-to-manual-camera-controls-images/image12.png#lightbox)

La vista contiene gli elementi principali seguenti:

- `UIImageView` che visualizzerà il feed video.
- `UISegmentedControl` che cambierà la modalità messa a fuoco da automatica a bloccata.
- Quattro `UISlider` controlli che visualizzano e aggiornano offset, durata, ISO e bias.

Per collegare il controller di visualizzazione per il controllo dell'esposizione manuale, eseguire le operazioni seguenti:

1. Aggiungere le istruzioni using seguenti:

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

1. Aggiungere le variabili private seguenti:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```

1. Aggiungere le proprietà calcolate seguenti:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. Eseguire l'override del metodo `ViewDidLoad` e aggiungere il codice seguente:

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

1. Eseguire l'override del metodo `ViewDidAppear` e aggiungere quanto segue per avviare la registrazione quando viene caricata la vista:

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

1. Con la fotocamera in modalità auto, i dispositivi di scorrimento vengono spostati automaticamente quando la fotocamera regola l'esposizione:

    [![](intro-to-manual-camera-controls-images/image13.png "The sliders will move automatically as the camera adjusts exposure")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. Toccare il segmento bloccato e trascinare il dispositivo di scorrimento della distorsione per regolare la distorsione dell'esposizione automatica manualmente:

    [![](intro-to-manual-camera-controls-images/image14.png "Adjusting the bias of the automatic exposure manually")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. Toccare il segmento Custom e trascinare i dispositivi di scorrimento Duration e ISO per controllare manualmente l'esposizione:

    [![](intro-to-manual-camera-controls-images/image15.png "Drag the Duration and ISO sliders to manually control exposure")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. Arrestare l'applicazione.

Il codice precedente ha illustrato come monitorare le impostazioni di esposizione quando la fotocamera è in modalità automatica e come usare i dispositivi di scorrimento per controllare l'esposizione quando è in modalità bloccata o personalizzata.

## <a name="manual-white-balance"></a>Bilanciamento bianco manuale

I controlli di bilanciamento del bianco consentono agli utenti di modificare il saldo di colosr in un'immagine per renderli più realistici. Diverse fonti di luce hanno temperature di colore diverse e le impostazioni della fotocamera usate per acquisire un'immagine devono essere modificate per compensare tali differenze. Anche in questo caso, consentendo al controllo utente sul bilanciamento del bianco, è possibile apportare modifiche professionali che le routine automatiche non sono in grado di ottenere effetti artistici.

[![](intro-to-manual-camera-controls-images/image16.png "A sample image showing Manual White Balance adjustments")](intro-to-manual-camera-controls-images/image16.png#lightbox)

Ad esempio, l'ora legale ha un cast bluastro, mentre le luci incandescenti in tungsteno hanno una tinta più calda, gialla arancione. In confusione, i colori "cool" hanno temperature di colore maggiori rispetto ai colori "warm". Le temperature di colore sono una misura fisica, non una "percettiva".

La mente umana è molto utile per compensare le differenze nella temperatura del colore, ma questo è un elemento che non può essere fatto da una fotocamera. La fotocamera funziona aumentando il colore sullo spettro opposto per adattarsi alle differenze di colore.

La nuova API di esposizione iOS 8 consente all'applicazione di assumere il controllo del processo e fornire un controllo con granularità fine sulle impostazioni di bilanciamento del bianco della fotocamera.

### <a name="how-white-balance-works"></a>Funzionamento del bilanciamento del bianco

Prima di esaminare i dettagli del controllo del saldo bianco in un'applicazione IOS 8. Esaminiamo brevemente il funzionamento del bilanciamento del bianco:

Nello studio della percezione dei colori, lo spazio dei colori di [cie 1931 RGB e cie 1931 XYZ](https://en.wikipedia.org/wiki/CIE_1931_color_space) sono i primi spazi dei colori definiti matematicamente. Sono stati creati da International Commission on illuminazione (CIE) in 1931.

[![](intro-to-manual-camera-controls-images/image17.png "The CIE 1931 RGB color space and CIE 1931 XYZ color space")](intro-to-manual-camera-controls-images/image17.png#lightbox)

Il grafico sopra riportato Mostra tutti i colori visibili all'occhio umano, dal blu intenso al verde chiaro al rosso chiaro. Qualsiasi punto nel diagramma può essere tracciato con un valore X e Y, come illustrato nel grafico precedente.

Come visibile nel grafico, sono presenti valori X e Y che possono essere tracciati sul grafico che non rientrano nell'intervallo della visione umana e, di conseguenza, questi colori non possono essere riprodotti da una fotocamera.

La curva più piccola nel grafico precedente è denominata [locus planckiano](https://en.wikipedia.org/wiki/Planckian_locus), che esprime la temperatura di colore (in gradi Kelvin), con numeri più alti sul lato blu (più caldo) e numeri più bassi sul lato rosso (raffreddamento). Sono utili per le situazioni di illuminazione tipiche.

In condizioni di illuminazione mista, le rettifiche di bilanciamento del bianco dovranno deviare dal locus planckiano per apportare le modifiche necessarie. In queste situazioni è necessario spostare la regolazione sul lato verde o rosso/magenta della scala CIE.

i dispositivi iOS compensano i cast dei colori aumentando il guadagno del colore opposto. Ad esempio, se una scena è troppo blu, il guadagno rosso verrà incrementato per compensare. Questi valori di guadagno vengono calibrati per dispositivi specifici, in modo che siano dipendenti dal dispositivo.

### <a name="existing-white-balance-controls"></a>Controlli di bilanciamento del bianco esistenti

iOS 7 e versioni successive hanno fornito i controlli di bilanciamento del bianco esistenti seguenti tramite `WhiteBalanceMode` proprietà:

- `AVCapture WhiteBalance ModeLocked`: campiona la scena una volta e usando tali valori in tutta la scena.
- `AVCapture WhiteBalance ModeContinuousAutoExposure`: campiona la scena in modo continuo per assicurarsi che sia ben bilanciata.

E l'applicazione può monitorare la proprietà `AdjustingWhiteBalance` per vedere quando viene regolata l'esposizione.

### <a name="new-white-balance-controls-in-ios-8"></a>Nuovi controlli di bilanciamento del bianco in iOS 8

Oltre alle funzionalità già fornite da iOS 7 e versioni successive, sono ora disponibili le funzionalità seguenti per controllare il saldo bianco in iOS 8:

- Controllo completamente manuale dei guadagni RGB del dispositivo.
- Get, set e Key-Value osservano i guadagni RGB del dispositivo.
- Supporto per il bilanciamento del bianco utilizzando una scheda grigia.
- Routine di conversione da e verso spazi dei colori indipendenti dal dispositivo.

Per implementare le funzionalità precedenti, è stata aggiunta la struttura `AVCaptureWhiteBalanceGain` con i membri seguenti:

- `RedGain`
- `GreenGain`
- `BlueGain`

Il guadagno massimo del saldo bianco è attualmente quattro (4) e può essere pronto dalla proprietà `MaxWhiteBalanceGain`. Quindi, l'intervallo valido è compreso tra 1 (1) e `MaxWhiteBalanceGain` (4).

È possibile utilizzare la proprietà `DeviceWhiteBalanceGains` per osservare i valori correnti. Usare `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` per regolare i guadagni del saldo quando la fotocamera si trova nella modalità di bilanciamento del bianco bloccata.

#### <a name="conversion-routines"></a>Routine di conversione

Le routine di conversione sono state aggiunte ad iOS 8 per semplificare la conversione a e da spazi dei colori indipendenti dal dispositivo. Per implementare le routine di conversione, è stata aggiunta la struttura `AVCaptureWhiteBalanceChromaticityValues` con i membri seguenti:

- `X`-è un valore compreso tra 0 e 1.
- `Y`-è un valore compreso tra 0 e 1.

È stata aggiunta anche una struttura di `AVCaptureWhiteBalanceTemperatureAndTintValues` con i membri seguenti:

- `Temperature`: valore a virgola mobile in gradi Kelvin.
- `Tint`-è un offset da verde o magenta compreso tra 0 e 150 con valori positivi verso la direzione verde e negativo verso il magenta.

Usare i metodi `CaptureDevice.GetTemperatureAndTintValues`e `CaptureDevice.GetDeviceWhiteBalanceGains`per eseguire la conversione tra la temperatura e la tinta, la cromaticità e gli spazi colore di guadagno RGB.

> [!NOTE]
> Le routine di conversione sono più accurate e il valore da convertire è più vicino al locus planckiano.

#### <a name="gray-card-support"></a>Supporto della scheda grigio

Apple usa il termine grigio mondo per fare riferimento al supporto della scheda grigio incorporato in iOS 8. Consente all'utente di concentrarsi su una scheda grigia fisica che copre almeno il 50% del centro del frame e lo utilizza per regolare il bilanciamento del bianco. Lo scopo della scheda grigia è quello di ottenere il bianco che sembra neutro.

Questa operazione può essere implementata in un'applicazione richiedendo all'utente di collocare una scheda grigia fisica davanti alla fotocamera, monitorando la proprietà `GrayWorldDeviceWhiteBalanceGains` e attendendo che i valori siano stabiliti.

L'applicazione bloccherà quindi i guadagni del saldo bianco per il metodo `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` usando i valori della proprietà `GrayWorldDeviceWhiteBalanceGains` per applicare le modifiche.

Il dispositivo di acquisizione deve essere bloccato per la configurazione prima che possa essere apportata una modifica nel saldo bianco.

### <a name="manual-white-balance-example"></a>Esempio di bilanciamento del bianco manuale

Con il codice di installazione di acquisizione AV generale, è possibile aggiungere un `UIViewController` allo storyboard dell'applicazione e configurarlo come segue:

[![](intro-to-manual-camera-controls-images/image18.png "A UIViewController can be added to the applications Storyboard and configured as shown here")](intro-to-manual-camera-controls-images/image18.png#lightbox)

La vista contiene gli elementi principali seguenti:

- `UIImageView` che visualizzerà il feed video.
- `UISegmentedControl` che cambierà la modalità messa a fuoco da automatica a bloccata.
- Due `UISlider` controlli che visualizzano e aggiornano la temperatura e la tonalità.
- `UIButton` utilizzata per campionare uno spazio grigio di una scheda grigia e impostare il bilanciamento bianco utilizzando tali valori.

Eseguire le operazioni seguenti per collegare il controller di visualizzazione per il controllo di bilanciamento del bianco manuale:

1. Aggiungere le istruzioni using seguenti:

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

1. Aggiungere le variabili private seguenti:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```

1. Aggiungere le proprietà calcolate seguenti:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. Aggiungere il metodo privato seguente per impostare la temperatura e la tinta del nuovo bilanciamento del bianco:

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

1. Eseguire l'override del metodo `ViewDidLoad` e aggiungere il codice seguente:

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

1. Eseguire l'override del metodo `ViewDidAppear` e aggiungere quanto segue per avviare la registrazione quando viene caricata la vista:

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

1. Salvare le modifiche apportate al codice ed eseguire l'applicazione.
1. Con la fotocamera in modalità auto, i dispositivi di scorrimento vengono spostati automaticamente quando la fotocamera regola il bilanciamento del bianco:

    [![](intro-to-manual-camera-controls-images/image19.png "The sliders will move automatically as the camera adjusts white balance")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. Toccare il segmento bloccato e trascinare i dispositivi di scorrimento Temp e tinta per regolare manualmente il bilanciamento del bianco:

    [![](intro-to-manual-camera-controls-images/image20.png "Drag the Temp and Tint sliders to adjust the white balance manually")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. Con il segmento bloccato ancora selezionato, posizionare una scheda grigio fisica davanti alla fotocamera e toccare il pulsante della scheda grigia per modificare il bilanciamento del bianco nel mondo grigio:

    [![](intro-to-manual-camera-controls-images/image21.png "Tap the Gray Card button to adjust white balance to the Gray World")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. Arrestare l'applicazione.

Il codice precedente ha illustrato come monitorare le impostazioni di bilanciamento del bianco quando la fotocamera è in modalità automatica o usare i dispositivi di scorrimento per controllare il saldo bianco quando è in modalità di blocco.

## <a name="bracketed-capture"></a>Acquisizione tra parentesi quadre

L'acquisizione tra parentesi quadre si basa sulle impostazioni dei controlli della fotocamera manuali presentati sopra e consente all'applicazione di acquisire un attimo di tempo, in diversi modi.

In poche parole, l'acquisizione tra parentesi quadre è un impeto di immagini ancora acquisite con un'ampia gamma di impostazioni, dall'immagine all'immagine.

[![](intro-to-manual-camera-controls-images/image22.png "How Bracketed Capture works")](intro-to-manual-camera-controls-images/image22.png#lightbox)

Usando l'acquisizione tra parentesi quadre in iOS 8, un'applicazione può preimpostare una serie di controlli della fotocamera manuali, emettere un unico comando e fare in modo che la scena corrente restituisca una serie di immagini per ognuno dei set di impostazioni manuali.

### <a name="bracketed-capture-basics"></a>Nozioni fondamentali sull'acquisizione tra parentesi

Anche in questo caso, l'acquisizione tra parentesi quadre è un impeto di immagini ancora acquisite con diverse impostazioni dall'immagine all'immagine. Sono disponibili i tipi di acquisizione racchiusi tra parentesi:

- **Parentesi di esposizione automatica** , in cui tutte le immagini hanno un valore di distorsione diverso.
- **Parentesi di esposizione manuale** , in cui tutte le immagini hanno una velocità di apertura variabile (durata) e un importo ISO.
- **Parentesi graffa semplice** : serie di immagini ancora acquisite in rapida successione.

### <a name="new-bracketed-capture-controls-in-ios-8"></a>Nuovi controlli di acquisizione racchiusi tra parentesi quadre in iOS 8

Tutti i comandi di acquisizione racchiusi tra parentesi sono implementati nella classe `AVCaptureStillImageOutput`. Usare il metodo `CaptureStillImageBracket`per ottenere una serie di immagini con la matrice di impostazioni specificata.

Per gestire le impostazioni sono state implementate due nuove classi:

- `AVCaptureAutoExposureBracketedStillImageSettings`: ha una proprietà, `ExposureTargetBias`, usata per impostare la distorsione per una parentesi di esposizione automatica.
- `AVCaptureManual``ExposureBracketedStillImageSettings`: sono presenti due proprietà, `ExposureDuration` e `ISO`, utilizzate per impostare la velocità dell'otturatore e l'ISO per una parentesi di esposizione manuale.

### <a name="bracketed-capture-controls-dos-and-donts"></a>I controlli di acquisizione tra parentesi fanno e non

#### <a name="dos"></a>Fai

Di seguito è riportato un elenco di operazioni da eseguire quando si usano i controlli di acquisizione racchiusi tra parentesi quadre in iOS 8:

- Preparare l'app per la situazione di acquisizione del caso peggiore chiamando il metodo `PrepareToCaptureStillImageBracket`.
- Si supponga che i buffer di esempio provengano dallo stesso pool condiviso.
- Per rilasciare la memoria allocata da una chiamata di preparazione precedente, chiamare di nuovo `PrepareToCaptureStillImageBracket` e inviargli una matrice di un oggetto.

#### <a name="donts"></a>Non consentite

Di seguito è riportato un elenco di elementi che non devono essere eseguiti quando si usano i controlli di acquisizione racchiusi tra parentesi quadre in iOS 8:

- Non combinare i tipi di impostazioni di acquisizione tra parentesi quadre in un'unica acquisizione.
- Non richiedere più di `MaxBracketedCaptureStillImageCount` immagini in una singola acquisizione.

### <a name="bracketed-capture-details"></a>Dettagli acquisizione tra parentesi quadre

Quando si lavora con l'acquisizione tra parentesi quadre in iOS 8, è necessario prendere in considerazione i dettagli seguenti:

- Le impostazioni tra parentesi sostituiscono temporaneamente le impostazioni di `AVCaptureDevice`.
- Le impostazioni di stabilizzazione dell'immagine e del flash vengono ignorate.
- Tutte le immagini devono usare lo stesso formato di output (JPEG, PNG e così via)
- L'anteprima video può rilasciare frame.
- L'acquisizione tra parentesi è supportata in tutti i dispositivi compatibili con iOS 8.

Tenendo conto di queste informazioni, verrà ora esaminato un esempio di uso dell'acquisizione tra parentesi quadre in iOS 8.

### <a name="bracket-capture-example"></a>Esempio di acquisizione tra parentesi quadre

Con il codice di installazione di acquisizione AV generale, è possibile aggiungere un `UIViewController` allo storyboard dell'applicazione e configurarlo come segue:

[![](intro-to-manual-camera-controls-images/image23.png "A UIViewController can be added to the applications Storyboard and configured as shown here")](intro-to-manual-camera-controls-images/image23.png#lightbox)

La vista contiene gli elementi principali seguenti:

- `UIImageView` che visualizzerà il feed video.
- Tre `UIImageViews` che visualizzeranno i risultati dell'acquisizione.
- `UIScrollView` per ospitare il feed video e le visualizzazioni risultati.
- `UIButton` utilizzata per eseguire un'acquisizione tra parentesi quadre con alcune impostazioni predefinite.

Eseguire le operazioni seguenti per collegare il controller di visualizzazione per la cattura tra parentesi:

1. Aggiungere le istruzioni using seguenti:

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

1. Aggiungere le variabili private seguenti:

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```

1. Aggiungere le proprietà calcolate seguenti:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```

1. Aggiungere il metodo privato seguente per compilare le visualizzazioni dell'immagine di output richieste:

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

1. Eseguire l'override del metodo `ViewDidLoad` e aggiungere il codice seguente:

    ```csharp
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

1. Eseguire l'override del metodo `ViewDidAppear` e aggiungere il codice seguente:

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

1. Salvare le modifiche apportate al codice ed eseguire l'applicazione.
1. Incorniciare una scena e toccare il pulsante Acquisisci parentesi quadre:

    [![](intro-to-manual-camera-controls-images/image24.png "Frame a scene and tap the Capture Bracket button")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. Scorrere rapidamente verso sinistra per visualizzare le tre immagini acquisite dall'acquisizione tra parentesi quadre:

    [![](intro-to-manual-camera-controls-images/image25.png "Swipe right to left to see the three images taken by the Bracketed Capture")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. Arrestare l'applicazione.

Il codice precedente ha illustrato come configurare e acquisire un'acquisizione automatica tra parentesi in iOS 8.

## <a name="summary"></a>Riepilogo

In questo articolo è stata illustrata un'introduzione ai nuovi controlli della fotocamera manuale forniti da iOS 8 e sono stati trattati i concetti di base relativi a come funzionano. Sono stati forniti esempi di messa a fuoco manuale, esposizione manuale e bilanciamento del bianco manuale. Infine, è stato fornito un esempio di acquisizione tra parentesi quadre usando i controlli della fotocamera manuale descritti in precedenza

## <a name="related-links"></a>Collegamenti correlati

- [ManualCameraControls (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/manualcameracontrols)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
