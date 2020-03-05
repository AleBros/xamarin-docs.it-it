---
title: MediaElement Novell. Forms
description: Questo articolo illustra come usare MediaElement per riprodurre video e audio in un'applicazione Novell. Forms.
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2020
ms.openlocfilehash: 76a30bfb17c8263345d781386acf9d34c01aae3b
ms.sourcegitcommit: 5b6d3bddf7148f8bb374de5657bdedc125d72ea7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78292238"
---
# <a name="xamarinforms-mediaelement"></a>MediaElement Novell. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/MediaElementDemos)

[`MediaElement`](xref:Xamarin.Forms.MediaElement) è una vista per la riproduzione di video e audio. Il supporto supportato dalla piattaforma sottostante può essere riprodotto dalle origini seguenti:

- Il Web, usando un URI (HTTP o HTTPS).
- Risorsa incorporata nell'applicazione della piattaforma, usando lo schema URI `ms-appx:///`.
- File che provengono dalle cartelle di dati locali e temporanee dell'app, usando lo schema URI `ms-appdata:///`.
- Libreria del dispositivo.

[`MediaElement`](xref:Xamarin.Forms.MediaElement) possibile utilizzare i controlli di riproduzione della piattaforma, denominati controlli di trasporto. Tuttavia, sono disabilitati per impostazione predefinita e possono essere sostituiti con i controlli di trasporto personalizzati. Gli screenshot seguenti mostrano `MediaElement` riproduzione di un video con i controlli di trasporto della piattaforma:

[![Screenshot di un MediaElement che riproduce un video, in iOS e Android](mediaelement-images/playback-controls.png "MediaElement che riproduce un video")](mediaelement-images/playback-controls-large.png#lightbox "MediaElement che riproduce un video")

[`MediaElement`](xref:Xamarin.Forms.MediaElement) è disponibile in Novell. forms 4,5. Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la riga di codice seguente al file *app.XAML.cs* :

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement) è disponibile in iOS, Android, il piattaforma UWP (Universal Windows Platform) (UWP), macOS, Windows Presentation Foundation e Tizen.

[`MediaElement`](xref:Xamarin.Forms.MediaElement) definisce le proprietà seguenti:

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect), di tipo [`Aspect`](xref:Xamarin.Forms.Aspect), determina il modo in cui il supporto verrà ridimensionato per adattarsi all'area di visualizzazione. Il valore predefinito di questa proprietà è `AspectFit`.
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay), di tipo `bool`, indica se la riproduzione dei supporti viene avviata automaticamente quando viene impostata la proprietà [`Source`](xref:Xamarin.Forms.MediaElement.Source) . Il valore predefinito di questa proprietà è `true`.
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress), di tipo `double`, indica lo stato di avanzamento del buffer corrente. Il valore predefinito di questa proprietà è 0,0.
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek), di tipo `bool`, indica se il supporto può essere riposizionato impostando il valore della proprietà [`Position`](xref:Xamarin.Forms.MediaElement.Position) . Questa è una proprietà di sola lettura.
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), di tipo [`MediaElementState`](xref:Xamarin.Forms.MediaElementState), indica lo stato corrente del controllo. Si tratta di una proprietà di sola lettura il cui valore predefinito è `MediaElementState.Closed`.
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration), di tipo `TimeSpan?`, indica la durata del supporto attualmente aperto. Si tratta di una proprietà di sola lettura il cui valore predefinito è `null`.
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping), di tipo `bool`, descrive se l'origine multimediale attualmente caricata deve riprendere la riproduzione dall'inizio dopo il raggiungimento della fine. Il valore predefinito di questa proprietà è `false`.
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn), di tipo `bool`, determina se lo schermo del dispositivo deve rimanere acceso durante la riproduzione dei supporti. Il valore predefinito di questa proprietà è `false`.
- [`Position`](xref:Xamarin.Forms.MediaElement.Position), di tipo `TimeSpan`, descrive lo stato di avanzamento corrente tramite il tempo di riproduzione dei supporti. Il valore predefinito di questa proprietà è `TimeSpan.Zero`.
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls), di tipo `bool`, determina se vengono visualizzati i controlli di riproduzione delle piattaforme. Il valore predefinito di questa proprietà è `false`.
- [`Source`](xref:Xamarin.Forms.MediaElement.Source), di tipo [`MediaSource`](xref:Xamarin.Forms.MediaSource), indica l'origine del supporto caricato nel controllo.
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight), di tipo `int`, indica l'altezza del controllo. Questa è una proprietà di sola lettura.
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth), di tipo `int`, indica la larghezza del controllo. Questa è una proprietà di sola lettura.
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume), di tipo `double`, determina il volume del supporto, rappresentato su una scala lineare tra 0 e 1. Questa proprietà usa un'associazione `TwoWay` e il valore predefinito è 1.

