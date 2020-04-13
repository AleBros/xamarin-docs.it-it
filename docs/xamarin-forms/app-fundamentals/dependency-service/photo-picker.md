---
title: Selezione di una foto dalla Raccolta immagini
description: Questo articolo illustra come usare la classe DependencyService di Xamarin.Forms per scegliere una foto dalla Raccolta immagini del telefono.
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 6669dbaff3cfb5b929261352b8db046b35ec5b4f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "76020697"
---
# <a name="picking-a-photo-from-the-picture-library"></a>Selezione di una foto dalla Raccolta immagini

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

Questo articolo illustra in dettaglio la creazione di un'applicazione che consente all'utente di scegliere una foto dalla Raccolta immagini del telefono. Poiché Xamarin.Forms non include questa funzionalità, [`DependencyService`](xref:Xamarin.Forms.DependencyService) è necessario utilizzare per accedere alle API native in ogni piattaforma.

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

In primo luogo creare un'interfaccia nel codice condiviso che esprime la funzionalità desiderata. Nel caso di un'applicazione per la selezione di foto è sufficiente un solo metodo. Questo è definito [`IPhotoPickerService`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceDemos/Services/IPhotoPickerService.cs) nell'interfaccia nella libreria .NET Standard del codice di esempio:

```csharp
namespace DependencyServiceDemos
{
    public interface IPhotoPickerService
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

Il metodo `GetImageStreamAsync` viene definito come asincrono perché deve restituire un valore in modo rapido, ma non può restituire un oggetto `Stream` per la foto selezionata fino a quando l'utente non ha esplorato la raccolta di immagini e non ha selezionato un'immagine.

Questa interfaccia viene implementata in tutte le piattaforme mediante codice specifico per la piattaforma.

## <a name="ios-implementation"></a>Implementazione iOS

L'implementazione iOS `IPhotoPickerService` dell'interfaccia utilizza il [`UIImagePickerController`](xref:UIKit.UIImagePickerController) come descritto nella ricetta Scegli una foto dalla raccolta e [**codice**](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery) [di esempio](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

L'implementazione di iOS è contenuta nella [`PhotoPickerService`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceDemos.iOS/Services/PhotoPickerService.cs) classe nel progetto iOS del codice di esempio. Per rendere visibile questa classe al manager `DependencyService`, la classe deve essere identificata con un attributo [`assembly`] di tipo `Dependency`, deve essere pubblica e deve implementare in modo esplicito l'interfaccia `IPhotoPickerService`:

```csharp
[assembly: Dependency (typeof (PhotoPickerService))]
namespace DependencyServiceDemos.iOS
{
    public class PhotoPickerService : IPhotoPickerService
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<Stream> GetImageStreamAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.PhotoLibrary,
                MediaTypes = UIImagePickerController.AvailableMediaTypes(UIImagePickerControllerSourceType.PhotoLibrary)
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<Stream>();
            return taskCompletionSource.Task;
        }
        ...
    }
}

```

Il metodo `GetImageStreamAsync` crea un `UIImagePickerController` e lo inizializza per selezionare immagini dalla raccolta di foto. Sono necessari due gestori eventi: uno per quando l'utente seleziona una foto e l'altro per quando l'utente annulla la visualizzazione della raccolta di foto. Quindi `PresentModalViewController` visualizza la raccolta di foto all'utente.

A questo punto il metodo `GetImageStreamAsync` deve restituire un oggetto `Task<Stream>` al codice che origina la chiamata. Questa attività viene completata solo quando l'utente ha terminato l'interazione con la raccolta di foto e viene chiamato uno dei gestori eventi. Per situazioni come [`TaskCompletionSource`](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) questa, la classe è essenziale. La classe specifica un oggetto `Task` del tipo generico appropriato per la restituzione dal metodo `GetImageStreamAsync` e può ricevere il segnale successivamente, una volta completata l'attività.

Il gestore eventi `FinishedPickingMedia` viene chiamato quando l'utente ha selezionato un'immagine. Tuttavia il gestore specifica un oggetto `UIImage` e `Task` deve restituire un oggetto .NET `Stream`. Questa operazione viene eseguita `UIImage` in due passaggi: l'oggetto viene prima `NSData` convertito in `NSData` un file PNG o `Stream` JPEG in memoria memorizzato in un oggetto e quindi l'oggetto viene convertito in un oggetto .NET. Una chiamata al metodo `SetResult` per l'oggetto `TaskCompletionSource` completa l'operazione specificando l'oggetto `Stream`:

```csharp
namespace DependencyServiceDemos.iOS
{
    public class PhotoPickerService : IPhotoPickerService
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;
        ...
        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            UIImage image = args.EditedImage ?? args.OriginalImage;

