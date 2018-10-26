---
title: Selezione di una foto dalla raccolta immagini di
description: Questo articolo illustra come usare la classe di xamarin. Forms DependencyService per scegliere una foto dalla raccolta immagini del telefono.
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 00308a6c7883d4ac6ce41592d4a0e18f9fb28d52
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113313"
---
# <a name="picking-a-photo-from-the-picture-library"></a>Selezione di una foto dalla raccolta immagini di

Questo articolo illustra la creazione di un'applicazione che consente all'utente di scegliere una foto dalla raccolta immagini del telefono. Poiché xamarin. Forms non include questa funzionalità, è necessario usare [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) per accedere alle API native in ciascuna piattaforma.  Questo articolo verranno illustrati i passaggi seguenti per utilizzare `DependencyService` per questa attività:

- **[Creazione dell'interfaccia](#Creating_the_Interface)**  &ndash; comprendere come viene creata l'interfaccia nel codice condiviso.
- **[Implementazione di iOS](#iOS_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per iOS.
- **[Implementazione di Android](#Android_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per Android.
- **[L'implementazione della piattaforma Windows Universal](#UWP_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per la Universal Windows Platform (UWP).
- **[Implementazione nel codice condiviso](#Implementing_in_Shared_Code)**  &ndash; informazioni su come usare `DependencyService` chiamino l'implementazione native da codice condiviso.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

Innanzitutto, creare un'interfaccia nel codice condiviso che esprime la funzionalità desiderata. Nel caso di un'applicazione di prelievo di foto, è necessario un solo metodo. Questo elemento viene definito il [ `IPicturePicker` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) interfaccia nella libreria .NET Standard del codice di esempio:

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

Il `GetImageStreamAsync` metodo viene definito come asincrona perché il metodo deve restituire rapidamente, ma non può restituire un `Stream` oggetto per foto selezionata fino a quando l'utente ha esplorato la raccolta di immagini e selezionato uno.

Questa interfaccia viene implementata in tutte le piattaforme che usano codice specifico della piattaforma.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementazione di iOS

L'implementazione di iOS del `IPicturePicker` interfaccia viene utilizzata la [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) come descritto nel [ **scegliere una foto dalla raccolta** ](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery) recipe e [codice di esempio](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

L'implementazione di iOS è contenuto nel [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) classe nel progetto iOS del codice di esempio. Per rendere visibili a questa classe la `DependencyService` manager, la classe deve essere identificata con un [`assembly`] attributo di tipo `Dependency`, e la classe deve essere pubblica e implementare in modo esplicito il `IPicturePicker` interfaccia:

```csharp
[assembly: Dependency (typeof (PicturePickerImplementation))]

namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
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

Il `GetImageStreamAsync` metodo crea un `UIImagePickerController` e lo inizializza per selezionare le immagini dalla raccolta foto. Sono necessari due gestori eventi: uno per quando l'utente seleziona una foto e l'altro per quando l'utente annulla la visualizzazione della libreria di foto. Il `PresentModalViewController` quindi Visualizza la raccolta di foto all'utente.

A questo punto, il `GetImageStreamAsync` metodo deve restituire un `Task<Stream>` oggetto per il codice che viene chiamata. Questa attività viene completata solo quando l'utente ha terminato l'interazione con la libreria di foto e viene chiamato uno dei gestori di eventi. Per situazioni come queste, il [ `TaskCompletionSource` ](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) classe è essenziale. La classe fornisce una `Task` oggetto del tipo generico appropriato da restituire dal `GetImageStreamAsync` (metodo) e la classe può successivamente ricevere il segnale una volta completata l'attività.

Il `FinishedPickingMedia` gestore eventi viene chiamato quando l'utente ha selezionato un'immagine. Tuttavia, il gestore fornisce una `UIImage` oggetto e il `Task` deve restituire un .NET `Stream` oggetto. Questa operazione viene eseguita in due passaggi: il `UIImage` oggetto viene prima convertito in un file JPEG in memoria archiviato in un `NSData` oggetti e quindi la `NSData` oggetto viene convertito in .NET `Stream` oggetto. Una chiamata al `SetResult` metodo per il `TaskCompletionSource` oggetto completa l'operazione specificando il `Stream` oggetto:

```csharp
namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
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
                NSData data = image.AsJPEG(1);
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

Un'applicazione iOS richiede l'autorizzazione da parte dell'utente per accedere alla libreria di foto del telefono. Aggiungere il codice seguente il `dict` sezione del file Info. plist:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementazione di Android

L'implementazione di Android Usa la tecnica descritta nel [ **selezionare un'immagine** ](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image) recipe e la [codice di esempio](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image). Tuttavia, il metodo viene chiamato quando l'utente ha selezionato un'immagine dalla raccolta immagini di è un' `OnActivityResult` esegue l'override in una classe che deriva da `Activity`. Per questo motivo, la normale [ `MainActivity` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) state arricchite con un campo, una proprietà e un override della classe nel progetto Android la `OnActivityResult` metodo:

```csharp
public class MainActivity : FormsAppCompatActivity
{
    ...
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

Il `OnActivityResult`sostituzione indica il file di immagine selezionata con un Android `Uri` oggetto, ma questo può essere convertito in .NET `Stream` chiamando il `OpenInputStream` metodo del `ContentResolver` oggetto ottenuto dal dell'attività `ContentResolver` proprietà.

Ad esempio l'implementazione di iOS, l'implementazione di Android Usa un `TaskCompletionSource` per segnalare quando l'attività è stata completata. Ciò `TaskCompletionSource` oggetto viene definito come una proprietà pubblica nel `MainActivity` classe. In questo modo la proprietà verrà indicato il [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) classe nel progetto Android. Si tratta della classe con il `GetImageStreamAsync` metodo:

```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.Droid
{
    public class PicturePickerImplementation : IPicturePicker
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

Questo metodo accede il `MainActivity` classe per vari scopi: per il `Instance` proprietà, per il `PickImageId` field, per il `TaskCompletionSource` proprietà e chiamare `StartActivityForResult`. Questo metodo è definito per il `FormsAppCompatActivity` classe, ovvero la classe di base di `MainActivity`.

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>Implementazione di UWP

A differenza di iOS e Android implementazioni, l'implementazione del selettore foto per la piattaforma Windows universale non richiede il `TaskCompletionSource` classe. Il [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) classe Usa il [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) classe per ottenere l'accesso alla libreria di foto. Poiché il `PickSingleFileAsync` metodo di `FileOpenPicker` è a sua volta asincrona, il `GetImageStreamAsync` metodo è possibile utilizzare semplicemente `await` con tale metodo (e altri metodi asincroni) e restituire un `Stream` oggetto:


```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.UWP
{
    public class PicturePickerImplementation : IPicturePicker
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

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementazione nel codice condiviso

Ora che è stata implementata l'interfaccia per ogni piattaforma, l'applicazione nella libreria .NET Standard può sfruttare i vantaggi di esso.

Il [ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) classe crea un `Button` scegliere una foto:

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

Il `Clicked` gestore utilizza la `DependencyService` classe chiamare `GetImageStreamAsync`. Ciò comporta una chiamata nel progetto di piattaforma. Se il metodo restituisce un `Stream` dell'oggetto, quindi crea il gestore di un `Image` (elemento) per l'immagine con un `TapGestureRecognizer`e sostituisce il `StackLayout` della pagina per cui `Image`:

```csharp
pickPictureButton.Clicked += async (sender, e) =>
{
    pickPictureButton.IsEnabled = false;
    Stream stream = await DependencyService.Get<IPicturePicker>().GetImageStreamAsync();

    if (stream != null)
    {
        Image image = new Image
        {
            Source = ImageSource.FromStream(() => stream),
            BackgroundColor = Color.Gray
        };

        TapGestureRecognizer recognizer = new TapGestureRecognizer();
        recognizer.Tapped += (sender2, args) =>
        {
            (MainPage as ContentPage).Content = stack;
            pickPictureButton.IsEnabled = true;
        };
        image.GestureRecognizers.Add(recognizer);

        (MainPage as ContentPage).Content = image;
    }
    else
    {
        pickPictureButton.IsEnabled = true;
    }
};
```

Se si tocca il `Image` elemento restituisce la pagina alla normalità.


## <a name="related-links"></a>Collegamenti correlati

- [Scegliere una foto dalla raccolta (iOS)](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [Selezionare un'immagine (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)
- [DependencyService (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
