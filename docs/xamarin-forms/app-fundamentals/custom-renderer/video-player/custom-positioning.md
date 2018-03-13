---
title: Il posizionamento video personalizzato
ms.topic: article
ms.prod: xamarin
ms.assetid: 6D792264-30FF-46F7-8C1B-2FEF9D277DF4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: e7f2ad9e94d68007b1b7d0cca212cd51515a0108
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="custom-video-positioning"></a>Il posizionamento video personalizzato

I controlli di trasporto implementati da ogni piattaforma per includono un indicatore di posizione. Questa barra è simile a un dispositivo di scorrimento o la barra di scorrimento e Mostra la posizione corrente del video all'interno della durata totale. Inoltre, l'utente è possibile modificare la barra di posizione per spostarsi avanti o indietro in una nuova posizione nel video.

Questo articolo illustra come è possibile implementare la propria barra posizione personalizzata.

## <a name="the-duration-property"></a>La proprietà di durata

Un elemento di informazioni che `VideoPlayer` deve supportare un oggetto personalizzato della barra di posizione è la durata del video. Il `VideoPlayer` definisce una proprietà di sola lettura `Duration` proprietà di tipo `TimeSpan`:

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

Ad esempio il `Status` proprietà descritta nel [articolo precedente](custom-transport.md), questo `Duration` proprietà è di sola lettura. Viene definita con una privata `BindablePropertyKey` e può essere impostato solo facendo riferimento al `IVideoPlayerController` interfaccia, che include questo `Duration` proprietà:

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

Si noti inoltre che il gestore di modifica della proprietà che chiama un metodo denominato `SetTimeToEnd` descritto più avanti in questo articolo.

La durata di un video è *non* disponibili immediatamente dopo il `Source` proprietà `VideoPlayer` è impostata. Il file video deve essere scaricato parzialmente prima del lettore sottostante può determinare la sua durata.

Di seguito è il modo in cui ognuno dei renderer piattaforma Ottiene la durata del video:

### <a name="video-duration-in-ios"></a>Durata video in iOS

In iOS, la durata di un video verrà ottenuta dal `Duration` proprietà di `AVPlayerItem`, ma non immediatamente dopo il `AVPlayerItem` viene creato. È possibile impostare un osservatore iOS per la `Duration` proprietà, ma la `VideoPlayerRenderer` Ottiene la durata nel `UpdateStatus` metodo, che viene chiamato 10 volte al secondo:

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

Il `ConvertTime` metodo converte un `CMTime` l'oggetto in un `TimeSpan` valore.

### <a name="video-duration-in-android"></a>Durata video in Android

Il `Duration` proprietà di Android `VideoView` segnala una durata valida in millisecondi quando il `Prepared` evento di `VideoView` viene generato. Il Android `VideoPlayerRenderer` classe utilizza il gestore per ottenere il `Duration` proprietà:

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

### <a name="video-duration-in-uwp"></a>Durata video in UWP

Il `NaturalDuration` proprietà di `MediaElement` è un `TimeSpan` valore e diventa valido quando `MediaElement` viene attivato il `MediaOpened` evento:

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

## <a name="the-position-property"></a>La proprietà Position

`VideoPlayer` deve inoltre un `Position` proprietà che consentono di aumentare da zero a `Duration` come la riproduzione del video. `VideoPlayer` implementa la proprietà come il `Position` proprietà in UWP `MediaElement`, che è una proprietà associabile normale con pubblico `set` e `get` le funzioni di accesso:

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

Il `get` della funzione di accesso restituisce la posizione corrente del video come riproduzione, ma la `set` deve rispondere per la modifica dell'utente della barra degli strumenti posizione di spostare la posizione video avanti o indietro della funzione di accesso.

In iOS e Android, la proprietà che ottiene la posizione corrente ha solo un `get` funzione di accesso e un `Seek` metodo è disponibile per eseguire questa attività di secondo. Se si pensa che un oggetto separato `Seek` metodo sembra essere un approccio più appropriato di una singola `Position` proprietà. Un singolo `Position` proprietà ha un problema inerente: durante la riproduzione, il `Position` proprietà deve essere continuamente aggiornata per riflettere la nuova posizione. Non si desidera che la maggior parte delle modifiche per il `Position` proprietà affinché il lettore video spostare in una nuova posizione nel video. Se in tal caso, il lettore video avrebbe risposto effettuando la ricerca e l'ultimo valore di `Position` non spostare proprietà e il video.

Nonostante le difficoltà di implementazione di un `Position` proprietà con `set` e `get` funzioni di accesso, è stato scelto questo approccio perché è coerenza con la piattaforma UWP `MediaElement`, e ha un grande vantaggio con l'associazione dati: il `Position` proprietà del `VideoPlayer` può essere associato al dispositivo di scorrimento che viene utilizzato per visualizzare la posizione e da cercare in una nuova posizione. Tuttavia, alcune accortezze sono necessari quando si implementa questo `Position` proprietà evitare i cicli di commenti e suggerimenti.

### <a name="setting-and-getting-ios-position"></a>Impostazione e recupero posizione iOS

In iOS, il `CurrentTime` proprietà del `AVPlayerItem` oggetto indica la posizione corrente della riproduzione video. IOS `VideoPlayerRenderer` imposta il `Position` proprietà il `UpdateStatus` gestore allo stesso tempo che imposta il `Duration` proprietà:

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

Il renderer rileva quando il `Position` impostata da `VideoPlayer` è stata modificata nel `OnElementPropertyChanged` eseguire l'override e Usa il nuovo valore per chiamare un `Seek` metodo il `AVPlayer` oggetto:

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

Tenere presente che ogni volta il `Position` proprietà `VideoPlayer` viene impostato dal `OnUpdateStatus` gestore, la `Position` proprietà generato un `PropertyChanged` evento, che viene rilevato nel `OnElementPropertyChanged` eseguire l'override. Per la maggior parte di queste modifiche, il `OnElementPropertyChanged` metodo dovrebbe fare nulla. In caso contrario, con ogni modifica della posizione del video, verrà quindi spostato nella stessa posizione in cui è appena raggiunta!

Per evitare questo ciclo di feedback, la `OnElementPropertyChanged` metodo chiama solo `Seek` quando la differenza tra il `Position` proprietà e la posizione corrente del `AVPlayer` è maggiore di un secondo.

### <a name="setting-and-getting-android-position"></a>Impostazione e recupero posizione Android

Analogamente il renderer di iOS, il Android `VideoPlayerRenderer` imposta un nuovo valore per il `Position` proprietà il `OnUpdateStatus` gestore. Il `CurrentPosition` proprietà `VideoView` contiene la nuova posizione nell'unità di millisecondi:

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

Analogamente il renderer di iOS, inoltre, il renderer Android chiama il `SeekTo` metodo `VideoView` quando il `Position` proprietà è stata modificata, ma solo quando la modifica è diverso da più di un secondo il `CurrentPosition` valore `VideoView`:

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

### <a name="setting-and-getting-uwp-position"></a>Impostazione e recupero posizione UWP

La piattaforma UWP `VideoPlayerRenderer` handle il `Position` nello stesso modo come iOS e Android renderer, ma poiché il `Position` proprietà alla piattaforma UWP `MediaElement` è anche un `TimeSpan` valore, è necessaria alcuna conversione:

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

## <a name="calculating-a-timetoend-property"></a>Il calcolo di una proprietà TimeToEnd

Lettori video talvolta mostrano il tempo rimanente nel video. Questo valore inizia dalla durata del video quando il video inizia e diminuisce fino a zero quando termina il video.

`VideoPlayer` include una proprietà di sola lettura `TimeToEnd` proprietà che viene gestito interamente all'interno della classe in base alle modifiche apportate al `Duration` e `Position` proprietà:

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

