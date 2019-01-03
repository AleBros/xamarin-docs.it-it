---
title: Selezione di una foto dalla Raccolta immagini
description: Questo articolo illustra come usare la classe DependencyService di Xamarin.Forms per scegliere una foto dalla Raccolta immagini del telefono.
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 3452c79621013690f967e065c7afaf0768a50c3f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057489"
---
# <a name="picking-a-photo-from-the-picture-library"></a>Selezione di una foto dalla Raccolta immagini

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)

Questo articolo illustra in dettaglio la creazione di un'applicazione che consente all'utente di scegliere una foto dalla Raccolta immagini del telefono. Poiché Xamarin.Forms non include questa funzionalità, è necessario usare [`DependencyService`](xref:Xamarin.Forms.DependencyService) per accedere alle API native in ogni piattaforma.  Questo articolo illustra i passaggi seguenti per l'uso di `DependencyService` per questa attività:

- **[Creazione dell'interfaccia](#Creating_the_Interface)** &ndash; Informazioni sulla creazione dell'interfaccia nel codice condiviso.
- **[Implementazione in iOS](#iOS_Implementation)**: informazioni su come implementare l'interfaccia nel codice nativo per iOS.
- **[Implementazione in Android](#Android_Implementation)** &ndash; Informazioni su come implementare l'interfaccia nel codice nativo per Android.
- **[Implementazione nella piattaforma UWP (Universal Windows Platform)](#UWP_Implementation)** &ndash; Informazioni su come implementare l'interfaccia nel codice nativo per la piattaforma UWP (Universal Windows Platform).
- **[Implementazione nel codice condiviso](#Implementing_in_Shared_Code)** &ndash; Informazioni su come usare `DependencyService` per una chiamata all'implementazione nativa dal codice condiviso.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

In primo luogo creare un'interfaccia nel codice condiviso che esprime la funzionalità desiderata. Nel caso di un'applicazione per la selezione di foto è sufficiente un solo metodo. Questo elemento viene definito nell'interfaccia [`IPicturePicker`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) della libreria .NET Standard del codice di esempio:

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

Il metodo `GetImageStreamAsync` viene definito come asincrono perché deve restituire un valore in modo rapido, ma non può restituire un oggetto `Stream` per la foto selezionata fino a quando l'utente non ha esplorato la raccolta di immagini e non ha selezionato un'immagine.

Questa interfaccia viene implementata in tutte le piattaforme mediante codice specifico per la piattaforma.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementazione iOS

L'implementazione iOS dell'interfaccia `IPicturePicker` usa [`UIImagePickerController`](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) come descritto nel file recipe [**Scegliere una foto dalla raccolta**](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery) e nel relativo [codice di esempio](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

L'implementazione iOS è inclusa nella classe [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) del progetto iOS del codice di esempio. Per rendere visibile questa classe al manager `DependencyService`, la classe deve essere identificata con un attributo [`assembly`] di tipo `Dependency`, deve essere pubblica e deve implementare in modo esplicito l'interfaccia `IPicturePicker`:

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

Il metodo `GetImageStreamAsync` crea un `UIImagePickerController` e lo inizializza per selezionare immagini dalla raccolta di foto. Sono necessari due gestori eventi: uno per quando l'utente seleziona una foto e l'altro per quando l'utente annulla la visualizzazione della raccolta di foto. Quindi `PresentModalViewController` visualizza la raccolta di foto all'utente.

A questo punto il metodo `GetImageStreamAsync` deve restituire un oggetto `Task<Stream>` al codice che origina la chiamata. Questa attività viene completata solo quando l'utente ha terminato l'interazione con la raccolta di foto e viene chiamato uno dei gestori eventi. Per situazioni come queste, la classe [`TaskCompletionSource`](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) è indispensabile. La classe specifica un oggetto `Task` del tipo generico appropriato per la restituzione dal metodo `GetImageStreamAsync` e può ricevere il segnale successivamente, una volta completata l'attività.

Il gestore eventi `FinishedPickingMedia` viene chiamato quando l'utente ha selezionato un'immagine. Tuttavia il gestore specifica un oggetto `UIImage` e `Task` deve restituire un oggetto .NET `Stream`. Questa operazione viene eseguita in due passaggi: l'oggetto `UIImage` viene prima convertito in un file JPEG in memoria archiviato in un oggetto `NSData`, quindi l'oggetto `NSData` viene convertito in un oggetto .NET `Stream`. Una chiamata al metodo `SetResult` per l'oggetto `TaskCompletionSource` completa l'operazione specificando l'oggetto `Stream`:

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

Un'applicazione iOS richiede l'autorizzazione dell'utente per accedere alla raccolta di foto del telefono. Aggiungere il codice seguente alla sezione `dict` del file Info.plist:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementazione Android

L'implementazione Android usa la tecnica descritta nel file recipe [**Selezionare un'immagine**](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image) e nel relativo [codice di esempio](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image). Tuttavia il metodo chiamato quando l'utente ha selezionato un'immagine dalla raccolta immagini è una sostituzione `OnActivityResult` in una classe che deriva da `Activity`. Per questo motivo la classe normale [`MainActivity`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) nel progetto Android è stata corredata di un campo, una proprietà e una sostituzione del metodo `OnActivityResult`:

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

La sostituzione `OnActivityResult` indica il file immagine selezionato con un oggetto Android `Uri`, che può essere convertito in un oggetto .NET `Stream` chiamando il metodo `OpenInputStream` dell'oggetto `ContentResolver` che è stato ottenuto dalla proprietà `ContentResolver` dell'attività.

Come l'implementazione iOS, l'implementazione Android usa un elemento `TaskCompletionSource` per indicare quando l'attività è stata completata. L'oggetto `TaskCompletionSource` è definito come proprietà pubblica nella classe `MainActivity`. In questo modo viene inserito un riferimento alla proprietà nella classe [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) del progetto Android. Questa è la classe con il metodo `GetImageStreamAsync`:

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

Questo metodo accede alla classe `MainActivity` per vari scopi: per la proprietà `Instance`, per il campo `PickImageId`, per la proprietà `TaskCompletionSource` e per chiamare `StartActivityForResult`. Questo metodo è definito dalla classe `FormsAppCompatActivity`, che è la classe di base di `MainActivity`.

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>Implementazione nella piattaforma UWP

A differenza delle implementazioni per iOS e Android, l'implementazione del selettore foto per la piattaforma UWP (Universal Windows Platform) non richiede la classe `TaskCompletionSource`. La classe [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) usa la classe [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) per l'accesso alla raccolta di foto. Dato che il metodo `PickSingleFileAsync` di `FileOpenPicker` è asincrono, il metodo `GetImageStreamAsync` può usare semplicemente `await` con tale metodo (e con altri metodi) e restituire un oggetto `Stream`:


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

Ora che l'interfaccia è stata implementata per ogni piattaforma, l'applicazione nella libreria .NET Standard può trarre vantaggio da questa condizione.

La classe [`App`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) crea un elemento `Button` per scegliere una foto:

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

Il gestore `Clicked` usa la classe `DependencyService` per chiamare `GetImageStreamAsync`. Il risultato è una chiamata nel progetto di piattaforma. Se il metodo restituisce un oggetto `Stream`, il gestore crea un elemento `Image` per quell'immagine con un `TapGestureRecognizer` e sostituisce l'elemento `StackLayout` della pagina con questo elemento `Image`:

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

Se si tocca l'elemento `Image` la pagina torna allo stato normale.


## <a name="related-links"></a>Collegamenti correlati

- [Scegliere una foto dalla raccolta (iOS)](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [Selezionare un'immagine (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)
- [DependencyService (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
