---
title: Il caricamento di risorse dell'applicazione video
description: Questo articolo illustra come caricare video archiviati come risorse dell'applicazione in un'applicazione di lettore video con xamarin. Forms.
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 17e9e7061e4329431a0f34abdbbb616a1aff1b43
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171326"
---
# <a name="loading-application-resource-videos"></a>Il caricamento di risorse dell'applicazione video

Il renderer personalizzati per il `VideoPlayer` visualizzazione sono in grado di riprodurre file video che sono stati incorporati nei singoli progetti di piattaforma come risorse dell'applicazione. Tuttavia, la versione corrente di `VideoPlayer` non è possibile accedere alle risorse incorporate in una libreria .NET Standard.

Per caricare queste risorse, creare un'istanza di `ResourceVideoSource` impostando la `Path` proprietà per il nome del file (o la cartella e nome file) della risorsa. In alternativa, è possibile chiamare il metodo statico `VideoSource.FromResource` metodo facciano riferimento alla risorsa. Quindi, impostare il `ResourceVideoSource` dell'oggetto per il `Source` proprietà di `VideoPlayer`.

## <a name="storing-the-video-files"></a>Archiviare i file video

L'archiviazione di un file video nel progetto di piattaforma è diversa per ogni piattaforma.

### <a name="ios-video-resources"></a>risorse video iOS

In un progetto iOS, è possibile archiviare un video nel **le risorse** cartella o una sottocartella della **risorse** cartella. Il file video deve avere una `Build Action` di `BundleResource`. Impostare il `Path` proprietà di `ResourceVideoSource` al nome del file, ad esempio, **MyFile.mp4** per un file nei **risorse** cartella, o **MyFolder/MyFile.mp4**, in cui **MyFolder** è una sottocartella della **risorse**.

Nel **VideoPlayerDemos** soluzione, il **VideoPlayerDemos.iOS** progetto contiene una sottocartella della **risorse** denominato **video** che contiene un file denominato **iOSApiVideo.mp4**. Si tratta di un breve video che illustra come usare il sito web Xamarin per trovare la documentazione per iOS `AVPlayerViewController` classe.

### <a name="android-video-resources"></a>Risorse video Android

In un progetto Android, devono essere archiviati i video in una sottocartella della **le risorse** denominate **raw**. Il **raw** non è possibile creare sottocartelle nella cartella. Assegnare al file video un' `Build Action` di `AndroidResource`. Impostare il `Path` proprietà di `ResourceVideoSource` al nome del file, ad esempio **MyFile.mp4**.

Il **VideoPlayerDemos.Android** progetto contiene una sottocartella della **risorse** denominato **raw**, che contiene un file denominato **AndroidApiVideo.mp4**.

### <a name="uwp-video-resources"></a>Risorse video UWP

In un progetto Universal Windows Platform, è possibile archiviare i video in qualsiasi cartella del progetto. Assegnare al file un' `Build Action` di `Content`. Impostare il `Path` proprietà di `ResourceVideoSource` per la cartella e il nome di file, ad esempio **MyFolder/MyVideo.mp4**.

Il **VideoPlayerDemos.UWP** progetto contiene una cartella denominata **video** con il file **UWPApiVideo.mp4**.

## <a name="loading-the-video-files"></a>Il caricamento dei file video

Ognuna delle classi renderer platform contiene codice nella relativa `SetSource` metodo di caricamento dei file video archiviati come risorse.

### <a name="ios-resource-loading"></a>caricamento delle risorse di iOS

La versione di iOS `VideoPlayerRenderer` utilizza le `GetUrlForResource` metodo `NSBundle` per il caricamento della risorsa. Il percorso completo deve essere suddiviso in un nome file, estensione e directory. Il codice Usa il `Path` classe in .NET `System.IO` dello spazio dei nomi per dividere il percorso del file in questi componenti:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void SetSource()
        {
            AVAsset asset = null;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhitespace(path))
                {
                    string directory = Path.GetDirectoryName(path);
                    string filename = Path.GetFileNameWithoutExtension(path);
                    string extension = Path.GetExtension(path).Substring(1);
                    NSUrl url = NSBundle.MainBundle.GetUrlForResource(filename, extension, directory);
                    asset = AVAsset.FromUrl(url);
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="android-resource-loading"></a>Caricamento delle risorse Android

Android `VideoPlayerRenderer` utilizza il nome del pacchetto e nome file per costruire un `Uri` oggetto. Il nome del pacchetto è il nome dell'applicazione, in questo caso **VideoPlayerDemos.Android**, che può essere ottenuto da statico `Context.PackageName` proprietà. I risultanti `Uri` oggetto viene quindi passato per il `SetVideoURI` metodo `VideoView`:

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
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string package = Context.PackageName;
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string filename = Path.GetFileNameWithoutExtension(path).ToLowerInvariant();
                    string uri = "android.resource://" + package + "/raw/" + filename;
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="uwp-resource-loading"></a>Caricamento delle risorse di piattaforma UWP

La piattaforma UWP `VideoPlayerRenderer` costruisce una `Uri` oggetto per il percorso e lo imposta sulle `Source` proprietà della `MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = "ms-appx:///" + (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    Control.Source = new Uri(path);
                    hasSetSource = true;
                }
            }
        }
        ···
    }
}
```

## <a name="playing-the-resource-file"></a>La riproduzione del file di risorse

Il **riprodurre Video Resource** nella pagina il **VideoPlayerDemos** soluzione utilizza il `OnPlatform` classe per specificare il file video per ogni piattaforma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayVideoResourcePage"
             Title="Play Video Resource">
    <video:VideoPlayer>
        <video:VideoPlayer.Source>
            <video:ResourceVideoSource>
                <video:ResourceVideoSource.Path>
                    <OnPlatform x:TypeArguments="x:String">
                        <On Platform="iOS" Value="Videos/iOSApiVideo.mp4" />
                        <On Platform="Android" Value="AndroidApiVideo.mp4" />
                        <On Platform="UWP" Value="Videos/UWPApiVideo.mp4" />
                    </OnPlatform>
                </video:ResourceVideoSource.Path>
            </video:ResourceVideoSource>
        </video:VideoPlayer.Source>
    </video:VideoPlayer>
</ContentPage>
```

Se la risorsa di iOS è archiviata nel **risorse** cartella, e se la risorsa UWP viene archiviata nella cartella radice del progetto, è possibile usare lo stesso nome file per ogni piattaforma. Se in questo caso, quindi è possibile impostare direttamente a tale nome la `Source` proprietà di `VideoPlayer`.

Ecco la pagina in esecuzione:

[![Riprodurre Video Resource](loading-resources-images/playvideoresource-small.png "riprodurre Video Resource")](loading-resources-images/playvideoresource-large.png#lightbox "riprodurre risorse Video")

Abbiamo visto come [video vengono caricati da un URI Web](web-videos.md) nonché come riprodurre le risorse incorporate. Inoltre, è possibile [video vengono caricati dal Catalogo video del dispositivo](accessing-library.md).


## <a name="related-links"></a>Collegamenti correlati

- [Demo video Windows Media Player (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
