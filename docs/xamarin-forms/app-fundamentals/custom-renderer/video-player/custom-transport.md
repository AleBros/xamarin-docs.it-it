---
title: Controlli di trasporto video personalizzati
description: Questo articolo illustra come implementare controlli di trasporto personalizzati in un'applicazione lettore video usando Xamarin.Forms .
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c72f68f327b6c0df6959d37ed4bf390e3bd7f31d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135005"
---
# <a name="custom-video-transport-controls"></a>Controlli di trasporto video personalizzati

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

I controlli di trasporto di un lettore video includono i pulsanti che eseguono le funzioni di **riproduzione**, **pausa** e **arresto**. Questi pulsanti sono identificati in genere con icone note anziché testo e le funzioni di **riproduzione** e **pausa** sono in genere unite in un unico pulsante.

Per impostazione predefinita, `VideoPlayer` visualizza i controlli di trasporto supportati da ogni piattaforma. Quando si imposta la proprietà `AreTransportControlsEnabled` su `false`, questi controlli vengono soppressi. È quindi possibile controllare `VideoPlayer` a livello di codice o rendere disponibili i propri controlli di trasporto.

## <a name="the-play-pause-and-stop-methods"></a>Metodi Play, Pause e Stop

La classe `VideoPlayer` definisce tre metodi denominati `Play`, `Pause` e `Stop` che vengono implementati tramite l'attivazione degli eventi:

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

I gestori di questi eventi vengono impostati dalla classe `VideoPlayerRenderer` in ogni piattaforma, come illustrato di seguito:

### <a name="ios-transport-implementations"></a>Implementazioni di trasporto iOS

La versione iOS di `VideoPlayerRenderer` usa il metodo `OnElementChanged` per impostare i gestori di questi tre eventi quando la proprietà `NewElement` non è `null` e rimuove i gestori degli eventi quando `OldElement` non è `null`:

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

I gestori degli eventi vengono implementati tramite la chiamata dei metodi nell'oggetto `AVPlayer`. Poiché non è presente alcun metodo `Stop` per `AVPlayer`, il metodo viene simulato mettendo in pausa il video e spostando la posizione all'inizio.

### <a name="android-transport-implementations"></a>Implementazioni di trasporto Android

L'implementazione Android è simile all'implementazione iOS. I gestori per le tre funzioni vengono impostati quando `NewElement` non è `null` e rimossi quando `OldElement` non è `null`:

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

Le tre funzioni chiamano i metodi definiti da `VideoView`.

### <a name="uwp-transport-implementations"></a>Implementazioni di trasporto UWP

L'implementazione UWP delle tre funzioni di trasporto è molto simile alle implementazioni iOS e Android:

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

## <a name="the-video-player-status"></a>Stato del lettore video

L'implementazione delle funzioni di **riproduzione**, **pausa** e **arresto** non è sufficiente per supportare i controlli di trasporto. Spesso i comandi **Riproduci** e **Pausa** vengono implementati con lo stesso pulsante di cui viene modificato l'aspetto per indicare se il video è attualmente in riproduzione o in pausa. Inoltre, se il video non è ancora stato caricato, è possibile che il pulsante non sia abilitato.

Questi requisiti implicano che il lettore video debba rendere disponibile uno stato corrente che indica se è attualmente in riproduzione o in pausa o se non è ancora pronto per riprodurre il video. (Sebbene ogni piattaforma supporti anche proprietà che indicano se il video può essere messo in pausa o spostato in una nuova posizione, queste proprietà sono applicabili ai video in streaming e non ai file video e di conseguenza non sono supportate nel `VideoPlayer` descritto qui).

Il progetto **VideoPlayerDemos** include un'enumerazione `VideoStatus` con tre membri:

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

La classe `VideoPlayer` definisce una proprietà associabile di sola lettura denominata `Status` di tipo `VideoStatus`. Questa proprietà è definita come proprietà di sola lettura poiché deve essere impostata solo dal renderer di piattaforma:

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