Queste proprietà, ad eccezione della `CanSeek` proprietà, sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

La classe [`MediaElement`](xref:Xamarin.Forms.MediaElement) definisce anche quattro eventi:

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened) viene generato quando il flusso multimediale è stato convalidato e aperto.
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded) viene generato quando il `MediaElement` completa la riproduzione dei supporti.
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed) viene generato quando si verifica un errore associato all'origine del supporto.
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted) viene generato quando il punto di ricerca di un'operazione di ricerca richiesta è pronto per la riproduzione.

Inoltre, [`MediaElement`](xref:Xamarin.Forms.MediaElement) include i metodi [`Play`](xref:Xamarin.Forms.MediaElement.Play), [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)e [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) .

Per informazioni sui formati multimediali supportati in Android, vedere [formati multimediali supportati](https://developer.android.com/guide/topics/media/media-formats) in Developer.Android.com. Per informazioni sui formati multimediali supportati nella piattaforma UWP (Universal Windows Platform) (UWP), vedere [codec supportati](/windows/uwp/audio-video-camera/supported-codecs).

## <a name="play-remote-media"></a>Riproduzione di file multimediali remoti

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) può riprodurre file multimediali remoti usando gli schemi URI http e HTTPS. Questa operazione viene eseguita impostando la proprietà [`Source`](xref:Xamarin.Forms.MediaElement.Source) sull'URI del file multimediale:

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

Per impostazione predefinita, il supporto definito dalla proprietà [`Source`](xref:Xamarin.Forms.MediaElement.Source) viene riprodotto immediatamente dopo l'apertura del supporto. Per disattivare la riproduzione automatica dei supporti, impostare la proprietà [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay) su `false`.

I controlli per la riproduzione dei supporti sono disabilitati per impostazione predefinita e vengono abilitati impostando la proprietà [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls) su `true`. [`MediaElement`](xref:Xamarin.Forms.MediaElement) utilizzerà quindi i controlli di riproduzione della piattaforma.

## <a name="play-local-media"></a>Riprodurre supporti locali

I supporti locali possono essere riprodotti dalle origini seguenti:

- Risorsa incorporata nell'applicazione della piattaforma, usando lo schema URI `ms-appx:///`.
- File che provengono dalle cartelle di dati locali e temporanee dell'app, usando lo schema URI `ms-appdata:///`.
- Libreria del dispositivo.

Per ulteriori informazioni su questi schemi URI, vedere [schemi URI](/windows/uwp/app-resources/uri-schemes).

### <a name="play-media-embedded-in-the-app-package"></a>Riprodurre contenuti multimediali incorporati nel pacchetto dell'app

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) può riprodurre file multimediali incorporati nel pacchetto dell'app, usando lo schema URI di `ms-appx:///`. I file multimediali sono incorporati nel pacchetto dell'app inserendoli nel progetto Platform.

L'archiviazione di un file multimediale nel progetto della piattaforma è diversa per ogni piattaforma:

- In iOS, i file multimediali devono essere archiviati nella cartella **risorse** o in una sottocartella della cartella **risorse** . Il file multimediale deve avere una `Build Action` di `BundleResource`.
- In Android, i file multimediali devono essere archiviati in una sottocartella di **risorse** denominate **RAW**. La cartella **raw** non può contenere sottocartelle. Il file multimediale deve avere una `Build Action` di `AndroidResource`.
- In UWP i file multimediali possono essere archiviati in qualsiasi cartella del progetto. Il file multimediale deve avere una `BuildAction` di `Content`.

Per riprodurre i file multimediali che soddisfano questi criteri, è possibile usare lo schema URI `ms-appx:///`:

```xaml
<MediaElement Source="ms-appx:///XamarinForms101UsingEmbeddedImages.mp4"
              ShowsPlaybackControls="True" />
```

Quando si usa data binding, è possibile usare un convertitore di valori per applicare questo schema URI:

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

