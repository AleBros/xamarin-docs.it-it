---
title: L'accesso a Catalogo video del dispositivo
description: Questo articolo illustra come accedere a Catalogo video del dispositivo in un'applicazione di lettore video con xamarin. Forms.
ms.prod: xamarin
ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 619469e4c4fd3901491c20d6215ec0a25c49f69d
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171183"
---
# <a name="accessing-the-devices-video-library"></a>L'accesso a Catalogo video del dispositivo

Più moderni dispositivi mobili e computer desktop hanno la possibilità di registrare video usando la fotocamera del dispositivo. I video che consente di creare un utente vengono quindi archiviati come file nel dispositivo. Questi file possono essere recuperati dalla libreria di immagini e riprodotto dal `VideoPlayer` classe esattamente come qualsiasi altro video.

## <a name="the-photo-picker-dependency-service"></a>Il servizio di dipendenza selezione foto

Ognuna delle piattaforme include una caratteristica che consente all'utente di selezionare una foto o video dalla libreria di immagini del dispositivo. Il primo passaggio nella riproduzione di un video dalla libreria di immagini del dispositivo sta creando un servizio di dipendenza che richiama la selezione di immagini in ogni piattaforma. Il servizio di dipendenza descritto di seguito è molto simile a quello definito nel [ **selezionando una foto dalla raccolta immagini** ](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md) articolo, ad eccezione del fatto che il selettore video restituisce un nome di file anziché un `Stream`oggetto.

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

### <a name="the-ios-video-picker"></a>Il selettore di video iOS

IOS `VideoPicker` Usa iOS [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) per accedere alla libreria di immagini, che deve essere limitato ai video (detto "movies") in iOS `MediaType` proprietà. Si noti che `VideoPicker` implementa in modo esplicito il `IVideoPicker` interfaccia. Si noti inoltre il `Dependency` attributo che identifica la classe come un servizio di dipendenza. Questi sono i due requisiti che consentono di xamarin. Forms trovare il servizio di dipendenza nel progetto di piattaforma:

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

### <a name="the-android-video-picker"></a>Il selettore di video Android

L'implementazione di Android `IVideoPicker` richiede un metodo di callback che fa parte dell'attività dell'applicazione. Per questo motivo, il `MainActivity` classe definisce due proprietà, un campo e un metodo di callback:

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

Il `OnCreate` nel metodo `MainActivity` memorizza la propria istanza in statico `Current` proprietà. In questo modo l'implementazione di `IVideoPicker` per ottenere il `MainActivity` istanza per l'avvio di **Video selezionare** selezione:

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

Le aggiunte per il `MainActivity` oggetto sono l'unico codice nel [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) soluzione in cui deve essere modificata per supportare il codice dell'applicazione normale di `FormsVideoLibrary` classi.

### <a name="the-uwp-video-picker"></a>Il selettore di video UWP

L'implementazione di UWP del `IVideoPicker` interfaccia Usa la piattaforma UWP [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/). Inizia la ricerca di file con la libreria di immagini e limita i tipi di file MP4 e WMV (Windows Media Video):

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

Il **riprodurre Video di libreria** pagina della [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) programma illustra come usare il servizio di dipendenza di selezione dei video. Il file XAML contiene una `VideoPlayer` istanza e un `Button` etichettato **Mostra la libreria di Video**:

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

Il file code-behind contiene il `Clicked` gestore per il `Button`. Richiamare il servizio di dipendenza richiede una chiamata a `DependencyService.Get` per ottenere l'implementazione di un `IVideoPicker` interfaccia nel progetto di piattaforma. Il `GetVideoFileAsync` viene quindi chiamato il metodo su quell'istanza:

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

Il `Clicked` gestore Usa quindi tale nome di file per creare un `FileVideoSource` oggetto e impostarlo sulle `Source` proprietà del `VideoPlayer`.

Ognuna del `VideoPlayerRenderer` classi contiene il codice nella relativa `SetSource` metodo per gli oggetti di tipo `FileVideoSource`. Queste sono le seguenti:

### <a name="handling-ios-files"></a>Gestione di file di iOS

La versione di iOS `VideoPlayerRenderer` processi `FileVideoSource` oggetti usando il metodo statico `Asset.FromUrl` metodo con il nome del file. Ciò crea un `AVAsset` oggetto che rappresenta il file nella libreria di immagini del dispositivo:

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

### <a name="handling-android-files"></a>Gestione di file di Android

Quando si elaborano oggetti di tipo `FileVideoSource`, l'implementazione di Android `VideoPlayerRenderer` Usa le `SetVideoPath` metodo `VideoView` per specificare il file nella libreria di immagini del dispositivo:

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

### <a name="handling-uwp-files"></a>Gestione di file di UWP

Quando si gestiscono gli oggetti di tipo `FileVideoSource`, l'implementazione di UWP del `SetSource` metodo deve creare un `StorageFile` dell'oggetto, aprire il file per la lettura e passare l'oggetto di flusso per il `SetSource` metodo il `MediaElement`:

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

Per ogni piattaforma, il video viene riprodotto quasi immediatamente dopo il video di origine è impostata perché il file è presente nel dispositivo e non deve necessariamente essere scaricato.



## <a name="related-links"></a>Collegamenti correlati

- [Demo video Windows Media Player (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
- [Selezione di una foto dalla raccolta immagini di](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
