---
title: ''
description: Questo articolo illustra come implementare un renderer video player personalizzato in ogni piattaforma, usando Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d026ecc3288da155aefb0f68ee0c70721106c0da
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84133744"
---
# <a name="creating-the-platform-video-players"></a>Creazione di lettori video per le piattaforme

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

La soluzione [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) contiene tutto il codice per l'implementazione di un lettore video per Xamarin.Forms . Contiene anche una serie di pagine che illustrano come usare il lettore video in un'applicazione. Tutto il codice `VideoPlayer` e i renderer di piattaforma si trovano in cartelle di progetto denominate `FormsVideoLibrary` e usano anche lo spazio dei nomi `FormsVideoLibrary`. Questo semplifica la copia dei file nella propria applicazione e la definizione di riferimenti alle classi.

## <a name="the-video-player"></a>Lettore video

La [`VideoPlayer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/FormsVideoLibrary/VideoPlayer.cs) classe fa parte della libreria **VideoPlayerDemos** .NET standard condivisa tra le piattaforme. Deriva da `View`:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
    }
}
```

I membri di questa classe (e l'interfaccia `IVideoPlayerController`) sono descritti negli articoli seguenti.

Ogni piattaforma contiene una classe denominata `VideoPlayerRenderer` che a sua volta contiene il codice specifico della piattaforma per l'implementazione di un lettore video. L'attività principale di questo renderer è la creazione di un lettore video per la piattaforma.

### <a name="the-ios-player-view-controller"></a>Controller di visualizzazione del lettore per iOS

L'implementazione di un lettore video in iOS comporta l'uso di diverse classi. L'applicazione crea prima un [`AVPlayerViewController`](xref:AVKit.AVPlayerViewController) oggetto e quindi imposta la [`Player`](xref:AVKit.AVPlayerViewController.Player*) proprietà su un oggetto di tipo [`AVPlayer`](xref:AVFoundation.AVPlayer) . Quando viene assegnata una sorgente video al lettore sono necessarie classi aggiuntive.

Come tutti i renderer, iOS [`VideoPlayerRenderer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/FormsVideoLibrary/VideoPlayerRenderer.csVideoPlayerRenderer.cs) contiene un `ExportRenderer` attributo che identifica la `VideoPlayer` visualizzazione con il renderer:

```csharp
using System;
using System.ComponentModel;
using System.IO;

using AVFoundation;
using AVKit;
using CoreMedia;
using Foundation;
using UIKit;

using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.iOS.VideoPlayerRenderer))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
    }
}
```

Generalmente, un renderer che imposta un controllo della piattaforma deriva dalla [`ViewRenderer<View, NativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs) classe, dove `View` è il Xamarin.Forms `View` derivato (in questo caso `VideoPlayer` ) ed `NativeView` è un derivato iOS `UIView` per la classe renderer. Per questo renderer, tale argomento generico viene semplicemente impostato su `UIView` per motivi che verranno citati di seguito.

Quando un renderer si basa su un derivato `UIViewController` (come quello corrente), la classe deve eseguire l'override della proprietà `ViewController` e restituire il controller di visualizzazione, in questo caso `AVPlayerViewController`. Questo è lo scopo del campo `_playerViewController`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Create AVPlayerViewController
                    _playerViewController = new AVPlayerViewController();

                    // Set Player property to AVPlayer
                    player = new AVPlayer();
                    _playerViewController.Player = player;

                    // Use the View from the controller as the native control
                    SetNativeControl(_playerViewController.View);
                }
                ···
            }
        }
        ···
    }
}
```

Il compito principale della sostituzione `OnElementChanged` è verificare se la proprietà `Control` è `null` e in questo caso creare un controllo di piattaforma e passarlo al metodo `SetNativeControl`. In questo caso tale oggetto è disponibile solo nella proprietà `View` di `AVPlayerViewController`. Questo derivato `UIView` risulta essere una classe privata denominata `AVPlayerView`, ma poiché si tratta di una classe privata non è possibile specificarla in modo esplicito come secondo argomento generico per `ViewRenderer`.

In genere la proprietà `Control` della classe renderer fa quindi riferimento all'elemento `UIView` usato per implementare il renderer, ma in questo caso la proprietà `Control` non viene usata in un'altra posizione.

### <a name="the-android-video-view"></a>Visualizzazione video Android

Il renderer Android per `VideoPlayer` è basato sulla classe Android [`VideoView`](xref:Android.Widget.VideoView) . Tuttavia, se `VideoView` viene usato da solo per riprodurre un video in un' Xamarin.Forms applicazione, il video riempie l'area assegnata per `VideoPlayer` senza mantenere le proporzioni corrette. Per questo motivo (come si vedrà a breve), `VideoView` viene impostato come elemento figlio di un elemento `RelativeLayout` per Android. Una `using` direttiva definisce `ARelativeLayout` per distinguerla da Xamarin.Forms `RelativeLayout` ed è il secondo argomento generico in `ViewRenderer` :

```csharp
using System;
using System.ComponentModel;
using System.IO;

