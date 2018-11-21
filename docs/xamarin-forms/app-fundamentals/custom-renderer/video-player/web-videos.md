---
title: Riproduzione di un video Web
description: Questo articolo illustra come riprodurre video web in un'applicazione di lettore video con xamarin. Forms.
ms.prod: xamarin
ms.assetid: 75781A10-865D-4BA8-8D6B-E3DA012922BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 7f40d0d11fc932121b4ff7789969bbb1e354024c
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172210"
---
# <a name="playing-a-web-video"></a>Riproduzione di un video Web

Il `VideoPlayer` classe definisce un `Source` proprietà utilizzata per specificare l'origine del file video, nonché un `AutoPlay` proprietà. `AutoPlay` include l'impostazione predefinita del `true`, il che significa che il video deve iniziare la riproduzione automaticamente dopo `Source` è stato impostato:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Source property
        public static readonly BindableProperty SourceProperty =
            BindableProperty.Create(nameof(Source), typeof(VideoSource), typeof(VideoPlayer), null);

        [TypeConverter(typeof(VideoSourceConverter))]
        public VideoSource Source
        {
            set { SetValue(SourceProperty, value); }
            get { return (VideoSource)GetValue(SourceProperty); }
        }

        // AutoPlay property
        public static readonly BindableProperty AutoPlayProperty =
            BindableProperty.Create(nameof(AutoPlay), typeof(bool), typeof(VideoPlayer), true);

        public bool AutoPlay
        {
            set { SetValue(AutoPlayProperty, value); }
            get { return (bool)GetValue(AutoPlayProperty); }
        }
        ···
    }
}
```

Il `Source` proprietà è di tipo `VideoSource`, che si basa sulla xamarin. Forms [ `ImageSource` ](xref:Xamarin.Forms.ImageSource) astratta classe e relativi tre derivati [ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource), [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource), e [ `StreamImageSource` ](xref:Xamarin.Forms.StreamImageSource). Nessuna opzione di flusso è disponibile per il `VideoPlayer` , tuttavia, poiché iOS e Android non supportano la riproduzione di un video da un flusso.

## <a name="video-sources"></a>Origini video

La classe astratta `VideoSource` classe è costituito esclusivamente da tre metodi statici che crea un'istanza di tre classi che derivano da `VideoSource`:

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        public static VideoSource FromUri(string uri)
        {
            return new UriVideoSource() { Uri = uri };
        }

        public static VideoSource FromFile(string file)
        {
            return new FileVideoSource() { File = file };
        }

        public static VideoSource FromResource(string path)
        {
            return new ResourceVideoSource() { Path = path };
        }
    }
}
```

Il `UriVideoSource` classe viene utilizzata per specificare un file scaricabile video con un URI. Definisce una singola proprietà di tipo `string`:

```csharp
namespace FormsVideoLibrary
{
    public class UriVideoSource : VideoSource
    {
        public static readonly BindableProperty UriProperty =
            BindableProperty.Create(nameof(Uri), typeof(string), typeof(UriVideoSource));

        public string Uri
        {
            set { SetValue(UriProperty, value); }
            get { return (string)GetValue(UriProperty); }
        }
    }
}
```

Gestione degli oggetti di tipo `UriVideoSource` è descritta di seguito.

Il `ResourceVideoSource` classe viene utilizzata per accedere ai file video che vengono archiviati come risorse incorporate nell'applicazione della piattaforma specificato anche con un `string` proprietà:

```csharp
namespace FormsVideoLibrary
{
    public class ResourceVideoSource : VideoSource
    {
        public static readonly BindableProperty PathProperty =
            BindableProperty.Create(nameof(Path), typeof(string), typeof(ResourceVideoSource));

        public string Path
        {
            set { SetValue(PathProperty, value); }
            get { return (string)GetValue(PathProperty); }
        }
    }
}
```

Gestione degli oggetti di tipo `ResourceVideoSource` è descritta nell'articolo [il caricamento di risorse dell'applicazione video](loading-resources.md). Il `VideoPlayer` classe non dispone di funzionalità per caricare un file video archiviato come una risorsa nella libreria .NET Standard.

Il `FileVideoSource` classe viene utilizzata per accedere ai file video da catalogo video del dispositivo. L'unica proprietà anche è di tipo `string`:

```csharp
namespace FormsVideoLibrary
{
    public class FileVideoSource : VideoSource
    {
        public static readonly BindableProperty FileProperty =
                  BindableProperty.Create(nameof(File), typeof(string), typeof(FileVideoSource));

        public string File
        {
            set { SetValue(FileProperty, value); }
            get { return (string)GetValue(FileProperty); }
        }
    }
}
```