Il `SetTimeToEnd` metodo viene chiamato dai gestori di modifica della proprietà di entrambi `Duration` e `Position`.

## <a name="a-custom-slider-for-video"></a>Un dispositivo di scorrimento personalizzato per video

È possibile scrivere un controllo personalizzato per un indicatore di posizione oppure utilizzare il xamarin. Forms `Slider` o una classe che deriva da `Slider`, come illustrato di seguito `PositionSlider` classe. La classe definisce due nuove proprietà denominata `Duration` e `Position` di tipo `TimeSpan` destinati a essere associato a due proprietà con lo stesso nome in `VideoPlayer`. Si noti che il valore predefinito della modalità di associazione di `Position` proprietà è bidirezionale:

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

Il gestore di modifica della proprietà per il `Duration` set di proprietà di `Maximum` proprietà dell'oggetto sottostante `Slider` per il `TotalSeconds` proprietà del `TimeSpan` valore. Analogamente, il gestore di modifica della proprietà `Position` imposta il `Value` proprietà del `Slider`. In questo modo, l'oggetto sottostante `Slider` tiene traccia delle relative alla posizione del `PositionSlider`.

Il `PositionSlider` è stato aggiornato da sottostante `Slider` in una sola istanza: quando l'utente modifica il `Slider` per indicare che il video deve essere avanzato o annullato in una nuova posizione. Questo errore viene rilevato nel `PropertyChanged` nel costruttore del gestore di `PositionSlider`. Il gestore verifica una modifica di `Value` proprietà, e se è diverso dal `Position` proprietà, il `Position` viene impostata dal `Value` proprietà.

In teoria, interna `if` istruzione può essere scritto come segue:

```csharp
if (newPosition.Seconds != Position.Seconds)
{
    Position = newPosition;
}
```

Tuttavia, l'implementazione di Android `Slider` ha solo 1.000 discreti indipendentemente la `Minimum` e `Maximum` impostazioni. La lunghezza di un video è maggiore di 1.000 secondi, quindi due diversi `Position` valori corrisponderanno allo stesso `Value` impostazione il `Slider`e ciò `if` istruzione genera un falso positivo per la modifica di un utente di il `Slider`. È più sicuro controllare che la nuova posizione e la posizione esistente sono maggiori di / 100 della durata complessiva.

## <a name="using-the-positionslider"></a>Utilizzando il PositionSlider

Documentazione per la piattaforma UWP [ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/) visualizzati avvisi relativi al binding di `Position` proprietà perché la proprietà frequenza degli aggiornamenti. La documentazione consiglia che è possibile utilizzare un timer per query di `Position` proprietà.

Che è consigliabile, ma i tre `VideoPlayerRenderer` classi sono già indirettamente utilizzando un timer per aggiornare il `Position` proprietà. Il `Position` proprietà viene modificata in un gestore per il `UpdateStatus` evento, viene generato solo 10 volte al secondo.

Pertanto, il `Position` proprietà del `VideoPlayer` può essere associato al `Position` proprietà del `PositionSlider` senza problemi di prestazioni, come illustrato nel **personalizzato posizione barra** pagina:

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

Nasconde i puntini di sospensione (···) prima di `ActivityIndicator`; è uguale a quello precedente **trasporto Custom** pagina. Notare i due `Label` la visualizzazione di elementi di `Position` e `TimeToEnd` proprietà. I puntini di sospensione tra questi due `Label` elementi nasconde i due `Button` elementi mostrati nel **trasporto Custom** pagina per la riproduzione, pausa e arresto. La logica code-behind equivale inoltre il **trasporto Custom** pagina.

[![Il posizionamento personalizzato](custom-positioning-images/custompositioning-small.png "il posizionamento personalizzato")](custom-positioning-images/custompositioning-large.png#lightbox "il posizionamento personalizzato")

Questa operazione conclude la discussione relativa la `VideoPlayer`.

## <a name="related-links"></a>Collegamenti correlati

- [Demo di lettore video (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
