---
title: Controlli di trasporto video personalizzati
description: Questo articolo illustra come implementare i controlli di trasporto personalizzato in un'applicazione di lettore video con xamarin. Forms.
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 84870de28ffd30b2d29fb5d8fbea815e1fd0d9c4
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996438"
---
# <a name="custom-video-transport-controls"></a>Controlli di trasporto video personalizzati

I controlli di trasporto di un lettore video includono i pulsanti che eseguono le funzioni **riprodurre**, **Sospendi**, e **arrestare**. Questi pulsanti sono identificati in genere con icone familiare anziché di testo e il **riprodurre** e **Sospendi** funzioni vengono in genere combinati in un unico pulsante.

Per impostazione predefinita, il `VideoPlayer` Visualizza trasporto controlli supportati per ogni piattaforma. Quando si impostano i `AreTransportControlsEnabled` proprietà `false`, questi controlli vengono soppressi. È quindi possibile controllare il `VideoPlayer` a livello di codice o fornire i propri controlli di trasporto.

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

I gestori eventi per questi eventi vengono impostati dal `VideoPlayerRenderer` classe in ogni piattaforma, come illustrato di seguito:

### <a name="ios-transport-implementations"></a>le implementazioni di trasporto di iOS

La versione di iOS `VideoPlayerRenderer` Usa il `OnElementChanged` metodo per impostare i gestori per questi tre eventi quando il `NewElement` proprietà non è `null` e Scollega i gestori di eventi quando `OldElement` non `null`:

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

Vengono implementati i gestori eventi chiamando metodi sul `AVPlayer` oggetto. È presente alcun `Stop` metodo per `AVPlayer`, in modo che viene simulata sospendendo il video e sposta la posizione di inizio.

### <a name="android-transport-implementations"></a>Implementazioni di trasporto Android

L'implementazione di Android è simile all'implementazione di iOS. I gestori per le tre funzioni vengono impostati quando `NewElement` non è `null` e rimossa quando `OldElement` non è `null`:

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

Le tre funzioni di chiamano i metodi definiti da `VideoView`.

### <a name="uwp-transport-implementations"></a>Implementazioni di trasporto UWP

L'implementazione di UWP delle funzioni di tre trasporto è molto simile a iOS e Android implementazioni:

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

Implementa il **riprodurre**, **Sospendi**, e **arrestare** funzioni non sono sufficienti per supportare i controlli di trasporto. Spesso le **riprodurre** e **Sospendi** comandi vengono implementati con lo stesso pulsante che cambia il suo aspetto per indicare se il video è attualmente in fase di riproduzione o in pausa. Il pulsante, inoltre, non deve essere abilitato anche se il video non ha ancora caricato.

Questi requisiti implicano che il lettore video deve rendere disponibile un stato corrente che indica se è la riproduzione o messo in pausa o se non è ancora pronto per riprodurre un video. (Tre piattaforme supportano anche le proprietà che indicano se il video può essere messo in pausa o può essere spostato in una nuova posizione, ma queste proprietà sono applicabili per video in streaming, anziché file video, in modo che non sono supportate nel `VideoPlayer` descritte di seguito.)

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

Il `VideoPlayer` classe definisce una proprietà associabile solo reale denominata `Status` di tipo `VideoStatus`. Questa proprietà è definita in sola lettura perché deve essere impostato solo dal renderer di piattaforma:

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

In genere, una proprietà associabile di sola lettura sarebbe necessario un privato `set` funzione di accesso sul `Status` proprietà in modo che possa essere impostato all'interno della classe. Per un `View` derivative supportato dal renderer, tuttavia, la proprietà deve essere impostata all'esterno della classe, ma solo dal renderer di piattaforma.

Per questo motivo, viene definita un'altra proprietà con il nome `IVideoPlayerController.Status`. Questa è un'implementazione esplicita dell'interfaccia e viene reso possibile dal `IVideoPlayerController` dell'interfaccia che il `VideoPlayer` implementa classe:

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

Ciò è simile al modo in cui il [ `WebView` ](xref:Xamarin.Forms.WebView) controlli utilizza il [ `IWebViewController` ](xref:Xamarin.Forms.IWebViewController) interfaccia da implementare il `CanGoBack` e `CanGoForward` proprietà. (Vedere il codice sorgente del [ `WebView` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs) e relativo renderer per i dettagli.)

Questo rende possibile per una classe esterna `VideoPlayer` per impostare il `Status` proprietà facendo la `IVideoPlayerController` interfaccia. (Verrà visualizzato il codice al più presto.) La proprietà può essere impostata da altre classi oltre, ma è improbabile che da impostare inavvertitamente. In particolare, il `Status` non può essere impostata tramite l'associazione dati.

Per assistere i renderer di mantenere l'impostazione `Status` proprietà aggiornata, il `VideoPlayer` classe definisce un `UpdateStatus` evento che viene attivato ogni decimo di secondo:

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

### <a name="the-ios-status-setting"></a>L'impostazione di stato iOS

IOS `VideoPlayerRenderer` imposta un gestore per il `UpdateStatus` eventi (e si disconnette tale gestore quando sottostante `VideoPlayer` elemento è assente) e utilizza il gestore per impostare il `Status` proprietà:

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

Due proprietà della `AVPlayer` deve essere accessibile: la [ `Status` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.Status/) vlastnosti typu `AVPlayerStatus` e la [ `TimeControlStatus` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.TimeControlStatus/) vlastnosti typu `AVPlayerTimeControlStatus`. Si noti che il `Element` proprietà (ovvero il `VideoPlayer`) necessario eseguire il cast `IVideoPlayerController` per impostare il `Status` proprietà.

