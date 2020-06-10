---
title: "riconoscimento vocale con l'API del servizio vocale" Descrizione: "questo articolo spiega come usare l'API del servizio riconoscimento vocale di Azure per trascrivere il riconoscimento vocale in un' Xamarin.Forms applicazione".
ms. prod: Novell MS. AssetID: B435FF6B-8785-48D9-B2D9-1893F5A87EA1 ms. Technology: Novell-Forms Author: profexorgeek ms. Author: jusjohns ms. Date: 01/14/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="speech-recognition-using-azure-speech-service"></a>Riconoscimento vocale con il servizio riconoscimento vocale di Azure

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)

Il servizio riconoscimento vocale di Azure è un'API basata sul cloud che offre le funzionalità seguenti:

- **Riconoscimento vocale** consente di trascrivere file audio o flussi in testo.
- **Text-to-Speech** converte il testo di input in un riconoscimento vocale con sintesi umana.
- La **traduzione vocale** consente la traduzione in tempo reale e multilingue per sintesi vocale e sintesi vocale.
- Gli **assistenti vocali** possono creare interfacce di conversazione simili a quelle umane per le applicazioni.

Questo articolo illustra il modo in cui la sintesi vocale è implementata nell' Xamarin.Forms applicazione di esempio tramite il servizio di riconoscimento vocale di Azure. Gli screenshot seguenti illustrano l'applicazione di esempio in iOS e Android:

[![Screenshot dell'applicazione di esempio in iOS e Android](speech-recognition-images/speech-recognition-cropped.png)](speech-recognition-images/speech-recognition.png#lightbox "Screenshot dell'applicazione di esempio in iOS e Android")

## <a name="create-an-azure-speech-service-resource"></a>Creare una risorsa del servizio riconoscimento vocale di Azure

Il servizio riconoscimento vocale di Azure fa parte dei servizi cognitivi di Azure, che fornisce API basate sul cloud per attività quali il riconoscimento delle immagini, il riconoscimento vocale e la traduzione e la ricerca Bing. Per altre informazioni, vedere [che cosa sono i servizi cognitivi di Azure?](https://docs.microsoft.com/azure/cognitive-services/welcome).

Il progetto di esempio richiede la creazione di una risorsa Servizi cognitivi di Azure nella portale di Azure. È possibile creare una risorsa di servizi cognitivi per un singolo servizio, ad esempio un servizio di riconoscimento vocale, o come risorsa multiservizio. I passaggi per creare una risorsa di servizio vocale sono i seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Creare una risorsa multiservizio o a servizio singolo.
1. Ottenere le informazioni sulla chiave API e sull'area per la risorsa.
1. Aggiornare il file **Constants.cs** di esempio.

Per una guida dettagliata alla creazione di una risorsa, vedere [creare una risorsa di servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare. Quando si dispone di un account, è possibile creare una risorsa a servizio singolo a livello gratuito per provare il servizio.

## <a name="configure-your-app-with-the-speech-service"></a>Configurare l'app con il servizio di riconoscimento vocale

Dopo la creazione di una risorsa Servizi cognitivi, il file **Constants.cs** può essere aggiornato con l'area e la chiave API dalla risorsa di Azure:

```csharp
public static class Constants
{
    public static string CognitiveServicesApiKey = "YOUR_KEY_GOES_HERE";
    public static string CognitiveServicesRegion = "westus";
}
```

## <a name="install-nuget-speech-service-package"></a>Installare il pacchetto del servizio riconoscimento vocale NuGet

L'applicazione di esempio usa il pacchetto NuGet **Microsoft. CognitiveServices. Speech** per connettersi al servizio riconoscimento vocale di Azure. Installare il pacchetto NuGet nel progetto condiviso e in ogni progetto di piattaforma.

## <a name="create-an-imicrophoneservice-interface"></a>Creare un'interfaccia IMicrophoneService

Ogni piattaforma richiede l'autorizzazione per l'accesso al microfono. Il progetto di esempio fornisce un' `IMicrophoneService` interfaccia nel progetto condiviso e utilizza Xamarin.Forms `DependencyService` per ottenere le implementazioni della piattaforma dell'interfaccia.

```csharp
public interface IMicrophoneService
{
    Task<bool> GetPermissionAsync();
    void OnRequestPermissionResult(bool isGranted);
}
```

## <a name="create-the-page-layout"></a>Creare il layout di pagina

Il progetto di esempio definisce un layout di pagina di base nel file **MainPage. XAML** . Gli elementi di layout della chiave sono un oggetto `Button` che avvia il processo di trascrizione, un oggetto `Label` per contenere il testo trascritto e un oggetto `ActivityIndicator` da mostrare quando è in corso la trascrizione:

```xaml
<ContentPage ...>
    <StackLayout>
        <Frame ...>
            <ScrollView x:Name="scroll"
                        ...>
                <Label x:Name="transcribedText"
                       ... />
            </ScrollView>
        </Frame>

        <ActivityIndicator x:Name="transcribingIndicator"
                           IsRunning="False" />
        <Button x:Name="transcribeButton"
                ...
                Clicked="TranscribeClicked"/>
    </StackLayout>
</ContentPage>
```

## <a name="implement-the-speech-service"></a>Implementare il servizio di riconoscimento vocale

Il file code-behind **MainPage.XAML.cs** contiene tutta la logica per inviare audio e ricevere il testo trascritto dal servizio di riconoscimento vocale di Azure.

Il `MainPage` costruttore ottiene un'istanza dell' `IMicrophoneService` interfaccia da `DependencyService` :

```csharp
public partial class MainPage : ContentPage
{
    SpeechRecognizer recognizer;
    IMicrophoneService micService;
    bool isTranscribing = false;

    public MainPage()
    {
        InitializeComponent();

        micService = DependencyService.Resolve<IMicrophoneService>();
    }

    // ...
}
```

Il `TranscribeClicked` metodo viene chiamato quando `transcribeButton` viene toccata l'istanza:

```csharp
async void TranscribeClicked(object sender, EventArgs e)
{
    bool isMicEnabled = await micService.GetPermissionAsync();

    // EARLY OUT: make sure mic is accessible
    if (!isMicEnabled)
    {
        UpdateTranscription("Please grant access to the microphone!");
        return;
    }

    // initialize speech recognizer 
    if (recognizer == null)
    {
        var config = SpeechConfig.FromSubscription(Constants.CognitiveServicesApiKey, Constants.CognitiveServicesRegion);
        recognizer = new SpeechRecognizer(config);
        recognizer.Recognized += (obj, args) =>
        {
            UpdateTranscription(args.Result.Text);
        };
    }

    // if already transcribing, stop speech recognizer
    if (isTranscribing)
    {
        try
        {
            await recognizer.StopContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = false;
    }

    // if not transcribing, start speech recognizer
    else
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            InsertDateTimeRecord();
        });
        try
        {
            await recognizer.StartContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = true;
    }
    UpdateDisplayState();
}
```

Il metodo `TranscribeClicked` esegue le operazioni seguenti:

1. Verifica se l'applicazione ha accesso al microfono e si chiude in modo tempestivo in caso contrario.
1. Crea un'istanza della `SpeechRecognizer` classe se non esiste già.
1. Arresta la trascrizione continua se è in corso.
1. Inserisce un timestamp e avvia la trascrizione continua se non è in corso.
1. Invia una notifica all'applicazione per aggiornarne l'aspetto in base al nuovo stato dell'applicazione.

I restanti `MainPage` metodi della classe sono helper per la visualizzazione dello stato dell'applicazione:

```csharp
void UpdateTranscription(string newText)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (!string.IsNullOrWhiteSpace(newText))
        {
            transcribedText.Text += $"{newText}\n";
        }
    });
}

void InsertDateTimeRecord()
{
    var msg = $"=================\n{DateTime.Now.ToString()}\n=================";
    UpdateTranscription(msg);
}

void UpdateDisplayState()
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (isTranscribing)
        {
            transcribeButton.Text = "Stop";
            transcribeButton.BackgroundColor = Color.Red;
            transcribingIndicator.IsRunning = true;
        }
        else
        {
            transcribeButton.Text = "Transcribe";
            transcribeButton.BackgroundColor = Color.Green;
            transcribingIndicator.IsRunning = false;
        }
    });
}
```

Il `UpdateTranscription` metodo scrive l'oggetto fornito nell' `newText` `string` `Label` elemento denominato `transcribedText` . Forza l'esecuzione dell'aggiornamento sul thread dell'interfaccia utente, in modo che possa essere chiamato da qualsiasi contesto senza causare eccezioni. `InsertDateTimeRecord`Scrive la data e l'ora correnti nell' `transcribedText` istanza di per contrassegnare l'inizio di una nuova trascrizione. Infine, il `UpdateDisplayState` metodo aggiorna gli `Button` `ActivityIndicator` elementi e per indicare se la trascrizione è in corso o meno.

## <a name="create-platform-microphone-services"></a>Creazione di servizi di piattaforma microfono

Per raccogliere dati vocali, l'applicazione deve disporre dell'accesso al microfono. L' `IMicrophoneService` interfaccia deve essere implementata e registrata con `DependencyService` in ogni piattaforma per il funzionamento dell'applicazione.

### <a name="android"></a>Android

Il progetto di esempio definisce un' `IMicrophoneService` implementazione per Android denominata `AndroidMicrophoneService` :

```csharp
[assembly: Dependency(typeof(AndroidMicrophoneService))]
namespace CognitiveSpeechService.Droid.Services
{
    public class AndroidMicrophoneService : IMicrophoneService
    {
        public const int RecordAudioPermissionCode = 1;
        private TaskCompletionSource<bool> tcsPermissions;
        string[] permissions = new string[] { Manifest.Permission.RecordAudio };

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();

            if ((int)Build.VERSION.SdkInt < 23)
            {
                tcsPermissions.TrySetResult(true);
            }
            else
            {
                var currentActivity = MainActivity.Instance;
                if (ActivityCompat.CheckSelfPermission(currentActivity, Manifest.Permission.RecordAudio) != (int)Permission.Granted)
                {
                    RequestMicPermissions();
                }
                else
                {
                    tcsPermissions.TrySetResult(true);
                }

            }

            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermissions()
        {
            if (ActivityCompat.ShouldShowRequestPermissionRationale(MainActivity.Instance, Manifest.Permission.RecordAudio))
            {
                Snackbar.Make(MainActivity.Instance.FindViewById(Android.Resource.Id.Content),
                        "Microphone permissions are required for speech transcription!",
                        Snackbar.LengthIndefinite)
                        .SetAction("Ok", v =>
                        {
                            ((Activity)MainActivity.Instance).RequestPermissions(permissions, RecordAudioPermissionCode);
                        })
                        .Show();
            }
            else
            {
                ActivityCompat.RequestPermissions((Activity)MainActivity.Instance, permissions, RecordAudioPermissionCode);
            }
        }
    }
}
```

`AndroidMicrophoneService`Dispone delle funzionalità seguenti:

1. L' `Dependency` attributo registra la classe con `DependencyService` .
1. Il `GetPermissionAsync` metodo controlla se le autorizzazioni sono necessarie in base alla versione Android SDK e chiama `RequestMicPermissions` se l'autorizzazione non è già stata concessa.
1. Il `RequestMicPermissions` metodo usa la `Snackbar` classe per richiedere le autorizzazioni all'utente se è necessaria una logica; in caso contrario, richiede direttamente le autorizzazioni di registrazione audio.
1. Il `OnRequestPermissionResult` metodo viene chiamato con un `bool` risultato dopo che l'utente ha risposto alla richiesta di autorizzazioni.

La `MainActivity` classe è personalizzata per aggiornare l' `AndroidMicrophoneService` istanza quando sono state completate le richieste di autorizzazioni:

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    IMicrophoneService micService;
    internal static MainActivity Instance { get; private set; }
    
    protected override void OnCreate(Bundle savedInstanceState)
    {
        Instance = this;
        // ...
        micService = DependencyService.Resolve<IMicrophoneService>();
    }
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        // ...
        switch(requestCode)
        {
            case AndroidMicrophoneService.RecordAudioPermissionCode:
                if (grantResults[0] == Permission.Granted)
                {
                    micService.OnRequestPermissionResult(true);
                }
                else
                {
                    micService.OnRequestPermissionResult(false);
                }
                break;
        }
    }
}
```

La `MainActivity` classe definisce un riferimento statico chiamato `Instance` , che è richiesto dall' `AndroidMicrophoneService` oggetto durante la richiesta di autorizzazioni. Esegue l'override del `OnRequestPermissionsResult` metodo per aggiornare l' `AndroidMicrophoneService` oggetto quando la richiesta di autorizzazioni viene approvata o rifiutata dall'utente.

Infine, è necessario che l'applicazione Android includa l'autorizzazione per registrare l'audio nel file **AndroidManifest.xml** :

```xml
<manifest ...>
    ...
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
</manifest>
```

### <a name="ios"></a>iOS

Il progetto di esempio definisce un' `IMicrophoneService` implementazione per iOS denominata `iOSMicrophoneService` :

```csharp
[assembly: Dependency(typeof(iOSMicrophoneService))]
namespace CognitiveSpeechService.iOS.Services
{
    public class iOSMicrophoneService : IMicrophoneService
    {
        TaskCompletionSource<bool> tcsPermissions;

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();
            RequestMicPermission();
            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermission()
        {
            var session = AVAudioSession.SharedInstance();
            session.RequestRecordPermission((granted) =>
            {
                tcsPermissions.TrySetResult(granted);
            });
        }
    }
}
```

`iOSMicrophoneService`Dispone delle funzionalità seguenti:

1. L' `Dependency` attributo registra la classe con `DependencyService` .
1. Il `GetPermissionAsync` metodo chiama `RequestMicPermissions` per richiedere le autorizzazioni dall'utente del dispositivo.
1. Il `RequestMicPermissions` metodo usa l' `AVAudioSession` istanza condivisa per richiedere le autorizzazioni di registrazione.
1. Il `OnRequestPermissionResult` metodo aggiorna l' `TaskCompletionSource` istanza di con il `bool` valore fornito.

Infine, il file **info. plist** dell'app iOS deve includere un messaggio che indica all'utente il motivo per cui l'app richiede l'accesso al microfono. Modificare il file INFO. plist per includere i seguenti tag all'interno dell' `<dict>` elemento:

```xml
<plist>
    <dict>
        ...
        <key>NSMicrophoneUsageDescription</key>
        <string>Voice transcription requires microphone access</string>
    </dict>
</plist>
```

### <a name="uwp"></a>Piattaforma UWP

Il progetto di esempio definisce un' `IMicrophoneService` implementazione per UWP denominata `UWPMicrophoneService` :

```csharp
[assembly: Dependency(typeof(UWPMicrophoneService))]
namespace CognitiveSpeechService.UWP.Services
{
    public class UWPMicrophoneService : IMicrophoneService
    {
        public async Task<bool> GetPermissionAsync()
        {
            bool isMicAvailable = true;
            try
            {
                var mediaCapture = new MediaCapture();
                var settings = new MediaCaptureInitializationSettings();
                settings.StreamingCaptureMode = StreamingCaptureMode.Audio;
                await mediaCapture.InitializeAsync(settings);
            }
            catch(Exception ex)
            {
                isMicAvailable = false;
            }

            if(!isMicAvailable)
            {
                await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
            }

            return isMicAvailable;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            // intentionally does nothing
        }
    }
}
```

`UWPMicrophoneService`Dispone delle funzionalità seguenti:

1. L' `Dependency` attributo registra la classe con `DependencyService` .
1. Il `GetPermissionAsync` metodo tenta di inizializzare un' `MediaCapture` istanza di. Se l'operazione ha esito negativo, viene avviata una richiesta dell'utente per abilitare il microfono.
1. Il `OnRequestPermissionResult` metodo esiste per soddisfare l'interfaccia ma non è necessario per l'implementazione di UWP.

Infine, il **pacchetto UWP. appxmanifest** deve specificare che l'applicazione usa il microfono. Fare doppio clic sul file Package. appxmanifest e selezionare l'opzione **Microphone** nella scheda **funzionalità** di Visual Studio 2019:

[![Screenshot del manifesto in Visual Studio 2019](speech-recognition-images/package-manifest-cropped.png)](speech-recognition-images/package-manifest.png#lightbox "Screenshot del manifesto in Visual Studio 2019")

## <a name="test-the-application"></a>Test dell'applicazione

Eseguire l'app e fare clic sul pulsante **transpresepe** . L'app deve richiedere l'accesso al microfono e iniziare il processo di trascrizione. Il `ActivityIndicator` viene animato, mostrando che la trascrizione è attiva. Quando si parla, l'app esegue lo streaming dei dati audio alla risorsa servizi vocali di Azure, che risponderà con il testo trascritto. Il testo trascritto verrà visualizzato nell' `Label` elemento mentre viene ricevuto.

> [!NOTE]
> Gli emulatori Android non riescono a caricare e inizializzare le librerie di servizi vocali. È consigliabile eseguire test su un dispositivo fisico per la piattaforma Android.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di servizio vocale di Azure](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)
- [Panoramica del servizio riconoscimento vocale di Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)
- [Creare una risorsa per Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)
- [Avvio rapido: Riconoscimento vocale da un microfono](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone)