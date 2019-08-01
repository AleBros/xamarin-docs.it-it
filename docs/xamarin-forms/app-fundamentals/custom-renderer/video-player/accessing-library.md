---
title: Accesso alla raccolta video del dispositivo
description: Questo articolo illustra come accedere alla raccolta video del dispositivo in un'applicazione lettore video con Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 219f6498dc1349d32c8f0b247a187df75f2a523a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650705"
---
# <a name="accessing-the-devices-video-library"></a>Accesso alla raccolta video del dispositivo

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

I dispositivi mobili e i computer desktop più moderni hanno la possibilità di registrare video usando la fotocamera del dispositivo. I video creati dall'utente vengono poi archiviati come file nel dispositivo. Questi file possono essere recuperati dalla raccolta immagini e riprodotti dalla classe `VideoPlayer`, come qualsiasi altro video.

## <a name="the-photo-picker-dependency-service"></a>Servizio di dipendenza per la selezione foto

Ognuna delle piattaforme include una funzionalità che consente all'utente di selezionare una foto o un video dalla raccolta immagini del dispositivo. Il primo passaggio per la riproduzione di un video dalla raccolta immagini del dispositivo consiste nel creare un servizio di dipendenza che richiama la selezione immagini in ogni piattaforma. Il servizio di dipendenza descritto di seguito è molto simile a quello definito nell'articolo [**Selezione di una foto dalla Raccolta immagini**](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), ad eccezione del fatto che la selezione video restituisce un nome di file invece di un oggetto `Stream`.

Il progetto di libreria .NET Standard definisce un'interfaccia denominata `IVideoPicker` per il servizio di dipendenza:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPicker
    {
        Task<string> GetVideoFileAsync();
    }
}
```

Ognuna delle piattaforme contiene una classe denominata `VideoPicker` che implementa questa interfaccia.

### <a name="the-ios-video-picker"></a>Selezione video iOS

La classe `VideoPicker` iOS usa il [`UIImagePickerController`](xref:UIKit.UIImagePickerController) iOS per accedere alla raccolta immagini, specificando che deve essere limitata ai video (indicati come "movies") nella proprietà `MediaType` iOS. Si noti che `VideoPicker` implementa in modo esplicito l'interfaccia `IVideoPicker`. Si noti anche l'attributo `Dependency` che identifica la classe come un servizio di dipendenza. Esistono due requisiti che consentono a Xamarin.Forms di trovare il servizio di dipendenza nel progetto della piattaforma:

```csharp
using System;
using System.Threading.Tasks;
using UIKit;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.iOS.VideoPicker))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPicker : IVideoPicker
    {
        TaskCompletionSource<string> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<string> GetVideoFileAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.SavedPhotosAlbum,
                MediaTypes = new string[] { "public.movie" }
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<string>();
            return taskCompletionSource.Task;
        }

        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            if (args.MediaType == "public.movie")
            {
                taskCompletionSource.SetResult(args.MediaUrl.AbsoluteString);
            }
            else
            {
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }
    }
}
```

### <a name="the-android-video-picker"></a>Selezione video Android

L'implementazione Android di `IVideoPicker` richiede un metodo di callback che fa parte dell'attività dell'applicazione. Per questo motivo, la classe `MainActivity` definisce due proprietà, un campo e un metodo di callback:

```csharp
namespace VideoPlayerDemos.Droid
{
    ···
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            Current = this;
            ···
        }

        // Field, properties, and method for Video Picker
        public static MainActivity Current { private set; get; }

        public static readonly int PickImageId = 1000;

        public TaskCompletionSource<string> PickImageTaskCompletionSource { set; get; }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);

            if (requestCode == PickImageId)
            {
                if ((resultCode == Result.Ok) && (data != null))
                {
                    // Set the filename as the completion of the Task
                    PickImageTaskCompletionSource.SetResult(data.DataString);
                }
                else
                {
                    PickImageTaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

Il metodo `OnCreate` in `MainActivity` archivia la propria istanza nella proprietà statica `Current`. In questo modo l'implementazione di `IVideoPicker` può ottenere l'istanza `MainActivity` per avviare la selezione **Select Video**:

```csharp
using System;
using System.Threading.Tasks;
using Android.Content;
using Xamarin.Forms;

// Need application's MainActivity
using VideoPlayerDemos.Droid;

[assembly: Dependency(typeof(FormsVideoLibrary.Droid.VideoPicker))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPicker : IVideoPicker
    {
        public Task<string> GetVideoFileAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("video/*");
            intent.SetAction(Intent.ActionGetContent);

            // Get the MainActivity instance
            MainActivity activity = MainActivity.Current;

            // Start the picture-picker activity (resumes in MainActivity.cs)
            activity.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Video"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            activity.PickImageTaskCompletionSource = new TaskCompletionSource<string>();

            // Return Task object
            return activity.PickImageTaskCompletionSource.Task;
        }
    }
}
```

Le aggiunte all'oggetto `MainActivity` sono l'unico codice nella soluzione [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) che deve essere modificato all'interno del nomale codice dell'applicazione per supportare le classi `FormsVideoLibrary`.

### <a name="the-uwp-video-picker"></a>Selezione video UWP

L'implementazione di UWP dell'interfaccia `IVideoPicker` usa il [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) UWP. Inizia la ricerca di file dalla raccolta immagini limitando i tipi di file a MP4 e WMV (Windows Media Video):

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.Pickers;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.UWP.VideoPicker))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPicker : IVideoPicker
    {
        public async Task<string> GetVideoFileAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary
            };

            openPicker.FileTypeFilter.Add(".wmv");
            openPicker.FileTypeFilter.Add(".mp4");

            // Get a file and return the path
            StorageFile storageFile = await openPicker.PickSingleFileAsync();
            return storageFile?.Path;
        }
    }
}
```

## <a name="invoking-the-dependency-service"></a>Richiamare il servizio di dipendenza

La pagina **Play Library Video** del programma [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) dimostra come usare il servizio di dipendenza della selezione video. Il file XAML contiene un'istanza di `VideoPlayer` e un `Button` con etichetta **Show Video Library**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayLibraryVideoPage"
             Title="Play Library Video">
    <StackLayout>
        <video:VideoPlayer x:Name="videoPlayer"
                           VerticalOptions="FillAndExpand" />

        <Button Text="Show Video Library"
                Margin="10"
                HorizontalOptions="Center"
                Clicked="OnShowVideoLibraryClicked" />
    </StackLayout>
</ContentPage>
```

