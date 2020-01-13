---
title: Posizionamento del video personalizzato
description: Questo articolo illustra come implementare un indicatore di posizione personalizzato in un'applicazione lettore video con Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 6D792264-30FF-46F7-8C1B-2FEF9D277DF4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 12633b728240c2f90d0265fe7b9efb65ea49bf1f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650651"
---
# <a name="custom-video-positioning"></a>Posizionamento del video personalizzato

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

I controlli di trasporto implementati da ogni piattaforma includono un indicatore di posizione. L'indicatore è simile a un dispositivo di scorrimento o una barra di scorrimento e mostra la posizione corrente del video all'interno della durata totale. Inoltre, l'utente può modificare l'indicatore di posizione per spostarsi avanti o indietro in una nuova posizione nel video.

Questo articolo illustra come implementare l'indicatore di posizione personalizzato.

## <a name="the-duration-property"></a>Proprietà Duration

Un'informazione necessaria a `VideoPlayer` per supportare un indicatore di posizione personalizzato è la durata del video. `VideoPlayer` definisce una proprietà `Duration` di sola lettura di tipo `TimeSpan`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Duration read-only property
        private static readonly BindablePropertyKey DurationPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Duration), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public static readonly BindableProperty DurationProperty = DurationPropertyKey.BindableProperty;

        public TimeSpan Duration
        {
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        TimeSpan IVideoPlayerController.Duration
        {
            set { SetValue(DurationPropertyKey, value); }
            get { return Duration; }
        }
        ···
    }
}
```

Analogamente alla proprietà `Status` descritta nell'[articolo precedente](custom-transport.md), questa proprietà `Duration` è di sola lettura. Viene definita con una `BindablePropertyKey` privata e può essere impostata solo facendo riferimento all'interfaccia `IVideoPlayerController` che include questa proprietà `Duration`:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

Si noti anche il gestore di proprietà modificata che chiama un metodo denominato `SetTimeToEnd` descritto più avanti in questo articolo.

La durata di un video *non* è disponibile immediatamente dopo l'impostazione della proprietà `Source` di `VideoPlayer`. Il file video deve essere scaricato parzialmente prima che il lettore video sottostante possa determinarne la durata.

Di seguito è descritto il modo in cui ogni renderer di piattaforma ottiene la durata del video:

### <a name="video-duration-in-ios"></a>Durata del video in iOS

In iOS la durata di un video viene ottenuta dalla proprietà `Duration` di `AVPlayerItem`, ma non immediatamente dopo la creazione di `AVPlayerItem`. Sebbene sia possibile impostare un osservatore iOS per la proprietà `Duration`, `VideoPlayerRenderer` ottiene la durata nel metodo `UpdateStatus` che viene chiamato a 10 volte al secondo:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ((IVideoPlayerController)Element).Duration = ConvertTime(playerItem.Duration);
                ···
            }
        }

        TimeSpan ConvertTime(CMTime cmTime)
        {
            return TimeSpan.FromSeconds(Double.IsNaN(cmTime.Seconds) ? 0 : cmTime.Seconds);

        }
        ···
    }
}
```

Il metodo `ConvertTime` converte un oggetto `CMTime` in un valore `TimeSpan`.

### <a name="video-duration-in-android"></a>Durata del video in Android

La proprietà `Duration` di `VideoView` Android indica una durata valida in pochi millisecondi quando viene attivato l'evento `Prepared` di `VideoView`. La classe `VideoPlayerRenderer` Android usa il gestore per ottenere la proprietà `Duration`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            ···
            ((IVideoPlayerController)Element).Duration = TimeSpan.FromMilliseconds(videoView.Duration);
        }
        ···
    }
}
```

### <a name="video-duration-in-uwp"></a>Durata del video in UWP

La proprietà `NaturalDuration` di `MediaElement` è un valore `TimeSpan` che diventa valido quando `MediaElement` attiva l'evento `MediaOpened`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementMediaOpened(object sender, RoutedEventArgs args)
        {
            ((IVideoPlayerController)Element).Duration = Control.NaturalDuration.TimeSpan;
        }
        ···
    }
}
```