In genere, una proprietà associabile di sola lettura ha una funzione di accesso `set` privata nella proprietà `Status` in modo che possa essere impostata dall'interno della classe. Tuttavia, per una derivata `View` supportata dai renderer, la proprietà deve essere impostata dall'esterno della classe ma solo dal renderer della piattaforma.

Per questo motivo, viene definita un'altra proprietà con il nome `IVideoPlayerController.Status`. Questa è un'implementazione esplicita dell'interfaccia ed è resa possibile dall'interfaccia `IVideoPlayerController` implementata dalla classe `VideoPlayer`:

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

Questo metodo è simile al modo in cui il [`WebView`](xref:Xamarin.Forms.WebView) controllo utilizza l' [`IWebViewController`](xref:Xamarin.Forms.IWebViewController) interfaccia per implementare le `CanGoBack` `CanGoForward` proprietà e. Per informazioni dettagliate, vedere il codice sorgente di [`WebView`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs) e i relativi renderer.

Ciò consente a una classe esterna a `VideoPlayer` di impostare la proprietà `Status` facendo riferimento all'interfaccia `IVideoPlayerController`. Il codice verrà visualizzato a breve. La proprietà può essere impostata anche da altre classi, ma è improbabile che venga impostata inavvertitamente. In particolare, la proprietà `Status` non può essere impostata tramite un'associazione di dati.

Per fare in modo che i renderer mantengano questa proprietà `Status` aggiornata, la classe `VideoPlayer` definisce un evento `UpdateStatus` che viene attivato ogni decimo di secondo:

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

### <a name="the-ios-status-setting"></a>Impostazione di stato iOS

`VideoPlayerRenderer` iOS imposta un gestore per l'evento `UpdateStatus` (e disconnette il gestore quando l'elemento `VideoPlayer` è assente) e usa il gestore per impostare la proprietà `Status`:

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

`AVPlayer`È necessario accedere a due proprietà: la [`Status`](xref:AVFoundation.AVPlayer.Status*) proprietà di tipo `AVPlayerStatus` e la [`TimeControlStatus`](xref:AVFoundation.AVPlayer.TimeControlStatus*) proprietà di tipo `AVPlayerTimeControlStatus` . Si noti che è necessario che venga eseguito il cast della proprietà `Element` (ovvero `VideoPlayer`) in `IVideoPlayerController` per impostare la proprietà `Status`.

### <a name="the-android-status-setting"></a>Impostazione di stato Android

La [`IsPlaying`](xref:Android.Widget.VideoView.IsPlaying) proprietà di Android `VideoView` è un valore booleano che indica solo se il video viene riprodotto o sospeso. Per determinare se `VideoView` non può ancora riprodurre né mettere in pausa il video, è necessario che venga gestito l'evento `Prepared` di `VideoView`. Questi due gestori sono impostati nel metodo `OnElementChanged` e disconnessi durante l'override di `Dispose`:

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

Il gestore `UpdateStatus` usa il campo `isPrepared` (impostato nel gestore `Prepared`) e la proprietà `IsPlaying` per impostare la proprietà `Status`:

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

### <a name="the-uwp-status-setting"></a>Impostazione di stato UWP

`VideoPlayerRenderer` UWP usa l'evento `UpdateStatus`, ma l'evento non è necessario per l'impostazione della proprietà `Status`. `MediaElement`Definisce un [`CurrentStateChanged`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentStateChanged) evento che consente al renderer di ricevere una notifica quando la [`CurrentState`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentState*) proprietà viene modificata. La proprietà viene scollegata nell'override `Dispose`:

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

La `CurrentState` proprietà è di tipo [`MediaElementState`](/uwp/api/windows.ui.xaml.media.mediaelementstate) ed è facilmente mappata in `VideoStatus` :

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

## <a name="play-pause-and-stop-buttons"></a>Pulsanti Play, Pause e Stop

