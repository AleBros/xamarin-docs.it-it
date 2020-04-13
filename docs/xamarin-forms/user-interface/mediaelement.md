---
title: Xamarin.Forms MediaElement
description: In questo articolo viene illustrato come utilizzare MediaElement per riprodurre video e audio in un'applicazione Xamarin.Forms.This article explains how to use MediaElement to play video and audio in a Xamarin.Forms application.
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2020
ms.openlocfilehash: 6f6c51c428de569ceb09ed6a26cfc36881c86dc5
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628333"
---
# <a name="xamarinforms-mediaelement"></a>Xamarin.Forms MediaElement

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)

[`MediaElement`](xref:Xamarin.Forms.MediaElement)è una visualizzazione per la riproduzione di video e audio. I supporti supportati dalla piattaforma sottostante possono essere riprodotti dalle origini seguenti:Media that's supported by the underlying platform can be played from the following sources:

- Il Web, utilizzando un URI (HTTP o HTTPS).
- Risorsa incorporata nell'applicazione della `ms-appx:///` piattaforma, usando lo schema URI.
- File provenienti dalle cartelle di dati locali e `ms-appdata:///` temporanei dell'app, usando lo schema URI.
- Libreria del dispositivo.

[`MediaElement`](xref:Xamarin.Forms.MediaElement)è possibile utilizzare i controlli di riproduzione della piattaforma, che vengono definiti controlli di trasporto. Tuttavia, sono disabilitati per impostazione predefinita e possono essere sostituiti con i propri controlli di trasporto. Le schermate `MediaElement` seguenti mostrano la riproduzione di un video con i controlli di trasporto della piattaforma:The following screenshots show playing a video with the platform transport controls:

[![Screenshot di un oggetto MediaElement che riproduce un video, in iOS e Android](mediaelement-images/playback-controls.png "MediaElement riproduzione di un video")](mediaelement-images/playback-controls-large.png#lightbox "MediaElement riproduzione di un video")

[`MediaElement`](xref:Xamarin.Forms.MediaElement)è disponibile in Xamarin.Forms 4.5. Tuttavia, è attualmente sperimentale e può essere utilizzato solo aggiungendo la seguente riga di codice al file *di App.xaml.cs:*

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement)è disponibile su iOS, Android, la piattaforma Windows universale (UWP), macOS, Windows Presentation Foundation e Tizen.

[`MediaElement`](xref:Xamarin.Forms.MediaElement)definisce le seguenti proprietà:

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect), di [`Aspect`](xref:Xamarin.Forms.Aspect)tipo , determina la modalità di ridimensionamento del supporto per adattarla all'area di visualizzazione. Il valore predefinito di questa proprietà è `AspectFit`.
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay), di `bool`tipo , indica se la [`Source`](xref:Xamarin.Forms.MediaElement.Source) riproduzione multimediale verrà avviata automaticamente quando la proprietà è impostata. Il valore predefinito di questa proprietà è `true`.
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress)di tipo `double`, indica l'avanzamento corrente del buffering. Il valore predefinito di questa proprietà è 0.0.The default value of this property is 0.0.
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek)di tipo `bool`, indica se il supporto può essere riposizionato impostando il valore della [`Position`](xref:Xamarin.Forms.MediaElement.Position) proprietà . Questa proprietà è di sola lettura.
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), di [`MediaElementState`](xref:Xamarin.Forms.MediaElementState)tipo , indica lo stato corrente del controllo. Si tratta di una proprietà di `MediaElementState.Closed`sola lettura, il cui valore predefinito è .
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration), di `TimeSpan?`tipo , indica la durata del supporto attualmente aperto. Si tratta di una proprietà di `null`sola lettura il cui valore predefinito è .
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping), di `bool`tipo , descrive se l'origine multimediale attualmente caricata deve riprendere la riproduzione dall'inizio dopo aver raggiunto la fine. Il valore predefinito di questa proprietà è `false`.
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn), di `bool`tipo , determina se lo schermo del dispositivo deve rimanere acceso durante la riproduzione multimediale. Il valore predefinito di questa proprietà è `false`.
- [`Position`](xref:Xamarin.Forms.MediaElement.Position), di `TimeSpan`tipo , descrive lo stato di avanzamento corrente nel tempo di riproduzione del file multimediale. Il valore predefinito di questa proprietà è `TimeSpan.Zero`.
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls), di `bool`tipo , determina se i controlli di riproduzione delle piattaforme vengono visualizzati. Il valore predefinito di questa proprietà è `false`.
- [`Source`](xref:Xamarin.Forms.MediaElement.Source), di [`MediaSource`](xref:Xamarin.Forms.MediaSource)tipo , indica l'origine del supporto caricato nel controllo.
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight), di `int`tipo , indica l'altezza del controllo. Questa proprietà è di sola lettura.
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth), di `int`tipo , indica la larghezza del controllo. Questa proprietà è di sola lettura.
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume), di `double`tipo , determina il volume del supporto, rappresentato su una scala lineare compresa tra 0 e 1. Questa proprietà `TwoWay` utilizza un'associazione e il relativo valore predefinito è 1.This property uses a binding, and its default value is 1.