Il file code-behind contiene il gestore `Clicked` per `Button`. Per richiamare il servizio di dipendenza, è necessaria una chiamata a `DependencyService.Get` per ottenere l'implementazione di un'interfaccia `IVideoPicker` nel progetto della piattaforma. Il metodo `GetVideoFileAsync` viene quindi chiamato su quell'istanza:

```csharp
namespace VideoPlayerDemos
{
    public partial class PlayLibraryVideoPage : ContentPage
    {
        public PlayLibraryVideoPage()
        {
            InitializeComponent();
        }

       async void OnShowVideoLibraryClicked(object sender, EventArgs args)
        {
            Button btn = (Button)sender;
            btn.IsEnabled = false;

            string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();

            if (!String.IsNullOrWhiteSpace(filename))
            {
                videoPlayer.Source = new FileVideoSource
                {
                    File = filename
                };
            }

            btn.IsEnabled = true;
        }
    }
}
```

Il gestore `Clicked` usa quindi tale nome di file per creare un oggetto `FileVideoSource` e impostarlo sulla proprietà `Source` di `VideoPlayer`.

Ognuna delle classi `VideoPlayerRenderer` contiene codice nel relativo metodo `SetSource` per gli oggetti di tipo `FileVideoSource`. Questi tipi sono illustrati di seguito:

### <a name="handling-ios-files"></a>Gestione di file iOS

La versione iOS di `VideoPlayerRenderer` elabora gli oggetti `FileVideoSource` usando il metodo statico `Asset.FromUrl` con il nome del file. Viene così creato un oggetto `AVAsset` che rappresenta il file nella raccolta immagini del dispositivo:

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
            else if (Element.Source is FileVideoSource)
            {
                string uri = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-android-files"></a>Gestione di file Android

Quando si elaborano oggetti di tipo `FileVideoSource`, l'implementazione di Android di `VideoPlayerRenderer` usa il metodo `SetVideoPath` di `VideoView` per specificare il file nella raccolta immagini del dispositivo:

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
            else if (Element.Source is FileVideoSource)
            {
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    videoView.SetVideoPath(filename);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-uwp-files"></a>Gestione di file UWP

Quando si gestiscono gli oggetti di tipo `FileVideoSource`, l'implementazione UWP del metodo `SetSource` deve creare un oggetto `StorageFile`, aprire il file per la lettura e passare l'oggetto flusso al metodo `SetSource` di `MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is FileVideoSource)
            {
                // Code requires Pictures Library in Package.appxmanifest Capabilities to be enabled
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    StorageFile storageFile = await StorageFile.GetFileFromPathAsync(filename);
                    IRandomAccessStreamWithContentType stream = await storageFile.OpenReadAsync();
                    Control.SetSource(stream, storageFile.ContentType);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

Per ogni piattaforma, la riproduzione del video inizia quasi immediatamente dopo l'impostazione dell'origine del video, perché il file è presente nel dispositivo e non è necessario scaricarlo.



## <a name="related-links"></a>Collegamenti correlati

- [Video Player Demos (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) (Esempio di demo di lettore video)
- [Selezione di una foto dalla Raccolta immagini](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
