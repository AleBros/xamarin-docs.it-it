---
title: Caricamento delle risorse dell'applicazione video
description: Questo articolo illustra come caricare video archiviati come risorse dell'applicazione in un'applicazione lettore video con Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 17e9e7061e4329431a0f34abdbbb616a1aff1b43
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171326"
---
# <a name="loading-application-resource-videos"></a>Caricamento delle risorse dell'applicazione video

I renderer personalizzati per la visualizzazione `VideoPlayer` sono in grado di riprodurre file video incorporati nei singoli progetti di piattaforma come risorse dell'applicazione. Tuttavia, la versione corrente di `VideoPlayer` non può accedere alle risorse incorporate in una libreria .NET Standard.

Per caricare queste risorse, creare un'istanza di `ResourceVideoSource` impostando la proprietà `Path` sul nome di file (o sulla cartella e il nome di file) della risorsa. In alternativa, è possibile chiamare il metodo statico `VideoSource.FromResource` per fare riferimento alla risorsa. Impostare quindi l'oggetto `ResourceVideoSource` sulla proprietà `Source` di `VideoPlayer`.

## <a name="storing-the-video-files"></a>Archiviazione dei file video

L'archiviazione di un file video nel progetto per la piattaforma è diversa per ogni piattaforma.

### <a name="ios-video-resources"></a>Risorse video iOS

In un progetto iOS è possibile archiviare un video nella cartella **Risorse** o in una sottocartella della cartella **Risorse**. Il file video deve avere `BundleResource` come `Build Action`. Impostare la proprietà `Path` di `ResourceVideoSource` sul nome di file, ad esempio, **File.mp4** per un file nella cartella **Risorse** o su **Cartella/File.mp4**, dove **Cartella** è una sottocartella della cartella **Risorse**.

Nella soluzione **VideoPlayerDemos** il progetto **VideoPlayerDemos.iOS** contiene una sottocartella di **Risorse** denominata **Videos** che contiene un file denominato **iOSApiVideo.mp4**. Si tratta di un breve video che illustra come usare il sito Web Xamarin per trovare la documentazione per la classe `AVPlayerViewController` iOS.

### <a name="android-video-resources"></a>Risorse video Android

In un progetto Android i video devono essere archiviati in una sottocartella della cartella **Risorse** denominata **raw**. La cartella **raw** non può contenere sottocartelle. Assegnare al file video `AndroidResource` come `Build Action`. Impostare la proprietà `Path` di `ResourceVideoSource` sul nome del file, ad esempio **File.mp4**.

Il progetto **VideoPlayerDemos.Android** contiene una sottocartella della cartella **Risorse** denominata **raw**, che contiene un file denominato **AndroidApiVideo.mp4**.

### <a name="uwp-video-resources"></a>Risorse video UWP

In un progetto UWP (Universal Windows Platform) è possibile archiviare i video in qualsiasi cartella del progetto. Assegnare al file `Content` come `Build Action`. Impostare la proprietà `Path` di `ResourceVideoSource` su cartella e nome di file, ad esempio **Cartella/Video.mp4**.

Il progetto **VideoPlayerDemos.UWP** contiene una cartella denominata **Videos** con il file **UWPApiVideo.mp4**.

## <a name="loading-the-video-files"></a>Caricamento dei file video

Ognuna delle classi del renderer della piattaforma contiene codice nel relativo metodo `SetSource` per il caricamento dei file video archiviati come risorse.

### <a name="ios-resource-loading"></a>Caricamento di risorse iOS

La versione iOS di `VideoPlayerRenderer` usa il metodo `GetUrlForResource` di `NSBundle` per il caricamento della risorsa. Il percorso completo deve essere suddiviso in nome di file, estensione e directory. Il codice usa la classe `Path` nello spazio dei nomi .NET `System.IO` per dividere il percorso del file in questi componenti:

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

Android `VideoPlayerRenderer` usa il nome di file e il nome di pacchetto per costruire un oggetto `Uri`. Il nome del pacchetto è il nome dell'applicazione, in questo caso **VideoPlayerDemos.Android**, che può essere ottenuto dalla proprietà statica `Context.PackageName`. L'oggetto `Uri` risultante viene quindi passato al metodo `SetVideoURI` di `VideoView`:

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

### <a name="uwp-resource-loading"></a>Caricamento di risorse UWP

La piattaforma UWP `VideoPlayerRenderer` costruisce un oggetto `Uri` per il percorso e lo imposta sulla proprietà `Source` di `MediaElement`:

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

## <a name="playing-the-resource-file"></a>Riproduzione del file di risorse

La pagina **Play Video Resource** nella soluzione **VideoPlayerDemos** usa la classe `OnPlatform` per specificare il file video per ogni piattaforma:

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

Se la risorsa di iOS è archiviata nella cartella **Risorse** e se la risorsa UWP è archiviata nella cartella radice del progetto, è possibile usare lo stesso nome di file per ogni piattaforma. In questo caso, è possibile impostare direttamente tale nome sulla proprietà `Source` di `VideoPlayer`.

Ecco la pagina in esecuzione:

[![Play Video Resource](loading-resources-images/playvideoresource-small.png "Play Video Resource")](loading-resources-images/playvideoresource-large.png#lightbox "Play Video Resource")

Si è visto come [caricare video da un URI Web](web-videos.md) e come riprodurre risorse incorporate. È anche possibile [caricare i video dalla raccolta video del dispositivo](accessing-library.md).


## <a name="related-links"></a>Collegamenti correlati

- [Video Player Demos (sample)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) (Esempio di demo di lettore video)