Queste proprietà, ad eccezione della `CanSeek` proprietà, sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di associazioni dati e stili.

La [`MediaElement`](xref:Xamarin.Forms.MediaElement) classe definisce inoltre quattro eventi:The class also defines four events:

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened)viene generato quando il flusso multimediale è stato convalidato e aperto.
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded)viene attivato `MediaElement` quando termina la riproduzione dei file multimediali.
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed)viene generato quando si verifica un errore associato all'origine multimediale.
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted)viene generato quando il punto di ricerca di un'operazione di ricerca richiesta è pronto per la riproduzione.

Inoltre, [`MediaElement`](xref:Xamarin.Forms.MediaElement) include [`Play`](xref:Xamarin.Forms.MediaElement.Play) [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)i [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) metodi , , e .

Per informazioni sui formati multimediali supportati in Android, vedere [Formati multimediali supportati](https://developer.android.com/guide/topics/media/media-formats) in developer.android.com. Per informazioni sui formati multimediali supportati nella piattaforma UWP (Universal Windows), vedere [Codec supportati.](/windows/uwp/audio-video-camera/supported-codecs)

## <a name="play-remote-media"></a>Riprodurre file multimediali remoti

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) oggetto può riprodurre file multimediali remoti utilizzando gli schemi URI HTTP e HTTPS. Questa operazione viene eseguita impostando la [`Source`](xref:Xamarin.Forms.MediaElement.Source) proprietà sull'URI del file multimediale:

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

Per impostazione predefinita, il [`Source`](xref:Xamarin.Forms.MediaElement.Source) contenuto multimediale definito dalla proprietà viene riprodotto immediatamente dopo l'apertura del contenuto multimediale. Per sospendere la riproduzione automatica dei file multimediali, impostate la [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay) proprietà su `false`.

I controlli di riproduzione multimediale sono [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls) disabilitati `true`per impostazione predefinita e vengono abilitati impostando la proprietà su . [`MediaElement`](xref:Xamarin.Forms.MediaElement)utilizzerà quindi i controlli di riproduzione della piattaforma.

## <a name="play-local-media"></a>Riprodurre file multimediali locali

I supporti locali possono essere riprodotti dalle seguenti fonti:

- Risorsa incorporata nell'applicazione della `ms-appx:///` piattaforma, usando lo schema URI.
- File provenienti dalle cartelle di dati locali e `ms-appdata:///` temporanei dell'app, usando lo schema URI.
- Libreria del dispositivo.

Per ulteriori informazioni su questi schemi URI, vedere [Schemi URI](/windows/uwp/app-resources/uri-schemes).

### <a name="play-media-embedded-in-the-app-package"></a>Riprodurre contenuti multimediali incorporati nel pacchetto dell'app

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) oggetto può riprodurre file multimediali incorporati `ms-appx:///` nel pacchetto dell'app usando lo schema URI. I file multimediali vengono incorporati nel pacchetto dell'app inserendoli nel progetto della piattaforma.

L'archiviazione di un file multimediale nel progetto di piattaforma è diversa per ogni piattaforma:Storing a media file in the platform project is different for each platform:

