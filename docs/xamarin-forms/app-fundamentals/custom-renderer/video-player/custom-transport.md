---
title: Controlli di trasporto personalizzato di video
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 5463a91dba5840ebe655aa1509d9f98e73643d26
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="custom-video-transport-controls"></a>Controlli di trasporto personalizzato di video

I pulsanti che eseguono le funzioni di includere i controlli di trasporto di un lettore video **riprodurre**, **pausa**, e **arrestare**. Questi pulsanti sono identificati in genere con icone familiarità anziché di testo e **riprodurre** e **pausa** funzioni vengono in genere combinate in un unico pulsante.

Per impostazione predefinita, il `VideoPlayer` Visualizza trasporto controlli supportati da ogni piattaforma. Quando si imposta la `AreTransportControlsEnabled` proprietà `false`, questi controlli vengono soppressi. È quindi possibile controllare il `VideoPlayer` a livello di codice o fornire i propri controlli di trasporto.

## <a name="the-play-pause-and-stop-methods"></a>I metodi di riproduzione, pausa e arresto

Il `VideoPlayer` classe definisce tre metodi denominati `Play`, `Pause`, e `Stop` che vengono implementate tramite la generazione di eventi:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        public event EventHandler PlayRequested;

        public void Play()
        {
            PlayRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler PauseRequested;

        public void Pause()
        {
            PauseRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler StopRequested;

        public void Stop()
        {
            StopRequested?.Invoke(this, EventArgs.Empty);
        }
    }
}
```

Gestori eventi per questi eventi vengono impostati dalla `VideoPlayerRenderer` classe in ogni piattaforma, come illustrato di seguito:

### <a name="ios-transport-implementations"></a>le implementazioni del trasporto iOS

La versione di iOS `VideoPlayerRenderer` utilizza il `OnElementChanged` per impostare i gestori per questi tre eventi quando il `NewElement` proprietà non è `null` e rimuove i gestori di eventi quando `OldElement` non `null`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            player.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            player.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            player.Pause();
            player.Seek(new CMTime(0, 1));
        }
    }
}
```

I gestori eventi vengono implementati mediante la chiamata di metodi sul `AVPlayer` oggetto. Non esiste alcun `Stop` metodo per `AVPlayer`, pertanto viene simulato sospendendo il video e spostare la posizione di inizio.

### <a name="android-transport-implementations"></a>Implementazioni di trasporto Android

L'implementazione di Android è simile all'implementazione di iOS. I gestori per tre funzioni vengono impostati quando `NewElement` non `null` e scollegato quando `OldElement` non `null`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        void OnPlayRequested(object sender, EventArgs args)
        {
            videoView.Start();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            videoView.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            videoView.StopPlayback();
        }
    }
}
```

Le tre funzioni chiamano metodi definiti dal `VideoView`.

### <a name="uwp-transport-implementations"></a>Implementazioni di trasporto UWP

L'implementazione UWP delle funzioni di tre trasporto è molto simile alla iOS e Android implementazioni:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            Control.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            Control.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            Control.Stop();
        }
    }
}
```

## <a name="the-video-player-status"></a>Lo stato del lettore video

Implementazione di **riprodurre**, **pausa**, e **arrestare** funzioni non sono sufficienti per supportare i controlli di trasporto. Spesso il **riprodurre** e **pausa** comandi vengono implementati con lo stesso pulsante che cambia aspetto per indicare se il video è attualmente in fase di riproduzione o sospesa. Inoltre, il pulsante non deve anche abilitato se il video non è ancora caricato.

Questi requisiti implicano che il lettore video deve rendere disponibile un stato corrente che indica se la riproduzione o in pausa o se non è ancora pronto per riprodurre un video. (Tre piattaforme supportano anche le proprietà che indicano se il video può essere sospeso o può essere spostato in una nuova posizione, ma queste proprietà sono applicabili per video in streaming anziché file video, in modo che non sono supportati nel `VideoPlayer` descritti di seguito.)