Gestione degli oggetti di tipo `FileVideoSource` è descritta nell'articolo [l'accesso a catalogo Video del dispositivo](accessing-library.md).

Il `VideoSource` classe include una `TypeConverter` attributo che fa riferimento `VideoSourceConverter`:

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        ···
    }
}
```

Viene richiamato il convertitore di tipo quando il `Source` è impostata su una stringa in XAML. Di seguito è riportato il `VideoSourceConverter` classe:

```csharp
namespace FormsVideoLibrary
{
    public class VideoSourceConverter : TypeConverter
    {
        public override object ConvertFromInvariantString(string value)
        {
            if (!String.IsNullOrWhiteSpace(value))
            {
                Uri uri;
                return Uri.TryCreate(value, UriKind.Absolute, out uri) && uri.Scheme != "file" ?
                                VideoSource.FromUri(value) : VideoSource.FromResource(value);
            }

            throw new InvalidOperationException("Cannot convert null or whitespace to ImageSource");
        }
    }
}
```

Il `ConvertFromInvariantString` metodo tenta di convertire la stringa da un `Uri` oggetto. Se l'operazione riesce, e lo schema non è `file:`, il metodo restituisce un `UriVideoSource`. In caso contrario, restituisce un `ResourceVideoSource`.

## <a name="setting-the-video-source"></a>Impostando l'origine video

Tutta la logica che includono origini video viene implementata nei renderer di singole piattaforme. Le sezioni seguenti mostrano come i renderer di piattaforma riprodurre video quando le `Source` è impostata su un `UriVideoSource` oggetto.

### <a name="the-ios-video-source"></a>L'origine video iOS

Due sezioni del `VideoPlayerRenderer` sono coinvolti nella configurazione l'origine video del lettore video. Xamarin. Forms quando crea innanzitutto un oggetto di tipo `VideoPlayer`, il `OnElementChanged` metodo viene chiamato con il `NewElement` che impostare proprietà dell'oggetto arguments `VideoPlayer`. Il `OnElementChanged` chiamate al metodo `SetSource`:

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
                SetSource();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

In un secondo momento su, quando la `Source` proprietà viene modificata, il `OnElementPropertyChanged` metodo viene chiamato con un `PropertyName` proprietà di "Origine", e `SetSource` viene chiamata nuovamente.

Per riprodurre un file video in iOS, un oggetto di tipo [ `AVAsset` ](https://developer.xamarin.com/api/type/AVFoundation.AVAsset/) prima di tutto viene creato per incapsulare il file video, e che viene usato per creare un' [ `AVPlayerItem` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/), che viene quindi trasferita nel `AVPlayer`oggetto. Ecco come la `SetSource` metodo consente di gestire i `Source` proprietà quando è di tipo `UriVideoSource`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        ···
        void SetSource()
        {
            AVAsset asset = null;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
            if (asset != null)
            {
                playerItem = new AVPlayerItem(asset);
            }
            else
            {
                playerItem = null;
            }

            player.ReplaceCurrentItemWithPlayerItem(playerItem);

            if (playerItem != null && Element.AutoPlay)
            {
                player.Play();
            }
        }
        ···
    }
}
```

Il `AutoPlay` non dispone di proprietà analogia nelle classi video iOS, in modo che la proprietà viene esaminata la fine del `SetSource` metodo da chiamare il `Play` metodo sul `AVPlayer` oggetto.

In alcuni casi, i video ha continuato a cui è assegnato dopo la pagina con il `VideoPlayer` ci si torna alla home page. Per arrestare il video, il `ReplaceCurrentItemWithPlayerItem` è impostata nella `Dispose` eseguire l'override:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void Dispose(bool disposing)
        {
            base.Dispose(disposing);

            if (player != null)
            {
                player.ReplaceCurrentItemWithPlayerItem(null);
            }
        }
        ···
    }
}
```

### <a name="the-android-video-source"></a>L'origine video Android

Android `VideoPlayerRenderer` deve impostare l'origine del lettore video quando il `VideoPlayer` viene innanzitutto creato e versioni successive quando il `Source` le modifiche alle proprietà:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                ···
            }
        }
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

Il `SetSource` metodo gestisce gli oggetti di tipo `UriVideoSource` chiamando `SetVideoUri` sul `VideoView` con Android `Uri` oggetto creato dalla stringa di URI. Il `Uri` classe completo qui per distinguerlo da .NET `Uri` classe:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void SetSource()
        {
            isPrepared = false;
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···

            if (hasSetSource && Element.AutoPlay)
            {
                videoView.Start();
            }
        }
        ···
    }
}
```