- In iOS, i file multimediali devono essere archiviati nella cartella Risorse o in una sottocartella della cartella **Risorse.On** iOS, media files must be stored in the **Resources** folder, or a subfolder of the Resources folder. Il file multimediale `Build Action` `BundleResource`deve avere un file di file .
- In Android, i file multimediali devono essere memorizzati in una sottocartella di **risorse** denominata **raw**. La cartella **raw** non può contenere sottocartelle. Il file multimediale `Build Action` `AndroidResource`deve avere un file di file .
- Nella piattaforma UWP, i file multimediali possono essere memorizzati in qualsiasi cartella del progetto. Il file multimediale `BuildAction` `Content`deve avere un file di file .

I file multimediali che soddisfano questi `ms-appx:///` criteri possono quindi essere riprodotti utilizzando lo schema URI:

```xaml
<MediaElement Source="ms-appx:///XamarinForms101UsingEmbeddedImages.mp4"
              ShowsPlaybackControls="True" />
```

Quando si usa l'associazione dati, è possibile usare un convertitore di valori per applicare questo schema URI:When using data binding, a value converter can be used to apply this URI scheme:

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        if (Device.RuntimePlatform == Device.UWP)
            return new Uri($"ms-appx:///Assets/{value}");
        else
            return new Uri($"ms-appx:///{value}");
    }
    // ...
}
```

Un'istanza `VideoSourceConverter` di può quindi essere `ms-appx:///` utilizzata per applicare lo schema URI a un file multimediale incorporato:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Per ulteriori informazioni sullo schema URI ms-appx, vedere [ms-appx e ms-appx-web](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web).

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>Riprodurre contenuti multimediali dalle cartelle locali e temporanee dell'app

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) oggetto può riprodurre file multimediali copiati nelle cartelle di `ms-appdata:///` dati locali o temporanei dell'app, utilizzando lo schema URI.

L'esempio seguente [`Source`](xref:Xamarin.Forms.MediaElement.Source) mostra la proprietà impostata su un file multimediale archiviato nella cartella dei dati locali dell'app:The following example shows the property set to a media file that's stored in the app's local data folder:

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

L'esempio seguente [`Source`](xref:Xamarin.Forms.MediaElement.Source) mostra la proprietà di un file multimediale archiviato nella cartella dei dati temporanei dell'app:The following example shows the property to a media file that's stored in the app's temporary data folder:

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> Oltre a riprodurre file multimediali archiviati nelle cartelle di dati locali o temporanei dell'app, UWP può anche riprodurre i file multimediali che si trovano nella cartella roaming dell'app. Ciò può essere ottenuto anteponendo al file multimediale il prefisso `ms-appdata:///roaming/`.

Quando si usa l'associazione dati, è possibile usare un convertitore di valori per applicare questo schema URI:When using data binding, a value converter can be used to apply this URI scheme:

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        return new Uri($"ms-appdata:///{value}");
    }
    // ...
}
```

Un'istanza `VideoSourceConverter` di può quindi essere `ms-appdata:///` usata per applicare lo schema URI a un file multimediale nella cartella dei dati locale o temporanea dell'app:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Per ulteriori informazioni sullo schema URI ms-appdata, vedere [ms-appdata](/windows/uwp/app-resources/uri-schemes#ms-appdata).

#### <a name="copying-a-media-file-to-the-apps-local-or-temporary-data-folder"></a>Copia di un file multimediale nella cartella dei dati locale o temporanea dell'app

La riproduzione di un file multimediale archiviato nella cartella dei dati locale o temporanea dell'app richiede che il file multimediale venga copiato dall'app. Questa operazione può essere eseguita, ad esempio, copiando un file multimediale dal pacchetto dell'app:This can be accomplished, for example, by copying a media file from the app package:

```csharp
// This method copies the video from the app package to the app data
// directory for your app. To copy the video to the temp directory
// for your app, comment out the first line of code, and uncomment
// the second line of code.
public static async Task CopyVideoIfNotExists(string filename)
{
    string folder = FileSystem.AppDataDirectory;
    //string folder = Path.GetTempPath();
    string videoFile = Path.Combine(folder, "XamarinVideo.mp4");

    if (!File.Exists(videoFile))
    {
        using (Stream inputStream = await FileSystem.OpenAppPackageFileAsync(filename))
        {
            using (FileStream outputStream = File.Create(videoFile))
            {
                await inputStream.CopyToAsync(outputStream);
            }
        }
    }
}
```