### <a name="the-android-status-setting"></a>L'impostazione dello stato di Android

Il [ `IsPlaying` ](https://developer.xamarin.com/api/property/Android.Widget.VideoView.IsPlaying/) proprietà nel Android `VideoView` è un valore booleano che indica solo se il video è in fase di riproduzione o in pausa. Per determinare se il `VideoView` possibile né play né interrompono il video ancora, il `Prepared` eventi di `VideoView` devono essere gestiti. Questi due gestori sono impostati nel `OnElementChanged` metodo e disconnesse durante la `Dispose` eseguire l'override:

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

Il `UpdateStatus` gestore Usa il `isPrepared` campo (impostato `Prepared` gestore) e il `IsPlaying` proprietà da impostare il `Status` proprietà:

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

### <a name="the-uwp-status-setting"></a>L'impostazione di stato della piattaforma UWP

La piattaforma UWP `VideoPlayerRenderer` Usa il `UpdateStatus` evento, ma non sia necessario per l'impostazione di `Status` proprietà. Il `MediaElement` definisce un [ `CurrentStateChanged` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentStateChanged) eventi che consente il renderer di ricevere una notifica quando il [ `CurrentState` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentState) proprietà è stata modificata. La proprietà viene scollegata nel `Dispose` eseguire l'override:

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

Il `CurrentState` proprietà è di tipo [ `MediaElementState` ](/uwp/api/windows.ui.xaml.media.mediaelementstate)e con facilità mappe in `VideoStatus`:

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

Usando i caratteri Unicode per simbolico **riprodurre**, **Sospendi**, e **arrestare** immagini risulta problematico. Il [tecnici vari](https://unicode-table.com/en/blocks/miscellaneous-technical/) sezione dello standard Unicode definisce tre caratteri simbolo apparentemente adeguati a questo scopo. Questi sono:

- 0x23F5 (Media verso destra triangolo nero) o &#x23F5; per la **riprodurre**
- 0x23F8 (barra verticale doppia) o &#x23F8; per la **pausa**
- 0x23F9 (quadrato nero) o &#x23F9; per la **arrestare**

Indipendentemente dal fatto che come questi simboli visualizzati nel browser e browser diversi li gestisce in modi diversi, non vengono visualizzati in modo coerente sulle piattaforme supportate da xamarin. Forms. In dispositivi iOS e UWP, il **pausa** e **arrestare** caratteri hanno un aspetto di grafico, con un piano bianco e uno sfondo blu 3D. Ciò non avviene in Android, in cui il simbolo è semplicemente blu. Tuttavia, i punti di codice 0x23F5 per **riprodurre** è privo di stesso aspetto su UWP che non lo è nemmeno è supportata in iOS e Android.

Per questo motivo, i punti di codice 0x23F5 non può essere utilizzato per **riprodurre**. È un ottimo sostituto:

- 0x25B6 (triangolo nero verso destra) o &#x25B6; per la **riprodurre**

Questo è supportato da tutte e tre le piattaforme ad eccezione del fatto che sia un semplice triangolo nero che non corrisponde a ' aspetto 3D **pausa** e **arrestare**. Una possibilità consiste nel seguire i punti di codice 0x25B6 con un codice di varianti:

- 0x25B6 seguita da 0xFE0F (variant 16) o &#x25B6; &#xFE0F; per **Riproduci**

Questo è ciò che viene usato nel tag riportati di seguito. In iOS, offre il **riprodurre** lo stesso aspetto 3D: simbolo il **Sospendi** e **arrestare** pulsanti, ma la variante non funziona in Android e UWP.

Il **trasporto Custom** pagina set il **AreTransportControlsEnabled** proprietà **false** e include un `ActivityIndicator` visualizzato durante il caricamento di video e due pulsanti. `DataTrigger` gli oggetti vengono usati per abilitare e disabilitare la `ActivityIndicator` e i pulsanti e per cambiare il primo pulsante tra **Riproduci** e **Sospendi**:

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

I trigger sono descritti in dettaglio nell'articolo [i trigger Data](~/xamarin-forms/app-fundamentals/triggers.md#data).

Il file code-behind con i gestori per il pulsante `Clicked` eventi:

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

Poiché `AutoPlay` è impostata su `false` nel **CustomTransport.xaml** file, sarà necessario premere il **riprodurre** pulsante quando viene abilitato per avviare il video. I pulsanti sono definiti in modo che i caratteri Unicode in precedenza sono accompagnati dagli equivalenti di testo. I pulsanti hanno un aspetto coerente in ogni piattaforma durante la riproduzione del video:

[![Trasporto personalizzato a cui è assegnato](custom-transport-images/customtransportplaying-small.png "trasporto personalizzato a cui è assegnato")](custom-transport-images/customtransportplaying-large.png#lightbox "trasporto personalizzato a cui è assegnato")

Ma in Android e UWP, il **riprodurre** pulsante ha un aspetto molto diverso quando viene messo in pausa il video:

[![Trasporto personalizzato sospesa](custom-transport-images/customtransportpaused-small.png "trasporto personalizzato in pausa")](custom-transport-images/customtransportpaused-large.png#lightbox "trasporto personalizzato in pausa")

In un'applicazione di produzione sarà probabilmente necessario usare le proprie immagini bitmap per i pulsanti per ottenere uniformità.


## <a name="related-links"></a>Collegamenti correlati

- [Demo video Windows Media Player (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