Il **VideoPlayerDemos** progetto include un `VideoStatus` enumerazione con tre membri:

```csharp
namespace FormsVideoLibrary
{
    public enum VideoStatus
    {
        NotReady,
        Playing,
        Paused
    }
}
```

Il `VideoPlayer` classe definisce una proprietà associabile solo reale denominata `Status` di tipo `VideoStatus`. Questa proprietà è definita come di sola lettura perché deve essere impostata solo dal renderer di piattaforma:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Status read-only property
        private static readonly BindablePropertyKey StatusPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Status), typeof(VideoStatus), typeof(VideoPlayer), VideoStatus.NotReady);

        public static readonly BindableProperty StatusProperty = StatusPropertyKey.BindableProperty;

        public VideoStatus Status
        {
            get { return (VideoStatus)GetValue(StatusProperty); }
        }

        VideoStatus IVideoPlayerController.Status
        {
            set { SetValue(StatusPropertyKey, value); }
            get { return Status; }
        }
        ···
    }
}
```

In genere, una proprietà associabile di sola lettura avrebbe una privata `set` funzione di accesso sul `Status` proprietà per consentire l'impostazione da all'interno della classe. Per un `View` derivato supportato dal renderer, tuttavia, la proprietà deve essere impostata all'esterno della classe, ma solo dal renderer di piattaforma.

Per questo motivo, viene definita un'altra proprietà con il nome `IVideoPlayerController.Status`. Questa è un'implementazione esplicita dell'interfaccia e si è reso possibile dal `IVideoPlayerController` interfaccia il `VideoPlayer` implementazione della classe:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

Questa operazione è simile alla modalità [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) controlli utilizza il [ `IWebViewController` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IWebViewController/) interfaccia da implementare il `CanGoBack` e `CanGoForward` proprietà. (Vedere il codice sorgente di [ `WebView` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs) il renderer per informazioni dettagliate e.)

Questo rende possibile per una classe esterna `VideoPlayer` per impostare il `Status` proprietà facendo riferimento al `IVideoPlayerController` interfaccia. (Verrà visualizzato il codice al più presto.) La proprietà può essere impostata da altre classi oltre, ma è probabile impostare inavvertitamente. In particolare, il `Status` non può essere impostata tramite l'associazione dati.

Per agevolare il renderer mantenere questo `Status` proprietà aggiornata, il `VideoPlayer` classe definisce un `UpdateStatus` evento che viene attivato ogni decimo di secondo:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        public event EventHandler UpdateStatus;

        public VideoPlayer()
        {
            Device.StartTimer(TimeSpan.FromMilliseconds(100), () =>
            {
                UpdateStatus?.Invoke(this, EventArgs.Empty);
                return true;
            });
        }
        ···
    }
}
```

### <a name="the-ios-status-setting"></a>L'impostazione dello stato di iOS