> [!NOTE]
> Nell'esempio di `FileSystem` codice precedente viene utilizzata la classe inclusa in Xamarin.Essentials. Per ulteriori informazioni, vedere [Xamarin.Essentials: File System Helpers](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android).

### <a name="play-media-from-the-device-library"></a>Riprodurre contenuti multimediali dalla libreria dei dispositivi

La maggior parte dei moderni dispositivi mobili e computer desktop hanno la possibilità di registrare video e audio utilizzando la fotocamera e il microfono del dispositivo. I supporti creati vengono quindi archiviati come file nel dispositivo. Questi file possono essere recuperati dalla [`MediaElement`](xref:Xamarin.Forms.MediaElement)libreria e riprodotti dal file .

Ognuna delle piattaforme include una funzionalità che consente all'utente di selezionare i supporti dalla libreria del dispositivo. In Xamarin.Forms, i progetti di piattaforma possono richiamare [`DependencyService`](xref:Xamarin.Forms.DependencyService) questa funzionalità e possono essere chiamati dalla classe .

Il servizio di dipendenza per la selezione video utilizzato nell'applicazione di esempio è molto simile a quello definito in [Selezione di una foto dalla raccolta immagini](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), con la differenza che la selezione restituisce un nome file anziché un `Stream` oggetto . Il progetto di codice condiviso `IVideoPicker`definisce un'interfaccia `GetVideoFileAsync`denominata , che definisce un singolo metodo denominato . Ogni piattaforma implementa quindi `VideoPicker` questa interfaccia in una classe.

Esempio di codice seguente viene illustrato come recuperare un file multimediale dalla libreria di dispositivi:The following code example shows how to retrieve a media file from the device library:

```csharp
string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();
if (!string.IsNullOrWhiteSpace(filename))
{
    mediaElement.Source = new FileMediaSource
    {
        File = filename
    };
}
```

Il servizio di dipendenza di selezione `DependencyService.Get` video viene richiamato `IVideoPicker` chiamando il metodo per ottenere l'implementazione di un'interfaccia nel progetto di piattaforma. Il `GetVideoFileAsync` metodo viene quindi chiamato su tale istanza e il [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) nome file restituito [`Source`](xref:Xamarin.Forms.MediaElement.Source) viene utilizzato [`MediaElement`](xref:Xamarin.Forms.MediaElement)per creare un oggetto e impostarlo sulla proprietà dell'oggetto .

## <a name="change-video-aspect-ratio"></a>Modificare le proporzioni del video

La [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect) proprietà determina la modalità di ridimensionamento dei supporti video per adattarsi all'area di visualizzazione. Per impostazione predefinita, questa `AspectFit` proprietà è impostata sul membro [`Aspect`](xref:Xamarin.Forms.Aspect) di enumerazione, ma può essere impostata su uno qualsiasi dei membri dell'enumerazione:

- `AspectFit`indica che il video verrà informato, se necessario, per adattarlo all'area di visualizzazione, mantenendo le proporzioni.
- `AspectFill`indica che il video verrà ritagliato in modo da riempire l'area di visualizzazione, mantenendo le proporzioni.
- `Fill`indica che il video verrà allungato per riempire l'area di visualizzazione.

## <a name="poll-for-position-data"></a>Sondaggio per i dati sulla posizione

La notifica di [`Position`](xref:Xamarin.Forms.MediaElement.Position) modifica della proprietà per la proprietà associabile viene attivata solo nei momenti chiave, ad esempio l'inizio e la fine della riproduzione e la sospensione che si verificano. Pertanto, l'associazione dati alla `Position` proprietà non produrrà dati di posizione accurati. È invece necessario impostare un timer ed eseguire il polling della proprietà.

Un buon posto per fare `OnAppearing` questo è nella sostituzione per la pagina che richiede i dati di posizione come media viene riprodotto:

```csharp
bool polling = true;

protected override void OnAppearing()
{
    base.OnAppearing();

    Device.StartTimer(TimeSpan.FromMilliseconds(1000), () =>
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            positionLabel.Text = mediaElement.Position.ToString("hh\\:mm\\:ss");
        });
        return polling;
    });
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    polling = false;
}
```