## <a name="the-position-property"></a>Proprietà Position

`VideoPlayer` richiede anche una proprietà `Position` che aumenta da zero a `Duration` durante la riproduzione del video. `VideoPlayer` implementa questa proprietà come la proprietà `Position` nel `MediaElement` UWP, una normale proprietà associabile con le funzioni di accesso pubbliche `set` e `get`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Position property
        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }
        ···
    }
}
```

Sebbene la funzione di accesso `get` restituisca la posizione corrente del video durante la riproduzione, la funzione di accesso `set` è progettata per rispondere alla manipolazione dell'utente dell'indicatore di posizione tramite lo spostamento in avanti e indietro della posizione del video.

In iOS e Android la proprietà che ottiene la posizione corrente ha solo la funzione di accesso `get` ed è disponibile un metodo `Seek` per eseguire questa seconda attività. Un metodo `Seek` separato sembra essere un approccio maggiormente sensibile rispetto a una singola proprietà `Position`. Una singola proprietà `Position` comporta un problema: durante la riproduzione del video la proprietà `Position` deve essere aggiornata continuamente per riflettere la nuova posizione. Tuttavia, la maggior parte delle modifiche alla proprietà `Position` non deve causare lo spostamento del lettore video in una nuova posizione nel video. Se così fosse, il lettore video risponderebbe cercando l'ultimo valore della proprietà `Position` e il video non avanzerebbe.

Nonostante le difficoltà di implementazione di una proprietà `Position` con le funzioni di accesso `set` e `get`, è stato scelto questo approccio perché coerente con `MediaElement` UWP e perché offre un grande vantaggio con il data binding: la proprietà `Position` di `VideoPlayer` può essere associata al dispositivo di scorrimento usato sia per visualizzare la posizione che per effettuare la ricerca in una nuova posizione. Tuttavia, sono necessarie diverse precauzioni durante l'implementazione di questa proprietà `Position` per evitare i cicli di feedback.

### <a name="setting-and-getting-ios-position"></a>Impostazione e recupero della posizione iOS

In iOS la proprietà `CurrentTime` dell'oggetto `AVPlayerItem` indica la posizione corrente del video riprodotto. `VideoPlayerRenderer` iOS imposta la proprietà `Position` nel gestore `UpdateStatus` nello stesso momento in cui imposta la proprietà `Duration`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ···
                ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, ConvertTime(playerItem.CurrentTime));
            }
        }
        ···
    }
}
```

Il renderer rileva quando la proprietà `Position` impostata da `VideoPlayer` è stata modificata nell'override di `OnElementPropertyChanged` e usa il nuovo valore per chiamare un metodo `Seek` nell'oggetto `AVPlayer`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                TimeSpan controlPosition = ConvertTime(player.CurrentTime);

                if (Math.Abs((controlPosition - Element.Position).TotalSeconds) > 1)
                {
                    player.Seek(CMTime.FromSeconds(Element.Position.TotalSeconds, 1));
                }
            }
        }
        ···
    }
}
```

Tenere presente che ogni volta che viene impostata la proprietà `Position` in `VideoPlayer` dal gestore `OnUpdateStatus`, la proprietà `Position` attiva un evento `PropertyChanged` che viene rilevato nell'override di `OnElementPropertyChanged`. Per la maggior parte delle modifiche, il metodo `OnElementPropertyChanged` non deve eseguire alcuna operazione. In caso contrario, a ogni modifica nella posizione del video verrebbe eseguito uno spostamento nella stessa posizione appena raggiunta.

Per evitare questo loop del feedback, il metodo `OnElementPropertyChanged` chiama solo `Seek` quando la differenza tra la proprietà `Position` e la posizione corrente di `AVPlayer` è maggiore di un secondo.

### <a name="setting-and-getting-android-position"></a>Impostazione e recupero della posizione Android

Come avviene nel renderer iOS, `VideoPlayerRenderer` di Android imposta un nuovo valore per la proprietà `Position` nel gestore `OnUpdateStatus`. La proprietà `CurrentPosition` di `VideoView` contiene la nuova posizione in unità di millisecondi:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            TimeSpan timeSpan = TimeSpan.FromMilliseconds(videoView.CurrentPosition);
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, timeSpan);
        }
        ···
    }
}
```

