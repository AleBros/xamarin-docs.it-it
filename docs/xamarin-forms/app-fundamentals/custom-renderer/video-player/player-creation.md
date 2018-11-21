---
title: Creazione di lettori video platform
description: Questo articolo illustra come implementare un renderer personalizzato lettore video su ogni piattaforma con xamarin. Forms.
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 0090ec798e8d7b1dfb9bd8e25f09d71ec0353b45
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171911"
---
# <a name="creating-the-platform-video-players"></a>Creazione di lettori video platform

Il [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) soluzione contiene tutto il codice per implementare un lettore video per xamarin. Forms. Contiene inoltre una serie di pagine che illustra come usare il lettore video all'interno di un'applicazione. Tutti i `VideoPlayer` il renderer di piattaforma e codice si trovano in cartelle di progetto denominate `FormsVideoLibrary`e anche usare lo spazio dei nomi `FormsVideoLibrary`. Ciò deve semplificarti copiare i file nella propria applicazione e le classi di riferimento.

## <a name="the-video-player"></a>Il lettore video

Il [ `VideoPlayer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/VideoPlayer.cs) classe fa parte il **VideoPlayerDemos** libreria .NET Standard che viene condiviso tra le piattaforme. Deriva da `View`:

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

I membri di questa classe (e `IVideoPlayerController` interface) sono descritti negli articoli seguenti.

Ognuna delle piattaforme contiene una classe denominata `VideoPlayerRenderer` che contiene il codice specifico della piattaforma per implementare un lettore video. L'attività principale di questo renderer consiste nel creare un lettore video per la piattaforma.

### <a name="the-ios-player-view-controller"></a>Il controller di visualizzazione iOS player

Quando si implementa un lettore video in iOS, sono coinvolti diverse classi. L'applicazione crea prima di tutto una [ `AVPlayerViewController` ](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) e quindi imposta la [ `Player` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.Player/) proprietà a un oggetto di tipo [ `AVPlayer` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayer/). Classi aggiuntive sono necessari quando il lettore viene assegnato un'origine video.

Come tutti i renderer, iOS [ `VideoPlayerRenderer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/VideoPlayerRenderer.cs) contiene un' `ExportRenderer` attributo che identifica il `VideoPlayer` visualizzazione con il renderer:

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

In genere un renderer che consente di impostare una piattaforma di controllo deriva dal [ `ViewRenderer<View, NativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs) (classe), dove `View` è di xamarin. Forms `View` derivato (in questo caso, `VideoPlayer`) e `NativeView` è iOS `UIView` derivativo della classe renderer. Per questo renderer, tale argomento generico viene semplicemente impostato `UIView`, per motivi di si vedrà a breve.

Quando un renderer si basa su un `UIViewController` derivato (quello corrente è), quindi la classe deve eseguire l'override di `ViewController` proprietà e restituire il controller di visualizzazione, in questo caso `AVPlayerViewController`. Vale a dire lo scopo del `_playerViewController` campo:

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

Il compito principale del `OnElementChanged` override consiste nel verificare se il `Control` è di proprietà `null` e, in questo caso, creare un controllo di piattaforma e passarlo al `SetNativeControl` (metodo). In questo caso, tale oggetto è disponibile solo dal `View` proprietà del `AVPlayerViewController`. Che `UIView` derivative risulta essere una classe privata denominata `AVPlayerView`, ma poiché è privato, non è possibile specificare in modo esplicito come secondo argomento generico per `ViewRenderer`.

A livello generale il `Control` proprietà della classe renderer si intende successivamente il `UIView` usato per implementare il renderer, ma in questo caso il `Control` proprietà non viene utilizzata in un' posizione.

### <a name="the-android-video-view"></a>La visualizzazione di video Android

Il renderer per Android `VideoPlayer` si basa su Android [ `VideoView` ](https://developer.xamarin.com/api/type/Android.Widget.VideoView/) classe. Tuttavia, se `VideoView` viene usata da sola per riprodurre un video in un'applicazione xamarin. Forms, i video riempimenti entro l'area per il `VideoPlayer` senza dover gestire le corrette proporzioni. Per questo motivo (come si vedrà a breve), il `VideoView` viene eseguito un elemento figlio di un Android `RelativeLayout`. Oggetto `using` direttiva definisce `ARelativeLayout` per distinguerlo da xamarin. Forms `RelativeLayout`, e il secondo argomento generico che si trova nel `ViewRenderer`:

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

A partire da xamarin. Forms 2.5, il renderer di Android deve includere un costruttore con un `Context` argomento.

Il `OnElementChanged` sostituzione consente di creare sia il `VideoView` e `RelativeLayout` e imposta i parametri del layout per il `VideoView` al centro all'interno di `RelativeLayout`.


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

Un gestore per il `Prepared` evento associato con questo metodo e scollegato nel `Dispose` (metodo). Questo evento viene generato al termine il `VideoView` sono disponibili informazioni sufficienti per avviare la riproduzione di un file video.

### <a name="the-uwp-media-element"></a>L'elemento di supporto della piattaforma UWP

In di Windows della piattaforma UWP (Universal), il lettore video più comune è [ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/). Documentazione della `MediaElement` indica che il [ `MediaPlayerElement` ](/uwp/api/windows.ui.xaml.controls.mediaplayerelement/) deve essere utilizzato invece quando è necessario supportare le versioni di partire da Windows 10 build 1607 solo.

Il `OnElementChanged` sostituzione deve creare un `MediaElement`, impostare un paio di gestori di eventi e passare la `MediaElement` oggetto `SetNativeControl`:

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

I due gestori vengono disconnessi nel `Dispose` evento per il renderer.

## <a name="showing-the-transport-controls"></a>Che mostra i controlli di trasporto

Tutti i lettori video inclusi le piattaforme supportano un set predefinito di controlli di trasporto che includono i pulsanti per la riproduzione e la sospensione e una barra per indicare la posizione corrente all'interno del video e spostare in una nuova posizione.

Il `VideoPlayer` classe definisce una proprietà denominata `AreTransportControlsEnabled` e imposta il valore predefinito su `true`:


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

Anche se questa proprietà ha entrambe `set` e `get` funzioni di accesso, il renderer deve gestire casi solo quando la proprietà è impostata. Il `get` della funzione di accesso restituisce semplicemente il valore corrente della proprietà.

Proprietà, ad esempio `AreTransportControlsEnabled` vengono gestiti nei renderer di piattaforma in due modi:

- La prima volta è durante la creazione di xamarin. Forms un `VideoPlayer` elemento. Ciò viene indicato nella `OnElementChanged` eseguire l'override del renderer quando il `NewElement` proprietà non `null`. A questo punto, è possibile impostare il renderer è lettore video propria piattaforma dal valore iniziale della proprietà come definito nel `VideoPlayer`.

- Se la proprietà in `VideoPlayer` cambia in un secondo momento, il `OnElementPropertyChanged` nel programma di rendering viene chiamato. In questo modo il renderer aggiornare il lettore video di piattaforma in base all'impostazione di proprietà di nuovo.

Le sezioni seguenti illustrano come il `AreTransportControlsEnabled` proprietà viene gestita in ogni piattaforma.

### <a name="ios-playback-controls"></a>controlli di riproduzione di iOS

La proprietà di iOS `AVPlayerViewController` che determina la visualizzazione del trasporto è controlli [ `ShowsPlaybackControls` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.ShowsPlaybackControls/). Ecco come tale proprietà è impostata in iOS `VideoViewRenderer`:

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

Il `Element` proprietà del renderer si intende il `VideoPlayer` classe.

### <a name="the-android-media-controller"></a>Il controller multimediali Android

In Android, la visualizzazione dei controlli di trasporto richiede la creazione di un [ `MediaController` ](https://developer.xamarin.com/api/type/Android.Widget.MediaController/) oggetto e la relativa associazione con il `VideoView` oggetto. Vengono illustrati i meccanismi di `SetAreTransportControlsEnabled` metodo:

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

### <a name="the-uwp-transport-controls-property"></a>La proprietà di controlli di trasporto UWP

La piattaforma UWP `MediaElement` definisce una proprietà denominata [ `AreTransportControlsEnabled` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_AreTransportControlsEnabled), in modo che viene impostata dal `VideoPlayer` proprietà lo stesso nome:

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

Una proprietà di altre è necessaria avviare la riproduzione di un video: questo è il fondamentale `Source` proprietà che fa riferimento a un file video. Implementa il `Source` proprietà è descritta nell'articolo successivo [riproduzione di un Video Web](web-videos.md).


## <a name="related-links"></a>Collegamenti correlati

- [Demo video Windows Media Player (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