using Android.Content;
using Android.Media;
using Android.Widget;
using ARelativeLayout = Android.Widget.RelativeLayout;

using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.Droid.VideoPlayerRenderer))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        public VideoPlayerRenderer(Context context) : base(context)
        {
        }
        ···
    }
}
```

A partire da Xamarin.Forms 2,5, i renderer Android devono includere un costruttore con un `Context` argomento.

L'override `OnElementChanged` consente di creare sia `VideoView` che `RelativeLayout` e imposta i parametri del layout per `VideoView` al fine di centrarlo in `RelativeLayout`.

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Save the VideoView for future reference
                    videoView = new VideoView(Context);

                    // Put the VideoView in a RelativeLayout
                    ARelativeLayout relativeLayout = new ARelativeLayout(Context);
                    relativeLayout.AddView(videoView);

                    // Center the VideoView in the RelativeLayout
                    ARelativeLayout.LayoutParams layoutParams =
                        new ARelativeLayout.LayoutParams(LayoutParams.MatchParent, LayoutParams.MatchParent);
                    layoutParams.AddRule(LayoutRules.CenterInParent);
                    videoView.LayoutParameters = layoutParams;

                    // Handle a VideoView event
                    videoView.Prepared += OnVideoViewPrepared;

                    // Use the RelativeLayout as the native control
                    SetNativeControl(relativeLayout);
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            base.Dispose(disposing);
        }

        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
    }
}
```

Un gestore per l'evento `Prepared` viene allegato in questo metodo e rimosso nel metodo `Dispose`. Questo evento viene attivato quando `VideoView` dispone di informazioni sufficienti per avviare la riproduzione di un file video.

### <a name="the-uwp-media-element"></a>Elemento multimediale della piattaforma UWP

Nel piattaforma UWP (Universal Windows Platform) (UWP), il lettore video più comune è [`MediaElement`](xref:Windows.UI.Xaml.Controls.MediaElement) . Questa documentazione di `MediaElement` indica che [`MediaPlayerElement`](xref:Windows.UI.Xaml.Controls.MediaPlayerElement) deve essere usato invece quando è necessario supportare solo le versioni di Windows 10 a partire dalla Build 1607.

La sostituzione `OnElementChanged` deve creare un elemento `MediaElement`, impostare due gestori eventi e passare l'oggetto `MediaElement` a `SetNativeControl`:

```csharp
using System;
using System.ComponentModel;

using Windows.Storage;
using Windows.Storage.Streams;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.UWP.VideoPlayerRenderer))]

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
                    MediaElement mediaElement = new MediaElement();
                    SetNativeControl(mediaElement);

                    mediaElement.MediaOpened += OnMediaElementMediaOpened;
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                Control.MediaOpened -= OnMediaElementMediaOpened;
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }        
        ···
    }
}
```

I due gestori eventi vengono disconnessi nell'evento `Dispose` per il renderer.

## <a name="showing-the-transport-controls"></a>Visualizzazione dei controlli di trasporto

Tutti i lettori video inclusi nelle piattaforme supportano un set predefinito di controlli di trasporto, che includono pulsanti per la riproduzione e la messa in pausa e una barra per indicare la posizione corrente nel video e consentire lo spostamento in una nuova posizione.

La classe `VideoPlayer` definisce una proprietà denominata `AreTransportControlsEnabled` e imposta il valore predefinito su `true`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // AreTransportControlsEnabled property
        public static readonly BindableProperty AreTransportControlsEnabledProperty =
            BindableProperty.Create(nameof(AreTransportControlsEnabled), typeof(bool), typeof(VideoPlayer), true);

        public bool AreTransportControlsEnabled
        {
            set { SetValue(AreTransportControlsEnabledProperty, value); }
            get { return (bool)GetValue(AreTransportControlsEnabledProperty); }
        }
        ···
    }
}
```

Anche se questa proprietà dispone delle funzioni di accesso `set` e `get`, il renderer deve gestire casi solo quando la proprietà è impostata. La funzione di accesso `get` restituisce semplicemente il valore corrente della proprietà.

Le proprietà come `AreTransportControlsEnabled` vengono gestite nei renderer di piattaforma in due modi:

- La prima volta è quando Xamarin.Forms Crea un `VideoPlayer` elemento. Questo è indicato nella sostituzione `OnElementChanged` del renderer quando la proprietà `NewElement` non è `null`. In questa fase il renderer può impostare il proprio lettore video della piattaforma dal valore iniziale della proprietà, definito in `VideoPlayer`.

- Se la proprietà in `VideoPlayer` cambia in un secondo momento, viene chiamato il metodo `OnElementPropertyChanged` nel renderer. In questo modo il renderer può aggiornare il lettore video della piattaforma in base alla nuova impostazione della proprietà.

Le sezioni seguenti illustrano la gestione della proprietà `AreTransportControlsEnabled` in ogni piattaforma.

### <a name="ios-playback-controls"></a>Controlli di riproduzione iOS

La proprietà di iOS `AVPlayerViewController` che governa la visualizzazione dei controlli di trasporto è [`ShowsPlaybackControls`](xref:AVKit.AVPlayerViewController.ShowsPlaybackControls*) . Ecco come è impostata tale proprietà in `VideoViewRenderer` per iOS:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            ((AVPlayerViewController)ViewController).ShowsPlaybackControls = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

La proprietà `Element` del renderer fa riferimento alla classe `VideoPlayer`.

### <a name="the-android-media-controller"></a>Controller multimediale per Android

Per visualizzare i controlli di trasporto in Android è necessario creare un [`MediaController`](xref:Android.Widget.MediaController) oggetto e associarlo all' `VideoView` oggetto. I meccanismi sono illustrati nel metodo `SetAreTransportControlsEnabled`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            if (Element.AreTransportControlsEnabled)
            {
                mediaController = new MediaController(Context);
                mediaController.SetMediaPlayer(videoView);
                videoView.SetMediaController(mediaController);
            }
            else
            {
                videoView.SetMediaController(null);

                if (mediaController != null)
                {
                    mediaController.SetMediaPlayer(null);
                    mediaController = null;
                }
            }
        }
        ···
    }
}
```

### <a name="the-uwp-transport-controls-property"></a>Proprietà per i controlli di trasporto della piattaforma UWP

UWP `MediaElement` definisce una proprietà denominata [`AreTransportControlsEnabled`](xref:Windows.UI.Xaml.Controls.MediaElement.AreTransportControlsEnabled*) , in modo che la proprietà venga impostata dalla `VideoPlayer` proprietà con lo stesso nome:

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
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            Control.AreTransportControlsEnabled = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

È necessaria un'altra proprietà per iniziare a riprodurre un video: si tratta della proprietà `Source` essenziale che fa riferimento a un file video. L'implementazione della proprietà `Source` è descritta nell'articolo successivo, [Riproduzione di un video Web](web-videos.md).

## <a name="related-links"></a>Collegamenti correlati

- [Demo lettore video (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
