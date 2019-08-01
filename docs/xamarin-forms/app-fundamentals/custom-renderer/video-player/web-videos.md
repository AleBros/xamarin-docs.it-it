---
title: Riproduzione di un video Web
description: Questo articolo illustra come riprodurre un'applicazione lettore video con Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 75781A10-865D-4BA8-8D6B-E3DA012922BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 9beff615c39fc34b5a58a93d309bb20543cad77f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650410"
---
# <a name="playing-a-web-video"></a>Riproduzione di un video Web

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

La classe `VideoPlayer` definisce una proprietà `Source`, che viene usata per specificare l'origine del file video, e una proprietà `AutoPlay`. Il valore predefinito della proprietà `AutoPlay` è `true`, vale a dire il video verrà riprodotto automaticamente dopo aver impostato la proprietà `Source`:

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

La proprietà `Source` è di tipo `VideoSource`, che si basa sulla classe astratta [`ImageSource`](xref:Xamarin.Forms.ImageSource) di Xamarin.Forms e le sue tre derivate [`UriImageSource`](xref:Xamarin.Forms.UriImageSource), [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) e [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource). Non sono tuttavia disponibili opzioni di flusso per la classe `VideoPlayer`. iOS e Android non supportano infatti la riproduzione di un video da un flusso.

## <a name="video-sources"></a>Origini video

La classe astratta `VideoSource` è costituita esclusivamente da tre metodi statici che creano un'istanza delle tre classi derivate da `VideoSource`:

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

La classe `UriVideoSource` specifica un file video scaricabile con un URI. Definisce una singola proprietà di tipo `string`:

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

La gestione degli oggetti di tipo `UriVideoSource` è descritta di seguito.

La classe `ResourceVideoSource` viene usata per accedere ai file video archiviati come risorse incorporate nell'applicazione della piattaforma, specificata anche con una proprietà `string`:

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

La gestione degli oggetti di tipo `ResourceVideoSource` è descritta nell'articolo [Caricamento delle risorse dell'applicazione video](loading-resources.md). La classe `VideoPlayer` non può caricare un file video archiviato come risorsa nella libreria .NET Standard.

La classe `FileVideoSource` viene usata per accedere ai file video dalla libreria video del dispositivo. Anche la singola proprietà è di tipo `string`:

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

La gestione degli oggetti di tipo `FileVideoSource` è descritta nell'articolo [Accesso alla raccolta di video del dispositivo](accessing-library.md).

La classe `VideoSource` include un attributo `TypeConverter` che fa riferimento alla classe `VideoSourceConverter`:

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

Questo convertore di tipo viene richiamato quando la proprietà `Source` è impostata su una stringa in XAML. Di seguito è riportata la classe `VideoSourceConverter`:

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

Il metodo `ConvertFromInvariantString` tenta di convertire la stringa in un oggetto `Uri`. Se l'operazione riesce, e lo schema non è `file:`, il metodo restituirà una classe `UriVideoSource`. In caso contrario, restituirà `ResourceVideoSource`.

## <a name="setting-the-video-source"></a>Impostazione dell'origine video

Tutta la logica restante che riguarda le origini video viene implementata nei singoli renderer della piattaforma. Le sezioni seguenti illustrano come i renderer della piattaforma riproducono i video se la proprietà `Source` è impostata su un oggetto `UriVideoSource`.

### <a name="the-ios-video-source"></a>Origine video iOS

Sono necessarie due sezioni di `VideoPlayerRenderer` per impostare l'origine video del lettore video. Se Xamarin.Forms crea prima un oggetto di tipo `VideoPlayer`, il metodo `OnElementChanged` viene chiamato con la proprietà `NewElement` degli oggetti arguments impostati su tale oggetto `VideoPlayer`. Il metodo `OnElementChanged` chiama `SetSource`:

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

In un secondo momento, quando la proprietà `Source` viene cambiata, il metodo `OnElementPropertyChanged` viene chiamato con una proprietà `PropertyName` di "Source" e viene nuovamente chiamato il metodo `SetSource`.

Per riprodurre un file video in iOS, viene prima creato un oggetto di tipo [`AVAsset`](xref:AVFoundation.AVAsset) per incapsulare il file video, il quale viene usato per creare un oggetto [`AVPlayerItem`](xref:AVFoundation.AVPlayerItem), che sarà poi passato all'oggetto `AVPlayer`. Di seguito è riportato come il metodo `SetSource` gestisce la proprietà `Source` quando è di tipo `UriVideoSource`:

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