In questo esempio, l'override `OnAppearing` `positionLabel` avvia `Position` un timer che viene aggiornato con il valore ogni secondo. Il callback del timer viene richiamato `false`ogni secondo, fino a quando il callback non restituisce . Quando si verifica `OnDisappearing` lo spostamento tra le pagine viene eseguito l'override, che interrompe il callback del timer richiamato.

## <a name="understand-mediasource-types"></a>Comprendere i tipi MediaSource

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) oggetto può riprodurre contenuti multimediali impostandone la [`Source`](xref:Xamarin.Forms.MediaElement.Source) proprietà su un file multimediale remoto o locale. La `Source` proprietà è [`MediaSource`](xref:Xamarin.Forms.MediaSource)di tipo e questa classe definisce due metodi statici:

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*), restituisce un'istanza [`MediaSource`](xref:Xamarin.Forms.MediaSource) da un `string` argomento.
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*), restituisce un'istanza [`MediaSource`](xref:Xamarin.Forms.MediaSource) da un `Uri` argomento.

Inoltre, la [`MediaSource`](xref:Xamarin.Forms.MediaSource) classe dispone anche `MediaSource` di `string` operatori `Uri` impliciti che restituiscono istanze da e argomenti.

> [!NOTE]
> Quando [`Source`](xref:Xamarin.Forms.MediaElement.Source) la proprietà è impostata in XAML, viene [`MediaSource`](xref:Xamarin.Forms.MediaSource) richiamato `string` `Uri`un convertitore di tipi per restituire un'istanza da o .