Inoltre, come nel renderer iOS, il renderer Android chiama il metodo `SeekTo` di `VideoView` quando la proprietà `Position` viene modificata, ma solo quando la modifica corrisponde a una differenza maggiore di un secondo rispetto al valore `CurrentPosition` di `VideoView`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs(videoView.CurrentPosition - Element.Position.TotalMilliseconds) > 1000)
                {
                    videoView.SeekTo((int)Element.Position.TotalMilliseconds);
                }
            }
        }
        ···
    }
}
```

### <a name="setting-and-getting-uwp-position"></a>Impostazione e recupero della posizione UWP

`VideoPlayerRenderer` di UWP gestisce `Position` allo stesso modo dei renderer iOS e Android, ma poiché la proprietà `Position` di `MediaElement` di UWP è anche un valore `TimeSpan`, non è necessaria alcuna conversione:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs((Control.Position - Element.Position).TotalSeconds) > 1)
                {
                    Control.Position = Element.Position;
                }
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, Control.Position);
        }
        ···
    }
}
```

## <a name="calculating-a-timetoend-property"></a>Calcolo di una proprietà TimeToEnd

A volte i lettori video visualizzano il tempo rimanente del video. Questo valore ha inizialmente il valore della durata del video e diminuisce fino a zero al termine del video.