L'uso di caratteri Unicode per le immagini simboliche di **Play**, **Pause** e **Stop** è complesso. La sezione [Miscellaneous Technical](https://unicode-table.com/en/blocks/miscellaneous-technical/) (Informazioni tecniche varie) dello standard Unicode definisce tre caratteri simbolo adeguati a questo scopo. Si tratta di:

- 0x23F5 (triangolo nero medio rivolto verso destra) oppure &#x23F5; per **Play**
- 0x23F8 (due barre verticali) oppure &#x23F8; per **Pause**
- 0x23F9 (quadrato nero) oppure &#x23F9; per **Stop**

Indipendentemente dal modo in cui questi simboli vengono visualizzati nel browser (e diversi browser li gestiscono in modi diversi), non vengono visualizzati in modo coerente sulle piattaforme supportate da Xamarin.Forms . Nei dispositivi iOS e UWP i caratteri **Pause** e **Stop** vengono rappresentati con uno sfondo 3D blu e un primo piano bianco. In Android invece il simbolo è solo blu. Tuttavia, il punto di codice 0x23F5 per **Play** non ha lo stesso aspetto nella piattaforma UWP e non è supportato in iOS e Android.

Per questo motivo, il punto di codice 0x23F5 non può essere usato per **Play**. Una buona alternativa è rappresentata da:

- 0x25B6 (triangolo nero rivolto verso destra) oppure &#x25B6; per **Play**

Questo elemento è supportato da ogni piattaforma ma è costituito da un triangolo nero semplice diverso dalla rappresentazione 3D di **Pause** e **Stop**. Una soluzione possibile consiste nello specificare dopo il punto di codice 0x25B6 un codice variante:

- 0x25B6 seguito da 0xFE0F (variante 16) oppure &#x25B6;&#xFE0F; per **Play**

Questa è la soluzione usata nel markup illustrato di seguito. In iOS viene applicata al simbolo **Play** la stessa rappresentazione 3D dei pulsanti **Pause** e **Stop**, ma il codice variante non può essere usato in Android e UWP.

La pagina **Custom Transport** (Trasporto personalizzato) imposta la proprietà **AreTransportControlsEnabled** su **false** e include `ActivityIndicator` visualizzato durante il caricamento del video e due pulsanti. `DataTrigger`gli oggetti vengono usati per abilitare e disabilitare i `ActivityIndicator` pulsanti e e per cambiare il primo pulsante tra **Play** e **pause**:

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

I trigger di dati sono descritti in dettaglio nell'articolo [Trigger di dati](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers).

Il file code-behind include i gestori per gli eventi `Clicked` del pulsante:

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

Poiché `AutoPlay` è impostato su `false` nel file **CustomTransport.xaml**, sarà necessario premere il pulsante **Play** quando viene abilitato per avviare il video. I pulsanti sono definiti in modo che i caratteri Unicode descritti in precedenza siano accompagnati dagli equivalenti di testo. I pulsanti hanno un aspetto coerente in ogni piattaforma durante la riproduzione del video:

[![Riproduzione del trasporto personalizzato](custom-transport-images/customtransportplaying-small.png "Riproduzione del trasporto personalizzato")](custom-transport-images/customtransportplaying-large.png#lightbox "Riproduzione del trasporto personalizzato")

Tuttavia, in Android e UWP il pulsante **Play** ha un aspetto molto diverso quando il video è in pausa:

[![Il trasporto personalizzato è stato sospeso](custom-transport-images/customtransportpaused-small.png "Il trasporto personalizzato è stato sospeso")](custom-transport-images/customtransportpaused-large.png#lightbox "Il trasporto personalizzato è stato sospeso")

In un'applicazione di produzione è probabile che si desideri usare le proprie immagini bitmap per i pulsanti per ottenere uniformità visiva.

## <a name="related-links"></a>Collegamenti correlati

- [Demo lettore video (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