            if (image != null)
            {
                // Convert UIImage to .NET Stream object
                NSData data;
                if (args.ReferenceUrl.PathExtension.Equals("PNG") || args.ReferenceUrl.PathExtension.Equals("png"))
                {
                    data = image.AsPNG();
                }
                else
                {
                    data = image.AsJPEG(1);
                }
                Stream stream = data.AsStream();

                UnregisterEventHandlers();

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
            }
            else
            {
                UnregisterEventHandlers();
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            UnregisterEventHandlers();
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }

        void UnregisterEventHandlers()
        {
            imagePicker.FinishedPickingMedia -= OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled -= OnImagePickerCancelled;
        }
    }
}
```

Un'applicazione iOS richiede l'autorizzazione dell'utente per accedere alla raccolta di foto del telefono. Aggiungere il codice seguente alla sezione `dict` del file Info.plist:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```

## <a name="android-implementation"></a>Implementazione Android

L'implementazione Android usa la tecnica descritta nel file recipe [**Selezionare un'immagine**](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image) e nel relativo [codice di esempio](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image). Tuttavia il metodo chiamato quando l'utente ha selezionato un'immagine dalla raccolta immagini è una sostituzione `OnActivityResult` in una classe che deriva da `Activity`. Per questo motivo, la classe normale [`MainActivity`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceDemos.Android/MainActivity.cs) nel progetto Android è stata integrata `OnActivityResult` con un campo, una proprietà e un override del metodo:

```csharp
public class MainActivity : FormsAppCompatActivity
{
    internal static MainActivity Instance { get; private set; }  

    protected override void OnCreate(Bundle savedInstanceState)
    {
        // ... 
        Instance = this;
    }
    // ...
    // Field, property, and method for Picture Picker
    public static readonly int PickImageId = 1000;

    public TaskCompletionSource<Stream> PickImageTaskCompletionSource { set; get; }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent intent)
    {
        base.OnActivityResult(requestCode, resultCode, intent);

        if (requestCode == PickImageId)
        {
            if ((resultCode == Result.Ok) && (intent != null))
            {
                Android.Net.Uri uri = intent.Data;
                Stream stream = ContentResolver.OpenInputStream(uri);

                // Set the Stream as the completion of the Task
                PickImageTaskCompletionSource.SetResult(stream);
            }
            else
            {
                PickImageTaskCompletionSource.SetResult(null);
            }
        }
    }
}
```

La sostituzione `OnActivityResult` indica il file immagine selezionato con un oggetto Android `Uri`, che può essere convertito in un oggetto .NET `Stream` chiamando il metodo `OpenInputStream` dell'oggetto `ContentResolver` che è stato ottenuto dalla proprietà `ContentResolver` dell'attività.

Come l'implementazione iOS, l'implementazione Android usa un elemento `TaskCompletionSource` per indicare quando l'attività è stata completata. L'oggetto `TaskCompletionSource` è definito come proprietà pubblica nella classe `MainActivity`. In questo modo la proprietà [`PhotoPickerService`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceDemos.Android/Services/PhotoPickerService.cs) a cui viene fatto riferimento nella classe nel progetto Android. Questa è la classe con il metodo `GetImageStreamAsync`:

```csharp
[assembly: Dependency(typeof(PhotoPickerService))]
namespace DependencyServiceDemos.Droid
{
    public class PhotoPickerService : IPhotoPickerService
    {
        public Task<Stream> GetImageStreamAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("image/*");
            intent.SetAction(Intent.ActionGetContent);

            // Start the picture-picker activity (resumes in MainActivity.cs)
            MainActivity.Instance.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Picture"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            MainActivity.Instance.PickImageTaskCompletionSource = new TaskCompletionSource<Stream>();

            // Return Task object
            return MainActivity.Instance.PickImageTaskCompletionSource.Task;
        }
    }
}
```

Questo metodo accede alla classe `MainActivity` per vari scopi: per la proprietà `Instance`, per il campo `PickImageId`, per la proprietà `TaskCompletionSource` e per chiamare `StartActivityForResult`. Questo metodo è definito dalla classe `FormsAppCompatActivity`, che è la classe di base di `MainActivity`.

## <a name="uwp-implementation"></a>Implementazione UWP

A differenza delle implementazioni per iOS e Android, l'implementazione del selettore foto per la piattaforma UWP (Universal Windows Platform) non richiede la classe `TaskCompletionSource`. La [`PhotoPickerService`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceDemos.UWP/Services/PhotoPickerService.cs) classe [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) utilizza la classe per ottenere l'accesso alla libreria di foto. Dato che il metodo `PickSingleFileAsync` di `FileOpenPicker` è asincrono, il metodo `GetImageStreamAsync` può usare semplicemente `await` con tale metodo (e con altri metodi) e restituire un oggetto `Stream`:

```csharp
[assembly: Dependency(typeof(PhotoPickerService))]
namespace DependencyServiceDemos.UWP
{
    public class PhotoPickerService : IPhotoPickerService
    {
        public async Task<Stream> GetImageStreamAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary,
            };

            openPicker.FileTypeFilter.Add(".jpg");
            openPicker.FileTypeFilter.Add(".jpeg");
            openPicker.FileTypeFilter.Add(".png");

            // Get a file and return a Stream
            StorageFile storageFile = await openPicker.PickSingleFileAsync();

            if (storageFile == null)
            {
                return null;
            }

            IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
            return raStream.AsStreamForRead();
        }
    }
}
```

## <a name="implementing-in-shared-code"></a>Implementazione nel codice condiviso

Ora che l'interfaccia è stata implementata per ogni piattaforma, il codice condiviso nella libreria .NET Standard può trarne vantaggio.

L'interfaccia [`Button`](xref:Xamarin.Forms.Button) utente include un oggetto su cui è possibile fare clic per scegliere una foto:The UI includes a that can be clicked to choose a photo:

```xaml
<Button Text="Pick Photo"
        Clicked="OnPickPhotoButtonClicked" />
```

Il gestore dell'evento `Clicked` usa la classe `DependencyService` per chiamare `GetImageStreamAsync`. Il risultato è una chiamata al progetto di piattaforma. Se il metodo restituisce un oggetto `Stream`, il gestore imposta la proprietà `Source` dell'oggetto `image` sui dati `Stream`:

```csharp
async void OnPickPhotoButtonClicked(object sender, EventArgs e)
{
    (sender as Button).IsEnabled = false;

    Stream stream = await DependencyService.Get<IPhotoPickerService>().GetImageStreamAsync();
    if (stream != null)
    {
        image.Source = ImageSource.FromStream(() => stream);
    }

    (sender as Button).IsEnabled = true;
}
```

## <a name="related-links"></a>Collegamenti correlati

- [DependencyService (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/) (Esempio di DependencyService)
- [Scegliere una foto dalla raccolta (iOS)](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [Selezionare un'immagine (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)