IOS `VideoPlayerRenderer` imposta un gestore per il `UpdateStatus` evento (e disconnette tale gestore quando sottostante `VideoPlayer` elemento è assente) e utilizza il gestore per impostare il `Status` proprietà:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (player.Status)
            {
                case AVPlayerStatus.ReadyToPlay:
                    switch (player.TimeControlStatus)
                    {
                        case AVPlayerTimeControlStatus.Playing:
                            videoStatus = VideoStatus.Playing;
                            break;

                        case AVPlayerTimeControlStatus.Paused:
                            videoStatus = VideoStatus.Paused;
                            break;
                    }
                    break;
                }
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
            ···
        }
        ···
    }
}
```

Due proprietà di `AVPlayer` deve essere accessibile: il [ `Status` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.Status/) proprietà di tipo `AVPlayerStatus` e [ `TimeControlStatus` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.TimeControlStatus/) proprietà di tipo `AVPlayerTimeControlStatus`. Si noti che il `Element` proprietà (ovvero il `VideoPlayer`) deve essere impostato `IVideoPlayerController` per impostare il `Status` proprietà.

### <a name="the-android-status-setting"></a>L'impostazione dello stato di Android

Il [ `IsPlaying` ](https://developer.xamarin.com/api/property/Android.Widget.VideoView.IsPlaying/) proprietà di Android `VideoView` è un valore booleano che indica solo se il video viene riprodotto o in pausa. Per determinare se il `VideoView` possibile né play o sospendere il video ancora, `Prepared` evento di `VideoView` deve essere gestito. Impostate in queste due gestori di `OnElementChanged` (metodo) e disconnesse durante la `Dispose` eseguire l'override:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    videoView.Prepared += OnVideoViewPrepared;
                    ···
                }
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }

        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            if (Element != null)
            {
                Element.UpdateStatus -= OnUpdateStatus;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

Il `UpdateStatus` gestore utilizza il `isPrepared` campo (impostato `Prepared` gestore) e `IsPlaying` proprietà da impostare il `Status` proprietà:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus status = VideoStatus.NotReady;

            if (isPrepared)
            {
                status = videoView.IsPlaying ? VideoStatus.Playing : VideoStatus.Paused;
            }
            ···
        }
        ···
    }
}
```

### <a name="the-uwp-status-setting"></a>L'impostazione di stato UWP

UWP `VideoPlayerRenderer` si avvalgono del `UpdateStatus` evento, ma è necessaria per l'impostazione di `Status` proprietà. Il `MediaElement` definisce un [ `CurrentStateChanged` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentStateChanged) un evento che consente il renderer per ricevere una notifica quando il [ `CurrentState` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentState) proprietà è stata modificata. La proprietà viene disconnesso nel `Dispose` override:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                };
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                ···
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

