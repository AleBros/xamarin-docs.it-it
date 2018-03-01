---
title: Selezione di una foto dalla raccolta immagini di
description: Utilizzare DependencyService per scegliere una foto dalla raccolta immagini del telefono
ms.topic: article
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 6ac228f85f3f717ddf95e0dc2e434b13bfec5d06
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="picking-a-photo-from-the-picture-library"></a>Selezione di una foto dalla raccolta immagini di

In questo articolo vengono illustrati la creazione di un'applicazione che consente all'utente di scegliere una foto dalla raccolta immagini del telefono. Poiché xamarin. Forms non include questa funzionalità, è necessario utilizzare [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) per accedere ad API native in ciascuna piattaforma.  In questo articolo illustra i passaggi seguenti per utilizzare `DependencyService` per questa attività:

- **[La creazione dell'interfaccia](#Creating_the_Interface)**  &ndash; comprendere la modalità di creazione dell'interfaccia nel codice condiviso.
- **[iOS implementazione](#iOS_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per iOS.
- **[Implementazione Android](#Android_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per Android.
- **[L'implementazione della piattaforma Windows Universal](#UWP_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per la piattaforma UWP (Universal Windows).
- **[Implementazione di Windows Phone](#Windows_Phone_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per Windows Phone 8.1.
- **[Implementazione di codice condiviso](#Implementing_in_Shared_Code)**  &ndash; imparare a usare `DependencyService` chiamino l'implementazione native da codice condiviso.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

Innanzitutto, creare un'interfaccia nel codice condiviso che esprime la funzionalità desiderata. Nel caso di un'applicazione di prelievo di foto, è necessario un solo metodo. Questo elemento viene definito il [ `IPicturePicker` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) interfaccia nella libreria di classi portabile il codice di esempio:

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

Il `GetImageStreamAsync` metodo viene definito come asincrona perché il metodo deve restituire rapidamente, ma non può restituire un `Stream` oggetto per la foto selezionata fino a quando l'utente dispone di esplorare la raccolta di immagini e selezionato uno.

Questa interfaccia viene implementata in tutte le piattaforme tramite codice specifico della piattaforma.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementazione di iOS

L'implementazione di iOS del `IPicturePicker` interfaccia viene utilizzata la [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) come descritto nel [ **scegliere una foto dalla raccolta** ](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/) recipe e [codice di esempio](https://github.com/xamarin/recipes/tree/master/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

L'implementazione di iOS è contenuto nel [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) classe nel progetto iOS del codice di esempio. Per rendere visibile a questa classe di `DependencyService` manager, la classe deve essere identificata con un [`assembly`] attributo di tipo `Dependency`, e la classe deve essere pubblico e implementare in modo esplicito il `IPicturePicker` interfaccia:

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

Il `GetImageStreamAsync` metodo crea un `UIImagePickerController` e lo inizializza per selezionare le immagini dalla raccolta foto. Sono necessari due gestori: uno per quando l'utente seleziona una foto e l'altro per quando l'utente annulla la visualizzazione della raccolta foto. Il `PresentModalViewController` quindi Visualizza la raccolta di foto all'utente.

A questo punto, il `GetImageStreamAsync` metodo deve restituire un `Task<Stream>` oggetto per il codice che viene chiamata. Questa attività viene completata solo quando l'utente ha terminato l'interazione con la raccolta di foto e viene chiamato uno dei gestori eventi. Per tali situazioni, il [ `TaskCompletionSource` ](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) classe è essenziale. La classe fornisce un `Task` oggetto del tipo generico appropriato da restituire dal `GetImageStreamAsync` metodo e la classe può successivamente essere segnalato quando l'attività è stata completata.

Il `FinishedPickingMedia` gestore eventi viene chiamato quando l'utente ha selezionato un'immagine. Tuttavia, il gestore fornisce un `UIImage` oggetto e `Task` deve restituire un .NET `Stream` oggetto. Questa operazione viene eseguita in due passaggi: il `UIImage` oggetto viene prima convertito in un file JPEG in memoria archiviato in un `NSData` oggetto, quindi il `NSData` oggetto viene convertito in .NET `Stream` oggetto. Una chiamata al `SetResult` metodo il `TaskComkpletionSource` oggetto completa l'operazione, fornendo il `Stream` oggetto:

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

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
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

Un'applicazione iOS richiede l'autorizzazione da parte dell'utente per accedere alla libreria di foto del telefono. Il comando seguente per aggiungere il `dict` sezione del file Info. plist:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementazione di Android

L'implementazione di Android Usa la tecnica descritta nel [ **selezionare un'immagine** ](https://developer.xamarin.com/recipes/android/other_ux/pick_image/) recipe e [codice di esempio](https://github.com/xamarin/recipes/tree/master/android/other_ux/pick_image). Tuttavia, il metodo che viene chiamato quando l'utente ha selezionato un'immagine dalla raccolta immagini di è un `OnActivityResult` eseguire l'override in una classe che deriva da `Activity`. Per questo motivo, la normale [ `MainActivity` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) state arricchite con un campo, una proprietà e un override della classe nel progetto Android la `OnActivityResult` metodo:

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
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

Il `OnActivityResult`override indica il file di immagine selezionata con un Android `Uri` oggetto, ma può essere convertito in .NET `Stream` oggetto chiamando il `OpenInputStream` metodo il `ContentResolver` oggetto ottenuto dal dell'attività `ContentResolver` proprietà.

Ad esempio l'implementazione di iOS, l'implementazione di Android Usa un `TaskCompletionSource` per segnalare quando l'attività è stata completata. Questo `TaskCompletionSource` oggetto viene definito come una proprietà pubblica di `MainActivity` classe. In questo modo la proprietà verrà indicato il [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) classe nel progetto Android. Questa è la classe con il `GetImageStreamAsync` metodo:

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

            // Get the MainActivity instance
            MainActivity activity = Forms.Context as MainActivity;

            // Start the picture-picker activity (resumes in MainActivity.cs)
            activity.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Picture"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            activity.PickImageTaskCompletionSource = new TaskCompletionSource<Stream>();

            // Return Task object
            return activity.PickImageTaskCompletionSource.Task;
        }
    }
}
```

Il metodo accede il `MainActivity` classe per diversi scopi: per il `PickImageId` campo, per il `TaskCompletionSource` proprietà e di chiamare `StartActivityForResult`. Questo metodo è definito per il `FormsApplicationActivity` classe che rappresenta la classe base di `MainActivity`.

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>Implementazione di piattaforma UWP

A differenza di iOS e Android implementazioni, l'implementazione del selettore foto per la piattaforma Windows universale non richiede la `TaskCompletionSource` classe. Il [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) classe Usa il [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) classe per ottenere l'accesso alla raccolta foto. Poiché il `PickSingleFileAsync` metodo `FileOpenPicker` è asincrona, il `GetImageStreamAsync` metodo è possibile utilizzare semplicemente `await` con tale metodo (e altri metodi asincroni) e restituire un `Stream` oggetto:


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

<a name="Windows_Phone_Implementation" />

## <a name="windows-phone-81-implementation"></a>Implementazione di Windows Phone 8.1

L'implementazione di Windows Phone 8.1 è simile all'implementazione UWP ad eccezione di una grande differenza con un impatto significativo: il `PickSingleFileAsync` metodo `FileOpenPicker` non è disponibile. Al contrario, il `GetImageStreamAsync` metodo deve chiamare `PickSingleFileAndContinue`. Questo metodo restituisce quando la raccolta di foto viene visualizzata all'utente, ma la selezione dell'utente di una foto viene segnalata da una chiamata al `OnActivated` metodo la `App` classe.

Per questo motivo, il [ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/WinPhone/App.xaml.cs) classe creata dal modello di progetto xamarin. Forms nel progetto Windows Phone 8.1 è stata completata con una proprietà di tipo `TaskCompletionSource` e un override del `OnActivated` metodo:

```csharp
namespace DependencyServiceSample.WinPhone
{
    public sealed partial class App : Application
    {
        ...
        public TaskCompletionSource<Stream> TaskCompletionSource { set; get; }

        protected async override void OnActivated(IActivatedEventArgs args)
        {
            base.OnActivated(args);

            IContinuationActivatedEventArgs continuationArgs = args as IContinuationActivatedEventArgs;

            if (continuationArgs != null &&
                continuationArgs.Kind == ActivationKind.PickFileContinuation)
            {
                FileOpenPickerContinuationEventArgs pickerArgs = args as FileOpenPickerContinuationEventArgs;

                if (pickerArgs.Files.Count > 0)
                {
                    // Get the file and a Stream
                    StorageFile storageFile = pickerArgs.Files[0];
                    IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
                    Stream stream = raStream.AsStreamForRead();

                    // Set the completion of the Task
                    TaskCompletionSource.SetResult(stream);
                }
                else
                {
                    TaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

Il `OnActivated` metodo potrebbe essere chiamato per vari motivi, inclusi l'avvio dell'applicazione. Il codice si adatta alle sole chiamate quando la selezione e apertura file non è terminata e successivamente viene ottenuto un `Stream` dall'oggetto di `StorageFile`.

Il [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/WinPhone/PicturePickerImplementation.cs) classe contiene il `GetImageStreamAsync` metodo che crea il `FileOpenPicker` e chiama `PickSingleFileAndContainue`:

```csharp
[assembly: Xamarin.Forms.Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.WinPhone
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public Task<Stream> GetImageStreamAsync()
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

            // Display the picker for a single file (resumes in OnActivated in App.xaml.cs)
            openPicker.PickSingleFileAndContinue();

            // Create a TaskCompletionSource stored in App.xaml.cs
            TaskCompletionSource<Stream> taskCompletionSource = new TaskCompletionSource<Stream>();
            (Application.Current as App).TaskCompletionSource = taskCompletionSource;

            // Return the Task object
            return taskCompletionSource.Task;
        }
    }
}

```

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementazione di codice condiviso

Ora che l'interfaccia è stata implementata per ogni piattaforma, l'applicazione nella libreria di classi portabile common può sfruttarla.

Il [ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) classe crea un `Button` per scegliere una foto:

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

Il `Clicked` gestore utilizza il `DependencyService` classe chiamare `GetImageStreamAsync`. Ciò comporta una chiamata nel progetto di piattaforma. Se il metodo restituisce un `Stream` dell'oggetto, quindi crea il gestore un `Image` elemento per l'immagine con un `TabGestureRecognizer`e sostituisce il `StackLayout` della pagina che `Image`:

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

Toccando il `Image` elemento restituisce la pagina Normal.


## <a name="related-links"></a>Collegamenti correlati

- [Scegliere una foto dalla raccolta (iOS)](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/)
- [Selezionare un'immagine (Android)](https://developer.xamarin.com/recipes/android/other_ux/pick_image/)
- [DependencyService (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