Android `VideoView` non ha un corrispondente `AutoPlay` proprietà, in modo che il `Start` metodo viene chiamato se è stato impostato un nuovo video.

Non c'è una differenza tra il comportamento di iOS e Android renderer se il `Source` proprietà di `VideoPlayer` è impostata su `null`, oppure se il `Uri` proprietà di `UriVideoSource` è impostata su `null` o in una stringa vuota. Se il lettore video iOS sta attualmente riproducendo un video, e `Source` è impostata su `null` (o la stringa viene `null` o lasciare vuoto), `ReplaceCurrentItemWithPlayerItem` viene chiamato con `null` valore. Il video corrente viene sostituito e viene interrotta la riproduzione.

Android non supporta una struttura analoga. Se il `Source` è impostata su `null`, il `SetSource` metodo semplicemente la Ignora e il video corrente continua a essere riprodotto.

### <a name="the-uwp-video-source"></a>L'origine video UWP

La piattaforma UWP `MediaElement` definisce un `AutoPlay` proprietà, che viene gestita nel programma di rendering come qualsiasi altra proprietà:

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
                SetSource();
                SetAutoPlay();
                ···    
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            else if (args.PropertyName == VideoPlayer.AutoPlayProperty.PropertyName)
            {
                SetAutoPlay();
            }
            ···
        }
        ···
    }
}
```

Il `SetSource` proprietà gestisce una `UriVideoSource` oggetto impostando il `Source` proprietà della `MediaElement` per .NET `Uri` valore, o a `null` se il `Source` proprietà di `VideoPlayer` è impostato su `null`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    Control.Source = new Uri(uri);
                    hasSetSource = true;
                }
            }
            ···
            if (!hasSetSource)
            {
                Control.Source = null;
            }
        }

        void SetAutoPlay()
        {
            Control.AutoPlay = Element.AutoPlay;
        }
        ···
    }
}
```

## <a name="setting-a-url-source"></a>Impostazione di un'origine di URL

Con l'implementazione di queste proprietà nei renderer di tre, è possibile riprodurre un video da un'origine di URL. Il **riprodurre Video Web** pagina il [ **VideoPlayDemos** ]( https://developer.xamarin.com/samples/xamarin-forms/customrenderers/videoplayerdemos/index.md) programma è definito dal file XAML seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

Il `VideoSourceConverter` classe converte la stringa in un `UriVideoSource`. Quando si passa al **riprodurre Video Web** pagina, il video inizia il caricamento e avvia la riproduzione quando una quantità sufficiente di dati è stata scaricata e memorizzato nel buffer. Il video è circa 10 minuti di lunghezza:

[![Riproduci Video Web](web-videos-images/playwebvideo-small.png "riprodurre Video Web")](web-videos-images/playwebvideo-large.png#lightbox "riprodurre Video Web")

In ognuna delle piattaforme, i controlli di trasporto dissolvenza se non si è abituati, ma può essere ripristinati per visualizzare toccando il video.

È possibile impedire il video di avvio automatico impostando il `AutoPlay` proprietà `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

Sarà necessario premere il **riprodurre** per avviare il video.

Analogamente, è possibile eliminare la visualizzazione dei controlli di trasporto impostando la `AreTransportControlsEnabled` proprietà `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

Se entrambe le proprietà è impostata su `false`, quindi avvia la riproduzione di video e non vi sarà alcuna possibilità di avviarlo. È necessario chiamare `Play` da file code-behind o per creare i propri controlli di trasporto, come descritto nell'articolo [implementazione di controlli di trasporto Video personalizzati](custom-transport.md).

Il **app** file include le risorse necessarie per due video aggiuntivi:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.App">
    <Application.Resources>
        <ResourceDictionary>

            <video:UriVideoSource x:Key="ElephantsDream"
                                  Uri="https://archive.org/download/ElephantsDream/ed_hd_512kb.mp4" />

            <video:UriVideoSource x:Key="BigBuckBunny"
                                  Uri="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

            <video:UriVideoSource x:Key="Sintel"
                                  Uri="https://archive.org/download/Sintel/sintel-2048-stereo_512kb.mp4" />

        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Per fare riferimento a una di queste altre film, è possibile sostituire l'URL esplicito nel **PlayWebVideo.xaml** file con un `StaticResource` estensione di markup, nel qual caso `VideoSourceConverter` non è necessario per creare il `UriVideoSource` oggetto:

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

In alternativa, è possibile impostare il `Source` proprietà da un file video in un `ListView`, come descritto nell'articolo successivo [associazione di origini Video al lettore](source-bindings.md).





## <a name="related-links"></a>Collegamenti correlati

- [Demo video Windows Media Player (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