Il `CurrentState` proprietà è di tipo [ `MediaElementState` ](/uwp/api/windows.ui.xaml.media.mediaelementstate)e viene eseguito il mapping con facilità in `VideoStatus`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementCurrentStateChanged(object sender, RoutedEventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (Control.CurrentState)
            {
                case MediaElementState.Playing:
                    videoStatus = VideoStatus.Playing;
                    break;

                case MediaElementState.Paused:
                case MediaElementState.Stopped:
                    videoStatus = VideoStatus.Paused;
                    break;
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
        }
        ···
    }
}
```

## <a name="play-pause-and-stop-buttons"></a>Riproduzione, pausa e arrestare i pulsanti

Utilizzo di caratteri Unicode per simbolico **riprodurre**, **pausa**, e **arrestare** immagini risulta problematico. Il [varie tecniche](https://unicode-table.com/en/blocks/miscellaneous-technical/) sezione dello standard Unicode definisce tre caratteri simbolo apparentemente adeguati a questo scopo. Questi sono:

- 0x23F5 (medium rivolta verso destra triangolo nero) o &#x23F5; per **riprodurre**
- 0x23F8 (barra verticale doppia) o &#x23F8; per **Sospendi**
- 0x23F9 (quadrato di colore nero) o &#x23F9; per **arrestare**

Indipendentemente dal fatto che come questi simboli visualizzati nel browser e browser diversi gestiscono in modi diversi, non vengono sempre visualizzati sulle piattaforme supportate da xamarin. Forms. Nei dispositivi UWP, iOS e il **pausa** e **arrestare** caratteri hanno un aspetto grafico, con un piano bianco e uno sfondo blu 3D. Ciò non rappresenta il case in Android, in cui il simbolo è semplicemente blu. Tuttavia, il punto di codice 0x23F5 per **riprodurre** hanno stesso aspetto su UWP e non è supportata in iOS e Android.

Per questo motivo, il punto di codice 0x23F5 non può essere utilizzato per **riprodurre**. È un sostituto valido:

- 0x25B6 (triangolo nero verso destra) o &#x25B6; per **riprodurre**

Questo è supportato da tutti e tre le piattaforme ad eccezione del fatto che è una forma di triangolo nero normale che assomiglia a aspetto 3D **pausa** e **arrestare**. Una possibilità consiste nel seguire il punto di codice 0x25B6 con un codice di variante:

- 0x25B6 seguita da 0xFE0F (variant 16) o &#x25B6; &#xFE0F; per **Riproduci**

Questo è ciò che viene utilizzato nel codice indicato di seguito. In iOS, offre il **riprodurre** lo stesso aspetto 3D dei simboli di **pausa** e **arrestare** pulsanti, ma la variante non funziona in Android e la piattaforma UWP.

Il **trasporto Custom** pagina imposta il **AreTransportControlsEnabled** proprietà **false** e include un `ActivityIndicator` visualizzato quando si carica il video e due pulsanti. `DataTrigger` gli oggetti vengono utilizzati per attivare e disattivare il `ActivityIndicator` e i pulsanti e per passare tra il primo pulsante **riprodurre** e **pausa**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomTransportPage"
             Title="Custom Transport">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AutoPlay="False"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource BigBuckBunny}" />

        <ActivityIndicator Grid.Row="0"
                           Color="Gray"
                           IsVisible="False">
            <ActivityIndicator.Triggers>
                <DataTrigger TargetType="ActivityIndicator"
                             Binding="{Binding Source={x:Reference videoPlayer},
                                               Path=Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsVisible" Value="True" />
                    <Setter Property="IsRunning" Value="True" />
                </DataTrigger>
            </ActivityIndicator.Triggers>
        </ActivityIndicator>

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="0, 10"
                     BindingContext="{x:Reference videoPlayer}">

            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.Playing}">
                        <Setter Property="Text" Value="&#x23F8; Pause" />
                    </DataTrigger>

                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>

            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

I trigger Data sono descritti in dettaglio nell'articolo [i trigger Data](~/xamarin-forms/app-fundamentals/triggers.md#data).

Il file code-behind è i gestori per il pulsante `Clicked` eventi:

```csharp
namespace VideoPlayerDemos
{
    public partial class CustomTransportPage : ContentPage
    {
        public CustomTransportPage()
        {
            InitializeComponent();
        }

        void OnPlayPauseButtonClicked(object sender, EventArgs args)
        {
            if (videoPlayer.Status == VideoStatus.Playing)
            {
                videoPlayer.Pause();
            }
            else if (videoPlayer.Status == VideoStatus.Paused)
            {
                videoPlayer.Play();
            }
        }

        void OnStopButtonClicked(object sender, EventArgs args)
        {
            videoPlayer.Stop();
        }
    }
}
```

Poiché `AutoPlay` è impostato su `false` nel **CustomTransport.xaml** file, sarà necessario premere il **riprodurre** pulsante quando viene abilitato per iniziare il video. I pulsanti sono definiti in modo che i caratteri Unicode in precedenza sono accompagnati da equivalenti di testo. I pulsanti sono un aspetto coerente in ogni piattaforma per la riproduzione del video:

[![Riproduzione di trasporto personalizzato](custom-transport-images/customtransportplaying-small.png "la riproduzione di trasporto personalizzato")](custom-transport-images/customtransportplaying-large.png#lightbox "la riproduzione di trasporto personalizzato")

Ma in Android e UWP, il **riprodurre** pulsante è molto diverso se il video è stato sospeso:

[![Trasporto personalizzato sospeso](custom-transport-images/customtransportpaused-small.png "trasporto personalizzato sospeso")](custom-transport-images/customtransportpaused-large.png#lightbox "sospesa di trasporto personalizzato")

In un'applicazione di produzione, è opportuno utilizzare le proprie immagini bitmap per i pulsanti per raggiungere l'uniformità.


## <a name="related-links"></a>Collegamenti correlati

- [Demo di lettore video (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