La [`MediaSource`](xref:Xamarin.Forms.MediaSource) classe dispone inoltre di due classi derivate:The class also has two derived classes:

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource), utilizzato per specificare un file multimediale remoto da un URI. Questa classe [`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri) dispone di una proprietà `Uri`che può essere impostata su un oggetto .
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource), utilizzato per specificare un file `string`multimediale locale da un file . Questa classe [`File`](xref:Xamarin.Forms.FileMediaSource.File) dispone di una proprietà `string`che può essere impostata su un oggetto . Inoltre, questa classe dispone di `string` operatori `FileMediaSource` impliciti per `FileMediaSource` convertire `string`un oggetto in un oggetto e un oggetto in un oggetto .

> [!NOTE]
> Quando [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) un oggetto viene creato in XAML, viene [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) richiamato `string`un convertitore di tipi per restituire un'istanza da un oggetto .

## <a name="determine-mediaelement-status"></a>Determinare lo stato di MediaElementDetermine MediaElement status

La [`MediaElement`](xref:Xamarin.Forms.MediaElement) classe definisce una proprietà associabile di sola lettura denominata [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), di tipo . [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) Questa proprietà indica lo stato corrente del controllo, ad esempio se il file multimediale è in riproduzione o in pausa o se non è ancora pronto per riprodurre il file multimediale.

L'enumerazione [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) definisce i seguenti membri:

- `Closed`indica che `MediaElement` il contiene nessun supporto.
- `Opening`indica che `MediaElement` l'oggetto sta convalidando e tentando di caricare l'origine specificata.
- `Buffering`indica che `MediaElement` l'oggetto sta caricando il file multimediale per la riproduzione. La [`Position`](xref:Xamarin.Forms.MediaElement.Position) sua proprietà non avanza durante questo stato. Se `MediaElement` il video è stato riprodotto, continua a visualizzare l'ultimo fotogramma visualizzato.
- `Playing`indica che `MediaElement` l'oggetto sta riproducendo l'origine multimediale.
- `Paused`indica che `MediaElement` l'oggetto [`Position`](xref:Xamarin.Forms.MediaElement.Position) non promuove la sua proprietà. Se `MediaElement` il video è stato riprodotto, continua a visualizzare il fotogramma corrente.
- `Stopped`indica che `MediaElement` l'oggetto contiene contenuti multimediali, ma non viene riprodotto o messo in pausa. La [`Position`](xref:Xamarin.Forms.MediaElement.Position) sua proprietà è 0 e non avanza. Se il file multimediale `MediaElement` caricato è video, visualizza il primo fotogramma.

In genere non è necessario [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) esaminare [`MediaElement`](xref:Xamarin.Forms.MediaElement) la proprietà quando si utilizzano i controlli di trasporto. Tuttavia, questa proprietà diventa importante quando si implementano controlli di trasporto personalizzati.

## <a name="implement-custom-transport-controls"></a>Implementare controlli di trasporto personalizzatiImplement custom transport controls

I controlli di trasporto di un lettore multimediale includono i pulsanti che eseguono le funzioni **Riproduci**, **Pausa**e **Interrompi**. Questi pulsanti sono identificati in genere con icone note anziché testo e le funzioni di **riproduzione** e **pausa** sono in genere unite in un unico pulsante.

Per impostazione [`MediaElement`](xref:Xamarin.Forms.MediaElement) predefinita, i controlli di riproduzione sono disabilitati. In questo modo `MediaElement` è possibile controllare l'oggetto a livello di codice o fornendo controlli di trasporto personalizzati. A supporto di `MediaElement` [`Play`](xref:Xamarin.Forms.MediaElement.Play)questo, [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) include i metodi , [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)e .

L'esempio XAML seguente mostra [`MediaElement`](xref:Xamarin.Forms.MediaElement) una pagina che contiene controlli di trasporto personalizzati e a e :The following XAML example shows a page that contains a and custom transport controls:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MediaElementDemos.CustomTransportPage"
             Title="Custom transport">
    <Grid>
        ...
        <MediaElement x:Name="mediaElement"
                      AutoPlay="False"
                      ... />
        <StackLayout BindingContext="{x:Reference mediaElement}"
                     ...>
            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Playing}">
                        <Setter Property="Text"
                                Value="&#x23F8; Pause" />
                    </DataTrigger>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Buffering}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Stopped}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

In questo esempio, i controlli [`Button`](xref:Xamarin.Forms.Button) di trasporto personalizzati vengono definiti come oggetti. Tuttavia, sono `Button` presenti solo due `Button` oggetti, il primo che `Button` rappresenta **Play** e **Pause**e il secondo che rappresenta **Stop**. [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)Gli oggetti vengono utilizzati per attivare e disattivare i pulsanti e per alternare il primo pulsante tra **Riproduci** e **Pausa**. Per ulteriori informazioni sui trigger di dati, vedere [Xamarin.Forms Triggers](~/xamarin-forms/app-fundamentals/triggers.md).

Il file code-behind include i [`Clicked`](xref:Xamarin.Forms.Button.Clicked) gestori per gli eventi:The code-behind file has the handlers for the events:

```csharp
void OnPlayPauseButtonClicked(object sender, EventArgs args)
{
    if (mediaElement.CurrentState == MediaElementState.Stopped ||
        mediaElement.CurrentState == MediaElementState.Paused)
    {
        mediaElement.Play();
    }
    else if (mediaElement.CurrentState == MediaElementState.Playing)
    {
        mediaElement.Pause();
    }
}

void OnStopButtonClicked(object sender, EventArgs args)
{
    mediaElement.Stop();
}
```

Il pulsante **Riproduci** può essere premuto, una volta abilitato, per iniziare la riproduzione:

[![Screenshot di un oggetto MediaElement con controlli di trasporto personalizzati in iOS e Android](mediaelement-images/custom-transport-playback.png "MediaElement riproduzione di un video")](mediaelement-images/custom-transport-playback-large.png#lightbox "MediaElement riproduzione di un video")

Premendo il pulsante **Pausa** si ottiene una pausa per la riproduzione:

[![Screenshot di un oggetto MediaElement con riproduzione sospesa, in iOS e Android](mediaelement-images/custom-transport-paused.png "MediaElement con un video in pausa")](mediaelement-images/custom-transport-paused-large.png#lightbox "MediaElement con un video in pausa")

Premendo il pulsante **Stop** si interrompe la riproduzione e si riporta la posizione del file multimediale all'inizio.

## <a name="implement-a-custom-position-bar"></a>Implementare una barra di posizione personalizzataImplement a custom position bar

I controlli di trasporto implementati da ogni piattaforma includono un indicatore di posizione. Questa barra è simile a un dispositivo di scorrimento e mostra la posizione corrente del supporto entro la durata totale. Inoltre, è possibile manipolare la barra di posizione per spostarsi avanti o indietro in una nuova posizione nel video.

L'implementazione di una barra di posizione personalizzata richiede la conoscenza della durata del contenuto multimediale e della posizione di riproduzione corrente. Questi dati sono [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) disponibili [`Position`](xref:Xamarin.Forms.MediaElement.Position) nelle proprietà e .

> [!IMPORTANT]
> L'oggetto [`Position`](xref:Xamarin.Forms.MediaElement.Position) deve essere sottoposto a polling per ottenere dati di posizione accurati. Per ulteriori informazioni, consultate Poll per i [dati di posizione.](#poll-for-position-data)

Una barra di posizione personalizzata può essere implementata utilizzando un [`Slider`](xref:Xamarin.Forms.Slider)oggetto , come illustrato nell'esempio seguente:

```csharp
public class PositionSlider : Slider
{
    public static readonly BindableProperty DurationProperty =
        BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                propertyChanged: (bindable, oldValue, newValue) =>
                                {
                                    ((PositionSlider)bindable).SetTimeToEnd();
                                    double seconds = ((TimeSpan)newValue).TotalSeconds;
                                    ((Slider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                });

    public TimeSpan Duration
    {
        get { return (TimeSpan)GetValue(DurationProperty); }
        set { SetValue(DurationProperty, value); }
    }

    public static readonly BindableProperty PositionProperty =
        BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                propertyChanged: (bindable, oldValue, newValue) => ((PositionSlider)bindable).SetTimeToEnd());

    public TimeSpan Position
    {
        get { return (TimeSpan)GetValue(PositionProperty); }
        set { SetValue(PositionProperty, value); }
    }

    static readonly BindablePropertyKey TimeToEndPropertyKey =
        BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan());

    public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

    public TimeSpan TimeToEnd
    {
        get { return (TimeSpan)GetValue(TimeToEndProperty); }
        private set { SetValue(TimeToEndPropertyKey, value); }
    }

    public PositionSlider()
    {
        PropertyChanged += (sender, args) =>
        {
            if (args.PropertyName == "Value")
            {
                TimeSpan newPosition = TimeSpan.FromSeconds(Value);
                if (Math.Abs(newPosition.TotalSeconds - Position.TotalSeconds) / Duration.TotalSeconds > 0.01)
                {
                    Position = newPosition;
                }
            }
        };
    }

    void SetTimeToEnd()
    {
        TimeToEnd = Duration - Position;
    }
}
```

La `PositionSlider` classe definisce `Duration` `Position` le proprie proprietà `TimeToEnd` associabili e una proprietà associabile. Tutte e tre `TimeSpan`le proprietà sono di tipo . Il gestore di `Duration` proprietà modificata per [`Slider`](xref:Xamarin.Forms.Slider) la `TotalSeconds` proprietà `TimeSpan` imposta la `Maximum` proprietà dell'oggetto sulla proprietà del valore. La `TimeToEnd` proprietà viene calcolata `Duration` in `Position` base alle modifiche apportate alle proprietà e e inizia alla durata del supporto e diminuisce fino a zero man mano che la riproduzione procede.

L'oggetto `PositionSlider` viene [`Slider`](xref:Xamarin.Forms.Slider) aggiornato `Slider` dal sottostante quando l'oggetto viene spostato per indicare che il supporto deve essere avanzato o invertito in una nuova posizione. Questo viene rilevato `PropertyChanged` nel `PositionSlider` gestore nel costruttore. Il gestore cerca una modifica nella proprietà `Value` e se la proprietà è diversa dalla proprietà `Position`, la proprietà `Position` viene impostata dalla proprietà `Value`. Per ulteriori informazioni [`Slider`](xref:Xamarin.Forms.Slider) sull'utilizzo di un dispositivo di [scorrimento See Xamarin.Forms](~/xamarin-forms/user-interface/slider.md)

> [!NOTE]
> Su Android, [`Slider`](xref:Xamarin.Forms.Slider) l'unico ha 1000 passaggi `Minimum` discreti, indipendentemente dalle impostazioni e `Maximum` . Se la lunghezza del supporto è maggiore di `Position` 1000 secondi, due valori diversi corrisponderebbero allo stesso `Value` valore di `Slider`. Questo è il motivo per cui il codice precedente controlla che la nuova posizione e la posizione esistente siano maggiori di un centesimo della durata complessiva.

L'esempio seguente `PositionSlider` mostra l'essere consumato in una pagina:The following example shows the being consumed on a page:

```xaml
<controls:PositionSlider x:Name="positionSlider"
                         BindingContext="{x:Reference mediaElement}"
                         Duration="{Binding Duration}"
                         ValueChanged="OnPositionSliderValueChanged">
    <controls:PositionSlider.Triggers>
        <DataTrigger TargetType="controls:PositionSlider"
                     Binding="{Binding CurrentState}"
                     Value="{x:Static MediaElementState.Buffering}">
            <Setter Property="IsEnabled" Value="False" />
        </DataTrigger>
    </controls:PositionSlider.Triggers>