La proprietà `AutoPlay` non ha analogie con le classi video iOS. Viene quindi esaminata al termine del metodo `SetSource` per chiamare il metodo `Play` sull'oggetto `AVPlayer`.

In alcuni casi è successo che la riproduzione dei video continuasse nonostante la pagina con l'oggetto `VideoPlayer` fosse tornata alla home page. Per arrestare il video, viene impostato anche `ReplaceCurrentItemWithPlayerItem` nell'override `Dispose`:

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

### <a name="the-android-video-source"></a>Origine video Android

Per la classe `VideoPlayerRenderer` Android è necessario impostare l'origine video del lettore quando la classe `VideoPlayer` viene creata la prima volta e successivamente quando viene cambiata la proprietà `Source`:

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

Il metodo `SetSource` gestisce gli oggetti di tipo `UriVideoSource` chiamando `SetVideoUri` su `VideoView` con un oggetto `Uri` Android creato dall'URI del valore di stringa. Qui la classe `Uri` è completa per essere distinta dalla classe `Uri` di .NET:

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

L'oggetto `VideoView` Android non ha una proprietà `AutoPlay` corrispondente, quindi viene chiamato il metodo `Start` se è stato impostato un nuovo video.

Il comportamento dei renderer iOS e Android è diverso se la proprietà `Source` di `VideoPlayer` è impostata su `null`, oppure se la proprietà `Uri` di `UriVideoSource` è impostata su `null` o una stringa vuota. Se il lettore video iOS sta riproducendo un video e la proprietà `Source` è impostata su `null` (oppure la stringa è `null` o vuota), viene chiamato l'oggetto `ReplaceCurrentItemWithPlayerItem` con il valore `null`. Il video corrente viene sostituito e ne viene arrestata la riproduzione.

Android non supporta un funzionamento simile. Se la proprietà `Source` è impostata su `null`, il metodo `SetSource` semplicemente la ignora e la riproduzione del video non viene interrotta.

### <a name="the-uwp-video-source"></a>Origine video della piattaforma UWP

L'oggetto `MediaElement` della piattaforma UWP definisce una proprietà `AutoPlay`, che il renderer gestisce come qualsiasi altra proprietà:

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

La proprietà `SetSource` gestisce un oggetto `UriVideoSource` impostando la proprietà `Source` di `MediaElement` sul valore `Uri` di .NET, oppure su `null` se la proprietà `Source` di `VideoPlayer` è impostata su `null`:

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

## <a name="setting-a-url-source"></a>Impostazione di un'origine URL

Dopo aver implementato queste proprietà nei tre renderer, è possibile riprodurre un video da un'origine URL. La pagina **Play Web Video** (Riproduci video Web) nel programma [**VideoPlayDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) è definita dal file XAML seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

La classe `VideoSourceConverter` converte la stringa in un oggetto `UriVideoSource`. Quando si passa alla pagina **Play Web Video** (Riproduci video Web), il video viene caricato e inizia a essere riprodotto non appena viene scaricato e memorizzato nel buffer un numero sufficiente di dati. Il video ha una lunghezza di circa 10 minuti:

[![Play Web Video](web-videos-images/playwebvideo-small.png "Play Web Video")](web-videos-images/playwebvideo-large.png#lightbox "Play Web Video")

In ogni piattaforma i controlli di trasporto si dissolvono se non vengono usati. Possono essere tuttavia ripristinati e visualizzati toccando il video.

È possibile impedire che il video inizi automaticamente impostando la proprietà `AutoPlay` su `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

Sarà necessario premere il pulsante **Play** (Riproduci) per avviare il video.

Analogamente, è possibile nascondere i controlli di trasporto impostando la proprietà `AreTransportControlsEnabled` su `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

Se entrambe le proprietà vengono impostate su `false`, il video non sarà riprodotto e non sarà possibile avviarne la riproduzione. Sarà necessario chiamare `Play` da file code-behind oppure creare controlli di trasporto personalizzati, come descritto nell'articolo [Implementazione di controlli di trasporto video personalizzati](custom-transport.md).

Il file **App.xaml** include le risorse per due video aggiuntivi:

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

Per fare riferimento a uno di questi altri video, è possibile sostituire l'URL esplicito nel file **PlayWebVideo.xaml** con un'estensione di markup `StaticResource`. In questo caso la classe `VideoSourceConverter` non è necessaria per creare l'oggetto `UriVideoSource`:

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

In alternativa, è possibile impostare la proprietà `Source` da un file video in un oggetto `ListView`, come descritto nel prossimo articolo [Associazione di origini video al lettore](source-bindings.md).





## <a name="related-links"></a>Collegamenti correlati

- [Demo lettore video (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
