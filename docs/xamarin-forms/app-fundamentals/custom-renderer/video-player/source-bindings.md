---
title: Associazione di origini video al lettore
description: Questo articolo illustra come associare origini video al lettore video tramite Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 504E0C7E-051A-4AF2-B654-BAB4D0957928
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 0fe170aaa880aebcaff7899c440a0522e1f83a0c
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051121"
---
# <a name="binding-video-sources-to-the-player"></a>Associazione di origini video al lettore

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)

Quando la proprietà `Source` della visualizzazione di `VideoPlayer` viene impostata su un nuovo file video, la riproduzione del video esistente viene arrestata e inizia il nuovo video. Questa funzionalità è dimostrata dalla pagina **Select Web Video** dell'esempio [**VideoPlayerDemos**](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/). La pagina include un controllo `ListView` con i titoli dei tre video a cui fa riferimento il file **App.xaml**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.SelectWebVideoPage"
             Title="Select Web Video">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0" />

        <ListView Grid.Row="1"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Elephant's Dream</x:String>
                    <x:String>Big Buck Bunny</x:String>
                    <x:String>Sintel</x:String>
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

Quando viene selezionato un video, viene eseguito il gestore dell'evento `ItemSelected` nel file code-behind. Il gestore rimuove eventuali spazi e apostrofi dal titolo e lo usa come chiave per ottenere una delle risorse definite nel file **App.xaml**. Tale oggetto `UriVideoSource` viene poi impostato sulla proprietà `Source` di `VideoPlayer`.

```csharp
namespace VideoPlayerDemos
{
    public partial class SelectWebVideoPage : ContentPage
    {
        public SelectWebVideoPage()
        {
            InitializeComponent();
        }

        void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
        {
            if (args.SelectedItem != null)
            {
                string key = ((string)args.SelectedItem).Replace(" ", "").Replace("'", "");
                videoPlayer.Source = (UriVideoSource)Application.Current.Resources[key];
            }
        }
    }
}
```

Al primo caricamento della pagina, nessun elemento è selezionato in `ListView`, quindi è necessario selezionarne uno per avviare la riproduzione del video:

[![Select Web Video](source-bindings-images/selectwebvideo-small.png "Select Web Video")](source-bindings-images/selectwebvideo-large.png#lightbox "Select Web Video")

La proprietà `Source` di `VideoPlayer` è supportata da una proprietà associabile e ciò significa che può essere la destinazione di un data binding. Questa funzionalità viene dimostrata dalla pagina **Bind to VideoPlayer**. Il markup nel file **BindToVideoPlayer.xaml** è supportato dalla classe seguente che incapsula un titolo di un video e un oggetto `VideoSource` corrispondente:

```csharp
namespace VideoPlayerDemos
{
    public class VideoInfo
    {
        public string DisplayName { set; get; }

        public VideoSource VideoSource { set; get; }

        public override string ToString()
        {
            return DisplayName;
        }
    }
}
```

`ListView` nel file **BindToVideoPlayer.xaml** contiene una matrice di questi oggetti `VideoInfo`, ognuno dei quali è inizializzato con un titolo di video e l'oggetto `UriVideoSource` dal dizionario risorse in **App.xaml**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VideoPlayerDemos"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.BindToVideoPlayerPage"
             Title="Bind to VideoPlayer">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           Source="{Binding Source={x:Reference listView},
                                            Path=SelectedItem.VideoSource}" />

        <ListView x:Name="listView"
                  Grid.Row="1">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:VideoInfo}">
                    <local:VideoInfo DisplayName="Elephant's Dream"
                                     VideoSource="{StaticResource ElephantsDream}" />

                    <local:VideoInfo DisplayName="Big Buck Bunny"
                                     VideoSource="{StaticResource BigBuckBunny}" />

                    <local:VideoInfo DisplayName="Sintel"
                                     VideoSource="{StaticResource Sintel}" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

La proprietà `Source` di `VideoPlayer` è associata a `ListView`. Il `Path` del binding è specificato come `SelectedItem.VideoSource`, ovvero un percorso composto costituito da due proprietà: `SelectedItem` è una proprietà di `ListView`. L'elemento selezionato è di tipo `VideoInfo`, che ha una proprietà `VideoSource`.

Come con la prima pagina **Select Web Video**, inizialmente non è selezionato alcun elemento da `ListView`, pertanto è necessario selezionare uno dei video prima di iniziare la riproduzione.


## <a name="related-links"></a>Collegamenti correlati

- [Demo lettore video (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