</controls:PositionSlider>
```

In questo esempio, `Duration` la `PositionSlider` proprietà dell'oggetto [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) è [`MediaElement`](xref:Xamarin.Forms.MediaElement)associata a dati alla proprietà dell'oggetto . Quando [`Value`](xref:Xamarin.Forms.Slider.Value) la proprietà [`Slider`](xref:Xamarin.Forms.Slider) delle `ValueChanged` modifiche, l'evento viene generato e viene eseguito il `OnPositionSliderValueChanged` gestore. Questo gestore [`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position) imposta la proprietà `PositionSlider.Position` sul valore della proprietà. Pertanto, `Slider` trascinando i risultati nella modifica della posizione di riproduzione multimediale:

[![Screenshot di un oggetto MediaElement con una barra di posizione personalizzata in iOS e Android](mediaelement-images/custom-position-bar.png "MediaElement con una barra di posizione personalizzataMediaElement with a custom position bar")](mediaelement-images/custom-position-bar-large.png#lightbox "MediaElement con una barra di posizione personalizzataMediaElement with a custom position bar")

Inoltre, un [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) oggetto viene utilizzato `PositionSlider` per disabilitare il quando il supporto è il buffering. Per ulteriori informazioni sui trigger di dati, vedere [Xamarin.Forms Triggers](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="implement-a-custom-volume-control"></a>Implementare un controllo del volume personalizzatoImplement a custom volume control

I controlli di riproduzione multimediale implementati da ogni piattaforma includono una barra del volume. Questa barra è simile a un dispositivo di scorrimento e mostra il volume del supporto. Inoltre, è possibile manipolare la barra del volume per aumentare o diminuire il volume.

Una barra del volume personalizzata può essere implementata utilizzando un [`Slider`](xref:Xamarin.Forms.Slider)oggetto , come illustrato nell'esempio seguente:A custom volume bar can be implemented using a , as shown in the following example:

```xaml
<StackLayout>
    <MediaElement AutoPlay="False"
                  Source="{StaticResource AdvancedAsync}" />
    <Slider Maximum="1.0"
            Minimum="0.0"
            Value="{Binding Volume}"
            Rotation="270"
            WidthRequest="100" />
</StackLayout>
```

In questo esempio, i [`Slider`](xref:Xamarin.Forms.Slider) `Value` dati associano la proprietà alla [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) proprietà dell'oggetto [`MediaElement`](xref:Xamarin.Forms.MediaElement). Ciò è `Volume` possibile perché `TwoWay` la proprietà utilizza un'associazione. Pertanto, `Value` la modifica della `Volume` proprietà comporterà la modifica della proprietà.

> [!NOTE]
> La [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) proprietà dispone di un callback vlidation che garantisce che il valore sia maggiore o uguale a 0,0 e minore o uguale a 1,0.

Per ulteriori informazioni [`Slider`](xref:Xamarin.Forms.Slider) sull'utilizzo di un dispositivo di [scorrimento See Xamarin.Forms](~/xamarin-forms/user-interface/slider.md)

## <a name="related-links"></a>Collegamenti correlati

- [MediaElementDemos (esempio)MediaElementDemos (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)
- [Schemi URI](/windows/uwp/app-resources/uri-schemes)
- [Trigger Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Dispositivo di scorrimento Xamarin.Forms](~/xamarin-forms/user-interface/slider.md)
- [Android: formati multimediali supportati](https://developer.android.com/guide/topics/media/media-formats)
- [UWP: Codec supportati](/windows/uwp/audio-video-camera/supported-codecs)