È quindi possibile usare un'istanza del `VideoSourceConverter` per applicare lo schema URI `ms-appx:///` a un file multimediale incorporato:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Per ulteriori informazioni sullo schema URI ms-appx, vedere [ms-appx e ms-appx-web](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web).

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>Riprodurre file multimediali dalle cartelle locali e temporanee dell'app

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) può riprodurre file multimediali che vengono copiati nelle cartelle di dati locali o temporanee dell'app, usando lo schema URI di `ms-appdata:///`.

Nell'esempio seguente viene illustrata la proprietà [`Source`](xref:Xamarin.Forms.MediaElement.Source) impostata su un file multimediale archiviato nella cartella di dati locale dell'app:

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

Nell'esempio seguente viene illustrata la proprietà [`Source`](xref:Xamarin.Forms.MediaElement.Source) a un file multimediale archiviato nella cartella temporanea dei dati dell'app:

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> Oltre a riprodurre file multimediali archiviati nelle cartelle di dati locali o temporanee dell'app, UWP può anche riprodurre file multimediali che si trovano nella cartella roaming dell'app. Per ottenere questo risultato, è possibile predisporre il file multimediale con `ms-appdata:///roaming/`.

Quando si usa data binding, è possibile usare un convertitore di valori per applicare questo schema URI:

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

È quindi possibile usare un'istanza del `VideoSourceConverter` per applicare lo schema URI `ms-appdata:///` a un file multimediale nella cartella dati locale o temporanea dell'app:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Per ulteriori informazioni sullo schema URI ms-appdata, vedere [ms-appdata](/windows/uwp/app-resources/uri-schemes#ms-appdata).

#### <a name="copying-a-media-file-to-the-apps-local-or-temporary-data-folder"></a>Copia di un file multimediale nella cartella dati locale o temporanea dell'app

Per la riproduzione di un file multimediale archiviato nella cartella locale o temporanea dei dati dell'app, è necessario che il file multimediale venga copiato dall'app. Questa operazione può essere eseguita, ad esempio, copiando un file multimediale dal pacchetto dell'app:

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
> L'esempio di codice precedente usa la classe `FileSystem` inclusa in Novell. Essentials. Per altre informazioni, vedere [Novell. Essentials: helper del file System](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android).

### <a name="play-media-from-the-device-library"></a>Riprodurre file multimediali dalla libreria di dispositivi

La maggior parte dei dispositivi mobili e dei computer desktop moderni è in grado di registrare video e audio usando la fotocamera e il microfono del dispositivo. I supporti creati vengono quindi archiviati come file nel dispositivo. Questi file possono essere recuperati dalla libreria e riprodotti dal [`MediaElement`](xref:Xamarin.Forms.MediaElement).

Ognuna delle piattaforme include una funzionalità che consente all'utente di selezionare i supporti dalla libreria del dispositivo. In Novell. Forms, i progetti Platform possono richiamare questa funzionalità e possono essere chiamati dalla classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

Il servizio di dipendenza video picking usato nell'applicazione di esempio è molto simile a quello definito nella [raccolta di una foto dalla raccolta immagini](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), tranne per il fatto che il selettore restituisce un nome di file anziché un oggetto `Stream`. Il progetto di codice condiviso definisce un'interfaccia denominata `IVideoPicker`, che definisce un solo metodo denominato `GetVideoFileAsync`. Ogni piattaforma implementa quindi questa interfaccia in una classe `VideoPicker`.

Nell'esempio di codice seguente viene illustrato come recuperare un file multimediale dalla libreria di dispositivi:

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

Il servizio di dipendenza video picking viene richiamato chiamando il metodo `DependencyService.Get` per ottenere l'implementazione di un'interfaccia `IVideoPicker` nel progetto Platform. Il metodo `GetVideoFileAsync` viene quindi chiamato su tale istanza e il nome file restituito viene usato per creare un oggetto [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) e per impostarlo sulla proprietà [`Source`](xref:Xamarin.Forms.MediaElement.Source) della [`MediaElement`](xref:Xamarin.Forms.MediaElement).

## <a name="change-video-aspect-ratio"></a>Cambia proporzioni video

La proprietà [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect) determina il modo in cui i supporti video verranno ridimensionati per adattarsi all'area di visualizzazione. Per impostazione predefinita, questa proprietà è impostata sul membro dell'enumerazione `AspectFit`, ma può essere impostata su uno dei membri dell'enumerazione [`Aspect`](xref:Xamarin.Forms.Aspect) :

- `AspectFit` indica che il video sarà sottoposto a Letterbox, se necessario, per adattarlo all'area di visualizzazione, mantenendo al tempo stesso le proporzioni.
- `AspectFill` indica che il video verrà ritagliato in modo da riempire l'area di visualizzazione, mantenendo al tempo stesso le proporzioni.
- `Fill` indica che il video verrà allungato per riempire l'area di visualizzazione.

## <a name="poll-for-position-data"></a>Polling dei dati sulla posizione

La notifica della modifica delle proprietà per la proprietà [`Position`](xref:Xamarin.Forms.MediaElement.Position) associabile viene attivata solo in momenti chiave, ad esempio inizio e fine della riproduzione, e in fase di sospensione. Pertanto, data binding alla proprietà `Position` non restituirà dati di posizione accurati. È necessario invece configurare un timer ed eseguire il polling della proprietà.

Per eseguire questa operazione, è necessario eseguire l'override del `OnAppearing` per la pagina che richiede la posizione dei dati come supporto:

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

In questo esempio, l'override del `OnAppearing` avvia un timer che aggiorna `positionLabel` con il valore `Position` ogni secondo. Il callback del timer viene richiamato ogni secondo, fino a quando il callback non restituisce `false`. Quando si verifica la navigazione della pagina, viene eseguito il `OnDisappearing` override, che interrompe il callback del timer richiamato.

## <a name="understand-mediasource-types"></a>Informazioni sui tipi di MediaSource

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) può riprodurre supporti impostando la relativa proprietà [`Source`](xref:Xamarin.Forms.MediaElement.Source) su un file multimediale remoto o locale. La proprietà `Source` è di tipo [`MediaSource`](xref:Xamarin.Forms.MediaSource)e questa classe definisce due metodi statici:

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*)restituisce un'istanza [`MediaSource`](xref:Xamarin.Forms.MediaSource) da un argomento `string`.
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*)restituisce un'istanza [`MediaSource`](xref:Xamarin.Forms.MediaSource) da un argomento `Uri`.

Inoltre, la classe [`MediaSource`](xref:Xamarin.Forms.MediaSource) dispone anche di operatori impliciti che restituiscono `MediaSource` istanze da `string` e `Uri` argomenti.

> [!NOTE]
> Quando la proprietà [`Source`](xref:Xamarin.Forms.MediaElement.Source) è impostata in XAML, viene richiamato un convertitore di tipi per restituire un'istanza di [`MediaSource`](xref:Xamarin.Forms.MediaSource) da un `string` o `Uri`.

La classe [`MediaSource`](xref:Xamarin.Forms.MediaSource) dispone anche di due classi derivate:

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource), che consente di specificare un file multimediale remoto da un URI. Questa classe dispone di una proprietà [`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri) che può essere impostata su una `Uri`.
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource), che consente di specificare un file multimediale locale da una `string`. Questa classe dispone di una proprietà [`File`](xref:Xamarin.Forms.FileMediaSource.File) che può essere impostata su una `string`. Inoltre, questa classe dispone di operatori impliciti per convertire un `string` in un oggetto `FileMediaSource` e un oggetto `FileMediaSource` in un `string`.

> [!NOTE]
> Quando viene creato un oggetto [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) in XAML, viene richiamato un convertitore di tipi per restituire un'istanza di [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) da un `string`.

## <a name="determine-mediaelement-status"></a>Determinare lo stato di MediaElement

La classe [`MediaElement`](xref:Xamarin.Forms.MediaElement) definisce una proprietà associabile di sola lettura denominata [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), di tipo [`MediaElementState`](xref:Xamarin.Forms.MediaElementState). Questa proprietà indica lo stato corrente del controllo, ad esempio se il supporto viene riprodotto o sospeso o se non è ancora pronto per riprodurre i supporti.

L'enumerazione [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) definisce i membri seguenti:

- `Closed` indica che il `MediaElement` non contiene supporti.
- `Opening` indica che il `MediaElement` sta convalidando e tentando di caricare l'origine specificata.
- `Buffering` indica che il `MediaElement` sta caricando il supporto per la riproduzione. La proprietà [`Position`](xref:Xamarin.Forms.MediaElement.Position) non avanza durante questo stato. Se il `MediaElement` stava riproducendo video, continuerà a visualizzare l'ultimo fotogramma visualizzato.
- `Playing` indica che il `MediaElement` sta riproducendo l'origine multimediale.
- `Paused` indica che il `MediaElement` non sposta in avanti la relativa proprietà [`Position`](xref:Xamarin.Forms.MediaElement.Position) . Se il `MediaElement` stava riproducendo video, continuerà a visualizzare il frame corrente.
- `Stopped` indica che il `MediaElement` contiene supporti, ma non viene riprodotto o sospeso. Il [`Position`](xref:Xamarin.Forms.MediaElement.Position) proprietà è 0 e non avanza. Se il supporto caricato è video, il `MediaElement` Visualizza il primo frame.

Non è in genere necessario esaminare la proprietà [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) quando si usano i controlli di trasporto [`MediaElement`](xref:Xamarin.Forms.MediaElement) . Tuttavia, questa proprietà diventa importante quando si implementano controlli di trasporto personalizzati.

## <a name="implement-custom-transport-controls"></a>Implementare controlli di trasporto personalizzati

I controlli di trasporto di un lettore multimediale includono i pulsanti che eseguono le funzioni **Play**, **pause**e **Stop**. Questi pulsanti sono identificati in genere con icone note anziché testo e le funzioni di **riproduzione** e **pausa** sono in genere unite in un unico pulsante.

Per impostazione predefinita, i controlli di riproduzione [`MediaElement`](xref:Xamarin.Forms.MediaElement) sono disabilitati. Ciò consente di controllare la `MediaElement` a livello di codice o di fornire controlli di trasporto personalizzati. In questo `MediaElement` sono inclusi i metodi [`Play`](xref:Xamarin.Forms.MediaElement.Play), [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)e [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) .

Nell'esempio di codice XAML riportato di seguito viene illustrata una pagina che contiene un [`MediaElement`](xref:Xamarin.Forms.MediaElement) e controlli di trasporto personalizzati:

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

In questo esempio, i controlli di trasporto personalizzati vengono definiti come oggetti [`Button`](xref:Xamarin.Forms.Button) . Esistono tuttavia solo due oggetti `Button`, con la prima `Button` che rappresenta la **riproduzione** e la **pausa**e la seconda `Button` che rappresenta l' **arresto**. [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) oggetti vengono usati per abilitare e disabilitare i pulsanti e per cambiare il primo pulsante tra **Play** e **pause**. Per ulteriori informazioni sui trigger di dati, vedere [trigger Novell. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

Il file code-behind contiene i gestori per gli eventi [`Clicked`](xref:Xamarin.Forms.Button.Clicked) :

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

[![Screenshot di un MediaElement con controlli di trasporto personalizzati in iOS e Android](mediaelement-images/custom-transport-playback.png "MediaElement che riproduce un video")](mediaelement-images/custom-transport-playback-large.png#lightbox "MediaElement che riproduce un video")

La pressione del pulsante **Sospendi** comporta la sospensione della riproduzione:

[![Screenshot di un MediaElement con riproduzione sospesa, in iOS e Android](mediaelement-images/custom-transport-paused.png "MediaElement con un video sospeso")](mediaelement-images/custom-transport-paused-large.png#lightbox "MediaElement con un video sospeso")

Premendo il pulsante **Interrompi** viene arrestata la riproduzione e viene restituita la posizione del file multimediale all'inizio.

## <a name="implement-a-custom-position-bar"></a>Implementare una barra di posizione personalizzata

I controlli di trasporto implementati da ogni piattaforma includono un indicatore di posizione. Questa barra è simile a un dispositivo di scorrimento e Mostra la posizione corrente del supporto entro la durata totale. Inoltre, è possibile modificare la barra di posizione per spostarsi avanti o indietro in una nuova posizione nel video.

Per implementare una barra di posizionamento personalizzata è necessario conoscere la durata del supporto e la posizione di riproduzione corrente. Questi dati sono disponibili nelle proprietà [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) e [`Position`](xref:Xamarin.Forms.MediaElement.Position) .

> [!IMPORTANT]
> È necessario eseguire il polling del [`Position`](xref:Xamarin.Forms.MediaElement.Position) per ottenere i dati di posizione accurati. Per altre informazioni, vedere eseguire il [polling dei dati di posizione](#poll-for-position-data).

Una barra di posizionamento personalizzata può essere implementata usando un [`Slider`](xref:Xamarin.Forms.Slider), come illustrato nell'esempio seguente:

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

La classe `PositionSlider` definisce le proprie `Duration` e `Position` proprietà associabili e una proprietà `TimeToEnd` associabile. Tutte e tre le proprietà sono di tipo `TimeSpan`. Il gestore della proprietà modificata per la proprietà `Duration` imposta la proprietà `Maximum` della [`Slider`](xref:Xamarin.Forms.Slider) sulla proprietà `TotalSeconds` del valore di `TimeSpan`. La proprietà `TimeToEnd` viene calcolata in base alle modifiche apportate alle proprietà `Duration` e `Position` e inizia in base alla durata del supporto e diminuisce fino a zero mentre la riproduzione continua.

Il `PositionSlider` viene aggiornato dall' [`Slider`](xref:Xamarin.Forms.Slider) sottostante quando il `Slider` viene spostato per indicare che il supporto deve essere avanzato o invertito in una nuova posizione. Questa operazione viene rilevata nel gestore di `PropertyChanged` nel costruttore di `PositionSlider`. Il gestore cerca una modifica nella proprietà `Value` e se la proprietà è diversa dalla proprietà `Position`, la proprietà `Position` viene impostata dalla proprietà `Value`. Per ulteriori informazioni sull'utilizzo di un [`Slider`](xref:Xamarin.Forms.Slider) vedere il [dispositivo di scorrimento Novell. Forms](~/xamarin-forms/user-interface/slider.md)

> [!NOTE]
> In Android, il [`Slider`](xref:Xamarin.Forms.Slider) dispone solo di 1000 passaggi discreti, indipendentemente dalle impostazioni `Minimum` e `Maximum`. Se la lunghezza del supporto è maggiore di 1000 secondi, due valori `Position` diversi corrisponderanno allo stesso `Value` della `Slider`. Questo è il motivo per cui il codice precedente verifica che la nuova posizione e la posizione esistente siano superiori a un centesimo della durata complessiva.

Nell'esempio seguente viene illustrata la `PositionSlider` utilizzata in una pagina:

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

In questo esempio, la proprietà `Duration` del `PositionSlider` è associata a dati alla proprietà [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) della [`MediaElement`](xref:Xamarin.Forms.MediaElement). Quando la proprietà [`Value`](xref:Xamarin.Forms.Slider.Value) della [`Slider`](xref:Xamarin.Forms.Slider) viene modificata, viene generato l'evento `ValueChanged` e viene eseguito il gestore `OnPositionSliderValueChanged`. Questo gestore imposta la proprietà [`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position) sul valore della proprietà `PositionSlider.Position`. Pertanto, il trascinamento del `Slider` comporta la modifica della posizione di riproduzione dei supporti:

[![Screenshot di un MediaElement con una barra di posizione personalizzata, in iOS e Android](mediaelement-images/custom-position-bar.png "MediaElement con una barra di posizione personalizzata")](mediaelement-images/custom-position-bar-large.png#lightbox "MediaElement con una barra di posizione personalizzata")

Inoltre, un oggetto [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) viene usato per disabilitare l'`PositionSlider` quando il contenuto multimediale è memorizzato nel buffer. Per ulteriori informazioni sui trigger di dati, vedere [trigger Novell. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="implement-a-custom-volume-control"></a>Implementare un controllo volume personalizzato

I controlli di riproduzione multimediale implementati da ogni piattaforma includono una barra del volume. Questa barra è simile a un dispositivo di scorrimento e Mostra il volume del supporto. Inoltre, è possibile modificare la barra del volume per aumentare o diminuire il volume.

Una barra del volume personalizzata può essere implementata usando un [`Slider`](xref:Xamarin.Forms.Slider), come illustrato nell'esempio seguente:

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

In questo esempio, il [`Slider`](xref:Xamarin.Forms.Slider) dati associa la relativa proprietà `Value` alla proprietà [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) della [`MediaElement`](xref:Xamarin.Forms.MediaElement). Questa operazione è possibile perché la proprietà `Volume` usa un'associazione di `TwoWay`. Pertanto, la modifica della proprietà `Value` comporterà la modifica della proprietà `Volume`.

> [!NOTE]
> La proprietà [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) dispone di un callback vlidation che assicura che il valore sia maggiore o uguale a 0,0 e minore o uguale a 1,0.

Per ulteriori informazioni sull'utilizzo di un [`Slider`](xref:Xamarin.Forms.Slider) vedere il [dispositivo di scorrimento Novell. Forms](~/xamarin-forms/user-interface/slider.md)

## <a name="related-links"></a>Collegamenti correlati

- [MediaElementDemos (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/MediaElementDemos)
- [Schemi URI](/windows/uwp/app-resources/uri-schemes)
- [Trigger di Novell. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Dispositivo di scorrimento Novell. Forms](~/xamarin-forms/user-interface/slider.md)
- [Android: formati multimediali supportati](https://developer.android.com/guide/topics/media/media-formats)
- [UWP: codec supportati](/windows/uwp/audio-video-camera/supported-codecs)