`VideoPlayer` include una proprietà `TimeToEnd` di sola lettura gestita interamente all'interno della classe in base alle modifiche alle proprietà `Duration` e `Position`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        private static readonly BindablePropertyKey TimeToEndPropertyKey =
            BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan());

        public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

        public TimeSpan TimeToEnd
        {
            private set { SetValue(TimeToEndPropertyKey, value); }
            get { return (TimeSpan)GetValue(TimeToEndProperty); }
        }

        void SetTimeToEnd()
        {
            TimeToEnd = Duration - Position;
        }
        ···
    }
}
```

Il metodo `SetTimeToEnd` viene chiamato dai gestori di proprietà modificata di `Duration` e `Position`.

## <a name="a-custom-slider-for-video"></a>Dispositivo di scorrimento personalizzato per i video

È possibile scrivere un controllo personalizzato per un indicatore di posizione oppure usare `Slider` di Xamarin.Forms o una classe che deriva da `Slider`, ad esempio la classe `PositionSlider` seguente. La classe definisce due nuove proprietà denominate `Duration` e `Position` di tipo `TimeSpan` progettate per essere associate alle due proprietà con lo stesso nome in `VideoPlayer`. Si noti che la modalità di associazione predefinita della proprietà `Position` è bidirezionale:

```csharp
namespace FormsVideoLibrary
{
    public class PositionSlider : Slider
    {
        public static readonly BindableProperty DurationProperty =
            BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                    });

        public TimeSpan Duration
        {
            set { SetValue(DurationProperty, value); }
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                    defaultBindingMode: BindingMode.TwoWay,
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Value = seconds;
                                    });

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
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
    }
}
```

Il gestore di proprietà modificata per la proprietà `Duration` imposta la proprietà `Maximum` di `Slider` sottostante sulla proprietà `TotalSeconds` del valore `TimeSpan`. Allo stesso modo, il gestore di proprietà modificata per `Position` imposta la proprietà `Value` di `Slider`. In questo modo, `Slider` sottostante rileva la posizione di `PositionSlider`.

`PositionSlider` viene aggiornato dall'elemento `Slider` sottostante in una sola istanza: quando l'utente modifica `Slider` per indicare che il video deve avanzare o tornare indietro in una nuova posizione. Ciò viene rilevato nel gestore `PropertyChanged` nel costruttore di `PositionSlider`. Il gestore cerca una modifica nella proprietà `Value` e se la proprietà è diversa dalla proprietà `Position`, la proprietà `Position` viene impostata dalla proprietà `Value`.

In teoria, l'istruzione `if` interna può essere scritta come segue:

```csharp
if (newPosition.Seconds != Position.Seconds)
{
    Position = newPosition;
}
```

Tuttavia, l'implementazione Android di `Slider` ha solo 1.000 passaggi discreti indipendentemente dalle impostazioni `Minimum` e `Maximum`. Se la lunghezza di un video è maggiore di 1.000 secondi, due valori `Position` diversi corrispondono alla stessa impostazione `Value` di `Slider` e l'istruzione `if` attiva un falso positivo per una manipolazione dell'utente di `Slider`. È preferibile invece verificare che la nuova posizione e la posizione esistente siano maggiori di un centesimo della durata complessiva.

## <a name="using-the-positionslider"></a>Uso di PositionSlider

La documentazione di [`MediaElement`](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/) UWP consiglia di fare attenzione all'associazione alla proprietà `Position` poiché la proprietà viene aggiornata di frequente. Nella documentazione viene consigliato di usare un timer per eseguire una query della proprietà `Position`.

Sebbene sia un ottimo consiglio, le tre classi `VideoPlayerRenderer` usano già indirettamente un timer per aggiornare la proprietà `Position`. La proprietà `Position` viene modificata in un gestore per l'evento `UpdateStatus` che viene attivato solo 10 volte al secondo.

Di conseguenza, la proprietà `Position` di `VideoPlayer` può essere associata alla proprietà `Position` di `PositionSlider` senza problemi di prestazioni, come illustrato nella pagina **Custom Position Bar** (Indicatore di posizione personalizzato):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomPositionBarPage"
             Title="Custom Position Bar">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource ElephantsDream}" />

        ···

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="10, 0"
                     BindingContext="{x:Reference videoPlayer}">

            <Label Text="{Binding Path=Position,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center"/>

            ···

            <Label Text="{Binding Path=TimeToEnd,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center" />
        </StackLayout>

        <video:PositionSlider Grid.Row="2"
                              Margin="10, 0, 10, 10"
                              BindingContext="{x:Reference videoPlayer}"
                              Duration="{Binding Duration}"           
                              Position="{Binding Position}">
            <video:PositionSlider.Triggers>
                <DataTrigger TargetType="video:PositionSlider"
                             Binding="{Binding Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </video:PositionSlider.Triggers>
        </video:PositionSlider>
    </Grid>
</ContentPage>
```

I primi puntini di sospensione (···) nascondono `ActivityIndicator` come avviene nella pagina **Custom Transport** (Trasporto personalizzato) precedente. Si noti i due elementi `Label` che visualizzano le proprietà `Position` e `TimeToEnd`. I puntini di sospensione tra i due elementi `Label` nascondono i due elementi `Button` visualizzati nella pagina **Custom Transport** (Trasporto personalizzato) per la riproduzione, la pausa e l'arresto. Anche la logica code-behind equivale alla pagina **Custom Transport** (Trasporto personalizzato).

[![Posizionamento personalizzato](custom-positioning-images/custompositioning-small.png "Posizionamento personalizzato")](custom-positioning-images/custompositioning-large.png#lightbox "Posizionamento personalizzato")

Queste informazioni concludono la descrizione di `VideoPlayer`.

## <a name="related-links"></a>Collegamenti correlati

- [Demo lettore video (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
